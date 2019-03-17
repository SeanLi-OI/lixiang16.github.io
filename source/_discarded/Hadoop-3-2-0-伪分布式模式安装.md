title: Hadoop 3.2.0 伪分布式模式安装
author: Lixiang
tags:
  - Distributed System
  - Hadoop
categories:
  - Distributed System
date: 2019-03-17 13:36:00
---
# 简介
----------------------
* 本文基于Ubuntu16.04和Hadoop3.2.0从零搭建单机伪分布式系统，并成功运行Hadoop官方wordcount示例。

# 系统环境
----------------------
* 腾讯云标准型S2-Ubuntu Server 16.04.1 LTS 64位

# 前置环境搭建
----------------------

## 搭建Java SE Development Kit环境
----------------------

### 下载Java SE Development Kit
* 官网下载地址：[Java SE Development Kit 8 Downloads](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* 本文下载版本：jdk-8u201-linux-x64.tar.gz

### 安装Java SE Development Kit
* 解压jdk-8u201-linux-x64.tar.gz并移动文件夹
  ```
  $ mkdir /usr/lib/jvm
  $ tar -zxvf jdk-8u201-linux-x64.tar.gz -C /usr/lib/jvm
  ```
* 编辑.bashrc，添加环境变量
  ```
  export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_201
  export JRE_HOME=${JAVA_HOME}/jre
  export CLASSPATH=.:{JAVA_HOME}/lib:${JRE_HOME}/lib
  export PATH=${JAVA_HOME}/bin:$PATH
  ```
* 测试java环境配置

  ```
  $ java -version
  java version "1.8.0_201"
  Java(TM) SE Runtime Environment (build 1.8.0_201-b09)
  Java HotSpot(TM) 64-Bit Server VM (build 25.201-b09, mixed mode)

  ```
  

# 搭建Hadoop环境
----------------


   ## 下载Apache Hadoop
    * 官网下载地址：[Apache Hadoop](https://hadoop.apache.org/releases.html)
    * 本文下载版本(from tuna)：[hadoop-3.2.0.tar.gz](https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/hadoop-3.2.0/hadoop-3.2.0.tar.gz)
    
  ## 安装Apache Hadoop
  * 解压hadoop-3.2.0.tar.gz，移动并进入文件夹
    ```
    $ mkdir /usr/local/hadoop
    $ tar -zxvf hadoop-3.2.0.tar.gz -C /usr/local/hadoop
    $ cd /usr/local/hadoop/hadoop-3.2.0
    ```
  * 配置Hadoop内部环境变量
    * 编辑 etc/hadoop/hadoop-env.sh
    ```
    export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_201
    # set to the root of your Java installation
    export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_201
    ```
    * 测试环境配置

      ```
      $ bin/hadoop version
      Hadoop 3.2.0
    Source code repository https://github.com/apache/hadoop.git -r e97acb3bd8f3befd27418996fa5d4b50bf2e17bf
    Compiled by sunilg on 2019-01-08T06:08Z
    Compiled with protoc 2.5.0
    From source with checksum d3f0795ed0d9dc378e2c785d3668f39
    This command was run using /usr/local/hadoop/hadoop-3.2.0/share/hadoop/common/hadoop-common-3.2.0.jar

      ```
  * 编辑.bashrc，添加环境变量
  ```
  export HADOOP_HOME=/usr/local/hadoop/hadoop-3.2.0
  export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
  ```
  * 配置 etc/hadoop/core-site.xml
  ```
  <configuration>
      <property>
          <name>fs.defaultFS</name>
          <value>hdfs://localhost:9000</value>
      </property>
  </configuration>
  ```
  * 配置 etc/hadoop/hdfs-site.xml
  ```
  <configuration>
      <property>
          <name>dfs.replication</name>
          <value>1</value>
      </property>
  </configuration>
  ```


# 测试运行Hadoop fuck
------------------------
* 格式化namenode
```
$ bin/hdfs namenode -format
```
* 启动namenode和datanode
```
$ sbin/start-dfs.sh
```
* 此时可以通过浏览器访问namenode
```
http://localhost:9870/
/*CVM请注意打开9870端口*/
```
* 上传本机文件到HDFS（[HDFS常用命令](https://www.cnblogs.com/m-study/p/8343169.html)） 
```
$ bin/hdfs dfs -mkdir input
$ bin/hdfs dfs -put /home/input.txt input
```
* 运行官方示例wordcount
```
$ hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.0.jar wordcount  /input/input.txt  /output
```
* 查看结果
```
$ bin/hdfs dfs -cat output/*
```
* 停止namenode和datanode
```
$ sbin/stop-dfs.sh
```