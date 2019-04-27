---
layout: post
title: "MySQL 8.0 자동 mysql_upgrade"
date: 2019-04-24 13:30:06 +0900
comments: true
---
# MySQL 8.0 자동 mysql_upgrade

https://elephantdolphin.blogspot.com/2019/04/bye-bye-to-mysqlupgrade-change-to.html

MySQL 8.0.16 release Notes에 mysql_upgrade 관련한 변경 사항이 있다고 합니다.
기존에는 version upgrade 하면 mysql_upgrade 실행을 해서 schema 변경 사항들을 최신으로 적용을 해줬습니다. MySQL 8.0.16부터는 수동으로 mysql_upgrade 하던것을 자동으로 하는것으로 변경 됐다고 합니다.
앞으로는 MySQL 업그레이드를 하고 서버 시작을 하면 자동으로 upgrade를 하게 되어서 mysql_upgrade는 deprecated 된다고 합니다.
