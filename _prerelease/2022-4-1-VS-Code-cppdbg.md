---
layout: post
title: 使用 VS Code 调试C语言
categories: [vscode, cate2]
description: some word here
keywords: vscode, gdb
---

VS Code仅仅是一个代码编辑器，并不自带任何用于调试的编译器
因此为了使用vs code 调试 c语言，还需要额外安装编译器
编译程序需要gcc、g++，调试程序需要gdb，而软件MinGW同时包含了这几个功能
因此我们要先下载MinGW
## 安装MinGW注意事项
下载地址：
https://www.mingw-w64.org/downloads/

## 为编译器设置环境变量

随后再去VS Code官网下载安装VS Code

## 为VS Code设置C语言扩展插件

## VS Code汉化（可选）

## 设置工作空间

## 配置调试参数
作为代码编辑器而不是IDE，VSCode默认并没有为C语言设置任何调试配置
C/C++默认配置的调试文件只能针对单个程序进行调试
所以为了确保工作空间每个文件都可以单独灵活调试，需要自己编写调试配置
先在工作空间下新建文件夹 .vscode
这个文件内部将存放该工作空间的调试参数
*妥善保管该文件夹防止误删*
调试C语言时 该文件夹下一般有以下文件

launch.json
调试参数配置文件

tasks.json
自动任务配置文件

setting.json
针对该工作空间的设置储存文件

c_cpp_properties.json
C语言编译环境配置文件(无法设置编译器环境变量时才需使用)

launch.json便是调试配置文件了
右下角默认添加的配置为单个程序配置文件

{
   "version": "0.2.0",
   "configurations": [
       {
           "name": "C++ 启动 (GDB)", //调试配置名称
           "type": "cppdbg", //调试类型 cppdbg 为 C++ Debug
           "request": "launch",
           "targetArchitecture": "x86",
           "program": "${workspaceRoot}\\${fileBasename}.exe", //运行调试程序目录 {workspaceRoot}为文件根目录 {fileBasename}为文件无后缀名称
           "miDebuggerPath":"gdb", //调试命令
           "args": [], //命令附加参数
           "stopAtEntry": false,
           "cwd": "${workspaceRoot}", //命令行运行环境
           "externalConsole": true, //是否在外部运行
           "preLaunchTask": "compile" //调试前任务(详细见.vscode/tasks.json)
           }
   ]
}

{
    
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) 启动",
            "preLaunchTask": "C/C++: g++.exe 生成活动文件",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "gdb",
            "miDebuggerPath": "gdb",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                },
                {
                    "description":  "将反汇编风格设置为 Intel",
                    "text": "-gdb-set disassembly-flavor intel",
                    "ignoreFailures": true
                }
            ]
        }
        
    ]
}

然而launch.json只负责调试文件，并不接管编译任务
通过可以通过里面的 "preLaunchTask" 参数来设置调试前任务
刚好可以用这个任务设置编译任务
在.vscode文件夹下添加 tasks.json 文件

该文件内容比较固定，不需要过多解释
{
    "version": "2.0.0",
    "tasks": [
        {
            "type": "shell",
            "label": "compile",
            "command": "g++", //命令
            "args": [
                "-g",
                "${file}",
                "-o",
                "${fileDirname}\\${fileBasenameNoExtension}.exe"
            ], //命令附加参数
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}

{
    // See https://go.microsoft.com/fwlink/?LinkId=733558 
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "type": "shell",
            "label": "C/C++: g++.exe 生成活动文件",
            "command": "C:\\Program Files\\mingw-w64\\x86_64-8.1.0-posix-sjlj-rt_v6-rev0\\mingw64\\bin\\g++.exe",
            "args": [
                "-g",
                "${file}",
                "-o",
                "${fileDirname}\\${fileBasenameNoExtension}.exe"
            ],
            "options": {
                "cwd": "C:\\Program Files\\mingw-w64\\x86_64-8.1.0-posix-sjlj-rt_v6-rev0\\mingw64\\bin"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "调试器生成的任务。"
        }
    ]
}


C++编译命令行环境配置
如果上面设置环境变量没有成功，就需要在vscode内部配置运行环境变量
这步过程比较繁杂，光复制粘贴是不行的，需要根据自己的MinGW目录来修改

下面的配置范例是以MinGW根目录为
D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/
的前提下编写的


c_cpp_properties.json

{
    "configurations": [
        {
            "name": "Win32",
            "includePath": [
                "${workspaceRoot}",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++/x86_64-w64-mingw32",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++/backward",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++/tr1",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/x86_64-w64-mingw32/include"
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "__GNUC__=6",
                "__cdecl=__attribute__((__cdecl__))"
            ],
            "intelliSenseMode": "msvc-x64",
            "browse": {
                "path": [
                    "${workspaceRoot}",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++/x86_64-w64-mingw32",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++/backward",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++/tr1",
                "D:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/x86_64-w64-mingw32/include"
                ]
            },
            "limitSymbolsToIncludedHeaders": true,
            "databaseFilename": ""
        }
    ],
    "version": 3
}
