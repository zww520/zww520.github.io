---
title: Code Snippets配置
date: 2026-05-25 00:43:48
categories:
    - VSCode
tags: 
    - VSCode
---

Code Snippets配置

<!-- more -->

## 配置Code Snippets

### 配置C++ Code Snippets

- 配置头文件保护宏定义
- 配置库导出宏定义

``` json
// cpp.json
{
    "C++ Header Guard": {
        "prefix": "cheader",
        "body": [
            "#ifndef ${TM_FILENAME_BASE/(.*)/${1:/upcase}/}_H",
            "#define ${TM_FILENAME_BASE/(.*)/${1:/upcase}/}_H",
            "",
            "$0",
            "",
            "#endif // ${TM_FILENAME_BASE/(.*)/${1:/upcase}/}_H"
        ]
    },
    "C++ Library Export": {
        "prefix": "clib-export",
        "body": [
            "#ifdef _WIN32",
            "    ifdef ${TM_FILENAME_BASE/(.*)/${1:/upcase}/}_EXPORTS",
            "        define ${TM_FILENAME_BASE/(.*)/${1:/upcase}/}_EXPORTS __declspec(dllexport)",
            "    else",
            "        define ${TM_FILENAME_BASE/(.*)/${1:/upcase}/}_EXPORTS __declspec(dllimport)",
            "    endif",
            "#else",
                "#if defined(__GNUC__) || defined(__clang__)",
            "       #undef ${TM_FILENAME_BASE/(.*)/${1:/upcase}/}_EXPORTS",
            "       #define ${TM_FILENAME_BASE/(.*)/${1:/upcase}/}_EXPORTS __attribute__((visibility(\"default\")))",
            "   #else",
            "       #define ${TM_FILENAME_BASE/(.*)/${1:/upcase}/}_EXPORTS",
            "   #endif",
            "#endif"
        ]
    }
}
```