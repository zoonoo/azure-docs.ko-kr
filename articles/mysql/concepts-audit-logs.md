---
title: 감사 로그 - MySQL용 Azure 데이터베이스
description: MySQL용 Azure 데이터베이스에서 사용할 수 있는 감사 로그와 로깅 수준을 활성화하기 위한 사용 가능한 매개 변수에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: b42f0d7a8146f7f2b313959273abd22303c89a60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062555"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>MySQL에 대한 Azure 데이터베이스의 로그 감사

MySQL용 Azure 데이터베이스에서 사용자가 감사 로그를 사용할 수 있습니다. 감사 로그는 데이터베이스 수준 활동을 추적하는 데 사용할 수 있으며 일반적으로 규정 준수에 사용됩니다.

> [!IMPORTANT]
> 감사 로그 기능은 현재 미리 보기 상태입니다.

## <a name="configure-audit-logging"></a>감사 로깅 구성

기본적으로 감사 로그는 비활성화됩니다. 활성화하려면 ON으로 `audit_log_enabled` 설정합니다.

조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- `audit_log_events`: 기록할 이벤트를 제어합니다. 특정 감사 이벤트는 아래 표를 참조하십시오.
- `audit_log_include_users`: MySQL 사용자가 로깅에 포함될 수 있습니다. 이 매개 변수의 기본값은 비어 있으며 로깅을 위한 모든 사용자가 포함됩니다. 이 보다 우선 `audit_log_exclude_users`순위가 높습니다. 매개 변수의 최대 길이는 512자입니다.
> [!Note]
> `audit_log_include_users`보다 높은 `audit_log_exclude_users`우선 순위가 있습니다. 예를 들어 `audit_log_include_users`  =  `demouser` 우선 `audit_log_exclude_users`  =  `demouser`순위가 높기 때문에 `audit_log_include_users` 사용자가 감사 로그에 포함될 경우 및
- `audit_log_exclude_users`: MySQL 사용자가 로깅에서 제외됩니다. 매개 변수의 최대 길이는 512자입니다.

> [!Note]
> 에 `sql_text`대 한 로그는 2048 자를 초과 하는 경우 잘립니다.

| **이벤트** | **설명** |
|---|---|
| `CONNECTION` | - 연결 개시(성공 또는 실패) <br> - 세션 중 다른 사용자 / 암호로 사용자 재인증 <br> - 연결 종료 |
| `DML_SELECT`| SELECT 쿼리 |
| `DML_NONSELECT` | 쿼리 삽입/삭제/업데이트 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | "데이터베이스 삭제"와 같은 쿼리 |
| `DCL` | "권한 부여"와 같은 쿼리 |
| `ADMIN` | "상태 표시"와 같은 쿼리 |
| `GENERAL` | DML_SELECT, DML_NONSELECT, DML, DDL, DCL 및 관리자의 모든 |
| `TABLE_ACCESS` | - MySQL 5.7에서만 사용 가능 <br> - 선택 또는 삽입과 같은 테이블 읽기 문 ... 선택 <br> - 삭제 또는 TRUNCATE 테이블과 같은 테이블 삭제 문 <br> - 삽입 또는 바꾸기와 같은 테이블 삽입 문 <br> - 업데이트와 같은 테이블 업데이트 문 |

## <a name="access-audit-logs"></a>감사 로그 액세스

감사 로그는 Azure 모니터 진단 로그와 통합됩니다. MySQL 서버에서 감사 로그를 사용하도록 설정한 후에는 Azure 모니터 로그, 이벤트 허브 또는 Azure 저장소로 로그를 내보엠버로 보내줄 수 있습니다. Azure 포털에서 진단 로그를 사용하도록 설정하는 방법에 대한 자세한 내용은 [감사 로그 포털 문서를](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)참조하십시오.

## <a name="diagnostic-logs-schemas"></a>진단 로그 스키마

다음 섹션에서는 이벤트 유형에 따라 MySQL 감사 로그에 의해 출력되는 내용을 설명합니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 출력 방법에 따라 달라질 수 있습니다.

### <a name="connection"></a>연결

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics` |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 서버의 이름 |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` `CHANGE USER` (MySQL 5.7에서만 사용 가능) |
| `connection_id_d` | MySQL에서 생성된 고유 연결 ID |
| `host_s` | 비어 있음 |
| `ip_s` | MySQL에 연결하는 클라이언트의 IP 주소 |
| `user_s` | 쿼리를 실행하는 사용자 이름 |
| `db_s` | 연결된 데이터베이스 이름 |
| `\_ResourceId` | 리소스 URI |

### <a name="general"></a>일반

아래 스키마는 일반, DML_SELECT, DML_NONSELECT, DML, DDL, DCL 및 ADMIN 이벤트 유형에 적용됩니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics` |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 서버의 이름 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` `RESULT` (MySQL 5.6에서만 사용 가능) |
| `event_time` | UTC 타임스탬프의 쿼리 시작 시간 |
| `error_code_d` | 쿼리에 실패한 경우 오류 코드입니다. `0`오류 없음을 의미합니다. |
| `thread_id_d` | 쿼리를 실행한 스레드의 ID |
| `host_s` | 비어 있음 |
| `ip_s` | MySQL에 연결하는 클라이언트의 IP 주소 |
| `user_s` | 쿼리를 실행하는 사용자 이름 |
| `sql_text_s` | 전체 쿼리 텍스트 |
| `\_ResourceId` | 리소스 URI |

### <a name="table-access"></a>테이블 액세스

> [!NOTE]
> 테이블 액세스 로그는 MySQL 5.7에 대해서만 출력됩니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics` |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 서버의 이름 |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` 또는 `DELETE` |
| `connection_id_d` | MySQL에서 생성된 고유 연결 ID |
| `db_s` | 액세스한 데이터베이스 이름 |
| `table_s` | 액세스한 테이블 이름 |
| `sql_text_s` | 전체 쿼리 텍스트 |
| `\_ResourceId` | 리소스 URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure 모니터 로그에서 로그 분석

감사 로그가 진단 로그를 통해 Azure 모니터 로그에 파이프되면 감사된 이벤트에 대한 추가 분석을 수행할 수 있습니다. 다음은 시작하는 데 도움이 되는 몇 가지 샘플 쿼리입니다. 서버 이름으로 아래를 업데이트하십시오.

- 특정 서버에 일반 이벤트 나열

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- 특정 서버의 연결 이벤트 목록

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- 특정 서버에서 감사된 이벤트 요약

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- 특정 서버에서 감사 이벤트 유형 분포 그래프

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- 감사 로그에 사용할 수 있는 진단 로그를 사용하는 모든 MySQL 서버에서 감사된 이벤트를 나열합니다.

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>다음 단계

- [Azure 포털에서 감사 로그를 구성하는 방법](howto-configure-audit-logs-portal.md)