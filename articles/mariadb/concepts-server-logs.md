---
title: Azure Database for MariaDB의 서버 로그
description: Azure Database for MariaDB에서 사용할 수 있는 로그와, 다양한 로깅 수준을 활성화하는 데 사용할 수 있는 매개 변수에 대해 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 7a517be49a249b0b73c901137381bd05946aa4cc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065712"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 로그인으로 느린 쿼리
Azure Database for MariaDB에서는 사용자에게 느린 쿼리 로그를 제공합니다. 트랜잭션 로그에 대한 액세스는 지원되지 않습니다. 느린 쿼리 로그를 사용하여 문제 해결을 위한 성능 병목을 파악할 수 있습니다.

느린 쿼리 로그에 대한 자세한 내용은 MariaDB 설명서에서 [느린 쿼리 로그](https://mariadb.com/kb/en/library/slow-query-log-overview/)를 참조하세요.

## <a name="access-slow-query-logs"></a>느린 쿼리 로그 액세스
목록 및 Azure portal 및 Azure CLI를 사용 하 여 MariaDB 느린 쿼리 로그에 대 한 Azure Database를 다운로드할 수 있습니다.

Azure Portal에서 Azure Database for MariaDB 서버를 찾습니다. **모니터링** 머리글 아래에서 **서버 로그** 페이지를 선택합니다.

Azure CLI에 대한 자세한 내용은 [Azure CLI를 사용한 서버 로그 구성 및 액세스](howto-configure-server-logs-cli.md)를 참조하세요.

## <a name="log-retention"></a>로그 보존
로그는 생성 시점에서 최대 7일까지 사용할 수 있습니다. 사용 가능한 로그의 전체 크기가 7GB를 초과하면 여유 공간이 생길 때까지 가장 오래된 파일이 삭제됩니다.

즉, 24시간이 지나거나 전체 크기가 7GB를 초과할 때마다(먼저 해당되는 쪽) 로그가 가장 오래된 파일부터 삭제됩니다.

## <a name="configure-slow-query-logging"></a>느린 쿼리 로깅 구성
기본적으로 느린 쿼리 로그는 비활성화됩니다. 활성화하려면 slow_query_log를 ON으로 설정합니다.

조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- **long_query_time**: 쿼리가 기록되는 long_query_time(초)보다 쿼리가 오래 걸릴 경우 기본값은 10초입니다.
- **log_slow_admin_statements**: ON에 slow_query_log에 쓰여진 문에서 ALTER_TABLE 및 ANALYZE_TABLE 등과 같은 관리 문이 포함된 경우
- **log_queries_not_using_indexes**: 인덱스를 사용하지 않는 쿼리가 slow_query_log에 기록되는지 여부를 결정합니다.
- **log_throttle_queries_not_using_indexes**: 이 매개 변수는 느린 쿼리 로그에 쓸 수 있는 인덱스가 아닌 쿼리 수의 한도를 결정합니다. 이 매개 변수는 log_queries_not_using_indexes가 ON으로 설정된 경우 적용됩니다.

느린 쿼리 로그 매개 변수의 전체 설명은 MariaDB [느린 쿼리 로그 설명서](https://mariadb.com/kb/en/library/slow-query-log-overview/)를 참조하세요.

## <a name="diagnostic-logs"></a>진단 로그
Azure Database for MariaDB는 Azure Monitor 진단 로그와 통합 됩니다. MariaDB 서버의 느린 쿼리 로그를 설정한 후에 Azure Monitor 로그, Event Hubs 또는 Azure Storage에 내보낸에 선택할 수 있습니다. 진단 로그를 사용하도록 설정하는 방법에 대한 자세한 내용은 [진단 로그 설명서](../azure-monitor/platform/diagnostic-logs-overview.md)의 방법 섹션을 참조하세요.

> [!IMPORTANT]
> 서버 로그에 대한 진단 기능은 범용 및 메모리 최적화 [가격 책정 계층](concepts-pricing-tiers.md)에만 사용할 수 있습니다.

아래 표에는 각 로그의 내용에 대한 설명이 나와 있습니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 출력 방법에 따라 달라질 수 있습니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics` |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | 서버의 이름 |
| `start_time_t` [UTC] | 쿼리가 시작된 시간 |
| `query_time_s` | 쿼리를 실행하는 데 걸린 총 시간 |
| `lock_time_s` | 쿼리가 잠긴 총 시간 |
| `user_host_s` | 사용자 이름 |
| `rows_sent_s` | 전송된 행 수 |
| `rows_examined_s` | 검사된 행 수 |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | ID를 삽입 합니다. |
| `sql_text_s` | 전체 쿼리 |
| `server_id_s` | 서버 ID |
| `thread_id_s` | 스레드 ID |
| `\_ResourceId` | 리소스 URI |

## <a name="next-steps"></a>다음 단계
- [Azure Portal에서 서버 로그를 구성하고 액세스하는 방법](howto-configure-server-logs-portal.md)
