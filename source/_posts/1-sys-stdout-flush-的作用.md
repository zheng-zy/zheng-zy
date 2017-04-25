---
title: sys.stdout.flush()的作用
description: python sys.stdout.flush()的作用
tags: Python
categories: tech
abbrlink: 32022
date: 2017-03-10 14:34:57
---

偶然间发现python sys.stdout.flush()代码，不知道是什么作用，出于好奇，研究了一下。看代码可以知道是控制台输出，清除内部I/O缓存，但是为什么需要如此？

### 简单的测试程序
```
#!/usr/bin/env python
# coding=utf-8

import time
import sys

for i in range(5):
    print i,
    #sys.stdout.flush()
    time.sleep(1)
```

在window下，注释`#sys.stdout.flush()`代码行跟没有注释输出相同。
在linux下，注释`#sys.stdout.flush()`代码行时，是在间隔5秒后一下子输出`1,2,3,4,5`，而不注释是每间隔一秒输出一个数字，所以在多线程编程的时候linux下，需要实时查看数据，需要设置`sys.stdout.flush()`控制台输出不缓存。
