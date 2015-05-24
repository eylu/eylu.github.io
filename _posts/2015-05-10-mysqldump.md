---
layout: default
title: Mysql 主从配置
date: 2015-05-10 10:49:00
category: Mysql
---

Mysql的主从复制至少是需要两个Mysql的服务，当然Mysql的服务是可以分布在不同的服务器上，也可以在一台服务器上启动多个服务。

主192.168.1.102
从192.168.1.104

# 安装

略

注意：确保主从服务器上的Mysql版本相同

# 配置

### 1、配置主服务器

在主服务器上,设置一个从数据库的账户,使用REPLICATION SLAVE赋予权限

```
[主服务器]# mysql -uroot -proot
[主服务器]mysql> CREATE USER 'slave001'@'192.168.1.104' IDENTIFIED BY '123456';
[主服务器]mysql> GRANT REPLICATION SLAVE ON *.* TO 'slave001'@'192.168.1.104' IDENTIFIED BY '123456' WITH GRANT OPTION;

 或者使用现有用户（不建议使用）

[主服务器]mysql> GRANT REPLICATION SLAVE ON *.* TO 'root'@'192.168.1.104' IDENTIFIED BY 'root' WITH GRANT OPTION;
[主服务器]mysql> flush privileges; #刷新授权
```

### 2、手动导入（同步）数据

把MySQL主服务器 192.168.1.102 中的数据库 demodump 导入到MySQL从服务器 192.168.1.104 中

```
[主服务器]mysql> flush tables with read lock;    #数据库只读锁定命令，防止导出数据库的时候有数据写入
[主服务器]# mysqldump -uroot -proot demodump > /home/demodump_bak.sql
[主服务器]mysql> unlock tables;   #解除锁定
```

导入数据库到MySQL从服务器 192.168.1.104 中

```
[从服务器]# mysql -uroot -p  #进入从服务器MySQL控制台
[从服务器]mysql> create database demodump;   #创建数据库
[从服务器]mysql> use demodump;    #进入数据库
[从服务器]mysql> source /home/demodump_bak.sql;  #此时已把 demodump_bak.sql 上传到从服务器，然后导入备份文件到从服务器数据库
```

### 3、配置主服务器 mysql 的 my.cnf 配置文件

```
[主服务器]# vi /etc/my.ini

将下面的配置项添加在 [mysqld] 下，如果已有，就检查更正即可

  server-id=1  #设置服务器id，为1表示主服务器，注意：如果原来的配置文件中已经有这一行，就不用再添加了。
  log_bin=mysql-bin  #启动MySQ二进制日志系统，注意：如果原来的配置文件中已经有这一行，就不用再添加了。
  binlog-do-db=demodump  #需要同步的数据库名，如果有多个数据库，可重复此参数，每个数据库一行
  binlog-ignore-db=mysql  #不同步mysql系统数据库
```

查看binlog是否已经开启，如果现实如下则说明已经开启。

```
[主服务器]mysql> show variables like "%log%";
=> log_bin | ON

[主服务器]mysql> show master status;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000001 |      107 | demodump     | mysql            |
+------------------+----------+--------------+------------------+

记住 File、Position字段，稍后同步数据时会用到。
```

### 4、配置从服务器 mysql 的 my.cnf 配置文件

```
[从服务器]# vi /etc/my.cnf   #在[mysqld]部分添加下面内容

  server-id=2   #配置文件中已经有一行server-id=1，修改其值为2，表示为从数据库
  log-bin=mysql-bin  #启动MySQ二进制日志系统，注意：如果原来的配置文件中已经有这一行，就不用再添加了。
  replicate-do-db=demodump   #需要同步的数据库名，如果有多个数据库，可重复此参数，每个数据库一行
  replicate-ignore-db=mysql  #不同步mysql系统数据库
  master-host=192.168.1.102  #主服务器ip
  master-user=slave001       #主服务器分配的用户名
  master-password=123456     #主服务器分配的用户名对应的密码
  master-port=3306           #主服务器mysql端口，默认3306
  master-connect-retry=10
:wq!    #保存退出

[从服务器]# service mysqld restart   #重启MySQL
```

### 5、同步数据

```
[从服务器]# mysql  -u root -p   #进入MySQL控制台
[从服务器] mysql> slave stop;   #停止slave同步进程
[从服务器] mysql> change master to master_host='192.168.1.102',master_user='slave001',master_password='123456',master_log_file='mysql-bin.000001',master_log_pos=107;
[从服务器] mysql> slave start;    #开启slave同步进程

这时，配置已完成。


这时，一直报错，ERROR 1200 (HY000): The server is not configured as slave; fix in config file or with CHANGE MASTER TO

于是查看日志
1.查看 从服务器 上的Mysql报错日志，有这么一句：
141009  6:06:29 [ERROR] Server id not set, will not start slave
意思是，slave的server-id没有设置。
那就奇怪了，我明明在配置文件里面指定了server-id的了，并且有重启mysql服务，难道不起效？
分别在主从上执行命令“show variables like 'server_id';”。

-------从机上面查看端口
mysql> SHOW VARIABLES LIKE 'server_id';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| server_id     | 0     |
+---------------+-------+
1 row in set (0.00 sec)
我就纳闷呢，本来设置的是2，此时却是0，
-------主机上面查看
mysql> SHOW VARIABLES LIKE 'server_id';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| server_id     | 1     |
+---------------+-------+
1 row in set (0.00 sec)
跟设置的一样。
既然参数文件不生效，就试试在数据库命令里面设置：
在从机 192.168.1.104 上执行命令
mysql > SET GLOBAL server_id=2;
再次在从机 192.168.1.104 上执行 slave start 和 show slave status，成功了。
注意！！！由于“SET GLOBAL server_id=2;”命令会在mysql服务重启后丢失，所以一定要写到配置文件里面。
但为什么我之前修改了my.cnf文件不起效？