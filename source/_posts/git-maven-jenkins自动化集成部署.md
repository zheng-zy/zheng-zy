---
title: git+maven+jenkins自动化集成部署
description: 如题，主要是为了减少运维人员的负担，避免手工部署遗漏。
tags: 运维
categories: tech
abbrlink: 53662
date: 2017-02-23 17:34:26
---
部署
设置tomcat manger页面ip访问权限
tomcat-8.0.36\webapps\manager\META-INF\content.xml
`<Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />`
一般配置到局域网
`<Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="192.168.*.*" />`
设置tomcat用户访问manager权限
```tomcat-user.xml
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<role rolename="tomcat"/>
<role rolename="role1"/>
<user username="tomcat" password="tomcat" roles="tomcat,manager-gui,manager-script,manager-jmx,manager-status"/>
<user username="both" password="both" roles="tomcat,role1"/>
<user username="role1" password="role1" roles="role1"/>
```
拷贝jenkins.war到tomcat webapps下面，启动tomcat
运行tomcat访问127.0.0.1:8080/jenkins

默认安装插件

安装自动化部署核心的两个插件
系统管理——》管理插件——》可选插件
Maven Integration plugin
Deploy to container Plugin

新建——》构建一个maven项目（安装Maven Integration plugin插件之后才有）
