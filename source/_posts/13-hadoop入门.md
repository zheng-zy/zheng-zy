---
title: hadoop入门1——hadoop集群环境搭建
description: hadoop集群环境搭建
tags:
  - Hadoop
categories: tech
abbrlink: 23656
date: 2017-04-17 19:28:51
---

### 前提
* vmware12
* centos64_6.5
* jdk6
* hadoop-1.2.1

### 环境搭建

```
[root@bogon share]# cd hadoop-1.2.1
[root@bogon conf]# cat masters
master
[root@bogon conf]# cat slaves
slave1
slave2
# 网络配置
# 三台
[root@bogon conf]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

192.168.32.129 master
192.168.32.130 slave1
192.168.32.131 slave2
# 三台分别
[root@bogon conf]# cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=master/slave1/slave2
[root@bogon conf]# hostname master/slave1/slave2
# 关闭防火墙
[root@bogon conf]# /etc/init.d/iptables stop
# 检查
[root@bogon jdk1.6.0_45]# iptables -L
# 关闭selinux
[root@bogon jdk1.6.0_45]# setenforce 0
[root@bogon jdk1.6.0_45]# getenforce 
Permissive
# 免密登录
[root@bogon src]# ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
26:91:07:be:89:c8:38:6a:3b:9e:2d:94:4a:3a:0c:fc root@master
The key's randomart image is:
+--[ RSA 2048]----+
|      .          |
|     . o         |
|      + .        |
| o . . =         |
|+ + . + S        |
|o*     o         |
|O..              |
|=++E             |
|.=o.             |
+-----------------
[root@bogon .ssh]# cat authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAxhohMfcWiny3CO151MgOqtvqyAZfHd4/ELuxbfEWOENZfwlyPy2joW1ZU7ya7WUMz11xNIphfXmejO8nLeDHhpC8Kh4DDp9HEQE7UpHZvGvUbIdGJS2Fv9LUuhZasosC8TbXchD01Mylp+sQqqDxedxiqcsCpot0KNu2qlu5pg5tA/ARGZOGdkxgXoe2zYCJ2fPj9ooBuuKKKkJiippKjEzYlomwl+racV5WeNKcf0TZWgWdrO62Hj2q3Q1w1ap1hC0sE+tbexJ9c52CvUP3Jvh8AppbjI8gp9euKzDY1LTNr4TuRsE06GOgxQOQ8fisYKz9+X5lyPWTkrbKcOuQRw== root@master
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAzlPfudPnKdPVbTFTzffF1w9ILB89eOyTvpfUThgly02HvnAYjyR3OsLidbtjtIQ3+dkmCkFA4W50I/NhA5mm2FA5VDen+PIsZvxheNCfyj9TV8oesIj99XaaU8ACE8rqr0NBrF6OuHTGV4b0UIfrazlXWs+flojDFiid5r/L7ZaflYSYDIDLR50vHPkdk9AV93DmDY8bL4skQTrczkxa6FOGqofYw2FyZeMX4sore1N/zv61ZbX6vRAe9ndUcB0fZ4Ikhs1bOwMYDM0Mji2hvjuCAq4gjHfZncSr/GeXVRQBulHRSSbnQX+GZyPfMIRT0VRULBNsAU/Hy/Angw3fqQ== root@slave1
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA5I1bVbDhoG3xto145cuMQ76RcuALlHZPmoe2KSROr9v+HVEmpXnqB9TD7geaP+UbkThM7HCPtOuRntN2N0BbqEKDle82qCqm5UDsRobysSBGY6XqanSDtDAC3gEC/de0Bup234W7EVsldpgzUWaRcDS5GksN2y++YhwbRfgApN/n31YJl2ZnYe6mvO2efWnAA0ZNt4Lm2/2STE2HcS6epI8r3ZaSo6rEKZrni/CJp/RiWVTbffzXdBauz4ILHoroF6CKBGCb+4c0Sf/HNRu6zrz8+H0mA8fH6rDhnSqqSBG86BkGWewruRiUycrt1kjr6XWH4XXJodqp8BxS7cBLBQ== root@slave2
[root@bogon .ssh]# chmod 600 authorized_keys
[root@bogon .ssh]# cp authorized_keys /mnt/hgfs/share/
[root@bogon .ssh]# cp /mnt/hgfs/share/authorized_keys ./
# hadoop配置
[root@bogon hadoop-1.2.1]# mkdir tmp
[root@bogon hadoop-1.2.1]# cd conf/
# 指定临时目录地址和路径，namenode的uri
[root@bogon conf]# cat core-site.xml 
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<!-- Put site-specific property overrides in this file. -->

<configuration>
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/usr/local/src/hadoop-1.2.1/tmp</value>
	</property>
	<property>
	        <name>fs.default.name</name>
	        <value>hdfs://192.168.32.129:9000</value>
	</property>

</configuration>
# 配置mapreduce的job.tracker
[root@bogon conf]# cat mapred-site.xml 
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<!-- Put site-specific property overrides in this file. -->

<configuration>
	<property>
		<name>mapred.job.tracker</name>
		<value>http://192.168.32.129:9001</value>
	</property>
</configuration>

# 数据在hdfs存储的副本数，默认为３
[root@bogon conf]# cat hdfs-site.xml 
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<!-- Put site-specific property overrides in this file. -->

<configuration>
	<property>
		<name>dfs.replication</name>
		<value>3</value>
	</property>
</configuration>
# 配置hadoop-env.sh在末尾添加
[root@bogon conf]# cat hadoop-env.sh 
# The scheduling priority for daemon processes.  See 'man nice'.
# export HADOOP_NICENESS=10
export JAVA_HOME=/usr/local/src/jdk1.6.0_45
# 一式三份
[root@bogon ~]# cp -r /mnt/hgfs/share/hadoop-1.2.1 /usr/local/src/
# 启动hadoop集群
# 第一次启动先格式化
[root@bogon bin]# ./hadoop namenode -format
17/04/17 05:36:58 INFO namenode.NameNode: STARTUP_MSG: 
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = master/192.168.32.129
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 1.2.1
STARTUP_MSG:   build = https://svn.apache.org/repos/asf/hadoop/common/branches/branch-1.2 -r 1503152; compiled by 'mattf' on Mon Jul 22 15:23:09 PDT 2013
STARTUP_MSG:   java = 1.6.0_45
************************************************************/
17/04/17 05:36:58 INFO util.GSet: Computing capacity for map BlocksMap
17/04/17 05:36:58 INFO util.GSet: VM type       = 64-bit
17/04/17 05:36:58 INFO util.GSet: 2.0% max memory = 1013645312
17/04/17 05:36:58 INFO util.GSet: capacity      = 2^21 = 2097152 entries
17/04/17 05:36:58 INFO util.GSet: recommended=2097152, actual=2097152
17/04/17 05:36:58 INFO namenode.FSNamesystem: fsOwner=root
17/04/17 05:36:58 INFO namenode.FSNamesystem: supergroup=supergroup
17/04/17 05:36:58 INFO namenode.FSNamesystem: isPermissionEnabled=true
17/04/17 05:36:58 INFO namenode.FSNamesystem: dfs.block.invalidate.limit=100
17/04/17 05:36:58 INFO namenode.FSNamesystem: isAccessTokenEnabled=false accessKeyUpdateInterval=0 min(s), accessTokenLifetime=0 min(s)
17/04/17 05:36:58 INFO namenode.FSEditLog: dfs.namenode.edits.toleration.length = 0
17/04/17 05:36:58 INFO namenode.NameNode: Caching file names occuring more than 10 times 
17/04/17 05:36:59 INFO common.Storage: Image file /usr/local/src/hadoop-1.2.1/tmp/dfs/name/current/fsimage of size 110 bytes saved in 0 seconds.
17/04/17 05:36:59 INFO namenode.FSEditLog: closing edit log: position=4, editlog=/usr/local/src/hadoop-1.2.1/tmp/dfs/name/current/edits
17/04/17 05:36:59 INFO namenode.FSEditLog: close success: truncate to 4, editlog=/usr/local/src/hadoop-1.2.1/tmp/dfs/name/current/edits
17/04/17 05:36:59 INFO common.Storage: Storage directory /usr/local/src/hadoop-1.2.1/tmp/dfs/name has been successfully formatted.
17/04/17 05:36:59 INFO namenode.NameNode: SHUTDOWN_MSG: 
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at master/192.168.32.129
************************************************************/
[root@bogon bin]# ./start-all.sh 
namenode running as process 5086. Stop it first.
slave1: datanode running as process 4949. Stop it first.
slave2: starting datanode, logging to /usr/local/src/hadoop-1.2.1/libexec/../logs/hadoop-root-datanode-slave2.out
master: secondarynamenode running as process 5245. Stop it first.
starting jobtracker, logging to /usr/local/src/hadoop-1.2.1/libexec/../logs/hadoop-badou-jobtracker-bogon.out
slave1: tasktracker running as process 5036. Stop it first.
slave2: starting tasktracker, logging to /usr/local/src/hadoop-1.2.1/libexec/../logs/hadoop-root-tasktracker-slave2.out
# 分别在三台机器上输入jps验证hadoop启动是否成功
[root@bogon bin]# jps
5245 SecondaryNameNode
5086 NameNode
5673 Jps
5595 JobTracker
# 简单使用
# 查看当前hadoop下文件s
[root@bogon bin]# ./hadoop fs -ls /
# 上传passwd文件
[root@bogon bin]# ./hadoop fs -put /etc/passwd /
[root@bogon bin]# ./hadoop fs -ls /
Found 1 items
-rw-r--r--   3 root supergroup       1432 2017-04-17 05:40 /passwd
# 查看上传文件内容
[root@bogon bin]# ./hadoop fs -cat /passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin

```


### 版权声明
作者：苏陌年
出处：http://www.zhengzy.top/ 
版权所有，欢迎保留原文链接进行转载：)
