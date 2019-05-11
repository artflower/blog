---
layout: post
title: "MariaDB / MySQL / Percona : Docker 이미지 비교"
date: 2019-05-11 16:30:06 +0900
comments: true
---
# MariaDB / MySQL 8.0 / Percona : Docker 이미지 비교

severalnines에서 MariaDB / MySQL / Percona 서버들에 대한 Docker 이미지를 비교 한게 있어서 살펴 봤습니다.
https://severalnines.com/blog/popular-docker-images-mysql-and-mariadb-server

해당 글을 보다가 좀 더 궁금한 사항들이 있어서 버전별로 좀 더 확인을 해 봤습니다.

repository | &nbsp;version(tag)&nbsp; | &nbsp;size&nbsp; | &nbsp;OS&nbsp;
--|--|--|--
mariadb | 10.4 | 392MB | Ubuntu 18.04
mariadb | 10.3 (latest) | 381MB | Ubuntu 18.04
mariadb | 10.2 | 378MB | Ubuntu 18.04
mariadb | 10.1 | 391MB | Ubuntu 18.04
mariadb | 10.0 | 352MB | Ubuntu 16.04
mariadb | 5.5 | 352MB | Ubuntu 14.04
mariadb/columnstore | 1.2 | 1.15GB | CentOS 7
percona | 8.0 | 727MB | CentOS 7
percona | 5.7 (latest) | 583MB | CentOS 7
percona | 5.6 | 421MB | CentOS 7
percona | 5.5 | 294MB | Debian 9
mysql | 8.0 (latest) | 443MB | Debian 9
mysql | 5.7 | 373MB | Debian 9
mysql | 5.6 | 256MB | Debian 9
mysql | 5.5 | 205MB | Debian 9
mysql/mysql-server | 8.0 | 289MB | Oracle Linux 7
bitnami/mariadb | 10.1 (latest) | 261MB | Debian 9
bitnami/mysql | 5.7 (latest) | 287MB | Debian 9
centos/mariadb-102-centos7 | 10.2 | 456MB | CentOS 7
centos/mariadb-101-centos7 | 10.1 | 476MB | CentOS 7
centos/mariadb-100-centos7 | 10.0 | 439MB | CentOS 7
centos/mysql-80-centos7 | 8.0 | 569MB | CentOS 7
centos/mysql-57-centos7 | 5.7 | 452MB | CentOS 7
centos/mysql-56-centos7 | 5.6 | 407MB | CentOS 7
yobasystems/alpine-mariadb | 10.2 | 228MB | alpine 3.8
ipburger/mysql-alpine.docker | 10.2 | 154MB | alpine 3.8
wangxian/alpine-mysql | 10.1 | 176MB |  alpine 3.4

MariaDB 이미지들은 5.5부터 버전이 올라가면서 이미지 크기도 조금씩 커졌습니다.
MySQL은 5.7버전까지는 MariaDB하고 이미지 크기가 비슷하다가 8.0 버전에서 MariaDB보다 커졌습니다.
Percona 서버들이 이미지 크기가 제일 크긴 합니다.
MariaDB ColumnStore 엔진은 1GB가 넘습니다.
CentOS에서의 MariaDB / MySQL 이미지들이 더 크긴 하고요.
alpine 리눅스를 사용하면 이미지 크기를 100~200MB 정도 줄일 수 있습니다.