---
title: 로그 - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: PostgreSQL - 단일 서버에 대한 Azure 데이터베이스의 로깅 구성, 저장소 및 분석에 대해 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280496"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>PostgreSQL용 Azure 데이터베이스의 로그 - 단일 서버
PostgreSQL용 Azure 데이터베이스를 사용하면 Postgres의 표준 로그를 구성하고 액세스할 수 있습니다. 로그를 사용하여 구성 오류 및 최적이 아닌 성능을 식별, 문제 해결 및 복구할 수 있습니다. 구성하고 액세스할 수 있는 로깅 정보에는 오류, 쿼리 정보, autovacuum 레코드, 연결 및 검사점이 포함됩니다. (트랜잭션 로그에 대한 액세스를 사용할 수 없습니다.)

감사 로깅은 Postgres 확장, pgaudit를 통해 사용할 수 있습니다. 자세한 내용은 감사 [개념](concepts-audit.md) 문서를 참조하십시오.


## <a name="configure-logging"></a>로깅 구성 
로깅 서버 매개 변수를 사용하여 서버에 Postgres 표준 로깅을 구성할 수 있습니다. PostgreSQL 서버에 `log_checkpoints` 대한 각 Azure `log_connections` 데이터베이스에서 기본적으로 설정됩니다. 로깅 요구에 맞게 매개 변수를 추가로 조정할 수도 있습니다. 

![Azure Database for PostgreSQL - 로깅 매개 변수](./media/concepts-server-logs/log-parameters.png)

Postgres 로그 매개 변수에 대해 자세히 알아보려면 Postgres 설명서의 [로그 할 시간](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) 및 [로그 할 것](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) 섹션을 방문하십시오. 전부는 아니지만 Postgres 로깅 매개 변수는 PostgreSQL용 Azure 데이터베이스에서 구성할 수 있습니다.

PostgreSQL에 대한 Azure 데이터베이스에서 매개 변수를 구성하는 방법에 대해 알아보려면 [포털 설명서](howto-configure-server-parameters-using-portal.md) 또는 [CLI 설명서를](howto-configure-server-parameters-using-cli.md)참조하십시오. 

> [!NOTE]
> 대용량 로그(예: 문 로깅)를 구성하면 상당한 성능 오버헤드가 추가될 수 있습니다. 

## <a name="access-log-files"></a>.log 파일 액세스
PostgreSQL에 대한 Azure 데이터베이스의 기본 로그 형식은 .log입니다. 이 로그의 샘플 줄은 다음과 같습니다.

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

PostgreSQL용 Azure 데이터베이스는 .log 파일에 대한 단기 저장소 위치를 제공합니다. 새 파일은 1시간 또는 100MB마다 시작됩니다. 로그는 Postgres에서 내보내지면서 현재 파일에 추가됩니다.  

`log_retention_period` 매개 변수를 사용하여 이 단기 로그 저장소의 보존 기간을 설정할 수 있습니다. 기본값은 3일이며 최대값은 7일입니다. 단기 저장소 위치에는 최대 1GB의 로그 파일을 보관할 수 있습니다. 1GB 가 지나면 보존 기간에 관계없이 가장 오래된 파일이 삭제되어 새 로그를 위한 공간을 확보합니다. 

로그 및 로그 분석을 장기적으로 보존하려면 .log 파일을 다운로드하여 타사 서비스로 이동할 수 있습니다. [Azure 포털](howto-configure-server-logs-in-portal.md), [Azure CLI를](howto-configure-server-logs-using-cli.md)사용하여 파일을 다운로드할 수 있습니다. 또는 JSON 형식의 로그를 장기 위치로 자동으로 내보하는 Azure Monitor 진단 설정을 구성할 수 있습니다. 아래 섹션에서 이 옵션에 대해 자세히 알아보십시오. 

매개 변수를 `logging_collector` OFF로 설정하여 .log 파일 생성을 중지할 수 있습니다. Azure Monitor 진단 설정을 사용하는 경우 .log 파일 생성을 끄는 것이 좋습니다. 이 구성은 추가 로깅의 성능 영향을 줄입니다.

## <a name="diagnostic-logs"></a>진단 로그
PostgreSQL용 Azure 데이터베이스는 Azure 모니터 진단 설정과 통합됩니다. 진단 설정을 사용하면 분석 및 경고를 위해 JSON 형식으로 Postgres 로그를 Azure Monitor Logs, 스트리밍용 이벤트 허브 및 보관용 Azure 저장소로 보낼 수 있습니다. 

> [!IMPORTANT]
> 서버 로그에 대한 이 진단 기능은 범용 및 메모리 [최적화가격 책정 계층에서만](concepts-pricing-tiers.md)사용할 수 있습니다.


### <a name="configure-diagnostic-settings"></a>진단 설정 구성
Azure 포털, CLI, REST API 및 Powershell을 사용하여 Postgres 서버에 대한 진단 설정을 활성화할 수 있습니다. 선택할 로그 범주는 **PostgreSQLLogs**입니다. 쿼리 [저장소를](concepts-query-store.md)사용하는 경우 구성할 수 있는 다른 로그가 있습니다.

Azure 포털을 사용하여 진단 로그를 사용하려면 다음을 수행합니다.

   1. 포털에서 Postgres 서버의 탐색 메뉴에서 *진단 설정으로* 이동합니다.
   2. *진단 설정 추가를*선택합니다.
   3. 이 설정의 이름을 지정합니다. 
   4. 원하는 끝점(저장소 계정, 이벤트 허브, 로그 분석)을 선택합니다. 
   5. 로그 유형 **PostgreSQLLogs를 선택합니다.**
   7. 설정을 저장합니다.

Powershell, CLI 또는 REST API를 사용하여 진단 로그를 사용하려면 [진단 설정](../azure-monitor/platform/diagnostic-settings.md) 문서를 방문하십시오.

### <a name="access-diagnostic-logs"></a>진단 로그 액세스

로그에 액세스하는 방법은 선택한 끝점에 따라 다릅니다. Azure 저장소의 경우 로그 저장소 계정 문서를 [참조하세요.](../azure-monitor/platform/resource-logs-collect-storage.md) 이벤트 허브의 경우 [Azure 로그 스트림 문서를 참조하세요.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Azure 모니터 로그의 경우 선택한 작업 영역으로 로그가 전송됩니다. Postgres 로그는 **AzureDiagnostics** 컬렉션 모드를 사용하므로 AzureDiagnostics 테이블에서 쿼리할 수 있습니다. 표의 필드는 아래에 설명되어 있습니다. [Azure Monitor 로그 쿼리](../azure-monitor/log-query/log-query-overview.md) 개요에서 쿼리 및 경고에 대해 자세히 알아봅니다.

다음은 시작하려고 시도할 수 있는 쿼리입니다. 쿼리에 따라 경고를 구성할 수 있습니다.

마지막 날의 특정 서버에 대한 모든 Postgres 로그 검색
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

로컬호스트가 아닌 모든 연결 시도 검색
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
위의 쿼리는 이 작업 영역에서 Postgres 서버 로깅에 대한 지난 6시간 동안의 결과를 표시합니다.

### <a name="log-format"></a>로그 형식

다음 표는 **PostgreSQLLogs** 형식의 필드를 설명합니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 선택한 출력 엔드포인트에 따라 달라질 수 있습니다. 

|**필드** | **설명** |
|---|---|
| TenantId | 테넌트 ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| Type | 로그의 형식 항상 `AzureDiagnostics` |
| SubscriptionId | 서버가 속한 구독의 GUID |
| ResourceGroup | 서버가 속한 리소스 그룹의 이름 |
| ResourceProvider | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | 리소스 URI |
| 리소스 | 서버의 이름 |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | 로깅 수준(예: LOG, ERROR, NOTICE) |
| 메시지 | 기본 로그 메시지 | 
| 도메인 | 서버 버전(예: postgres-10) |
| 세부 정보 | 보조 로그 메시지(해당하는 경우) |
| ColumnName | 열 이름(해당하는 경우) |
| SchemaName | 스키마 이름(해당하는 경우) |
| DatatypeName | 데이터 형식 이름(해당하는 경우) |
| LogicalServerName | 서버의 이름 | 
| _ResourceId | 리소스 URI |
| 접두사 | 로그 라인의 접두사 |


## <a name="next-steps"></a>다음 단계
- [Azure Portal](howto-configure-server-logs-in-portal.md) 또는 [Azure CLI](howto-configure-server-logs-using-cli.md)에서 로그에 액세스하는 방법에 대해 자세히 알아봅니다.
- [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/)에 대해 자세히 알아봅니다.
- [감사 로그에](concepts-audit.md) 대해 자세히 알아보기
