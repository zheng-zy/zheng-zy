---
title: notepad++配置python环境
description: 有时候编写一个简单的python小程序运行，如果去开启idea实在麻烦，所以在文本编辑器中配置python开发环境
tags: 开发环境
categories: 技术
abbrlink: 46179
date: 2017-01-15 17:26:55
---

有时候编写一个小程序，使用python脚本，就几行代码，打开ide太麻烦，所以在notepad++中配置

### 配置python运行

* 点击菜单栏上面的运行拷贝以下命令，点击保存
```
cmd /k cd /d "$(CURRENT_DIRECTORY)" & python "$(FULL_CURRENT_PATH)" &ECHO & PAUSE & EXIT 
```

* 选取快捷键，这里采用Ctrl+R

### 配置制表符，换行

* 设置——》首选项——》language——》tab size——》设置为4，勾起Replace by space

* 视图——》显示符号——》显示空格和制表符

* 视图——》自动换行

### 安装Python Script插件

* 插件——》Plugin Manager——》搜索Python Script ——》install

### 安装compare插件

* 搜索compare ——》install
