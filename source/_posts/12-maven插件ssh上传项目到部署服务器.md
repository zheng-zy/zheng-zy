---
title: maven插件ssh上传项目到部署服务器
description: maven插件ssh上传项目到部署服务器
tags:
  - Maven
categories: tech
abbrlink: 10398
date: 2017-04-17 15:43:30
---
### pom
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-antrun-plugin</artifactId>
    <executions>
        <execution>
            <id>ftp</id>
            <phase>install</phase>
            <configuration>
                <tasks>
                    <scp todir="root@192.168.97.200:/home/www/src/demo_service"
                         sftp="true" port="22" trust="true" password="root123456"
                         failonerror="false" verbose="true" passphrase="">
                    <fileset dir="${project.build.directory}/demo_service">
                    </fileset>
                    </scp>
                </tasks>
            </configuration>
            <goals>
                <goal>run</goal>
            </goals>
        </execution>
    </executions>
    <dependencies>
        <dependency>
            <groupId>org.apache.ant</groupId>
            <artifactId>ant-jsch</artifactId>
            <version>1.9.4</version>
        </dependency>
    </dependencies>
</plugin>
```
### 部署
```
mvn clean install
```
### 版权声明
作者：苏陌年
出处：http://www.zhengzy.top/ 
版权所有，欢迎保留原文链接进行转载：)
