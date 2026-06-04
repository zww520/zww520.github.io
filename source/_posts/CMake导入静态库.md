---
title: CMake导入静态库
date: 2026-06-04 23:41:01
categories:
    - CMake
tags: 
    - CMake
    - C++
---

CMake导入静态库

<!-- more -->

## CMake导入静态库示例

### 目录结构

```
BuildStaticLibrary/
├── CMakeLists.txt                 # 根项目配置文件
├── build.sh                       # 编译脚本
├── cmake/                         # CMake 配置目录
│   └── BuildConfig.cmake          # 通用编译配置
├── src/                           # 源代码目录
│   ├── CMakeLists.txt             # 主源代码配置
│   ├── main.cpp                   # 程序入口
├── thirdparty/                    # 第三方库统一存放路径
│   └── MakeLists.txt 
│   └── logger/                    # 日志模块
│       ├── CMakeLists.txt
│   └── include/  
│       ├── logger.h               # 日志接口
│       └── logger_export.h        # 导出宏定义
│   └── lib/  
│       ├── liblogger.a            # 日志静态库
```

### CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)

project(main VERSION 1.2.3 LANGUAGES CXX)

include(cmake/BuildConfig.cmake)

add_subdirectory(src)
```

### src/CMakeLists.txt

```cmake
set(SOURCES main.cpp)

add_subdirectory(thirdparty)

add_executable(${PROJECT_NAME} ${SOURCES})

target_link_libraries(${PROJECT_NAME} PRIVATE logger)  

set(CMAKE_VERBOSE_MAKEFILE ON)  # 打印详细的构建信息
```

### thirdparty/CMakeLists.txt

```cmake
add_subdirectory(logger)
```

### thirdparty/logger/CMakeLists.txt

```cmake
add_library(logger STATIC IMPORTED) # 声明一个全局的导入库

set_target_properties(logger PROPERTIES # 设置库属性
    IMPORTED_LOCATION ${CMAKE_CURRENT_SOURCE_DIR}/lib/liblogger.a
    INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include
)

# 也可以通过这种方式，导入头文件搜索路径
target_include_directories(logger INTERFACE
    "${CMAKE_CURRENT_SOURCE_DIR}/include"
)
```
