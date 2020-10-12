---
title: 로그-Azure Database for PostgreSQL-유연한 서버
description: Azure Database for PostgreSQL 유연한 서버에서 로깅 구성, 저장소 및 분석에 대해 설명 합니다.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 852bce26e348c817b609d5ff837641741afe4461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940396"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL-유연한 서버의 로그

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

Azure Database for PostgreSQL를 사용 하 여 Postgres의 표준 로그를 구성 하 고 액세스할 수 있습니다. 로그를 사용 하 여 구성 오류를 식별 하 고, 문제를 해결 하 고, 문제를 해결할 수 있습니다. 구성 및 액세스할 수 있는 로깅 정보에는 오류, 쿼리 정보, autovacuum 레코드, 연결 및 검사점이 포함 됩니다. (트랜잭션 로그에 대 한 액세스를 사용할 수 없습니다.)

감사 로깅은 Postgres 확장을 통해 사용할 수 `pgaudit` 있습니다. 자세히 알아보려면 [감사 개념](concepts-audit.md) 문서를 참조 하세요.

## <a name="configure-logging"></a>로깅 구성

로깅 서버 매개 변수를 사용 하 여 서버에서 Postgres 표준 로깅을 구성할 수 있습니다. Postgres 로그 매개 변수에 대 한 자세한 내용을 보려면 Postgres 설명서의 [시기를 기록](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) 하는 시기 및 [로깅할 항목](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) 섹션을 참조 하세요. Azure Database for PostgreSQL에서 대부분의 Postgres 로깅 매개 변수를 구성할 수 있습니다.

Azure Database for PostgreSQL에서 매개 변수를 구성 하는 방법에 대 한 자세한 내용은 [포털 설명서](howto-configure-server-parameters-using-portal.md) 또는 [CLI 설명서](howto-configure-server-parameters-using-cli.md)를 참조 하세요.

> [!NOTE]
> 문 로깅과 같이 많은 양의 로그를 구성 하면 상당한 성능 오버 헤드를 추가할 수 있습니다. 

## <a name="accessing-logs"></a>로그 액세스

Azure Database for PostgreSQL은 Azure Monitor 진단 설정과 통합 됩니다. 진단 설정을 사용 하면 분석 및 경고를 위해 Postgres 로그를 JSON 형식으로 Azure Monitor 하 고, 스트리밍 Event Hubs 하 고, 보관에 Azure Storage 보낼 수 있습니다. 

### <a name="log-format"></a>로그 형식

다음 표에서는 **PostgreSQLLogs** 형식에 대 한 필드를 설명 합니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 선택한 출력 엔드포인트에 따라 달라질 수 있습니다. 

|**필드** | **설명** |
|---|---|
| TenantId | 테넌트 ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| Type | 로그의 형식 항상 `AzureDiagnostics`입니다. |
| SubscriptionId | 서버가 속한 구독의 GUID |
| ResourceGroup | 서버가 속한 리소스 그룹의 이름 |
| ResourceProvider | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORPOSTGRESQL`입니다. |
| ResourceType | `Servers` |
| ResourceId | 리소스 URI |
| 리소스 | 서버의 이름 |
| 범주 | `PostgreSQLLogs` |
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
| 접두사 | 로그 줄의 접두사 |


## <a name="next-steps"></a>다음 단계

- [로그를 구성 하 고 액세스](howto-configure-and-access-logs.md)하는 방법에 대해 자세히 알아보세요.
- [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)에 대해 자세히 알아보세요.
- [감사 로그](concepts-audit.md) 에 대해 자세히 알아보기
