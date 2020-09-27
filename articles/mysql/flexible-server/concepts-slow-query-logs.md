---
title: 느리게 쿼리 로그-Azure Database for MySQL 유연한 서버
description: Azure Database for MySQL 유연한 서버에서 사용할 수 있는 저속 쿼리 로그를 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 512a103f5ac116f1f4eb18631cdc8e4a8b9380c9
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399213"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Azure Database for MySQL 유연한 서버에서 느리게 쿼리 로그 (미리 보기)

> [!IMPORTANT] 
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure Database for MySQL 유연한 서버에서 저속 쿼리 로그는 사용자가 구성 하 고 액세스 하는 데 사용할 수 있습니다. 저속 쿼리 로그는 기본적으로 사용 하지 않도록 설정 되어 있으므로 문제 해결 중 성능 병목 상태를 식별 하는 데 도움이 됩니다.

MySQL 저속 쿼리 로그에 대 한 자세한 내용은 MySQL 엔진 설명서의 [저속 쿼리 로그 섹션](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) 을 참조 하세요.

## <a name="configure-slow-query-logging"></a>저속 쿼리 로깅 구성 
기본적으로 저속 쿼리 로그는 사용할 수 없습니다. 로그를 사용 하도록 설정 하려면 `slow_query_log` 서버 매개 변수를 *ON*으로 설정 합니다. Azure Portal 또는 Azure CLI를 사용 하 여 구성할 수 있습니다. <!-- add link to server parameter-->. 

느리게 쿼리 로깅 동작을 제어 하기 위해 조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- **long_query_time**: `long_query_time` 완료 하는 데 (초) 보다 오래 걸리면 쿼리를 기록 합니다. 기본값은 10초입니다.
- **log_slow_admin_statements**: 관리 문이 있는지 확인 합니다 (예: `ALTER_TABLE`, `ANALYZE_TABLE` )가 기록 됩니다.
- **log_queries_not_using_indexes**: 인덱스를 사용 하지 않는 쿼리를 로깅할지 여부를 결정 합니다.
- **log_throttle_queries_not_using_indexes**: 저속 쿼리 로그에 쓸 수 있는 인덱싱되지 않은 쿼리 수를 제한 합니다. 이 매개 변수 `log_queries_not_using_indexes` 는가 *ON* 으로 설정 된 경우에 적용 됩니다.

> [!IMPORTANT]
> 테이블이 인덱싱되지 않은 경우 `log_queries_not_using_indexes` 및 `log_throttle_queries_not_using_indexes` 매개 변수를 **ON** 으로 설정 하면 이러한 인덱싱되지 않은 테이블에 대해 실행 되는 모든 쿼리가 저속 쿼리 로그에 기록 되기 때문에 MySQL 성능에 영향을 줄 수 있습니다.

느린 쿼리 로그 매개 변수의 전체 설명은 MySQL [느린 쿼리 로그 설명서](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)를 참조하세요.

## <a name="access-slow-query-logs"></a>저속 쿼리 로그 액세스

저속 쿼리 로그는 Azure Monitor 진단 설정에 통합 됩니다. MySQL 유연한 서버에서 느리게 쿼리 로그를 사용 하도록 설정 하면 로그, Event Hubs 또는 Azure Storage Azure Monitor으로 내보낼 수 있습니다. 진단 설정에 대해 자세히 알아보려면 [진단 로그 설명서](../../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요. Azure Portal에서 진단 설정을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [저속 쿼리 로그 포털 문서](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics)를 참조 하세요.

다음 표에서는 저속 쿼리 로그의 출력을 설명 합니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 출력 방법에 따라 달라질 수 있습니다.

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
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | ID 삽입 |
| `sql_text_s` | 전체 쿼리 |
| `server_id_s` | 서버 ID |
| `thread_id_s` | 스레드 ID |
| `\_ResourceId` | 리소스 URI |

> [!Note]
> 의 경우 `sql_text_s` 로그는 2048 자를 초과 하는 경우 잘립니다.

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
- [감사 로그](concepts-audit-logs.md) 에 대해 자세히 알아보기
- [Azure Portal](how-to-configure-slow-query-logs-portal.md) 에서 느리게 쿼리 로그 구성
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->