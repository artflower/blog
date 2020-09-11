---
layout: post
title: "MariaDB Connect Storage Engine - MSSQL ODBC link"
date: 2020-09-11 22:30:06 +0900
categories: [MariaDB]
tags: [MariaDB, MySQL, MSSQL, ODBC]
comments: true
---
# MariaDB Connect Storage Engine - MSSQL ODBC 연결

MariaDB의 Connect 스토리지 엔진을 이용해서 MSSQL 서버에 ODBC 연결을 할 수 있습니다.
먼저 ODBC를 설치하고 ODBC 설정을 합니다.
Linux는 CentOS7입니다.

```
# yum install epel-release
# yum install freetds
# yum install unixODBC

# cat /etc/odbcinst.ini
[FreeTDS]
Description = FreeTDS Driver
Driver = /usr/lib64/libtdsodbc.so.0
Setup = /usr/lib64/libtdsS.so.2
FileUsage = 1
CPTimeout = 5
CRReuse = 5

# cat /etc/odbc.ini
[NEOCLOVA]
driver=FreeTDS
server=127.0.0.1
port=1433
database=ssauravy
client_charset = UTF-8
tds_version = 8.0
```
MariaDB를 설치한 후에 Connect 스토리지엔진을 설치하고 MSSQL 서버의 Table에 연결을 해서 사용합니다.
```
> install soname 'ha_connect';

> CREATE TABLE t_test1 ENGINE=CONNECT, TABLE_TYPE=ODBC, TABNAME='dbo.test' CONNECTION='Driver=FreeTDS;Server=127.0.0.1;Database=dbtest;UID=neoclova;PWD=welcome;Port=1433';
> CREATE TABLE t_test2 ENGINE=CONNECT TABLE_TYPE=ODBC TABNAME='dbo.test' CONNECTION='DSN=NEOCLOVA;UID=neoclova;PWD=welcome';
```
