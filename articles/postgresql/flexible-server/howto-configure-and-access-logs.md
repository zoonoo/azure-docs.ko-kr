---
title: 로그 구성 및 액세스-유연한 서버-Azure Database for PostgreSQL
description: Azure Database for PostgreSQL-유연한 서버에 대 한 데이터베이스 로그에 액세스 하는 방법
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0b7b09696cbbe12a57d066e452b4c8ea7a7b8f27
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938844"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 유연한 서버에서 로그 구성 및 액세스

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

PostgreSQL 로그는 유연한 서버의 모든 노드에서 사용할 수 있습니다. 저장소 서버 또는 분석 서비스에 로그를 제공할 수 있습니다. 로그를 사용 하 여 구성 오류를 식별 하 고, 문제를 해결 하 고, 문제를 해결할 수 있습니다.

## <a name="configure-diagnostic-settings"></a>진단 설정 구성

Azure Portal, CLI, REST API 및 PowerShell을 사용 하 여 Postgres server에 대 한 진단 설정을 사용 하도록 설정할 수 있습니다. 선택할 로그 범주는 **PostgreSQLLogs**입니다.

Azure Portal를 사용 하 여 리소스 로그를 사용 하도록 설정 하려면

1. 포털에서 Postgres server의 탐색 메뉴에 있는 *진단 설정* 으로 이동 합니다.
   
2. *진단 설정 추가*를 선택 합니다.
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="진단 설정 추가 단추":::

3. 이 설정의 이름을로 설정 합니다. 

4. 기본 설정 끝점 (저장소 계정, 이벤트 허브, log analytics)을 선택 합니다. 

5. **PostgreSQLLogs**로그 유형을 선택 합니다.
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="PostgreSQL 로그 선택":::

7. 설정을 저장합니다.

PowerShell, CLI 또는 REST API를 사용 하 여 리소스 로그를 사용 하도록 설정 하려면 [진단 설정](../../azure-monitor/platform/diagnostic-settings.md) 문서를 참조 하세요.

### <a name="access-resource-logs"></a>리소스 로그 액세스

로그에 액세스 하는 방법은 선택한 끝점에 따라 다릅니다. Azure Storage는 [로그 저장소 계정](../../azure-monitor/platform/resource-logs-collect-storage.md) 문서를 참조 하세요. Event Hubs에 대해서는 [Stream Azure logs](../../azure-monitor/platform/resource-logs-stream-event-hubs.md) 문서를 참조 하세요.

Azure Monitor 로그의 경우 로그는 선택한 작업 영역으로 전송 됩니다. Postgres 로그 **는 azurediagnostics 수집 모드** 를 사용 하므로 azurediagnostics 테이블에서 쿼리할 수 있습니다. 테이블의 필드는 아래에 설명 되어 있습니다. [Azure Monitor 로그 쿼리](../../azure-monitor/log-query/log-query-overview.md) 개요의 쿼리 및 경고에 대해 자세히 알아보세요.

다음은 시작 하기 위해 시도할 수 있는 쿼리입니다. 쿼리를 기반으로 경고를 구성할 수 있습니다.

마지막 날에 특정 서버에 대 한 모든 Postgres 로그를 검색 합니다.

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

모든 localhost가 아닌 연결 시도를 검색 합니다.

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

위의 쿼리는이 작업 영역에 있는 Postgres 서버 로깅에 대해 최근 6 시간 동안의 결과를 표시 합니다.

## <a name="next-steps"></a>다음 단계

- [Log analytics 쿼리 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
- [Azure event hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) 에 대 한 자세한 정보
