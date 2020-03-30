---
title: 느린 쿼리 로그 - MySQL용 Azure 데이터베이스
description: MySQL용 Azure 데이터베이스에서 사용할 수 있는 느린 쿼리 로그와 다른 로깅 수준을 사용하도록 설정하는 데 사용할 수 있는 매개 변수에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 9a3a58cab2d9673a4660967e3a11d7f88900e718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269433"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>MySQL에 대한 Azure 데이터베이스의 느린 쿼리 로그
Azure Database for MySQL에서는 사용자에게 느린 쿼리 로그를 제공합니다. 트랜잭션 로그에 대한 액세스는 지원되지 않습니다. 느린 쿼리 로그를 사용하여 문제 해결을 위한 성능 병목을 파악할 수 있습니다.

MySQL 느린 쿼리 로그에 대한 자세한 내용은 MySQL 참조 설명서의 [느린 쿼리 로그 섹션](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)을 참조하세요.

## <a name="access-slow-query-logs"></a>느린 쿼리 로그에 액세스
Azure 포털 및 Azure CLI를 사용하여 MySQL 느린 쿼리 로그에 대한 Azure 데이터베이스나열 및 다운로드할 수 있습니다.

Azure Portal에서 Azure Database for MySQL Server를 찾습니다. **모니터링** 머리글 아레에서 **서버 로그** 페이지를 선택합니다.

Azure CLI에 대한 자세한 내용은 [Azure CLI를 사용하여 느린 쿼리 로그 구성 및 액세스](howto-configure-server-logs-in-cli.md)를 참조하세요.

마찬가지로 진단 로그를 사용하여 로그를 Azure 모니터에 파이프할 수 있습니다. 자세한 내용은 [아래를](concepts-server-logs.md#diagnostic-logs) 참조하십시오.

## <a name="log-retention"></a>로그 보존
로그는 생성 시점에서 최대 7일까지 사용할 수 있습니다. 사용 가능한 로그의 전체 크기가 7GB를 초과하면 여유 공간이 생길 때까지 가장 오래된 파일이 삭제됩니다. 

즉, 24시간이 지나거나 전체 크기가 7GB를 초과할 때마다(먼저 해당되는 쪽) 로그가 가장 오래된 파일부터 삭제됩니다.

## <a name="configure-slow-query-logging"></a>느린 쿼리 로깅 구성 
기본적으로 느린 쿼리 로그는 비활성화됩니다. 활성화하려면 slow_query_log를 ON으로 설정합니다.

조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- **long_query_time**: 쿼리가 기록되는 long_query_time(초)보다 쿼리가 오래 걸릴 경우 기본값은 10초입니다.
- **log_slow_admin_statements**: ON에 slow_query_log에 쓰여진 문에서 ALTER_TABLE 및 ANALYZE_TABLE 등과 같은 관리 문이 포함된 경우
- **log_queries_not_using_indexes**: 인덱스를 사용하지 않는 쿼리가 slow_query_log에 기록되는지 여부를 결정합니다.
- **log_throttle_queries_not_using_indexes**:이 매개 변수는 느린 쿼리 로그에 쓸 수 있는 비 인덱스 쿼리의 수 한도를 결정합니다. 이 매개 변수는 log_queries_not_using_indexes가 ON으로 설정된 경우 적용됩니다.
- **log_output**: "File"이면 느린 쿼리 로그를 로컬 서버 저장소와 Azure 모니터 진단 로그에 모두 기록할 수 있습니다. "없음"이면 느린 쿼리 로그는 Azure 모니터 진단 로그에만 기록됩니다. 

> [!IMPORTANT]
> 테이블이 인덱싱되지 않은 `log_queries_not_using_indexes` 경우 `log_throttle_queries_not_using_indexes` 인덱싱되지 않은 테이블에 대해 실행되는 모든 쿼리가 느린 쿼리 로그에 기록되므로 및 매개 변수를 ON으로 설정하면 MySQL 성능에 영향을 줄 수 있습니다.<br><br>
> 느린 쿼리를 장기간 로깅하려는 경우 "없음"으로 설정하는 `log_output` 것이 좋습니다. "File"으로 설정하면 이러한 로그가 로컬 서버 저장소에 기록되며 MySQL 성능에 영향을 줄 수 있습니다. 

느린 쿼리 로그 매개 변수의 전체 설명은 MySQL [느린 쿼리 로그 설명서](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)를 참조하세요.

## <a name="diagnostic-logs"></a>진단 로그
Azure Database for MySQL은 Azure Monitor 진단 로그와 통합됩니다. MySQL 서버에서 느린 쿼리 로그를 사용하도록 설정한 후에는 해당 로그, 이벤트 허브 또는 Azure 저장소로 내보내도록 선택할 수 있습니다. 진단 로그를 사용하도록 설정하는 방법에 대한 자세한 내용은 [진단 로그 설명서](../azure-monitor/platform/platform-logs-overview.md)의 방법 섹션을 참조하세요.

아래 표에는 각 로그의 내용에 대한 설명이 나와 있습니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 출력 방법에 따라 달라질 수 있습니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated`[UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics` |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | 서버의 이름 |
| `start_time_t`[UTC] | 쿼리가 시작된 시간 |
| `query_time_s` | 쿼리가 실행되는 데 걸린 총 시간(초) |
| `lock_time_s` | 쿼리가 잠긴 총 시간(초) |
| `user_host_s` | 사용자 이름 |
| `rows_sent_s` | 전송된 행 수 |
| `rows_examined_s` | 검사된 행 수 |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | 삽입 ID |
| `sql_text_s` | 전체 쿼리 |
| `server_id_s` | 서버 ID |
| `thread_id_s` | 스레드 ID |
| `\_ResourceId` | 리소스 URI |

> [!Note]
> 에 `sql_text`대 한 로그는 2048 자를 초과 하는 경우 잘립니다.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure 모니터 로그에서 로그 분석

느린 쿼리 로그가 진단 로그를 통해 Azure Monitor Log에 파이프되면 느린 쿼리에 대한 추가 분석을 수행할 수 있습니다. 다음은 시작하는 데 도움이 되는 몇 가지 샘플 쿼리입니다. 서버 이름으로 아래를 업데이트하십시오.

- 특정 서버에서 10초 이상 쿼리

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- 특정 서버에서 가장 긴 쿼리 5개 목록

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- 특정 서버에서 느린 쿼리를 최소, 최대, 평균 및 표준 편차 쿼리 시간으로 요약

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- 특정 서버에서 느린 쿼리 분포 그래프

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 진단 로그를 사용하도록 설정한 모든 MySQL 서버에서 10초 이상 쿼리 표시

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>다음 단계
- [Azure 포털에서 느린 쿼리 로그를 구성하는 방법](howto-configure-server-logs-in-portal.md)
- [Azure CLI에서 느린 쿼리 로그를 구성하는 방법](howto-configure-server-logs-in-cli.md).
