
# Immutable biovecs and biovec iterators

Kent Overstreet <kmo@daterainc.com>


# 1. Introduction

自 3.13 起，biovecs 不应在提交 bio 后修改。 相反，我们有一个新的 struct bvec_iter ，它表示一个 biovec 的范围 - 迭代器将在 bio 完成时被修改，而不是 biovec 。

更具体地说，需要部分完成bio的旧代码将更新bi_sector和bi_size，并将bi_idx推进到下一个biovec。 如果它在 Biovec 中途结束，它将增加 bv_offset 并减少 bv_len 中完成的字节数。

在新的方案中，为了部分完成bio而必须改变的所有内容都被隔离到struct bvec_iter中：bi_sector、bi_size和bi_idx已移至那里； 并且struct bvec_iter没有修改bv_offset和bv_len，而是有bi_bvec_done，它表示当前bvec中完成的字节数。

有一堆新的辅助宏用于隐藏血淋淋的细节 - 特别是，呈现部分完成的 biovec 的错觉，以便正常代码不必处理 bi_bvec_done。

- 驱动程序代码不应再直接引用biovecs； 我们现在有bio_iovec()和bio_iter_iovec()宏，它们返回文字struct biovecs，它们是从原始biovecs构造的，但考虑到bi_bvec_done和bi_size。

    bio_for_each_segment() 已更新为采用 bvec_iter 参数而不是整数（对应于 bi_idx）； 对于很多代码，转换只需要更改 bio_for_each_segment() 的参数类型。

- 推进 bvec_iter 是通过 bio_advance_iter() 完成的； bio_advance() 是bio_advance_iter() 的包装器，它对bio->bi_iter 进行操作，并且还会推进bio 完整性的iter（如果存在）。

    有一个较低级别的高级函数 - bvec_iter_advance() - 它采用指向 Biovec 的指针，而不是 Bio； 这是生物完整性代码使用的。

从 5.12 开始，不支持 bv_len 为零的 bvec 段。


# 2. What's all this get us?

拥有真正的迭代器并使 biovecs 不可变具有许多优点：

- 以前，当您一次不处理一个 bvec 时，迭代 BIOS 是非常尴尬的 - 例如，block/bio.c 中的 bio_copy_data() ，它将一个 BIOS 的内容复制到另一个 BIOS 中。 因为 Biovec 的大小不一定相同，所以旧代码非常复杂 - 它必须同时遍历两个不同的 BIOS，并为每个 Biovec 保留 bi_idx 和偏移量。

    新代码更加简单 - 看看吧。 这种模式在很多地方都会出现； 之前很多驱动程序本质上都是开放编码 bvec 迭代器，并且具有通用实现可以大大简化大量代码。

- 之前，在bio完成后可能需要使用biovec的任何代码（可能将数据复制到其他地方，或者如果出现错误则可能在其他地方重新提交）必须保存整个bvec数组 - 同样，这 在相当多的地方都在进行。

- Biovec 可以在多个 BIOS 之间共享 - bvec iter 可以代表现有 Biovec 的任意范围，在 Biovec 的中途开始和结束。 这就是实现任意 BIOS 有效分割的原因。 请注意，这意味着我们_仅_使用bi_size来确定何时到达bio的末尾，而不是bi_vcnt - 并且bio_iovec()宏在构造biovec时考虑bi_size。

- 分割BIOS现在更加简单。 旧的 bio_split() 甚至无法在具有多个 bvec 的 BIOS 上运行！ 现在，我们可以有效地分割任意大小的bios——因为新的bios可以共享旧bios的biovec。

    必须小心确保当分割bio仍在使用它时biovec不会被释放，以防原始bio先完成。 分割 BIOS 时使用 bio_chain() 有助于解决此问题。

- 提交部分完成的 BIOS 现在完全没问题 - 这偶尔会出现在堆栈块驱动程序中，并且各种代码（例如 md 和 bcache）对此有一些丑陋的解决方法。

    过去的情况是，提交部分完成的 bio 对于大多数设备来说都可以正常工作，但由于访问原始 bvec 数组是常态，因此并非所有驱动程序都会尊重 bi_idx，并且这些驱动程序会中断。 现在，由于所有驱动程序_必须_通过 bvec 迭代器 - 并且已经过审核以确保它们 - 提交部分完成的 BIOS 是完全可以的。


# 3. Other implications

- 现在几乎所有 bi_idx 的用法都是不正确的并已被删除； 相反，以前使用 bi_idx 的地方现在使用 bvec_iter，可能将其传递给辅助宏之一。

    IE。 现在，您不再使用bio_iovec_idx()（或bio->bi_iovec[bio->bi_idx]），而是使用bio_iter_iovec()，它接受bvec_iter并返回文字结构bio_vec - 从原始biovec即时构建，但考虑到 bi_bvec_done（和bi_size）。

- bi_vcnt 不能被驱动程序代码信任或依赖 - 即任何实际上不拥有该 bio 的东西。 原因有两个：首先，实际上不再需要迭代bio了——我们只使用bi_size。 其次，当克隆一个bio并重用（一部分）原始bio的biovec时，为了计算新bio的bi_vcnt，我们必须迭代新bio中的所有biovec——这是愚蠢的，因为不需要。

    所以，不要再使用bi_vcnt了。

- 当前接口允许块层根据需要分割BIOS，因此我们可以消除很多复杂性，特别是在堆叠驱动程序中。 创建 BIOS 的代码可以创建任何方便的大小的 BIOS，更重要的是，堆栈驱动程序不必处理它们自己的 BIOS 大小限制和底层设备的限制。 因此，无需为各个块驱动程序定义 ->merge_bvec_fn() 回调。


# 4. Usage of helpers

- 以下名称带有 _all 后缀的助手只能在非 BIO_CLONED Bio 上使用。 它们通常由文件系统代码使用。 驱动程序不应该使用它们，因为生物在到达驱动程序之前可能已经被分割。

```c
bio_for_each_segment_all()
bio_for_each_bvec_all()
bio_first_bvec_all()
bio_first_page_all()
bio_first_folio_all()
bio_last_bvec_all()
```

- 以下帮助程序迭代单页段。 迭代期间传递的“struct bio_vec”将包含一个单页 IO 向量：

```c
bio_for_each_segment()
bio_for_each_segment_all()
```

- 以下帮助程序迭代多页 bvec。 传递的'struct bio_vec'将在迭代期间包含一个多页IO向量：

```c
bio_for_each_bvec()
bio_for_each_bvec_all()
rq_for_each_bvec()
```
