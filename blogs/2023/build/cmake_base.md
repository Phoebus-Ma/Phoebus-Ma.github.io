
# CMake base tutorial

CMake是一个跨平台的安装（编译）工具，可以用简单的语句来描述所有平台的安装(编译过程)。Cmake 并不直接建构出最终的软件，而是产生标准的建构档（如 Unix 的 Makefile 或 Windows Visual C++ 的 projects/workspaces），然后再依一般的建构方式使用。


# 1.安装

cmake下载地址:[https://cmake.org/download/]

本文使用的是Ubuntu Linux环境，通过apt安装cmake：

```bash
$ sudo apt update
$ sudo apt install cmake
```


# 2.构建和运行

构建如下文件：

```bash
.
├── build
└── src
    ├── CMakeLists.txt
    └── main.c
```

main.c

```c
#include <stdio.h>


int main(void)
{
    printf("hello world.\n");

    return 0;
}
```

CMakeLists.txt

```makefile
# 运行此文件的cmake最低版本
cmake_minimum_required(VERSION 3.10)

# 设置项目名Tutorial
project(Tutorial)

# 增加可执行文件Tutorial，由如下文件关联:main.c
add_executable(Tutorial main.c)
```

注：CMakeLists.txt中的关键字大小写均可，这里默认使用小写。

有两种构建方式(二选一，推荐第二种):

1. 内部构建

```bash
$ cd ./src
$ cmake .
```

2. 外部构建

```bash
$ cd ./build
$ cmake ../src/
```

无论是内部构建还是外部构建，都会生成一系列文件，由于平台是Linux，所以会生成Makefile文件。

3. 编译

一般使用如下方式：

```bash
$ cmake --build .
```

因为是在Linux环境下，所以也可以使用make编译，如下：

```bash
$ make
```

4. 运行

本例编译后生成叫"Tutorial"的可执行文件，直接运行：

```bash
$ ./Tutorial
```


# 3.说明

1. cmake命令不区分大小写，但是参数、变量区分大小写;
2. 参数用空格或分号隔开;
3. 使用${VAR}引用变量;
4. 引号可加可不加，但如果字符串中有空格则要加引号;


# 4.概念

1. 目标文件(target): 可执行文件(add_executable)、库文件(add_library);
2. 命令(cmake-command):用来设置数据的命令;
3. 变量(cmake-variable):以"CMAKE_"开头的变量名;
4. 属性(cmake-properties):文件/文件夹都有各自的属性;


# 5.命令

## 5.1 cmake_minimum_required

```bash
# 设置最低cmake版本
cmake_minimum_required(VERSION <min>)
```

例：

```makefile
cmake_minimum_required(VERSION 3.10)
```


## 5.2 project

```bash
# 设置项目名
project(<PROJECT-NAME> [<language-name>...])
project(<PROJECT-NAME>
        [VERSION <major>[.minor[.<patch>[.<tweak>]]]]
        [DESCRIPTION <project-description-string>]
        [HOMEPAGE_URL <url-string>]
        [LANGUAGES <language-name>...])

# 项目名会被存储在变量PROJECT_NAME和CMAKE_PROJECT_NAME中
# PROJECT_SOURCE_DIR等价于<PROJECT-NAME>_SOURCE_DIR
# PROJECT_BINARY_DIR等价于<PROJECT-NAME>_BINARY_DIR

# 如果定义了版本号
# 版本号被保存在PROJECT_VERSION和<PROJECT-NAME>_VERSION中
# 主版本号被保存在PROJECT_VERSION_MAJOR和<PROJECT-NAME>_VERSION_MAJOR中
# 次版本号被保存在PROJECT_VERSION_MINOR和<PROJECT-NAME>_VErSION_MINOR中
```

例：

```makefile
project(Tutorial)
project(Tutorial C CXX)
project(Tutorial VERSION 2.3 LANGUAGES CXX)
```


## 5.3 add_executable

```bash
# 用指定的源文件为项目添加可执行文件
add_executable(<name> [WIN32] [MACOSX_BUNDLE]
                [EXCLUDE_FROM_ALL]
                [sourcel] [source2] ...)

# <name>即生成可执行文件的名字(与项目名没有关系),在一个项目中必须唯一
```

例：

```makefile
add_executable(Tutorial tutorial.cxx)
```


## 5.4 message

```bash
# 打印信息
message([<mode>] "message text" ...)

# STATUS 前缀为"--"的信息
# SEND_ERROR产生错误，跳过生成过程
# FATAL_ERROR产生错误，终止运行
```

例：

```makefile
message(STATUS "${PROJECT_VERSION_MAJOR}")
```


## 5.5 set

```bash
# 将变量设置为指定值
set(<variable> <value>)
```

例：

```makefile
# 设置C++标准
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REOUIRED True)

# 设置输出文件位置
# 设置运行时目标(exe、dll、so)的输出位置
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# 设置存档目标文件(lib、a)的输出位置
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
```


## 5.6 option

```bash
# 定义一个开关
option(<variable> "<help_text>" [value])

# value的值为ON或OFF，默认为OFF
# option的值可以通过命令行改变 -D <variable>=ON/OFF
```

例：

```makefile
option(VERSION_ENABLE "output version" ON)
```


## 5.7 configure_file

```makefile
# 将输入文件进行替换并生成输出文件
configure_file(<input> <output>)

# 输入文件中形如@VAR@或${VAR}的字符串会被替换为这些变量的当前值，如果未定义则被替换为空字符串
```

```bash
#cmakedefine VAR ...
// 会被替换为以下两行之一，取决于VAR是否被设置
1.被设置会替换成:#define VAR ...
2.没有被设置替换成:/* #undef VAR */
```

例：

目录树

```bash
.
├── build
└── src
    ├── CMakeLists.txt
    ├── config.h.in
    └── main.c
```

main.c

```c
#include <stdio.h>
#include "config.h"

int main(void)
{
    printf("hello world.\n");
    printf("Version %d.%d.\n", PROJECT_VERSION_MAJOR, PROJECT_VERSION_MINOR);

    return 0;
}
```

config.h.in

```makefile
#cmakedefine PROJECT_VERSION_MAJOR @PROJECT_VERSION_MAJOR@
#cmakedefine PROJECT_VERSION_MINOR @PROJECT_VERSION_MINOR@
```

CMakeLists.txt

```makefile
# 设置运行此文件的最低cmake版本
cmake_minimum_required(VERSION 3.10)

# 设置项目名和版本号
# 版本1.2，其中1是Major号，2是Minor号
# Major号有效，会设置PROJECT_VERSION_MAJOR
# Minor号有效，会设置PROJECT_VERSION_MINOR
project(Tutorial VERSION 1.2)

# 将输入文件进行替换并生成输出文件
# 由于设置了版本号1.2，在输出文件config.h中
# 会定义并替换Major和Minor真实的数字
configure_file(config.h.in config.h)

# 增加可执行文件，关联main.c
add_executable(Tutorial main.c)

# 指定目标的头文件路径
# 用来指明本例生成的config.h会在目标路径中
target_include_directories(Tutorial PUBLIC "${PROJECT_BINARY_DIR}")
```

运行方式

```bash
$ cd build
$ cmake ../src/
$ cmake --build .
$ ./Tutorial
hello world.
Version 1.2.
```

生成的config.h的内容如下：

```c
#define PROJECT_VERSION_MAJOR 1
#define PROJECT_VERSION_MINOR 2
```


## 5.8 include_directories

```bash
# 指定所有目标的头文件路径
include_directories(dir1 [dir2 ...])
# 目录会被添加到当前文件的INCLUDE_DIRECTORIES属性中
# 当前文件的每一个目标文件的INCLUDE_DIRECTORIES属性中也会田间该目录
```

例：

```makefile
include_directories(${PROJECT_BINARY_DIR})
```


## 5.9 target_include_directories

```bash
# 指定目标的头文件路径
target_include_directories(<target>
                            <INTERFACE|PUBLIC|PRIVATE> [items1...]
                            [<INTERFACE|PUBLIC|PRIVATE> [items2] ...])
```

例：

```makefile
target_include_directories(Tutorial PUBLIC "${PROJECT_BINARY_DIR}")
```


## 5.10 add_subdirectory

```makefile
# 添加源文件目录
add_subdirectory(source_dir [binary_dir] [EXCLUDE_FORM_ALL])

# binary_dir 指定编译结果存放的位置
```

目录树

```bash
.
├── build
└── src
    ├── CMakeLists.txt
    ├── calc
    │   ├── CMakeLists.txt
    │   ├── add.c
    │   ├── add.h
    │   ├── sub.c
    │   └── sub.h
    ├── config.h.in
    └── main.c
```

例(src/CMakeLists.txt)：

```makefile
add_subdirectory(calc)
```


# 6.库

## 6.1 add_library

```bash
# 用指定的原为见生成库
add_library(<name> [STATIC | SHARED | MODULE]
            [EXCLUDE_FORM_ALL]
            [<source>...])
# STATIC 静态库
# SHARED 动态库
# 生成的库文件名为lib<name>.xxx
```


## 6.2 target_link_libraries

```bash
# 为目标链接库
target_link_libraries(<target>
                    <PRIVATE|PUBLIC|INTERFACE> <item>...
                    [<PRIVATE|PUBLIC|INTERFACE> <item>...]...)
# item可以是target名，绝对路径(必须保证文件存在)
```


## 6.3 区分

```makefile
# 头文件目录
include_directories()
target_include_directories()

# 链接时库目录
link_directories()
target_link_directories()

# 链接库
link_libraries()
target_link_libraries()

# 默认推荐使用以"target_"开头的关键字，因为不带"target_"的关键字是全局包含、向下传递的
```

例：

目录树

```bash
.
├── build
└── src
    ├── CMakeLists.txt
    ├── calc
    │   ├── CMakeLists.txt
    │   ├── add.c
    │   ├── add.h
    │   ├── sub.c
    │   └── sub.h
    ├── config.h.in
    └── main.c
```

src/main.c

```c
#include <stdio.h>

#include "config.h"
#include "add.h"
#include "sub.h"


int main(void)
{
    printf("hello world.\n");
    printf("Version %d.%d.\n", PROJECT_VERSION_MAJOR, PROJECT_VERSION_MINOR);
    printf("3 + 2 = %d.\n", add(3, 2));
    printf("3 - 2 = %d.\n", sub(3, 2));

    return 0;
}
```

src/config.h.in

```makefile
#cmakedefine PROJECT_VERSION_MAJOR @PROJECT_VERSION_MAJOR@
#cmakedefine PROJECT_VERSION_MINOR @PROJECT_VERSION_MINOR@
```

src/CMakeLists.txt

```makefile
# 运行此文件的最低cmake版本
cmake_minimum_required(VERSION 3.10)

# 可执行文件存放位置
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# 动态库存放位置
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# 静态库存放位置
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# 设置项目名
project(Tutorial VERSION 1.2)

# 替换并输出config.h
configure_file(config.h.in config.h)

# 增加子目录
add_subdirectory(calc)

# 添加可执行文件，关联main.c
add_executable(Tutorial main.c)

# 链接目标库
target_link_libraries(Tutorial PUBLIC addition)
target_link_libraries(Tutorial PUBLIC subtract)

# 添加搜索头文件路径的位置
target_include_directories(Tutorial PUBLIC
                        ${PROJECT_BINARY_DIR}
                        ${PROJECT_SOURCE_DIR}/calc
                        )
```

src/calc/add.h

```c
#ifndef __ADD_H__
#define __ADD_H__


int add(int a, int b);

#endif /* __ADD_H__ */
```

src/calc/add.c

```c
#include "add.h"

int add(int a, int b)
{
    return a + b;
}
```

src/calc/sub.h

```c
#ifndef __SUB_H__
#define __SUB_H__


int sub(int a, int b);

#endif /* __SUB_H__ */
```

src/calc/sub.c

```c
#include "sub.h"


int sub(int a, int b)
{
    return a - b;
}
```

src/calc/CMakeLists.txt

```makefile
# 生成名为addition的静态库，关联add.c
# Linux下为libaddition.a
add_library(addition STATIC add.c)

# 生成名为subtract的动态库，关联sub.c
# Linux下为libsubtract.so
add_library(subtract SHARED sub.c)
```

运行测试

```bash
$ cd build
$ cmake ../src/
$ cmake --build .
$ ./bin/Tutorial
hello world.
Version 1.2.
3 + 2 = 5.
3 - 2 = 1.
```


# 7.安装

```makefile
# 安装目标
install(TARGETS <target> DESTINATION <dir>)
# 安装文件
install(FILES <file> DESTINATION <dir>)
# 安装非目标文件的可执行程序，如脚本
install(PROGRAMS <非目标文件的可执行程序> DESTINATION <dir>)
# 安装目录
install(DIRECTORY <dir> DESTINATION <dir>)
```

例：

```makefile
# CMakeLists.txt
install(TARGETS Tutorial DESTINATION lib)
install(FILES config.h DESTINATION include)
install(DIRECTORY docs/ DESTINATION doc)
```

安装命令

```bash
# 安装到默认目录CMAKE_INSTALL_PREFIX
$ cmake --install .

# 安装到指定目录
$ cmake --install . --prefix <dir>
```
