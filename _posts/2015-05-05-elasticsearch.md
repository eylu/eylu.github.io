---
layout: default
title: ElasticsSearch Install
date: 2015-5-05 11:31:47
category: OTHER
---

# ElasticsSearch 全文检索

## 安装

1、下载安装 [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

2、配置 JDK 环境变量

  JAVA_HOME 设置为 /path/java/jdk
  Path 添加 %JAVA_HOME%/bin;
  classpath 设置为 .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar

3、安装 ElasticsSearch

  1) Windows 安装
  下载 [安装包](https://www.elastic.co/downloads/elasticsearch)
  执行 bin/elasticsearch.bat

  2) Centos 安装
  下载 [安装包](https://www.elastic.co/downloads/elasticsearch)
  执行 bin/elasticsearch

4、使用
