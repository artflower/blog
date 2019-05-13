---
layout: post
title: "MariaDB Storage Engine - AWS S3"
date: 2019-05-13 10:00:06 +0900
categories: [MariaDB, AWS]
tags: [MariaDB, AWS, S3]
comments: true
---
# MariaDB Storage Engine - AWS S3

MariaDB 10.5의 새로운 기능으로 AWS S3 이용할 수 있는 Storage Engine이 추가 됩니다.
https://mariadb.com/kb/en/library/using-the-s3-storage-engine/

S3 스토리지 엔진은 읽기 전용이며 MariaDB 테이블을 Amazon S3에 보관하거나 S3 API를 이용해서 타사 공용 또는 사설 클라우드의 데이터를 MariaDB에서 읽을 수 있습니다.

S3 테이블을 생성하면 로컬 스토리지에 frm 파일을 저장한다고 합니다. S3에서 테이블 정의가 변경되고 로컬 캐시에 있으면 FLUSH TABLES를 실행하여 MariaDB가 변경 사항을 확인하고 .frm 파일을 업데이트 합니다.

aria_s3_copy 툴을 이용해서 S3 스토리지 엔진을 활용할 수 있다고 합니다. .aria_s3_copy는 S3에서 Aria 테이블을 복사하는 데 사용하는 도구입니다.
