---
title: CMake公共编译配置
date: 2026-06-02 23:15:01
categories:
    - CMake
tags: 
    - CMake
    - C++
---

使用CMake编译可执行文件

<!-- more -->

## 整体架构

```
BuildStaticLibrary/
├── CMakeLists.txt                 # 根项目配置文件
├── build.sh                       # 编译脚本
├── cmake/                         # CMake 配置目录
│   └── BuildConfig.cmake          # 通用编译配置
├── src/                           # 源代码目录
│   ├── CMakeLists.txt             # 主源代码配置
│   ├── main.cpp                   # 程序入口
│   ├── global/                    # 全局模块（版本管理）
│   │   ├── CMakeLists.txt         # 全局模块配置
│   │   ├── version.h              # 版本声明
│   │   └── version.cpp            # 版本实现
│   └── logger/                    # 日志模块
│       ├── CMakeLists.txt         # 日志模块配置
│       ├── logger.h               # 日志接口
│       ├── logger.cpp             # 日志实现
│       ├── logger_impl.h          # 日志内部实现
│       └── logger_export.h        # 导出宏定义
└── build/                         # 编译输出目录
    ├── CMakeCache.txt             # CMake 缓存配置
    ├── Makefile                   # 顶层编译规则
    ├── cmake_install.cmake        # 安装规则
    ├── CMakeFiles/                # CMake 生成的文件目录
    │   ├── cmake.check_cache      # 缓存检查
    │   ├── CMakeDirectoryInformation.cmake
    │   ├── Makefile.cmake         # CMake 生成的 Makefile
    │   ├── Makefile2              # 子目录编译规则
    │   ├── progress.marks         # 编译进度标记
    │   ├── TargetDirectories.txt  # 目标目录列表
    │   ├── 3.28.3/                # CMake 版本目录
    │   │   ├── CMakeCXXCompiler.cmake   # C++ 编译器配置
    │   │   ├── CMakeSystem.cmake        # 系统配置
    │   │   └── CompilerIdCXX/           # 编译器标识
    │   ├── global.dir/            # global 模块编译目录
    │   │   ├── build.make         # 模块编译规则
    │   │   └── cmake_clean.cmake  # 清理规则
    │   └── logger.dir/            # logger 模块编译目录
    │       ├── build.make         # 模块编译规则
    │       └── cmake_clean.cmake  # 清理规则
    ├── bin/                       # 可执行文件目录
    │   └── release/               # Release 配置输出
    │       └── main               # 最终可执行文件
    ├── lib/                       # 静态库目录
    │   ├── libglobal.a            # 全局模块静态库
    │   └── liblogger.a            # 日志模块静态库
    └── src/                       # 编译过程中间文件
        ├── global/                # global 模块中间文件
        │   └── CMakeFiles/
        └── logger/                # logger 模块中间文件
            └── CMakeFiles/
```

---

## 详细说明

### 📁 根目录文件

| 文件 | 说明 |
|------|------|
| **CMakeLists.txt** | 项目根配置文件，定义项目名称、版本、C++ 标准等 |
| **build.sh** | 编译脚本，自动创建 build 目录并执行 cmake 和 make |

### 📁 cmake/ 目录

| 文件 | 说明 |
|------|------|
| **BuildConfig.cmake** | 通用编译配置，包含 C++ 标准版本和项目版本定义 |

### 📁 src/ 源代码目录

#### 主文件

| 文件 | 说明 |
|------|------|
| **CMakeLists.txt** | 指定源代码编译规则、子目录和链接依赖 |
| **main.cpp** | 程序入口点，调用 logger 和 version 模块 |

#### src/global/ 模块

| 文件 | 说明 |
|------|------|
| **CMakeLists.txt** | 编译成 `libglobal.a` 静态库 |
| **version.h** | 版本信息声明：`const char* getVersion()` |
| **version.cpp** | 版本信息实现：返回版本字符串 "1.0.0" |

#### src/logger/ 模块

| 文件 | 说明 |
|------|------|
| **CMakeLists.txt** | 编译成 `liblogger.a` 静态库 |
| **logger.h** | 日志接口声明 |
| **logger.cpp** | 日志功能实现 |
| **logger_impl.h** | 日志内部实现细节 |
| **logger_export.h** | 库导出宏定义 |

### 📁 build/ 编译输出目录

#### 配置文件

| 文件 | 说明 |
|------|------|
| **CMakeCache.txt** | CMake 缓存，保存配置信息和编译选项 |
| **Makefile** | 顶层 Makefile，包含所有编译目标 |
| **cmake_install.cmake** | 安装规则 |

#### CMakeFiles/ 子目录

| 子目录 | 说明 |
|--------|------|
| **3.28.3/** | CMake 版本信息和编译器配置 |
| **global.dir/** | global 模块的编译规则文件 |
| **logger.dir/** | logger 模块的编译规则文件 |

#### bin/ 可执行文件目录

| 文件 | 说明 |
|------|------|
| **release/main** | 最终可执行文件，链接了 global 和 logger 静态库 |

#### lib/ 静态库目录

| 文件 | 说明 |
|------|------|
| **libglobal.a** | global 模块编译成的静态库 |
| **liblogger.a** | logger 模块编译成的静态库 |

---

## 编译流程

```
源代码（src/）
    ↓
CMake 解析 CMakeLists.txt
    ↓
生成编译规则（build/CMakeFiles/）
    ↓
编译各模块源文件
    ├─ version.cpp → version.cpp.o
    └─ logger.cpp → logger.cpp.o
    ↓
打包静态库
    ├─ libglobal.a（包含 version.cpp.o）
    └─ liblogger.a（包含 logger.cpp.o）
    ↓
编译主程序
    └─ main.cpp → main.cpp.o
    ↓
链接所有目标文件和静态库
    └─ main.cpp.o + libglobal.a + liblogger.a
    ↓
生成可执行文件
    └─ build/bin/release/main
```

---

## 关键依赖关系

### CMake 目标依赖

```
main (可执行文件)
  ├─ global (静态库)
  │   └─ SOURCES: src/global/version.cpp
  └─ logger (静态库)
      └─ SOURCES: src/logger/logger.cpp
```

---

## 编译命令

```bash
# 进入项目目录
cd BuildStaticLibrary

# 方法1：使用提供的编译脚本
./build.sh

# 方法2：手动编译
mkdir -p build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make

# 运行程序
./build/bin/release/main
```


---

## 输出示例

运行编译后的程序：

```bash
$ ./build/bin/release/main
Hello, World!
1.0.0
```

表示：
- 第一行：logger 模块输出的日志信息
- 第二行：getVersion() 返回的版本号


## CMakeLists.txt说明

根目录CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)

project(main VERSION 1.0 LANGUAGES CXX)

include(cmake/BuildConfig.cmake)

add_subdirectory(src)
```

src/CMakeLists.txt

```cmake
set(SOURCES main.cpp)

add_subdirectory(global)
add_subdirectory(logger)

add_executable(${PROJECT_NAME} ${SOURCES})

target_link_libraries(${PROJECT_NAME} PRIVATE global) 
target_link_libraries(${PROJECT_NAME} PRIVATE logger)  
```

src/global/CMakeLists.txt

```cmake
set(SOURCES version.cpp)

# 指定编译静态库
add_library(global STATIC ${SOURCES})

# 指定库版本，需先创建目标才能配置属性
set_target_properties(global PROPERTIES
    NO_SONAME ON
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION_MAJOR}
)

target_include_directories(global PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/..)
```

src/logger/CMakeLists.txt

```cmake
set(SOURCES logger.cpp)

# 指定编译静态库
add_library(logger STATIC ${SOURCES})

# 指定库版本，需先创建目标才能配置属性
set_target_properties(logger PROPERTIES
    NO_SONAME ON
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION_MAJOR}
)

target_include_directories(logger PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/..)
```