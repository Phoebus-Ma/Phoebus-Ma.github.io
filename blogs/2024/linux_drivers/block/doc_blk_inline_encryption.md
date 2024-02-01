
# Inline Encryption

# 1. Background

内联加密硬件逻辑上位于内存和磁盘之间，可以在数据进出磁盘时对其进行加密/解密。 对于每个 I/O 请求，软件可以精确控制内联加密硬件如何根据密钥、算法、数据单元大小（加密/解密的粒度）和数据单元编号（决定加密大小的值）来加密/解密数据。 初始化向量）。

某些内联加密硬件直接在低级 I/O 请求中接受所有加密参数，包括原始密钥。 然而，大多数内联加密硬件具有固定数量的“密钥槽”，并且要求首先将密钥、算法和数据单元大小编程到密钥槽中。 每个低级 I/O 请求仅包含一个键槽索引和数据单元号。

请注意，内联加密硬件与传统加密加速器有很大不同，传统加密加速器通过内核加密 API 支持。 传统加密加速器在内存区域上运行，而内联加密硬件则在 I/O 请求上运行。 因此，内联加密硬件需要由块层管理，而不是内核加密 API。

内联加密硬件也与“自加密驱动器”有很大不同，例如基于 TCG Opal 或 ATA 安全标准的驱动器。 自加密驱动器不提供细粒度的加密控制，并且无法验证生成的密文的正确性。 内联加密硬件提供了加密的细粒度控制，包括每个扇区的密钥和初始化向量的选择，并且可以测试正确性。


# 2. Objective

我们希望在内核中支持内联加密。 为了使测试更容易，我们还希望在不存在实际内联加密硬件时支持回退到内核加密 API。 我们还希望内联加密能够与设备映射器和环回等分层设备一起使用（即我们希望能够使用底层设备的内联加密硬件（如果存在），否则回退到加密 API 加密/解密）。


# 3. Constraints and notes

- 我们需要一种方法让上层（例如文件系统）指定用于加密/解密 Bio 的加密上下文，并且设备驱动程序（例如 UFSHCD）需要能够在处理请求时使用该加密上下文。 加密上下文还引入了对生物合并的限制； 块层需要了解这些约束。

- 不同的内联加密硬件具有不同的支持算法、支持的数据单元大小、最大数据单元数量等。我们将这些属性称为“加密能力”。 我们需要一种让设备驱动程序以通用方式向上层通告加密功能的方法。

- 内联加密硬件通常（但并非总是）要求在使用之前将密钥编程到键槽中。 由于编程键槽可能很慢并且键槽可能不是很多，因此我们不应该只为每个 I/O 请求编程键，而应该跟踪键槽中的键，并在可能的情况下重用已经编程的键槽 。

- 上层通常定义加密密钥的特定寿命终止，例如 当加密目录被锁定或加密映射被拆除时。 此时，密钥将从内存中擦除。 我们必须为上层提供一种方法，将密钥从它们所在的任何键槽中逐出。

- 如果可能，设备映射器设备必须能够通过其底层设备的内联加密支持。 但是，设备映射器设备本身具有键槽是没有意义的。


# 4. Basic design

我们引入 struct blk_crypto_key 来表示内联加密密钥及其使用方式。 这包括密钥的实际字节； 钥匙的大小； 密钥将使用的算法和数据单元大小； 以及表示密钥将使用的最大数据单元数所需的字节数。

我们引入 struct bio_crypt_ctx 来表示加密上下文。 它包含一个数据单元号和一个指向 blk_crypto_key 的指针。 我们添加指向bio_crypt_ctx的指针到struct bio和struct request； 这允许块层（例如文件系统）的用户在创建bio时提供加密上下文，并将其向下传递到堆栈以供块层和设备驱动程序处理。 请注意，加密上下文没有明确说明是否加密或解密，因为这是从生物的方向隐含的； WRITE 表示加密，READ 表示解密。

我们还引入了 struct blk_crypto_profile 来包含特定内联加密设备的所有通用内联加密相关状态。 blk_crypto_profile 充当内联加密硬件驱动程序宣传其加密功能并向上层提供某些功能（例如，编程和逐出密钥的功能）的方式。 每个想要支持内联加密的设备驱动程序都会构造一个 blk_crypto_profile，然后将其与磁盘的 request_queue 关联起来。

blk_crypto_profile 还管理硬件的键槽（如果适用）。 这发生在块层，因此块层的用户只需指定加密上下文，根本不需要了解密钥槽，设备驱动程序也不需要关心密钥槽管理的大多数细节。

具体来说，对于每个键槽，块层（通过 blk_crypto_profile）跟踪该键槽包含哪个 blk_crypto_key（如果有），以及有多少正在进行的 I/O 请求正在使用它。 当块层为具有加密上下文的bio创建结构体请求时，如果可能的话，它会获取已经包含密钥的密钥槽。 否则，它将等待空闲键槽（任何 I/O 未使用的键槽），然后使用设备驱动程序提供的函数将键编程到最近最少使用的空闲键槽中。 在这两种情况下，生成的密钥槽都存储在请求的 crypt_keyslot 字段中，然后设备驱动程序可以访问该密钥槽，并在请求完成后释放它。

struct request还包含一个指向原始bio_crypt_ctx的指针。 请求可以从多个 BIOS 构建，并且块层在尝试合并 BIOS 和请求时必须考虑加密上下文。 对于要合并的两个 BIOS/请求，它们必须具有兼容的加密上下文：两者都未加密，或者都使用相同的密钥和连续的数据单元号进行加密。 仅保留请求中第一个 Bio 的加密上下文，因为其余 BIOS 已被验证为与第一个 Bio 合并兼容。

为了使内联加密能够与基于 request_queue 的分层设备一起使用，克隆请求时，也会克隆其加密上下文。 当克隆的请求被提交后，它就会像平常一样被处理； 这包括根据需要从克隆的目标设备获取键槽。


# 5. blk-crypto-fallback

希望上层（例如文件系统）的内联加密支持能够在没有真正的内联加密硬件的情况下进行测试，块层的键槽管理逻辑也是如此。 还希望允许上层始终使用内联加密，而不必以多种方式实现加密。

因此，我们还引入了 blk-crypto-fallback，它是使用内核加密 API 进行内联加密的实现。 blk-crypto-fallback 内置于块层中，因此它可以在任何块设备上运行，无需任何特殊设置。 本质上，当具有加密上下文的bio被提交到不支持该加密上下文的block_device时，块层将使用blk-crypto-fallback处理bio的加密/解密。

对于加密，数据无法就地加密，因为调用者通常依赖于数据未经修改。 相反，blk-crypto-fallback 分配弹跳页面，用这些弹跳页面填充新的bio，将数据加密到这些弹跳页面中，然后提交该“弹跳”bio。 当反弹bio完成时，blk-crypto-fallback完成原始bio。 如果原始bio太大，可能需要多次反弹bio； 详情请参阅代码。

对于解密，blk-crypto-fallback 用自己的“包装”bio 的完成回调 (bi_complete) 和私有数据 (bi_private)，取消设置 bio 的加密上下文，然后提交 bio。 如果读取成功完成，blk-crypto-fallback 将恢复 Bio 的原始完成回调和私有数据，然后使用内核加密 API 就地解密 Bio 的数据。 解密发生在工作队列中，因为它可能会休眠。 之后，blk-crypto-fallback 完成了bio。

在这两种情况下，blk-crypto-fallback 提交的 BIOS 不再具有加密上下文。 因此，较低层只能看到标准的未加密 I/O。

blk-crypto-fallback 还定义了自己的 blk_crypto_profile 并拥有自己的“keyslots”； 它的键槽包含 struct crypto_skcipher 对象。 其原因是双重的。 首先，它允许在没有实际内联加密硬件的情况下测试键槽管理逻辑。 其次，与实际的内联加密硬件类似，加密 API 不直接在请求中接受密钥，而是要求提前设置密钥，而设置密钥的成本可能很高； 此外，由于需要锁定，分配 crypto_skcipher 根本不可能发生在 I/O 路径上。 因此，键槽的概念对于 blk-crypto-fallback 仍然有意义。

请注意，无论使用真正的内联加密硬件还是 blk-crypto-fallback，写入磁盘的密文（以及数据的磁盘格式）都将是相同的（假设内联加密硬件的实现和内核 所用算法的加密 API 实现符合规范且功能正常）。

blk-crypto-fallback 是可选的，由 CONFIG_BLK_INLINE_ENCRYPTION_FALLBACK 内核配置选项控制。


# 6. API presented to users of the block layer

blk_crypto_config_supported() 允许用户提前检查具有特定加密设置的内联加密是否可以在特定的 block_device 上工作——通过硬件或通过 blk-crypto-fallback。 该函数接受一个 struct blk_crypto_config ，它类似于 blk_crypto_key ，但省略了密钥的实际字节，而只包含算法、数据单元大小等。如果禁用 blk-crypto-fallback ，则该函数可能很有用。

blk_crypto_init_key() 允许用户初始化 blk_crypto_key。

用户必须在实际开始在 block_device 上使用 blk_crypto_key 之前调用 blk_crypto_start_using_key() （即使之前调用了 blk_crypto_config_supported()）。 如果需要的话，这是初始化 blk-crypto-fallback 所必需的。 不得从数据路径调用此函数，因为这可能必须分配资源，在这种情况下可能会发生死锁。

接下来，要将加密上下文附加到bio，用户应该调用bio_crypt_set_ctx()。 给定 blk_crypto_key 和将用于加密/解密的数据单元号，该函数分配 bio_crypt_ctx 并将其附加到 bio。 用户无需担心稍后释放bio_crypt_ctx，因为释放或重置bio时会自动发生。

最后，当在 block_device 上使用 blk_crypto_key 进行内联加密时，用户必须调用 blk_crypto_evict_key()。 这确保了密钥从它可能被编程到的所有键槽中被逐出，并从它可能链接到的任何内核数据结构中取消链接。

综上，对于区块层的用户来说，一个blk_crypto_key的生命周期如下：

1. blk_crypto_config_supported() (可选)
2. blk_crypto_init_key()
3. blk_crypto_start_using_key()
4. bio_crypt_set_ctx() (可能很多次)
5. blk_crypto_evict_key() (所有 I/O 完成后)
6. 将 blk_crypto_key 归零 (这没有专门的功能)

如果在多个 block_device 上使用 blk_crypto_key，则必须在每个 block_device 上调用 blk_crypto_config_supported() （如果使用）、blk_crypto_start_using_key() 和 blk_crypto_evict_key()。


# 7. API presented to device drivers

想要支持内联加密的设备驱动程序必须在其设备的 request_queue 中设置 blk_crypto_profile。 为此，它首先必须调用 blk_crypto_profile_init() （或其资源管理变体 devm_blk_crypto_profile_init()），提供键槽的数量。

接下来，它必须通过设置 blk_crypto_profile 中的字段来宣传其加密功能，例如 mode_supported 和 max_dun_bytes_supported。

然后，它必须在 blk_crypto_profile 的 ll_ops 字段中设置函数指针，以告诉上层如何控制内联加密硬件，例如 如何编程和逐出键槽。 大多数驱动程序需要实现keyslot_program和keyslot_evict。 有关详细信息，请参阅 struct blk_crypto_ll_ops 的注释。

一旦驱动程序向 request_queue 注册 blk_crypto_profile，驱动程序通过该队列接收的 I/O 请求可能具有加密上下文。 所有加密上下文都将与 blk_crypto_profile 中声明的加密功能兼容，因此驱动程序无需担心处理不受支持的请求。 此外，如果在 blk_crypto_profile 中声明了非零数量的密钥槽，则具有加密上下文的所有 I/O 请求也将具有已使用适当密钥进行编程的密钥槽。

如果驱动程序实现运行时挂起并且其 blk_crypto_ll_ops 在设备运行时挂起时不起作用，则驱动程序还必须将 blk_crypto_profile 的 dev 字段设置为指向将在任何低级操作之前恢复的 struct device 操作被调用。

如果存在内联加密硬件丢失其密钥槽内容的情况，例如 设备重置后，驱动程序必须处理键槽的重新编程。 为此，驱动程序可以调用 blk_crypto_reprogram_all_keys()。

最后，如果驱动程序使用 blk_crypto_profile_init() 而不是 devm_blk_crypto_profile_init()，则它负责在不再需要加密配置文件时调用 blk_crypto_profile_destroy()。


# 8. Layered Devices

基于请求队列的分层设备（例如希望支持内联加密的 dm-rq）需要为其 request_queue 创建自己的 blk_crypto_profile，并公开它们选择的任何功能。 当分层设备想要将该请求的克隆传递到另一个 request_queue 时，blk-crypto 将根据需要初始化并准备克隆。


# 9. Interaction between inline encryption and blk integrity

在此补丁发布时，还没有真正的硬件支持这两个功能。 然而，这些功能确实会相互影响，并且让它们正常协同工作并不是一件容易的事。 特别是，当 WRITE bio 想要在支持这两种功能的设备上使用内联加密时，bio 将指定一个加密上下文，然后计算其完整性信息（使用明文数据，因为加密将在数据被传输时发生） 正在写入），并且数据和完整性信息被发送到设备。 显然，在数据加密之前必须验证完整性信息。 数据加密后，设备不得存储随明文数据接收的完整性信息，因为这可能会泄露有关明文数据的信息。 因此，它必须从密文数据重新生成完整性信息并将其存储在磁盘上。 存储明文数据完整性信息的另一个问题是，它会根据是否存在硬件内联加密支持或是否使用内核加密 API 回退来更改磁盘格式（因为如果使用回退，设备将接收完整性信息） 密文的信息，而不是明文的信息）。

因为还没有任何真正的硬件，所以谨慎地假设硬件实现可能无法正确地同时实现这两个功能，并且暂时不允许组合。 每当设备支持完整性时，内核都会假装该设备不支持硬件内联加密（通过将设备的 request_queue 中的 blk_crypto_profile 设置为 NULL）。 当加密 API 回退启用时，这意味着所有具有加密上下文的 BIOS 将使用回退，并且 IO 将照常完成。 当回退被禁用时，具有加密上下文的 Bio 将失败。
