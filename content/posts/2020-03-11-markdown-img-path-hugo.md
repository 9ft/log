---
slug: markdown-img-path-hugo
title: Hugo博文图片路径
date: 2020-03-11T23:19:00+08:00
---
每次切换博客系统，搞系统的时间总是比写博客的时间要长，hugo也不例外，这次说一下hugo下博文图片的路径配置。之前配置的是将博文图片放在 `static/posts` 下建立和博文一样的文件夹存储图片，hugo生成静态网站时会将 static 文件夹拷贝到网站根目录，图片地址刚好在文章url的下一级，看起来很合理，但在本地编辑含图文章时特别不方便，只能通过hugo server的实时预览查看效果。

我的目标是除了线上正常显示图片外，本地编辑也要正常显示图片方便编辑，本地我常用的markdown编辑器是Typora，笔记用的是Bear，iOS端用的是iA Writer。通常来说markdown里引用图片路径时是以当前`.md`为基准的相对路径，每个软件配置的图片目录都略有差异。

为了最大化的使引用图片方便，实现文章中直接使用图片名即可引用图片，如`![](image.png)`，达到这样的效果需要`.md`和`image.png`在同一目录下，但以下这种方式生成网站时会引用不到图片。此时生成网页时图片查找路径是`https://example.com/posts/2020-01-01-post/image.png`，会导致加载不到图片。

```
content
└── posts
    ├── 2020-01-01-post.md   // <- https://example.com/posts/2020-01-01-post
    └── image.png            // <- https://example.com/posts/image.png
```

考虑将图片放到文章名的下一级目录中就可以解决这个问题，这样也符合Bear中导出的格式。这样设置时，`.md`中的引用路径就得是`2020-01-01-post/image.png`，这样看起来貌似没什么问题，但如果文章中配置了`slug: custom`, 此时生成网页时图片查找路径是`https://example.com/posts/custom/image.png`，会导致加载不到图片。

```
content
└── posts
    ├── 2020-01-01-post.md  // <- https://example.com/posts/2020-01-01-post
    └── 2020-01-01-post
        └── image.png       // <- https://example.com/posts/2020-01-01-post/image.png
```

经过我试验，slug是可以对所在文件夹图片生效的，此时生成网页时图片查找路径是`https://example.com/posts/custom/image.png`，可以加载图片，但未设置slug时就加载不了了，设置了slug时如下

```
content
└── posts
    └── 2020-01-01-post
        ├── post.md     // slug: custom <- https://example.com/posts/custom
        └── image.png   // <- https://example.com/posts/custom/image.png
```

似乎是个不可调和矛盾，如果让图片跟随slug就得和md文件在同一级，但md文件会使url增加一个层级。

还好有两个特殊的`.md`文件: `index.md`, `_index.md`，根据文档[Page Bundles](https://gohugo.io/content-management/page-bundles)介绍，index.md 用于树叶节点，_index.md用在树枝节点，如果文章命名为 index.md，那么生成的网页将为当前文件夹名，问题解决，文件目录如下

```
content
└── posts
    └── 2020-01-01-post
        ├── index.md    // <- https://example.com/posts/2020-01-01-post
        └── image.png   // <- https://example.com/posts/2020-01-01-post/image.png
```

