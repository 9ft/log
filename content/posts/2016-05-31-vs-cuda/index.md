---
slug: vs-cuda
title: 让 VS2015 运行 CUDA 程序
date: 2016-05-31T13:24:50+0800
---
虽然 `Nsight` 支持 `Visual Studio 2015` 了, 但 `CUDA` 貌似还不能在 `VS2015` 下运行. 运行已有的 `VS2013` `CUDA` 程序会提示 `<Import>` 中声明的路径错误. 而且新建项目中并没有建立 CUDA 程序的选项.

![Error](2016-05-31-VS2015-CUDA-1.png "Error")

对比文件夹  
`C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V120\BuildCustomizations`  
`C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140\BuildCustomizations`

![Dir Compare](2016-05-31-VS2015-CUDA-2.png "Dir Compare")

发现缺少如下几个文件:

- CUDA 7.5.props
- CUDA 7.5.targets
- CUDA 7.5.xml
- msbldver.dll
- Nvda.Build.CudaTasks.v7.5.dll

将以上文件全部复制到 `C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140\BuildCustomizations`, 再次运行已有的 `CUDA` 程序.

会有如下提示:

>The Visual Studio 2015 platform toolset is not currently supported.  Please change the Platform Toolset property in the VC++ project properties under Configuration Properties | General.

提示说的很明白, 在项目属性中将 `平台工具集: Visual Studio 2015 (v140)` 改成 `Visual Studio 2013 (v120)` 即可.

运行已有的 `CUDA` 程序, 成功.
