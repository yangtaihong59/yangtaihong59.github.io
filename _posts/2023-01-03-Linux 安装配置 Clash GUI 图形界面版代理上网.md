---
layout: post
title:  Linux 安装配置 Clash GUI 图形界面版代理上网
subtitle: 安装及环境配置
date:   2023-01-03
categories: Linux
tags: [Linux,环境配置]
---

# Linux 安装配置 Clash GUI 图形界面版代理上网

## 资源准备

下载 [Clash GUI](https://github.com/Fndroid/clash_for_windows_pkg/releases) 版
```
https://github.com/Fndroid/clash_for_windows_pkg/releases
```
选择 Clash.for.Windows-0.00.00-x64-linux.tar.gz 命名的版本下载 

## 安装 Clash

### 解压文件

```
tar -xzvf Clash.for.Windows-0.00.00-x64-linux.tar.gz 
```

### 重命名
```
mv 'Clash for Windows-0.00.00-x64-linux' Clash
```

## 配置系统环境

### 配置系统代理

```
sudo chmod 0666 /etc/environment
sudo chmod 0444 /etc/environment
vim /etc/environment
```
vim 黏贴至文件后边
``` vim
http_proxy=http://127.0.0.1:7890/

https_proxy=http://127.0.0.1:7890/

ftp_proxy=http://127.0.0.1:7890/

HTTP_PROXY=http://127.0.0.1:7890/

HTTPS_PROXY=http://127.0.0.1:7890/

FTP_PROXY=http://127.0.0.1:7890/

```

### 配置 DNS 服务器

```
vim /etc/resolv.conf
```

代理到国外IP的话，需要调整一下DNS
``` vim
nameserver 8.8.8.8
```

### 重启

## 运行 Clash

```
cd clash

./cfw
```