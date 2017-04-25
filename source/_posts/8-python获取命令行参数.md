---
title: python获取命令行参数
description: python获取命令行参数
tags:
  - Python
categories: tech
abbrlink: 39140
date: 2017-04-08 10:39:47
---

### argparse 获取命令行参数
```
#!usr/bin/env python
# coding=utf-8

import argparse

parser = argparse.ArgumentParser()
parser.add_argument('--data_dir', type=str, default='/tmp/data', help='Directory for storing data')
FLAGS = parser.parse_args()
print FLAGS.data_dir
```
#### 使用
```
python xxx.py --data_dir=data
```
#### 输出

> data


### 版权声明
作者：苏陌年
出处：http://www.zhengzy.top/ 
版权所有，欢迎保留原文链接进行转载：)
