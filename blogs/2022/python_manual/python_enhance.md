# Python 3 Enhance Tutorial (Chinese)

## 目录

[1.正则表达式](#1-正则表达式)

[2.CGI编程](#2-cgi编程)

[3.MySQL](#3-mysql)

[4.网络编程](#4-网络编程)

[5.SMTP发送邮件](#5-smtp发送邮件)

[6.多线程](#6-多线程)

[7.XML 解析](#7-xml解析)

[8.JSON](#8-json)

[9.日期和时间](#9-日期和时间)

[10.内置函数](#10-内置函数)

[11.MongoDB](#11-mongodb)

[12.urllib](#12-urllib)



## 1 正则表达式

正则表达式是一个特殊的字符序列，它能帮助你方便的检查一个字符串是否与某种模式匹配。

Python 自1.5版本起增加了re 模块，它提供 Perl 风格的正则表达式模式。

re 模块使 Python 语言拥有全部的正则表达式功能。

compile 函数根据一个模式字符串和可选的标志参数生成一个正则表达式对象。该对象拥有一系列方法用于正则表达式匹配和替换。

re 模块也提供了与这些方法功能完全一致的函数，这些函数使用一个模式字符串做为它们的第一个参数。


### 1.1 re.match函数

re.match 尝试从字符串的起始位置匹配一个模式，如果不是起始位置匹配成功的话，match()就返回none。

函数语法：

```python
re.match(pattern, string, flags=0)
```

函数参数说明：

| Parameter  | Description                       |
| ---------- | --------------------------------- |
| pattern    | 匹配的正则表达式                   |
| string     | 要匹配的字符串                     |
| flags      | 标志位，用于控制正则表达式的匹配方式 |

匹配成功re.match方法返回一个匹配的对象，否则返回None。

我们可以使用group(num) 或 groups() 匹配对象函数来获取匹配表达式。

| 匹配对象方法  | Description                                        |
| ------------ | -------------------------------------------------- |
| group(num=0) | 匹配的整个表达式的字符串，group() 可以一次输入多个组号 |
| groups()     | 返回一个包含所有小组字符串的元组，从 1 到 所含的小组号  |

```python
import re
print(re.match('www', 'www.google.com').span())  # 在起始位置匹配
print(re.match('com', 'www.google.com'))         # 不在起始位置匹配
```

以上实例运行输出结果为：

```bash
(0, 3)
None
```

```python
import re
 
line = "Cats are smarter than dogs"
# .* 表示任意匹配除换行符（\n、\r）之外的任何单个或多个字符
# (.*?) 表示"非贪婪"模式，只保存第一个匹配到的子串
matchObj = re.match( r'(.*) are (.*?) .*', line, re.M|re.I)
 
if matchObj:
   print ("matchObj.group() : ", matchObj.group())
   print ("matchObj.group(1) : ", matchObj.group(1))
   print ("matchObj.group(2) : ", matchObj.group(2))
else:
   print ("No match!!")
```

以上实例执行结果如下：

```bash
matchObj.group() :  Cats are smarter than dogs
matchObj.group(1) :  Cats
matchObj.group(2) :  smarter
```


### 1.2 re.search方法

re.search 扫描整个字符串并返回第一个成功的匹配。

函数语法：

```python
re.search(pattern, string, flags=0)
```

函数参数说明：

| Parameter  | Description                       |
| ---------- | --------------------------------- |
| pattern    | 匹配的正则表达式                   |
| string     | 要匹配的字符串                     |
| flags      | 标志位，用于控制正则表达式的匹配方式 |

匹配成功re.search方法返回一个匹配的对象，否则返回None。

我们可以使用group(num) 或 groups() 匹配对象函数来获取匹配表达式。

| 匹配对象方法  | Description                                        |
| ------------ | -------------------------------------------------- |
| group(num=0) | 匹配的整个表达式的字符串，group() 可以一次输入多个组号 |
| groups()     | 返回一个包含所有小组字符串的元组，从 1 到 所含的小组号  |

```python
import re
 
print(re.search('www', 'www.google.com').span())  # 在起始位置匹配
print(re.search('com', 'www.google.com').span())         # 不在起始位置匹配
```

以上实例运行输出结果为：

```bash
(0, 3)
(11, 14)
```

```python
import re
 
line = "Cats are smarter than dogs"
 
searchObj = re.search( r'(.*) are (.*?) .*', line, re.M|re.I)
 
if searchObj:
   print ("searchObj.group() : ", searchObj.group())
   print ("searchObj.group(1) : ", searchObj.group(1))
   print ("searchObj.group(2) : ", searchObj.group(2))
else:
   print ("Nothing found!!")
```

以上实例执行结果如下：

```
searchObj.group() :  Cats are smarter than dogs
searchObj.group(1) :  Cats
searchObj.group(2) :  smarter
```

### 1.3 re.match与re.search的区别

re.match 只匹配字符串的开始，如果字符串开始不符合正则表达式，则匹配失败，函数返回 None，而 re.search 匹配整个字符串，直到找到一个匹配。

```python
import re
 
line = "Cats are smarter than dogs"
 
matchObj = re.match( r'dogs', line, re.M|re.I)
if matchObj:
   print ("match --> matchObj.group() : ", matchObj.group())
else:
   print ("No match!!")
 
matchObj = re.search( r'dogs', line, re.M|re.I)
if matchObj:
   print ("search --> matchObj.group() : ", matchObj.group())
else:
   print ("No match!!")
```

以上实例运行结果如下：

```bash
No match!!
search --> matchObj.group() :  dogs
```


### 1.4 检索和替换

Python 的re模块提供了re.sub用于替换字符串中的匹配项。

语法：

```python
re.sub(pattern, repl, string, count=0, flags=0)
```

参数：

* pattern : 正则中的模式字符串。
* repl : 替换的字符串，也可为一个函数。
* string : 要被查找替换的原始字符串。
* count : 模式匹配后替换的最大次数，默认 0 表示替换所有的匹配。
* flags : 编译时用的匹配模式，数字形式。

前三个为必选参数，后两个为可选参数。

```python
import re
 
phone = "2004-959-559 # 这是一个电话号码"
 
# 删除注释
num = re.sub(r'#.*$', "", phone)
print ("电话号码 : ", num)
 
# 移除非数字的内容
num = re.sub(r'\D', "", phone)
print ("电话号码 : ", num)
```

以上实例执行结果如下：

```bash
电话号码 :  2004-959-559 
电话号码 :  2004959559
```

### 1.5 repl 参数是一个函数

以下实例中将字符串中的匹配的数字乘于 2：

```python
import re
 
# 将匹配的数字乘于 2
def double(matched):
    value = int(matched.group('value'))
    return str(value * 2)
 
s = 'A23G4HFD567'
print(re.sub('(?P<value>\d+)', double, s))
```

执行输出结果为：

```bash
A46G8HFD1134
```


### 1.6 compile 函数

compile 函数用于编译正则表达式，生成一个正则表达式（ Pattern ）对象，供 match() 和 search() 这两个函数使用。

语法格式为：

```python
re.compile(pattern[, flags])
```

参数：

* pattern : 一个字符串形式的正则表达式
* flags 可选，表示匹配模式，比如忽略大小写，多行模式等，具体参数为：
* re.I 忽略大小写
    * re.L 表示特殊字符集 \w, \W, \b, \B, \s, \S 依赖于当前环境
    * re.M 多行模式
    * re.S 即为' . '并且包括换行符在内的任意字符（' . '不包括换行符）
    * re.U 表示特殊字符集 \w, \W, \b, \B, \d, \D, \s, \S 依赖于 Unicode 字符属性数据库
    * re.X 为了增加可读性，忽略空格和' # '后面的注释

```python
>>>import re
>>> pattern = re.compile(r'\d+')                    # 用于匹配至少一个数字
>>> m = pattern.match('one12twothree34four')        # 查找头部，没有匹配
>>> print( m )
None
>>> m = pattern.match('one12twothree34four', 2, 10) # 从'e'的位置开始匹配，没有匹配
>>> print( m )
None
>>> m = pattern.match('one12twothree34four', 3, 10) # 从'1'的位置开始匹配，正好匹配
>>> print( m )                                        # 返回一个 Match 对象
<_sre.SRE_Match object at 0x10a42aac0>
>>> m.group(0)   # 可省略 0
'12'
>>> m.start(0)   # 可省略 0
3
>>> m.end(0)     # 可省略 0
5
>>> m.span(0)    # 可省略 0
(3, 5)
```

在上面，当匹配成功时返回一个 Match 对象，其中：

* group([group1, …]) 方法用于获得一个或多个分组匹配的字符串，当要获得整个匹配的子串时，可直接使用 group() 或 group(0)；
* start([group]) 方法用于获取分组匹配的子串在整个字符串中的起始位置（子串第一个字符的索引），参数默认值为 0；
* end([group]) 方法用于获取分组匹配的子串在整个字符串中的结束位置（子串最后一个字符的索引+1），参数默认值为 0；
* span([group]) 方法返回 (start(group), end(group))。

再看看一个例子：

```python
>>>import re
>>> pattern = re.compile(r'([a-z]+) ([a-z]+)', re.I)   # re.I 表示忽略大小写
>>> m = pattern.match('Hello World Wide Web')
>>> print( m )                            # 匹配成功，返回一个 Match 对象
<_sre.SRE_Match object at 0x10bea83e8>
>>> m.group(0)                            # 返回匹配成功的整个子串
'Hello World'
>>> m.span(0)                             # 返回匹配成功的整个子串的索引
(0, 11)
>>> m.group(1)                            # 返回第一个分组匹配成功的子串
'Hello'
>>> m.span(1)                             # 返回第一个分组匹配成功的子串的索引
(0, 5)
>>> m.group(2)                            # 返回第二个分组匹配成功的子串
'World'
>>> m.span(2)                             # 返回第二个分组匹配成功的子串索引
(6, 11)
>>> m.groups()                            # 等价于 (m.group(1), m.group(2), ...)
('Hello', 'World')
>>> m.group(3)                            # 不存在第三个分组
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: no such group
```

### 1.7 findall

在字符串中找到正则表达式所匹配的所有子串，并返回一个列表，如果有多个匹配模式，则返回元组列表，如果没有找到匹配的，则返回空列表。

`注意： match 和 search 是匹配一次 findall 匹配所有。`

语法格式为：

```python
re.findall(pattern, string, flags=0)
或
pattern.findall(string[, pos[, endpos]])
```

参数：

* pattern 匹配模式。
* string 待匹配的字符串。
* pos 可选参数，指定字符串的起始位置，默认为 0。
* endpos 可选参数，指定字符串的结束位置，默认为字符串的长度。

查找字符串中的所有数字：

```python
import re
 
result1 = re.findall(r'\d+','amazon 123 google 456')
 
pattern = re.compile(r'\d+')   # 查找数字
result2 = pattern.findall('amazon 123 google 456')
result3 = pattern.findall('run88oob123google456', 0, 10)
 
print(result1)
print(result2)
print(result3)
```

输出结果：

```bash
['123', '456']
['123', '456']
['88', '12']
```

多个匹配模式，返回元组列表：

```python
import re

result = re.findall(r'(\w+)=(\d+)', 'set width=20 and height=10')
print(result)
```

```python
[('width', '20'), ('height', '10')]
```


### 1.8 re.finditer

和 findall 类似，在字符串中找到正则表达式所匹配的所有子串，并把它们作为一个迭代器返回。

```python
re.finditer(pattern, string, flags=0)
```

参数：

| Parameter  | Description                       |
| ---------- | --------------------------------- |
| pattern    | 匹配的正则表达式                   |
| string     | 要匹配的字符串                     |
| flags      | 标志位，用于控制正则表达式的匹配方式 |

```python
import re
 
it = re.finditer(r"\d+","12a32bc43jf3") 
for match in it: 
    print (match.group() )
```

输出结果：

```bash
12 
32 
43 
3
```


### 1.9 re.split

split 方法按照能够匹配的子串将字符串分割后返回列表，它的使用形式如下：

```python
re.split(pattern, string[, maxsplit=0, flags=0])
```

参数：

| Parameter  | Description                       |
| ---------- | --------------------------------- |
| pattern    | 匹配的正则表达式                   |
| string     | 要匹配的字符串                     |
| flags      | 标志位，用于控制正则表达式的匹配方式 |


```bash
>>> import re
>>> re.split('\W+', 'google, google, google.')
['google', 'google', 'google', '']
>>> re.split('(\W+)', ' google, google, google.')
['', ' ', 'google', ', ', 'google', ', ', 'google', '.', '']
>>> re.split('\W+', ' google, google, google.', 1) 
['', 'google, google, google.']
 
>>> re.split('a*', 'hello world')   # 对于一个找不到匹配的字符串而言，split 不会对其作出分割
['hello world']
```


### 1.10 正则表达式对象

#### re.RegexObject

re.compile() 返回 RegexObject 对象。

#### re.MatchObject

group() 返回被 RE 匹配的字符串。

* start() 返回匹配开始的位置
* end() 返回匹配结束的位置
* span() 返回一个元组包含匹配 (开始,结束) 的位置


### 1.11 正则表达式修饰符 - 可选标志

正则表达式可以包含一些可选标志修饰符来控制匹配的模式。修饰符被指定为一个可选的标志。多个标志可以通过按位 OR(|) 它们来指定。如 re.I | re.M 被设置成 I 和 M 标志：

| 修饰符      | Description                                            |
| ---------- | ------------------------------------------------------ |
| re.I       | 使匹配对大小写不敏感                                     |
| re.L       | 做本地化识别（locale-aware）匹配                         |
| re.M       | 多行匹配，影响 ^ 和 $                                    |
| re.S       | 使 . 匹配包括换行在内的所有字符                           |
| re.U       | 根据Unicode字符集解析字符。这个标志影响 \w, \W, \b, \B    |
| re.X       | 该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解 |


### 1.12 正则表达式模式

模式字符串使用特殊的语法来表示一个正则表达式。

字母和数字表示他们自身。一个正则表达式模式中的字母和数字匹配同样的字符串。

多数字母和数字前加一个反斜杠时会拥有不同的含义。

标点符号只有被转义时才匹配自身，否则它们表示特殊的含义。

反斜杠本身需要使用反斜杠转义。

由于正则表达式通常都包含反斜杠，所以你最好使用原始字符串来表示它们。模式元素(如 r'\t'，等价于 \\t )匹配相应的特殊字符。

下表列出了正则表达式模式语法中的特殊元素。如果你使用模式的同时提供了可选的标志参数，某些模式元素的含义会改变。

| Parameter  | Description                                                  |
| ---------- | ------------------------------------------------------------ |
| ^          | 匹配字符串的开头                                               |
| $          | 匹配字符串的末尾                                               |
| .          | 匹配任意字符，除了换行符                                       |
| [...]      | 用来表示一组字符,单独列出：[amk] 匹配 'a'，'m'或'k'             |
| [^...]     | 不在[]中的字符：[^abc] 匹配除了a,b,c之外的字符                  |
| re*        | 匹配0个或多个的表达式                                          |
| re+        | 匹配1个或多个的表达式                                          |
| re?        | 匹配0个或1个由前面的正则表达式定义的片段，非贪婪方式              |
| re{ n}     | 匹配n个前面表达式                                              |
| re{ n,}    | 精确匹配n个前面表达式                                          |
| re{ n, m}  | 匹配 n 到 m 次由前面的正则表达式定义的片段，贪婪方式              |
| a| b       | 匹配a或b                                                      |
| (re)       | 匹配括号内的表达式，也表示一个组                                |
| (?imx)     | 正则表达式包含三种可选标志：i, m, 或x, 只影响括号中的区域         |
| (?-imx)    | 正则表达式关闭 i, m, 或 x 可选标志。只影响括号中的区域            |
| (?: re)    | 类似 (...), 但是不表示一个组                                    |
| (?imx: re) | 在括号中使用i, m, 或 x 可选标志                                 |
| (?-imx: re)| 在括号中不使用i, m, 或 x 可选标志                               |
| (?#...)    | 注释                                                          |
| (?= re)    | 前向肯定界定符                                                 |
| (?! re)    | 前向否定界定符                                                 |
| (?> re)    | 匹配的独立模式，省去回溯                                        |
| \w         | 匹配数字字母下划线                                              |
| \W         | 匹配非数字字母下划线                                            |
| \s         | 匹配任意空白字符，等价于 [\t\n\r\f]                             |
| \S         | 匹配任意非空字符                                                |
| \d         | 匹配任意数字，等价于 [0-9]                                      |
| \D         | 匹配任意非数字                                                  |
| \A         | 匹配字符串开始                                                  |
| \Z         | 匹配字符串结束，如果是存在换行，只匹配到换行前的结束字符串          |
| \z         | 匹配字符串结束                                                  |
| \G         | 匹配最后匹配完成的位置                                           |
| \b         | 匹配一个单词边界，也就是指单词和空格间的位置                       |
| \B         | 匹配非单词边界                                                  |
| \n, \t, 等 | 匹配一个换行符。匹配一个制表符, 等                                |
| \1...\9    | 匹配第n个分组的内容                                              |
| \10        | 匹配第n个分组的内容，如果它经匹配。否则指的是八进制字符码的表达式    |


### 1.13 正则表达式实例

字符匹配

| Example    | Description                       |
| ---------- | --------------------------------- |
| python     | 匹配 "python"                     |

字符类

| Example    | Description                       |
| ---------- | --------------------------------- |
| [Pp]ython  | 匹配 "Python" 或 "python"         |
| rub[ye]    | 匹配 "ruby" 或 "rube"             |
| [aeiou]    | 匹配中括号内的任意一个字母          |
| [0-9]      | 匹配任何数字。类似于 [0123456789]  |
| [a-z]      | 匹配任何小写字母                   |
| [A-Z]      | 匹配任何大写字母                   |
| [a-zA-Z0-9]| 匹配任何字母及数字                 |
| [^aeiou]   | 除了aeiou字母以外的所有字符        |
| [^0-9]     | 匹配除了数字外的字符               |


特殊字符类

| Example    | Description                                                      |
| ---------- | ---------------------------------------------------------------- |
| pattern    | 匹配的正则表达式                                                   |
| .          | 匹配除 "\n" 之外的任何单个字符                                      |
| \d         | 匹配一个数字字符。等价于 [0-9]                                      |
| \D         | 匹配一个非数字字符。等价于 [^0-9]                                   |
| \s         | 匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]  |
| \S         | 匹配任何非空白字符。等价于 [^ \f\n\r\t\v]                           |
| \w         | 匹配包括下划线的任何单词字符。等价于'[A-Za-z0-9_]'                   |
| \W         | 匹配任何非单词字符。等价于 '[^A-Za-z0-9_]'                          |



## 2 CGI编程

### 2.1 什么是CGI

CGI 目前由NCSA维护，NCSA定义CGI如下：

CGI(Common Gateway Interface),通用网关接口,它是一段程序,运行在服务器上如：HTTP服务器，提供同客户端HTML页面的接口。


### 2.2 网页浏览

为了更好的了解CGI是如何工作的，我们可以从在网页上点击一个链接或URL的流程：

* 1、使用你的浏览器访问URL并连接到HTTP web 服务器。
* 2、Web服务器接收到请求信息后会解析URL，并查找访问的文件在服务器上是否存在，如果存在返回文件的内容，否则返回错误信息。
* 3、浏览器从服务器上接收信息，并显示接收的文件或者错误信息。

CGI程序可以是Python脚本，PERL脚本，SHELL脚本，C或者C++程序等。


### 2.3 Web服务器支持及配置

在你进行CGI编程前，确保您的Web服务器支持CGI及已经配置了CGI的处理程序。

Apache 支持CGI 配置：

设置好CGI目录：

```bash
ScriptAlias /cgi-bin/ /var/www/cgi-bin/
```

所有的HTTP服务器执行CGI程序都保存在一个预先配置的目录。这个目录被称为CGI目录，并按照惯例，它被命名为/var/www/cgi-bin目录。

CGI文件的扩展名为.cgi，python也可以使用.py扩展名。

默认情况下，Linux服务器配置运行的cgi-bin目录中为/var/www。

如果你想指定其他运行CGI脚本的目录，可以修改httpd.conf配置文件，如下所示：

```bash
<Directory "/var/www/cgi-bin">
   AllowOverride None
   Options +ExecCGI
   Order allow,deny
   Allow from all
</Directory>
```

在 AddHandler 中添加 .py 后缀，这样我们就可以访问 .py 结尾的 python 脚本文件：

```bash
AddHandler cgi-script .cgi .pl .py
```


### 2.4 第一个CGI程序

我们使用Python创建第一个CGI程序，文件名为hello.py，文件位于/var/www/cgi-bin目录中，内容如下：

```python
print ("Content-type:text/html")
print ()                             # 空行，告诉服务器结束头部
print ('<html>')
print ('<head>')
print ('<meta charset="utf-8">')
print ('<title>Hello Word - 我的第一个 CGI 程序！</title>')
print ('</head>')
print ('<body>')
print ('<h2>Hello Word! 我是来自网上购物的第一CGI程序</h2>')
print ('</body>')
print ('</html>')
```

文件保存后修改 hello.py，修改文件权限为 755：

```bash
chmod 755 hello.py 
```

用浏览器打开这个程序就可以看到一个Hello World！页面。


这个的hello.py脚本是一个简单的Python脚本，脚本第一行的输出内容"Content-type:text/html"发送到浏览器并告知浏览器显示的内容类型为"text/html"。

用 print 输出一个空行用于告诉服务器结束头部信息。


### 2.5 HTTP头部

hello.py文件内容中的" Content-type:text/html"即为HTTP头部的一部分，它会发送给浏览器告诉浏览器文件的内容类型。

HTTP头部的格式如下：

```html
HTTP 字段名: 字段内容
```

例如：

```html
Content-type: text/html
```

以下表格介绍了CGI程序中HTTP头部经常使用的信息：

| head                  | description                                            |
| --------------------- | ------------------------------------------------------ |
| Content-type:         | 请求的与实体对应的MIME信息。例如: Content-type:text/html  |
| Expires: Date         | 响应过期的日期和时间                                     |
| Location: URL         | 用来重定向接收方到非请求URL的位置来完成请求或标识新的资源   |
| Last-modified: Date   | 请求资源的最后修改时间                                   |
| Content-length: N     | 请求的内容长度                                           |
| Set-Cookie: String    | 设置Http Cookie                                         |


### 2.6 CGI环境变量

所有的CGI程序都接收以下的环境变量，这些变量在CGI程序中发挥了重要的作用：

| operator        | description                                                                                   |
| --------------- | --------------------------------------------------------------------------------------------- |
| CONTENT_TYPE    | 这个环境变量的值指示所传递来的信息的MIME类型                                                      |
| CONTENT_LENGTH  | 如果服务器与CGI程序信息的传递方式是POST，这个环境变量即使从标准输入STDIN中可以读到的有效数据的字节数  |
| HTTP_COOKIE     | 客户机内的 COOKIE 内容                                                                         |
| HTTP_USER_AGENT | 提供包含了版本数或其他专有数据的客户浏览器信息                                                    |
| PATH_INFO       | 这个环境变量的值表示紧接在CGI程序名之后的其他路径信息。它常常作为CGI程序的参数出现                   |
| QUERY_STRING    | 如果服务器与CGI程序信息的传递方式是GET，这个环境变量的值即使所传递的信息                            |
| REMOTE_ADDR     | 这个环境变量的值是发送请求的客户机的IP地址                                                        |
| REMOTE_HOST     | 这个环境变量的值包含发送CGI请求的客户机的主机名。如果不支持你想查询，则无需定义此环境变量             |
| REQUEST_METHOD  | 提供脚本被调用的方法。对于使用 HTTP/1.0 协议的脚本，仅 GET 和 POST 有意义                          |
| SCRIPT_FILENAME | CGI脚本的完整路径                                                                               |
| SCRIPT_NAME     | CGI脚本的的名称                                                                                 |
| SERVER_NAME     | 这是你的 WEB 服务器的主机名、别名或IP地址                                                         |
| SERVER_SOFTWARE | 这个环境变量的值包含了调用CGI程序的HTTP服务器的名称和版本号。例如，上面的值为Apache/2.2.14(Unix)     |

以下是一个简单的CGI脚本输出CGI的环境变量：

```python
import os

print ("Content-type: text/html")
print ()
print ("<meta charset=\"utf-8\">")
print ("<b>环境变量</b><br>")
print ("<ul>")
for key in os.environ.keys():
    print ("<li><span style='color:green'>%30s </span> : %s </li>" % (key,os.environ[key]))
print ("</ul>")
```

将以上点保存为 test.py ,并修改文件权限为 755，通过浏览器打开，可以看到效果。


### 2.7 GET和POST方法

浏览器客户端通过两种方法向服务器传递信息，这两种方法就是 GET 方法和 POST 方法。

#### 2.7.1 使用GET方法传输数据

GET方法发送编码后的用户信息到服务端，数据信息包含在请求页面的URL上，以"?"号分割, 如下所示：

```html
http://www.test.com/cgi-bin/hello.py?key1=value1&key2=value2
```

有关 GET 请求的其他一些注释：

* GET 请求可被缓存
* GET 请求保留在浏览器历史记录中
* GET 请求可被收藏为书签
* GET 请求不应在处理敏感数据时使用
* GET 请求有长度限制
* GET 请求只应当用于取回数据

#### 2.7.2 简单的url实例：GET方法

以下是一个简单的URL，使用GET方法向hello_get.py程序发送两个参数：

```bash
/cgi-bin/test.py?name=网上购物&url=http://www.amazon.com
```

以下为 hello_get.py 文件的代码：

```python
# CGI处理模块
import cgi, cgitb

# 创建 FieldStorage 的实例化
form = cgi.FieldStorage()

# 获取数据
site_name = form.getvalue('name')
site_url  = form.getvalue('url')

print ("Content-type:text/html")
print ()
print ("<html>")
print ("<head>")
print ("<meta charset=\"utf-8\">")
print ("<title>网上购物 CGI 测试实例</title>")
print ("</head>")
print ("<body>")
print ("<h2>%s官网：%s</h2>" % (site_name, site_url))
print ("</body>")
print ("</html>")
```

文件保存后修改 hello_get.py，修改文件权限为 755：

```bash
chmod 755 hello_get.py 
```

浏览器请求如下内容：

```bash
/cgi-bin/test.py?name=网上购物&url=http://www.amazon.com
```

可以看到预览效果。

#### 2.7.3 简单的表单实例：GET方法

以下是一个通过HTML的表单使用GET方法向服务器发送两个数据，提交的服务器脚本同样是hello_get.py文件，hello_get.html 代码如下：

```html
<!DOCTYPE html>
<html>
   <head>
      <meta charset="utf-8">
      <title>网上购物(amazon.com)</title>
   </head>
   <body>
      <form action="/cgi-bin/hello_get.py" method="get">
         站点名称: <input type="text" name="name">  <br />

         站点 URL: <input type="text" name="url" />
         <input type="submit" value="提交" />
      </form>
   </body>
</html>
```

默认情况下 cgi-bin 目录只能存放脚本文件，我们将 hello_get.html 存储在 test 目录下，修改文件权限为 755：

chmod 755 hello_get.html

使用浏览器打开这个html，可以看到效果。


#### 2.7.4 使用POST方法传递数据

使用POST方法向服务器传递数据是更安全可靠的，像一些敏感信息如用户密码等需要使用POST传输数据。

以下同样是hello_get.py ，它也可以处理浏览器提交的POST表单数据:

```python
# CGI处理模块
import cgi, cgitb

# 创建 FieldStorage 的实例化
form = cgi.FieldStorage()

# 获取数据
site_name = form.getvalue('name')
site_url  = form.getvalue('url')

print ("Content-type:text/html")
print ()
print ("<html>")
print ("<head>")
print ("<meta charset=\"utf-8\">")
print ("<title>网上购物 CGI 测试实例</title>")
print ("</head>")
print ("<body>")
print ("<h2>%s官网：%s</h2>" % (site_name, site_url))
print ("</body>")
print ("</html>")
```

以下为表单通过POST方法（method="post"）向服务器脚本 hello_get.py 提交数据:

```html
<!DOCTYPE html>
   <html>
      <head>
         <meta charset="utf-8">
         <title>网上购物(amazon.com)</title>
      </head>
      <body>
         <form action="/cgi-bin/hello_get.py" method="post">
            站点名称: <input type="text" name="name">  <br />

            站点 URL: <input type="text" name="url" />
            <input type="submit" value="提交" />
         </form>
      </body>
   </html>
</form>
```

打开这个html可以看到效果。

#### 2.7.5 通过CGI程序传递checkbox数据

checkbox用于提交一个或者多个选项数据，HTML代码如下：

```html
<!DOCTYPE html>
<html>
   <head>
      <meta charset="utf-8">
      <title>网上购物(amazon.com)</title>
   </head>
   <body>
      <form action="/cgi-bin/checkbox.py" method="POST" target="_blank">
         <input type="checkbox" name="amazon" value="on" /> 网上购物
         <input type="checkbox" name="google" value="on" /> Google
         <input type="submit" value="选择站点" />
      </form>
   </body>
</html>
```

以下为 checkbox.py 文件的代码：

```python
# 引入 CGI 处理模块
import cgi, cgitb

# 创建 FieldStorage的实例
form = cgi.FieldStorage()

# 接收字段数据
if form.getvalue('google'):
   google_flag = "是"
else:
   google_flag = "否"

if form.getvalue('amazon'):
   amazon_flag = "是"
else:
   amazon_flag = "否"

print ("Content-type:text/html")
print ()
print ("<html>")
print ("<head>")
print ("<meta charset=\"utf-8\">")
print ("<title>网上购物 CGI 测试实例</title>")
print ("</head>")
print ("<body>")
print ("<h2> 网上购物是否选择了 : %s</h2>" % amazon_flag)
print ("<h2> Google 是否选择了 : %s</h2>" % google_flag)
print ("</body>")
print ("</html>")
```

修改 checkbox.py 权限：

```bash
chmod 755 checkbox.py
```

用浏览器打开这个html可以看到效果。

#### 2.7.6 通过CGI程序传递Radio数据

Radio 只向服务器传递一个数据，HTML代码如下：

```html
<!DOCTYPE html>
<html>
   <head>
      <meta charset="utf-8">
      <title>网上购物(amazon.com)</title>
   </head>
   <body>
      <form action="/cgi-bin/radiobutton.py" method="post" target="_blank">
         <input type="radio" name="site" value="amazon" /> 网上购物
         <input type="radio" name="site" value="google" /> Google
         <input type="submit" value="提交" />
      </form>
   </body>
</html>
```

radiobutton.py 脚本代码如下：

```python
# 引入 CGI 处理模块
import cgi, cgitb

# 创建 FieldStorage的实例
form = cgi.FieldStorage()

# 接收字段数据
if form.getvalue('site'):
   site = form.getvalue('site')
else:
   site = "提交数据为空"

print ("Content-type:text/html")
print ()
print ("<html>")
print ("<head>")
print ("<meta charset=\"utf-8\">")
print ("<title>网上购物 CGI 测试实例</title>")
print ("</head>")
print ("<body>")
print ("<h2> 选中的网站是 %s</h2>" % site)
print ("</body>")
print ("</html>")
```

修改 radiobutton.py 权限：

```bash
chmod 755 radiobutton.py
```

使用浏览器打开这个html，可以看到效果。

#### 2.7.7 通过CGI程序传递 Textarea 数据

Textarea 向服务器传递多行数据，HTML 代码如下：

```html
<!DOCTYPE html>
<html>
   <head>
      <meta charset="utf-8">
      <title>网上购物(amazon.com)</title>
   </head>
   <body>
      <form action="/cgi-bin/textarea.py" method="post" target="_blank">
         <textarea name="textcontent" cols="40" rows="4">
            在这里输入内容...
         </textarea>
         <input type="submit" value="提交" />
      </form>
   </body>
</html>
```

textarea.py 脚本代码如下：

```python
# 引入 CGI 处理模块
import cgi, cgitb

# 创建 FieldStorage的实例
form = cgi.FieldStorage()

# 接收字段数据
if form.getvalue('textcontent'):
   text_content = form.getvalue('textcontent')
else:
   text_content = "没有内容"

print ("Content-type:text/html")
print ()
print ("<html>")
print ("<head>")
print ("<meta charset=\"utf-8\">")
print ("<title>网上购物 CGI 测试实例</title>")
print ("</head>")
print ("<body>")
print ("<h2> 输入的内容是：%s</h2>" % text_content)
print ("</body>")
print ("</html>")
```

修改 textarea.py 权限：

```bash
chmod 755 textarea.py
```

使用浏览器打开html可以看到效果。

#### 2.7.8 通过CGI程序传递下拉数据

HTML 下拉框代码如下：

```html
<!DOCTYPE html>
<html>
   <head>
      <meta charset="utf-8">
      <title>网上购物(amazon.com)</title>
   </head>
   <body>
      <form action="/cgi-bin/dropdown.py" method="post" target="_blank">
         <select name="dropdown">
            <option value="amazon" selected>网上购物</option>
            <option value="google">Google</option>
         </select>
         <input type="submit" value="提交"/>
      </form>
   </body>
</html>
```

dropdown.py 脚本代码如下所示：

```python
# 引入 CGI 处理模块
import cgi, cgitb

# 创建 FieldStorage的实例
form = cgi.FieldStorage()

# 接收字段数据
if form.getvalue('dropdown'):
   dropdown_value = form.getvalue('dropdown')
else:
   dropdown_value = "没有内容"

print ("Content-type:text/html")
print ()
print ("<html>")
print ("<head>")
print ("<meta charset=\"utf-8\">")
print ("<title>网上购物 CGI 测试实例</title>")
print ("</head>")
print ("<body>")
print ("<h2> 选中的选项是：%s</h2>" % dropdown_value)
print ("</body>")
print ("</html>")
```

修改 dropdown.py 权限：

```bash
chmod 755 dropdown.py
```

使用浏览器打开html，可以看到效果。


### 2.8 CGI中使用Cookie

在 http 协议一个很大的缺点就是不对用户身份的进行判断，这样给编程人员带来很大的不便， 而 cookie 功能的出现弥补了这个不足。

cookie 就是在客户访问脚本的同时，通过客户的浏览器，在客户硬盘上写入纪录数据 ，当下次客户访问脚本时取回数据信息，从而达到身份判别的功能，cookie 常用在身份校验中。

#### 2.8.1 cookie的语法

http cookie的发送是通过http头部来实现的，他早于文件的传递，头部set-cookie的语法如下：

```html
Set-cookie:name=name;expires=date;path=path;domain=domain;secure 
```

* name=name: 需要设置cookie的值(name不能使用";"和","号),有多个name值时用 ";" 分隔，例如：name1=name1;name2=name2;name3=name3。
* expires=date: cookie的有效期限,格式： expires="Wdy,DD-Mon-YYYY HH:MM:SS"
* path=path: 设置cookie支持的路径,如果path是一个路径，则cookie对这个目录下的所有文件及子目录生效，例如： path="/cgi-bin/"，如果path是一个文件，则cookie指对这个文件生效，例如：path="/cgi-bin/cookie.cgi"。
* domain=domain: 对cookie生效的域名，例如：domain="www.google.com"
* secure: 如果给出此标志，表示cookie只能通过SSL协议的https服务器来传递。
* cookie的接收是通过设置环境变量HTTP_COOKIE来实现的，CGI程序可以通过检索该变量获取cookie信息。

#### 2.8.2 Cookie设置

Cookie的 设置非常简单，cookie 会在 http 头部单独发送。以下实例在 cookie 中设置了 name 和 expires：

```python
print ('Set-Cookie: name="网上购物";expires=Wed, 28 Aug 2016 18:30:00 GMT')
print ('Content-Type: text/html')

print ()
print ("""
<html>
  <head>
    <meta charset="utf-8">
    <title>网上购物(amazon.com)</title>
  </head>
    <body>
        <h1>Cookie set OK!</h1>
    </body>
</html>
""")
```

将以上代码保存到 cookie_set.py，并修改 cookie_set.py 权限：

```bash
chmod 755 cookie_set.py
```

以上实例使用了 Set-Cookie 头信息来设置 Cookie 信息，可选项中设置了 Cookie 的其他属性，如过期时间 Expires，域名 Domain，路径 Path。这些信息设置在 "Content-type:text/html" 之前。


### 2.9 检索Cookie信息

Cookie信息检索页非常简单，Cookie信息存储在CGI的环境变量HTTP_COOKIE中，存储格式如下：

key1=value1;key2=value2;key3=value3....
以下是一个简单的CGI检索cookie信息的程序：

```python
# 导入模块
import os
import http.cookies

print ("Content-type: text/html")
print ()

print ("""
<html>
<head>
<meta charset="utf-8">
<title>网上购物(amazon.com)</title>
</head>
<body>
<h1>读取cookie信息</h1>
""")

if 'HTTP_COOKIE' in os.environ:
    cookie_string=os.environ.get('HTTP_COOKIE')
    c= http.cookies.SimpleCookie()
   # c=Cookie.SimpleCookie()
    c.load(cookie_string)

    try:
        data=c['name'].value
        print ("cookie data: "+data+"<br>")
    except KeyError:
        print ("cookie 没有设置或者已过去<br>")
print ("""
</body>
</html>
""")
```

将以上代码保存到 cookie_get.py，并修改 cookie_get.py 权限：

```bash
chmod 755 cookie_get.py
```

使用浏览器打开这个py文件，可以看到效果。


#### 2.9.1 文件上传实例

HTML设置上传文件的表单需要设置 enctype 属性为 multipart/form-data，代码如下所示：

```html
<!DOCTYPE html>
<html>
   <head>
      <meta charset="utf-8">
      <title>网上购物(amazon.com)</title>
   </head>
   <body>
      <form enctype="multipart/form-data"
                     action="/cgi-bin/save_file.py" method="post">
         <p>选中文件: <input type="file" name="filename" /></p>
         <p><input type="submit" value="上传" /></p>
      </form>
   </body>
</html>
```

save_file.py 脚本文件代码如下：

```python
import cgi, os
import cgitb; cgitb.enable()

form = cgi.FieldStorage()

# 获取文件名
fileitem = form['filename']

# 检测文件是否上传
if fileitem.filename:
   # 设置文件路径
   fn = os.path.basename(fileitem.filename)
   open('/tmp/' + fn, 'wb').write(fileitem.file.read())

   message = '文件 "' + fn + '" 上传成功'
   
else:
   message = '文件没有上传'
   
print ("""\
Content-Type: text/html\n
<html>
<head>
<meta charset="utf-8">
<title>网上购物(amazon.com)</title>
</head>
<body>
   <p>%s</p>
</body>
</html>
""" % (message,))
```

将以上代码保存到 save_file.py，并修改 save_file.py 权限：

```bash
chmod 755 save_file.py
```

使用浏览器打开html，可以看到效果。

如果你使用的系统是Unix/Linux，你必须替换文件分隔符，在window下只需要使用open()语句即可：

```python
fn = os.path.basename(fileitem.filename.replace("\\", "/" ))
```


### 2.10 文件下载对话框

我们先在当前目录下创建 foo.txt 文件，用于程序的下载。

文件下载通过设置HTTP头信息来实现，功能代码如下：

```python
# HTTP 头部
print ("Content-Disposition: attachment; filename=\"foo.txt\"")
print ()
# 打开文件
fo = open("foo.txt", "rb")

str = fo.read();
print (str)

# 关闭文件
fo.close()
```



## 3 MySQL

### 3.1 mysql-connector 驱动

MySQL 是最流行的关系型数据库管理系统，如果你不熟悉 MySQL，可以阅读我们的 MySQL 教程。

本章节我们为大家介绍使用 mysql-connector 来连接使用 MySQL， mysql-connector 是 MySQL 官方提供的驱动器。

我们可以使用 pip 命令来安装 mysql-connector：

```bash
python -m pip install mysql-connector
```

使用以下代码测试 mysql-connector 是否安装成功：

```python
# demo_mysql_test.py
import mysql.connector
```

执行以上代码，如果没有产生错误，表明安装成功。

`注意：如果你的 MySQL 是 8.0 版本，密码插件验证方式发生了变化，早期版本为 mysql_native_password，8.0 版本为 caching_sha2_password，所以需要做些改变：`

先修改 my.ini 配置：

```bash
[mysqld]
default_authentication_plugin=mysql_native_password
```

然后在 mysql 下执行以下命令来修改密码：

```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
```


### 3.2 创建数据库连接

可以使用以下代码来连接数据库：

```python
# demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",       # 数据库主机地址
  user="yourusername",    # 数据库用户名
  passwd="yourpassword"   # 数据库密码
)
 
print(mydb)
```

#### 3.2.1 创建数据库

创建数据库使用 "CREATE DATABASE" 语句，以下创建一个名为 amazon_db 的数据库：

```python
# demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456"
)
 
mycursor = mydb.cursor()
 
mycursor.execute("CREATE DATABASE amazon_db")
```

创建数据库前我们也可以使用 "SHOW DATABASES" 语句来查看数据库是否存在：

```python
# demo_mysql_test.py
# 输出所有数据库列表

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456"
)
 
mycursor = mydb.cursor()
 
mycursor.execute("SHOW DATABASES")
 
for x in mycursor:
  print(x)
```

或者我们可以直接连接数据库，如果数据库不存在，会输出错误信息：

```python
# demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
```


### 3.3 创建数据表

创建数据表使用 "CREATE TABLE" 语句，创建数据表前，需要确保数据库已存在，以下创建一个名为 sites 的数据表：

```python
# demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
mycursor.execute("CREATE TABLE sites (name VARCHAR(255), url VARCHAR(255))")
```

执行成功后，我们可以看到数据库创建的数据表 sites，字段为 name 和 url。

我们也可以使用 "SHOW TABLES" 语句来查看数据表是否已存在：

```python
# demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
mycursor.execute("SHOW TABLES")
 
for x in mycursor:
  print(x)
```

#### 3.3.1 主键设置

创建表的时候我们一般都会设置一个主键（PRIMARY KEY），我们可以使用 "INT AUTO_INCREMENT PRIMARY KEY" 语句来创建一个主键，主键起始值为 1，逐步递增。

如果我们的表已经创建，我们需要使用 ALTER TABLE 来给表添加主键：

```python
# demo_mysql_test.py
# 给 sites 表添加主键。

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
mycursor.execute("ALTER TABLE sites ADD COLUMN id INT AUTO_INCREMENT PRIMARY KEY")
```

如果你还未创建 sites 表，可以直接使用以下代码创建。

```python
# demo_mysql_test.py
# 给表创建主键

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
mycursor.execute("CREATE TABLE sites (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(255), url VARCHAR(255))")
```

### 3.4 插入数据

插入数据使用 "INSERT INTO" 语句：

```python
# demo_mysql_test.py
# 向 sites 表插入一条记录

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "INSERT INTO sites (name, url) VALUES (%s, %s)"
val = ("AMAZON", "https://www.amazon.com")
mycursor.execute(sql, val)
 
mydb.commit()    # 数据表内容有更新，必须使用到该语句
 
print(mycursor.rowcount, "记录插入成功。")
```

执行代码，输出结果为：

```bash
1 记录插入成功
```

#### 3.4.1 批量插入

批量插入使用 executemany() 方法，该方法的第二个参数是一个元组列表，包含了我们要插入的数据：

```python
# demo_mysql_test.py
# 向 sites 表插入多条记录

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "INSERT INTO sites (name, url) VALUES (%s, %s)"
val = [
  ('Google', 'https://www.google.com'),
  ('Github', 'https://www.github.com'),
  ('Taobao', 'https://www.taobao.com'),
  ('stackoverflow', 'https://www.stackoverflow.com/')
]
 
mycursor.executemany(sql, val)
 
mydb.commit()    # 数据表内容有更新，必须使用到该语句
 
print(mycursor.rowcount, "记录插入成功。")
```

执行代码，输出结果为：

```bash
4 记录插入成功。
```

执行以上代码后，我们可以看看数据表的记录：

| No. | Website         | Url                            |
| --- | --------------- | ------------------------------ |
| 1   | AMAZON          | https://www.amazon.com         |
| 2   | Google          | https://www.google.com         |
| 3   | Github          | https://www.github.com         |
| 4   | Taobao          | https://www.taobao.com         |
| 5   | stackvoerflow   | https://www.stackoverflow.com  |


如果我们想在数据记录插入后，获取该记录的 ID ，可以使用以下代码：

```python
# demo_mysql_test.py
import mysql.connector

mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "INSERT INTO sites (name, url) VALUES (%s, %s)"
val = ("Zhihu", "https://www.zhihu.com")
mycursor.execute(sql, val)
 
mydb.commit()
 
print("1 条记录已插入, ID:", mycursor.lastrowid)
```

执行代码，输出结果为：

```bash
1 条记录已插入, ID: 6
```


### 3.5 查询数据

查询数据使用 SELECT 语句：

```python
# demo_mysql_test.py
import mysql.connector

mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
mycursor.execute("SELECT * FROM sites")
 
myresult = mycursor.fetchall()     # fetchall() 获取所有记录
 
for x in myresult:
  print(x)
```

执行代码，输出结果为：

```bash
(1, 'AMAZON', 'https://www.amazon.com')
(2, 'Google', 'https://www.google.com')
(3, 'Github', 'https://www.github.com')
(4, 'Taobao', 'https://www.taobao.com')
(5, 'stackoverflow', 'https://www.stackoverflow.com/')
(6, 'Zhihu', 'https://www.zhihu.com')
```

也可以读取指定的字段数据：

```python
# demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
mycursor.execute("SELECT name, url FROM sites")
 
myresult = mycursor.fetchall()
 
for x in myresult:
  print(x)
```

执行代码，输出结果为：

```bash
('AMAZON', 'https://www.amazon.com')
('Google', 'https://www.google.com')
('Github', 'https://www.github.com')
('Taobao', 'https://www.taobao.com')
('stackoverflow', 'https://www.stackoverflow.com/')
('Zhihu', 'https://www.zhihu.com')
```

如果我们只想读取一条数据，可以使用 fetchone() 方法：

```python
# demo_mysql_test.py
import mysql.connector

mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
mycursor.execute("SELECT * FROM sites")
 
myresult = mycursor.fetchone()
 
print(myresult)
```

执行代码，输出结果为：

```bash
(1, 'AMAZON', 'https://www.amazon.com')
```

#### 3.5.1 where 条件语句

如果我们要读取指定条件的数据，可以使用 where 语句：

```python
# demo_mysql_test.py
# 读取 name 字段为 AMAZON 的记录

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "SELECT * FROM sites WHERE name ='AMAZON'"
 
mycursor.execute(sql)
 
myresult = mycursor.fetchall()
 
for x in myresult:
  print(x)
```

执行代码，输出结果为：

```bash
(1, 'AMAZON', 'https://www.amazon.com')
```

也可以使用通配符 %：

```python
# demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "SELECT * FROM sites WHERE url LIKE '%oo%'"
 
mycursor.execute(sql)
 
myresult = mycursor.fetchall()
 
for x in myresult:
  print(x)
```

执行代码，输出结果为：

```bash
(1, 'AMAZON', 'https://www.amazon.com')
(2, 'Google', 'https://www.google.com')
```

为了防止数据库查询发生 SQL 注入的攻击，我们可以使用 %s 占位符来转义查询的条件：

```python
# demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "SELECT * FROM sites WHERE name = %s"
na = ("AMAZON", )
 
mycursor.execute(sql, na)
 
myresult = mycursor.fetchall()
 
for x in myresult:
  print(x)
```

#### 3.5.2 排序

查询结果排序可以使用 ORDER BY 语句，默认的排序方式为升序，关键字为 ASC，如果要设置降序排序，可以设置关键字 DESC。

```python
# demo_mysql_test.py
# 按 name 字段字母的升序排序

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "SELECT * FROM sites ORDER BY name"
 
mycursor.execute(sql)
 
myresult = mycursor.fetchall()
 
for x in myresult:
  print(x)
```

执行代码，输出结果为：

```bash
(3, 'Github', 'https://www.github.com')
(2, 'Google', 'https://www.google.com')
(1, 'AMAZON', 'https://www.amazon.com')
(5, 'stackoverflow', 'https://www.stackoverflow.com/')
(4, 'Taobao', 'https://www.taobao.com')
(6, 'Zhihu', 'https://www.zhihu.com')
```

降序排序实例：

```python
# demo_mysql_test.py
# 按 name 字段字母的降序排序

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "SELECT * FROM sites ORDER BY name DESC"
 
mycursor.execute(sql)
 
myresult = mycursor.fetchall()
 
for x in myresult:
  print(x)
```

执行代码，输出结果为：

```bash
(6, 'Zhihu', 'https://www.zhihu.com')
(4, 'Taobao', 'https://www.taobao.com')
(5, 'stackoverflow', 'https://www.stackoverflow.com/')
(1, 'AMAZON', 'https://www.amazon.com')
(2, 'Google', 'https://www.google.com')
(3, 'Github', 'https://www.github.com')
```

#### 3.5.3 Limit

如果我们要设置查询的数据量，可以通过 "LIMIT" 语句来指定

```python
# demo_mysql_test.py
# 读取前 3 条记录

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
mycursor.execute("SELECT * FROM sites LIMIT 3")
 
myresult = mycursor.fetchall()
 
for x in myresult:
  print(x)
```

执行代码，输出结果为：

```bash
(1, 'AMAZON', 'https://www.amazon.com')
(2, 'Google', 'https://www.google.com')
(3, 'Github', 'https://www.github.com')
```

也可以指定起始位置，使用的关键字是 OFFSET：

```python
# demo_mysql_test.py
# 从第二条开始读取前 3 条记录

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
mycursor.execute("SELECT * FROM sites LIMIT 3 OFFSET 1")  # 0 为 第一条，1 为第二条，以此类推
 
myresult = mycursor.fetchall()
 
for x in myresult:
  print(x)
```

执行代码，输出结果为：

```bash
(2, 'Google', 'https://www.google.com')
(3, 'Github', 'https://www.github.com')
(4, 'Taobao', 'https://www.taobao.com')
```


### 3.6 删除记录

删除记录使用 "DELETE FROM" 语句：

```python
# demo_mysql_test.py
# 删除 name 为 stackoverflow 的记录

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "DELETE FROM sites WHERE name = 'stackoverflow'"
 
mycursor.execute(sql)
 
mydb.commit()
 
print(mycursor.rowcount, " 条记录删除")
```

执行代码，输出结果为：

```bash
1  条记录删除
```

`注意：要慎重使用删除语句，删除语句要确保指定了 WHERE 条件语句，否则会导致整表数据被删除。`

为了防止数据库查询发生 SQL 注入的攻击，我们可以使用 %s 占位符来转义删除语句的条件：

```python
# demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "DELETE FROM sites WHERE name = %s"
na = ("stackoverflow", )
 
mycursor.execute(sql, na)
 
mydb.commit()
 
print(mycursor.rowcount, " 条记录删除")
```

执行代码，输出结果为：

```bash
1  条记录删除
```


### 3.7 更新表数据

数据表更新使用 "UPDATE" 语句：

```python
# demo_mysql_test.py
# 将 name 为 Zhihu 的字段数据改为 ZH

import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "UPDATE sites SET name = 'ZH' WHERE name = 'Zhihu'"
 
mycursor.execute(sql)
 
mydb.commit()
 
print(mycursor.rowcount, " 条记录被修改")
```

执行代码，输出结果为：

```bash
1  条记录被修改
```

`注意：UPDATE 语句要确保指定了 WHERE 条件语句，否则会导致整表数据被更新。`

为了防止数据库查询发生 SQL 注入的攻击，我们可以使用 %s 占位符来转义更新语句的条件：

```python
demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "UPDATE sites SET name = %s WHERE name = %s"
val = ("Zhihu", "ZH")
 
mycursor.execute(sql, val)
 
mydb.commit()
 
print(mycursor.rowcount, " 条记录被修改")
```

执行代码，输出结果为：

```bash
1  条记录被修改
```


### 3.8 删除表

删除表使用 "DROP TABLE" 语句， IF EXISTS 关键字是用于判断表是否存在，只有在存在的情况才删除：

```python
# demo_mysql_test.py
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456",
  database="amazon_db"
)
mycursor = mydb.cursor()
 
sql = "DROP TABLE IF EXISTS sites"  # 删除数据表 sites
 
mycursor.execute(sql)
```


### 3.9 PyMySQL 安装

在使用 PyMySQL 之前，我们需要确保 PyMySQL 已安装。

PyMySQL 下载地址：https://github.com/PyMySQL/PyMySQL。

如果还未安装，我们可以使用以下命令安装最新版的 PyMySQL：

```bash
$ pip3 install PyMySQL
```

如果你的系统不支持 pip 命令，可以使用以下方式安装：

1、使用 git 命令下载安装包安装(你也可以手动下载)：

```bash
$ git clone https://github.com/PyMySQL/PyMySQL
$ cd PyMySQL/
$ python3 setup.py install
```

2、如果需要制定版本号，可以使用 curl 命令来安装：

```bash
$ # X.X 为 PyMySQL 的版本号
$ curl -L https://github.com/PyMySQL/PyMySQL/tarball/pymysql-X.X | tar xz
$ cd PyMySQL*
$ python3 setup.py install
$ # 现在你可以删除 PyMySQL* 目录
```

`注意：请确保您有root权限来安装上述模块。`

安装的过程中可能会出现"ImportError: No module named setuptools"的错误提示，意思是你没有安装setuptools，你可以访问https://pypi.python.org/pypi/setuptools 找到各个系统的安装方法。

Linux 系统安装实例：

```bash
$ wget https://bootstrap.pypa.io/ez_setup.py
$ python3 ez_setup.py
```


### 3.10 数据库连接

连接数据库前，请先确认以下事项：

* 您已经创建了数据库 TESTDB.
* 在TESTDB数据库中您已经创建了表 EMPLOYEE
* EMPLOYEE表字段为 FIRST_NAME, LAST_NAME, AGE, SEX 和 INCOME。
* 连接数据库TESTDB使用的用户名为 "testuser" ，密码为 "test123",你可以可以自己设定或者直接使用root用户名及其密码，Mysql数据库用户授权请使用Grant命令。
* 在你的机子上已经安装了 Python pymysql 模块。
* 如果您对sql语句不熟悉，可以访问我们的 SQL基础教程

实例：
以下实例链接 Mysql 的 TESTDB 数据库：

```python 
import pymysql
 
# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='testuser',
                     password='test123',
                     database='TESTDB')
 
# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()
 
# 使用 execute()  方法执行 SQL 查询 
cursor.execute("SELECT VERSION()")
 
# 使用 fetchone() 方法获取单条数据.
data = cursor.fetchone()
 
print ("Database version : %s " % data)
 
# 关闭数据库连接
db.close()
```

执行以上脚本输出结果如下：

```bash
Database version : 5.5.20-log
```


### 3.11 创建数据库表

如果数据库连接存在我们可以使用execute()方法来为数据库创建表，如下所示创建表EMPLOYEE：

```python 
import pymysql
 
# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='testuser',
                     password='test123',
                     database='TESTDB')
 
# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()
 
# 使用 execute() 方法执行 SQL，如果表存在则删除
cursor.execute("DROP TABLE IF EXISTS EMPLOYEE")
 
# 使用预处理语句创建表
sql = """CREATE TABLE EMPLOYEE (
         FIRST_NAME  CHAR(20) NOT NULL,
         LAST_NAME  CHAR(20),
         AGE INT,  
         SEX CHAR(1),
         INCOME FLOAT )"""
 
cursor.execute(sql)
 
# 关闭数据库连接
db.close()
```


### 3.12 数据库插入操作

以下实例使用执行 SQL INSERT 语句向表 EMPLOYEE 插入记录：

```python 
import pymysql
 
# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='testuser',
                     password='test123',
                     database='TESTDB')
 
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
 
# SQL 插入语句
sql = """INSERT INTO EMPLOYEE(FIRST_NAME,
         LAST_NAME, AGE, SEX, INCOME)
         VALUES ('Mac', 'Mohan', 20, 'M', 2000)"""
try:
   # 执行sql语句
   cursor.execute(sql)
   # 提交到数据库执行
   db.commit()
except:
   # 如果发生错误则回滚
   db.rollback()
 
# 关闭数据库连接
db.close()
```

以上例子也可以写成如下形式：

```python 
import pymysql
 
# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='testuser',
                     password='test123',
                     database='TESTDB')
 
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
 
# SQL 插入语句
sql = "INSERT INTO EMPLOYEE(FIRST_NAME, \
       LAST_NAME, AGE, SEX, INCOME) \
       VALUES ('%s', '%s',  %s,  '%s',  %s)" % \
       ('Mac', 'Mohan', 20, 'M', 2000)
try:
   # 执行sql语句
   cursor.execute(sql)
   # 执行sql语句
   db.commit()
except:
   # 发生错误时回滚
   db.rollback()
 
# 关闭数据库连接
db.close()
```

以下代码使用变量向SQL语句中传递参数:

```bash
..................................
user_id = "test123"
password = "password"

con.execute('insert into Login values( %s,  %s)' % \
             (user_id, password))
..................................
```


### 3.13 数据库查询操作

Python查询Mysql使用 fetchone() 方法获取单条数据, 使用fetchall() 方法获取多条数据。

* fetchone(): 该方法获取下一个查询结果集。结果集是一个对象
* fetchall(): 接收全部的返回结果行.
* rowcount: 这是一个只读属性，并返回执行execute()方法后影响的行数。

实例：
查询EMPLOYEE表中salary（工资）字段大于1000的所有数据：

```python 
import pymysql
 
# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='testuser',
                     password='test123',
                     database='TESTDB')
 
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
 
# SQL 查询语句
sql = "SELECT * FROM EMPLOYEE \
       WHERE INCOME > %s" % (1000)
try:
   # 执行SQL语句
   cursor.execute(sql)
   # 获取所有记录列表
   results = cursor.fetchall()
   for row in results:
      fname = row[0]
      lname = row[1]
      age = row[2]
      sex = row[3]
      income = row[4]
       # 打印结果
      print ("fname=%s,lname=%s,age=%s,sex=%s,income=%s" % \
             (fname, lname, age, sex, income ))
except:
   print ("Error: unable to fetch data")
 
# 关闭数据库连接
db.close()
```

以上脚本执行结果如下：

```bash
fname=Mac, lname=Mohan, age=20, sex=M, income=2000
```


### 3.14 数据库更新操作

更新操作用于更新数据表的数据，以下实例将 TESTDB 表中 SEX 为 'M' 的 AGE 字段递增 1：

```python 
import pymysql
 
# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='testuser',
                     password='test123',
                     database='TESTDB')
 
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
 
# SQL 更新语句
sql = "UPDATE EMPLOYEE SET AGE = AGE + 1 WHERE SEX = '%c'" % ('M')
try:
   # 执行SQL语句
   cursor.execute(sql)
   # 提交到数据库执行
   db.commit()
except:
   # 发生错误时回滚
   db.rollback()
 
# 关闭数据库连接
db.close()
```

### 3.15 删除操作

删除操作用于删除数据表中的数据，以下实例演示了删除数据表 EMPLOYEE 中 AGE 大于 20 的所有数据：

```python 
import pymysql
 
# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='testuser',
                     password='test123',
                     database='TESTDB')
 
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
 
# SQL 删除语句
sql = "DELETE FROM EMPLOYEE WHERE AGE > %s" % (20)
try:
   # 执行SQL语句
   cursor.execute(sql)
   # 提交修改
   db.commit()
except:
   # 发生错误时回滚
   db.rollback()
 
# 关闭连接
db.close()
```


### 3.16 执行事务

事务机制可以确保数据一致性。

事务应该具有4个属性：原子性、一致性、隔离性、持久性。这四个属性通常称为ACID特性。

* 原子性（atomicity）。一个事务是一个不可分割的工作单位，事务中包括的诸操作要么都做，要么都不做。
* 一致性（consistency）。事务必须是使数据库从一个一致性状态变到另一个一致性状态。一致性与原子性是密切相关的。
* 隔离性（isolation）。一个事务的执行不能被其他事务干扰。即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。
* 持久性（durability）。持续性也称永久性（permanence），指一个事务一旦提交，它对数据库中数据的改变就应该是永久性的。接下来的其他操作或故障不应该对其有任何影响。

Python DB API 2.0 的事务提供了两个方法 commit 或 rollback。

```python
# SQL删除记录语句
sql = "DELETE FROM EMPLOYEE WHERE AGE > %s" % (20)
try:
   # 执行SQL语句
   cursor.execute(sql)
   # 向数据库提交
   db.commit()
except:
   # 发生错误时回滚
   db.rollback()
```

对于支持事务的数据库， 在Python数据库编程中，当游标建立之时，就自动开始了一个隐形的数据库事务。

commit()方法游标的所有更新操作，rollback（）方法回滚当前游标的所有操作。每一个方法都开始了一个新的事务。


### 3.17 错误处理

DB API中定义了一些数据库操作的错误及异常，下表列出了这些错误和异常:

| operator           | description                                                                 |
| ------------------ | --------------------------------------------------------------------------- |
| Warning            | 当有严重警告时触发，例如插入数据是被截断等等。必须是 StandardError 的子类        |
| Error              | 警告以外所有其他错误类。必须是 StandardError 的子类                            |
| InterfaceError     | 当有数据库接口模块本身的错误（而不是数据库的错误）发生时触发。 必须是Error的子类   |
| DatabaseError      | 和数据库有关的错误发生时触发。 必须是Error的子类                                |
| DataError          | 当有数据处理时的错误发生时触发，例如：除零错误，数据超范围等等                    |
| OperationalError   | 指非用户控制的，而是操作数据库时发生的错误                                      |
| IntegrityError     | 完整性相关的错误，例如外键检查失败等。必须是DatabaseError子类                    |
| InternalError      | 数据库的内部错误，例如游标（cursor）失效了、事务同步失败等等                     |
| ProgrammingError   | 程序错误，例如数据表（table）没找到或已存在、SQL语句语法错误、 参数数量错误等等    |
| NotSupportedError  | 不支持错误，指使用了数据库不支持的函数或API等                                   |



## 4 网络编程

Python 提供了两个级别访问的网络服务。：

* 低级别的网络服务支持基本的 Socket，它提供了标准的 BSD Sockets API，可以访问底层操作系统Socket接口的全部方法。
* 高级别的网络服务模块 SocketServer， 它提供了服务器中心类，可以简化网络服务器的开发。


### 4.1 什么是 Socket?

Socket又称"套接字"，应用程序通常通过"套接字"向网络发出请求或者应答网络请求，使主机间或者一台计算机上的进程间可以通讯。

### 4.2 socket()函数

Python 中，我们用 socket() 函数来创建套接字，语法格式如下：

```python
socket.socket([family[, type[, proto]]])
```

### 4.2.1 参数

* family: 套接字家族可以是 AF_UNIX 或者 AF_INET
* type: 套接字类型可以根据是面向连接的还是非连接分为SOCK_STREAM或SOCK_DGRAM
* protocol: 一般不填默认为0.

### 4.2.2 Socket 对象(内建)方法

| function                             | description                                                                 |
| ------------------------------------ | --------------------------------------------------------------------------- |
| 服务器端套接字                                                                                                      |
| s.bind()                             | 绑定地址(host,port)到套接字,在AF_INET下,以元组(host,port)的形式表示地址         |
| s.listen()                           | 开始TCP监听                                                                  |
| s.accept()                           | 被动接受TCP客户端连接,(阻塞式)等待连接的到来                                    |
| 客户端套接字                                                                                                        |
| s.connect()                          | 主动初始化TCP服务器连接                                                       |
| s.connect_ex()                       | connect()函数的扩展版本,出错时返回出错码,而不是抛出异常                         |
| 公共用途的套接字函数                                                                                                 |
| s.recv()                             | 接收TCP数据，数据以字符串形式返回，bufsize指定要接收的最大数据量                 |
| s.send()                             | 发送TCP数据，将string中的数据发送到连接的套接字                                 |
| s.sendall()                          | 完整发送TCP数据，完整发送TCP数据                                               |
| s.recvfrom()                         | 接收UDP数据，与recv()类似，但返回值是(data,address)                            |   
| s.sendto()                           | 发送UDP数据，将数据发送到套接字,address是形式为(ipaddr，port)的元组,指定远程地址 |
| s.close()                            | 关闭套接字                                                                   |
| s.getpeername()                      | 返回连接套接字的远程地址。返回值通常是元组（ipaddr,port）                       |
| s.getsockname()                      | 返回套接字自己的地址。通常是一个元组(ipaddr,port)                              |
| s.setsockopt(level,optname,value)    | 设置给定套接字选项的值                                                        |
| s.getsockopt(level,optname[.buflen]) | 返回套接字选项的值                                                            |
| s.settimeout(timeout)                | 设置套接字操作的超时期，timeout是一个浮点数，单位是秒。值为None表示没有超时期     |
| s.gettimeout()                       | 返回当前超时期的值，单位是秒，如果没有设置超时期，则返回None                     |
| s.fileno()                           | 返回套接字的文件描述符                                                        |
| s.setblocking(flag)                  | 如果flag为False，则将套接字设为非阻塞模式，否则将套接字设为阻塞模式（默认值）     |
| s.makefile()                         | 创建一个与该套接字相关连的文件                                                 |


### 4.3 简单实例

#### 4.3.1 服务端

我们使用 socket 模块的 socket 函数来创建一个 socket 对象。socket 对象可以通过调用其他函数来设置一个 socket 服务。

现在我们可以通过调用 bind(hostname, port) 函数来指定服务的 port(端口)。

接着，我们调用 socket 对象的 accept 方法。该方法等待客户端的连接，并返回 connection 对象，表示已连接到客户端。

完整代码如下：

```python
# 文件名：server.py

# 导入 socket、sys 模块
import socket
import sys

# 创建 socket 对象
serversocket = socket.socket(
            socket.AF_INET, socket.SOCK_STREAM)

# 获取本地主机名
host = socket.gethostname()

port = 9999

# 绑定端口号
serversocket.bind((host, port))

# 设置最大连接数，超过后排队
serversocket.listen(5)

while True:
    # 建立客户端连接
    clientsocket,addr = serversocket.accept()      

    print("连接地址: %s" % str(addr))
   
    msg='欢迎访问Python教程！'+ "\r\n"
    clientsocket.send(msg.encode('utf-8'))
    clientsocket.close()
```

#### 4.3.2 客户端

接下来我们写一个简单的客户端实例连接到以上创建的服务。端口号为 9999。

socket.connect(hostname, port ) 方法打开一个 TCP 连接到主机为 hostname 端口为 port 的服务商。连接后我们就可以从服务端获取数据，记住，操作完成后需要关闭连接。

```python
# 文件名：client.py

# 导入 socket、sys 模块
import socket
import sys

# 创建 socket 对象
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 获取本地主机名
host = socket.gethostname()

# 设置端口号
port = 9999

# 连接服务，指定主机和端口
s.connect((host, port))

# 接收小于 1024 字节的数据
msg = s.recv(1024)

s.close()

print (msg.decode('utf-8'))
```

现在我们打开两个终端，第一个终端执行 server.py 文件：

```bash
$ python3 server.py
```

第二个终端执行 client.py 文件：

```bash
$ python3 client.py 
欢迎访问Python教程！
```

这时我们再打开第一个终端，就会看到有以下信息输出：

```bash
连接地址： ('192.168.0.118', 33397)
```

### 4.4 Python Internet 模块

以下列出了 Python 网络编程的一些重要模块：

| 协议   | 功能用处           | 端口号  | Python 模块                 |
| ---- - | ----------------- | ------ | --------------------------- |
| HTTP   | 网页访问           | 80     | httplib, urllib, xmlrpclib  |
| NNTP   | 阅读和张贴新闻文章  | 119    | nntplib                     |
| FTP    | 文件传输           | 20     | ftplib, urllib              |
| SMTP   | 发送邮件           | 25     | smtplib                     |
| POP3   | 接收邮件           | 110    | poplib                      |
| IMAP4  | 获取邮件           | 143    | imaplib                     |
| Telnet | 命令行             | 23     | telnetlib                   |
| Gopher | 信息查找           | 70     | gopherlib, urllib           |



## 5 SMTP发送邮件

SMTP（Simple Mail Transfer Protocol）即简单邮件传输协议,它是一组用于由源地址到目的地址传送邮件的规则，由它来控制信件的中转方式。

python的smtplib提供了一种很方便的途径发送电子邮件。它对smtp协议进行了简单的封装。

Python创建 SMTP 对象语法如下：

```python
import smtplib

smtpObj = smtplib.SMTP( [host [, port [, local_hostname]]] )
```

参数说明：

* host: SMTP 服务器主机。 你可以指定主机的ip地址或者域名如:amazon.com，这个是可选参数。
* port: 如果你提供了 host 参数, 你需要指定 SMTP 服务使用的端口号，一般情况下SMTP端口号为25。
* local_hostname: 如果SMTP在你的本机上，你只需要指定服务器地址为 localhost 即可。

Python SMTP对象使用sendmail方法发送邮件，语法如下：

```python
SMTP.sendmail(from_addr, to_addrs, msg[, mail_options, rcpt_options]
```

参数说明：

* from_addr: 邮件发送者地址。
* to_addrs: 字符串列表，邮件发送地址。
* msg: 发送消息

这里要注意一下第三个参数，msg是字符串，表示邮件。我们知道邮件一般由标题，发信人，收件人，邮件内容，附件等构成，发送邮件的时候，要注意msg的格式。这个格式就是smtp协议中定义的格式。

实例
以下是一个使用Python发送邮件简单的实例：

```python 
import smtplib
from email.mime.text import MIMEText
from email.header import Header
 
sender = 'from@amazon.com'
receivers = ['429240967@qq.com']  # 接收邮件，可设置为你的QQ邮箱或者其他邮箱
 
# 三个参数：第一个为文本内容，第二个 plain 设置文本格式，第三个 utf-8 设置编码
message = MIMEText('Python 邮件发送测试...', 'plain', 'utf-8')
message['From'] = Header("网上购物", 'utf-8')     # 发送者
message['To'] =  Header("测试", 'utf-8')          # 接收者
 
subject = 'Python SMTP 邮件测试'
message['Subject'] = Header(subject, 'utf-8')
 
 
try:
    smtpObj = smtplib.SMTP('localhost')
    smtpObj.sendmail(sender, receivers, message.as_string())
    print ("邮件发送成功")
except smtplib.SMTPException:
    print ("Error: 无法发送邮件")
```

我们使用三个引号来设置邮件信息，标准邮件需要三个头部信息： From, To, 和 Subject ，每个信息直接使用空行分割。

我们通过实例化 smtplib 模块的 SMTP 对象 smtpObj 来连接到 SMTP 访问，并使用 sendmail 方法来发送信息。

执行以上程序，如果你本机安装sendmail，就会输出：

```bash
$ python3 test.py 
邮件发送成功
```

查看我们的收件箱(一般在垃圾箱)，就可以查看到邮件信息.

如果我们本机没有 sendmail 访问，也可以使用其他服务商的 SMTP 访问（QQ、网易、Google等）。

```python 
import smtplib
from email.mime.text import MIMEText
from email.header import Header
 
# 第三方 SMTP 服务
mail_host="smtp.XXX.com"  #设置服务器
mail_user="XXXX"    #用户名
mail_pass="XXXXXX"   #口令 
 
 
sender = 'from@amazon.com'
receivers = ['429240967@qq.com']  # 接收邮件，可设置为你的QQ邮箱或者其他邮箱
 
message = MIMEText('Python 邮件发送测试...', 'plain', 'utf-8')
message['From'] = Header("网上购物", 'utf-8')
message['To'] =  Header("测试", 'utf-8')
 
subject = 'Python SMTP 邮件测试'
message['Subject'] = Header(subject, 'utf-8')
 
 
try:
    smtpObj = smtplib.SMTP() 
    smtpObj.connect(mail_host, 25)    # 25 为 SMTP 端口号
    smtpObj.login(mail_user,mail_pass)
    smtpObj.sendmail(sender, receivers, message.as_string())
    print ("邮件发送成功")
except smtplib.SMTPException:
    print ("Error: 无法发送邮件")
```


### 5.1 使用Python发送HTML格式的邮件

Python发送HTML格式的邮件与发送纯文本消息的邮件不同之处就是将MIMEText中_subtype设置为html。具体代码如下：

```python 
import smtplib
from email.mime.text import MIMEText
from email.header import Header
 
sender = 'from@amazon.com'
receivers = ['429240967@qq.com']  # 接收邮件，可设置为你的QQ邮箱或者其他邮箱
 
mail_msg = """
<p>Python 邮件发送测试...</p>
<p><a href="http://www.amazon.com">这是一个链接</a></p>
"""
message = MIMEText(mail_msg, 'html', 'utf-8')
message['From'] = Header("网上购物", 'utf-8')
message['To'] =  Header("测试", 'utf-8')
 
subject = 'Python SMTP 邮件测试'
message['Subject'] = Header(subject, 'utf-8')
 
 
try:
    smtpObj = smtplib.SMTP('localhost')
    smtpObj.sendmail(sender, receivers, message.as_string())
    print ("邮件发送成功")
except smtplib.SMTPException:
    print ("Error: 无法发送邮件")
```

执行以上程序，如果你本机安装sendmail，就会输出：

```bash
$ python3 test.py 
邮件发送成功
```

查看我们的收件箱(一般在垃圾箱)，就可以查看到邮件信息.


### 5.2 Python 发送带附件的邮件

发送带附件的邮件，首先要创建MIMEMultipart()实例，然后构造附件，如果有多个附件，可依次构造，最后利用smtplib.smtp发送。

```python 
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.header import Header
 
sender = 'from@amazon.com'
receivers = ['429240967@qq.com']  # 接收邮件，可设置为你的QQ邮箱或者其他邮箱
 
#创建一个带附件的实例
message = MIMEMultipart()
message['From'] = Header("网上购物", 'utf-8')
message['To'] =  Header("测试", 'utf-8')
subject = 'Python SMTP 邮件测试'
message['Subject'] = Header(subject, 'utf-8')
 
#邮件正文内容
message.attach(MIMEText('这是网上购物Python 邮件发送测试……', 'plain', 'utf-8'))
 
# 构造附件1，传送当前目录下的 test.txt 文件
att1 = MIMEText(open('test.txt', 'rb').read(), 'base64', 'utf-8')
att1["Content-Type"] = 'application/octet-stream'
# 这里的filename可以任意写，写什么名字，邮件中显示什么名字
att1["Content-Disposition"] = 'attachment; filename="test.txt"'
message.attach(att1)
 
# 构造附件2，传送当前目录下的 amazon.txt 文件
att2 = MIMEText(open('amazon.txt', 'rb').read(), 'base64', 'utf-8')
att2["Content-Type"] = 'application/octet-stream'
att2["Content-Disposition"] = 'attachment; filename="amazon.txt"'
message.attach(att2)
 
try:
    smtpObj = smtplib.SMTP('localhost')
    smtpObj.sendmail(sender, receivers, message.as_string())
    print ("邮件发送成功")
except smtplib.SMTPException:
    print ("Error: 无法发送邮件")
```

```bash
$ python3 test.py 
邮件发送成功
```

查看我们的收件箱(一般在垃圾箱)，就可以查看到邮件信息.


### 5.3 在 HTML 文本中添加图片

邮件的 HTML 文本中一般邮件服务商添加外链是无效的，正确添加图片的实例如下所示：

```python 
import smtplib
from email.mime.image import MIMEImage
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.header import Header
 
sender = 'from@amazon.com'
receivers = ['429240967@qq.com']  # 接收邮件，可设置为你的QQ邮箱或者其他邮箱
 
msgRoot = MIMEMultipart('related')
msgRoot['From'] = Header("网上购物", 'utf-8')
msgRoot['To'] =  Header("测试", 'utf-8')
subject = 'Python SMTP 邮件测试'
msgRoot['Subject'] = Header(subject, 'utf-8')
 
msgAlternative = MIMEMultipart('alternative')
msgRoot.attach(msgAlternative)
 
 
mail_msg = """
<p>Python 邮件发送测试...</p>
<p><a href="http://www.amazon.com">网上购物链接</a></p>
<p>图片演示：</p>
<p><img src="cid:image1"></p>
"""
msgAlternative.attach(MIMEText(mail_msg, 'html', 'utf-8'))
 
# 指定图片为当前目录
fp = open('test.png', 'rb')
msgImage = MIMEImage(fp.read())
fp.close()
 
# 定义图片 ID，在 HTML 文本中引用
msgImage.add_header('Content-ID', '<image1>')
msgRoot.attach(msgImage)
 
try:
    smtpObj = smtplib.SMTP('localhost')
    smtpObj.sendmail(sender, receivers, msgRoot.as_string())
    print ("邮件发送成功")
except smtplib.SMTPException:
    print ("Error: 无法发送邮件")
```

```bash
$ python3 test.py 
邮件发送成功
```

查看我们的收件箱(如果在垃圾箱可能需要移动到收件箱才可正常显示)，就可以查看到邮件信息.


### 5.4 使用第三方 SMTP 服务发送

这里使用了 QQ 邮箱(你也可以使用 163，Gmail等)的 SMTP 服务.

QQ 邮箱通过生成授权码来设置密码.

QQ 邮箱 SMTP 服务器地址：smtp.qq.com，ssl 端口：465。

以下实例你需要修改：发件人邮箱（你的QQ邮箱），密码，收件人邮箱（可发给自己）。


```python
# QQ SMTP

import smtplib
from email.mime.text import MIMEText
from email.utils import formataddr
 
my_sender='429240967@qq.com'    # 发件人邮箱账号
my_pass = 'xxxxxxxxxx'              # 发件人邮箱密码
my_user='429240967@qq.com'      # 收件人邮箱账号，我这边发送给自己
def mail():
    ret=True
    try:
        msg=MIMEText('填写邮件内容','plain','utf-8')
        msg['From']=formataddr(["FromAmazon",my_sender])  # 括号里的对应发件人邮箱昵称、发件人邮箱账号
        msg['To']=formataddr(["FK",my_user])              # 括号里的对应收件人邮箱昵称、收件人邮箱账号
        msg['Subject']="网上购物发送邮件测试"                # 邮件的主题，也可以说是标题
 
        server=smtplib.SMTP_SSL("smtp.qq.com", 465)  # 发件人邮箱中的SMTP服务器，端口是25
        server.login(my_sender, my_pass)  # 括号中对应的是发件人邮箱账号、邮箱密码
        server.sendmail(my_sender,[my_user,],msg.as_string())  # 括号中对应的是发件人邮箱账号、收件人邮箱账号、发送邮件
        server.quit()  # 关闭连接
    except Exception:  # 如果 try 中的语句没有执行，则会执行下面的 ret=False
        ret=False
    return ret
 
ret=mail()
if ret:
    print("邮件发送成功")
else:
    print("邮件发送失败")
```

```bash
$ python test.py 
邮件发送成功
```

发送成功后，登陆收件人邮箱即可查看到数据。



## 6 多线程

多线程类似于同时执行多个不同程序，多线程运行有如下优点：

* 使用线程可以把占据长时间的程序中的任务放到后台去处理。
* 用户界面可以更加吸引人，比如用户点击了一个按钮去触发某些事件的处理，可以弹出一个进度条来显示处理的进度。
* 程序的运行速度可能加快。
* 在一些等待的任务实现上如用户输入、文件读写和网络收发数据等，线程就比较有用了。在这种情况下我们可以释放一些珍贵的资源如内存占用等等。

每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。

每个线程都有他自己的一组CPU寄存器，称为线程的上下文，该上下文反映了线程上次运行该线程的CPU寄存器的状态。

指令指针和堆栈指针寄存器是线程上下文中两个最重要的寄存器，线程总是在进程得到上下文中运行的，这些地址都用于标志拥有线程的进程地址空间中的内存。

* 线程可以被抢占（中断）。
* 在其他线程正在运行时，线程可以暂时搁置（也称为睡眠） -- 这就是线程的退让。

线程可以分为:

* 内核线程：由操作系统内核创建和撤销。
* 用户线程：不需要内核支持而在用户程序中实现的线程。

Python3 线程中常用的两个模块为：

* _thread
* threading(推荐使用)

thread 模块已被废弃。用户可以使用 threading 模块代替。所以，在 Python3 中不能再使用"thread" 模块。为了兼容性，Python3 将 thread 重命名为 "_thread"。


### 6.1 开始学习Python线程

Python中使用线程有两种方式：函数或者用类来包装线程对象。

函数式：调用 _thread 模块中的start_new_thread()函数来产生新线程。语法如下:

```bash
_thread.start_new_thread ( function, args[, kwargs] )
```

参数说明:

* function - 线程函数。
* args - 传递给线程函数的参数,他必须是个tuple类型。
* kwargs - 可选参数。

```python
import _thread
import time

# 为线程定义一个函数
def print_time( threadName, delay):
   count = 0
   while count < 5:
      time.sleep(delay)
      count += 1
      print ("%s: %s" % ( threadName, time.ctime(time.time()) ))

# 创建两个线程
try:
   _thread.start_new_thread( print_time, ("Thread-1", 2, ) )
   _thread.start_new_thread( print_time, ("Thread-2", 4, ) )
except:
   print ("Error: 无法启动线程")

while 1:
   pass
```

执行以上程序输出结果如下：

```bash
Thread-1: Wed Jan  5 17:38:08 2022
Thread-2: Wed Jan  5 17:38:10 2022
Thread-1: Wed Jan  5 17:38:10 2022
Thread-1: Wed Jan  5 17:38:12 2022
Thread-2: Wed Jan  5 17:38:14 2022
Thread-1: Wed Jan  5 17:38:14 2022
Thread-1: Wed Jan  5 17:38:16 2022
Thread-2: Wed Jan  5 17:38:18 2022
Thread-2: Wed Jan  5 17:38:22 2022
Thread-2: Wed Jan  5 17:38:26 2022
```

执行以上程后可以按下 ctrl-c 退出。


### 6.2 线程模块

Python3 通过两个标准库 _thread 和 threading 提供对线程的支持。

_thread 提供了低级别的、原始的线程以及一个简单的锁，它相比于 threading 模块的功能还是比较有限的。

threading 模块除了包含 _thread 模块中的所有方法外，还提供的其他方法：

* threading.currentThread(): 返回当前的线程变量。
* threading.enumerate(): 返回一个包含正在运行的线程的list。正在运行指线程启动后、结束前，不包括启动前和终止后的线程。
* threading.activeCount(): 返回正在运行的线程数量，与len(threading.enumerate())有相同的结果。

除了使用方法外，线程模块同样提供了Thread类来处理线程，Thread类提供了以下方法:

* run(): 用以表示线程活动的方法。
* start():启动线程活动。
* join([time]): 等待至线程中止。这阻塞调用线程直至线程的join() 方法被调用中止-正常退出或者抛出未处理的异常-或者是可选的超时发生。
* isAlive(): 返回线程是否活动的。
* getName(): 返回线程名。
* setName(): 设置线程名。


### 6.3 使用 threading 模块创建线程

我们可以通过直接从 threading.Thread 继承创建一个新的子类，并实例化后调用 start() 方法启动新线程，即它调用了线程的 run() 方法：

```python
import threading
import time

exitFlag = 0

class myThread (threading.Thread):
    def __init__(self, threadID, name, delay):
        threading.Thread.__init__(self)
        self.threadID = threadID
        self.name = name
        self.delay = delay
    def run(self):
        print ("开始线程：" + self.name)
        print_time(self.name, self.delay, 5)
        print ("退出线程：" + self.name)

def print_time(threadName, delay, counter):
    while counter:
        if exitFlag:
            threadName.exit()
        time.sleep(delay)
        print ("%s: %s" % (threadName, time.ctime(time.time())))
        counter -= 1

# 创建新线程
thread1 = myThread(1, "Thread-1", 1)
thread2 = myThread(2, "Thread-2", 2)

# 开启新线程
thread1.start()
thread2.start()
thread1.join()
thread2.join()
print ("退出主线程")
```

以上程序执行结果如下；

```bash
开始线程：Thread-1
开始线程：Thread-2
Thread-1: Wed Jan  5 17:34:54 2022
Thread-2: Wed Jan  5 17:34:55 2022
Thread-1: Wed Jan  5 17:34:55 2022
Thread-1: Wed Jan  5 17:34:56 2022
Thread-2: Wed Jan  5 17:34:57 2022
Thread-1: Wed Jan  5 17:34:57 2022
Thread-1: Wed Jan  5 17:34:58 2022
退出线程：Thread-1
Thread-2: Wed Jan  5 17:34:59 2022
Thread-2: Wed Jan  5 17:35:01 2022
Thread-2: Wed Jan  5 17:35:03 2022
退出线程：Thread-2
退出主线程
```


### 6.4 线程同步

如果多个线程共同对某个数据修改，则可能出现不可预料的结果，为了保证数据的正确性，需要对多个线程进行同步。

使用 Thread 对象的 Lock 和 Rlock 可以实现简单的线程同步，这两个对象都有 acquire 方法和 release 方法，对于那些需要每次只允许一个线程操作的数据，可以将其操作放到 acquire 和 release 方法之间。如下：

多线程的优势在于可以同时运行多个任务（至少感觉起来是这样）。但是当线程需要共享数据时，可能存在数据不同步的问题。

考虑这样一种情况：一个列表里所有元素都是0，线程"set"从后向前把所有元素改成1，而线程"print"负责从前往后读取列表并打印。

那么，可能线程"set"开始改的时候，线程"print"便来打印列表了，输出就成了一半0一半1，这就是数据的不同步。为了避免这种情况，引入了锁的概念。

锁有两种状态——锁定和未锁定。每当一个线程比如"set"要访问共享数据时，必须先获得锁定；如果已经有别的线程比如"print"获得锁定了，那么就让线程"set"暂停，也就是同步阻塞；等到线程"print"访问完毕，释放锁以后，再让线程"set"继续。

经过这样的处理，打印列表时要么全部输出0，要么全部输出1，不会再出现一半0一半1的尴尬场面。

```python
import threading
import time

class myThread (threading.Thread):
    def __init__(self, threadID, name, delay):
        threading.Thread.__init__(self)
        self.threadID = threadID
        self.name = name
        self.delay = delay
    def run(self):
        print ("开启线程： " + self.name)
        # 获取锁，用于线程同步
        threadLock.acquire()
        print_time(self.name, self.delay, 3)
        # 释放锁，开启下一个线程
        threadLock.release()

def print_time(threadName, delay, counter):
    while counter:
        time.sleep(delay)
        print ("%s: %s" % (threadName, time.ctime(time.time())))
        counter -= 1

threadLock = threading.Lock()
threads = []

# 创建新线程
thread1 = myThread(1, "Thread-1", 1)
thread2 = myThread(2, "Thread-2", 2)

# 开启新线程
thread1.start()
thread2.start()

# 添加线程到线程列表
threads.append(thread1)
threads.append(thread2)

# 等待所有线程完成
for t in threads:
    t.join()
print ("退出主线程")
```

执行以上程序，输出结果为：

```bash
开启线程： Thread-1
开启线程： Thread-2
Thread-1: Wed Jan  5 17:36:50 2022
Thread-1: Wed Jan  5 17:36:51 2022
Thread-1: Wed Jan  5 17:36:52 2022
Thread-2: Wed Jan  5 17:36:54 2022
Thread-2: Wed Jan  5 17:36:56 2022
Thread-2: Wed Jan  5 17:36:58 2022
退出主线程
```


### 6.5 线程优先级队列（ Queue）

Python 的 Queue 模块中提供了同步的、线程安全的队列类，包括FIFO（先入先出)队列Queue，LIFO（后入先出）队列LifoQueue，和优先级队列 PriorityQueue。

这些队列都实现了锁原语，能够在多线程中直接使用，可以使用队列来实现线程间的同步。

Queue 模块中的常用方法:

* Queue.qsize() 返回队列的大小
* Queue.empty() 如果队列为空，返回True,反之False
* Queue.full() 如果队列满了，返回True,反之False
* Queue.full 与 maxsize 大小对应
* Queue.get([block[, timeout]])获取队列，timeout等待时间
* Queue.get_nowait() 相当Queue.get(False)
* Queue.put(item) 写入队列，timeout等待时间
* Queue.put_nowait(item) 相当Queue.put(item, False)
* Queue.task_done() 在完成一项工作之后，Queue.task_done()函数向任务已经完成的队列发送一个信号
* Queue.join() 实际上意味着等到队列为空，再执行别的操作

```python
import queue
import threading
import time

exitFlag = 0

class myThread (threading.Thread):
    def __init__(self, threadID, name, q):
        threading.Thread.__init__(self)
        self.threadID = threadID
        self.name = name
        self.q = q
    def run(self):
        print ("开启线程：" + self.name)
        process_data(self.name, self.q)
        print ("退出线程：" + self.name)

def process_data(threadName, q):
    while not exitFlag:
        queueLock.acquire()
        if not workQueue.empty():
            data = q.get()
            queueLock.release()
            print ("%s processing %s" % (threadName, data))
        else:
            queueLock.release()
        time.sleep(1)

threadList = ["Thread-1", "Thread-2", "Thread-3"]
nameList = ["One", "Two", "Three", "Four", "Five"]
queueLock = threading.Lock()
workQueue = queue.Queue(10)
threads = []
threadID = 1

# 创建新线程
for tName in threadList:
    thread = myThread(threadID, tName, workQueue)
    thread.start()
    threads.append(thread)
    threadID += 1

# 填充队列
queueLock.acquire()
for word in nameList:
    workQueue.put(word)
queueLock.release()

# 等待队列清空
while not workQueue.empty():
    pass

# 通知线程是时候退出
exitFlag = 1

# 等待所有线程完成
for t in threads:
    t.join()
print ("退出主线程")
```

以上程序执行结果：

```bash
开启线程：Thread-1
开启线程：Thread-2
开启线程：Thread-3
Thread-3 processing One
Thread-1 processing Two
Thread-2 processing Three
Thread-3 processing Four
Thread-1 processing Five
退出线程：Thread-3
退出线程：Thread-2
退出线程：Thread-1
退出主线程
```



## 7 XML解析

### 7.1 什么是 XML？

XML 指可扩展标记语言（eXtensible Markup Language），标准通用标记语言的子集，是一种用于标记电子文件使其具有结构性的标记语言。 你可以通过本站学习 XML 教程

XML 被设计用来传输和存储数据。

XML 是一套定义语义标记的规则，这些标记将文档分成许多部件并对这些部件加以标识。

它也是元标记语言，即定义了用于定义其他与特定领域有关的、语义的、结构化的标记语言的句法语言。


### 7.2 Python 对 XML 的解析

常见的 XML 编程接口有 DOM 和 SAX，这两种接口处理 XML 文件的方式不同，当然使用场合也不同。

Python 有三种方法解析 XML，SAX，DOM，以及 ElementTree:

#### 7.2.1 SAX (simple API for XML )

Python 标准库包含 SAX 解析器，SAX 用事件驱动模型，通过在解析 XML 的过程中触发一个个的事件并调用用户定义的回调函数来处理 XML 文件。

#### 7.2.2 DOM(Document Object Model)

将 XML 数据在内存中解析成一个树，通过对树的操作来操作 XML。

本章节使用到的 XML 实例文件 movies.xml 内容如下：

```xml
<collection shelf="New Arrivals">
<movie title="Enemy Behind">
   <type>War, Thriller</type>
   <format>DVD</format>
   <year>2003</year>
   <rating>PG</rating>
   <stars>10</stars>
   <description>Talk about a US-Japan war</description>
</movie>
<movie title="Transformers">
   <type>Anime, Science Fiction</type>
   <format>DVD</format>
   <year>1989</year>
   <rating>R</rating>
   <stars>8</stars>
   <description>A schientific fiction</description>
</movie>
   <movie title="Trigun">
   <type>Anime, Action</type>
   <format>DVD</format>
   <episodes>4</episodes>
   <rating>PG</rating>
   <stars>10</stars>
   <description>Vash the Stampede!</description>
</movie>
<movie title="Ishtar">
   <type>Comedy</type>
   <format>VHS</format>
   <rating>PG</rating>
   <stars>2</stars>
   <description>Viewable boredom</description>
</movie>
</collection>
```

### 7.3 Python 使用 SAX 解析 xml

SAX 是一种基于事件驱动的API。

利用 SAX 解析 XML 文档牵涉到两个部分: 解析器和事件处理器。

解析器负责读取 XML 文档，并向事件处理器发送事件，如元素开始跟元素结束事件。

而事件处理器则负责对事件作出响应，对传递的 XML 数据进行处理。

* 1、对大型文件进行处理；
* 2、只需要文件的部分内容，或者只需从文件中得到特定信息。
* 3、想建立自己的对象模型的时候。

在 Python 中使用 sax 方式处理 xml 要先引入 xml.sax 中的 parse 函数，还有 xml.sax.handler 中的 ContentHandler。

#### 7.3.1 ContentHandler 类方法介绍

**characters(content) 方法**

调用时机：

从行开始，遇到标签之前，存在字符，content 的值为这些字符串。

从一个标签，遇到下一个标签之前， 存在字符，content 的值为这些字符串。

从一个标签，遇到行结束符之前，存在字符，content 的值为这些字符串。

标签可以是开始标签，也可以是结束标签。

**startDocument() 方法**

文档启动的时候调用。

**endDocument() 方法**

解析器到达文档结尾时调用。

**startElement(name, attrs) 方法**

遇到XML开始标签时调用，name 是标签的名字，attrs 是标签的属性值字典。

**endElement(name) 方法**

遇到XML结束标签时调用。


### 7.4 make_parser 方法

以下方法创建一个新的解析器对象并返回。

```python
xml.sax.make_parser( [parser_list] )
```

参数说明:

* parser_list - 可选参数，解析器列表


### 7.5 parser 方法

以下方法创建一个 SAX 解析器并解析xml文档：

```python
xml.sax.parse( xmlfile, contenthandler[, errorhandler])
```

参数说明:

* xmlfile - xml文件名
* contenthandler - 必须是一个 ContentHandler 的对象
* errorhandler - 如果指定该参数，errorhandler 必须是一个 SAX ErrorHandler 对象


### 7.6 parseString 方法

parseString 方法创建一个 XML 解析器并解析 xml 字符串：

```python
xml.sax.parseString(xmlstring, contenthandler[, errorhandler])
```

参数说明:

* xmlstring - xml字符串
* contenthandler - 必须是一个 ContentHandler 的对象
* errorhandler - 如果指定该参数，errorhandler 必须是一个 SAX ErrorHandler对象


### 7.7 Python 解析XML实例

```python
import xml.sax

class MovieHandler( xml.sax.ContentHandler ):
   def __init__(self):
      self.CurrentData = ""
      self.type = ""
      self.format = ""
      self.year = ""
      self.rating = ""
      self.stars = ""
      self.description = ""

   # 元素开始调用
   def startElement(self, tag, attributes):
      self.CurrentData = tag
      if tag == "movie":
         print ("*****Movie*****")
         title = attributes["title"]
         print ("Title:", title)

   # 元素结束调用
   def endElement(self, tag):
      if self.CurrentData == "type":
         print ("Type:", self.type)
      elif self.CurrentData == "format":
         print ("Format:", self.format)
      elif self.CurrentData == "year":
         print ("Year:", self.year)
      elif self.CurrentData == "rating":
         print ("Rating:", self.rating)
      elif self.CurrentData == "stars":
         print ("Stars:", self.stars)
      elif self.CurrentData == "description":
         print ("Description:", self.description)
      self.CurrentData = ""

   # 读取字符时调用
   def characters(self, content):
      if self.CurrentData == "type":
         self.type = content
      elif self.CurrentData == "format":
         self.format = content
      elif self.CurrentData == "year":
         self.year = content
      elif self.CurrentData == "rating":
         self.rating = content
      elif self.CurrentData == "stars":
         self.stars = content
      elif self.CurrentData == "description":
         self.description = content
 
if ( __name__ == "__main__"):
   
   # 创建一个 XMLReader
   parser = xml.sax.make_parser()
   # 关闭命名空间
   parser.setFeature(xml.sax.handler.feature_namespaces, 0)

   # 重写 ContextHandler
   Handler = MovieHandler()
   parser.setContentHandler( Handler )
   
   parser.parse("movies.xml")
```

以上代码执行结果如下：

```bash
*****Movie*****
Title: Enemy Behind
Type: War, Thriller
Format: DVD
Year: 2003
Rating: PG
Stars: 10
Description: Talk about a US-Japan war
*****Movie*****
Title: Transformers
Type: Anime, Science Fiction
Format: DVD
Year: 1989
Rating: R
Stars: 8
Description: A schientific fiction
*****Movie*****
Title: Trigun
Type: Anime, Action
Format: DVD
Rating: PG
Stars: 10
Description: Vash the Stampede!
*****Movie*****
Title: Ishtar
Type: Comedy
Format: VHS
Rating: PG
Stars: 2
Description: Viewable boredom
```


### 7.8 使用xml.dom解析xml

文件对象模型（Document Object Model，简称DOM），是W3C组织推荐的处理可扩展置标语言的标准编程接口。

一个 DOM 的解析器在解析一个 XML 文档时，一次性读取整个文档，把文档中所有元素保存在内存中的一个树结构里，之后你可以利用DOM 提供的不同的函数来读取或修改文档的内容和结构，也可以把修改过的内容写入xml文件。

Python 中用 xml.dom.minidom 来解析 xml 文件，实例如下：

```python
from xml.dom.minidom import parse
import xml.dom.minidom

# 使用minidom解析器打开 XML 文档
DOMTree = xml.dom.minidom.parse("movies.xml")
collection = DOMTree.documentElement
if collection.hasAttribute("shelf"):
   print ("Root element : %s" % collection.getAttribute("shelf"))

# 在集合中获取所有电影
movies = collection.getElementsByTagName("movie")

# 打印每部电影的详细信息
for movie in movies:
   print ("*****Movie*****")
   if movie.hasAttribute("title"):
      print ("Title: %s" % movie.getAttribute("title"))

   type = movie.getElementsByTagName('type')[0]
   print ("Type: %s" % type.childNodes[0].data)
   format = movie.getElementsByTagName('format')[0]
   print ("Format: %s" % format.childNodes[0].data)
   rating = movie.getElementsByTagName('rating')[0]
   print ("Rating: %s" % rating.childNodes[0].data)
   description = movie.getElementsByTagName('description')[0]
   print ("Description: %s" % description.childNodes[0].data)
```

以上程序执行结果如下：

```bash
Root element : New Arrivals
*****Movie*****
Title: Enemy Behind
Type: War, Thriller
Format: DVD
Rating: PG
Description: Talk about a US-Japan war
*****Movie*****
Title: Transformers
Type: Anime, Science Fiction
Format: DVD
Rating: R
Description: A schientific fiction
*****Movie*****
Title: Trigun
Type: Anime, Action
Format: DVD
Rating: PG
Description: Vash the Stampede!
*****Movie*****
Title: Ishtar
Type: Comedy
Format: VHS
Rating: PG
Description: Viewable boredom
```



## 8 JSON

SON (JavaScript Object Notation) 是一种轻量级的数据交换格式。

Python3 中可以使用 json 模块来对 JSON 数据进行编解码，它包含了两个函数：

* json.dumps(): 对数据进行编码。
* json.loads(): 对数据进行解码。

在 json 的编解码过程中，Python 的原始类型与 json 类型会相互转换，具体的转化对照如下：

Python 编码为 JSON 类型转换对应表：

| Python                                  | JSON   |
| --------------------------------------- | ------ |
| dict                                    | object |
| list, tuple                             | array  |
| str                                     | string |
| int, float, int- & float-derived Enums  | number |
| True                                    | true   |
| False                                   | false  |
| None                                    | null   |

JSON 解码为 Python 类型转换对应表：

| JSON         | Python    |
| ------------ | --------- |
| object       | dict      |
| array        | list      |
| string       | str       |
| number(int)  | int       |
| number(real) | float     |
| true         | True      |
| false        | False     |
| null         | None      |


### 8.1 json.dumps 与 json.loads 实例

以下实例演示了 Python 数据结构转换为JSON：

```python 
import json
 
# Python 字典类型转换为 JSON 对象
data = {
    'no' : 1,
    'name' : 'Amazon',
    'url' : 'http://www.amazon.com'
}
 
json_str = json.dumps(data)
print ("Python 原始数据：", repr(data))
print ("JSON 对象：", json_str)
```

执行以上代码输出结果为：

```bash
Python 原始数据： {'url': 'http://www.amazon.com', 'no': 1, 'name': 'Amazon'}
JSON 对象： {"url": "http://www.amazon.com", "no": 1, "name": "Amazon"}
```

通过输出的结果可以看出，简单类型通过编码后跟其原始的repr()输出结果非常相似。

接着以上实例，我们可以将一个JSON编码的字符串转换回一个Python数据结构：

```python 
import json
 
# Python 字典类型转换为 JSON 对象
data1 = {
    'no' : 1,
    'name' : 'Amazon',
    'url' : 'http://www.amazon.com'
}
 
json_str = json.dumps(data1)
print ("Python 原始数据：", repr(data1))
print ("JSON 对象：", json_str)
 
# 将 JSON 对象转换为 Python 字典
data2 = json.loads(json_str)
print ("data2['name']: ", data2['name'])
print ("data2['url']: ", data2['url'])
```

执行以上代码输出结果为：

```bash
Python 原始数据： {'name': 'Amazon', 'no': 1, 'url': 'http://www.amazon.com'}
JSON 对象： {"name": "Amazon", "no": 1, "url": "http://www.amazon.com"}
data2['name']:  Amazon
data2['url']:  http://www.amazon.com
```

如果你要处理的是文件而不是字符串，你可以使用 json.dump() 和 json.load() 来编码和解码JSON数据。例如：

```python
# 写入 JSON 数据
with open('data.json', 'w') as f:
    json.dump(data, f)
 
# 读取数据
with open('data.json', 'r') as f:
    data = json.load(f)
```



## 9 日期和时间

Python 程序能用很多方式处理日期和时间，转换日期格式是一个常见的功能。

Python 提供了一个 time 和 calendar 模块可以用于格式化日期和时间。

时间间隔是以秒为单位的浮点小数。

每个时间戳都以自从 1970 年 1 月 1 日午夜（历元）经过了多长时间来表示。

Python 的 time 模块下有很多函数可以转换常见日期格式。如函数 time.time() 用于获取当前时间戳, 如下实例:

```python
import time  # 引入time模块

ticks = time.time()
print ("当前时间戳为:", ticks)
```

以上实例输出结果：

```bash
当前时间戳为: 1459996086.7115328
```

时间戳单位最适于做日期运算。但是1970年之前的日期就无法以此表示了。太遥远的日期也不行，UNIX和Windows只支持到2038年。


### 9.1 什么是时间元组？

很多Python函数用一个元组装起来的9组数字处理时间:

| No. | Word            | Value                             |
| --- | --------------- | --------------------------------- |
| 0   | 4位数年          | 2008                              |
| 1   | 月              | 1 到 12                            |
| 2   | 日              | 1 到 31                            |
| 3   | 小时            | 0 到 23                            |
| 4   | 分钟            | 0 到 59                            |
| 5   | 秒              | 0 到 61 (60或61 是闰秒)            |
| 6   | 一周的第几日     | 0 到 6 (0是周一)                   |
| 7   | 一年的第几日     | 1 到 366 (儒略历)                  |
| 8   | 夏令时          | -1, 0, 1, -1是决定是否为夏令时的旗帜 |

上述也就是struct_time元组。这种结构具有如下属性：

| No. | Word            | Value                                                     |
|---- | --------------- | --------------------------------------------------------- |
| 0   | tm_year         | 2008                                                      |
| 1   | tm_mon          | 1 到 12                                                   |
| 2   | tm_mday         | 1 到 31                                                   |
| 3   | tm_hour         | 0 到 23                                                   |
| 4   | tm_min          | 0 到 59                                                   |
| 5   | tm_sec          | 0 到 61 (60或61 是闰秒)                                    |
| 6   | tm_wday         | 0到6 (0是周一)                                             |
| 7   | tm_yday         | 一年中的第几天，1 到 366                                    |
| 8   | tm_isdst        | 是否为夏令时，值有：1(夏令时)、0(不是夏令时)、-1(未知)，默认 -1|


### 9.2 获取当前时间

从返回浮点数的时间戳方式向时间元组转换，只要将浮点数传递给如localtime之类的函数。

```python
import time

localtime = time.localtime(time.time())
print ("本地时间为 :", localtime)
```

以上实例输出结果：

```bash
本地时间为 : time.struct_time(tm_year=2016, tm_mon=4, tm_mday=7, tm_hour=10, tm_min=28, tm_sec=49, tm_wday=3, tm_yday=98, tm_isdst=0)
```


### 9.3 获取格式化的时间

你可以根据需求选取各种格式，但是最简单的获取可读的时间模式的函数是asctime():

```python
import time

localtime = time.asctime( time.localtime(time.time()) )
print ("本地时间为 :", localtime)
```

以上实例输出结果：

```bash
本地时间为 : Thu Apr  7 10:29:13 2016
```


### 9.4 格式化日期

我们可以使用 time 模块的 strftime 方法来格式化日期：

```bash
time.strftime(format[, t])
```

```python
import time

# 格式化成2016-03-20 11:45:39形式
print (time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()))

# 格式化成Sat Mar 28 22:24:24 2016形式
print (time.strftime("%a %b %d %H:%M:%S %Y", time.localtime()))
 
# 将格式字符串转换为时间戳
a = "Sat Mar 28 22:24:24 2016"
print (time.mktime(time.strptime(a,"%a %b %d %H:%M:%S %Y")))
```

以上实例输出结果：

```bash
2016-04-07 10:29:46
Thu Apr 07 10:29:46 2016
1459175064.0
```

python中时间日期格式化符号：

* %y 两位数的年份表示（00-99）
* %Y 四位数的年份表示（000-9999）
* %m 月份（01-12）
* %d 月内中的一天（0-31）
* %H 24小时制小时数（0-23）
* %I 12小时制小时数（01-12）
* %M 分钟数（00=59）
* %S 秒（00-59）
* %a 本地简化星期名称
* %A 本地完整星期名称
* %b 本地简化的月份名称
* %B 本地完整的月份名称
* %c 本地相应的日期表示和时间表示
* %j 年内的一天（001-366）
* %p 本地A.M.或P.M.的等价符
* %U 一年中的星期数（00-53）星期天为星期的开始
* %w 星期（0-6），星期天为星期的开始
* %W 一年中的星期数（00-53）星期一为星期的开始
* %x 本地相应的日期表示
* %X 本地相应的时间表示
* %Z 当前时区的名称
* %% %号本身


### 9.5 获取某月日历

Calendar 模块有很广泛的方法用来处理年历和月历，例如打印某月的月历：

```python
import calendar

cal = calendar.month(2016, 1)
print ("以下输出2016年1月份的日历:")
print (cal)
```

以上实例输出结果：

```bash
以下输出2016年1月份的日历:
    January 2016
Mo Tu We Th Fr Sa Su
             1  2  3
 4  5  6  7  8  9 10
11 12 13 14 15 16 17
18 19 20 21 22 23 24
25 26 27 28 29 30 31
```


### 9.6 Time 模块

Time 模块包含了以下内置函数，既有时间处理的，也有转换时间格式的：


#### 9.6.1 time.altzone

返回格林威治西部的夏令时地区的偏移秒数。如果该地区在格林威治东部会返回负值（如西欧，包括英国）。对夏令时启用地区才能使用。	
以下实例展示了 altzone()函数的使用方法：

```bash
>>> import time
>>> print ("time.altzone %d " % time.altzone)
time.altzone -28800 
```
#### 9.6.2 time.asctime([tupletime])

接受时间元组并返回一个可读的形式为"Tue Dec 11 18:07:14 2008"（2008年12月11日 周二18时07分14秒）的24个字符的字符串。	
以下实例展示了 asctime()函数的使用方法：

```bash
>>> import time
>>> t = time.localtime()
>>> print ("time.asctime(t): %s " % time.asctime(t))
time.asctime(t): Thu Apr  7 10:36:20 2016 
```

#### 9.6.3 time.clock()

用以浮点数计算的秒数返回当前的CPU时间。用来衡量不同程序的耗时，比time.time()更有用。	
实例

由于该方法依赖操作系统，在 Python 3.3 以后不被推荐，而在 3.8 版本中被移除，需使用下列两个函数替代。

```python
time.perf_counter()  # 返回系统运行时间
time.process_time()  # 返回进程运行时间
```

#### 9.6.4 time.ctime([secs])

作用相当于asctime(localtime(secs))，未给参数相当于asctime()	
以下实例展示了 ctime()函数的使用方法：

```bash
>>> import time
>>> print ("time.ctime() : %s" % time.ctime())
time.ctime() : Thu Apr  7 10:51:58 2016
```

#### 9.6.5 time.gmtime([secs])

接收时间戳（1970纪元后经过的浮点秒数）并返回格林威治天文时间下的时间元组t。注：t.tm_isdst始终为0	
以下实例展示了 gmtime()函数的使用方法：

```bash
>>> import time
>>> print ("gmtime :", time.gmtime(1455508609.34375))
gmtime : time.struct_time(tm_year=2016, tm_mon=2, tm_mday=15, tm_hour=3, tm_min=56, tm_sec=49, tm_wday=0, tm_yday=46, tm_isdst=0)
```

#### 9.6.6 time.localtime([secs]

接收时间戳（1970纪元后经过的浮点秒数）并返回当地时间下的时间元组t（t.tm_isdst可取0或1，取决于当地当时是不是夏令时）。	
以下实例展示了 localtime()函数的使用方法：

```bash
>>> import time
>>> print ("localtime(): ", time.localtime(1455508609.34375))
localtime():  time.struct_time(tm_year=2016, tm_mon=2, tm_mday=15, tm_hour=11, tm_min=56, tm_sec=49, tm_wday=0, tm_yday=46, tm_isdst=0)
```

#### 9.6.7 time.mktime(tupletime)

接受时间元组并返回时间戳（1970纪元后经过的浮点秒数）。

```python
import time

t = (2016, 2, 17, 17, 3, 38, 1, 48, 0)
secs = time.mktime( t )
print ("time.mktime(t) : %f" %  secs)
print ("asctime(localtime(secs)): %s" % time.asctime(time.localtime(secs)))
```

以上实例输出结果为：

```bash
time.mktime(t) : 1455699818.000000
asctime(localtime(secs)): Wed Feb 17 17:03:38 2016
```

#### 9.6.8 time.sleep(secs)

推迟调用线程的运行，secs指秒数。	
以下实例展示了 sleep()函数的使用方法：

```python
import time

print ("Start : %s" % time.ctime())
time.sleep( 5 )
print ("End : %s" % time.ctime())
```

#### 9.6.9 time.strftime(fmt[,tupletime])

接收以时间元组，并返回以可读字符串表示的当地时间，格式由fmt决定。	
以下实例展示了 strftime()函数的使用方法：

```bash
>>> import time
>>> print (time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()))
2016-04-07 11:18:05
```

#### 9.6.10 time.strptime(str,fmt='%a %b %d %H:%M:%S %Y')

根据fmt的格式把一个时间字符串解析为时间元组。	
以下实例展示了 strptime()函数的使用方法：

```bash
>>> import time
>>> struct_time = time.strptime("30 Nov 00", "%d %b %y")
>>> print ("返回元组: ", struct_time)
返回元组:  time.struct_time(tm_year=2000, tm_mon=11, tm_mday=30, tm_hour=0, tm_min=0, tm_sec=0, tm_wday=3, tm_yday=335, tm_isdst=-1)
```

#### 9.6.11 time.time( )

返回当前时间的时间戳（1970纪元后经过的浮点秒数）。	
以下实例展示了 time()函数的使用方法：

```bash
>>> import time
>>> print(time.time())
1459999336.1963577
```

#### 9.6.12 time.tzset()

根据环境变量TZ重新初始化时间相关设置。

```python
import time
import os
 
os.environ['TZ'] = 'EST+05EDT,M4.1.0,M10.5.0'
time.tzset()
print (time.strftime('%X %x %Z'))
 
os.environ['TZ'] = 'AEST-10AEDT-11,M10.5.0,M3.5.0'
time.tzset()
print (time.strftime('%X %x %Z'))
```

以上实例输出结果为：

```bash
23:25:45 04/06/16 EDT
13:25:45 04/07/16 AEST
```

#### 9.6.13 time.perf_counter()

返回计时器的精准时间（系统的运行时间），包含整个系统的睡眠时间。由于返回值的基准点是未定义的，所以，只有连续调用的结果之间的差才是有效的。	

```python
import time

scale = 50 

print("执行开始".center(scale//2,"-"))  # .center() 控制输出的样式，宽度为 25//2，即 22，汉字居中，两侧填充 -

start = time.perf_counter() # 调用一次 perf_counter()，从计算机系统里随机选一个时间点A，计算其距离当前时间点B1有多少秒。当第二次调用该函数时，默认从第一次调用的时间点A算起，距离当前时间点B2有多少秒。两个函数取差，即实现从时间点B1到B2的计时功能。
for i in range(scale+1):   
    a = '*' * i             # i 个长度的 * 符号
    b = '.' * (scale-i)  # scale-i） 个长度的 . 符号。符号 * 和 . 总长度为50 
    c = (i/scale)*100  # 显示当前进度，百分之多少
    dur = time.perf_counter() - start    # 计时，计算进度条走到某一百分比的用时
    print("\r{:^3.0f}%[{}->{}]{:.2f}s".format(c,a,b,dur),end='')  # \r用来在每次输出完成后，将光标移至行首，这样保证进度条始终在同一行输出，即在一行不断刷新的效果；{:^3.0f}，输出格式为居中，占3位，小数点后0位，浮点型数，对应输出的数为c；{}，对应输出的数为a；{}，对应输出的数为b；{:.2f}，输出有两位小数的浮点数，对应输出的数为dur；end=''，用来保证不换行，不加这句默认换行。
    time.sleep(0.1)     # 在输出下一个百分之几的进度前，停止0.1秒
print("\n"+"执行结果".center(scale//2,'-'))
```

测试输出：

```bash
-----------执行开始----------
24 %[************->......................................]1.24s
```

#### 9.6.14 time.process_time()

返回当前进程执行 CPU 的时间总和，不包含睡眠时间。由于返回值的基准点是未定义的，所以，只有连续调用的结果之间的差才是有效的。	 

Time模块包含了以下2个非常重要的属性：

| operator        | description                                                           |
| --------------- | --------------------------------------------------------------------- |
| time.timezone   | 属性time.timezone是当地时区（未启动夏令时）距离格林威治的偏移秒数          |
| time.tzname     | 属性time.tzname包含一对根据情况的不同而不同的字符串，分带夏令时的和不带的   |


### 9.7 日历（Calendar）模块

此模块的函数都是日历相关的，例如打印某月的字符月历。

星期一是默认的每周第一天，星期天是默认的最后一天。更改设置需调用calendar.setfirstweekday()函数。模块包含了以下内置函数：

#### 9.7.1 calendar.calendar(year,w=2,l=1,c=6)

返回一个多行字符串格式的year年年历，3个月一行，间隔距离为c。 每日宽度间隔为w字符。每行长度为21* W+18+2* C。l是每星期行数。

#### 9.7.2 calendar.firstweekday( )

返回当前每周起始日期的设置。默认情况下，首次载入caendar模块时返回0，即星期一。

#### 9.7.3 calendar.isleap(year)

是闰年返回 True，否则为 false。

```bash
>>> import calendar
>>> print(calendar.isleap(2000))
True
>>> print(calendar.isleap(1900))
False
```

#### 9.7.4 calendar.leapdays(y1,y2)

返回在Y1，Y2两年之间的闰年总数。

#### 9.7.5 calendar.month(year,month,w=2,l=1)

返回一个多行字符串格式的year年month月日历，两行标题，一周一行。每日宽度间隔为w字符。每行的长度为7* w+6。l是每星期的行数。

#### 9.7.6 calendar.monthcalendar(year,month)

返回一个整数的单层嵌套列表。每个子列表装载代表一个星期的整数。Year年month月外的日期都设为0;范围内的日子都由该月第几日表示，从1开始。

#### 9.7.7 calendar.monthrange(year,month)

返回两个整数。第一个是该月的星期几，第二个是该月有几天。星期几是从0（星期一）到 6（星期日）。

```bash
>>> import calendar
>>> calendar.monthrange(2014, 11)
(5, 30)
```

(5, 30)解释：5 表示 2014 年 11 月份的第一天是周六，30 表示 2014 年 11 月份总共有 30 天。

#### 9.7.8 calendar.prcal(year, w=0, l=0, c=6, m=3)

相当于 print (calendar.calendar(year, w=0, l=0, c=6, m=3))。

#### 9.7.9 calendar.prmonth(theyear, themonth, w=0, l=0)

相当于 print(calendar.month(theyear, themonth, w=0, l=0))。

#### 9.7.10 calendar.setfirstweekday(weekday)

设置每周的起始日期码。0（星期一）到6（星期日）。

#### 9.7.11 calendar.timegm(tupletime)

和time.gmtime相反：接受一个时间元组形式，返回该时刻的时间戳（1970纪元后经过的浮点秒数）。

#### 9.7.12 calendar.weekday(year,month,day)

返回给定日期的日期码。0（星期一）到6（星期日）。月份为 1（一月） 到 12（12月）。


### 9.8 其他相关模块和函数

在Python中，其他处理日期和时间的模块还有：

time 模块
datetime模块



## 10 内置函数

注意：有些函数与 Python2.x 变化不大，会直接跳转到 Python2.x 教程下的内置函数说明，大家要注意下哈。

|                 |              |                 |              |                 |
| --------------- | ------------ | --------------- | ------------ | --------------- |
| abs()           | dict()       | help()          | min()        | setattr()       |
| all()           | dir()        | hex()           | next()       | slice()         |
| any()           | divmod()     | id()            | object()     | sorted()        |
| ascii()         | enumerate()  | input()         | oct()        | staticmethod()  |
| bin()           | eval()       | int()           | open()       | str()           |
| bool()          | exec()       | isinstance()    | ord()        | sum()           |
| bytearray()     | filter()     | issubclass()    | pow()        | super()         |
| bytes()         | float()      | iter()          | print()      | tuple()         |
| callable()      | format()     | len()           | property()   | type()          |
| chr()           | frozenset()  | list()          | range()      | vars()          |
| classmethod()   | getattr()    | locals()        | repr()       | zip()           |
| compile()       | globals()    | map()           | reversed()   | import__()      |
| complex()       | hasattr()    | max()           | round()      | reload()        |
| delattr()       | hash()       | memoryview()    | set()        |                 |



## 11 MongoDB

MongoDB 是目前最流行的 NoSQL 数据库之一，使用的数据类型 BSON（类似 JSON）。

MongoDB 数据库安装与介绍可以查看我们的 MongoDB 教程。

### 11.1 PyMongo

Python 要连接 MongoDB 需要 MongoDB 驱动，这里我们使用 PyMongo 驱动来连接。

#### 11.1.1 pip 安装
pip 是一个通用的 Python 包管理工具，提供了对 Python 包的查找、下载、安装、卸载的功能。

安装 pymongo:

```bash
$ python3 -m pip3 install pymongo
```

也可以指定安装的版本:

```bash
$ python3 -m pip3 install pymongo==3.5.1
```

更新 pymongo 命令：

```bash
$ python3 -m pip3 install --upgrade pymongo
```

#### 11.1.2 easy_install 安装

旧版的 Python 可以使用 easy_install 来安装，easy_install 也是 Python 包管理工具。

```bash
$ python -m easy_install pymongo
```

更新 pymongo 命令：

```bash
$ python -m easy_install -U pymongo
```

#### 11.1.3 测试 PyMongo

接下来我们可以创建一个测试文件 demo_test_mongodb.py，代码如下：

```python
# demo_test_mongodb.py

import pymongo
```

执行以上代码文件，如果没有出现错误，表示安装成功。


### 11.2 创建数据库

#### 11.2.1 创建一个数据库

创建数据库需要使用 MongoClient 对象，并且指定连接的 URL 地址和要创建的数据库名。

如下实例中，我们创建的数据库 amazondb :

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
```

`注意: 在 MongoDB 中，数据库只有在内容插入后才会创建! 就是说，数据库创建后要创建集合(数据表)并插入一个文档(记录)，数据库才会真正创建。`

#### 11.2.2 判断数据库是否已存在

我们可以读取 MongoDB 中的所有数据库，并判断指定的数据库是否存在：

```python 
import pymongo
 
myclient = pymongo.MongoClient('mongodb://localhost:27017/')
 
dblist = myclient.list_database_names()
# dblist = myclient.database_names() 
if "amazondb" in dblist:
  print("数据库已存在！")
```

`注意：database_names 在最新版本的 Python 中已废弃，Python3.7+ 之后的版本改为了 list_database_names()。`


### 11.3 创建集合

MongoDB 中的集合类似 SQL 的表。

#### 11.3.1 创建一个集合

MongoDB 使用数据库对象来创建集合，实例如下：

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
 
mycol = mydb["sites"]
```

`注意: 在 MongoDB 中，集合只有在内容插入后才会创建! 就是说，创建集合(数据表)后要再插入一个文档(记录)，集合才会真正创建。`

#### 11.3.2 判断集合是否已存在

我们可以读取 MongoDB 数据库中的所有集合，并判断指定的集合是否存在：

```python 
import pymongo
 
myclient = pymongo.MongoClient('mongodb://localhost:27017/')
 
mydb = myclient['amazondb']
 
collist = mydb. list_collection_names()
# collist = mydb.collection_names()
if "sites" in collist:   # 判断 sites 集合是否存在
  print("集合已存在！")
```

`注意：collection_names 在最新版本的 Python 中已废弃，Python3.7+ 之后的版本改为了 list_collection_names()。`


### 11.4 增、删、改、查等操作

#### 11.4.1 添加数据

MongoDB 中的一个文档类似 SQL 表中的一条记录。

**插入集合**

集合中插入文档使用 insert_one() 方法，该方法的第一参数是字典 name => value 对。

以下实例向 sites 集合中插入文档：

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
mydict = { "name": "AMAZON", "alexa": "10000", "url": "https://www.amazon.com" }
 
x = mycol.insert_one(mydict) 
print(x)
print(x)
```

执行输出结果为：

```bash
<pymongo.results.InsertOneResult object at 0x10a34b288>
```

**返回 _id 字段**

insert_one() 方法返回 InsertOneResult 对象，该对象包含 inserted_id 属性，它是插入文档的 id 值。

```python 
import pymongo
 
myclient = pymongo.MongoClient('mongodb://localhost:27017/')
mydb = myclient['amazondb']
mycol = mydb["sites"]
 
mydict = { "name": "Google", "alexa": "1", "url": "https://www.google.com" }
 
x = mycol.insert_one(mydict)
 
print(x.inserted_id)
```

执行输出结果为：

```bash
5b2369cac315325f3698a1cf
```

如果我们在插入文档时没有指定 _id，MongoDB 会为每个文档添加一个唯一的 id。

**插入多个文档**

集合中插入多个文档使用 insert_many() 方法，该方法的第一参数是字典列表。

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
mylist = [
  { "name": "Taobao", "alexa": "100", "url": "https://www.taobao.com" },
  { "name": "QQ", "alexa": "101", "url": "https://www.qq.com" },
  { "name": "Facebook", "alexa": "10", "url": "https://www.facebook.com" },
  { "name": "知乎", "alexa": "103", "url": "https://www.zhihu.com" },
  { "name": "Github", "alexa": "109", "url": "https://www.github.com" }
]
 
x = mycol.insert_many(mylist)
 
# 输出插入的所有文档对应的 _id 值
print(x.inserted_ids)
```

输出结果类似如下：

```bash
[ObjectId('5b236aa9c315325f5236bbb6'), ObjectId('5b236aa9c315325f5236bbb7'), ObjectId('5b236aa9c315325f5236bbb8'), ObjectId('5b236aa9c315325f5236bbb9'), ObjectId('5b236aa9c315325f5236bbba')]
```

*insert_many()* 方法返回 InsertManyResult 对象，该对象包含 inserted_ids 属性，该属性保存着所有插入文档的 id 值。

执行完以上查找，我们可以在命令终端，查看数据是否已插入:

```bash
> use amazondb
switched to db amazondb
> db.sites.find()
... ...
>
```

**插入指定 _id 的多个文档**

我们也可以自己指定 id，插入，以下实例我们在 site2 集合中插入数据，_id 为我们指定的：

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["site2"]
 
mylist = [
  { "_id": 1, "name": "AMAZON", "cn_name": "网上购物"},
  { "_id": 2, "name": "Google", "address": "Google 搜索"},
  { "_id": 3, "name": "Facebook", "address": "脸书"},
  { "_id": 4, "name": "Taobao", "address": "淘宝"},
  { "_id": 5, "name": "Zhihu", "address": "知乎"}
]
 
x = mycol.insert_many(mylist)
 
# 输出插入的所有文档对应的 _id 值
print(x.inserted_ids)
```

输出结果为：

```bash
[1, 2, 3, 4, 5]
```

执行完以上查找，

```bash
> db.site2.find()
```

我们可以在命令终端，查看数据是否已插入.

#### 11.4.2 查询数据

MongoDB 中使用了 find 和 find_one 方法来查询集合中的数据，它类似于 SQL 中的 SELECT 语句。

本文使用的测试数据如下：

```bash
> use amazondb
switched to db amazondb
> db.sites.find()
... ...
>
```

**查询一条数据**

我们可以使用 find_one() 方法来查询集合中的一条数据。

查询 sites 文档中的第一条数据：

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
x = mycol.find_one()
 
print(x)
```

输出结果为：

```bash
{'_id': ObjectId('5b23696ac315325f269f28d1'), 'name': 'AMAZON', 'alexa': '10000', 'url': 'https://www.amazon.com'}
```

**查询集合中所有数据**

find() 方法可以查询集合中的所有数据，类似 SQL 中的 SELECT * 操作。

以下实例查找 sites 集合中的所有数据：

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
for x in mycol.find():
  print(x)
```

输出结果为：

```bash
{'_id': ObjectId('5b23696ac315325f269f28d1'), 'name': 'AMAZON', 'alexa': '10000', 'url': 'https://www.amazon.com'}
{'_id': ObjectId('5b2369cac315325f3698a1cf'), 'name': 'Google', 'alexa': '1', 'url': 'https://www.google.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb6'), 'name': 'Taobao', 'alexa': '100', 'url': 'https://www.taobao.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb7'), 'name': 'QQ', 'alexa': '101', 'url': 'https://www.qq.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb8'), 'name': 'Facebook', 'alexa': '10', 'url': 'https://www.facebook.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb9'), 'name': '知乎', 'alexa': '103', 'url': 'https://www.zhihu.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbba'), 'name': 'Github', 'alexa': '109', 'url': 'https://www.github.com'}
```

**查询指定字段的数据**

我们可以使用 find() 方法来查询指定字段的数据，将要返回的字段对应值设置为 1。

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
for x in mycol.find({},{ "_id": 0, "name": 1, "alexa": 1 }):
  print(x)
```

输出结果为：

```bash
{'name': 'AMAZON', 'alexa': '10000'}
{'name': 'Google', 'alexa': '1'}
{'name': 'Taobao', 'alexa': '100'}
{'name': 'QQ', 'alexa': '101'}
{'name': 'Facebook', 'alexa': '10'}
{'name': '知乎', 'alexa': '103'}
{'name': 'Github', 'alexa': '109'}
```

除了 _id，你不能在一个对象中同时指定 0 和 1，如果你设置了一个字段为 0，则其他都为 1，反之亦然。

以下实例除了 alexa 字段外，其他都返回：

```python
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
for x in mycol.find({},{ "alexa": 0 }):
  print(x)
```

输出结果为：

```bash
{'_id': ObjectId('5b23696ac315325f269f28d1'), 'name': 'AMAZON', 'url': 'https://www.amazon.com'}
{'_id': ObjectId('5b2369cac315325f3698a1cf'), 'name': 'Google', 'url': 'https://www.google.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb6'), 'name': 'Taobao', 'url': 'https://www.taobao.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb7'), 'name': 'QQ', 'url': 'https://www.qq.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb8'), 'name': 'Facebook', 'url': 'https://www.facebook.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb9'), 'name': '知乎', 'url': 'https://www.zhihu.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbba'), 'name': 'Github', 'url': 'https://www.github.com'}
```

以下代码同时指定了 0 和 1 则会报错：

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
for x in mycol.find({},{ "name": 1, "alexa": 0 }):
  print(x)
```

错误内容大概如下：

```bash
pymongo.errors.OperationFailure: Projection cannot have a mix of inclusion and exclusion.
```

**根据指定条件查询**

我们可以在 find() 中设置参数来过滤数据。

以下实例查找 name 字段为 "AMAZON" 的数据：

```python
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
myquery = { "name": "AMAZON" }
 
mydoc = mycol.find(myquery)
 
for x in mydoc:
  print(x)
```

输出结果为：

```bash
{'_id': ObjectId('5b23696ac315325f269f28d1'), 'name': 'AMAZON', 'alexa': '10000', 'url': 'https://www.amazon.com'}
```

**高级查询**

查询的条件语句中，我们还可以使用修饰符。

以下实例用于读取 name 字段中第一个字母 ASCII 值大于 "H" 的数据，大于的修饰符条件为 {"$gt": "H"} :

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
myquery = { "name": { "$gt": "H" } }
 
mydoc = mycol.find(myquery)
 
for x in mydoc:
  print(x)
```

输出结果为：

```bash
{'_id': ObjectId('5b23696ac315325f269f28d1'), 'name': 'AMAZON', 'alexa': '10000', 'url': 'https://www.amazon.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb6'), 'name': 'Taobao', 'alexa': '100', 'url': 'https://www.taobao.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb7'), 'name': 'QQ', 'alexa': '101', 'url': 'https://www.qq.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb9'), 'name': '知乎', 'alexa': '103', 'url': 'https://www.zhihu.com'}
```

**使用正则表达式查询**

我们还可以使用正则表达式作为修饰符。

正则表达式修饰符只用于搜索字符串的字段。

以下实例用于读取 name 字段中第一个字母为 "R" 的数据，正则表达式修饰符条件为 {"$regex": "^R"} :

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
myquery = { "name": { "$regex": "^R" } }
 
mydoc = mycol.find(myquery)
 
for x in mydoc:
  print(x)
```

输出结果为：

```bash
{'_id': ObjectId('5b23696ac315325f269f28d1'), 'name': 'AMAZON', 'alexa': '10000', 'url': 'https://www.amazon.com'}
```

**返回指定条数记录**

如果我们要对查询结果设置指定条数的记录可以使用 limit() 方法，该方法只接受一个数字参数。

以下实例返回 3 条文档记录：

```python
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
myresult = mycol.find().limit(3)
 
# 输出结果
for x in myresult:
  print(x)
```

输出结果为：

```bash
{'_id': ObjectId('5b23696ac315325f269f28d1'), 'name': 'AMAZON', 'alexa': '10000', 'url': 'https://www.amazon.com'}
{'_id': ObjectId('5b2369cac315325f3698a1cf'), 'name': 'Google', 'alexa': '1', 'url': 'https://www.google.com'}
{'_id': ObjectId('5b236aa9c315325f5236bbb6'), 'name': 'Taobao', 'alexa': '100', 'url': 'https://www.taobao.com'}
```

#### 11.4.3 修改数据

我们可以在 MongoDB 中使用 update_one() 方法修改文档中的记录。该方法第一个参数为查询的条件，第二个参数为要修改的字段。

如果查找到的匹配数据多于一条，则只会修改第一条。

本文使用的测试数据如下：

```bash
> use amazondb
switched to db amazondb
> db.sites.find()
... ...
```

以下实例将 alexa 字段的值 10000 改为 12345:

```python
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
myquery = { "alexa": "10000" }
newvalues = { "$set": { "alexa": "12345" } }
 
mycol.update_one(myquery, newvalues)

# 输出修改后的  "sites"  集合
for x in mycol.find():
  print(x)
```

执行可以看到输出结果。

*update_one()* 方法只能修匹配到的第一条记录，如果要修改所有匹配到的记录，可以使用 update_many()。

以下实例将查找所有以 F 开头的 name 字段，并将匹配到所有记录的 alexa 字段修改为 123：

```python
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
myquery = { "name": { "$regex": "^F" } }
newvalues = { "$set": { "alexa": "123" } }
 
x = mycol.update_many(myquery, newvalues)
 
print(x.modified_count, "文档已修改")
```

输出结果为：

```bash
1 文档已修改
```

#### 11.4.4 数据排序

sort() 方法可以指定升序或降序排序。

sort() 方法第一个参数为要排序的字段，第二个字段指定排序规则，1 为升序，-1 为降序，默认为升序。

本文使用的测试数据如下：

```bash
> db.sites.find()
... ...
```

对字段 alexa 按升序排序：

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
mydoc = mycol.find().sort("alexa")
for x in mydoc:
  print(x)
```

运行可以看到输出结果。

对字段 alexa 按降序排序：

```python
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
mydoc = mycol.find().sort("alexa", -1)
 
for x in mydoc:
  print(x)
```

运行可以看到输出结果。

#### 11.4.5 删除数据

我们可以使用 delete_one() 方法来删除一个文档，该方法第一个参数为查询对象，指定要删除哪些数据。

本文使用的测试数据如下：

```bash
> db.sites.find()
... ...
```

以下实例删除 name 字段值为 "Taobao" 的文档：

```python
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
myquery = { "name": "Taobao" }
 
mycol.delete_one(myquery)
 
# 删除后输出
for x in mycol.find():
  print(x)
```

运行可以看到输出结果。

**删除多个文档**

我们可以使用 delete_many() 方法来删除多个文档，该方法第一个参数为查询对象，指定要删除哪些数据。

删除所有 name 字段中以 F 开头的文档:

```python 
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
myquery = { "name": {"$regex": "^F"} }
 
x = mycol.delete_many(myquery)
 
print(x.deleted_count, "个文档已删除")
```

输出结果为：

```bash
1 个文档已删除
```

**删除集合中的所有文档**

delete_many() 方法如果传入的是一个空的查询对象，则会删除集合中的所有文档：

```python
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
x = mycol.delete_many({})
 
print(x.deleted_count, "个文档已删除")
```

输出结果为：

```bash
5 个文档已删除
```

**删除集合**

我们可以使用 drop() 方法来删除一个集合。

以下实例删除了 customers 集合：

```python
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["amazondb"]
mycol = mydb["sites"]
 
mycol.drop()
```

如果删除成功 drop() 返回 true，如果删除失败(集合不存在)则返回 false。

我们使用以下命令在终端查看集合是否已删除：

```bash
> use amazondb
switched to db amazondb
> show tables;
```



## 12 urllib

Python urllib 库用于操作网页 URL，并对网页的内容进行抓取处理。

本文主要介绍 Python3 的 urllib。

urllib 包 包含以下几个模块：

* urllib.request - 打开和读取 URL。
* urllib.error - 包含 urllib.request 抛出的异常。
* urllib.parse - 解析 URL。
* urllib.robotparser - 解析 robots.txt 文件。

### 12.1 urllib.request

urllib.request 定义了一些打开 URL 的函数和类，包含授权验证、重定向、浏览器 cookies等。

urllib.request 可以模拟浏览器的一个请求发起过程。

我们可以使用 urllib.request 的 urlopen 方法来打开一个 URL，语法格式如下：

```python
urllib.request.urlopen(url, data=None, [timeout, ]*, cafile=None, capath=None, cadefault=False, context=None)
```

* url：url 地址。
* data：发送到服务器的其他数据对象，默认为 None。
* timeout：设置访问超时时间。
* cafile 和 capath：cafile 为 CA 证书， capath 为 CA 证书的路径，使用 HTTPS 需要用到。
* cadefault：已经被弃用。
* context：ssl.SSLContext类型，用来指定 SSL 设置。

实例如下：

```python
from urllib.request import urlopen

myURL = urlopen("https://www.amazon.com/")
print(myURL.read())
```

以上代码使用 urlopen 打开一个 URL，然后使用 read() 函数获取网页的 HTML 实体代码。

read() 是读取整个网页内容，我们可以指定读取的长度：

```python
from urllib.request import urlopen

myURL = urlopen("https://www.amazon.com/")
print(myURL.read(300))
```

除了 read() 函数外，还包含以下两个读取网页内容的函数：

* readline() - 读取文件的一行内容

```python
from urllib.request import urlopen

myURL = urlopen("https://www.amazon.com/")
print(myURL.readline()) #读取一行内容
```

* readlines() - 读取文件的全部内容，它会把读取的内容赋值给一个列表变量。

```python
from urllib.request import urlopen

myURL = urlopen("https://www.amazon.com/")
lines = myURL.readlines()
for line in lines:
    print(line) 
```

我们在对网页进行抓取时，经常需要判断网页是否可以正常访问，这里我们就可以使用 getcode() 函数获取网页状态码，返回 200 说明网页正常，返回 404 说明网页不存在:

```python
import urllib.request

myURL1 = urllib.request.urlopen("https://www.amazon.com/")
print(myURL1.getcode())   # 200

try:
    myURL2 = urllib.request.urlopen("https://www.amazon.com/no.html")
except urllib.error.HTTPError as e:
    if e.code == 404:
        print(404)   # 404
```

如果要将抓取的网页保存到本地，可以使用 Python3 File write() 方法 函数：

```python
from urllib.request import urlopen

myURL = urlopen("https://www.amazon.com/")
f = open("amazon_urllib_test.html", "wb")
content = myURL.read()  # 读取网页内容
f.write(content)
f.close()
```

执行以上代码，在本地就会生成一个 amazon_urllib_test.html 文件，里面包含了 https://www.amazon.com/ 网页的内容。

URL 的编码与解码可以使用 urllib.request.quote() 与 urllib.request.unquote() 方法：

```python
import urllib.request

encode_url = urllib.request.quote("https://www.amazon.com/")  # 编码
print(encode_url)

unencode_url = urllib.request.unquote(encode_url)    # 解码
print(unencode_url)
```

输出结果为：

```bash
https%3A//www.amazon.com/
https://www.amazon.com/
```


### 12.2 模拟头部信息

我们抓取网页一般需要对 headers（网页头信息）进行模拟，这时候需要使用到 urllib.request.Request 类：

```python
class urllib.request.Request(url, data=None, headers={}, origin_req_host=None, unverifiable=False, method=None)
```

* url：url 地址。
* data：发送到服务器的其他数据对象，默认为 None。
* headers：HTTP 请求的头部信息，字典格式。
* origin_req_host：请求的主机地址，IP 或域名。
* unverifiable：很少用整个参数，用于设置网页是否需要验证，默认是False。。
* method：请求方法， 如 GET、POST、DELETE、PUT等。

```php
# py3_urllib_test.php
import urllib.request
import urllib.parse

url = 'https://www.amazon.com/?s='  # 网上购物搜索页面
keyword = 'Python 教程'
key_code = urllib.request.quote(keyword)  # 对请求进行编码
url_all = url+key_code
header = {
    'User-Agent':'Mozilla/5.0 (X11; Fedora; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36'
}   #头部信息
request = urllib.request.Request(url_all,headers=header)
reponse = urllib.request.urlopen(request).read()

fh = open("./urllib_test_amazon_search.html","wb")    # 将文件写入到当前目录中
fh.write(reponse)
fh.close()
```

打开 urllib_test_amazon_search.html 文件（可以使用浏览器打开），即可看到效果。

表单 POST 传递数据，我们先创建一个表单，代码如下，我这里使用了 PHP 代码来获取表单的数据：

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>网上购物(amazon.com) urllib POST  测试</title>
</head>
<body>
<form action="" method="post" name="myForm">
    Name: <input type="text" name="name"><br>
    Tag: <input type="text" name="tag"><br>
    <input type="submit" value="提交">
</form>
<hr>
<?php
// 使用 PHP 来获取表单提交的数据，你可以换成其他的
if(isset($_POST['name']) && $_POST['tag'] ) {
   echo $_POST["name"] . ', ' . $_POST['tag'];
}
?>
</body>
</html>
```

```python
import urllib.request
import urllib.parse

url = 'https://www.amazon.com/try/py3/py3_urllib_test.php'  # 提交到表单页面
data = {'name':'AMAZON', 'tag' : '网上购物'}   # 提交数据
header = {
    'User-Agent':'Mozilla/5.0 (X11; Fedora; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36'
}   #头部信息
data = urllib.parse.urlencode(data).encode('utf8')  # 对参数进行编码，解码使用 urllib.parse.urldecode
request=urllib.request.Request(url, data, header)   # 请求处理
reponse=urllib.request.urlopen(request).read()      # 读取结果

fh = open("./urllib_test_post_amazon.html","wb")    # 将文件写入到当前目录中
fh.write(reponse)
fh.close()
```

打开 urllib_test_post_amazon.html 文件（可以使用浏览器打开），即可看到效果。


### 12.3 urllib.error

urllib.error 模块为 urllib.request 所引发的异常定义了异常类，基础异常类是 URLError。

urllib.error 包含了两个方法，URLError 和 HTTPError。

URLError 是 OSError 的一个子类，用于处理程序在遇到问题时会引发此异常（或其派生的异常），包含的属性 reason 为引发异常的原因。

HTTPError 是 URLError 的一个子类，用于处理特殊 HTTP 错误例如作为认证请求的时候，包含的属性 code 为 HTTP 的状态码， reason 为引发异常的原因，headers 为导致 HTTPError 的特定 HTTP 请求的 HTTP 响应头。

对不存在的网页抓取并处理异常:

```python
import urllib.request
import urllib.error

myURL1 = urllib.request.urlopen("https://www.amazon.com/")
print(myURL1.getcode())   # 200

try:
    myURL2 = urllib.request.urlopen("https://www.amazon.com/no.html")
except urllib.error.HTTPError as e:
    if e.code == 404:
        print(404)   # 404
```


### 12.4 urllib.parse

urllib.parse 用于解析 URL，格式如下：

```bash
urllib.parse.urlparse(urlstring, scheme='', allow_fragments=True)
```

urlstring 为 字符串的 url 地址，scheme 为协议类型，

allow_fragments 参数为 false，则无法识别片段标识符。相反，它们被解析为路径，参数或查询组件的一部分，并 fragment 在返回值中设置为空字符串。

```python
from urllib.parse import urlparse

o = urlparse("https://www.amazon.com/?s=python+%E6%95%99%E7%A8%8B")
print(o)
```

以上实例输出结果为：

```bash
ParseResult(scheme='https', netloc='www.amazon.com', path='/', params='', query='s=python+%E6%95%99%E7%A8%8B', fragment='')
```

从结果可以看出，内容是一个元组，包含 6 个字符串：协议，位置，路径，参数，查询，判断。

我们可以直接读取协议内容：

```python
from urllib.parse import urlparse

o = urlparse("https://www.amazon.com/?s=python+%E6%95%99%E7%A8%8B")
print(o.scheme)
```

以上实例输出结果为：

```bash
https
```

完整内容如下：

| 属性      | 索引 | 值                    | 值（如果不存在）  |
| --------- | --- | --------------------- | ---------------  |
| scheme    | 0   | URL协议                | scheme 参数      |
| netloc    | 1   | 网络位置部分           | 空字符串          |
| path      | 2   | 分层路径               | 空字符串          |
| params    | 3   | 最后路径元素的参数      | 空字符串          |
| query     | 4   | 查询组件               | 空字符串          |
| fragment  | 5   | 片段识别               | 空字符串          |
| username  |     | 用户名                 | None             |
| password  |     | 密码                   | None             |
| hostname  |     | 主机名(小写)            | None            |
| port      |     | 端口号为整数(如果存在)   | None            |


### 12.5 urllib.robotparser

urllib.robotparser 用于解析 robots.txt 文件。

robots.txt（统一小写）是一种存放于网站根目录下的 robots 协议，它通常用于告诉搜索引擎对网站的抓取规则。

urllib.robotparser 提供了 RobotFileParser 类，语法如下：

```python
class urllib.robotparser.RobotFileParser(url='')
```

这个类提供了一些可以读取、解析 robots.txt 文件的方法：

* set_url(url) - 设置 robots.txt 文件的 URL。

* read() - 读取 robots.txt URL 并将其输入解析器。

* parse(lines) - 解析行参数。

* can_fetch(useragent, url) - 如果允许 useragent 按照被解析 robots.txt 文件中的规则来获取 url 则返回 True。

* mtime() -返回最近一次获取 robots.txt 文件的时间。 这适用于需要定期检查 robots.txt 文件更新情况的长时间运行的网页爬虫。

* modified() - 将最近一次获取 robots.txt 文件的时间设置为当前时间。

* crawl_delay(useragent) -为指定的 useragent 从 robots.txt 返回 Crawl-delay 形参。 如果此形参不存在或不适用于指定的 useragent 或者此形参的 robots.txt 条目存在语法错误，则返回 None。

* request_rate(useragent) -以 named tuple RequestRate(requests, seconds) 的形式从 robots.txt 返回 Request-rate 形参的内容。 如果此形参不存在或不适用于指定的 useragent 或者此形参的 robots.txt 条目存在语法错误，则返回 None。

* site_maps() - 以 list() 的形式从 robots.txt 返回 Sitemap 形参的内容。 如果此形参不存在或者此形参的 robots.txt 条目存在语法错误，则返回 None。

```bash
>>> import urllib.robotparser
>>> rp = urllib.robotparser.RobotFileParser()
>>> rp.set_url("http://www.musi-cal.com/robots.txt")
>>> rp.read()
>>> rrate = rp.request_rate("*")
>>> rrate.requests
3
>>> rrate.seconds
20
>>> rp.crawl_delay("*")
6
>>> rp.can_fetch("*", "http://www.musi-cal.com/cgi-bin/search?city=San+Francisco")
False
>>> rp.can_fetch("*", "http://www.musi-cal.com/")
True
```


