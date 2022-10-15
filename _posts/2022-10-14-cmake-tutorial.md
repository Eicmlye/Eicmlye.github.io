---
layout:         post
title:          "CMake 教程"
subtitle:   	
post-date:      2022-10-14
update-date:    2022-10-15
author:         "Eicmlye"
header-img:     "img/em-post/20221014-cmake.jpg"
catalog:        true
tags: # for multiple tags, tabs should be replaced by spaces before '-';
    - 教程
    - CMake
---

### 0. 基本术语

configure 配置, 通过预配置文件产生完整的配置文件;
generate 生成, 通过配置文件生成工程文件链接树;
compile 编译, build 链接, project 工程, build tree 链接树. 

### 1. 基本结构

#### 1.1. 基本语法

函数名称不区分大小写, 但建议全大写. 变量名称区分大小写, 本文中内置变量均以全大写表示. 

```cmake
FUNCTION(VARIABLE <replacable> [optional])
```

命令结尾不需要分号. 用 `${VARIABLE}` 表示对变量 `VARIABLE` 取值. 多个参数之间用空格分隔, 路径或文件名用双引号括起. 

#### 1.2. 基本命令

##### 1.2.1. CMake 版本要求

```cmake
CMAKE_MINIMUM_REQUIRED(VERSION version_tag)
```

注意这里 `VERSION` 是内置变量名, 必须全大写. 只有当前 CMake 版本不低于 `${version_tag}` 时才会继续构建. 

##### 1.2.2. 工程变量

函数

```cmake
PROJECT(<project_name> [C] [CXX] [Java])
```

将[定义一系列内置变量](https://gitlab.kitware.com/cmake/community/-/wikis/doc/cmake/Useful-Variables): 
- 定义当前 `CMakeLists.txt` 所在目录 `../<project_name>/` 为工程目录;
- 定义变量 `PROJECT_NAME` 的值为 `<project_name>`;
- 定义变量 `PROJECT_SOURCE_DIR` 的值为 `"../<project_name>/"`;
- 声明变量 `PROJECT_BINARY_DIR` , 并在生成时将其值定义为链接树的根节点目录;
- 定义变量 `CMAKE_CURRENT_SOURCE_DIR` 的值为当前处理的 CMakeLists.txt 文件所在的目录;
- 定义变量 `CMAKE_CURRENT_BINARY_DIR` 的值为 `${CMAKE_CURRENT_SOURCE_DIR}` 中的源文件编译或链接产生的文件要存放到的目录, 其相对 `4{PROJECT_BINARY_DIR}` 的路径与 `${CMAKE_CURRENT_SOURCE_DIR}` 相对 `${PROJECT_SOURCE_DIR}` 的路径相同.

可选参数定义工程所用的语言, 缺省时默认选择所有可能的语言. 

##### 1.2.3. 生成工程文件

函数

```cmake
ADD_EXECUTABLE(<exefile_name> ${<src_list>})
```

定义可执行项目文件名变量 `<exefile_name>` , 生成的工程文件（.vcsproj）与该变量同名, 并链接变量 `src_list` 中的所有源文件. 

函数

```cmake
ADD_LIBRARY(<archive_name> ${<archive_src_list>})
```

定义静态库名变量 `<archive_name>` , 生成的静态库工程文件与该变量同名, 并链接变量 `<archive_src_list>` 中的所有源文件. 

##### 1.2.4. 定义变量

函数

```cmake
SET(<variable_name> [<value_list>])
## SET(SRC_LIST "main.cpp" "func.cpp")
## SET(SRC_LIST "main.cpp";"func.cpp")
## SET(SRC_LIST ${SRC_LIST} "func.cpp")
```

定义变量 `<variable_name>` 的值为 `<value_list>` , 允许新变量自指, 值列表元素可以用空格或分号分隔. 

函数

```cmake
AUX_SOURCE_DIRECTORY(<src_dir> <variable_name>)
```

将目录 `<src_dir>` 中的所有源文件的名列表赋值给变量 `<variable_name>` .

##### 1.2.5. 显示命令行提示信息

```cmake
MESSAGE([SEND_ERROR | STATUS | FATAL_ERROR] MESSAGE)
# Sending customized messages to console;
# SEND_ERROR: produce an error and skip the generation process;
# STATUS: print "-- MESSAGE" to console;
# FATAL_ERROR: terminate all cmake process at once;
## MESSAGE(STATUS "This is BINARY dir: " ${PROJECT_BINARY_DIR})
```
