---
title: 느리게 쿼리 로그-Azure Database for MySQL
description: Azure Database for MySQL에서 사용할 수 있는 저속 쿼리 로그와 다양 한 로깅 수준을 사용 하도록 설정 하는 데 사용할 수 있는 매개 변수를 설명 합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: 0b00db8e89afda8682ddedccfec7e5a6147b7125
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534979"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL의 느린 쿼리 로그
Azure Database for MySQL에서는 사용자에게 느린 쿼리 로그를 제공합니다. 트랜잭션 로그에 대한 액세스는 지원되지 않습니다. 느린 쿼리 로그를 사용하여 문제 해결을 위한 성능 병목을 파악할 수 있습니다.

MySQL 느린 쿼리 로그에 대한 자세한 내용은 MySQL 참조 설명서의 [느린 쿼리 로그 섹션](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)을 참조하세요.

서버에서 [쿼리 저장소](concepts-query-store.md) 를 사용 하도록 설정 하면 `CALL mysql.az_procedure_collect_wait_stats (900, 30);` 저속 쿼리 로그에 ""와 같은 쿼리가 기록 될 수 있습니다. 이 동작은 쿼리 저장소 기능이 쿼리에 대 한 통계를 수집 하는 것으로 예상 됩니다. 

## <a name="configure-slow-query-logging"></a>저속 쿼리 로깅 구성 
기본적으로 느린 쿼리 로그는 비활성화됩니다. 사용 하도록 설정 하려면을 `slow_query_log` ON으로 설정 합니다. Azure Portal 또는 Azure CLI를 사용 하 여이 기능을 사용 하도록 설정할 수 있습니다. 

조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- **long_query_time** : 쿼리가 기록되는 long_query_time(초)보다 쿼리가 오래 걸릴 경우 기본값은 10초입니다.
- **log_slow_admin_statements** : ON에 slow_query_log에 쓰여진 문에서 ALTER_TABLE 및 ANALYZE_TABLE 등과 같은 관리 문이 포함된 경우
- **log_queries_not_using_indexes** : 인덱스를 사용하지 않는 쿼리가 slow_query_log에 기록되는지 여부를 결정합니다.
- **log_throttle_queries_not_using_indexes** :이 매개 변수는 느린 쿼리 로그에 쓸 수 있는 비 인덱스 쿼리의 수 한도를 결정합니다. 이 매개 변수는 log_queries_not_using_indexes가 ON으로 설정된 경우 적용됩니다.
- **log_output** : "File" 이면 저속 쿼리 로그가 로컬 서버 저장소에 기록 되 고 진단 로그를 Azure Monitor 수 있습니다. "None"이면 느린 쿼리 로그가 Azure Monitor 진단 로그에만 기록됩니다. 

> [!IMPORTANT]
> 테이블이 인덱싱되지 않은 경우 `log_queries_not_using_indexes` 및 `log_throttle_queries_not_using_indexes` 매개 변수를 ON으로 설정 하면 이러한 인덱싱되지 않은 테이블에 대해 실행 되는 모든 쿼리가 저속 쿼리 로그에 기록 되기 때문에 MySQL 성능에 영향을 줄 수 있습니다.<br><br>
> 오랜 시간 동안 느리게 쿼리를 로깅할 계획인 경우 `log_output` 를 "없음"으로 설정 하는 것이 좋습니다. "File"로 설정 된 경우 이러한 로그는 로컬 서버 저장소에 기록 되며 MySQL 성능에 영향을 줄 수 있습니다. 

느린 쿼리 로그 매개 변수의 전체 설명은 MySQL [느린 쿼리 로그 설명서](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)를 참조하세요.

## <a name="access-slow-query-logs"></a>저속 쿼리 로그 액세스
Azure Database for MySQL에서 저속 쿼리 로그에 액세스 하는 두 가지 옵션이 있습니다. 로컬 서버 저장소 또는 Azure Monitor 진단 로그입니다. 이는 매개 변수를 사용 하 여 설정 됩니다 `log_output` .

로컬 서버 저장소의 경우 Azure Portal 또는 Azure CLI를 사용 하 여 느리게 쿼리 로그를 나열 하 고 다운로드할 수 있습니다. Azure Portal에서 Azure Portal 서버로 이동 합니다. **모니터링** 머리글 아레에서 **서버 로그** 페이지를 선택합니다. Azure CLI에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 저속 쿼리 로그 구성 및 액세스](howto-configure-server-logs-in-cli.md)를 참조 하세요. 

Azure Monitor 진단 로그를 사용 하면 느리게 쿼리 로그를 Azure Monitor 로그 (Log Analytics), Azure Storage 또는 Event Hubs로 파이프 할 수 있습니다. 자세한 내용은 [아래](concepts-server-logs.md#diagnostic-logs) 를 참조 하세요.

## <a name="local-server-storage-log-retention"></a>로컬 서버 저장소 로그 보존
서버의 로컬 저장소에 로깅할 때 로그를 만들 때까지 최대 7 일 동안 사용할 수 있습니다. 사용 가능한 로그의 전체 크기가 7GB를 초과하면 여유 공간이 생길 때까지 가장 오래된 파일이 삭제됩니다.

즉, 24시간이 지나거나 전체 크기가 7GB를 초과할 때마다(먼저 해당되는 쪽) 로그가 가장 오래된 파일부터 삭제됩니다.

> [!Note]
> 위의 로그 보존은 Azure Monitor 진단 로그를 사용 하 여 파이프 된 로그에는 적용 되지 않습니다. 내보내는 데이터 싱크에 대 한 보존 기간을 변경할 수 있습니다 (예: Azure Storage).

## <a name="diagnostic-logs"></a>진단 로그
Azure Database for MySQL은 Azure Monitor 진단 로그와 통합됩니다. MySQL 서버에서 느리게 쿼리 로그를 사용 하도록 설정 하면 로그, Event Hubs 또는 Azure Storage를 Azure Monitor 하도록 선택할 수 있습니다. 진단 로그를 사용하도록 설정하는 방법에 대한 자세한 내용은 [진단 로그 설명서](../azure-monitor/platform/platform-logs-overview.md)의 방법 섹션을 참조하세요.

아래 표에는 각 로그의 내용에 대한 설명이 나와 있습니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 출력 방법에 따라 달라질 수 있습니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` 시간은 | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics`입니다. |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL`입니다. |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | 서버의 이름 |
| `start_time_t` 시간은 | 쿼리가 시작된 시간 |
| `query_time_s` | 쿼리를 실행 하는 데 걸린 총 시간 (초)입니다. |
| `lock_time_s` | 쿼리가 잠긴 총 시간 (초)입니다. |
| `user_host_s` | 사용자 이름 |
| `rows_sent_s` | 전송된 행 수 |
| `rows_examined_s` | 검사된 행 수 |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | ID 삽입 |
| `sql_text_s` | 전체 쿼리 |
| `server_id_s` | 서버 ID |
| `thread_id_s` | 스레드 ID |
| `\_ResourceId` | 리소스 URI |

> [!Note]
> 의 경우 `sql_text` 로그는 2048 자를 초과 하는 경우 잘립니다.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure Monitor 로그의 로그 분석

저속 쿼리 로그가 진단 로그를 통해 Azure Monitor 로그에 전달 되 면 속도가 느려지는 쿼리를 추가로 분석할 수 있습니다. 다음은 시작 하는 데 도움이 되는 몇 가지 샘플 쿼리입니다. 아래를 서버 이름으로 업데이트 해야 합니다.

- 특정 서버에서 10 초 보다 긴 쿼리

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- 특정 서버에 가장 긴 쿼리 5 개 나열

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- 특정 서버에서 최소, 최대, 평균 및 표준 편차 쿼리 시간을 기준으로 느리게 쿼리를 요약 합니다.

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- 특정 서버에서 느리게 쿼리를 배포 하는 그래프

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 진단 로그가 활성화 된 모든 MySQL 서버에서 10 초 보다 긴 쿼리 표시

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>다음 단계
- [Azure Portal에서 느리게 쿼리 로그를 구성 하는 방법](howto-configure-server-logs-in-portal.md)
- [Azure CLI에서 느리게 쿼리 로그를 구성 하는 방법](howto-configure-server-logs-in-cli.md)