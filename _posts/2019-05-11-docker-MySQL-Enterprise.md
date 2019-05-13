---
layout: post
title: "MySQL 8.0 Enterprise : Docker 설치"
date: 2019-05-11 16:30:06 +0900
categories: [MySQL, docker]
tags: [MySQL, docker]

comments: true
---
# MySQL 8.0 Enterprise : Docker 설치

Docker에서 오픈소스 MySQL community를 설치하는건 일반적으로 아래와 같이 docker pull 등의 명령으로 docker image를 다운로드 받아서 사용을 할 수 있습니다.

~~~
# docker run -p 3307:3306 -d --name mysql -e MYSQL_ROOT_PASSWORD=password mysql/mysql-server
~~~

상용라이선스인 MySQL Enterprise는 어떻게 사용할 수 있는지에 대해서 아래 웺사이트에서 알 수 있습니다.
https://scriptingmysql.wordpress.com/2019/05/10/mysql-server-deployment-with-docker-basic-installation-instructions-for-both-the-community-and-enterprise-versions/

MySQL Enterprise 구매를 하면 주는 Oracle Support portal 계정으로 로그인해서 docker image를 검색하고 다운로드 받을 수 있습니다.
다운로드 받은 docker image를 아래와 같이 docker에 올려주면 됩니다.

~~~
# docker load -i mysql-enterprise-server-8.0.16.tar
~~~
