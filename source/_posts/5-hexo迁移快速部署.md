---
title: hexo迁移快速部署
description: hexo迁移快速部署
categories: tech 
tags: Hexo
abbrlink: 55337
date: 2017-04-07 11:03:12
---

```
sudo apt-get install git
git config --global user.email "zhezhiyong@163.com"
git config --global user.name "zhezhiyong"
git config --list

ssh-keygen -t rsa -C "zhezhiyong@163.com"

sudo apt-get install nodejs
sudo apt-get install npm
npm install -g hexo-cli
hexo init blog
cd blog
npm install hexo-deployer-git --save

git clone https://github.com/tufu9441/maupassant-hexo.git themes/maupassant
npm install hexo-renderer-jade --save
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install hexo-renderer-sass --save

```


### 版权声明
作者：苏陌年
出处：http://www.zhengzy.top/ 
版权所有，欢迎保留原文链接进行转载：)
