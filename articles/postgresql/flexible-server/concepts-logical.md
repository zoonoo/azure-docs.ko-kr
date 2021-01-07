---
title: 논리적 복제 및 논리적 디코딩-Azure Database for PostgreSQL 유연한 서버
description: Azure Database for PostgreSQL 유연한 서버에서 논리적 복제 및 논리적 디코딩을 사용 하는 방법에 대해 알아봅니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b6689220873aaeb65337ba480e346e5d2c8020ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707866"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 유연한 서버에서 논리적 복제 및 논리적 디코딩

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

PostgreSQL의 논리적 복제 및 논리적 디코딩 기능은 Postgres 버전 11에 대 한 Azure Database for PostgreSQL 유연한 서버에서 지원 됩니다.

## <a name="comparing-logical-replication-and-logical-decoding"></a>논리적 복제 및 논리적 디코딩 비교
논리적 복제 및 논리적 디코딩은 여러 유사성을 가집니다. 둘 다
* Postgres에서 데이터를 복제할 수 있습니다.
* 변경 내용 소스로 [WAL (미리 쓰기 로그)](https://www.postgresql.org/docs/current/wal.html) 사용
* [논리적 복제 슬롯](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) 을 사용 하 여 데이터를 전송 합니다. 슬롯은 변경 내용의 스트림을 나타냅니다.
* 테이블의 [복제본 id 속성](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) 을 사용 하 여 보낼 수 있는 변경 내용을 확인 합니다.
* DDL 변경 내용 복제 안 함


두 기술의 차이점은 논리적 복제입니다. 
* 복제할 테이블 또는 테이블 집합을 지정할 수 있습니다.
* PostgreSQL 인스턴스 간에 데이터를 복제 합니다.

논리 디코딩 
* 데이터베이스의 모든 테이블에서 변경 내용을 추출 합니다. 
* PostgreSQL 인스턴스 간에 데이터를 직접 보낼 수 없습니다.


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>논리적 복제 및 논리적 디코딩을 위한 필수 구성 요소

1. 서버 매개 변수를 `wal_level` 로 설정 `logical` 합니다.
2. 서버를 다시 시작 하 여 `wal_level` 변경 내용을 적용 합니다.
3. PostgreSQL 인스턴스가 연결 하는 리소스의 네트워크 트래픽을 허용 하는지 확인 합니다.
4. 관리 사용자 복제 권한을 부여 합니다.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>논리적 복제 및 논리적 디코딩 사용

### <a name="logical-replication"></a>논리적 복제
논리적 복제는 ' 게시자 ' 및 ' 구독자 ' 라는 용어를 사용 합니다. 
* 게시자는 **데이터를 전송**하는 PostgreSQL 데이터베이스입니다. 
* 구독자는 데이터 **를**전송 하는 PostgreSQL 데이터베이스입니다.

논리적 복제를 시도 하는 데 사용할 수 있는 몇 가지 샘플 코드는 다음과 같습니다.

1. 게시자 데이터베이스에 연결 합니다. 테이블을 만들고 일부 데이터를 추가 합니다.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. 테이블에 대 한 게시를 만듭니다.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. 구독자 데이터베이스에 연결 합니다. 게시자와 동일한 스키마를 사용 하 여 테이블을 만듭니다.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. 이전에 만든 게시에 연결 하는 구독을 만듭니다.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. 이제 구독자에서 테이블을 쿼리할 수 있습니다. 게시자에서 데이터를 수신 하는 것을 볼 수 있습니다.
   ```SQL
   SELECT * FROM basic;
   ```

게시자의 테이블에 더 많은 행을 추가 하 고 구독자에 대 한 변경 내용을 볼 수 있습니다.

[논리적 복제](https://www.postgresql.org/docs/current/logical-replication.html)에 대 한 자세한 내용은 PostgreSQL 설명서를 참조 하세요.

### <a name="logical-decoding"></a>논리 디코딩
논리적 디코딩을 스트리밍 프로토콜 또는 SQL 인터페이스를 통해 사용할 수 있습니다. 

#### <a name="streaming-protocol"></a>스트리밍 프로토콜
스트리밍 프로토콜을 사용 하 여 변경 내용을 사용 하는 것이 좋습니다. 사용자 고유의 소비자/커넥터를 만들거나 [Debezium](https://debezium.io/)와 같은 타사 서비스를 사용할 수 있습니다. 

[Pg_recvlogical에서 스트리밍 프로토콜을 사용 하는 예제](https://github.com/eulerto/wal2json#pg_recvlogical)는 wal2json 설명서를 참조 하세요.

#### <a name="sql-interface"></a>SQL 인터페이스 
아래 예제에서는 wal2json 플러그 인과 함께 SQL 인터페이스를 사용 합니다.
 
1. 슬롯을 만듭니다.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL 명령을 실행 합니다. 예를 들면 다음과 같습니다.
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. 변경 내용을 사용 합니다.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   출력은 다음과 같습니다.
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. 슬롯을 사용 했으면 슬롯을 삭제 합니다.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

[논리적 디코딩](https://www.postgresql.org/docs/current/logicaldecoding.html)에 대해 자세히 알아보려면 PostgreSQL 설명서를 참조 하세요.


## <a name="monitoring"></a>모니터링
논리적 디코딩을 모니터링 해야 합니다. 사용 하지 않는 모든 복제 슬롯을 삭제 해야 합니다. 슬롯은 변경을 읽을 때까지 Postgres WAL 로그 및 관련 시스템 카탈로그에 저장 됩니다. 구독자 또는 소비자가 실패 하거나 적절히 구성 되지 않은 경우에는 소비 되지 않은 로그가 누적 되어 저장소를 채웁니다. 또한 로그가 소비 되지 않으면 트랜잭션 ID 래핑의 위험이 증가 합니다. 두 경우 모두 서버를 사용할 수 없게 될 수 있습니다. 따라서 논리적 복제 슬롯이 지속적으로 사용 되는 것이 중요 합니다. 논리적 복제 슬롯을 더 이상 사용 하지 않는 경우 즉시 삭제 합니다.

Pg_replication_slots 뷰의 ' 활성 ' 열은 슬롯에 소비자가 연결 되어 있는지 여부를 나타냅니다.
```SQL
SELECT * FROM pg_replication_slots;
```

사용 되는 **최대 트랜잭션 id** 및 저장소에 대 한 [경고 설정](howto-alert-on-metrics.md) 유연한 서버 메트릭을 **사용** 하 여 값이 정상 임계값을 초과 하는 경우 사용자에 게 알립니다. 

## <a name="limitations"></a>제한 사항
* **읽기 복제본** -Azure Database for PostgreSQL 읽기 복제본은 현재 유연한 서버에 대해 지원 되지 않습니다.
* **슬롯 및 HA 장애 조치 (failover)** -주 서버의 논리 복제 슬롯은 보조 AZ의 대기 서버에서 사용할 수 없습니다. 이는 서버에서 영역 중복 고가용성 옵션을 사용 하는 경우에 적용 됩니다. 대기 서버에 대 한 장애 조치 (failover)가 발생 하는 경우 대기 서버에서 논리적 복제 슬롯을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [네트워킹 옵션](concepts-networking.md) 에 대 한 자세한 정보
* 유연한 서버에서 사용할 수 있는 [확장](concepts-extensions.md) 에 대 한 자세한 정보
* [고가용성](concepts-high-availability.md) 에 대 한 자세한 정보

