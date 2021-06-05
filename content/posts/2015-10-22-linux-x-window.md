---
slug: linux-x-window
title: Linux 不能进入 x window
date: 2015-10-22T08:00:00+0800
---
老师给我分配一个 `GPU` 工作站, 刚开机好像我把 `/etc/X11/xorg.conf` 搞坏了, 导致不能进入 x window, 可把我急坏了, 按照[这里][1]的步骤做下来就好, 发现自己太菜了, 记录下来.

解决方法就是重新生成一个 `/etc/X11/xorg.conf` 文件.

## 生成新的 `/etc/X11/xorg.conf`

### 1 以超级用户的身份建立初始的配置文件:

```shell
Xorg -configure
```

### 2 测试现存的配置文件(不建议用这个, 会黑屏, 不知道情况的会抓狂)

```shell
Xorg -config xorg.conf.new
# 不建议使用
```

通过 `retro` 选项使用旧的模式:

```shell
Xorg -config xorg.conf.new -retro
```

使用组合键 `Ctrl+Alt+Fn` 回到终端.

### 3 将新的 `xorg.conf` 放到他该在的位置.

```shell
# cp xorg.conf.new /etc/X11/xorg.conf
```

如此简单!

[1]:https://www.freebsd.org/doc/zh_CN.UTF-8/books/handbook/x-config.html "配置 X11"
