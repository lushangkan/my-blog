---
title: 解决CMake编译中的error C2734： “GifAsciiTable8x8”错误
published: "2021-12-23 23:38:00"
draft: false
description: 在Windows10中编译dilb时，出现了以下错误：
author: 路上看见
tags:
  - 默认分类
toc: true
---


在Windows10中编译dilb时，出现了以下错误：

[!图片][1]

    PS E:\dlib-19.22> py setup.py install
    ...
    C:\ProgramData\Anaconda3\Library\include\gif_lib.h(286,61): error C2734: “GifAsciiTable8x8”: 如果不是外部的，则必须初始化常量对象 (编译源文件
    E:\dlib-19.22\tools\python\src\vector.cpp) [E:\dlib-19.22\build\temp.win-amd64-3.9\Release\_dlib_pybind11.vcxproj]
    C:\ProgramData\Anaconda3\Library\include\gif_lib.h(286,61): error C2734: “GifAsciiTable8x8”: 如果不是外部的，则必须初始化常量对象 (编译源文件
    E:\dlib-19.22\tools\python\src\matrix.cpp) [E:\dlib-19.22\build\temp.win-amd64-3.9\Release\_dlib_pybind11.vcxproj]
    C:\ProgramData\Anaconda3\Library\include\gif_lib.h(286,61): error C2734: “GifAsciiTable8x8”: 如果不是外部的，则必须初始化常量对象 (编译源文件
    E:\dlib-19.22\tools\python\src\dlib.cpp) [E:\dlib-19.22\build\temp.win-amd64-3.9\Release\_dlib_pybind11.vcxproj]
    C:\ProgramData\Anaconda3\Library\include\gif_lib.h(286,61): error C2734: “GifAsciiTable8x8”: 如果不是外部的，则必须初始化常量对象 (编译源文件
    E:\dlib-19.22\tools\python\src\svm_c_trainer.cpp) [E:\dlib-19.22\build\temp.win-amd64-3.9\Release\_dlib_pybind11.vcxproj]
    ...
    
随后在Github上找到了问题的原因。
原来是因为Anaconda3附带的giflib有问题，导致编译错误。
只需要在命令后添加就可以解决
如：--no DLIB_GIF_SUPPORT

    PS E:\dlib-19.22> py setup.py install --no DLIB_GIF_SUPPORT
    ...
    Finished processing dependencies for dlib==19.22.0

成功!

  [1]: ./138905933.png