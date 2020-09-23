---
title: 논리적 디코딩-Azure Database for PostgreSQL 단일 서버
description: Azure Database for PostgreSQL 단일 서버에서 변경 데이터 캡처에 대 한 논리적 디코딩 및 wal2json에 대해 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: bd886bea90c1092e38fac191a60a118aab0bef1f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903899"
---
# <a name="logical-decoding"></a>논리 디코딩
 
[PostgreSQL의 논리적 디코딩을](https://www.postgresql.org/docs/current/logicaldecoding.html) 통해 외부 소비자에 게 데이터 변경 내용을 스트리밍할 수 있습니다. 논리적 디코딩은 이벤트 스트리밍 및 변경 데이터 캡처 시나리오에 사용 되는 많이입니다.

논리적 디코딩은 출력 플러그 인을 사용 하 여 Postgres의 쓰기 미리 로그 (WAL)를 읽을 수 있는 형식으로 변환 합니다. Azure Database for PostgreSQL 출력 플러그 인 [wal2json](https://github.com/eulerto/wal2json), [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) 및 pgoutput을 제공 합니다. pgoutput은 Postgres 버전 10 이상에서 Postgres를 통해 사용할 수 있습니다.

Postgres 논리적 디코딩이 작동 하는 방식에 대 한 개요를 보려면 [블로그를 방문](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421)하세요. 

> [!NOTE]
> 논리적 디코딩은 Azure Database for PostgreSQL 단일 서버에서 공개 미리 보기로 제공 됩니다.


## <a name="set-up-your-server"></a>서버 설정 
논리적 디코딩 및 [읽기 복제본](concepts-read-replicas.md) 은 모두 정보에 대 한 Postgres write 미리 로그 (WAL)에 따라 달라 집니다. 이러한 두 기능에는 Postgres의 다른 로깅 수준이 필요 합니다. 논리적 디코딩에는 읽기 복제본 보다 높은 수준의 로깅이 필요 합니다.

올바른 로깅 수준을 구성 하려면 Azure replication support 매개 변수를 사용 합니다. Azure 복제 지원에는 세 가지 설정 옵션이 있습니다.

* **Off** -WAL에 최소 정보를 저장 합니다. 이 설정은 대부분의 Azure Database for PostgreSQL 서버에서 사용할 수 없습니다.  
* **복제본** -보다 자세한 정보를 **해제**합니다. 이는 [읽기 복제본](concepts-read-replicas.md) 이 작동 하는 데 필요한 최소 수준의 로깅입니다. 이 설정은 대부분의 서버에서 기본값입니다.
* **논리적** - **복제본**보다 자세한 정보를 표시 합니다. 논리적 디코딩을 작동 하기 위한 최소 로깅 수준입니다. 읽기 복제본도이 설정에서 작동 합니다.

이 매개 변수를 변경한 후에는 서버를 다시 시작 해야 합니다. 내부적으로이 매개 변수는 Postgres 매개 변수, 및를 설정 합니다 `wal_level` `max_replication_slots` `max_wal_senders` .

### <a name="using-azure-cli"></a>Azure CLI 사용

1. Azure. replication_support를로 설정 `logical` 합니다.
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. 서버를 다시 시작 하 여 변경 내용을 적용 합니다.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

### <a name="using-azure-portal"></a>Azure Portal 사용

1. Azure replication support를 **logical**로 설정 합니다. **저장**을 선택합니다.

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="Azure Database for PostgreSQL-복제-Azure 복제 지원":::

2. **예**를 선택 하 여 서버를 다시 시작 하 여 변경 내용을 적용 합니다.

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="Azure Database for PostgreSQL-복제-다시 시작 확인":::


## <a name="start-logical-decoding"></a>논리적 디코딩 시작

논리적 디코딩은 스트리밍 프로토콜 또는 SQL 인터페이스를 통해 사용 될 수 있습니다. 두 방법 모두 [복제 슬롯](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)을 사용 합니다. 슬롯은 단일 데이터베이스의 변경 내용을 나타냅니다.

복제 슬롯을 사용 하려면 Postgres의 복제 권한이 필요 합니다. 이번에는 서버의 관리 사용자 에게만 복제 권한을 사용할 수 있습니다. 

### <a name="streaming-protocol"></a>스트리밍 프로토콜
스트리밍 프로토콜을 사용 하 여 변경 내용을 사용 하는 것이 좋습니다. 사용자 고유의 소비자/커넥터를 만들거나 [Debezium](https://debezium.io/)와 같은 도구를 사용할 수 있습니다. 

[Pg_recvlogical에서 스트리밍 프로토콜을 사용 하는 예제](https://github.com/eulerto/wal2json#pg_recvlogical)는 wal2json 설명서를 참조 하세요.


### <a name="sql-interface"></a>SQL 인터페이스
아래 예제에서는 wal2json 플러그 인과 함께 SQL 인터페이스를 사용 합니다.
 
1. 슬롯을 만듭니다.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL 명령을 실행 합니다. 다음은 그 예입니다. 
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


## <a name="monitoring-slots"></a>슬롯 모니터링

논리적 디코딩을 모니터링 해야 합니다. 사용 하지 않는 모든 복제 슬롯을 삭제 해야 합니다. 슬롯은 소비자가 변경 내용을 읽을 때까지 Postgres WAL 로그 및 관련 시스템 카탈로그를 유지 합니다. 소비자가 실패 하거나 적절히 구성 되지 않은 경우에는 소비 되지 않은 로그가 누적 되어 저장소를 채웁니다. 또한 로그가 소비 되지 않으면 트랜잭션 ID 래핑의 위험이 증가 합니다. 두 경우 모두 서버를 사용할 수 없게 될 수 있습니다. 따라서 논리적 복제 슬롯이 지속적으로 사용 되는 것이 중요 합니다. 논리적 복제 슬롯을 더 이상 사용 하지 않는 경우 즉시 삭제 합니다.

Pg_replication_slots 뷰의 ' 활성 ' 열은 슬롯에 소비자가 연결 되어 있는지 여부를 나타냅니다.
```SQL
SELECT * FROM pg_replication_slots;
```

값이 정상 임계값을 초과 하는 경우 알림을 보내는 *데 사용 되는 저장소* 및 *복제본 메트릭의 최대 지연* 에 대해 [경고를 설정](howto-alert-on-metric.md) 합니다. 

> [!IMPORTANT]
> 사용 하지 않는 복제 슬롯을 삭제 해야 합니다. 이 작업을 수행 하지 못하면 서버를 사용할 수 없게 될 수 있습니다.

## <a name="how-to-drop-a-slot"></a>슬롯을 삭제 하는 방법
복제 슬롯을 적극적으로 사용 하지 않는 경우에는 삭제 해야 합니다.

SQL을 사용 하 여 라는 복제 슬롯을 삭제 하려면 `test_slot` 다음을 수행 합니다.
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> 논리적 디코딩 사용을 중지 하는 경우 azure. replication_support을 다시 또는로 변경 합니다. `replica` `off` 에서 보유 하 고 있는 WAL 세부 정보는 더 자세한 정보 `logical` 를 사용 하 여 논리적 디코딩을 사용 하지 않을 때 사용 하지 않도록 설정 해야 합니다. 

 
## <a name="next-steps"></a>다음 단계

* [논리적 디코딩에 대 한 자세한](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)내용은 Postgres 설명서를 참조 하세요.
* 논리적 디코딩에 대해 궁금한 사항이 있는 경우 [팀](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) 에 연락 하세요.
* [복제본 읽기](concepts-read-replicas.md)에 대해 자세히 알아보세요.

