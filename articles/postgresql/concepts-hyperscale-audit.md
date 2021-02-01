---
title: 감사 로깅-Azure Database for PostgreSQL-Hyperscale (Citus)
description: Azure Database for PostgreSQL-Hyperscale (Citus)의 pgAudit 감사 로깅에 대 한 개념입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: d8a21a5583ec4655a2ee8593e50be5c7b5f702b7
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99227607"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale의 감사 로깅 (Citus)

Azure Database for PostgreSQL-Hyperscale (Citus)에서 데이터베이스 작업의 감사 로깅은 PostgreSQL 감사 확장 ( [pgaudit](https://www.pgaudit.org/))을 통해 사용할 수 있습니다. pgAudit는 자세한 세션 및/또는 개체 감사 로깅을 제공 합니다.

> [!IMPORTANT]
> pgAudit는 Azure Database for PostgreSQL-Hyperscale (Citus)에 대 한 미리 보기 상태입니다.

계산 및 저장소 크기 조정과 같은 작업에 대 한 Azure 리소스 수준 로그를 원하는 경우 [Azure 활동 로그](../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요.

## <a name="usage-considerations"></a>용도 고려 사항
기본적으로 pgAudit 로그 문은 Postgres의 표준 로깅 기능을 사용하여 일반 로그 문과 함께 내보내집니다. Citus (Azure Database for PostgreSQL-Hyperscale)에서 Log Analytics의 이후 분석을 위해 Azure Monitor 로그 저장소로 전송 되는 모든 로그를 구성할 수 있습니다. Azure Monitor 리소스 로깅을 사용 하도록 설정 하면 사용자의 선택에 따라 로그가 자동으로 (JSON 형식으로) Azure Storage, Event Hubs 및/또는 Azure Monitor 로그에 전송 됩니다.

## <a name="enabling-pgaudit"></a>PgAudit 사용

PgAudit 확장은 모든 Citus (Hyperscale) 서버 그룹 노드에서 미리 설치 되 고 사용 하도록 설정 됩니다. 이 기능을 사용 하도록 설정 하는 데 필요한 작업은 없습니다.

## <a name="pgaudit-settings"></a>pgAudit 설정

pgAudit를 사용 하면 세션 또는 개체 감사 로깅을 구성할 수 있습니다. [세션 감사 로깅은](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) 실행 된 문의 자세한 로그를 내보냅니다. [개체 감사 로깅은](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) 특정 관계로 범위가 지정 된 감사입니다. 하나 또는 두 가지 로깅 유형을 설정 하도록 선택할 수 있습니다. 

> [!NOTE]
> pgAudit 설정은 전역적으로 지정 되며 데이터베이스 또는 역할 수준에서 지정할 수 없습니다.
>
> 또한 pgAudit 설정은 서버 그룹의 노드당 지정 됩니다. 모든 노드에서 변경 내용을 적용 하려면 각 노드에 개별적으로 적용 해야 합니다.

로그를 시작 하려면 pgAudit 매개 변수를 구성 해야 합니다. [Pgaudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) 는 각 매개 변수의 정의를 제공 합니다. 먼저 매개 변수를 테스트 하 고 예상 되는 동작을 확인 하 고 있는지 확인 합니다.

> [!NOTE]
> `pgaudit.log_client`을 ON으로 설정 하면 로그를 파일에 기록 하는 대신 클라이언트 프로세스 (예: psql)로 리디렉션합니다. 이 설정은 일반적으로 사용하지 않도록 설정해야 합니다. <br> <br>
> `pgaudit.log_level``pgaudit.log_client`는가 on 인 경우에만 사용할 수 있습니다.

> [!NOTE]
> Citus (Azure Database for PostgreSQL-Hyperscale)에서는 `pgaudit.log` `-` pgaudit 설명서에 설명 된 대로 (빼기) 기호 바로 가기를 사용 하 여 설정할 수 없습니다. 모든 필수 문 클래스 (읽기, 쓰기 등)는 개별적으로 지정 해야 합니다.

## <a name="audit-log-format"></a>감사 로그 형식
각 감사 항목은 `AUDIT:` 로그 줄의 시작 부분에 표시 됩니다. 항목의 나머지 형식은 [Pgaudit 설명서](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)에 자세히 설명 되어 있습니다.

## <a name="getting-started"></a>시작
신속 하 게 시작 하려면를 `pgaudit.log` 로 설정 하 `WRITE` 고 서버 로그를 열어 출력을 검토 합니다. 

## <a name="viewing-audit-logs"></a>감사 로그 보기
로그에 액세스 하는 방법은 선택한 끝점에 따라 다릅니다. Azure Storage는 [로그 저장소 계정](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) 문서를 참조 하세요. Event Hubs에 대해서는 [Stream Azure logs](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs) 문서를 참조 하세요.

Azure Monitor 로그의 경우 로그는 선택한 작업 영역으로 전송 됩니다. Postgres 로그 **는 azurediagnostics 수집 모드** 를 사용 하므로 azurediagnostics 테이블에서 쿼리할 수 있습니다. 테이블의 필드는 아래에 설명 되어 있습니다. [Azure Monitor 로그 쿼리](../azure-monitor/log-query/log-query-overview.md) 개요의 쿼리 및 경고에 대해 자세히 알아보세요.

이 쿼리를 사용 하 여 시작할 수 있습니다. 쿼리를 기반으로 경고를 구성할 수 있습니다.

마지막 날의 특정 서버에 대 한 Postgres logs의 모든 pgAudit 항목을 검색 합니다.
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>다음 단계

- [로깅을 설정 하는 방법에 대 한 자세한 내용은 Azure Database for PostgreSQL-Hyperscale (Citus) 및 로그 액세스 방법을 확인 하십시오.](howto-hyperscale-logging.md)
