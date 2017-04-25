---
title: GitHub上使用Hexo搭建博客
description: GitHub上使用Hexo搭建博客
categories: 技术
tags: Hexo
abbrlink: 26928
date: 2016-09-19 18:55:45
---
<!-- more -->
### [安装git](https://git-scm.com/downloads/)
### [安装nodejs](http://nodejs.cn/)
### [安装Hexo](https://hexo.io/zh-cn/docs/)

### 注册GitHub账号
### 建立跟用户名一样的仓库
![这里写图片描述](http://odr4ba8oz.bkt.clouddn.com/image/hexo/1.png)
### 配置SSH公钥
> Hexo远程部署是基于SSH方式，所以需要配置相应的SSH密钥
	
### 设置git邮箱和用户名

```bash
$ git config --global user.email "your email"
$ git config --global user.name "your name"
$ git config --list
```
	
### 生成密钥，回车三次，在当前用户目录下/.ssh文件中生成id_rsa和id_rsa.pub文件

```bash
$ ssh-keygen -t rsa -C "your email"
```
	
### 在GitHub中添加生成的SSH密钥
![这里写图片描述](http://odr4ba8oz.bkt.clouddn.com/image/hexo/2.png)
![这里写图片描述](http://odr4ba8oz.bkt.clouddn.com/image/hexo/3.png)
![这里写图片描述](http://odr4ba8oz.bkt.clouddn.com/image/hexo/4.png)
### 安装hexo-deployer-git

```bash
$ npm install hexo-deployer-git --save
```

### 修改Hexo项目的站点配置文件_config.yml，找到deploy

```
deploy:
  type: git
  repository: git@github.com:zheng-zy/zheng-zy.github.io.git
  branch: master
```
	
### 部署远程博客

```bash
hexo g
hexo d
```
	
### 成功显示
	
```bash
INFO  Deploy done: git
```

### 访问查看[zheng-zy.github.io](http://zheng-zy.github.io/)即可
