---
slug: deploy-githttp
title: 架设 HTTP 协议的 Git 服务器
date: 2016-08-01T14:25:24+0800
lastmod: 2017-01-17T22:24:51+0800
---
搭建 Git 服务器, 网上随便一搜, 教程都是 SSH 协议上的. **本文是搭建基于 `HTTP` 协议的 `Git` 服务器.**

`HTTP` 协议 `Git` 服务器的优点:
- 省去证书的操作, 只要记用户名密码.
- 搭建时不需要创建 `git` 用户. 使用用户 `www-data`.

# 安装
```shell
sudo apt-get install git gitweb nginx fcgiwrap
```
- git:
- gitweb: Git 自带 GitWeb 的 CGI 脚本
- fcgiwrap: 为 Nginx 提供 cgi 支持

# 配置

## git-http-backend

最简单的配置:

```nginx
server {
    listen       80;
    server_name  git.example.com;

    auth_basic            "Restricted";
    auth_basic_user_file  /etc/nginx/passwd;

    root /usr/share/nginx/git.example.com;

    location ~ (/.*) {
        include       fastcgi_params;
        fastcgi_param SCRIPT_FILENAME     /usr/lib/git-core/git-http-backend;
        fastcgi_param GIT_HTTP_EXPORT_ALL "";
        fastcgi_param GIT_PROJECT_ROOT    /usr/share/nginx/git.example.com;
        fastcgi_param PATH_INFO           $1;
        fastcgi_param REMOTE_USER         $remote_user;
        fastcgi_pass  unix:/var/run/fcgiwrap.socket;
    }
}
```

上面这段配置有很多坑...搞了好久才可用.

所有操作在用户 www-data 下进行的, 将仓库所有者设置为 www-data.

```shell
chown -R www-data:www-data repo.git
```

此时 `git.example.com` 中的仓库可以通过 `http://git.example.com/repo.git` 克隆.

push 提示权限不足的话, 使用命令设置一下权限:

```shell
chmod a+rw -R repo.git
```

## gitweb

配置 `/etc/gitweb.conf`:

```shell
$projectroot = "/usr/share/nginx/git.example.com";
```

```nginx
server {
  listen 80;
  server_name git.example.com;
  root /usr/share/gitweb;

  auth_basic "Restricted";
  auth_basic_user_file /etc/nginx/passwd;

  location /index.cgi {
    include fastcgi_params;
    fastcgi_param SCRIPT_NAME $uri;
    fastcgi_param GITWEB_CONFIG /etc/gitweb.conf;
    fastcgi_pass  unix:/var/run/fcgiwrap.socket;
  }

  location / {
    index index.cgi;
  }
}
```

GUI 美化: https://github.com/kogakure/gitweb-theme

## git-http-backend 与 gitweb

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name git.example.com;
    root /usr/share/gitweb;

    auth_basic "Restricted";
    auth_basic_user_file /etc/nginx/passwd;

    # static repo files for cloning over https
    location ~ ^.*\.git/objects/([0-9a-f]+/[0-9a-f]+|pack/pack-[0-9a-f]+.(pack|idx))$ {
        root /usr/share/nginx/git.example.com;
    }

    # requests that need to go to git-http-backend
    location ~ ^.*\.git/(HEAD|info/refs|objects/info/.*|git-(upload|receive)-pack)$ {
        root /usr/share/nginx/git.example.com;

        fastcgi_pass unix:/var/run/fcgiwrap.socket;
        fastcgi_param SCRIPT_FILENAME   /usr/lib/git-core/git-http-backend;
        fastcgi_param PATH_INFO         $uri;
        fastcgi_param GIT_PROJECT_ROOT  /usr/share/nginx/git.example.com;
        fastcgi_param GIT_HTTP_EXPORT_ALL "";
        fastcgi_param REMOTE_USER $remote_user;
        include fastcgi_params;
    }

    # send anything else to gitweb if it's not a real file
    try_files $uri @gitweb;
    location @gitweb {
        root /usr/share/gitweb;

        fastcgi_pass unix:/var/run/fcgiwrap.socket;
        fastcgi_param SCRIPT_FILENAME   /usr/share/gitweb/gitweb.cgi;
        fastcgi_param PATH_INFO         $uri;
        fastcgi_param GITWEB_CONFIG     /etc/gitweb.conf;
        include fastcgi_params;
    }
}
```

这段代码也超级多坑, 搞了更久...

这时的效果是:

- git.example.com 访问 Gitweb 网页,
- http://git.example.com/repo.git 进行 git 操作.

# 杂项

**使用 `htpasswd` 创建用户**

使用 `htpasswd` 创建用户名及设定密码.

```shell
htpasswd /etc/nginx/passwd user1
```

没有 `htpasswd` 命令时, 还有[替代方法](https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-nginx-on-ubuntu-14-04):

```shell
sudo sh -c "echo -n 'user:' >> /etc/nginx/passwd"
sudo sh -c "openssl passwd -apr1 >> /etc/nginx/passwd"
```
