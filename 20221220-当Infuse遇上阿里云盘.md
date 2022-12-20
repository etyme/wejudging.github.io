---
title: 当Infuse遇上阿里云盘
date: 2022-12-20
tags: [infuse, 阿里云盘]
categories: 折腾
---

# 当Infuse遇上阿里云盘

## 阿里云盘介绍

阿里云盘是阿里巴巴全球资深技术团队倾力打造的一款个人网盘，主要功能为速度快、不打扰、够安全、易于分享，为C端用户提供存储备份及智能相册等服务的网盘产品。 最近一段时间阿里云盘可谓是火出了太阳系（还不是因为115太贵，百度网盘太慢），各种资源无论是影视作品还是素材都在阿里云盘上层出不穷。

## Infuse介绍

相信家中有NAS的朋友应该对媒体管理软件并不陌生，因为除了数据储存之外，我们购买NAS很大一部分需求是储存和播放电影和电视剧。 Infuse在本质上是一个媒体管理软件，拥有类似于Netflix这样的页面，如果你没有NAS ，你也可以使用Emby、Jellyfin、Plex第三方流媒体服务，或者连接Google Drive这种网络硬盘。

## 准备工作

* [注册阿里云网盘账号](https://pages.aliyundrive.com/mobile-page/web/beinvited.html?code=4e06ec7)
* 你是Infuse的订阅用户

## 浏览器获取refreshToken

* 列表项先通过浏览器（建议chrome）打开阿里云盘官网并登录：[www.aliyundrive.com](https://www.aliyundrive.com/drive/)
* 列表项登录成功后，按F12打开开发者工具，点击`Console`，输入以下代码，并回车

```js
JSON.parse(window.localStorage.getItem("token"))["refresh_token"];
```

* 控制台输出的一串字符就是你的refreshToken啦

[![WX20210818-142734](https://cdn.jsdelivr.net/gh/zxfccmm/image@master/20210714/WX20210818-142734.30bl1gsedly0.png)](https://cdn.jsdelivr.net/gh/zxfccmm/image@master/20210714/WX20210818-142734.30bl1gsedly0.png)

WX20210818-142734

## 使用docker创建阿里云盘的Webdav服务

项目地址：[zxbu/webdav-aliyundriver](https://github.com/zxbu/webdav-aliyundriver)

## docker run运行

```sh
docker run -d --name=webdav-aliyundriver --restart=always -p 8080:8080  -v /etc/localtime:/etc/localtime -v /etc/aliyun-driver/:/etc/aliyun-driver/ -e TZ="Asia/Shanghai" -e ALIYUNDRIVE_REFRESH_TOKEN="your refreshToken" -e ALIYUNDRIVE_AUTH_PASSWORD="admin" -e JAVA_OPTS="-Xmx1g" zx5253/webdav-aliyundriver

# /etc/aliyun-driver/ 挂载卷自动维护了最新的refreshToken，建议挂载
# ALIYUNDRIVE_AUTH_PASSWORD 是admin账户的密码，建议修改
# JAVA_OPTS 可修改最大内存占用，比如 -e JAVA_OPTS="-Xmx512m" 表示最大内存限制为512m
```

## docker-compose运行

```yaml
version: '3'
services:
  webdav-aliyundriver:
    image: zx5253/webdav-aliyundriver
    container_name: webdav-aliyundriver
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime
      - ./docker/etc/aliyun-driver/:/etc/aliyun-driver/
    ports:
      - "1234:8080"
    tty: true
    environment:
      - TZ=Asia/Shanghai
      - ALIYUNDRIVE_REFRESH_TOKEN=<change me>
      - ALIYUNDRIVE_AUTH_PASSWORD=<change me>
```

## 群晖教程

[群晖NAS高级服务 - 使用阿里云盘打造免费 WebDAV 服务](https://www.ioiox.com/archives/142.html)

## 参数说明

```
--aliyundrive.refresh-token
    阿里云盘的refreshToken，获取方式见下文
--server.port
    非必填，服务器端口号，默认为8080
--aliyundrive.auth.enable=true
    是否开启WebDav账户验证，默认开启
--aliyundrive.auth.user-name=admin
    WebDav账户，默认admin
--aliyundrive.auth.password=admin
    WebDav密码，默认admin
```

## 使用Infuse连接阿里云盘的Webdav服务

[![WX20210818-145254](https://cdn.jsdelivr.net/gh/zxfccmm/image@master/20210714/WX20210818-145254.5yx0sn4d3yo0.png)](https://cdn.jsdelivr.net/gh/zxfccmm/image@master/20210714/WX20210818-145254.5yx0sn4d3yo0.png)

WX20210818-145254

> 这里的用户名默认是admin，密码就是刚才设置的ALIYUNDRIVE_AUTH_PASSWORD

> 路径不选择默认是根目录`/`

## 连接成功

[![1](https://cdn.jsdelivr.net/gh/zxfccmm/image@master/20210714/1.6ypmc7596f80.png)](https://cdn.jsdelivr.net/gh/zxfccmm/image@master/20210714/1.6ypmc7596f80.png)

1

[![2](https://cdn.jsdelivr.net/gh/zxfccmm/image@master/20210714/2.56xqgzws6980.png)](https://cdn.jsdelivr.net/gh/zxfccmm/image@master/20210714/2.56xqgzws6980.png)

2

[![3](https://cdn.jsdelivr.net/gh/zxfccmm/image@master/20210714/3.4ssa681ndw80.png)](https://cdn.jsdelivr.net/gh/zxfccmm/image@master/20210714/3.4ssa681ndw80.png)

3

## 最后分享

最后分享一下我最近保存在阿里云盘上的一些资源

| 名称                 | 地址                                                                                   |
| ---------------------- | :--------------------------------------------------------------------------------------- |
| 爱情公寓4K全系列     | [https://www.aliyundrive.com/s/Rcb4VyVTNAH](https://www.aliyundrive.com/s/Rcb4VyVTNAH) |
| 漫威全系列           | [https://www.aliyundrive.com/s/xkBaheim6U6](https://www.aliyundrive.com/s/xkBaheim6U6) |
| 复联3+4 4K单文件60g+ | [https://www.aliyundrive.com/s/48vmGvstx9W](https://www.aliyundrive.com/s/48vmGvstx9W) |
| 火影忍者4K           | [https://www.aliyundrive.com/s/ig4niUCh8Vw](https://www.aliyundrive.com/s/ig4niUCh8Vw) |
| 迪士尼收藏版1        | [https://www.aliyundrive.com/s/VuzVnUdbaBz](https://www.aliyundrive.com/s/VuzVnUdbaBz) |
| 迪士尼收藏版2        | [https://www.aliyundrive.com/s/1GHf8nHHA23](https://www.aliyundrive.com/s/1GHf8nHHA23) |
| 迪士尼收藏版3        | [https://www.aliyundrive.com/s/zSRdbKiuj51](https://www.aliyundrive.com/s/zSRdbKiuj51) |
