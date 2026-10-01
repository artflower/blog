---
layout: post
title: "MySQL Galera Cluster EOL 마이그레이션"
date: 2026-10-01 09:30:36 +0900
categories: [MariaDB, MySQL, Percona, Galera]
tags: [MariaDB, MySQL, Percona, Galera]
comments: true
---
# MySQL Galera Cluster EOL로 인한 마이그레이션

## [DB 아키텍처] MySQL Galera Cluster 공식 지원 종료(EOS)와 대안: MariaDB 및 Percona 전환 검토

**2026년 9월 30일을 기점으로 MySQL Galera Cluster에 대한 공식 유지보수 및 바이너리 릴리스 지원(EOS/EOL)이 종료되었습니다.** 
과거 MySQL 환경에서 멀티 마스터(Multi-Master) 고가용성을 구현하기 위해 널리 사용되던 Galera Cluster 사용자들은 이제 신규 업데이트와 보안 패치를 받을 수 없게 되었으며, 시스템 안정성을 확보하기 위해 MariaDB나 Percona로의 전환을 검토해야 합니다.

이러한 단종과 전환의 배경에는 데이터베이스 벤더 3사의 각기 다른 행보가 있습니다.

### Oracle MySQL의 Group Replication

Oracle은 자체 개발한 MySQL Group Replication(MGR)을 MySQL의 표준 고가용성 솔루션으로 채택했습니다.

Percona 블로그는 이 상황을 다음과 같이 설명합니다.

> "[Oracle, the upstream MySQL provider, introduced its own replication implementation... Unlike the others mentioned above, it isn't based on Galera. Group Replication was built from the ground up as a new solution.](https://www.percona.com/blog/battle-for-synchronous-replication-in-mysql-galera-vs-group-replication/)"

MySQL 공식 고가용성 기술이 Galera에서 MGR로 이동하면서, 기존 Galera 사용자는 순정 MySQL 환경만으로 기존 클러스터를 유지하기 어려워졌습니다.

<br>

### MariaDB의 Codership 인수와 Raft 아키텍처 도입

2025년 5월, MariaDB는 Galera Cluster 원천 기술을 보유한 Codership을 인수했습니다.

이 인수 이후 MariaDB는 MySQL용 Galera Cluster에 대한 신규 기능 개발을 중단하고 유지보수 지원을 2026년 9월 30일에 종료한다고 선언했습니다. 
향후 Galera 관련 신규 기술과 기능은 MariaDB Galera Cluster에만 독점적으로 제공됩니다.

> "[MariaDB Plc's acquisition of Codership... As we look to the future, we are focusing all innovation and new feature development exclusively on MariaDB Galera Cluster.](https://mariadb.com/resources/blog/upgrade-now-announcing-mysql-galera-cluster-in-place-migration-to-mariadb-galera-cluster/)"

또한 MariaDB는 Enterprise 버전에서 2026년 Raft 프로토콜 기반의 'MariaDB Advanced Cluster'를 새롭게 출시했습니다.

> "[MariaDB Advanced Cluster (built on RAFT) provides a highly available, strongly consistent, and fault-tolerant solution for database deployment.](https://mariadb.com/docs/release-notes/advanced-cluster/mariadb-advanced-cluster-quickstart-guide)"

기존 Galera의 전체 노드 동기화 방식과 달리, 이 Raft 기반 클러스터는 전체 노드의 과반수(Majority Quorum)가 합의하면 쓰기(Write)를 확정하므로 쓰기 지연(Latency) 문제를 개선했습니다.

<br>

### Percona의 Galera 지원 유지

Oracle과 MariaDB가 각자의 생태계를 구축하는 동안, Percona는 기존 MySQL 기반 Galera 클러스터 사용자에 대한 지원 유지를 보장하고 있습니다.

Percona 공식 홈페이지는 PXC(Percona XtraDB Cluster)를 통한 장기 지원을 다음과 같이 안내합니다.

> "[Keep support on the cluster you run today, or move to Percona XtraDB Cluster and stay on MySQL. Your clustering layer can stay open source, stay on MySQL, and stay supported.](https://www.percona.com/mysql/support/mysql-galera/)"

기존 애플리케이션 구조 및 MySQL 쿼리 환경을 변경하지 않고 오픈소스 기반의 Galera 아키텍처를 유지하려는 경우, 엔터프라이즈 모니터링 툴(PMM) 및 최적화가 포함된 Percona XtraDB Cluster(PXC)로 전환하여 운영하는 것이 표준적인 대안입니다.

블로그의 마무리(결론) 부분에 독자가 자신의 운영 환경에 맞춰 합리적인 선택을 할 수 있도록 '의사결정 기준'을 제시해 주면 좋습니다. 불필요한 형용사를 배제하고 실무적인 관점에서 다음과 같이 추가하는 것을 제안합니다.

---

### Galera Cluster 전환을 위한 아키텍처 선택 기준

MySQL Galera Cluster의 공식 지원 종료(EOS)로 인해 대안 아키텍처로의 마이그레이션을 검토해야 합니다. 
데이터베이스 운영 환경과 애플리케이션 종속성에 따라 아래의 기준을 참고하여 전환 방향을 결정할 수 있습니다.  


**MariaDB Galera / Advanced Cluster로의 전환이 적합한 경우:**  
* 원천 기술 및 최신 아키텍처 확보:
    - Galera 기술의 주도권을 가진 MariaDB의 지속적인 업데이트를 보장받을 수 있음 
    - 중장기적으로 쓰기 지연(Latency)이 개선된 Raft 기반 클러스터로의 확장을 고려하는 환경
* MySQL 종속성 탈피 가능:
    - MariaDB 엔진으로 이관 시 쿼리 및 문법 수정 리스크가 적은 환경
 
<br>

**Percona XtraDB Cluster(PXC)로의 전환이 적합한 경우:**
* MySQL 생태계 및 호환성 유지:
    - 애플리케이션 쿼리와 운영 워크플로우가 철저히 MySQL에 맞춰져 있어 데이터베이스 엔진 변경에 따른 리스크 최소화
* 운영 연속성 및 모니터링 강화:
    - 기존 MySQL 기반 Galera 아키텍처를 그대로 유지
    - Percona Monitoring and Management(PMM) 및 XtraBackup을 통한 성능 최적화와 무중단 백업 체계를 구축하려는 환경


<br>
전환을 실행하기 앞서, 선택한 타겟 엔진(MariaDB 또는 Percona) 환경에서 애플리케이션 쿼리 호환성(CTE, JSON 함수 등), 트랜잭션 처리량, 동기화(SST) 트래픽 부하 등을 검증하는 사전 PoC(개념 증명)를 반드시 수행할 것을 권장합니다.