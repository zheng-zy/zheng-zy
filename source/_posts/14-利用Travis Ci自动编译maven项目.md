---
title: 利用Travis Ci自动编译maven项目
description: 自动化编译生成war
tags:
  - Maven
  - Travis-Ci
categories: tech
abbrlink: 
date: 2017-04-28 19:40:53
---

### 利用Travis Ci自动编译maven项目
    Travis Ci目前只支持github上面的项目，所以首先要有一个github账号，并且在上面建立一个项目，用来测试。
    项目建立之后要创建一个分支，这里取名为test，这里我们只演示从test分支打包之后上传到master分支。
    
### Travis Ci设置

* 登录并在My Repositories添加当前的github项目

* 设置相关选项参数

![image](http://git.oschina.net/zhengzy/md/raw/master/pic/travis-ci/1.png)


> Build only if .travis.yml is present：是只有在.travis.yml文件中配置的分支改变了才构建 

> Build pushes：当推送完这个分支后开始构建

### 在github上生成Access Token

* 点击github主页右上角小头像，setting选项
* 页面左边选项卡最下面Personal access tokens
* 勾选api权限，生成token
![image](http://git.oschina.net/zhengzy/md/raw/master/pic/travis-ci/2.png)
 
   
### test分支文件

* 拷贝一个可以正常编译的maven工程
* 编写.travis.yml文件，[yml格式校验](http://lint.travis-ci.org/)，内容如下
```.travis.yml
language: java

jdk:
  - oraclejdk8

cache:
  directories:
  - '$HOME/.m2/repository'
  
#before_install:
# - chmod +x gradlew

script:
  - mvn clean package -DskipTests=true
  
after_success:
  - cd ./target
  - git init
  - git config user.name "zhezhiyong"
  - git config user.email "zhezhiyong@163.com"
  - git add spring-boot-jsp.war
  - git commit -m "version 1"
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master

branches:
  only:
    - test

notifications:
  email: false
  
env:
  global:
  - GH_REF=github.com/zheng-zy/test-travis-ci-maven.git
```

* 提交test分支
```
git add *
git commit -m init
git push
```

### 验证结果

* 在github仓库查看打包的文件

### demo下载

* [github](https://github.com/zheng-zy/test-travis-ci-maven.git)



### 版权声明
作者：苏陌年
出处：http://www.zhengzy.top/ 
版权所有，欢迎保留原文链接进行转载：)
