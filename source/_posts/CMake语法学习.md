---
title: CMake语法学习
date: 2026-05-25 00:43:48
categories:
    - CMake
tags: 
    - CMake
    - C++
---

CMake语法学习

<!-- more -->

# CMake语法

## 基础命令

### 生成编译配置

指定在build目录生成编译配置
```shell
cmake -B build
```

### 编译

指定编译目录
```shell
cmake --build build
```

### 运行cmake脚本

脚本模式运行，用于测试
```shell
cmake -P TestCMake.cmake
```

## 基础语法

### 变量

定义变量，变量命名一般是全大写加下划线分割
```cmake
set(VAR_NAME "varName") 
```

定义缓存变量，跨文件可见

```cmake
set(VAR_NAME "varName" CACHE STRING "注释说明") 
```

变量赋值

```cmake
set(VAR_NAME_TMP ${VAR_NAME}) 
```

变量清空

```cmake
set(VAR_NAME “”) 
```

变量取消定义

```cmake
unset(VAR_NAME)
```

缓存变量取消定义

```cmake
unset(VAR_NAME CACHE)
```

### if语句

条件判断

```cmake
if(${RESULT} EQUAL 1)
elseif(${RESULT} EQUAL 2)
else()
endif()
```

大于判断

```cmake
if(2 GREATER 1)
endif()
```

等于判断

```cmake
if(1 EQUAL 1)
endif()
```

小于判断

```cmake
if(1 LESS 2)
endif()
```

大于等于判断

```cmake
if(2 GREATER_EQUAL 2)
endif()
```

小于等于判断

```cmake
if(1 LESS_EQUAL 2)
endif()
```

多重判断

```cmake
if(1 LESS_EQUAL 2 AND 2 LESS_EQUAL 2)
endif()

if(1 LESS_EQUAL 2 OR 3 LESS_EQUAL 2)
endif()

if(NOT 3 LESS_EQUAL 2)
endif()
```

字符串判断

```cmake
if(STRING_VAR STREQUAL "value") # 字符串相等
if(STRING_VAR MATCHES "pattern") # 字符串匹配正则
```

文件目录判断

```cmake
if(EXISTS "/path/to/file")      # 文件/目录存在
if(IS_DIRECTORY "/path/to/dir") # 是目录
```


### foreach语句

普通遍历

```cmake
set(ITEMS "A" "B" "C")
foreach(ITEM ${ITEMS})
    message("Item: ${ITEM}")
endforeach()    
```

纯列表遍历

```cmake
set(LIST_ITEMS "A" "B" "C")
foreach(ITEM IN LISTS LIST_ITEMS)
    message("Item: ${ITEM}")
endforeach()    
```

循环范围

```cmake
foreach(i RANGE 5)
    message("i: ${i}")
endforeach()      
```

### while语句

while循环

```cmake
set(MAX_RETRY 3)
while(MAX_RETRY GREATER 0)
    message("Try once: ${MAX_RETRY}")
    math(EXPR MAX_RETRY "${MAX_RETRY} - 1")
endwhile() 
```

### 函数

函数定义

```cmake
# 定义函数
function(my_function arg1 arg2)
    message("Argument 1: ${arg1}")
    message("Argument 2: ${arg2}")
endfunction()   

# 函数调用
my_function("Hello" "World")
```

带返回值函数定义

```cmake
function(get_sum arg1 arg2 result)
    math(EXPR tmp "${arg1}+${arg2}")
    set(${result} ${tmp} PARENT_SCOPE)
endfunction()   

get_sum(1 2 RET)
message("sum=:${RET}")
```

### 宏定义

宏定义

```cmake
# 宏定义 无自己的作用域，参数是文本替换
macro(my_macro arg1 arg2)
    message("Macro Argument 1: ${arg1}")
    message("Macro Argument 2: ${arg2}")
endmacro()  

# 调用宏
my_macro("Hello" "Macro")
```

## 内置工具

### 列表操作

初始化列表

```cmake
set(VAR_LIST "Item1" "Item2" "Item3")    
```

添加元素

```cmake
list(APPEND VAR_LIST "Item4" "Item5")
```

移除列表变量中的元素，大小写敏感

```cmake
list(REMOVE_ITEM VAR_LIST "Item2")
```

获取列表长度

```cmake
list(LENGTH VAR_LIST LIST_LENGTH)
```

获取列表中的元素

```cmake
list(GET VAR_LIST 0 FIRST_ITEM)
```

### 数学操作

加减乘除

```cmake
math(EXPR result "5 + 3")
math(EXPR result "5 - 3")
math(EXPR result "5 * 3")
math(EXPR result "5 / 3")
```

位运算

```cmake
math(EXPR result "5 << 3")
math(EXPR result "5 >> 3")
math(EXPR result "5 | 3")
math(EXPR result "5 & 3")
math(EXPR result "5 ^ 3")
```

### 字符串操作

初始化字符串

```cmake
set(VAR_STRING "Hello, CMake!")
```

添加字符串

```cmake
string(APPEND VAR_STRING "suffix")
```

获取字符串长度

```cmake
string(LENGTH ${VAR_STRING} LENGTH_VAR)
```

获取字符串子串

```cmake
string(SUBSTRING ${VAR_STRING} 0 5 RESULT)
```

转换字符串小写

```cmake
string(TOLOWER ${VAR_STRING} RESULT)
```

转换字符串大写

```cmake
string(TOUPPER ${VAR_STRING} RESULT)
```

替换字符串中的子串

```cmake
string(REPLACE "Hello" "OK" RESULT ${VAR_STRING})
```

### 输出操作

输出消息

```cmake
message("Hello, CMake")
```

输出状态消息

```cmake
message(STATUS "Hello, CMake")
```

输出警告消息

```cmake
message(WARNING "Hello, CMake")
```

输出错误消息，并终止构建

```cmake
message(FATAL_ERROR "Hello, CMake")
```

### SET操作

定义普通变量

```cmake
set(VAR_NAME "value")
```

定义缓存变量, 缓存变量存储在CMakeCache.txt文件中，在整个 CMake 构建树中全局有效，可以在CMake GUI中修改

```cmake
set(VAR_NAME "value" CACHE STRING "Description")
```

清除普通变量

```cmake
set(VAR_NAME "")
```

删除普通变量

```cmake
unset(VAR_NAME)
```

删除缓存变量

```cmake
unset(VAR_NAME CACHE)
```

定义列表变量

```cmake
set(VAR_LIST "item1" "item2" "item3")
```

添加新元素到列表变量

···cmake
set(VAR_LIST ${VAR_LIST} "item4" "item5")
···

设置临时环境变量

```cmake
set(ENV{MY_ENV_VAR} "some_value")
```

输出环境变量

```cmake
message("MY_ENV_VAR = $ENV{MY_ENV_VAR}")
```

### 文件操作

写入文件

```cmake
file(WRITE "./BasicSyntax/Test.txt" "content")
```

读取文件

```cmake
file(READ "./BasicSyntax/Test.txt" FILE_CONTENT)
```

追加到文件

```cmake
file(APPEND "./BasicSyntax/Test.txt" "more content\n")
```

列出目录内容

```cmake
file(GLOB SOURCE_FILES "${CMAKE_CURRENT_LIST_DIR}/*.cmake")

foreach(FILE IN LISTS SOURCE_FILES)
    message("Found file: ${FILE}")
endforeach()
```
