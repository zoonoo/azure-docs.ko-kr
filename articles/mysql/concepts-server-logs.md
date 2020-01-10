---
title: 느리게 쿼리 로그-Azure Database for MySQL
description: Azure Database for MySQL에서 사용할 수 있는 저속 쿼리 로그와 다양 한 로깅 수준을 사용 하도록 설정 하는 데 사용할 수 있는 매개 변수를 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 9b661a7fa6a7b9f079a3b24d1b83f27118c4bd23
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745858"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL의 저속 쿼리 로그
Azure Database for MySQL에서는 사용자에게 느린 쿼리 로그를 제공합니다. 트랜잭션 로그에 대한 액세스는 지원되지 않습니다. 느린 쿼리 로그를 사용하여 문제 해결을 위한 성능 병목을 파악할 수 있습니다.

MySQL 느린 쿼리 로그에 대한 자세한 내용은 MySQL 참조 설명서의 [느린 쿼리 로그 섹션](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)을 참조하세요.

## <a name="access-slow-query-logs"></a>저속 쿼리 로그 액세스
Azure Portal 및 Azure CLI를 사용 하 여 Azure Database for MySQL 느리게 쿼리 로그를 나열 하 고 다운로드할 수 있습니다.

Azure Portal에서 Azure Database for MySQL Server를 찾습니다. **모니터링** 머리글 아레에서 **서버 로그** 페이지를 선택합니다.

Azure CLI에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 저속 쿼리 로그 구성 및 액세스](howto-configure-server-logs-in-cli.md)를 참조 하세요.

마찬가지로 진단 로그를 사용 하 여 Azure Monitor로 로그를 파이프 할 수 있습니다. 자세한 내용은 [아래](concepts-server-logs.md#diagnostic-logs) 를 참조 하세요.

## <a name="log-retention"></a>로그 보존
로그는 생성 시점에서 최대 7일까지 사용할 수 있습니다. 사용 가능한 로그의 전체 크기가 7GB를 초과하면 여유 공간이 생길 때까지 가장 오래된 파일이 삭제됩니다. 

즉, 24시간이 지나거나 전체 크기가 7GB를 초과할 때마다(먼저 해당되는 쪽) 로그가 가장 오래된 파일부터 삭제됩니다.

## <a name="configure-slow-query-logging"></a>저속 쿼리 로깅 구성 
기본적으로 느린 쿼리 로그는 비활성화됩니다. 활성화하려면 slow_query_log를 ON으로 설정합니다.

조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- **long_query_time**: 쿼리가 기록되는 long_query_time(초)보다 쿼리가 오래 걸릴 경우 기본값은 10초입니다.
- **log_slow_admin_statements**: ON에 slow_query_log에 쓰여진 문에서 ALTER_TABLE 및 ANALYZE_TABLE 등과 같은 관리 문이 포함된 경우
- **log_queries_not_using_indexes**: 인덱스를 사용하지 않는 쿼리가 slow_query_log에 기록되는지 여부를 결정합니다.
- **log_throttle_queries_not_using_indexes**:이 매개 변수는 느린 쿼리 로그에 쓸 수 있는 비 인덱스 쿼리의 수 한도를 결정합니다. 이 매개 변수는 log_queries_not_using_indexes가 ON으로 설정된 경우 적용됩니다.
- **log_output**: "File" 이면 저속 쿼리 로그가 로컬 서버 저장소에 기록 되 고 진단 로그를 Azure Monitor 수 있습니다. "None" 인 경우 저속 쿼리 로그는 Azure Monitor 진단 로그에만 기록 됩니다. 

> [!Note]
> `sql_text`의 경우 로그는 2048 자를 초과 하는 경우 잘립니다.

느린 쿼리 로그 매개 변수의 전체 설명은 MySQL [느린 쿼리 로그 설명서](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)를 참조하세요.

## <a name="diagnostic-logs"></a>진단 로그
Azure Database for MySQL은 Azure Monitor 진단 로그와 통합됩니다. MySQL 서버의 느린 쿼리 로그를 설정한 후에 Azure Monitor 로그, Event Hubs 또는 Azure Storage에 내보내는 것을 선택할 수 있습니다. 진단 로그를 사용하도록 설정하는 방법에 대한 자세한 내용은 [진단 로그 설명서](../azure-monitor/platform/platform-logs-overview.md)의 방법 섹션을 참조하세요.

> [!IMPORTANT]
> 이 서버 로그에 대한 진단 기능은 범용 및 메모리 최적화 [가격 책정 계층](concepts-pricing-tiers.md)에서만 사용할 수 있습니다.

아래 표에는 각 로그의 내용에 대한 설명이 나와 있습니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 출력 방법에 따라 달라질 수 있습니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
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
| `start_time_t` [UTC] | 쿼리가 시작된 시간 |
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

## <a name="next-steps"></a>다음 단계
- [Azure CLI에서 서버 로그 구성 및 액세스](howto-configure-server-logs-in-cli.md)
