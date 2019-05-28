---
layout: post
title: "MariaDB Undo recovery 오래 걸릴때"
date: 2019-05-28 10:30:06 +0900
categories: [MariaDB, MySQL]
tags: [MariaDB, MySQL, recovery]
comments: true
---
# MariaDB Undo recovery 오래 걸릴때

대량의 트랙잭션을 수행하던 중에 MariaDB (MySQL) 서버가 비정상 종료를 하면 InnoDB 등의 transaction 지원을 하는 storage engine 들은 재시작하면서 undo recovery를 합니다.  
undo recovery가 빨리 완료가 되면 괜찮지만, 대량의 트랜잭션들은 몇일이 걸릴 수도 있습니다.  
MariaDB에서는 이럴때 mysqldump 백업을 하고 해당 데이터베이스를 drop 후에 복구하라고 합니다.  
https://mariadb.com/kb/en/library/innodb-recovery-modes/

데이터가 수백기가를 넘을때는 mysqldump 백업/복원 시간도 올래 걸립니다.
이럴때 ALTER TABLE ... DISCARD (IMPORT) TABLESPACE 명령을 이용해서 좀 더 빠르게 복구를 할 수 있습니다.

## undo recovery 하지 않고 테이블 복구 절차
- force recovery 모드로 MariaDB 시작
- mysqldump -d 데이터베이스 스키마 백업
- MariaDB 중지
- 데이터베이스 디렉토리의 ibd 파일 복사
- force recovery 모드로 MariaDB 시작
- drop database
- MariaDB 정상적으로 재시작
- create database
- 데이터베이스 스키마 복원
- alter table ... discard tablespace
- 복사했던 ibd 파일을 데이터베이스 디렉토리로 이동
- alter table ... import tablespace

## alter table 명령 만들기
테이블이 많을때 information schema 이용해서 alter table 명령을 만들 수 있습니다.
```
select concat('alter table ', table_schema, '.', table_name, ' import tablespace;') as sql_alter
from information_schema.tables
where table_schema = 'ohnew';
```
