---
title: 감사 로그-Azure Database for MySQL 유연한 서버
description: Azure Database for MySQL 유연한 서버에서 사용할 수 있는 감사 로그에 대해 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: e630ccec744c4edf0dc80d374ecc93526f960d44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295992"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버에서 감사 로그를 사용 하 여 데이터베이스 작업 추적

> [!IMPORTANT] 
> Azure Database for MySQL 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

유연한 서버 Azure Database for MySQL는 사용자에 게 감사 로그를 구성할 수 있는 기능을 제공 합니다. 감사 로그를 사용 하 여 연결, 관리, DDL 및 DML 이벤트를 비롯 한 데이터베이스 수준 작업을 추적할 수 있습니다. 이러한 유형의 로그는 일반적으로 규정 준수를 위해 사용됩니다.

## <a name="configure-audit-logging"></a>감사 로깅 구성

>[!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자를 기록 하는 것이 좋습니다.

기본적으로 감사 로그는 사용할 수 없습니다. 설정 하려면 `audit_log_enabled` 서버 매개 변수를 *ON*으로 설정 합니다. Azure Portal 또는 Azure CLI를 사용 하 여 구성할 수 있습니다. <!-- add link to server parameter-->. 

감사 로깅 동작을 제어 하기 위해 조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- `audit_log_events`: 로깅할 이벤트를 제어 합니다. 특정 감사 이벤트는 아래 표를 참조 하세요.
- `audit_log_include_users`: 로깅을 위해 포함할 MySQL 사용자입니다. 이 매개 변수의 기본값은 비어 있으며, 모든 사용자를 로깅할 수 있습니다. 이는 보다 우선 순위가 높습니다 `audit_log_exclude_users` . 매개 변수의 최대 길이는 512 자입니다.
- `audit_log_exclude_users`: 로그에서 제외할 MySQL 사용자입니다. 매개 변수의 최대 길이는 512 자입니다.

> [!NOTE]
> `audit_log_include_users` 보다 우선 순위가 높습니다 `audit_log_exclude_users` . 예를 들어 및의 경우 `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser` `audit_log_include_users` 우선 순위가 더 높기 때문에 사용자는 감사 로그에 포함 됩니다.

| **이벤트** | **설명** |
|---|---|
| `CONNECTION` | -연결 시작 (성공 또는 실패) <br> -사용자가 세션 중에 다른 사용자/암호를 사용 하 여 다시 인증 <br> -연결 종료 |
| `DML_SELECT`| SELECT 쿼리 |
| `DML_NONSELECT` | 쿼리 삽입/삭제/업데이트 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | "데이터베이스 삭제"와 같은 쿼리 |
| `DCL` | "권한 부여"와 같은 쿼리 |
| `ADMIN` | "SHOW STATUS"와 같은 쿼리 |
| `GENERAL` | DML_SELECT, DML_NONSELECT, DML, DDL, DCL 및 관리자의 모든 |
| `TABLE_ACCESS` | -MySQL 5.7에만 사용할 수 있음 <br> -SELECT 또는 INSERT INTO ...와 같은 테이블 읽기 문 [ <br> -DELETE 또는 TRUNCATE TABLE와 같은 Table delete 문 <br> -INSERT 또는 REPLACE와 같은 테이블 insert 문 <br> -UPDATE와 같은 테이블 업데이트 문 |

## <a name="access-audit-logs"></a>감사 로그 액세스

감사 로그는 Azure Monitor 진단 설정과 통합 됩니다. MySQL 유연한 서버에서 감사 로그를 사용 하도록 설정 하면 로그, Event Hubs 또는 Azure Storage Azure Monitor으로 내보낼 수 있습니다. 진단 설정에 대해 자세히 알아보려면 [진단 로그 설명서](../../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요. Azure Portal에서 진단 설정을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [감사 로그 포털 문서](how-to-configure-audit-logs-portal.md#set-up-diagnostics)를 참조 하세요.

다음 섹션에서는 이벤트 유형을 기반으로 하는 MySQL 감사 로그의 출력을 설명 합니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 출력 방법에 따라 달라질 수 있습니다.

### <a name="connection"></a>연결

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics`입니다. |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL`입니다. |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 서버의 이름 |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` , `CHANGE USER` (MySQL 5.7에만 사용 가능) |
| `connection_id_d` | MySQL에 의해 생성 된 고유 연결 ID |
| `host_s` | 비어 있음 |
| `ip_s` | MySQL에 연결 하는 클라이언트의 IP 주소 |
| `user_s` | 쿼리를 실행 하는 사용자의 이름입니다. |
| `db_s` | 연결 된 데이터베이스의 이름 |
| `\_ResourceId` | 리소스 URI |

### <a name="general"></a>일반

아래 스키마는 GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL 및 ADMIN 이벤트 유형에 적용 됩니다.

> [!NOTE]
> 의 경우 `sql_text_s` 로그는 2048 자를 초과 하는 경우 잘립니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics`입니다. |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL`입니다. |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 서버의 이름 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` , `RESULT` (MySQL 5.6에만 사용 가능) |
| `event_time` | 쿼리 시작 시간 (UTC 타임 스탬프) |
| `error_code_d` | 쿼리가 실패 한 경우 오류 코드입니다. `0` 오류가 없음을 의미 합니다. |
| `thread_id_d` | 쿼리를 실행 한 스레드의 ID |
| `host_s` | 비어 있음 |
| `ip_s` | MySQL에 연결 하는 클라이언트의 IP 주소 |
| `user_s` | 쿼리를 실행 하는 사용자의 이름입니다. |
| `sql_text_s` | 전체 쿼리 텍스트 |
| `\_ResourceId` | 리소스 URI |

### <a name="table-access"></a>테이블 액세스

> [!NOTE]
> 테이블 액세스 로그는 MySQL 5.7에 대해서만 출력 됩니다.<br>의 경우 `sql_text_s` 로그는 2048 자를 초과 하는 경우 잘립니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics`입니다. |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL`입니다. |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 서버의 이름 |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` 또는 `DELETE` |
| `connection_id_d` | MySQL에 의해 생성 된 고유 연결 ID |
| `db_s` | 액세스 한 데이터베이스 이름 |
| `table_s` | 액세스 한 테이블 이름 |
| `sql_text_s` | 전체 쿼리 텍스트 |
| `\_ResourceId` | 리소스 URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure Monitor 로그의 로그 분석

감사 로그가 진단 로그를 통해 Azure Monitor 로그를 파이프 하면 감사 된 이벤트에 대 한 추가 분석을 수행할 수 있습니다. 다음은 시작 하는 데 도움이 되는 몇 가지 샘플 쿼리입니다. 아래를 서버 이름으로 업데이트 해야 합니다.

- 특정 서버에 대 한 일반 이벤트 나열

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- 특정 서버에 대 한 연결 이벤트 나열

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- 특정 서버에서 감사 된 이벤트 요약

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- 특정 서버에 대 한 감사 이벤트 유형 배포 그래프

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- 감사 로그에 대해 진단 로그를 사용 하는 모든 MySQL 서버에서 감사 된 이벤트 나열

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>다음 단계
- [저속 쿼리 로그](concepts-slow-query-logs.md) 에 대 한 자세한 정보
- [Azure Portal](how-to-configure-audit-logs-portal.md) 에서 감사 쿼리 로그 구성
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->