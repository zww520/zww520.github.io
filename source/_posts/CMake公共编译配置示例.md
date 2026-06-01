---
title: CMake公共编译配置
date: 2026-06-01 23:41:01
categories:
    - CMake
tags: 
    - CMake
    - C++
---

CMake公共编译配置

<!-- more -->

## CMake公共编译配置示例

### 项目架构

```text
project/
├── build                       编译过程文件和编译结果
|   └── bin/debug               debug输出
    └── bin、release            release输出
├── cmake                       通用cmake文件路径
|   └── BuildConfig.cmake       编译配置   
├── logger                      库安装路径
|   └── include                 库头文件路径
|       └── logger              库名称文件夹
|           └── logger.h        库头文件
|   └── lib                     动态库路径
|       └── logger.so           动态库
├── src                         源代码
|   └── module1                 模块1
|   └── module2                 模块2
|   └── module3                 模块3
|   └── CMakeLists.txt
├── tests                       测试工程
│   └── test_func1.cpp          单元测试1
|   └── test_func2.cpp          单元测试2
|   └── CMakeLists.txt
├── build.sh                    编译脚本
└── CMakeLists.txt
```

### 编译脚本

默认编译Release版本

build.sh
```shell
BUILD_TYPE=${1:-Release}
cmake -B build -S . -DCMAKE_BUILD_TYPE=${BUILD_TYPE}
cmake --build build
```

### CMake公共编译配置

统一项目编译配置和输出路径

BuildConfig.cmake
```cmake
# 编译配置文件

# C++ 标准配置
# ============================================================================
# 设置C++标准版本（可通过命令行覆盖）
if(NOT DEFINED CMAKE_CXX_STANDARD)
    set(CMAKE_CXX_STANDARD 17 CACHE STRING "C++ standard to use")
endif()

# 要求编译器严格遵守指定的C++标准
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 禁用编译器扩展（使用标准C++，而非编译器特定扩展）
set(CMAKE_CXX_EXTENSIONS OFF)

message(STATUS "C++ Standard: C++${CMAKE_CXX_STANDARD}")
message(STATUS "Project Name: ${PROJECT_NAME}")
message(STATUS "Project Version: ${PROJECT_VERSION}")

# ============================================================================
# 编译选项配置
# ============================================================================
if(MSVC)
    # MSVC编译器选项, /W4 是最高级别的警告，/W3 是默认级别
    add_compile_options(/W4)

    # 调试和发布配置, /Zi 生成调试信息, /Od 禁止优化, /O2 启用优化, /DNDEBUG 定义NDEBUG宏
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /Zi /Od")
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /O2 /DNDEBUG")

    message(STATUS "Compiler: MSVC")
else()
    # GCC/Clang 编译器选项, -Wall 启用所有警告, -Wextra 启用额外警告, -Wpedantic 启用严格的标准兼容警告
    add_compile_options(-Wall -Wextra -Wpedantic)

    # 启用符号可见性隐藏（对库项目有意义，若仅可执行可注释此行）
    # add_compile_options(-fvisibility=hidden)

    # 调试和发布配置, -g 生成调试信息, -O0 禁止优化, -O2 启用优化, -DNDEBUG 定义NDEBUG宏
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g -O0")
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O2 -DNDEBUG")

    # 可选：启用LTO（链接时优化）
    # set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -flto")

    if(APPLE)
        message(STATUS "Compiler: Clang (Apple)")
    else()
        message(STATUS "Compiler: ${CMAKE_CXX_COMPILER_ID} ${CMAKE_CXX_COMPILER_VERSION}")
    endif()
endif()

# ============================================================================
# 输出目录配置
# ============================================================================
# 统一将输出放到 build 目录的特定子目录，并按配置区分
if(NOT DEFINED CMAKE_RUNTIME_OUTPUT_DIRECTORY)
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>")
endif()
if(NOT DEFINED CMAKE_LIBRARY_OUTPUT_DIRECTORY)
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib/$<CONFIG>")
endif()
if(NOT DEFINED CMAKE_ARCHIVE_OUTPUT_DIRECTORY)
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib/$<CONFIG>")
endif()

# 单配置生成器仍使用配置特定目录
if(NOT DEFINED CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG)
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG "${CMAKE_BINARY_DIR}/bin/debug")
endif()
if(NOT DEFINED CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE)
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE "${CMAKE_BINARY_DIR}/bin/release")
endif()
if(NOT DEFINED CMAKE_LIBRARY_OUTPUT_DIRECTORY_DEBUG)
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_DEBUG "${CMAKE_BINARY_DIR}/lib/debug")
endif()
if(NOT DEFINED CMAKE_LIBRARY_OUTPUT_DIRECTORY_RELEASE)
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_RELEASE "${CMAKE_BINARY_DIR}/lib/release")
endif()
if(NOT DEFINED CMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG)
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG "${CMAKE_BINARY_DIR}/lib/debug")
endif()
if(NOT DEFINED CMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE)
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE "${CMAKE_BINARY_DIR}/lib/release")
endif()

if(MSVC AND NOT DEFINED CMAKE_PDB_OUTPUT_DIRECTORY)
    set(CMAKE_PDB_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>")
endif()

message(STATUS "Output Directory: ${CMAKE_BINARY_DIR}")

# ============================================================================
# 安装目录配置
# ============================================================================
if(NOT DEFINED CMAKE_INSTALL_PREFIX)
    set(CMAKE_INSTALL_PREFIX "${CMAKE_BINARY_DIR}/install" CACHE PATH "Install prefix")
endif()

message(STATUS "Install Directory: ${CMAKE_INSTALL_PREFIX}")

# ============================================================================
# 编译模式
# ============================================================================
if(NOT DEFINED CMAKE_BUILD_TYPE AND NOT DEFINED CMAKE_CONFIGURATION_TYPES)
    set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type")
endif()

message(STATUS "Build Type: ${CMAKE_BUILD_TYPE}")
```

