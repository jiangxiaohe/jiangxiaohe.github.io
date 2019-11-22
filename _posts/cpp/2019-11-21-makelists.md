---
layout: post
title: cmakelists
tags:
categories: cpp
description: 用cmakelist工具来管理项目
---

# 编写CMakeLists.txt文件编译C/C++程序
* [例程](https://www.cnblogs.com/cv-pr/p/6206921.html)
* [例程](https://www.hahack.com/codes/cmake/#%E5%A4%9A%E4%B8%AA%E6%BA%90%E6%96%87%E4%BB%B6)
* [CMakeLists.txt 语法查询](https://blog.csdn.net/afei__/article/details/81201039)

https://www.cnblogs.com/cv-pr/p/6206921.html

CMake是一种跨平台编译工具，比make更为高级，使用起来要方便得多。CMake主要是编写CMakeLists.txt文件，然后用cmake命令将CMakeLists.txt文件转化为make所需要的makefile文件，最后用make命令编译源码生成可执行程序或共享库（so(shared object)）。因此CMake的编译基本就两个步骤：

```
cd build
cmake ..
make
```

## 语法详解
* option (USE_MYMATH "Use provided math implementation" ON)
option 命令添加了一个 USE_MYMATH 选项，并且默认值为 ON 。
* configure_file ("${PROJECT_SOURCE_DIR}/config.h.in" )
configure_file 命令用于加入一个配置头文件 config.h ，这个文件由 cmake 从 config.h.in 生成，通过这样的机制，将可以通过预定义一些参数和变量来控制代码的生成。

## 利用cmake编译文件demo
编写一个开放平的C++项目，即b=sqrt(a)，以此理解cmake编译的过程。

### 文件的目录结构与代码

```cpp
/**录结构

├── build
├── CMakeLists.txt
├── include
│   └── b.h
└── src
    ├── b.c
    └── main.c
**/


//头文件b.h
#ifndef B_FILE_HEADER_INC
#define B_FIEL_HEADER_INC

#include<math.h>

double cal_sqrt(double value);

#endif


//头文件b.c
#include "../include/b.h"

double cal_sqrt(double value){
    return sqrt(value);
}


//主函数main.c
#include "../include/b.h"
#include <stdio.h>
int main(int argc, char** argv){
    double a = 49.0;
    double b = 0.0;

    printf("input a:%f\n",a);
    b = cal_sqrt(a);
    printf("sqrt result:%f\n",b);
    return 0;
}
```

### 编写cmakelists.txt

接下来编写CMakeLists.txt文件，该文件放在和src，include的同级目录，实际方哪里都可以，只要里面编写的路径能够正确指向就好了。CMakeLists.txt文件，主要包含以上的7个步骤。

```
#1.cmake verson，指定cmake版本
cmake_minimum_required(VERSION 3.2)

#2.project name，指定项目的名称，一般和项目的文件夹名称对应
project(test_sqrt)

#3.head file path，头文件目录
include_dircetories(include)

#4.source directory，源文件目录
aux_source_directory(src DIR_SRCS)

#5.set environment variable，设置环境变量
SET(TEST_MATH ${DIR_SRCS})

#6.add executable file，添加要编译的可执行文件
add_executable(${PROJECT_NAME} ${TEST_MATH})

#7.add link library，添加可执行文件所需要的库，比如我们用到了libm.so（命名规则：lib+name+.so），就添加该库的名称
TARGET_LINK_LIBRARIES(${PROJECT_NAME} m)
```

### 编译和运行程序

备好了以上的所有材料，接下来，就可以编译了，由于编译中出现许多中间的文件，因此最好新建一个独立的目录build，在该目录下进行编译，编译步骤如下所示：

```
mkdir build
cd build
cmake ..
make
```

操作后，在build下生成的目录结构如下：

```
├── build
│   ├── CMakeCache.txt
│   ├── CMakeFiles
│   │   ├── 3.2.2
│   │   │   ├── CMakeCCompiler.cmake
│   │   │   ├── CMakeCXXCompiler.cmake
│   │   │   ├── CMakeDetermineCompilerABI_C.bin
│   │   │   ├── CMakeDetermineCompilerABI_CXX.bin
│   │   │   ├── CMakeSystem.cmake
│   │   │   ├── CompilerIdC
│   │   │   │   ├── a.out
│   │   │   │   └── CMakeCCompilerId.c
│   │   │   └── CompilerIdCXX
│   │   │       ├── a.out
│   │   │       └── CMakeCXXCompilerId.cpp
│   │   ├── cmake.check_cache
│   │   ├── CMakeDirectoryInformation.cmake
│   │   ├── CMakeOutput.log
│   │   ├── CMakeTmp
│   │   ├── feature_tests.bin
│   │   ├── feature_tests.c
│   │   ├── feature_tests.cxx
│   │   ├── Makefile2
│   │   ├── Makefile.cmake
│   │   ├── progress.marks
│   │   ├── TargetDirectories.txt
│   │   └── test_sqrt.dir
│   │       ├── build.make
│   │       ├── C.includecache
│   │       ├── cmake_clean.cmake
│   │       ├── DependInfo.cmake
│   │       ├── depend.internal
│   │       ├── depend.make
│   │       ├── flags.make
│   │       ├── link.txt
│   │       ├── progress.make
│   │       └── src
│   │           ├── b.c.o
│   │           └── main.c.o
│   ├── cmake_install.cmake
│   ├── Makefile
│   └── test_sqrt
├── CMakeLists.txt
├── include
│   └── b.h
└── src
    ├── b.c
    └── main.c
```

注意在build的目录下生成了一个可执行的文件test_sqrt,运行获取结果如下：

```
命令：
./test_sqrt
结果：
input a:49.000000
sqrt result:7.000000
```
