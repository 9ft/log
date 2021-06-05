---
slug: bingwallpaper
title: 设置 Bing 主页背景为系统桌面
date: 2016-11-27T13:41:00+0800
lastmod: 2021-06-05T20:23:00+0800
toc: true
---

每天更换主页背景是 bing.com 一大特色。

macOS 下可以通过简单脚本将图片设置为桌面背景。

**项目仓库 [9ft/BingWallpaper](https://github.com/9ft/BingWallpaper)**

## 编写脚本

1. 读取本地目录，检查今日图片是否获取过
2. curl 获取网页源码
3. 正则匹配出背景图
4. 使用 osascript 设置系统桌面背景

[BingWallpaper.sh](https://github.com/9ft/BingWallpaper/blob/master/BingWallpaper.sh)
```shell
#!/bin/sh
localDir="/Users/$USER/Pictures/BingWallpaper"
filenameRegex=".*"$(date "+%Y-%m-%d")".*jpg"
log="$localDir/bin/log.log"

if [ ! -d "$localDir" ]; then
    mkdir "$localDir"
fi

findResult=$(find $localDir -regex $filenameRegex)

if [ ! -n "$findResult" ]; then
    baseUrl="cn.bing.com"
    html=$(curl -L $baseUrl)

    imgurl=$(expr "$(echo "$html" | grep "&amp;rf")" : '.*href=\"\(\/th\?id=OHR\.[A-Za-z0-9]*\_ZH\-CN[0-9]*\_1920x1080\.jpg\).*')
    echo img $imgurl
    filename=$(expr "$imgurl" : '.*OHR\.\(.*\)')
    echo $filename
    localpath="$localDir/$(date "+%Y-%m-%d")-$filename"
    curl -o $localpath $baseUrl/$imgurl

    des=$(expr "$(echo "$html" | grep "id=\"sh_cp\" class=\"sc_light\"")" : '.*id=\"sh_cp\".*title=\"\(.*\)\" aria-label=\"主页图片信息\"')

    osascript -e "                              \
        tell application \"System Events\" to   \
            tell every desktop to               \
                set picture to \"$localpath\""
    osascript -e "display notification \"$des\" with title \"BingWallpaper\""
    echo "$(date +"%Y-%m-%d %H:%M:%S") Downloaded $filename" >> $log
else
    echo "$(date +"%Y-%m-%d %H:%M:%S") Exist" >> $log
    exit 0
fi
```

## 定时执行

使用 crontab 开机启动 (唤醒时不会运行)

```
@reboot sleep 60; sh ~/Pictures/BingWallpaper/bin/BingWallpaper.sh
```

或 使用 crontab 定时运行 (每小时)

```
* */1 * * * sh ~/Pictures/BingWallpaper/bin/BingWallpaper.sh
```
