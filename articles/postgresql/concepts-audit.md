---
title: 감사 로깅 - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: PostgreSQL - 단일 서버에 대한 Azure 데이터베이스의 pgAudit 감사 로깅에 대한 개념입니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842472"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>PostgreSQL - 단일 서버에 대한 Azure 데이터베이스의 감사 로깅

PostgreSQL에 대한 Azure 데이터베이스의 데이터베이스 활동 감사 로깅 - 단일 서버는 PostgreSQL 감사 확장: [pgAudit](https://www.pgaudit.org/)를 통해 사용할 수 있습니다. pgAudit는 자세한 세션 및/또는 개체 감사 로깅을 제공합니다.

> [!NOTE]
> pgAudit는 PostgreSQL에 대한 Azure 데이터베이스에서 미리 보기입니다.
> 확장은 범용 및 메모리 최적화 된 서버에서만 사용할 수 있습니다.

계산 및 저장소 크기 조정과 같은 작업에 대한 Azure 리소스 수준 로그를 원하는 경우 [Azure 활동 로그](../azure-monitor/platform/platform-logs-overview.md)를 참조하십시오.

## <a name="usage-considerations"></a>용도 고려 사항
기본적으로 pgAudit 로그 문은 Postgres의 표준 로깅 기능을 사용하여 일반 로그 문과 함께 내보내집니다. Azure Database for PostgreSQL에서 이러한 .log 파일은 Azure Portal 또는 CLI를 통해 다운로드할 수 있습니다. 파일 컬렉션의 최대 저장소는 1GB이며 각 파일은 최대 7일 동안 사용할 수 있습니다(기본값은 3일). 이 서비스는 단기 저장소 옵션입니다.

또는 Azure Monitor의 진단 로그 서비스에 내보내도록 모든 로그를 구성할 수 있습니다. Azure Monitor 진단 로깅을 사용하도록 설정하면 선택한 항목에 따라 로그가 자동으로 Azure 저장소, 이벤트 허브 및/또는 Azure Monitor 로그로 전송됩니다(JSON 형식).

pgAudit를 사용하도록 설정하면 서버에서 많은 양의 로깅이 생성되어 성능 및 로그 스토리지에 영향을 줍니다. Azure 진단 로그 서비스를 사용하는 것이 좋습니다. 이 서비스는 분석 및 경고 기능뿐만 아니라 장기적인 스토리지 옵션을 제공합니다. 추가 로깅의 성능 영향을 줄이려면 표준 로깅을 해제하는 것이 좋습니다.

   1. 매개 변수를 `logging_collector` OFF로 설정합니다. 
   2. 서버를 다시 시작하여 이 변경 을 적용합니다.

Azure 저장소, 이벤트 허브 또는 Azure Monitor 로그에 대한 로깅을 설정하는 방법을 알아보려면 [서버 로그 문서의](concepts-server-logs.md)진단 로그 섹션을 방문하십시오.

## <a name="installing-pgaudit"></a>pgAudit 설치

pgAudit를 설치하려면 서버의 공유 프리로드 라이브러리에 포함해야 합니다. Postgres 매개 `shared_preload_libraries` 변수를 변경하려면 서버를 다시 시작해야 합니다. [Azure 포털,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)또는 [REST API를](/rest/api/postgresql/configurations/createorupdate)사용하여 매개 변수를 변경할 수 있습니다.

Azure [포털](https://portal.azure.com)사용:

   1. Azure Database for PostgreSQL 서버를 선택합니다.
   2. 사이드바에서 **서버 매개 변수를 선택합니다.**
   3. `shared_preload_libraries` 매개 변수를 검색합니다.
   4. **pgaudit를**선택합니다.
   5. 서버를 다시 시작하여 변경 을 적용합니다.

   6. 클라이언트(예: psql)를 사용하여 서버에 연결하고 pgAudit 확장을 사용하도록 설정합니다.
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 오류가 표시되면 저장한 `shared_preload_libraries`후 서버를 다시 시작했는지 확인합니다.

## <a name="pgaudit-settings"></a>pg감사 설정

pgAudit를 사용하면 세션 또는 개체 감사 로깅을 구성할 수 있습니다. [세션 감사 로깅은](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) 실행된 명령문의 자세한 로그를 내보온다. [개체 감사 로깅은](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) 특정 관계에 대한 감사 범위가 관리됩니다. 하나 또는 두 가지 유형의 로깅을 설정할 수 있습니다. 

> [!NOTE]
> pgAudit 설정은 gloabally로 지정되며 데이터베이스 또는 역할 수준에서 지정할 수 없습니다.

[pgAudit를 설치한](#installing-pgaudit)후에는 로깅을 시작하도록 해당 매개 변수를 구성할 수 있습니다. [pgAudit 설명서는](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) 각 매개 변수의 정의를 제공합니다. 매개 변수를 먼저 테스트하고 예상 된 동작을 가져오는지 확인합니다.

> [!NOTE]
> ON으로 설정하면 `pgaudit.log_client` 로그가 파일에 기록되는 대신 클라이언트 프로세스(예: psql)로 리디렉션됩니다. 이 설정은 일반적으로 사용하지 않도록 설정해야 합니다. <br> <br>
> `pgaudit.log_level`설정되어 있을 `pgaudit.log_client` 때만 활성화됩니다.

> [!NOTE]
> PostgreSQL용 Azure 데이터베이스에서는 pgAudit `pgaudit.log` `-` 설명서에 설명된 대로 (빼기) 기호 바로 가기를 사용하여 설정할 수 없습니다. 모든 필수 문 클래스(READ, WRITE 등)는 개별적으로 지정해야 합니다.

### <a name="audit-log-format"></a>감사 로그 형식
각 감사 항목은 `AUDIT:` 로그 줄의 시작 부분에 가깝게 표시됩니다. 항목의 나머지 형식은 [pgAudit 문서에](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)자세히 설명되어 있습니다.

감사 요구 사항을 충족하기 위해 다른 필드가 필요한 경우 `log_line_prefix`Postgres 매개 변수를 사용합니다. `log_line_prefix`은 Postgres 로그 라인의 시작 부분에서 출력되는 문자열입니다. 예를 들어 다음 `log_line_prefix` 설정은 타임스탬프, 사용자 이름, 데이터베이스 이름 및 프로세스 ID를 제공합니다.

```
t=%m u=%u db=%d pid=[%p]:
```

자세한 `log_line_prefix`내용은 [PostgreSQL 설명서를](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)참조하십시오.

### <a name="getting-started"></a>시작
빠르게 시작하려면 `pgaudit.log` 로그를 `WRITE`설정하고 열어 출력을 검토합니다. 

## <a name="viewing-audit-logs"></a>감사 로그 보기
.log 파일을 사용하는 경우 PostgreSQL 오류 로그와 동일한 파일에 감사 로그가 포함됩니다. Azure [포털](howto-configure-server-logs-in-portal.md) 또는 [CLI](howto-configure-server-logs-using-cli.md)에서 로그 파일을 다운로드할 수 있습니다. 

Azure 진단 로깅을 사용하는 경우 로그에 액세스하는 방법은 선택한 끝점에 따라 다릅니다. Azure 저장소의 경우 로그 저장소 계정 문서를 [참조하세요.](../azure-monitor/platform/resource-logs-collect-storage.md) 이벤트 허브의 경우 [Azure 로그 스트림 문서를 참조하세요.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Azure 모니터 로그의 경우 선택한 작업 영역으로 로그가 전송됩니다. Postgres 로그는 **AzureDiagnostics** 컬렉션 모드를 사용하므로 AzureDiagnostics 테이블에서 쿼리할 수 있습니다. 표의 필드는 아래에 설명되어 있습니다. [Azure Monitor 로그 쿼리](../azure-monitor/log-query/log-query-overview.md) 개요에서 쿼리 및 경고에 대해 자세히 알아봅니다.

이 쿼리를 사용하여 시작할 수 있습니다. 쿼리에 따라 경고를 구성할 수 있습니다.

마지막 날의 특정 서버에 대한 모든 Postgres 로그 검색
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>다음 단계
- [PostgreSQL용 Azure 데이터베이스로 로그인하는 방법에 대해 알아보기](concepts-server-logs.md)
- [Azure 포털,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)또는 [REST API를](/rest/api/postgresql/configurations/createorupdate)사용하여 매개 변수를 설정하는 방법에 대해 알아봅니다.
