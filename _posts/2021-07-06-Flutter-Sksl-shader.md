---
layout: post
---

# 在移动设备上减少着色器编译卡顿

## 关联知识

### [SkSL(Skia Shading Lanaguage)](https://github.com/google/skia/blob/main/src/sksl/README)

SkSL ("Skia Shading Language") is a variant of GLSL which is used as Skia's
internal shading langua ge. SkSL is, at its heart, a single standardized version
of GLSL which avoids all of the various version and dialect differences found
in GLSL "in the wild", but it does bring a few of its own changes to the table.

Skia uses the SkSL compiler to convert SkSL code to GLSL, GLSL ES, or SPIR-V
before handing it over to the graphics driver.

### GLSL

GLSL - OpenGL Shading Language 也称作 GLslang，是一个以C语言为基础的高阶着色语言。它是由 OpenGL ARB 所建立，提供开发者对绘图管线更多的直接控制，而无需使用汇编语言或硬件规格语言。

随着近年来绘图卡的进步， 已在渲染管线中的顶点（vertex）和片断（fragment）层次中，加入更具弹性的新功能。 达到在这个层次中，使用片断和顶点着色器的可编程性。

最初这个功能是以组合语言撰写着色器来达到的。组合语言对开发者的使用是不直观而复杂的。OpenGL ARB 建立了 OpenGL 着色语言，为 GPU 的程式设计提供更加直观的方法，当维护开放标准的时候，就有助于带动 OpenGL 的历史。

最初 OpenGL 1.5 是以扩充形式引入，后来 OpenGL ARB 在 OpenGL 2.0 核心中正式纳入 GLSL。自 1992 年建立的OpenGL 1.0 起，OpenGL 2.0 是第一个 OpenGL 的大修改版。

使用 GLSL 有如下好处：

- 具有跨平台的相容性，包括 Macintosh、Windows 和 Linux 等操作系统。
- 所有支援 OpenGL 着色语言的绘图卡，都可以用来编写着色器。
- 允许厂商为特定的绘图卡产生最佳化的代码。

### 着色器

计算机图形学领域中，着色器（英语：shader）是一种计算机程序，原本用于进行图像的浓淡处理（计算图像中的光照、亮度、颜色等），但近来，它也被用于完成很多不同领域的工作，比如处理CG特效、进行与浓淡处理无关的视频后期处理、甚至用于一些与计算机图形学无关的其它领域。

使用着色器在图形硬件上计算渲染效果有很高的自由度。尽管不是硬性要求，但目前大多数着色器是针对GPU开发的。GPU的可编程绘图管线已经全面取代传统的固定管线，可以使用着色器语言对其编程。构成最终图像的像素、顶点、纹理，它们的位置、色相、饱和度、亮度、对比度也都可以利用着色器中定义的算法进行动态调整。调用着色器的外部程序，也可以利用它向着色器提供的外部变量、纹理来修改这些着色器中的参数。

## 调试过程

1. 下载项目
2. `flutter run --profile`运行应用到真机
3. 执行项目，记录卡段，查看分析


## 优化原理

1. 代码优化，减少重复渲染次数、减少离屏渲染操作
2. 着色器优化，使用着色器预编译减少着色器渲染卡顿

## 着色器优化方案

### 使用SKIA-Metal预编译[dev分支]

Flutter 2.3.0-16.0.pre • channel dev 

支持iOS的Metal着色器预编译

### 使用Flutter-SKIA渲染引擎[不推荐]

使用stable分支，因为stable分支目前仅支持Android的着色器渲染；

所以需要使用支持skia渲染的flutter-engine，需要降低flutter的引擎版本，1.2以前应该都是支持flutter-skia渲染的

## 参考链接

- [没有metal的引擎](https://github.com/acoutts/flutter-engines-no-metal)
- [支持运行metal](https://github.com/flutter/flutter/issues/79298)
- [自动可扩展着色器预热-iOS](https://github.com/flutter/flutter/issues/32170)
- [iOS jank解决方案讨论](https://github.com/flutter/flutter/issues/60267)