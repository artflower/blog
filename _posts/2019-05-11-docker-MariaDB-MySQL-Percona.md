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

<table>
<tr><th>repository</th><th>version(tag)</th><th>size</th><th>OS</th></tr>
<tr><td>mariadb</td><td>10.4</td><td>392MB</td><td>Ubuntu 18.04</td></tr>
<tr><td>mariadb</td><td>10.3 (latest)</td><td>381MB</td><td>Ubuntu 18.04</td></tr>
<tr><td>mariadb</td><td>10.2</td><td>378MB</td><td>Ubuntu 18.04</td></tr>
<tr><td>mariadb</td><td>10.1</td><td>391MB</td><td>Ubuntu 18.04</td></tr>
<tr><td>mariadb</td><td>10.0</td><td>352MB</td><td>Ubuntu 16.04</td></tr>
<tr><td>mariadb</td><td>5.5</td><td>352MB</td><td>Ubuntu 14.04</td></tr>
<tr><td>mariadb/columnstore</td><td>1.2</td><td>1.15GB</td><td>CentOS 7</td></tr>
<tr><td>percona</td><td>8.0</td><td>727MB</td><td>CentOS 7</td></tr>
<tr><td>percona</td><td>5.7 (latest)</td><td>583MB</td><td>CentOS 7</td></tr>
<tr><td>percona</td><td>5.6</td><td>421MB</td><td>CentOS 7</td></tr>
<tr><td>percona</td><td>5.5</td><td>294MB</td><td>Debian 9</td></tr>
<tr><td>mysql</td><td>8.0 (latest)</td><td>443MB</td><td>Debian 9</td></tr>
<tr><td>mysql</td><td>5.7</td><td>373MB</td><td>Debian 9</td></tr>
<tr><td>mysql</td><td>5.6</td><td>256MB</td><td>Debian 9</td></tr>
<tr><td>mysql</td><td>5.5</td><td>205MB</td><td>Debian 9</td></tr>
<tr><td>mysql/mysql-server</td><td>8.0</td><td>289MB</td><td>Oracle Linux 7</td></tr>
<tr><td>bitnami/mariadb</td><td>10.1 (latest)</td><td>261MB</td><td>Debian 9</td></tr>
<tr><td>bitnami/mysql</td><td>5.7 (latest)</td><td>287MB</td><td>Debian 9</td></tr>
<tr><td>centos/mariadb-102-centos7</td><td>10.2</td><td>456MB</td><td>CentOS 7</td></tr>
<tr><td>centos/mariadb-101-centos7</td><td>10.1</td><td>476MB</td><td>CentOS 7</td></tr>
<tr><td>centos/mariadb-100-centos7</td><td>10.0</td><td>439MB</td><td>CentOS 7</td></tr>
<tr><td>centos/mysql-80-centos7</td><td>8.0</td><td>569MB</td><td>CentOS 7</td></tr>
<tr><td>centos/mysql-57-centos7</td><td>5.7</td><td>452MB</td><td>CentOS 7</td></tr>
<tr><td>centos/mysql-56-centos7</td><td>5.6</td><td>407MB</td><td>CentOS 7</td></tr>
<tr><td>yobasystems/alpine-mariadb</td><td>10.2</td><td>228MB</td><td>alpine 3.8</td></tr>
<tr><td>ipburger/mysql-alpine.docker</td><td>10.2</td><td>154MB</td><td>alpine 3.8</td></tr>
<tr><td>wangxian/alpine-mysql</td><td>10.1</td><td>176MB</td><td> alpine 3.4</td></tr>
</table>  
  
  
    


* MariaDB 이미지들은 5.5부터 버전이 올라가면서 이미지 크기도 조금씩 커졌습니다.
* MySQL은 5.7버전까지는 MariaDB하고 이미지 크기가 비슷하다가 8.0 버전에서 MariaDB보다 커졌습니다.
* Percona 서버들이 이미지 크기가 제일 크긴 합니다.
* MariaDB ColumnStore 엔진은 1GB가 넘습니다.
* CentOS에서의 MariaDB / MySQL 이미지들이 더 크긴 하고요.
* alpine 리눅스를 사용하면 이미지 크기를 100~200MB 정도 줄일 수 있습니다.