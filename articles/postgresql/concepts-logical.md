---
title: 논리 디코딩 - Azure Database for PostgreSQL - 단일 서버
description: Azure Database for PostgreSQL - 단일 서버에서의 변경 데이터 캡처에 대한 논리 디코딩과 wal2json에 대해 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 0ea58050c5dc952392df56b4fb556a0998eef165
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96938905"
---
# <a name="logical-decoding"></a>논리 디코딩

[PostgreSQL의 논리 디코딩](https://www.postgresql.org/docs/current/logicaldecoding.html)을 사용하면 외부 소비자에게 데이터 변경 내용을 스트리밍할 수 있습니다. 논리 디코딩은 이벤트 스트리밍 및 변경 데이터 캡처 시나리오에 널리 사용됩니다.

논리 디코딩은 출력 플러그 인을 사용하여 Postgres의 WAL(미리 쓰기 로그)을 읽을 수 있는 형식으로 변환합니다. Azure Database for PostgreSQL은 출력 플러그 인에 [wal2json](https://github.com/eulerto/wal2json), [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html), pgoutput을 제공합니다. pgoutput은 PostgreSQL 버전 10 이상의 PostgreSQL에서 사용 가능합니다.

Postgres 논리 디코딩이 작동하는 방식에 대한 개요를 보려면 [블로그를 방문](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421)하세요. 

> [!NOTE]
> PostgreSQL 게시/구독을 사용하는 논리 복제는 Azure Database for PostgreSQL - 단일 서버에서 지원되지 않습니다.


## <a name="set-up-your-server"></a>서버 설정 
논리 디코딩과 [읽기 복제본](concepts-read-replicas.md)은 모두 정보에 대한 Postgres WAL(미리 쓰기 로그)에 따라 달라집니다. 해당하는 두 기능에는 Postgres의 다른 로깅 수준이 필요합니다. 논리 디코딩에는 읽기 복제본보다 높은 수준의 로깅이 필요합니다.

올바른 로깅 수준을 구성하려면 Azure 복제 지원 매개 변수를 사용하세요. Azure 복제 지원에는 다음 세 가지 설정 옵션이 있습니다.

* **해제** - WAL에 최소 정보를 저장합니다. 이 설정은 대부분의 Azure Database for PostgreSQL 서버에서 사용할 수 없습니다.  
* **복제본** - **해제** 보다 자세한 정보를 표시합니다. 이는 [읽기 복제본](concepts-read-replicas.md)이 작동하는 데 필요한 최소 로깅 수준입니다. 이 설정은 대부분의 서버에서 기본값입니다.
* **논리** - **복제본** 보다 자세한 정보를 표시합니다. 논리 디코딩이 작동하기 위한 최소 로깅 수준입니다. 읽기 복제본도 이 설정에서 작동합니다.


### <a name="using-azure-cli"></a>Azure CLI 사용

1. azure.replication_support를 `logical`로 설정합니다.
   ```azurecli-interactive
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. 서버를 다시 시작하여 변경 내용을 적용합니다.
   ```azurecli-interactive
   az postgres server restart --resource-group mygroup --name myserver
   ```
3. Postgres 9.5 또는 9.6을 실행하고 공용 네트워크 액세스를 사용하는 경우 논리 복제를 실행할 클라이언트의 공용 IP 주소를 포함하는 방화벽 규칙을 추가합니다. 방화벽 규칙 이름에는 **_replrule** 이 포함되어야 합니다. 예를 들면 *test_replrule* 입니다. 서버에 새 방화벽 규칙을 만들려면 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) 명령을 실행합니다. 

### <a name="using-azure-portal"></a>Azure Portal 사용

1. Azure 복제 지원을 **논리** 로 설정합니다. **저장** 을 선택합니다.

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="Azure Database for PostgreSQL - 복제 - Azure 복제 지원":::

2. 변경 내용을 저장하려면 **예** 를 선택하여 서버를 다시 시작합니다.

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="Azure Database for PostgreSQL - 복제 - 다시 시작 확인":::

3. Postgres 9.5 또는 9.6을 실행하고 공용 네트워크 액세스를 사용하는 경우 논리 복제를 실행할 클라이언트의 공용 IP 주소를 포함하는 방화벽 규칙을 추가합니다. 방화벽 규칙 이름에는 **_replrule** 이 포함되어야 합니다. 예를 들면 *test_replrule* 입니다. 그런 다음 **Save** 를 클릭합니다.

   :::image type="content" source="./media/concepts-logical/client-replrule-firewall.png" alt-text="Azure Database for PostgreSQL - 복제 - 방화벽 규칙 추가":::

## <a name="start-logical-decoding"></a>논리 디코딩 시작

논리 디코딩을 스트리밍 프로토콜 또는 SQL 인터페이스를 통해 이용할 수 있습니다. 두 방법 모두 [복제 슬롯](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)을 사용합니다. 슬롯은 단일 데이터베이스의 변경 스트림을 나타냅니다.

복제 슬롯을 사용하려면 Postgres의 복제 권한이 필요합니다. 이번에는 서버의 관리 사용자만 복제 권한을 사용할 수 있습니다. 

### <a name="streaming-protocol"></a>스트리밍 프로토콜
스트리밍 프로토콜을 사용하여 변경 내용을 사용하는 것이 좋습니다. 고유의 소비자/커넥터를 만들거나 [Debezium](https://debezium.io/)과 같은 도구를 사용할 수 있습니다. 

[pg_recvlogical로 스트리밍 프로토콜을 사용하는 예제](https://github.com/eulerto/wal2json#pg_recvlogical)는 wal2json 설명서를 참조하세요.


### <a name="sql-interface"></a>SQL 인터페이스
아래 예제에서는 wal2json 플러그 인과 함께 SQL 인터페이스를 사용합니다.
 
1. 슬롯을 만듭니다.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL 명령을 부여합니다. 예를 들면 다음과 같습니다.
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. 변경 내용을 이용합니다.
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

4. 슬롯 사용이 끝났으면 슬롯을 삭제합니다.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>슬롯 모니터링

논리 디코딩을 모니터링해야 합니다. 사용하지 않는 모든 복제 슬롯은 삭제되어야 합니다. 슬롯은 소비자가 변경 내용을 읽을 때까지 Postgres WAL 로그와 관련 시스템 카탈로그에 저장됩니다. 소비자가 실패하거나 적절히 구성되지 않으면 소비되지 않은 로그가 누적되어 스토리지를 채웁니다. 또한 로그가 이용되지 않으면 트랜잭션 ID 래핑의 위험이 증가합니다. 두 경우 모두 서버를 사용할 수 없게 될 수 있습니다. 따라서 논리 복제 슬롯을 지속적으로 이용하는 것이 중요합니다. 논리 복제 슬롯을 더 이상 사용하지 않는 경우 즉시 삭제합니다.

Pg_replication_slots 보기의 ‘활성’ 열은 슬롯에 소비자가 연결되어 있는지 여부를 나타냅니다.
```SQL
SELECT * FROM pg_replication_slots;
```

‘사용된 스토리지’ 및 ‘복제본 간 최대 지연’ 메트릭에 대한 [경고를 설정하여](howto-alert-on-metric.md) 값이 정상 임계값을 초과하면 알릴 수 있도록 합니다.  

> [!IMPORTANT]
> 사용되지 않는 복제 슬롯을 삭제해야 합니다. 이렇게 하지 않으면 서버를 사용할 수 없게 될 수 있습니다.

## <a name="how-to-drop-a-slot"></a>슬롯을 삭제하는 방법
복제 슬롯을 적극적으로 사용하지 않는 경우에는 슬롯을 삭제해야 합니다.

SQL을 사용하여 `test_slot`라는 이름의 복제 슬롯을 삭제하려면 다음을 수행합니다.
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> 논리 디코딩 사용을 중지하는 경우 azure.replication_support를 다시 `replica` 또는 `off`로 변경합니다. `logical`에 의해 유지되는 WAL 세부 정보는 더 자세한 정보이며, 논리 디코딩을 사용하지 않는 경우 사용하지 않도록 설정해야 합니다. 

 
## <a name="next-steps"></a>다음 단계

* [논리 디코딩에 대한 자세한 정보](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)는 Postgres 설명서를 참조하세요.
* 논리 디코딩에 대한 질문이 있는 경우 [Microsoft 팀](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)에 문의하세요.
* [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보세요.

