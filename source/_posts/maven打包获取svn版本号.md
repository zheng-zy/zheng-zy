---
title: maven打包获取svn版本号
description: maven打包采用svn本身的版本号做版本控制，这样方便生产环境与开发环境版本比对，也可以在每次发布时查看当前版本，防止线上程序发布不成功而无法判断。
tags: Maven
categories: tech 
abbrlink: 59386
date: 2017-01-17 17:31:30
---
针对jar、war包，采用maven进行打包时，采用svn版本号进行版本管理，提供程序获取查看写入的版本号

## 获取svn版本号写入mainfest文件（jar、war）

### 采用buildnumber-maven-plugin插件

* pom.xml示例

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>war</packaging>

	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.4.3.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<scm>
	<connection>scm:svn:http://code.taobao.org/svn/demo_</connection>
	<developerConnection>scm:svn:http://code.taobao.org/svn/demo_</developerConnection>
	<tag>HEAD</tag>
	<url>http://code.taobao.org/svn/demo_</url>
	</scm>
	
	<build>
		<plugins>
			<plugin>
			  <groupId>org.codehaus.mojo</groupId>
			  <artifactId>buildnumber-maven-plugin</artifactId>
			  <version>1.4</version>
			  <executions>
				<execution>
				  <phase>validate</phase>
				  <goals>
					<goal>create</goal>
				  </goals>
				</execution>
			  </executions>
			  <configuration>
				<doCheck>false</doCheck>
				<doUpdate>true</doUpdate>
			  </configuration>
			</plugin>
			<plugin>
			  <groupId>org.apache.maven.plugins</groupId>
			  <artifactId>maven-jar-plugin</artifactId>
			  <version>3.0.2</version>
			  <configuration>
				<archive>
				  <manifestEntries>
					<alikoubeiserver-svn-version>${buildNumber}</alikoubeiserver-svn-version>
				  </manifestEntries>
				</archive>
			  </configuration>
			</plugin>
			<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-war-plugin</artifactId>
			<version>3.0.0</version>
			<configuration>
			  <archive>
				<manifest>
				  <addDefaultImplementationEntries>true</addDefaultImplementationEntries>
				</manifest>
				<manifestEntries>
					<alikoubeiserver-svn-version>${buildNumber}</alikoubeiserver-svn-version>
				</manifestEntries>
			  </archive>
			</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```

* 可以查看打包好的jar、war中的mainfest文件，已经添加了alikoubeiserver-svn-version

```
Manifest-Version: 1.0
Implementation-Title: demo
Implementation-Version: 0.0.1-SNAPSHOT
Built-By: User
Implementation-Vendor-Id: com.example
alikoubeiserver-svn-version: 7
Created-By: Apache Maven 3.3.9
Build-Jdk: 1.8.0_91
Implementation-URL: http://projects.spring.io/spring-boot/demo/
Implementation-Vendor: Pivotal Software, Inc.
```


## 获取svn版本号写入自定义文件（war）

### 采用maven-svn-revision-number-plugin插件

* 在src/main/resources目录下建立自定义文件version.txt，文件名后缀随意，内容如下

```
repository = ${prefix.repository} 
path = ${prefix.path} 
revision = ${prefix.revision} 
mixedRevisions = ${prefix.mixedRevisions} 
committedRevision = ${prefix.committedRevision} 
committedDate = ${prefix.committedDate} 
status = ${prefix.status} 
specialStatus = ${prefix.specialStatus} 
```

prefix对应pom.xml中的<prefix>prefix</prefix>可以修改

* pom.xml示例

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>war</packaging>

	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.4.3.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<resources>
			<resource>
				<directory>${basedir}/src/main/resources</directory>
				<filtering>true</filtering>
			</resource>
		</resources>
		<plugins>
			<plugin>  
				<groupId>com.google.code.maven-svn-revision-number-plugin</groupId>  
				<artifactId>maven-svn-revision-number-plugin</artifactId>  
				<version>1.7</version>  
				<configuration>  
					<verbose>true</verbose>  
					<entries>  
						<entry>  
							<prefix>prefix</prefix>  
							<depth>empty</depth>  
						</entry>  
					</entries>  
				</configuration>  
				<executions>  
					<execution>  
						<phase>validate</phase>  
						<goals>  
							<goal>revision</goal>  
						</goals>  
					</execution>  
				</executions>  
				<dependencies>  
					<dependency>  
						<groupId>org.tmatesoft.svnkit</groupId>  
						<artifactId>svnkit</artifactId>  
						<version>1.8.5</version>  
					</dependency>  
				</dependencies>  
			</plugin>  
			<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-war-plugin</artifactId>
			<version>3.0.0</version>
			<configuration>
					 <webResources>  
			            <resource>  
			                <directory>src/main/resources</directory>  
			                <filtering>true</filtering>  
			                <targetPath>WEB-INF/classes</targetPath>  
			            </resource>  
			        </webResources>   
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```

* 可以查看打包好的jar、war中resources下面查看version.txt文件，如下：

```
repository =  
path =  
revision = 7 
mixedRevisions = false 
committedRevision = 7 
committedDate = 2017-01-13 00:06:47 +0800 (Fri, 13 Jan 2017) 
status =  
specialStatus =  
```

### 参考

1. [buildnumber-maven-plugin](http://www.mojohaus.org/buildnumber-maven-plugin/usage.html)

