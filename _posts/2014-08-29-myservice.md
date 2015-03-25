---
layout: default
title: 我的 Service 示例
date: 2014-08-24 15:18:00
category: linux
---

service --status-all

service --status-all | grep ntpd

service --status-all | less

service httpd status

列出所有服务启动级别：

chkconfig --list

列出服务和他们对应的端口：

netstat -tulpn

VIM
