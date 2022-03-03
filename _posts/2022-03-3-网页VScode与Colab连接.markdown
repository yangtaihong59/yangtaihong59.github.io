---
layout: post
title:  网页VScode与Colab连接
subtitle: 终极白嫖大法
date:   2022-03-3
categories: markdown
tags: [深度学习]
---
## 1、简介

本文介绍了一种Colab 连接 Google 云盘 并使用VScode 调试程序的方法，该方法可以使用VScode的所有功能进行更加方便的调试工作。

## 2、操作步骤

### 连接 Google 云盘

```python
from google.colab import drive
drive.mount('/content/drive/')
```

### 安装必要环境

```python
!pip install -q colabcode
from colabcode import ColabCode
```

### 关闭所有ngrok服务

```
!killall ngrok
```

### 放置授权令牌

```python
!ngrok authtoken ##你的令牌
```

注意此令牌首次配置时没有，先不用运行此段代码，在下一步给出

### 开启网页VScode服务

```python
ColabCode(port=10000, password='helloworld')
```

1、本行代码运行后会给出一个三个链接，点击第一个

2、进入后注册账号，然后就可以找到你的授权令牌，把授权令牌填入上一步

3、然后中心运行本步骤代码

4、点击第一个链接，输入 password 即可

## 3、总结

正常情况下按上述步骤完成设置后即可看到网页VScode打开了，在这个页面可以使用Vscode的文件管理系统和使用terminal。
