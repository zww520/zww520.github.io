---
title: CMake预置变量说明
date: 2026-06-04 00:43:48
categories:
    - CMake
tags: 
    - CMake
    - C++
---

CMake预置变量说明

<!-- more -->

## CMake预置变量

### 版本信息变量：

- CMAKE_VERSION：CMake版本号
- CMAKE_MAJOR_VERSION：主版本号
- CMAKE_MINOR_VERSION：次版本号
- CMAKE_PATCH_VERSION：补丁版本号

### 系统和平台变量：

- CMAKE_SYSTEM_NAME：操作系统名称（如Windows、Linux）
- CMAKE_SYSTEM_VERSION：操作系统版本
- CMAKE_SYSTEM_PROCESSOR：处理器架构
- WIN32、UNIX、APPLE：平台标识符

### 编译器和工具变量

- CMAKE_CXX_COMPILER：C++编译器路径
- CMAKE_C_COMPILER：C编译器路径
- CMAKE_LINKER：链接器路径
- CMAKE_AR：归档工具路径
- CMAKE_MAKE_PROGRAM：Make 程序路径（或等效构建工具）
- CMAKE_BUILD_TOOL：正在使用的构建工具路径
- CMAKE_COMMAND：CMake 可执行文件路径
- CMAKE_CXX_STANDARD：C++标准
- CMAKE_CXX_FLAGS：C++编译器标志
- CMAKE_C_FLAGS：C编译器标志
- CMAKE_EXE_LINKER_FLAGS：可执行文件链接标志
- CMAKE_SHARED_LINKER_FLAGS：共享库链接标志
- CMAKE_MODULE_LINKER_FLAGS：模块链接标志
- CMAKE_CXX_STANDARD_REQUIRED：是否强制要求指定的C++标准（ON/OFF）
- CMAKE_CXX_EXTENSIONS：是否使用编译器特定扩展（ON/OFF）
- CMAKE_POSITION_INDEPENDENT_CODE：是否生成位置无关代码（ON/OFF）
- CMAKE_CXX_COMPILER_ID：C++编译器标识（GCC、Clang、MSVC等）
- CMAKE_CXX_COMPILER_VERSION：C++编译器版本号
- CMAKE_CXX_COMPILER_FRONTEND_VARIANT：编译器前端变体
- CMAKE_CXX_COMPILER_TARGET：编译器目标平台
- CMAKE_C_COMPILER_ID：C编译器标识
- CMAKE_C_COMPILER_VERSION：C编译器版本号

### 构建和配置变量

- CMAKE_BUILD_TYPE：构建类型（如Debug、Release、RelWithDebInfo、MinSizeRel）
- CMAKE_CONFIGURATION_TYPES：支持的配置类型（多配置生成器，如Visual Studio）
- CMAKE_GENERATOR：当前使用的生成器名称（如 Ninja、Unix Makefiles、Visual Studio 17 2022）
- CMAKE_GENERATOR_PLATFORM：生成器平台（如 Win32、x64）
- CMAKE_GENERATOR_TOOLSET：生成器工具集（MSVC 工具集）
- CMAKE_CFG_INTDIR：多配置生成器中的配置目录名称（如 Debug、Release）
- CMAKE_BUILD_TYPE：单配置生成器中的构建类型

### 目录与文件变量

- CMAKE_SOURCE_DIR：顶级 CMakeLists.txt 所在的源目录
- CMAKE_BINARY_DIR：顶级构建目录
- CMAKE_CURRENT_SOURCE_DIR：当前处理的 CMakeLists.txt 所在源码目录
- CMAKE_CURRENT_BINARY_DIR：当前处理的构建目录
- CMAKE_CURRENT_LIST_DIR：当前 CMake 脚本文件所在目录
- CMAKE_CURRENT_LIST_FILE：当前 CMake 脚本文件路径
- CMAKE_CURRENT_LIST_LINE：当前 CMake 脚本文件中正在处理的行号
- CMAKE_PARENT_LIST_DIR：当前脚本的父目录
- CMAKE_PARENT_LIST_FILE：当前脚本的父文件路径
- CMAKE_HOME_DIRECTORY：最初启动 CMake 的顶级源目录
- CMAKE_ROOT：CMake 安装目录
- CMAKE_MODULE_PATH：额外的 CMake 模块搜索路径
- CMAKE_PREFIX_PATH：查找包和库的路径前缀

### 输出路径变量

- CMAKE_RUNTIME_OUTPUT_DIRECTORY：可执行文件或 Windows DLL 的输出目录
- CMAKE_LIBRARY_OUTPUT_DIRECTORY：共享库 (.so/.dylib/.dll) 的输出目录
- CMAKE_ARCHIVE_OUTPUT_DIRECTORY：静态库 (.a/.lib) 的输出目录
- CMAKE_PDB_OUTPUT_DIRECTORY：MSVC 的 PDB 文件输出目录
- CMAKE_INSTALL_PREFIX：安装路径前缀

### 项目变量

- PROJECT_NAME：当前项目名称
- PROJECT_VERSION：当前项目版本
- PROJECT_SOURCE_DIR：当前项目源目录
- PROJECT_BINARY_DIR：当前项目构建目录
- PROJECT_VERSION_MAJOR：当前项目主版本号
- PROJECT_VERSION_MINOR：当前项目次版本号
- PROJECT_VERSION_PATCH：当前项目补丁版本号
- CMAKE_PROJECT_NAME：顶级项目名称

### 其他常用内置变量

- CMAKE_SKIP_RPATH：是否跳过安装时的 rpath 设置
- CMAKE_INCLUDE_CURRENT_DIR：是否将当前目录添加到编译器的头文件搜索路径
- CMAKE_VISUAL_STUDIO_VERSION：Visual Studio 生成器版本（仅 Windows）
- CMAKE_OSX_ARCHITECTURES：macOS Xcode 构建目标架构
- CMAKE_OSX_SYSROOT：macOS SDK 根路径
- CMAKE_TOOLCHAIN_FILE：默认工具链文件路径
- CMAKE_VERBOSE_MAKEFILE：是否启用详细构建输出（ON/OFF）
- CMAKE_EXPORT_COMPILE_COMMANDS：是否导出 compile_commands.json
