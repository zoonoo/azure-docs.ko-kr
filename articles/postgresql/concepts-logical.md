---
title: 논리 디코딩 - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: PostgreSQL - 단일 서버에 대한 Azure 데이터베이스에서 변경 데이터 캡처에 대한 논리 디코딩 및 wal2json에 대해 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522148"
---
# <a name="logical-decoding"></a>논리적 디코딩
 
[PostgreSQL의 논리적 디코딩을](https://www.postgresql.org/docs/current/logicaldecoding.html) 사용하면 외부 소비자에게 데이터 변경 내용을 스트리밍할 수 있습니다. 논리 디코딩은 일반적으로 이벤트 스트리밍 및 변경 데이터 캡처 시나리오에 사용됩니다.

논리 디코딩은 출력 플러그인을 사용하여 Postgres의 미리 쓰기 로그(WAL)를 읽을 수 있는 형식으로 변환합니다. PostgreSQL용 Azure 데이터베이스는 두 가지 출력 플러그인인 [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) 및 [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> 논리 디코딩은 PostgreSQL - 단일 서버에 대한 Azure 데이터베이스의 공개 미리 보기입니다.


## <a name="set-up-your-server"></a>서버 설정
논리적 디코딩을 사용하려면 서버에서 WAL을 저장하고 스트리밍할 수 있습니다. 

1. azure.replication_support Azure `logical` CLI를 사용 하도록 설정 합니다. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > 읽기 복제본을 사용하는 경우 azure.replication_support 복제본을 실행할 수도 있도록 `logical` 설정합니다. 논리적 디코딩 사용을 중지하면 설정을 다시 `replica`로 변경합니다. 


2. 서버를 다시 시작하여 변경 내용을 적용합니다.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>논리적 디코딩 시작

논리 디코딩은 스트리밍 프로토콜 또는 SQL 인터페이스를 통해 사용할 수 있습니다. 두 방법 모두 [복제 슬롯을](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)사용합니다. 슬롯은 단일 데이터베이스의 변경 스트림을 나타냅니다.

복제 슬롯을 사용하려면 Postgres의 복제 권한이 필요합니다. 현재 복제 권한은 서버의 관리자 사용자만 사용할 수 있습니다. 

### <a name="streaming-protocol"></a>스트리밍 프로토콜
스트리밍 프로토콜을 사용하여 변경하는 것이 바람직한 경우가 많습니다. 당신은 당신의 자신의 소비자 / 커넥터를 만들 거나 [Debezium와](https://debezium.io/)같은 도구를 사용할 수 있습니다. 

pg_recvlogical 함께 [스트리밍 프로토콜을 사용하는 예는](https://github.com/eulerto/wal2json#pg_recvlogical)wal2json 설명서를 참조하십시오.


### <a name="sql-interface"></a>SQL 인터페이스
아래 예제에서는 wal2json 플러그인과 함께 SQL 인터페이스를 사용합니다.
 
1. 슬롯을 만듭니다.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL 명령을 내보입니다. 다음은 그 예입니다.
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. 변경 내용을 사용합니다.
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

4. 사용이 완료되면 슬롯을 놓습니다.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>슬롯 모니터링

논리적 디코딩을 모니터링해야 합니다. 사용하지 않은 복제 슬롯은 삭제해야 합니다. 슬롯은 소비자가 변경 내용을 읽을 때까지 Postgres WAL 로그 및 관련 시스템 카탈로그를 유지합니다. 소비자가 실패하거나 제대로 구성되지 않은 경우 사용되지 않은 로그가 쌓여 저장소를 채웁니다. 또한 사용되지 않는 로그는 트랜잭션 ID 래핑의 위험을 증가시게 됩니다. 두 경우 모두 서버를 사용할 수 없게 될 수 있습니다. 따라서 논리 복제 슬롯이 지속적으로 사용되는 것이 중요합니다. 논리 복제 슬롯이 더 이상 사용되지 않으면 즉시 삭제합니다.

pg_replication_slots 보기의 '활성' 열은 슬롯에 연결된 소비자가 있는지 여부를 나타냅니다.
```SQL
SELECT * FROM pg_replication_slots;
```

*사용된 저장소에* 대한 경고를 설정하고 *복제본 메트릭에서 최대 지연을* [설정하여](howto-alert-on-metric.md) 값이 정상 임계값을 초과할 때 이를 알려줍니다. 

> [!IMPORTANT]
> 사용하지 않은 복제 슬롯을 삭제해야 합니다. 이렇게 하지 않으면 서버를 사용할 수 없게 될 수 있습니다.

## <a name="how-to-drop-a-slot"></a>슬롯을 드롭하는 방법
복제 슬롯을 적극적으로 사용하지 않는 경우 삭제해야 합니다.

SQL을 사용하여 호출된 `test_slot` 복제 슬롯을 삭제하려면 다음을 수행합니다.
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> 논리적 디코딩 사용을 중지하면 azure.replication_support `replica` 다시 `off`또는 . 보존된 `logical` WAL 세부 정보는 더 자세한 내용이며 논리적 디코딩을 사용하지 않을 때는 사용하지 않도록 설정해야 합니다. 

 
## <a name="next-steps"></a>다음 단계

* [논리 디코딩에 대해 자세히 알아보려면](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)Postgres 문서를 방문하십시오.
* 논리적 디코딩에 대한 질문이 있으면 [팀에](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) 문의하십시오.
* [읽기 복제본에](concepts-read-replicas.md)대해 자세히 알아봅니다.

