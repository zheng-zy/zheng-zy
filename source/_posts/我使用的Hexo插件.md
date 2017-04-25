---
title: 我使用的Hexo插件
description: hexo一些好用的插件推荐
tags: Hexo
categories: tech 
abbrlink: 18765
date: 2016-09-21 14:19:57
---
<!-- more -->
### 根据文章标题生成静态页面的链接(hexo-abbrlink)
#### 安装
```
npm install hexo-abbrlink --save
```
#### 修改站点配置文件_config.yml
```
# permalink: :year/:month/:day/:title/
permalink: posts/:abbrlink/
```
#### 源码地址

https://github.com/rozbo/hexo-abbrlink
