# Python 3 Base Tutorial (Chinese)

## 目录

[1.基础语法](#1-python基础语法)

[2.基本数据类型](#2-基本数据类型)

[3.数据类型转换](#3-数据类型转换)

[4.推导式](#4-推导式)

[5.解释器](#5-解释器)

[6.注释](#6-注释)

[7.运算符](#7-运算符)

[8.数字(Number)](#8-数字number)

[9.字符串](#9-字符串)

[10.列表](#10-列表)

[11.元组](#11-元组)

[12.字典](#12-字典)

[13.集合](#13-集合)



## 1 Python基础语法

### 1.1 编码

默认情况下，Python 3 源码文件以 UTF-8 编码，所有字符串都是 unicode 字符串。 当然你也可以为源码文件指定不同的编码：


```bash
# -*- coding: cp-1252 -*-
```

上述定义允许在源文件中使用 Windows-1252 字符集中的字符编码，对应适合语言为保加利亚语、白罗斯语、马其顿语、俄语、塞尔维亚语。


### 1.2 标识符

* 第一个字符必须是字母表中字母或下划线 _ 。
* 标识符的其他的部分由字母、数字和下划线组成。
* 标识符对大小写敏感。


在 Python 3 中，可以用中文作为变量名，非 ASCII 标识符也是允许的了。


### 1.3 Python保留字

保留字即关键字，我们不能把它们用作任何标识符名称。Python 的标准库提供了一个 keyword 模块，可以输出当前版本的所有关键字：

```bash
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```


### 1.4 注释

Python中单行注释以 # 开头，实例如下：

```python 
# 第一个注释
print ("Hello, Python!") # 第二个注释
```

执行以上代码，输出结果为：

```bash
Hello, Python!
```

多行注释可以用多个 # 号，还有 ''' 和 """：

```python
# 第一个注释
# 第二个注释
 
'''
第三注释
第四注释
'''
 
"""
第五注释
第六注释
"""
print ("Hello, Python!")
```

执行以上代码，输出结果为：

```bash
Hello, Python!
```


### 1.5 行与缩进

python最具特色的就是使用缩进来表示代码块，不需要使用大括号 {} 。

缩进的空格数是可变的，但是同一个代码块的语句必须包含相同的缩进空格数。实例如下：

```python
if True:
    print ("True")
else:
    print ("False")
```

以下代码最后一行语句缩进数的空格数不一致，会导致运行错误：

```python
if True:
    print ("Answer")
    print ("True")
else:
    print ("Answer")
  print ("False")    # 缩进不一致，会导致运行错误
```

以上程序由于缩进不一致，执行后会出现类似以下错误：

```python
File "test.py", line 6
    print ("False")    # 缩进不一致，会导致运行错误
                                      ^
IndentationError: unindent does not match any outer indentation level
```


### 1.6 多行语句

Python 通常是一行写完一条语句，但如果语句很长，我们可以使用反斜杠 \ 来实现多行语句，例如：

```python
total = item_one + \
        item_two + \
        item_three
```

在 [], {}, 或 () 中的多行语句，不需要使用反斜杠 \，例如：

```python
total = ['item_one', 'item_two', 'item_three',
        'item_four', 'item_five']
```


### 1.7 数字类型

python中数字有四种类型：整数、布尔型、浮点数和复数。

* int (整数), 如 1, 只有一种整数类型 int，表示为长整型，没有 python2 中的 Long。
* bool (布尔), 如 True。
* float (浮点数), 如 1.23、3E-2
* complex (复数), 如 1 + 2j、 1.1 + 2.2j


### 1.8 字符串

* Python 中单引号 ' 和双引号 " 使用完全相同。
* 使用三引号(''' 或 """)可以指定一个多行字符串。
* 转义符 \。
* 反斜杠可以用来转义，使用 r 可以让反斜杠不发生转义。 如 r"this is a line with \n" 则 \n 会显示，并不是换行。
* 按字面意义级联字符串，如 "this " "is " "string" 会被自动转换为 this is string。
* 字符串可以用 + 运算符连接在一起，用 * 运算符重复。
* Python 中的字符串有两种索引方式，从左往右以 0 开始，从右往左以 -1 开始。
* Python 中的字符串不能改变。
* Python 没有单独的字符类型，一个字符就是长度为 1 的字符串。
* 字符串的截取的语法格式如下：变量[头下标:尾下标:步长]

```python
word = '字符串'
sentence = "这是一个句子。"
paragraph = """这是一个段落，
可以由多行组成"""
```

```python
str='123456789'
 
print(str)                 # 输出字符串
print(str[0:-1])           # 输出第一个到倒数第二个的所有字符
print(str[0])              # 输出字符串第一个字符
print(str[2:5])            # 输出从第三个开始到第五个的字符
print(str[2:])             # 输出从第三个开始后的所有字符
print(str[1:5:2])          # 输出从第二个开始到第五个且每隔一个的字符（步长为2）
print(str * 2)             # 输出字符串两次
print(str + '你好')         # 连接字符串
 
print('------------------------------')
 
print('hello\nGoogle')      # 使用反斜杠(\)+n转义特殊字符
print(r'hello\nGoogle')     # 在字符串前面添加一个 r，表示原始字符串，不会发生转义
```

这里的 r 指 raw，即 raw string，会自动将反斜杠转义，例如：

```bash
>>> print('\n')       # 输出空行

>>> print(r'\n')      # 输出 \n
\n
>>>
```

以上实例输出结果：

```bash
123456789
12345678
1
345
3456789
24
123456789123456789
123456789你好
------------------------------
hello
Google
hello\nGoogle
```


### 1.9 空行

函数之间或类的方法之间用空行分隔，表示一段新的代码的开始。类和函数入口之间也用一行空行分隔，以突出函数入口的开始。

空行与代码缩进不同，空行并不是 Python 语法的一部分。书写时不插入空行，Python 解释器运行也不会出错。但是空行的作用在于分隔两段不同功能或含义的代码，便于日后代码的维护或重构。

记住：空行也是程序代码的一部分。


### 1.10 等待用户输入

执行下面的程序在按回车键后就会等待用户输入：

```python
input("\n\n按下 enter 键后退出。")
```

以上代码中 ，\n\n 在结果输出前会输出两个新的空行。一旦用户按下 enter 键时，程序将退出。


### 1.11 同一行显示多条语句

Python 可以在同一行中使用多条语句，语句之间使用分号 ; 分割，以下是一个简单的实例：

```python
import sys; x = 'Google'; sys.stdout.write(x + '\n')
```

以上代码，输出结果为：

```bash
Google
```

使用交互式命令行执行，输出结果为：

```bash
>>> import sys; x = 'Google'; sys.stdout.write(x + '\n')
Google
7
```

此处的 7 表示字符数，Google 有 6 个字符，\n 表示一个字符，加起来 7 个字符。

```bash
>>> import sys
>>> sys.stdout.write(" hi ")    # hi 前后各有 1 个空格
 hi 4
```


### 1.12 多个语句构成代码组

缩进相同的一组语句构成一个代码块，我们称之代码组。

像if、while、def和class这样的复合语句，首行以关键字开始，以冒号( : )结束，该行之后的一行或多行代码构成代码组。

我们将首行及后面的代码组称为一个子句(clause)。

如下实例：

```python
if expression : 
   suite
elif expression : 
   suite 
else : 
   suite
```


### 1.13 print输出

print 默认输出是换行的，如果要实现不换行需要在变量末尾加上 end=""：

```python
x="a"
y="b"
# 换行输出
print( x )
print( y )
 
print('---------')
# 不换行输出
print( x, end=" " )
print( y, end=" " )
print()
```

以上实例执行结果为：

```bash
a
b
---------
a b
```

### 1.14 import与from...import

在 python 用 import 或者 from...import 来导入相应的模块。

将整个模块(somemodule)导入，格式为： import somemodule

从某个模块中导入某个函数,格式为： from somemodule import somefunction

从某个模块中导入多个函数,格式为： from somemodule import firstfunc, secondfunc, thirdfunc

将某个模块中的全部函数导入，格式为： from somemodule import *

```python
import sys
print('================Python import mode==========================')
print ('命令行参数为:')
for i in sys.argv:
    print (i)
print ('\n python 路径为',sys.path)
```

```python
from sys import argv,path  #  导入特定的成员
 
print('================python from import===================================')
print('path:',path) # 因为已经导入path成员，所以此处引用时不需要加sys.path
```


### 1.15 命令行参数

很多程序可以执行一些操作来查看一些基本信息，Python可以使用-h参数查看各参数帮助信息：

```bash
$ python -h
usage: python [option] ... [-c cmd | -m mod | file | -] [arg] ...
Options and arguments (and corresponding environment variables):
-c cmd : program passed in as string (terminates option list)
-d     : debug output from parser (also PYTHONDEBUG=x)
-E     : ignore environment variables (such as PYTHONPATH)
-h     : print this help message and exit

[ etc. ]
```

我们在使用脚本形式执行 Python 时，可以接收命令行输入的参数.



## 2 基本数据类型


Python 中的变量不需要声明。每个变量在使用前都必须赋值，变量赋值以后该变量才会被创建。

在 Python 中，变量就是变量，它没有类型，我们所说的"类型"是变量所指的内存中对象的类型。

等号（=）用来给变量赋值。

等号（=）运算符左边是一个变量名,等号（=）运算符右边是存储在变量中的值。例如：

```python
counter = 100          # 整型变量
miles   = 1000.0       # 浮点型变量
name    = "Google"     # 字符串

print (counter)
print (miles)
print (name)
```

执行以上程序会输出如下结果：

```bash
100
1000.0
Google
```


### 2.1 多个变量赋值

Python允许你同时为多个变量赋值。例如：

```python
a = b = c = 1
```

以上实例，创建一个整型对象，值为 1，从后向前赋值，三个变量被赋予相同的数值。

您也可以为多个对象指定多个变量。例如：

```python
a, b, c = 1, 2, "Google"
```

以上实例，两个整型对象 1 和 2 的分配给变量 a 和 b，字符串对象 "Google" 分配给变量 c。


### 2.2 标准数据类型

Python3 中有六个标准的数据类型：

* Number（数字）
* String（字符串）
* List（列表）
* Tuple（元组）
* Set（集合）
* Dictionary（字典）

Python3 的六个标准数据类型中：

* 不可变数据（3 个）：Number（数字）、String（字符串）、Tuple（元组）；
* 可变数据（3 个）：List（列表）、Dictionary（字典）、Set（集合）。


### 2.3 Number（数字）

Python3 支持 int、float、bool、complex（复数）。

在Python 3里，只有一种整数类型 int，表示为长整型，没有 python2 中的 Long。

像大多数语言一样，数值类型的赋值和计算都是很直观的。

内置的 type() 函数可以用来查询变量所指的对象类型。

```bash
>>> a, b, c, d = 20, 5.5, True, 4+3j
>>> print(type(a), type(b), type(c), type(d))
<class 'int'> <class 'float'> <class 'bool'> <class 'complex'>
```

此外还可以用 isinstance 来判断：

```bash
>>> a = 111
>>> isinstance(a, int)
True
>>>
```

isinstance 和 type 的区别在于：

* type()不会认为子类是一种父类类型。
* isinstance()会认为子类是一种父类类型。

```bash
>>> class A:
...     pass
... 
>>> class B(A):
...     pass
... 
>>> isinstance(A(), A)
True
>>> type(A()) == A 
True
>>> isinstance(B(), A)
True
>>> type(B()) == A
False
```

`注意：Python3 中，bool 是 int 的子类，True 和 False 可以和数字相加， True==1、False==0 会返回 True，但可以通过 is 来判断类型。`

```bash
>>> issubclass(bool, int) 
True
>>> True==1
True
>>> False==0
True
>>> True+1
2
>>> False+1
1
>>> 1 is True
False
>>> 0 is False
False
```

在 Python2 中是没有布尔型的，它用数字 0 表示 False，用 1 表示 True。

当你指定一个值时，Number 对象就会被创建：

```python
var1 = 1
var2 = 10
```

您也可以使用del语句删除一些对象引用。

del语句的语法是：

```python
del var1[,var2[,var3[....,varN]]]
```

您可以通过使用del语句删除单个或多个对象。例如：

```python
del var
del var_a, var_b
```


### 2.4 数值运算

```bash
>>> 5 + 4  # 加法
9
>>> 4.3 - 2 # 减法
2.3
>>> 3 * 7  # 乘法
21
>>> 2 / 4  # 除法，得到一个浮点数
0.5
>>> 2 // 4 # 除法，得到一个整数
0
>>> 17 % 3 # 取余
2
>>> 2 ** 5 # 乘方
32
```

注意：

* 1、Python可以同时为多个变量赋值，如a, b = 1, 2。
* 2、一个变量可以通过赋值指向不同类型的对象。
* 3、数值的除法包含两个运算符：/ 返回一个浮点数，// 返回一个整数。
* 4、在混合计算时，Python会把整型转换成为浮点数。


### 2.5 数值类型实例

| int        | float        | complex          |
| ---------- | ------------ | ---------------- |
| 10         | 0.0          | 3.14j            |
| 100        | 17.78        | 45.j             |
| -786       | -21.9        | 9.322e-36j       |
| 080        | 32.3e+18     | .876j            |
| -0490      | -90          | -.6545+0J        |
| -0x260     | -32.54e100   | 3e+26J           |
| 0x79       | 70.2E-12     | 4.53e-7j         |

Python还支持复数，复数由实数部分和虚数部分构成，可以用a + bj,或者complex(a,b)表示， 复数的实部a和虚部b都是浮点型。


### 2.6 String（字符串）

Python中的字符串用单引号 ' 或双引号 " 括起来，同时使用反斜杠 \ 转义特殊字符。

字符串的截取的语法格式如下：

```python
变量[头下标:尾下标]
```

索引值以 0 为开始值，-1 为从末尾的开始位置。

```bash
从后面索引：        -6  -5  -4  -3  -2  -1
从前面索引：         0   1   2   3   4   5
                    G   o   o   g   l   e
从前面截取：         :  1   2   3   4   5 :
从后面截取：         : -5  -4  -3  -2  -1 :
```

加号 + 是字符串的连接符， 星号 * 表示复制当前字符串，与之结合的数字为复制的次数。实例如下：

```python
str = 'Google'

print (str)          # 输出字符串
print (str[0:-1])    # 输出第一个到倒数第二个的所有字符
print (str[0])       # 输出字符串第一个字符
print (str[2:5])     # 输出从第三个开始到第五个的字符
print (str[2:])      # 输出从第三个开始的后的所有字符
print (str * 2)      # 输出字符串两次，也可以写成 print (2 * str)
print (str + "TEST") # 连接字符串
```

执行以上程序会输出如下结果：

```bash
Google
Googl
G
ogl
ogle
GoogleGoogle
GoogleTEST
```

Python 使用反斜杠 \ 转义特殊字符，如果你不想让反斜杠发生转义，可以在字符串前面添加一个 r，表示原始字符串：

```bash
>>> print('Twi\tter')
Twi     ter
>>> print(r'Twi\tter')
Twi\tter
>>>
```

另外，反斜杠(\)可以作为续行符，表示下一行是上一行的延续。也可以使用 """...""" 或者 '''...''' 跨越多行。

注意，Python 没有单独的字符类型，一个字符就是长度为1的字符串。

```bash
>>> word = 'Python'
>>> print(word[0], word[5])
P n
>>> print(word[-1], word[-6])
n P
```

与 C 字符串不同的是，Python 字符串不能被改变。向一个索引位置赋值，比如word[0] = 'm'会导致错误。

注意：

* 1、反斜杠可以用来转义，使用r可以让反斜杠不发生转义。
* 2、字符串可以用+运算符连接在一起，用*运算符重复。
* 3、Python中的字符串有两种索引方式，从左往右以0开始，从右往左以-1开始。
* 4、Python中的字符串不能改变。


### 2.7 List（列表）

List（列表） 是 Python 中使用最频繁的数据类型。

列表可以完成大多数集合类的数据结构实现。列表中元素的类型可以不相同，它支持数字，字符串甚至可以包含列表（所谓嵌套）。

列表是写在方括号 [] 之间、用逗号分隔开的元素列表。

和字符串一样，列表同样可以被索引和截取，列表被截取后返回一个包含所需元素的新列表。

列表截取的语法格式如下：

```python
变量[头下标:尾下标]
```

索引值以 0 为开始值，-1 为从末尾的开始位置。

加号 + 是列表连接运算符，星号 * 是重复操作。如下实例：

```python
list = [ 'abcd', 786 , 2.23, 'Google', 70.2 ]
tinylist = [123, 'Google']

print (list)            # 输出完整列表
print (list[0])         # 输出列表第一个元素
print (list[1:3])       # 从第二个开始输出到第三个元素
print (list[2:])        # 输出从第三个元素开始的所有元素
print (tinylist * 2)    # 输出两次列表
print (list + tinylist) # 连接列表
```

以上实例输出结果：

```bash
['abcd', 786, 2.23, 'Google', 70.2]
abcd
[786, 2.23]
[2.23, 'Google', 70.2]
[123, 'Google', 123, 'Google']
['abcd', 786, 2.23, 'Google', 70.2, 123, 'Google']
```

与Python字符串不一样的是，列表中的元素是可以改变的：

```bash
>>> a = [1, 2, 3, 4, 5, 6]
>>> a[0] = 9
>>> a[2:5] = [13, 14, 15]
>>> a
[9, 2, 13, 14, 15, 6]
>>> a[2:5] = []   # 将对应的元素值设置为 []
>>> a
[9, 2, 6]
```

List 内置了有很多方法，例如 append()、pop() 等等，这在后面会讲到。

注意:

* 1、List写在方括号之间，元素用逗号隔开。
* 2、和字符串一样，list可以被索引和切片。
* 3、List可以使用+操作符进行拼接。
* 4、List中的元素是可以改变的。

Python 列表截取可以接收第三个参数，参数作用是截取的步长，以下实例在索引 1 到索引 4 的位置并设置为步长为 2（间隔一个位置）来截取字符串：

如果第三个参数为负数表示逆向读取，以下实例用于翻转字符串：

```python
def reverseWords(input):
     
    # 通过空格将字符串分隔符，把各个单词分隔为列表
    inputWords = input.split(" ")
 
    # 翻转字符串
    # 假设列表 list = [1,2,3,4],  
    # list[0]=1, list[1]=2 ，而 -1 表示最后一个元素 list[-1]=4 ( 与 list[3]=4 一样)
    # inputWords[-1::-1] 有三个参数
    # 第一个参数 -1 表示最后一个元素
    # 第二个参数为空，表示移动到列表末尾
    # 第三个参数为步长，-1 表示逆向
    inputWords=inputWords[-1::-1]
 
    # 重新组合字符串
    output = ' '.join(inputWords)
     
    return output
 
if __name__ == "__main__":
    input = 'I like Google'
    rw = reverseWords(input)
    print(rw)
```

输出结果为：

```bash
Google like I
```


### 2.8 Tuple（元组）

元组（tuple）与列表类似，不同之处在于元组的元素不能修改。元组写在小括号 () 里，元素之间用逗号隔开。

元组中的元素类型也可以不相同：

```python
tuple = ( 'abcd', 786 , 2.23, 'Google', 70.2  )
tinytuple = (123, 'Google')

print (tuple)             # 输出完整元组
print (tuple[0])          # 输出元组的第一个元素
print (tuple[1:3])        # 输出从第二个元素开始到第三个元素
print (tuple[2:])         # 输出从第三个元素开始的所有元素
print (tinytuple * 2)     # 输出两次元组
print (tuple + tinytuple) # 连接元组
```

以上实例输出结果：

```bash
('abcd', 786, 2.23, 'Google', 70.2)
abcd
(786, 2.23)
(2.23, 'Google', 70.2)
(123, 'Google', 123, 'Google')
('abcd', 786, 2.23, 'Google', 70.2, 123, 'Google')
```

元组与字符串类似，可以被索引且下标索引从0开始，-1 为从末尾开始的位置。也可以进行截取（看上面，这里不再赘述）。

其实，可以把字符串看作一种特殊的元组。

```bash
>>> tup = (1, 2, 3, 4, 5, 6)
>>> print(tup[0])
1
>>> print(tup[1:5])
(2, 3, 4, 5)
>>> tup[0] = 11  # 修改元组元素的操作是非法的
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>>
```

虽然tuple的元素不可改变，但它可以包含可变的对象，比如list列表。

构造包含 0 个或 1 个元素的元组比较特殊，所以有一些额外的语法规则：

```python
tup1 = ()    # 空元组
tup2 = (20,) # 一个元素，需要在元素后添加逗号
```

string、list 和 tuple 都属于 sequence（序列）。

注意:

* 1、与字符串一样，元组的元素不能修改。
* 2、元组也可以被索引和切片，方法一样。
* 3、注意构造包含 0 或 1 个元素的元组的特殊语法规则。
* 4、元组也可以使用+操作符进行拼接。


### 2.9 Set（集合）

集合（set）是由一个或数个形态各异的大小整体组成的，构成集合的事物或对象称作元素或是成员。

基本功能是进行成员关系测试和删除重复元素。

可以使用大括号 { } 或者 set() 函数创建集合，注意：创建一个空集合必须用 set() 而不是 { }，因为 { } 是用来创建一个空字典。

创建格式：

```python
parame = {value01,value02,...}
或者
set(value)
```

实例：

```python
sites = {'Google', 'Taobao', 'Amazon', 'Meta', 'Zhihu', 'Baidu'}

print(sites)   # 输出集合，重复的元素被自动去掉

# 成员测试
if 'Google' in sites :
    print('Google 在集合中')
else :
    print('Google 不在集合中')


# set可以进行集合运算
a = set('abracadabra')
b = set('alacazam')

print(a)

print(a - b)     # a 和 b 的差集

print(a | b)     # a 和 b 的并集

print(a & b)     # a 和 b 的交集

print(a ^ b)     # a 和 b 中不同时存在的元素
```

以上实例输出结果：

```bash
{'Taobao', 'Meta', 'Google', 'Baidu', 'Zhihu', 'Amazon'}
Google 在集合中
{'d', 'a', 'b', 'r', 'c'}
{'d', 'r', 'b'}
{'m', 'l', 'z', 'd', 'a', 'b', 'r', 'c'}
{'a', 'c'}
{'d', 'm', 'l', 'b', 'r', 'z'}
```


### 2.10 Dictionary（字典）

字典（dictionary）是Python中另一个非常有用的内置数据类型。

列表是有序的对象集合，字典是无序的对象集合。两者之间的区别在于：字典当中的元素是通过键来存取的，而不是通过偏移存取。

字典是一种映射类型，字典用 { } 标识，它是一个无序的 键(key) : 值(value) 的集合。

键(key)必须使用不可变类型。

在同一个字典中，键(key)必须是唯一的。

```python
dict = {}
dict['one'] = "1 - Python教程"
dict[2]     = "2 - Python工具"

tinydict = {'name': 'Google','code':1, 'site': 'www.Google.com'}


print (dict['one'])       # 输出键为 'one' 的值
print (dict[2])           # 输出键为 2 的值
print (tinydict)          # 输出完整的字典
print (tinydict.keys())   # 输出所有键
print (tinydict.values()) # 输出所有值
```

以上实例输出结果：

```bash
1 - Python教程
2 - Python工具
{'name': 'Google', 'code': 1, 'site': 'www.Google.com'}
dict_keys(['name', 'code', 'site'])
dict_values(['Google', 1, 'www.Google.com'])
```

构造函数 dict() 可以直接从键值对序列中构建字典如下：

```bash
>>> dict([('Google', 1), ('Amazon', 2), ('Taobao', 3)])
{'Google': 1, 'Amazon': 2, 'Taobao': 3}
>>> {x: x**2 for x in (2, 4, 6)}
{2: 4, 4: 16, 6: 36}
>>> dict(Google=1, Amazon=2, Taobao=3)
{'Google': 1, 'Amazon': 2, 'Taobao': 3}
```

{x: x**2 for x in (2, 4, 6)} 该代码使用的是字典推导式，更多推导式内容可以参考：Python 推导式。

另外，字典类型也有一些内置的函数，例如 clear()、keys()、values() 等。

注意：

* 1、字典是一种映射类型，它的元素是键值对。
* 2、字典的关键字必须为不可变类型，且不能重复。
* 3、创建空字典使用 { }。


### 2.11 Python数据类型转换

有时候，我们需要对数据内置的类型进行转换，数据类型的转换，你只需要将数据类型作为函数名即可。

以下几个内置的函数可以执行数据类型之间的转换。这些函数返回一个新的对象，表示转换的值。

| function              | description                                       |
| --------------------- | ------------------------------------------------- |
| int(x [,base])        | 将x转换为一个整数                                   |
| float(x)              | 将x转换到一个浮点数                                 |
| complex(real [,imag]) | 创建一个复数                                       |
| str(x)                | 将对象 x 转换为字符串                               |
| repr(x)               | 将对象 x 转换为表达式字符串                         |
| eval(str)             | 用来计算在字符串中的有效Python表达式,并返回一个对象   |
| tuple(s)              | 将序列 s 转换为一个元组                             |
| list(s)               | 将序列 s 转换为一个列表                             |
| set(s)                | 转换为可变集合                                      |
| dict(d)               | 创建一个字典。d 必须是一个 (key, value)元组序列。     |
| frozenset(s)          | 转换为不可变集合                                    |
| chr(x)                | 将一个整数转换为一个字符                             |
| ord(x)                | 将一个字符转换为它的整数值                           |
| hex(x)                | 将一个整数转换为一个十六进制字符串                    |
| oct(x)                | 将一个整数转换为一个八进制字符串                      |



## 3 数据类型转换

有时候，我们需要对数据内置的类型进行转换，数据类型的转换，一般情况下你只需要将数据类型作为函数名即可。

Python 数据类型转换可以分为两种：

* 隐式类型转换 - 自动完成
* 显式类型转换 - 需要使用类型函数来转换


### 3.1 隐式类型转换

在隐式类型转换中，Python 会自动将一种数据类型转换为另一种数据类型，不需要我们去干预。

以下实例中，我们对两种不同类型的数据进行运算，较低数据类型（整数）就会转换为较高数据类型（浮点数）以避免数据丢失。

```python
num_int = 123
num_flo = 1.23

num_new = num_int + num_flo

print("datatype of num_int:",type(num_int))
print("datatype of num_flo:",type(num_flo))

print("Value of num_new:",num_new)
print("datatype of num_new:",type(num_new))
```

以上实例输出结果为：

```bash
num_int 数据类型为: <class 'int'>
num_flo 数据类型为: <class 'float'>
num_new: 值为: 124.23
num_new 数据类型为: <class 'float'>
```

代码解析：

* 实例中我们对两个不同数据类型的变量 num_int 和 num_flo 进行相加运算，并存储在变量 num_new 中。
* 然后查看三个变量的数据类型。
* 在输出结果中，我们看到 num_int 是 整型（integer） ， num_flo 是 浮点型（float）。
* 同样，新的变量 num_new 是 浮点型（float），这是因为 Python 会将较小的数据类型转换为较大的数据类型，以避免数据丢失。

我们再看一个实例，整型数据与字符串类型的数据进行相加：

```python
num_int = 123
num_str = "456"

print("Data type of num_int:",type(num_int))
print("Data type of num_str:",type(num_str))

print(num_int+num_str)
```

以上实例输出结果为：

```bash
num_int 数据类型为: <class 'int'>
num_str 数据类型为: <class 'str'>
Traceback (most recent call last):
  File "/Google-test/test.py", line 7, in <module>
    print(num_int+num_str)
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

从输出中可以看出，整型和字符串类型运算结果会报错，输出 TypeError。 Python 在这种情况下无法使用隐式转换。

但是，Python 为这些类型的情况提供了一种解决方案，称为显式转换。

### 3.2 显式类型转换

在显式类型转换中，用户将对象的数据类型转换为所需的数据类型。 我们使用 int()、float()、str() 等预定义函数来执行显式类型转换。

int() 强制转换为整型：

```python
x = int(1)   # x 输出结果为 1
y = int(2.8) # y 输出结果为 2
z = int("3") # z 输出结果为 3
```

float() 强制转换为浮点型：

```python
x = float(1)     # x 输出结果为 1.0
y = float(2.8)   # y 输出结果为 2.8
z = float("3")   # z 输出结果为 3.0
w = float("4.2") # w 输出结果为 4.2
```

str() 强制转换为字符串类型：

```python
x = str("s1") # x 输出结果为 's1'
y = str(2)    # y 输出结果为 '2'
z = str(3.0)  # z 输出结果为 '3.0'
```

整型和字符串类型进行运算，就可以用强制类型转换来完成：

```python
num_int = 123
num_str = "456"

print("num_int 数据类型为:",type(num_int))
print("类型转换前，num_str 数据类型为:",type(num_str))

num_str = int(num_str)    # 强制转换为整型
print("类型转换后，num_str 数据类型为:",type(num_str))

num_sum = num_int + num_str

print("num_int 与 num_str 相加结果为:",num_sum)
print("sum 数据类型为:",type(num_sum))
```

以上实例输出结果为：

```bash
num_int 数据类型为: <class 'int'>
类型转换前，num_str 数据类型为: <class 'str'>
类型转换后，num_str 数据类型为: <class 'int'>
num_int 与 num_str 相加结果为: 579
sum 数据类型为: <class 'int'>
```

以下几个内置的函数可以执行数据类型之间的转换。这些函数返回一个新的对象，表示转换的值。

| function              | description                                       |
| --------------------- | ------------------------------------------------- |
| int(x [,base])        | 将x转换为一个整数                                   |
| float(x)              | 将x转换到一个浮点数                                 |
| complex(real [,imag]) | 创建一个复数                                       |
| str(x)                | 将对象 x 转换为字符串                               |
| repr(x)               | 将对象 x 转换为表达式字符串                         |
| eval(str)             | 用来计算在字符串中的有效Python表达式,并返回一个对象   |
| tuple(s)              | 将序列 s 转换为一个元组                             |
| list(s)               | 将序列 s 转换为一个列表                             |
| set(s)                | 转换为可变集合                                      |
| dict(d)               | 创建一个字典。d 必须是一个 (key, value)元组序列。     |
| frozenset(s)          | 转换为不可变集合                                    |
| chr(x)                | 将一个整数转换为一个字符                             |
| ord(x)                | 将一个字符转换为它的整数值                           |
| hex(x)                | 将一个整数转换为一个十六进制字符串                    |
| oct(x)                | 将一个整数转换为一个八进制字符串                      |



## 4 推导式

Python 推导式是一种独特的数据处理方式，可以从一个数据序列构建另一个新的数据序列的结构体。

Python 支持各种数据结构的推导式：

* 列表(list)推导式
* 字典(dict)推导式
* 集合(set)推导式
* 元组(tuple)推导式

### 4.1 列表(list)推导式

列表推导式格式为：

```python
[表达式 for 变量 in 列表] 
[out_exp_res for out_exp in input_list]

或者 

[表达式 for 变量 in 列表 if 条件]
[out_exp_res for out_exp in input_list if condition]
```

* out_exp_res：列表生成元素表达式，可以是有返回值的函数。
* for out_exp in input_list：迭代 input_list 将 out_exp 传入到 out_exp_res 表达式中。
* if condition：条件语句，可以过滤列表中不符合条件的值。

过滤掉长度小于或等于3的字符串列表，并将剩下的转换成大写字母：

```bash
>>> names = ['Bob','Tom','alice','Jerry','Wendy','Smith']
>>> new_names = [name.upper()for name in names if len(name)>3]
>>> print(new_names)
['ALICE', 'JERRY', 'WENDY', 'SMITH']
```

计算 30 以内可以被 3 整除的整数：

```bash
>>> multiples = [i for i in range(30) if i % 3 == 0]
>>> print(multiples)
[0, 3, 6, 9, 12, 15, 18, 21, 24, 27]
```

### 4.2 字典(dict)推导式

字典推导基本格式：

```python
{ key_expr: value_expr for value in collection }

或

{ key_expr: value_expr for value in collection if condition }
```

使用字符串及其长度创建字典：

```bash
>>> listdemo = ['Google','Amazon', 'Taobao']
# 将列表中各字符串值为键，各字符串的长度为值，组成键值对
>>> newdict = {key:len(key) for key in listdemo}
>>> newdict
{'Google': 6, 'Amazon': 6, 'Taobao': 6}
```

提供三个数字，以三个数字为键，三个数字的平方为值来创建字典：

```bash
>>> dic = {x: x**2 for x in (2, 4, 6)}
>>> dic
{2: 4, 4: 16, 6: 36}
>>> type(dic)
<class 'dict'>
```


### 4.3 集合(set)推导式

集合推导式基本格式：

```python
{ expression for item in Sequence }
或
{ expression for item in Sequence if conditional }
```

计算数字 1,2,3 的平方数：

```bash
>>> setnew = {i**2 for i in (1,2,3)}
>>> setnew
{1, 4, 9}
```

判断不是 abc 的字母并输出：

```bash
>>> a = {x for x in 'abracadabra' if x not in 'abc'}
>>> a
{'d', 'r'}
>>> type(a)
<class 'set'>
```


### 4.4 元组(tuple)推导式

元组推导式可以利用 range 区间、元组、列表、字典和集合等数据类型，快速生成一个满足指定需求的元组。

元组推导式基本格式：

```python
(expression for item in Sequence )
或
(expression for item in Sequence if conditional )
```

元组推导式和列表推导式的用法也完全相同，只是元组推导式是用 () 圆括号将各部分括起来，而列表推导式用的是中括号 []，另外元组推导式返回的结果是一个生成器对象。

例如，我们可以使用下面的代码生成一个包含数字 1~9 的元组：

```python
>>> a = (x for x in range(1,10))
>>> a
<generator object <genexpr> at 0x7faf6ee20a50>  # 返回的是生成器对象

>>> tuple(a)       # 使用 tuple() 函数，可以直接将生成器对象转换成元组
(1, 2, 3, 4, 5, 6, 7, 8, 9)
```



## 5 解释器

Linux/Unix的系统上，一般默认的 python 版本为 2.x，我们可以将 python3.x 安装在 /usr/local/python3 目录中。

安装完成后，我们可以将路径 /usr/local/python3/bin 添加到您的 Linux/Unix 操作系统的环境变量中，这样您就可以通过 shell 终端输入下面的命令来启动 Python3 。

```bash
$ PATH=$PATH:/usr/local/python3/bin/python3    # 设置环境变量
$ python3 --version
Python 3.4.0
```

在Window系统下你可以通过以下命令来设置Python的环境变量，假设你的Python安装在 C:\Python34 下:

```bash
set path=%path%;C:\python34
```

### 5.1 交互式编程

我们可以在命令提示符中输入"Python"命令来启动Python解释器：

```bash
$ python3
```

执行以上命令后，出现如下窗口信息：

```bash
$ python3
Python 3.4.0 (default, Apr 11 2014, 13:05:11) 
[GCC 4.8.2] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

在 python 提示符中输入以下语句，然后按回车键查看运行效果：

```bash
print ("Hello, Python!");
```

以上命令执行结果如下：

```bash
Hello, Python!
```

当键入一个多行结构时，续行是必须的。我们可以看下如下 if 语句：

```bash
>>> flag = True
>>> if flag :
...     print("flag 条件为 True!")
... 
flag 条件为 True!
```


### 5.2 脚本式编程

将如下代码拷贝至 hello.py文件中：

```bash
print ("Hello, Python!");
```

通过以下命令执行该脚本：

```python
python3 hello.py
```

输出结果为：

```python
Hello, Python!
```

在Linux/Unix系统中，你可以在脚本顶部添加以下命令让Python脚本可以像SHELL脚本一样可直接执行：

```bash
#! /usr/bin/env python3
```

然后修改脚本权限，使其有执行权限，命令如下：

```bash
$ chmod +x hello.py
```

执行以下命令：

```bash
./hello.py
```

输出结果为：

```bash
Hello, Python!
```



## 6 注释

确保对模块, 函数, 方法和行内注释使用正确的风格。

Python 中的注释有单行注释和多行注释。

Python 中单行注释以 # 开头，例如：

```python
# 这是一个注释
print("Hello, World!")
```

多行注释用三个单引号 ''' 或者三个双引号 """ 将注释括起来，例如:


### 6.1 单引号（'''）

```python
'''
这是多行注释，用三个单引号
这是多行注释，用三个单引号 
这是多行注释，用三个单引号
'''
print("Hello, World!")
```


### 6.2 双引号（"""）

```python
"""
这是多行注释，用三个双引号
这是多行注释，用三个双引号 
这是多行注释，用三个双引号
"""
print("Hello, World!")
```



## 7 运算符

### 7.1 什么是运算符？

本章节主要说明 Python 的运算符。

举个简单的例子:

```python
4 + 5 = 9
```

例子中，4 和 5 被称为操作数，+ 称为运算符。

Python 语言支持以下类型的运算符:

* 算术运算符
* 比较（关系）运算符
* 赋值运算符
* 逻辑运算符
* 位运算符
* 成员运算符
* 身份运算符
* 运算符优先级

接下来让我们一个个来学习Python的运算符。


### 7.2 算术运算符

以下假设变量 a=10，变量 b=21：

| operator   | description                                  | example                 |
| ---------- | -------------------------------------------- | ----------------------- |
| +          | 加 - 两个对象相加                             | a + b 输出结果 31       |
| -          | 减 - 得到负数或是一个数减去另一个数             | a - b 输出结果 -11      |
| *          | 乘 - 两个数相乘或是返回一个被重复若干次的字符串  | a * b 输出结果 210      |
| /          | 除 - x 除以 y                                 | b / a 输出结果 2.1      |
| %          | 取模 - 返回除法的余数                          | b % a 输出结果 1        |
| **         | 幂 - 返回x的y次幂                             | a**b 为10的21次方        |
| //         | 取整除 - 向下取接近商的整数                    | 9//2 == 4, -9//2 == -5  |

以下实例演示了Python所有算术运算符的操作：

```python
a = 21
b = 10
c = 0
 
c = a + b
print ("1 - c 的值为：", c)
 
c = a - b
print ("2 - c 的值为：", c)
 
c = a * b
print ("3 - c 的值为：", c)
 
c = a / b
print ("4 - c 的值为：", c)
 
c = a % b
print ("5 - c 的值为：", c)
 
# 修改变量 a 、b 、c
a = 2
b = 3
c = a**b 
print ("6 - c 的值为：", c)
 
a = 10
b = 5
c = a//b 
print ("7 - c 的值为：", c)
```

以上实例输出结果：

```bash
1 - c 的值为： 31
2 - c 的值为： 11
3 - c 的值为： 210
4 - c 的值为： 2.1
5 - c 的值为： 1
6 - c 的值为： 8
7 - c 的值为： 2
```


### 7.3 比较运算符

以下假设变量a为10，变量b为20：

| operator   | description                     | example                |
| ---------- | ------------------------------- | ---------------------- |
| ==         | 等于 - 比较对象是否相等           | (a == b) 返回 False。  |
| !=         | 不等于 - 比较两个对象是否不相等    | (a != b) 返回 True。   |
| >          | 大于 - 返回x是否大于y             | (a > b) 返回 False。   |
| <          | 小于 - 返回x是否小于y。           | (a < b) 返回 True。    |
| >=         | 大于等于 - 返回x是否大于等于y。    | (a >= b) 返回 False。  |
| <=         | 小于等于 - 返回x是否小于等于y。    | (a <= b) 返回 True。   |

以下实例演示了Python所有比较运算符的操作：

```python
a = 21
b = 10
c = 0
 
if ( a == b ):
   print ("1 - a 等于 b")
else:
   print ("1 - a 不等于 b")
 
if ( a != b ):
   print ("2 - a 不等于 b")
else:
   print ("2 - a 等于 b")
 
if ( a < b ):
   print ("3 - a 小于 b")
else:
   print ("3 - a 大于等于 b")
 
if ( a > b ):
   print ("4 - a 大于 b")
else:
   print ("4 - a 小于等于 b")
 
# 修改变量 a 和 b 的值
a = 5
b = 20
if ( a <= b ):
   print ("5 - a 小于等于 b")
else:
   print ("5 - a 大于  b")
 
if ( b >= a ):
   print ("6 - b 大于等于 a")
else:
   print ("6 - b 小于 a")
```

以上实例输出结果：

```bash
1 - a 不等于 b
2 - a 不等于 b
3 - a 大于等于 b
4 - a 大于 b
5 - a 小于等于 b
6 - b 大于等于 a
```


### 7.4 赋值运算符

以下假设变量a为10，变量b为20：

| operator   | description                         | example                                                  |
| ---------- | ----------------------------------- | -------------------------------------------------------- |
| =          | 简单的赋值运算符                     | c = a + b 将 a + b 的运算结果赋值为 c                      |
| +=         | 加法赋值运算符                       | c += a 等效于 c = c + a                                   |
| -=         | 减法赋值运算符                       | c -= a 等效于 c = c - a                                   |
| *=         | 乘法赋值运算符                       | c *= a 等效于 c = c * a                                   |
| /=         | 除法赋值运算符                       | c /= a 等效于 c = c / a                                   |
| %=         | 取模赋值运算符                       | c %= a 等效于 c = c % a                                   |
| **=        | 幂赋值运算符                         | c **= a 等效于 c = c ** a                                 |
| //=        | 取整除赋值运算符                     | c //= a 等效于 c = c // a                                 |
| :=         | 海象运算符，可在表达式内部为变量赋值   | 赋值表达式可以避免调用 len() 两次: if (n := len(a)) > 10:   |

以下实例演示了Python所有赋值运算符的操作：

```python
a = 21
b = 10
c = 0
 
c = a + b
print ("1 - c 的值为：", c)
 
c += a
print ("2 - c 的值为：", c)
 
c *= a
print ("3 - c 的值为：", c)
 
c /= a 
print ("4 - c 的值为：", c)
 
c = 2
c %= a
print ("5 - c 的值为：", c)
 
c **= a
print ("6 - c 的值为：", c)
 
c //= a
print ("7 - c 的值为：", c)
```

以上实例输出结果：

```bash
1 - c 的值为： 31
2 - c 的值为： 52
3 - c 的值为： 1092
4 - c 的值为： 52.0
5 - c 的值为： 2
6 - c 的值为： 2097152
7 - c 的值为： 99864
```


### 7.5 位运算符

按位运算符是把数字看作二进制来进行计算的。Python中的按位运算法则如下：

下表中变量 a 为 60，b 为 13二进制格式如下：

```bash
a = 0011 1100

b = 0000 1101

-----------------

a&b = 0000 1100

a|b = 0011 1101

a^b = 0011 0001

~a  = 1100 0011
```

| operator   | description     | example               |
| ---------- | --------------- | --------------------- |
| &          | 按位与运算符     | (a & b) 输出结果 12    |
| |          | 按位或运算符     | (a | b) 输出结果 61    |
| ^          | 按位异或运算符   | (a ^ b) 输出结果 49    |
| ~          | 按位取反运算符   | a取反输出-61           |
| <<         | 左移动运算符     | a << 2 输出结果 240    |
| >>         | 右移动运算符     | a >> 2 输出结果 15     |

以下实例演示了Python所有位运算符的操作：

```python
a = 60            # 60 = 0011 1100 
b = 13            # 13 = 0000 1101 
c = 0
 
c = a & b        # 12 = 0000 1100
print ("1 - c 的值为：", c)
 
c = a | b        # 61 = 0011 1101 
print ("2 - c 的值为：", c)
 
c = a ^ b        # 49 = 0011 0001
print ("3 - c 的值为：", c)
 
c = ~a           # -61 = 1100 0011
print ("4 - c 的值为：", c)
 
c = a << 2       # 240 = 1111 0000
print ("5 - c 的值为：", c)
 
c = a >> 2       # 15 = 0000 1111
print ("6 - c 的值为：", c)
```

以上实例输出结果：

```bash
1 - c 的值为： 12
2 - c 的值为： 61
3 - c 的值为： 49
4 - c 的值为： -61
5 - c 的值为： 240
6 - c 的值为： 15
```


### 7.6 逻辑运算符

Python语言支持逻辑运算符，以下假设变量 a 为 10, b为 20:

| operator   | expression      | description                                                         |
| ---------- | --------------- | ------------------------------------------------------------------  |
| and        | x and y         | 布尔"与" - 如果 x 为 False，x and y 返回 x 的值，否则返回 y 的计算值    |
| or         | x or y	       | 布尔"或" - 如果 x 是 True，它返回 x 的值，否则它返回 y 的计算值         |
| not        | not x	       | 布尔"非" - 如果 x 为 True，返回 False 。如果 x 为 False，它返回 True   |

以上实例输出结果：

```python
a = 10
b = 20
 
if ( a and b ):
   print ("1 - 变量 a 和 b 都为 true")
else:
   print ("1 - 变量 a 和 b 有一个不为 true")
 
if ( a or b ):
   print ("2 - 变量 a 和 b 都为 true，或其中一个变量为 true")
else:
   print ("2 - 变量 a 和 b 都不为 true")
 
# 修改变量 a 的值
a = 0
if ( a and b ):
   print ("3 - 变量 a 和 b 都为 true")
else:
   print ("3 - 变量 a 和 b 有一个不为 true")
 
if ( a or b ):
   print ("4 - 变量 a 和 b 都为 true，或其中一个变量为 true")
else:
   print ("4 - 变量 a 和 b 都不为 true")
 
if not( a and b ):
   print ("5 - 变量 a 和 b 都为 false，或其中一个变量为 false")
else:
   print ("5 - 变量 a 和 b 都为 true")
```

以上实例输出结果：

```bash
1 - 变量 a 和 b 都为 true
2 - 变量 a 和 b 都为 true，或其中一个变量为 true
3 - 变量 a 和 b 有一个不为 true
4 - 变量 a 和 b 都为 true，或其中一个变量为 true
5 - 变量 a 和 b 都为 false，或其中一个变量为 false
```


### 7.7 成员运算符

除了以上的一些运算符之外，Python还支持成员运算符，测试实例中包含了一系列的成员，包括字符串，列表或元组。

| operator   | description                                      | example                                       |
| ---------- | ------------------------------------------------ | --------------------------------------------- |
| in         | 如果在指定的序列中找到值返回 True，否则返回 False    | x 在 y 序列中 , 如果 x 在 y 序列中返回 True     |
| not in     | 如果在指定的序列中没有找到值返回 True，否则返回 False| x 不在 y 序列中 , 如果 x 不在 y 序列中返回 True |

以下实例演示了Python所有成员运算符的操作：

```python
a = 10
b = 20
list = [1, 2, 3, 4, 5 ]
 
if ( a in list ):
   print ("1 - 变量 a 在给定的列表中 list 中")
else:
   print ("1 - 变量 a 不在给定的列表中 list 中")
 
if ( b not in list ):
   print ("2 - 变量 b 不在给定的列表中 list 中")
else:
   print ("2 - 变量 b 在给定的列表中 list 中")
 
# 修改变量 a 的值
a = 2
if ( a in list ):
   print ("3 - 变量 a 在给定的列表中 list 中")
else:
   print ("3 - 变量 a 不在给定的列表中 list 中")
```

以上实例输出结果：

```bash
1 - 变量 a 不在给定的列表中 list 中
2 - 变量 b 不在给定的列表中 list 中
3 - 变量 a 在给定的列表中 list 中
```


### 7.8 身份运算符

身份运算符用于比较两个对象的存储单元

| operator   | description                               | example                          |
| ---------- | ----------------------------------------- | -------------------------------- |
| is         | is 是判断两个标识符是不是引用自一个对象      | x is y, 类似 id(x) == id(y)       |
| is not     | is not 是判断两个标识符是不是引用自不同对象	| x is not y ， 类似 id(x) != id(y) |

`注： id() 函数用于获取对象内存地址。`

以下实例演示了Python所有身份运算符的操作：

```python
a = 20
b = 20
 
if ( a is b ):
   print ("1 - a 和 b 有相同的标识")
else:
   print ("1 - a 和 b 没有相同的标识")
 
if ( id(a) == id(b) ):
   print ("2 - a 和 b 有相同的标识")
else:
   print ("2 - a 和 b 没有相同的标识")
 
# 修改变量 b 的值
b = 30
if ( a is b ):
   print ("3 - a 和 b 有相同的标识")
else:
   print ("3 - a 和 b 没有相同的标识")
 
if ( a is not b ):
   print ("4 - a 和 b 没有相同的标识")
else:
   print ("4 - a 和 b 有相同的标识")
```

以上实例输出结果：

```bash
1 - a 和 b 有相同的标识
2 - a 和 b 有相同的标识
3 - a 和 b 没有相同的标识
4 - a 和 b 没有相同的标识
```

is 与 == 区别：

is 用于判断两个变量引用对象是否为同一个， == 用于判断引用变量的值是否相等。

```bash
>>>a = [1, 2, 3]
>>> b = a
>>> b is a 
True
>>> b == a
True
>>> b = a[:]
>>> b is a
False
>>> b == a
True
```

### 7.9 运算符优先级

以下表格列出了从最高到最低优先级的所有运算符：

| operator                  | description                                       |
| ------------------------- | ------------------------------------------------- |
| **                        | 指数 (最高优先级)                                   |
| ~ + -                     | 按位翻转, 一元加号和减号 (最后两个的方法名为 +@ 和 -@)|
| * / % //                  | 乘，除，求余数和取整除                              |
| + -                       | 加法减法                                           |
| >> <<                     | 右移，左移运算符                                    |
| &                         | 位 'AND'                                           |
| ^ |                       | 位运算符                                           |
| <= < > >=                 | 比较运算符                                         |
| == !=                     | 等于运算符                                         |
| = %= /= //= -= += *= **=  | 赋值运算符                                         |
| is is not                 | 身份运算符                                         |
| in not in                 | 成员运算符                                         |
| not and or                | 逻辑运算符                                         |

以下实例演示了Python所有运算符优先级的操作：

```python
a = 20
b = 10
c = 15
d = 5
e = 0
 
e = (a + b) * c / d       #( 30 * 15 ) / 5
print ("(a + b) * c / d 运算结果为：",  e)
 
e = ((a + b) * c) / d     # (30 * 15 ) / 5
print ("((a + b) * c) / d 运算结果为：",  e)
 
e = (a + b) * (c / d)    # (30) * (15/5)
print ("(a + b) * (c / d) 运算结果为：",  e)
 
e = a + (b * c) / d      #  20 + (150/5)
print ("a + (b * c) / d 运算结果为：",  e)
```

以上实例输出结果：

```bash
(a + b) * c / d 运算结果为： 90.0
((a + b) * c) / d 运算结果为： 90.0
(a + b) * (c / d) 运算结果为： 90.0
a + (b * c) / d 运算结果为： 50.0
```

and 拥有更高优先级:

```python
x = True
y = False
z = False
 
if x or y and z:
    print("yes")
else:
    print("no")
```

以上实例输出结果：

```bash
yes
```

注意：Pyhton3 已不支持 <> 运算符，可以使用 != 代替，如果你一定要使用这种比较运算符，可以使用以下的方式：

```bash
>>> from __future__ import barry_as_FLUFL
>>> 1 <> 2
True
```


## 8 数字(Number)

### 8.1 数字

Python 数字数据类型用于存储数值。

数据类型是不允许改变的,这就意味着如果改变数字数据类型的值，将重新分配内存空间。

以下实例在变量赋值时 Number 对象将被创建：

```python
var1 = 1
var2 = 10
```

您也可以使用del语句删除一些数字对象的引用。

del语句的语法是：

```python
del var1[,var2[,var3[....,varN]]]
```

您可以通过使用del语句删除单个或多个对象的引用，例如：

```python
del var
del var_a, var_b
```

Python 支持三种不同的数值类型：

* 整型(int) - 通常被称为是整型或整数，是正或负整数，不带小数点。Python3 整型是没有限制大小的，可以当作 Long 类型使用，所以 Python3 没有 Python2 的 Long 类型。布尔(bool)是整型的子类型。

* 浮点型(float) - 浮点型由整数部分与小数部分组成，浮点型也可以使用科学计数法表示（2.5e2 = 2.5 x 102 = 250）

* 复数( (complex)) - 复数由实数部分和虚数部分构成，可以用a + bj,或者complex(a,b)表示， 复数的实部a和虚部b都是浮点型。

我们可以使用十六进制和八进制来代表整数：

```bash
>>> number = 0xA0F # 十六进制
>>> number
2575

>>> number=0o37 # 八进制
>>> number
31
```

| int        | float        | complex          |
| ---------- | ------------ | ---------------- |
| 10         | 0.0          | 3.14j            |
| 100        | 17.78        | 45.j             |
| -786       | -21.9        | 9.322e-36j       |
| 080        | 32.3e+18     | .876j            |
| -0490      | -90          | -.6545+0J        |
| -0x260     | -32.54e100   | 3e+26J           |
| 0x79       | 70.2E-12     | 4.53e-7j         |

Python还支持复数，复数由实数部分和虚数部分构成，可以用a + bj,或者complex(a,b)表示， 复数的实部a和虚部b都是浮点型。


### 8.2 数字类型转换

有时候，我们需要对数据内置的类型进行转换，数据类型的转换，你只需要将数据类型作为函数名即可。

* int(x) 将x转换为一个整数。

* float(x) 将x转换到一个浮点数。

* complex(x) 将x转换到一个复数，实数部分为 x，虚数部分为 0。

* complex(x, y) 将 x 和 y 转换到一个复数，实数部分为 x，虚数部分为 y。x 和 y 是数字表达式。

以下实例将浮点数变量 a 转换为整数：

```bash
>>> a = 1.0
>>> int(a)
1
```


### 8.3 数字运算

Python 解释器可以作为一个简单的计算器，您可以在解释器里输入一个表达式，它将输出表达式的值。

表达式的语法很直白： +, -, * 和 /, 和其它语言（如Pascal或C）里一样。例如：

```bash
>>> 2 + 2
4
>>> 50 - 5*6
20
>>> (50 - 5*6) / 4
5.0
>>> 8 / 5  # 总是返回一个浮点数
1.6
```

注意：在不同的机器上浮点运算的结果可能会不一样。

在整数除法中，除法 / 总是返回一个浮点数，如果只想得到整数的结果，丢弃可能的分数部分，可以使用运算符 // ：

```bash
>>> 17 / 3  # 整数除法返回浮点型
5.666666666666667
>>>
>>> 17 // 3  # 整数除法返回向下取整后的结果
5
>>> 17 % 3  # ％操作符返回除法的余数
2
>>> 5 * 3 + 2 
17
```

`注意：// 得到的并不一定是整数类型的数，它与分母分子的数据类型有关系。`

```bash
>>> 7//2
3
>>> 7.0//2
3.0
>>> 7//2.0
3.0
>>> 
```

等号 = 用于给变量赋值。赋值之后，除了下一个提示符，解释器不会显示任何结果。

```python
>>> width = 20
>>> height = 5*9
>>> width * height
900
```

Python 可以使用 ** 操作来进行幂运算：

```bash
>>> 5 ** 2  # 5 的平方
25
>>> 2 ** 7  # 2的7次方
128
```

变量在使用前必须先"定义"（即赋予变量一个值），否则会出现错误：

```python
>>> n   # 尝试访问一个未定义的变量
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'n' is not defined
```

不同类型的数混合运算时会将整数转换为浮点数：

```bash
>>> 3 * 3.75 / 1.5
7.5
>>> 7.0 / 2
3.5
```

在交互模式中，最后被输出的表达式结果被赋值给变量 _ 。例如：

```bash
>>> tax = 12.5 / 100
>>> price = 100.50
>>> price * tax
12.5625
>>> price + _
113.0625
>>> round(_, 2)
113.06
```

此处， _ 变量应被用户视为只读变量。


### 8.4 数学函数

| operator        | description                                                        |
| --------------- | ------------------------------------------------------------------ |
| abs(x)          | 返回数字的绝对值，如abs(-10) 返回 10                                 |
| ceil(x)         | 返回数字的上入整数，如math.ceil(4.1) 返回 5                           |
| cmp(x, y)       | 如果 x < y 返回 -1, 如果 x == y 返回 0, 如果 x > y 返回 1             |
| exp(x)          | 返回e的x次幂(ex),如math.exp(1) 返回2.718281828459045                 |
| fabs(x)         | 返回数字的绝对值，如math.fabs(-10) 返回10.0                           |
| floor(x)        | 返回数字的下舍整数，如math.floor(4.9)返回 4                           |
| log(x)          | 如math.log(math.e)返回1.0,math.log(100,10)返回2.0                   |
| log10(x)        | 返回以10为基数的x的对数，如math.log10(100)返回 2.0                    |
| max(x1, x2,...) | 返回给定参数的最大值，参数可以为序列                                   |
| min(x1, x2,...) | 返回给定参数的最小值，参数可以为序列                                   |
| modf(x)         | 返回x的整数部分与小数部分，两部分的数值符号与x相同，整数部分以浮点型表示  |
| pow(x, y)       | x**y 运算后的值                                                     |
| round(x [,n])   | 返回浮点数 x 的四舍五入值，如给出 n 值，则代表舍入到小数点后的位数       |
| sqrt(x)         | 返回数字x的平方根                                                    |


### 8.5 随机数函数

随机数可以用于数学，游戏，安全等领域中，还经常被嵌入到算法中，用以提高算法效率，并提高程序的安全性。

Python包含以下常用随机数函数：

| operator                          | description                                                  |
| --------------------------------- | ------------------------------------------------------------ |
| choice(seq)                       | 从序列的元素中随机挑选一个元素                                  |
| randrange ([start,] stop [,step]) | 从指定范围内，按指定基数递增的集合中获取一个随机数，基数默认值为 1 |
| random()                          | 随机生成下一个实数，它在[0,1)范围内                             |
| seed([x])                         | 改变随机数生成器的种子seed                                     |
| shuffle(lst)                      | 将序列的所有元素随机排序                                       |
| uniform(x, y)                     | 随机生成下一个实数，它在[x,y]范围内                             |


### 8.6 三角函数

Python包括以下三角函数：

| operator     | description                                 |
| ------------ | ------------------------------------------- |
| acos(x)      | 返回x的反余弦弧度值                           |
| asin(x)      | 返回x的反正弦弧度值                           |
| atan(x)      | 返回x的反正切弧度值                           |
| atan2(y, x)  | 返回给定的 X 及 Y 坐标值的反正切值             |
| cos(x)       | 返回x的弧度的余弦值                           |
| hypot(x, y)  | 返回欧几里德范数 sqrt(x*x + y*y)              |
| sin(x)       | 返回的x弧度的正弦值                           |
| tan(x)       | 返回x弧度的正切值                             |
| degrees(x)   | 将弧度转换为角度,如degrees(math.pi/2),返回90.0|
| radians(x)   | 将角度转换为弧度                              |


### 8.7 数学常量

| operator   | description              |
| ---------- | ------------------------ |
| >          | 大于                     |
| >          | 大于                     |



## 9 字符串

字符串是 Python 中最常用的数据类型。我们可以使用引号( ' 或 " )来创建字符串。

创建字符串很简单，只要为变量分配一个值即可。例如：

```python
var1 = 'Hello World!'
var2 = "Google"
```


### 9.1 访问字符串中的值

Python 不支持单字符类型，单字符在 Python 中也是作为一个字符串使用。

Python 访问子字符串，可以使用方括号 [] 来截取字符串，字符串的截取的语法格式如下：

```bash
变量[头下标:尾下标]
```

索引值以 0 为开始值，-1 为从末尾的开始位置。

如下实例：

```python
var1 = 'Hello World!'
var2 = "Google"
 
print ("var1[0]: ", var1[0])
print ("var2[1:5]: ", var2[1:5])
```

以上实例执行结果：

```bash
var1[0]:  H
var2[1:5]:  oogl
```


### 9.2 字符串更新

你可以截取字符串的一部分并与其他字段拼接，如下实例：

```python
var1 = 'Hello World!'
 
print ("已更新字符串 : ", var1[:6] + 'Google!')
```

以上实例执行结果

```bash
已更新字符串 :  Hello Google!
```


### 9.3 转义字符

在需要在字符中使用特殊字符时，python 用反斜杠 \ 转义字符。如下表：

| operator   | description                     |
| ---------- | ------------------------------- |
| \(在行尾时) | 续行符                          |
| \\         | 反斜杠符号                       |
| \'         | 单引号                           |
| \"         | 双引号                           |
| \a         | 响铃                             |
| \b         | 退格(Backspace)                  |
| \000       | 空                               |
| \n         | 换行                             |
| \v         | 纵向制表符                       |
| \t         | 横向制表符                       |
| \r         | 回车                             |
| \f         | 换页                             |
| \yyy       | 八进制数，y 代表 0~7 的字符       |
| \xyy       | 十六进制数，以\x开头，y代表的字符  |
| \other     | 其它的字符以普通格式输出          |


### 9.4 字符串运算符

下表实例变量 a 值为字符串 "Hello"，b 变量值为 "Python"：

| operator   | description                                       |                              |
| ---------- | ------------------------------------------------- | ---------------------------- |
| +          | 字符串连接                                         | a + b 输出结果： HelloPython  |
| *          | 重复输出字符串                                     | a*2 输出结果：HelloHello      |
| []         | 通过索引获取字符串中字符                            | a[1] 输出结果 e               |
| [ : ]      | 截取字符串中的一部分，遵循左闭右开原则               | a[1:4] 输出结果 ell           |
| in         | 成员运算符 - 如果字符串中包含给定的字符返回 True     | 'H' in a 输出结果 True         |
| not in     | 成员运算符 - 如果字符串中不包含给定的字符返回 True   | 'M' not in a 输出结果 True     |
| r/R        | 原始字符串 - 所有的字符串都是直接按照字面的意思来使用 |	print( r'\n'), print( R'\n') |
| %          | 格式字符串                                         | 请看下一节内容                 |

```python
a = "Hello"
b = "Python"
 
print("a + b 输出结果：", a + b)
print("a * 2 输出结果：", a * 2)
print("a[1] 输出结果：", a[1])
print("a[1:4] 输出结果：", a[1:4])
 
if( "H" in a) :
    print("H 在变量 a 中")
else :
    print("H 不在变量 a 中")
 
if( "M" not in a) :
    print("M 不在变量 a 中")
else :
    print("M 在变量 a 中")
 
print (r'\n')
print (R'\n')
```

以上实例输出结果为：

```bash
a + b 输出结果： HelloPython
a * 2 输出结果： HelloHello
a[1] 输出结果： e
a[1:4] 输出结果： ell
H 在变量 a 中
M 不在变量 a 中
\n
\n
```


### 9.5 字符串格式化

Python 支持格式化字符串的输出 。尽管这样可能会用到非常复杂的表达式，但最基本的用法是将一个值插入到一个有字符串格式符 %s 的字符串中。

在 Python 中，字符串格式化使用与 C 中 sprintf 函数一样的语法。

```python
print ("我叫 %s 今年 %d 岁!" % ('小明', 10))
```

以上实例输出结果：

```bash
我叫 小明 今年 10 岁!
```

python字符串格式化符号:

| operator   | description                        |
| ---------- | ---------------------------------- |
| %c         | 格式化字符及其ASCII码                |
| %s         | 格式化字符串                        |
| %d         | 格式化整数                          |
| %u         | 格式化无符号整型                     |
| %o         | 格式化无符号八进制数                 |
| %x         | 格式化无符号十六进制数               |
| %X         | 格式化无符号十六进制数（大写）        |
| %f         | 格式化浮点数字，可指定小数点后的精度   |
| %e         | 用科学计数法格式化浮点数              |
| %E         | 作用同%e，用科学计数法格式化浮点数     |
| %g         | %f和%e的简写                         |
| %G         | %f 和 %E 的简写                      |
| %p         | 用十六进制数格式化变量的地址           |

格式化操作符辅助指令:

| operator  | description                                            |
| --------- | ------------------------------------------------------ |
| *         | 定义宽度或者小数点精度                                   |
| -         | 用做左对齐                                              |
| +         | 在正数前面显示加号( + )                                  |
| <sp>      | 在正数前面显示空格                                       |
| #         | 在八进制数前面显示零('0')，在十六进制前面显示'0x'或者'0X'  |
| 0         | 显示的数字前面填充'0'而不是默认的空格                     |
| %         | '%%'输出一个单一的'%'                                   |
| (var)     | 映射变量(字典参数)                                      |
| m.n.      | m 是显示的最小总宽度,n 是小数点后的位数(如果可用的话)      |

Python2.6 开始，新增了一种格式化字符串的函数 str.format()，它增强了字符串格式化的功能。


### 9.6 三引号

python三引号允许一个字符串跨多行，字符串中可以包含换行符、制表符以及其他特殊字符。实例如下

```python
para_str = """这是一个多行字符串的实例
多行字符串可以使用制表符
TAB ( \t )。
也可以使用换行符 [ \n ]。
"""
print (para_str)
```

以上实例执行结果为：

```bash
这是一个多行字符串的实例
多行字符串可以使用制表符
TAB (    )。
也可以使用换行符 [ 
 ]。
```

三引号让程序员从引号和特殊字符串的泥潭里面解脱出来，自始至终保持一小块字符串的格式是所谓的WYSIWYG（所见即所得）格式的。

一个典型的用例是，当你需要一块HTML或者SQL时，这时用字符串组合，特殊字符串转义将会非常的繁琐。

```bash
errHTML = '''
<HTML><HEAD><TITLE>
Friends CGI Demo</TITLE></HEAD>
<BODY><H3>ERROR</H3>
<B>%s</B><P>
<FORM><INPUT TYPE=button VALUE=Back
ONCLICK="window.history.back()"></FORM>
</BODY></HTML>
'''
cursor.execute('''
CREATE TABLE users (  
login VARCHAR(8), 
uid INTEGER,
prid INTEGER)
''')
```


### 9.7 f-string

f-string 是 python3.6 之后版本添加的，称之为字面量格式化字符串，是新的格式化字符串的语法。

之前我们习惯用百分号 (%):

```bash
>>> name = 'Google'
>>> 'Hello %s' % name
'Hello Google'
```

f-string 格式化字符串以 f 开头，后面跟着字符串，字符串中的表达式用大括号 {} 包起来，它会将变量或表达式计算后的值替换进去，实例如下：

```bash
>>> name = 'Google'
>>> f'Hello {name}'  # 替换变量
'Hello Google'
>>> f'{1+2}'         # 使用表达式
'3'

>>> w = {'name': 'Google', 'url': 'www.Google.com'}
>>> f'{w["name"]}: {w["url"]}'
'Google: www.Google.com'
```

用了这种方式明显更简单了，不用再去判断使用 %s，还是 %d。

在 Python 3.8 的版本中可以使用 = 符号来拼接运算表达式与结果：

```bash
>>> x = 1
>>> print(f'{x+1}')   # Python 3.6
2

>>> x = 1
>>> print(f'{x+1=}')   # Python 3.8
x+1=2
```


### 9.8 Unicode字符串

在Python2中，普通字符串是以8位ASCII码进行存储的，而Unicode字符串则存储为16位unicode字符串，这样能够表示更多的字符集。使用的语法是在字符串前面加上前缀 u。

在Python3中，所有的字符串都是Unicode字符串。


### 9.9 字符串内建函数

Python 的字符串常用内建函数如下：

| No. | description                                                                                             |
| --- | ------------------------------------------------------------------------------------------------------- |
| 1   | capitalize(), 将字符串的第一个字符转换为大写                                                               |
| 2   | center(width, fillchar), 返回一个指定的宽度width居中的字符串,fillchar为填充的字符,默认为空格                 |
| 3   | count(str, beg= 0,end=len(string)), 返回str在string里面出现的次数                                         |
| 4   | bytes.decode(encoding="utf-8", errors="strict"), bytes对象的decode()方法来解码给定的bytes对象              |
| 5   | encode(encoding='UTF-8',errors='strict'), 以 encoding 指定的编码格式编码字符串，如果出错默认报ValueError异常 |
| 6   | endswith(suffix, beg=0, end=len(string)), 检查字符串是否以 obj 结束如果是，返回 True,否则返回 False         |
| 7   | expandtabs(tabsize=8), 把字符串 string 中的 tab 符号转为空格，tab 符号默认的空格数是8                        |
| 8   | find(str, beg=0, end=len(string)), 检测 str 是否包含在字符串中，如果包含返回开始的索引值，否则返回-1          |
| 9   | index(str, beg=0, end=len(string)), 跟find()方法一样，只不过如果str不在字符串中会报一个异常                  |
| 10  | isalnum(), 如果字符串至少有一个字符并且所有字符都是字母或数字则返 回 True，否则返回 False                      |
| 11  | isalpha(), 如果字符串至少有一个字符并且所有字符都是字母或中文字则返回 True, 否则返回 False                     |
| 12  | isdigit(), 如果字符串只包含数字则返回 True 否则返回False                                                    |
| 13  | islower(), 如果字符串中字符都是小写，则返回True，否则返回False                                               |
| 14  | isnumeric(), 如果字符串中只包含数字字符，则返回True，否则返回False                                           |
| 15  | isspace(), 如果字符串中只包含空白，则返回True，否则返回False                                                 |
| 16  | istitle(), 如果字符串是标题化的(见 title())则返回 True，否则返回False                                        |
| 17  | isupper(), 如果字符串中包含至少一个区分大小写的字符，则返回True，否则返回False                                 |
| 18  | join(seq), 以指定字符串作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串                        |
| 19  | len(string), 返回字符串长度                                                                                |
| 20  | ljust(width[, fillchar]), 返回一个原字符串左对齐,并使用fillchar填充至长度width的新字符串，fillchar默认为空格   |
| 21  | lower(), 转换字符串中所有大写字符为小写                                                                     |
| 22  | lstrip(), 截掉字符串左边的空格或指定字符                                                                    |
| 23  | maketrans(), 创建字符映射的转换表                                                                          |
| 24  | max(str), 返回字符串str中最大的字母                                                                        |
| 25  | min(str), 返回字符串str中最小的字母                                                                        |
| 26  | replace(old, new [, max]), 把 将字符串中的old替换成new,如果max指定，则替换不超过max次                        |
| 27  | rfind(str, beg=0,end=len(string)), 类似于find()函数，不过是从右边开始查找                                   |
| 28  | rindex( str, beg=0, end=len(string)), 类似于index()，不过是从右边开始                                      |
| 29  | rjust(width,[, fillchar]), 返回一个原字符串右对齐,并使用fillchar(默认空格)填充至长度width的新字符串           |
| 30  | rstrip(), 删除字符串末尾的空格或指定字符                                                                    |
| 31  | split(str="", num=string.count(str)), 以str为分隔符截取字符串，如果num有指定值，则仅截取num+1个子字符串       |
| 32  | splitlines([keepends]), 按照行('\r', '\r\n', \n')分隔，返回一个包含各行作为元素的列表                        |
| 33  | startswith(substr, beg=0,end=len(string)), 检查字符串是否是以指定子字符串substr开头，是则返回True，否则False  |
| 34  | strip([chars]), 在字符串上执行lstrip()和rstrip()                                                           |
| 35  | swapcase(), 将字符串中大写转换为小写，小写转换为大写                                                         |
| 36  | title(), 返回"标题化"的字符串,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle())                     |
| 37  | translate(table, deletechars=""), 根据table给出的表转换string的字符, 要过滤掉的字符放到deletechars参数中     |
| 38  | upper(), 转换字符串中的小写字母为大写                                                                       |
| 39  | zfill(width), 返回长度为width的字符串，原字符串右对齐，前面填充0                                             |
| 40  | isdecimal(), 检查字符串是否只包含十进制字符，如果是返回true，否则返回false                                    |



## 10 列表

序列是 Python 中最基本的数据结构。

序列中的每个值都有对应的位置值，称之为索引，第一个索引是 0，第二个索引是 1，依此类推。

Python 有 6 个序列的内置类型，但最常见的是列表和元组。

列表都可以进行的操作包括索引，切片，加，乘，检查成员。

此外，Python 已经内置确定序列的长度以及确定最大和最小的元素的方法。

列表是最常用的 Python 数据类型，它可以作为一个方括号内的逗号分隔值出现。

列表的数据项不需要具有相同的类型

创建一个列表，只要把逗号分隔的不同的数据项使用方括号括起来即可。如下所示：

```python
list1 = ['Google', 'Amazon', 1998, 1994]
list2 = [1, 2, 3, 4, 5 ]
list3 = ["a", "b", "c", "d"]
list4 = ['red', 'green', 'blue', 'yellow', 'white', 'black']
```


### 10.1 访问列表中的值

与字符串的索引一样，列表索引从 0 开始，第二个索引是 1，依此类推。

通过索引列表可以进行截取、组合等操作。

```python
list = ['red', 'green', 'blue', 'yellow', 'white', 'black']
print( list[0] )
print( list[1] )
print( list[2] )
```

以上实例输出结果：

```bash
red
green
blue
```

索引也可以从尾部开始，最后一个元素的索引为 -1，往前一位为 -2，以此类推。

```python
list = ['red', 'green', 'blue', 'yellow', 'white', 'black']
print( list[-1] )
print( list[-2] )
print( list[-3] )
```

以上实例输出结果：

```bash
black
white
yellow
```

使用下标索引来访问列表中的值，同样你也可以使用方括号 [] 的形式截取字符

```python
nums = [10, 20, 30, 40, 50, 60, 70, 80, 90]
print(nums[0:4])
```

以上实例输出结果：

```bash
[10, 20, 30, 40]
```

使用负数索引值截取：

```python
list = ['Google', 'Amazon', "Zhihu", "Taobao", "Wiki"]
 
# 读取第二位
print ("list[1]: ", list[1])
# 从第二位开始（包含）截取到倒数第二位（不包含）
print ("list[1:-2]: ", list[1:-2])
```

以上实例输出结果：

```bash
list[1]:  Amazon
list[1:-2]:  ['Amazon', 'Zhihu']
```


### 10.2 更新列表

你可以对列表的数据项进行修改或更新，你也可以使用 append() 方法来添加列表项，如下所示：

```python 
list = ['Google', 'Amazon', 1998, 1994]
 
print ("第三个元素为 : ", list[2])
list[2] = 1995
print ("更新后的第三个元素为 : ", list[2])
 
list1 = ['Google', 'Amazon', 'Taobao']
list1.append('Baidu')
print ("更新后的列表 : ", list1)
```

`注意：我们会在接下来的章节讨论 append() 方法的使用。`

以上实例输出结果：

```bash
第三个元素为 :  1998
更新后的第三个元素为 :  1995
更新后的列表 :  ['Google', 'Amazon', 'Taobao', 'Baidu']
```


### 10.3 删除列表元素

可以使用 del 语句来删除列表的的元素，如下实例：

```python
list = ['Google', 'Amazon', 1998, 1994]
 
print ("原始列表 : ", list)
del list[2]
print ("删除第三个元素 : ", list)
```

以上实例输出结果：

```bash
原始列表 :  ['Google', 'Amazon', 1998, 1994]
删除第三个元素 :  ['Google', 'Amazon', 1994]
```

`注意：我们会在接下来的章节讨论 remove() 方法的使用`


### 10.4 列表脚本操作符

列表对 + 和 * 的操作符与字符串相似。+ 号用于组合列表，* 号用于重复列表。

如下所示：

| operator                                | result                         | description        |
| --------------------------------------- | ------------------------------ | ------------------ |
| len([1, 2, 3])                          | 3                              | 长度               |
| [1, 2, 3] + [4, 5, 6]                   | [1, 2, 3, 4, 5, 6]             | 组合               |
| ['Hi!'] * 4                             | ['Hi!', 'Hi!', 'Hi!', 'Hi!']   | 重复               |
| 3 in [1, 2, 3]                          | True                           | 元素是否存在于列表中 |
| for x in [1, 2, 3]: print(x, end=" ")   | 1 2 3                          | 迭代               |


### 10.5 列表截取与拼接

Python的列表截取与字符串操作类型，如下所示：

```python
L=['Google', 'Amazon', 'Taobao']
```

操作：

| operator  | result                | description                                      |
| --------- | --------------------- | ------------------------------------------------ |
| L[2]      | 'Taobao'              | 读取第三个元素                                    |
| L[-2]     | 'Amazon'              | 从右侧开始读取倒数第二个元素: count from the right  |
| L[1:]     | ['Amazon', 'Taobao']  | 输出从第二个元素开始后的所有元素                    |

```bash
>>> L=['Google', 'Amazon', 'Taobao']
>>> L[2]
'Taobao'
>>> L[-2]
'Amazon'
>>> L[1:]
['Amazon', 'Taobao']
>>>
```

列表还支持拼接操作：

```bash
>>>squares = [1, 4, 9, 16, 25]
>>> squares += [36, 49, 64, 81, 100]
>>> squares
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
>>>
```


### 10.6 嵌套列表

使用嵌套列表即在列表里创建其它列表，例如：

```python
>>>a = ['a', 'b', 'c']
>>> n = [1, 2, 3]
>>> x = [a, n]
>>> x
[['a', 'b', 'c'], [1, 2, 3]]
>>> x[0]
['a', 'b', 'c']
>>> x[0][1]
'b'
```


### 10.7 列表函数&方法

Python包含以下函数:

| operator  | description              |
| --------- | ------------------------ |
| len(list) | 列表元素个数              |
| max(list) | 返回列表元素最大值         |
| min(list) | 返回列表元素最小值         |
| list(seq) | 将元组转换为列表           |

Python包含以下方法:

| operator                             | description                                          |
| ------------------------------------ | ---------------------------------------------------- |
| list.append(obj)                     | 在列表末尾添加新的对象                                 |
| list.count(obj)                      | 统计某个元素在列表中出现的次数                          |
| list.extend(seq)                     | 在列表末尾一次性追加另一个序列中的多个值                 |
| list.index(obj)                      | 从列表中找出某个值第一个匹配项的索引位置                 |
| list.insert(index, obj)              | 将对象插入列表                                         |
| list.pop([index=-1])                 | 移除列表中的一个元素(默认最后一个元素),并返回该元素的值   |
| list.remove(obj)                     | 移除列表中某个值的第一个匹配项                          |
| list.reverse()                       | 反向列表中元素                                         |
| list.sort( key=None, reverse=False)  | 对原列表进行排序                                       |
| list.clear()                         | 清空列表                                              |
| list.copy()                          | 复制列表                                              |



## 11 元组

Python 的元组与列表类似，不同之处在于元组的元素不能修改。

元组使用小括号 ( )，列表使用方括号 [ ]。

元组创建很简单，只需要在括号中添加元素，并使用逗号隔开即可。

```bash
>>> tup1 = ('Google', 'Amazon', 1998, 1994)
>>> tup2 = (1, 2, 3, 4, 5 )
>>> tup3 = "a", "b", "c", "d"   #  不需要括号也可以
>>> type(tup3)
<class 'tuple'>
```

创建空元组

```bash
tup1 = ()
```

元组中只包含一个元素时，需要在元素后面添加逗号 , ，否则括号会被当作运算符使用：

```bash
>>> tup1 = (50)
>>> type(tup1)     # 不加逗号，类型为整型
<class 'int'>

>>> tup1 = (50,)
>>> type(tup1)     # 加上逗号，类型为元组
<class 'tuple'>
```

元组与字符串类似，下标索引从 0 开始，可以进行截取，组合等。


### 11.1 访问元组

元组可以使用下标索引来访问元组中的值，如下实例:

```python
tup1 = ('Google', 'Amazon', 1998, 1994)
tup2 = (1, 2, 3, 4, 5, 6, 7 )
 
print ("tup1[0]: ", tup1[0])
print ("tup2[1:5]: ", tup2[1:5])
```

以上实例输出结果：

```bash
tup1[0]:  Google
tup2[1:5]:  (2, 3, 4, 5)
```


### 11.2 修改元组

元组中的元素值是不允许修改的，但我们可以对元组进行连接组合，如下实例:

```python
tup1 = (12, 34.56)
tup2 = ('abc', 'xyz')
 
# 以下修改元组元素操作是非法的。
# tup1[0] = 100
 
# 创建一个新的元组
tup3 = tup1 + tup2
print (tup3)
```

以上实例输出结果：

```bash
(12, 34.56, 'abc', 'xyz')
```


### 11.3 删除元组

元组中的元素值是不允许删除的，但我们可以使用del语句来删除整个元组，如下实例:

```python
tup = ('Google', 'Amazon', 1998, 1994)
 
print (tup)
del tup
print ("删除后的元组 tup : ")
print (tup)
```

以上实例元组被删除后，输出变量会有异常信息，输出如下所示：

```bash
删除后的元组 tup : 

Traceback (most recent call last):
  File "test.py", line 8, in <module>
    print (tup)
NameError: name 'tup' is not defined
```


### 11.4 元组运算符

与字符串一样，元组之间可以使用 + 号和 * 号进行运算。这就意味着他们可以组合和复制，运算后会生成一个新的元组。

| operator                                | result                         | description  |
| --------------------------------------- | ------------------------------ | ------------ |
| len((1, 2, 3))                          | 3                              | 计算元素个数  |
| (1, 2, 3) + (4, 5, 6)                   | (1, 2, 3, 4, 5, 6)             | 连接         |
| ('Hi!',) * 4                            | ('Hi!', 'Hi!', 'Hi!', 'Hi!')   | 复制         |
| 3 in (1, 2, 3)                          | True                           | 元素是否存在  |
| for x in (1, 2, 3): print (x, end=" ")  | 1 2 3                          | 迭代         |



### 11.5 元组索引，截取

因为元组也是一个序列，所以我们可以访问元组中的指定位置的元素，也可以截取索引中的一段元素，如下所示：

元组：

```python
tup = ('Google', 'Amazon', 'Taobao', 'Wiki', 'Twitter', 'Meta')
```

| operator  | result                                           | description                                |
| --------- | ------------------------------------------------ | ------------------------------------------ |
| tup[1]    | 'Amazon'                                         | 读取第二个元素                              |
| tup[-2]   | 'Twitter'                                          | 反向读取，读取倒数第二个元素                  |
| tup[1:]   | ('Amazon', 'Taobao', 'Wiki', 'Twitter', 'Meta')  | 截取元素，从第二个开始后的所有元素            |
| tup[1:4]  | ('Amazon', 'Taobao', 'Wiki')                     | 截取元素，从第二个开始到第四个元素(索引为 3)   |

运行实例如下：

```bash
>>> tup = ('Google', 'Amazon', 'Taobao', 'Wiki', 'Twitter', 'Meta')
>>> tup[1]
'Amazon'
>>> tup[-2]
'Weibo'
>>> tup[1:]
('Amazon', 'Taobao', 'Wiki', 'Twitter', 'Meta')
>>> tup[1:4]
('Amazon', 'Taobao', 'Wiki')
>>>
```


### 11.6 元组内置函数

Python元组包含了以下内置函数

#### 11.6.1 len(tuple)

计算元组元素个数。

```
>>> tuple1 = ('Google', 'Amazon', 'Taobao')
>>> len(tuple1)
3
>>> 
```

##### 11.6.2 max(tuple)

返回元组中元素最大值。

```bash
>>> tuple2 = ('5', '4', '8')
>>> max(tuple2)
'8'
>>> 
```

#### 11.6.3 min(tuple)

返回元组中元素最小值。	

```bash
>>> tuple2 = ('5', '4', '8')
>>> min(tuple2)
'4'
>>> 
```

#### 11.6.4 tuple(iterable)

将可迭代系列转换为元组。	

```bash
>>> list1= ['Google', 'Taobao', 'Amazon', 'Baidu']
>>> tuple1=tuple(list1)
>>> tuple1
('Google', 'Taobao', 'Amazon', 'Baidu')
```


### 11.7 关于元组是不可变的

所谓元组的不可变指的是元组所指向的内存中的内容不可变。

```bash
>>> tup = ('r', 'u', 'n', 'o', 'o', 'b')
>>> tup[0] = 'g'     # 不支持修改元素
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>> id(tup)     # 查看内存地址
4440687904
>>> tup = (1,2,3)
>>> id(tup)
4441088800    # 内存地址不一样了
```

从以上实例可以看出，重新赋值的元组 tup，绑定到新的对象了，不是修改了原来的对象。



## 12 字典

字典是另一种可变容器模型，且可存储任意类型对象。

字典的每个键值 key=>value 对用冒号 : 分割，每个对之间用逗号(,)分割，整个字典包括在花括号 {} 中 ,格式如下所示：

```python
d = {key1 : value1, key2 : value2, key3 : value3 }
```

`注意：dict 作为 Python 的关键字和内置函数，变量名不建议命名为 dict。`

键必须是唯一的，但值则不必。

值可以取任何数据类型，但键必须是不可变的，如字符串，数字。

一个简单的字典实例：

```python
tinydict = {'name': 'google', 'likes': 123, 'url': 'www.google.com'}
```
也可如此创建字典：

```python
tinydict1 = { 'abc': 456 }
tinydict2 = { 'abc': 123, 98.6: 37 }
```


### 12.1 创建空字典

使用大括号 { } 创建空字典：

```python
# 使用大括号 {} 来创建空字典
emptyDict = {}
 
# 打印字典
print(emptyDict)
 
# 查看字典的数量
print("Length:", len(emptyDict))
 
# 查看类型
print(type(emptyDict))
```

以上实例输出结果：

```bash
{}
Length: 0
<class 'dict'>
```

使用内建函数 dict() 创建字典：

```python
emptyDict = dict()
 
# 打印字典
print(emptyDict)
 
# 查看字典的数量
print("Length:",len(emptyDict))
 
# 查看类型
print(type(emptyDict))
```

以上实例输出结果：

```bash
{}
Length: 0
<class 'dict'>
```


### 12.2 访问字典里的值

把相应的键放入到方括号中，如下实例:

```python
tinydict = {'Name': 'Google', 'Age': 7, 'Class': 'First'}
 
print ("tinydict['Name']: ", tinydict['Name'])
print ("tinydict['Age']: ", tinydict['Age'])
```

以上实例输出结果：

```bash
tinydict['Name']:  Google
tinydict['Age']:  7
```

如果用字典里没有的键访问数据，会输出错误如下：

```python 
tinydict = {'Name': 'Google', 'Age': 7, 'Class': 'First'}
 
print ("tinydict['Alice']: ", tinydict['Alice'])
```

以上实例输出结果：

```bash
Traceback (most recent call last):
  File "test.py", line 3, in <module>
    print ("tinydict['Alice']: ", tinydict['Alice'])
KeyError: 'Alice'
```


### 12.3 修改字典

向字典添加新内容的方法是增加新的键/值对，修改或删除已有键/值对如下实例:

```python
tinydict = {'Name': 'Google', 'Age': 24, 'Class': 'First'}

tinydict['Age'] = 25               # 更新 Age
tinydict['School'] = "Python教程"  # 添加信息


print ("tinydict['Age']: ", tinydict['Age'])
print ("tinydict['School']: ", tinydict['School'])
```

以上实例输出结果：

```bash
tinydict['Age']:  25
tinydict['School']:  Python教程
```


### 12.4 删除字典元素

能删单一的元素也能清空字典，清空只需一项操作。

显式删除一个字典用del命令，如下实例：

```python
tinydict = {'Name': 'Google', 'Age': 24, 'Class': 'First'}

del tinydict['Name'] # 删除键 'Name'
tinydict.clear()     # 清空字典
del tinydict         # 删除字典

print ("tinydict['Age']: ", tinydict['Age'])
print ("tinydict['School']: ", tinydict['School'])
```

但这会引发一个异常，因为用执行 del 操作后字典不再存在：

```bash
Traceback (most recent call last):
  File "/python-test/test.py", line 9, in <module>
    print ("tinydict['Age']: ", tinydict['Age'])
NameError: name 'tinydict' is not defined
```

`注：del() 方法后面也会讨论。`


### 12.5 字典键的特性

字典值可以是任何的 python 对象，既可以是标准的对象，也可以是用户定义的，但键不行。

两个重要的点需要记住：

1. 不允许同一个键出现两次。创建时如果同一个键被赋值两次，后一个值会被记住，如下实例：

```python
tinydict = {'Name': 'Python', 'Age': 7, 'Name': 'King'}
 
print ("tinydict['Name']: ", tinydict['Name'])
```

以上实例输出结果：

```bash
tinydict['Name']:  King
```

2. 键必须不可变，所以可以用数字，字符串或元组充当，而用列表就不行，如下实例：

```python
tinydict = {['Name']: 'Python', 'Age': 7}
 
print ("tinydict['Name']: ", tinydict['Name'])
```

以上实例输出结果：

```bash
Traceback (most recent call last):
  File "test.py", line 3, in <module>
    tinydict = {['Name']: 'Python', 'Age': 7}
TypeError: unhashable type: 'list'
```


### 12.6 字典内置函数&方法

Python字典包含了以下内置函数：

1. len(dict)

计算字典元素个数，即键的总数。	

```bash
>>> tinydict = {'Name': 'Python', 'Age': 7, 'Class': 'First'}
>>> len(tinydict)
3
```

2. str(dict)

输出字典，可以打印的字符串表示。	

```bash
>>> tinydict = {'Name': 'Python', 'Age': 7, 'Class': 'First'}
>>> str(tinydict)
"{'Name': 'Python', 'Class': 'First', 'Age': 7}"
```

3. type(variable)

返回输入的变量类型，如果变量是字典就返回字典类型。	

```bash
>>> tinydict = {'Name': 'Python', 'Age': 7, 'Class': 'First'}
>>> type(tinydict)
<class 'dict'>
```

Python字典包含了以下内置方法：

| operator                             | description                                                      |
| ------------------------------------ | ---------------------------------------------------------------- |
| dict.clear()                         | 删除字典内所有元素                                                 |
| dict.copy()                          | 返回一个字典的浅复制                                               |
| dict.fromkeys()                      | 创建一个新字典,以序列seq中元素做字典的键,val为字典所有键对应的初始值   |
| dict.get(key, default=None)          | 返回指定键的值，如果键不在字典中返回default设置的默认值               |
| key in dict                          | 如果键在字典dict里返回true，否则返回false                           |
| dict.items()                         | 以列表返回一个视图对象                                              |
| dict.keys()                          | 返回一个视图对象                                                   |
| dict.setdefault(key, default=None)   | 和get()类似, 但如果键不存在于字典中，将会添加键并将值设为default      |
| dict.update(dict2)                   | 把字典dict2的键/值对更新到dict里                                    |
| dict.values()                        | 返回一个视图对象                                                   |
| pop(key[,default])                   | 删除字典给定键 key 所对应的值，返回值为被删除的值                     |
| popitem()                            | 返回并删除字典中的最后一对键和值                                     |



## 13 集合

集合（set）是一个无序的不重复元素序列。

可以使用大括号 { } 或者 set() 函数创建集合，注意：创建一个空集合必须用 set() 而不是 { }，因为 { } 是用来创建一个空字典。

创建格式：

```python
parame = {value01,value02,...}
或者
set(value)
```

```bash
>>> basket = {'apple', 'orange', 'apple', 'pear', 'orange', 'banana'}
>>> print(basket)                      # 这里演示的是去重功能
{'orange', 'banana', 'pear', 'apple'}
>>> 'orange' in basket                 # 快速判断元素是否在集合内
True
>>> 'crabgrass' in basket
False

>>> # 下面展示两个集合间的运算.
...
>>> a = set('abracadabra')
>>> b = set('alacazam')
>>> a                                  
{'a', 'r', 'b', 'c', 'd'}
>>> a - b                              # 集合a中包含而集合b中不包含的元素
{'r', 'd', 'b'}
>>> a | b                              # 集合a或b中包含的所有元素
{'a', 'c', 'r', 'd', 'b', 'm', 'z', 'l'}
>>> a & b                              # 集合a和b中都包含了的元素
{'a', 'c'}
>>> a ^ b                              # 不同时包含于a和b的元素
{'r', 'd', 'b', 'm', 'z', 'l'}
```

类似列表推导式，同样集合支持集合推导式(Set comprehension):

```bash
>>> a = {x for x in 'abracadabra' if x not in 'abc'}
>>> a
{'r', 'd'}
```


### 13.1 集合的基本操作

#### 13.1.1 添加元素

语法格式如下：

```python
s.add( x )
```

将元素 x 添加到集合 s 中，如果元素已存在，则不进行任何操作。

```bash
>>> thisset = set(("Google", "Amazon", "Taobao"))
>>> thisset.add("Meta")
>>> print(thisset)
{'Taobao', 'Meta', 'Google', 'Amazon'}
```

还有一个方法，也可以添加元素，且参数可以是列表，元组，字典等，语法格式如下：

```python
s.update( x )
```

x 可以有多个，用逗号分开。

```bash
>>> thisset = set(("Google", "Amazon", "Taobao"))
>>> thisset.update({1,3})
>>> print(thisset)
{1, 3, 'Google', 'Taobao', 'Amazon'}
>>> thisset.update([1,4],[5,6])  
>>> print(thisset)
{1, 3, 4, 5, 6, 'Google', 'Taobao', 'Amazon'}
>>>
```

#### 13.1.2 移除元素

语法格式如下：

```python
s.remove( x )
```

将元素 x 从集合 s 中移除，如果元素不存在，则会发生错误。

```bash
>>> thisset = set(("Google", "Amazon", "Taobao"))
>>> thisset.remove("Taobao")
>>> print(thisset)
{'Google', 'Amazon'}
>>> thisset.remove("Meta")   # 不存在会发生错误
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Meta'
>>>
```

此外还有一个方法也是移除集合中的元素，且如果元素不存在，不会发生错误。格式如下所示：

```python
s.discard( x )
```

```bash
>>> thisset = set(("Google", "Amazon", "Taobao"))
>>> thisset.discard("Meta")  # 不存在不会发生错误
>>> print(thisset)
{'Taobao', 'Google', 'Amazon'}
```

我们也可以设置随机删除集合中的一个元素，语法格式如下：

```python
s.pop()
```

```python
thisset = set(("Google", "Amazon", "Taobao", "Meta"))
x = thisset.pop()

print(x)
```

输出结果：

```bash
$ python3 test.py 
Amazon
```

多次执行测试结果都不一样。

set 集合的 pop 方法会对集合进行无序的排列，然后将这个无序排列集合的左面第一个元素进行删除。


#### 13.1.3 计算集合元素个数

语法格式如下：

```python
len(s)
```

计算集合 s 元素个数。

```bash
>>> thisset = set(("Google", "Amazon", "Taobao"))
>>> len(thisset)
3
```

#### 13.1.4 清空集合

语法格式如下：

```python
s.clear()
```

清空集合 s。

```
>>> thisset = set(("Google", "Amazon", "Taobao"))
>>> thisset.clear()
>>> print(thisset)
set()
```

#### 13.1.5 判断元素是否在集合中存在

语法格式如下：

```python
x in s
```

判断元素 x 是否在集合 s 中，存在返回 True，不存在返回 False。

```bash
>>> thisset = set(("Google", "Amazon", "Taobao"))
>>> "Amazon" in thisset
True
>>> "Meta" in thisset
False
>>>
```

### 13.2 集合内置方法完整列表

| operator                       | description                                               |
| ------------------------------ | --------------------------------------------------------- |
| add()                          | 为集合添加元素                                             |
| clear()                        | 移除集合中的所有元素                                        |
| copy()                         | 拷贝一个集合                                               |
| difference()                   | 返回多个集合的差集                                          |
| difference_update()            | 移除集合中的元素，该元素在指定的集合也存在                    |
| discard()                      | 删除集合中指定的元素                                        |
| intersection()                 | 返回集合的交集                                              |
| intersection_update()          | 返回集合的交集                                              |
| isdisjoint()                   | 判断两个集合是否包含相同的元素，如果没有返回 True，否则False   |
| issubset()                     | 判断指定集合是否为该方法参数集合的子集                        |
| issuperset()                   | 判断该方法的参数集合是否为指定集合的子集                      |
| pop()                          | 随机移除元素                                                |
| remove()                       | 移除指定元素                                                |
| symmetric_difference()         | 返回两个集合中不重复的元素集合                               |
| symmetric_difference_update()  | 移除当前集合中在另外一个指定集合相同的元素                    |
| union()                        | 返回两个集合的并集                                          |
| update()                       | 给集合添加元素                                              |
