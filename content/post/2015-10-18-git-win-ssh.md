---
slug: git-win-ssh
title: Windows 下使用 Git 的配置
date: 2015-10-18T08:00:00+0800
---
`Windows` 下 Git 客户端我用的是 [`PortableGit`][Releases · msysgit/msysgit], 只要将 `PortableGit\bin` 丢进环境变量里就可以在命令行里用了.



`ssh key` 的文件夹 `.ssh` 不放在`用户目录`下, 而是放在 `PortableGit` 的目录下, 这样配置留在程序文件夹内, 以后就不需要刻意备份了. 然后 `win` 下也能愉快的使用 `Git` 了.


[Releases · msysgit/msysgit]:https://github.com/msysgit/msysgit/releases
