
# Shell base tutorial

在计算机科学中，Shell俗称壳(用来区别于核)，是指“为使用者提供操作界面”的软件，这里我们是指shell脚本基础编程。


# 1.概述

我们通过cat /etc/shells可以查看系统中存在的shell程序:

```bash
$ cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/usr/bin/sh
/bin/dash
/usr/bin/dash
```

一般Redhat系默认使用的是bash，Debian系默认使用的是dash。我们可以通过env命令或者printenv命令查看全局变量，使用set命令可查看当前shell的变量。

下面是一个简单的shell脚本文件，hello.sh(编写脚本文件一般都加后缀名".sh"，当然不加也可以用，最好是加后缀，增加辨识性):

```bash
#!/bin/bash

echo "hello world."
```

其中"#!"叫做shebang，第一行表示使用"/bin/bash"运行此文本，当然也可以用其它的shell程序，比如dash。运行此脚本文件，会输出"hello world."。


# 2.执行方式

执行脚本文件的常见方式有4种:

1. 指定shell程序

```bash
# 使用相对路径
$ bash ./hello.sh

# 或者使用绝对路径
$ bash /home/hello.sh
```

2. 增加执行权限

```bash
# 增加可执行权限
$ chmod +x hello.sh

# 使用相对路径执行
$ ./hello.sh

# 或者，使用绝对路径执行
$ /home/hello.sh
```

3. 使用"."执行

```bash
# 此例忽略可执行权限有无
$ . hello.sh

# 或者绝对路径
$ . /home/hello.sh
```

4. 使用"source"执行

```bash
# 此例忽略可执行权限有无
$ source hello.sh

# 或者绝对路径
$ source /home/hello.sh
```

其中1、2是在创建的子shell中执行，3、4是在当前shell中直接执行。


# 3.变量

## 3.1 系统预定义变量

1. $HOME、$PWD、$SHELL、$USER,查看变量:例echo $HOME;
2. 显示当前shell中所有变量 set
3. 显示全局变量: env或printenv


## 3.2 自定义变量

一、基本语法：

1. 定义变量

```bash
变量名=变量
```

2. 撤销变量

```bash
unset 变量名
```

3. 声明静态变量

```bash
readonly 变量，注意:不能unset
```

4.声明全局变量

```bash
export 变量,子shell中修改变量值不会影响当前shell中的值.
```

二、变量定义规则:

1. 变量名称可以有字母、数字和下划线组成，但是不能以数字开头，环境变量名建议大写;
2. 等号两侧不能有空格;
3. 在Bash中,变量默认类型都是字符串型，无法直接进行数值运算;
4. 变量的值如果有空格，需要使用双引号或单引号括起来.


## 3.3 特殊变量

```bash
$n
```

n为数字,$0代表脚本本身,$1-$9代表第1-9个参数，10以上需用大括号,如${10};

```bash
$#
```

获取所有输入参数的个数;

```bash
$*、$@
```

$*,代表命令行中所有的参数,并把所有参数看成一个整体;
$@,代表命令行中所有的参数,并每个参数区分对待;

```bash
$?
```

最后一次执行命令的返回状态,0代表正确执行,非0代表错误执行.


## 3.4 读取数据

`read (选项) (参数)`

1. 选项

-p: 指定读取值时的提示符;
-t: 指定读取值时的等待时间(秒),如果不加此项,表示一直等待;

2. 参数

变量: 指定读取值的变量名;

例：

```bash
#!/bin/bash

read -t 10 -p "Input number:" num
echo $num
```


# 4.运算符

$((运算式))或$[运算式]

例：

```bash
a=$[(1 + 2) * 3],
a=$(expr 4 \* 5),
a=`expr 6 + 7`
a=$((5 + 1))
for ((i = 0; i < 10; i++))
while [ $i -le 100 ]
while (($i < 100))
```


# 5.条件判断

一、基本语法：

test condition或[ condition ],二者等价,判断condition是否为真,注意condition前后都有空格;

二、常用判断条件

1. 整数之间比较

    - -eq 等于(equal)
    - -ne 不等于(not equal)
    - -lt 小于(less than)
    - -le 小于等于(less equal)
    - -gt 大于(greater than)
    - -ge 大于等于(greater equal)

注:如果是字符串之间的比较,使用"="判断相等,使用"!="判断不相等;

2. 文件权限判断

    - -r 有读权限(read)
    - -w 有写权限(write)
    - -x 有执行权限(execute)

3. 文件类型判断

    - -e 文件存在(existence)
    - -f 文件存在并且是一个常规文件(file)
    - -d 文件存在并且是一个目录(directory)

4. 多条件判断

    - && 表示前一条命令成功后,再执行后一条命令;
    - || 表示前一条命令失败后,才执行后一条命令;
    - -a and,逻辑与;
    - -o or,逻辑或;


# 6.流程控制

## 6.1 if

1. 单分支

```bash
if [ 条件表达式 ]; then
    程序
fi
```

或

```bash
if [ 条件表达式 ]
then
    程序
fi
```

2. 多分支

```bash
if [ 条件表达式 ]; then
    程序
elif [ 条件表达式 ]; then
    程序
else
    程序
fi
```

例(注意判断式中括号中的空格)：

```bash
#!/bin/bash

i=9

if [ $i -lt 3 ]; then
    echo "Not OK"
elif [ $i -le 6 ]; then
    echo "Normal"
else
    echo "Good"
fi
```


## 6.2 case

1. 写法

```bash
case $变量名 in
"值1")
    如果变量值等于值1,则执行程序1
;;
"值2")
    如果变量值等于值2,则执行程序2
;;
    ...省略其它分支...
*)
    如果变量值不等于上面任一值,则执行此程序
;;
esac
```

2. 注意事项

    - case行尾必须为单词"in",每个分支匹配必须以")"结束;
    - ";;"表示命令序列结束;
    - 最后的"*)",表示默认模式.

例:

```bash
#!/bin/bash

case $1 in
1)
    echo "one"
;;
2)
    echo "two"
;;
3)
    echo "three"
;;
*)
    echo "other"
;;
esac
```


## 6.3 for

1. 基本语法1：

```bash
for ((初始值; 循环控制条件; 变量变化))
do
    程序
done
```

例：

```bash
#!/bin/bash

sum=0

for ((i = 1; i <= 100; i++))
do
    sum=$[$sum + $i]
done

echo $sum
```

2. 基本语法2：

```bash
for 变量 in 值1 值2 值3...
do
    程序
done
```

例1:

```bash
#!/bin/bash

sum=0

for i in {1..100}
do
    sum=$[$sum + $i]
done

echo $sum
```

例2：

```bash
#!/bin/bash

for i in 1 2 3 4
do
    echo $i
done
```


## 6.4 while

(注意判断式中括号中的空格)

```bash
while [ 条件表达式 ]
do
    程序
done
```

例1：

```bash
#!/bin/bash

i=1
sum=0

while [ $i -le 100 ]
do
    sum=$[ $sum + $i ]
    i=$[ $i + 1 ]
done

echo $sum
```

例2：

```bash
#!/bin/bash

i=1
sum=0

while [ $i -le 100 ]
do
    let sum+=i
    let i++
done

echo $sum
```


# 7.函数

## 7.1 系统函数

1. basename

截取字符串最后一级名称。

```bash
basename [string/pathname] [suffix]
```

- string/pathname: 字符串/路径名;
- suffix: 后缀名，如果指定此项则会去除字符串与之相同的后缀名。

例：

```bash
$ basename /home/hello.sh .sh
hello
```

2. dirname

截取字符串的路径名称。

```bash
dirname [string/pathname]
```

- string/pathname: 字符串/路径名;

例：

```bash
$ dirname /home/hello.sh
/home
```

3. expr

数学计算。

```bash
expr [数学式]
```

例:

```bash
$ expr 7 + 8
15
```


## 7.2 自定义函数

```bash
[function] funname[()]
{
    Action;
    [return int;]
}
```

上述总"[]"表示里面内容可省略不写。函数应在调用之前声明。

- function： 声明函数，可以省略，后面的括号相同;
- funname： 函数名称；
- return： 显式加上返回return值，不显示加，返回最后一条命令执行的情况。

例：

```bash
#!/bin/bash

function add()
{
    s=0
    s=$[$1 + $2]
    echo $s
}

read -p "Input 2 number:" a b

sum=0
sum=$(add $a $b)

echo "a + b = "$sum
```


# 8.正则表达式

正则表达式使用单个字符串来描述、匹配一系列符合某个语法规则的字符串。
在很多文本编辑器里，正则表达式通常被用来检索、替换哪些符合某个模式的文本。
在Linux中，grep、sed、awk等文本处理工具都支持通过正则表达式进行模式匹配。


## 8.1 常规匹配

```bash
$ cat /etc/shells | grep bash
```

匹配所有包含bash的行。


## 8.2 常用特殊字符

1. 特殊字符"^"

```bash
$ cat /etc/shells | grep ^b
```

匹配所有以b开头的行。

2. 特殊字符"$"

```bash
$ cat /etc/shells | grep h$
```

匹配所有以h结尾的行。

3. 特殊字符"."

```bash
$ cat /etc/shells | grep b..h
```

匹配所有bxxh的行，其中xx表示两个任意字符。

4. 特殊字符"*"

```bash
$ cat /etc/shells | grep b*sh
```

匹配所有b*sh的行，其中*代表任意多个相同字符。

5. 字符区间"[]"

匹配某个范围内的一个字符。

- [7,9] ---- 匹配7或9;
- [0-9] ---- 匹配0-9中的任意1个数字;
- [0-9]* ---- 匹配任意长度的数字字符串;
- [a-z] ---- 匹配a-z中的任意1个字母;
- [a-z]* ---- 匹配任意长度的a-z中字符的字符串;
- [a-c,e-h] ---- 匹配a-c或e-h之间任意1个字符;

```bash
$ cat /etc/shells | grep [b,d]ash
```

匹配包含bash或dash的字符串。

6. 转义字符"\"

当我们需要匹配特殊字符时，例如$，就需要使用转义字符，"\$"，这样就可以正常匹配$了。

```bash
$ echo "adfvda$hwegwethk" | grep 'a\$h'
```

匹配包含a$h的字符串。

7. 正则表达式扩展

例(匹配中国11位手机号)：

```bash
$ echo "18912345678" | grep -E ^1[3456789][0-9]{9}$
```

"{9}"代表匹配9次[0-9].


# 9.文本处理

## 9.1 cut

负责剪切文件中的数据。

```bash
cut [选项参数] filename
```

选项参数:

- -f ---- 列号，提取第几列
- -d ---- 分隔符，按照指定的分隔符分隔列，默认是制表符"\t"
- -c ---- 按字符进行切割，后加n代表取第几列，如-c 2

例：

```bash
$ cut -d " " -f 1 hello.sh
```


## 9.2 awk

把文件逐行读入，以空格为默认分隔符将每行切片，切开的部分再进行分析处理。

```bash
awk [选项参数] '/pattern1/{action1} /pattern2/{action2}...' filename
```

- pattern ---- 表示awk在数据中查找的内容，就是匹配模式;
- action ---- 在找到匹配内容时所执行的一系列命令;

选项参数

- -F ---- 指定输入文件分隔符;
- -v ---- 赋值一个用户定义变量;

例：

```bash
$ cat /etc/passwd | awk -F : '/^root/{print $7}'
```
