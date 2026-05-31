---
title: koro1FileHeader插件配置
date: 2026-05-25 00:43:48
categories:
    - VSCode
tags: 
    - VSCode
---

koro1FileHeader插件配置

<!-- more -->

## koro1FileHeader插件配置

### 简介

用于快速生成文件头部注释和函数注释

### 插件Github地址

```
https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
```

### 快捷键

生成文件头部注释

```
window：ctrl+win+i,mac：ctrl+cmd+i, linux: ctrl+meta+i,Ubuntu: ctrl+super+i

```

生成函数注释

```
window：ctrl+win+t,mac：ctrl+cmd+t,linux: ctrl+meta+t, Ubuntu: ctrl+super+t
```

### 配置文件

```json
    "fileheader.configObj": {
        "language": {
            "h/hpp/cpp": {
                "head": "/**",
                "middle": " * @",
                "end": " */",
                "functionSymbol": {
                    "head": "/** ",
                    "middle": " * @",
                    "end": " */"
                },
                "functionParams": "js"
            }
        },
        "createFileTime": true, //  设为false更改为当前生成注释的时间
        "autoAdd": false, // 自动添加头部注释开启才能自动添加
        "autoAddLine": 100, // 默认文件超过100行就不再自动添加头部注释
        "autoAlready": false, // 只让支持的语言，自动添加头部注释
        "annotationStr": {
            "head": "/*", // 自定义注释头部
            "middle": " * @", // 自定义注释中间部分(注意空格,这也是最终生成注释的一部分)
            "end": " */", // 自定义注释尾部
            "use": false // 开启后，当匹配语言失败后，也能生成注释
        },
        "supportAutoLanguage": ["h", "c", "cpp"], // 设置支持自动添加头部注释的语言
        "headInsertLine": { // 头部注释第几行插入，默认是第一行
            "php": 2,
            "sh": 2
        },
        "beforeAnnotation": { // 头部注释前面插入内容
            "文件前缀": "该文件后缀的头部注释之前添加某些内容"
        },
        "afterAnnotation": { // 头部注释后缀插入内容
            "文件后缀": "该文件后缀的头部注释之后添加某些内容"
        },
        "specialOptions": {
            "特殊字段": "自定义比如LastEditTime/LastEditors"
        },
        "switch": {
            "newlineAddAnnotation": true // 注释里遇到换行添加注释符号
        },
        "moveCursor": true, // 移动光标到`Description :`所在行
        "dateFormat": "YYYY-MM-DD HH:mm:ss", // createFileTime为false, 即可更改为当前注释的插入时间格式
        "atSymbol": [
            "@", // 头部注释默认值
            "@" // 函数注释默认值
        ],
        "atSymbolObj": {
            "h": [ // 自定义.h文件注符号
                "@",
                "@"
            ]
        },
        "colon": [ // 所有文件的头部注释和函数注释的默认值
            ": ",
            ": "
        ],
        "colonObj": {
            "h": [
                ": ",
                ": "
            ]
        },
        "filePathColon": "路径分隔符替换",
        "showErrorMessage": false,
        "writeLog": false,
        "wideSame": false,
        "wideNum": 13,
        "functionWideNum": 0,
        "CheckFileChange": false,
        "createHeader": false,
        "useWorker": false,
        "designAddHead": false,
        "headDesignName": "random",
        "headDesign": false,
        "cursorModeInternalAll": {},
        "openFunctionParamsCheck": true,
        "functionParamsShape": [
            "{",
            "}"
        ],
        "functionBlankSpaceAll": {},
        "functionTypeSymbol": "*",
        "typeParamOrder": "type param",
        "customHasHeadEnd": {},
        "throttleTime": 60000,
        "functionParamAddStr": "",
        "NoMatchParams": "no show param"
    },
    "fileheader.cursorMode": {

    },
    "fileheader.customMade": {
        "Date": "Do not edit", // 文件创建时间(不变)
        "Author": "laplus", 
        "LastEditTime": "Do not edit", // 文件最后编辑时间
    },
```