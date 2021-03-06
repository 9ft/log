---
slug: mac-vim
title: Mac 下使用 Vim
date: 2016-08-23T14:38:24+0800
toc: true
---
[折腾完终端](/技术/2016/08/23/mac-terminal.html), 继续折腾另一个装B利器 `Vim`.

以前用过几个配置: [guochunyang/vim](https://github.com/guochunyang/vim), [Gvim/Vim 配置好了常用插件 (Windows 与 Linux 通用)](http://www.oschina.net/code/snippet_574132_13357).

也尝试过自己配置 Vim, 不过已经有好东西, 何必自造轮子. 现在发现一个更牛的配置 [`spf13-vim : Steve Francia's Vim Distribution`](https://github.com/spf13/spf13-vim).

## 准备

使用这个配置需要用 [vim with lua](https://github.com/Shougo/neocomplete.vim#requirements). Win 下习惯了 gVim, Mac 上继续使用带 GUI 的 MacVim, 如果本机已经安装了 Vim / MacVim, 需要删除重新按说明安装.

## 安装

参见 [`Linux, *nix, Mac OSX Installation`](https://github.com/spf13/spf13-vim#linux-nix-mac-osx-installation).

配置带了一个很牛的自动填充插件 [eocomplete](https://github.com/Shougo/neocomplete.vim) 和 插件管理 Vundle, 安装完配置启动 Vim / MacVim 观察效果.

## 配置

### 字体

1. 输入 `:set gfn=*` 弹出系统的字体选择框.
2. 输入 `:set gfn` 查看现在的字体配置。
3. 将得到的结果填入到 `~/.vimrc.local`.

举个例子, 我选择了 `Source Code Powerline Light`, `:set gfn` 得到 `guifont=Sauce Code Powerline Light:h14`, 在 `~/.vimrc.local` 填入 `set gfn=Sauce\ Code\ Powerline\ Light:h14`. *字体名跟自己想的并不一样, 开始直接把 `set gfn=Source\ Code\ Powerline\ Light:h14` 写到 `~/.vimrc.local` 怎么都不生效, 用上面的办法就能避免这样的问题.*

安装了 [Powerline-patched fonts](https://github.com/powerline/fonts) 字体后可以启用 [Airline](https://github.com/spf13/spf13-vim#airline). `~/.vimrc.before.local` 中添加

```shell
let g:airline_powerline_fonts=1
```

![Vim with Airline](airline.png "Vim with Airline")

### 配色

spf13-vim 很贴心自带了 solarized 配色, 使用 `:color solarized` 配置.

## 结果图

经过一翻折腾, 效果太棒了, 这下炫酷极了!

![MacVim](mac-vim.png "MacVim")

好了, 去写算法吧...
