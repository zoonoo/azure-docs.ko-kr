---
title: Azure Monitor 로그의 구조 | Microsoft Docs
description: Azure Monitor에서 로그 데이터를 검색하려면 로그 쿼리가 필요합니다.  이 문서는 Azure Monitor에서 새 로그 쿼리가 어떻게 사용되는지를 설명하고 새로 만들기 전에 이해해야 하는 개념을 제공합니다.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: e243ebbc31f9e941678ac76a83738276995b5ba1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297135"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure Monitor 로그의 구조
신속 하 게 사용 하 여 데이터에 대 한 통찰력을 얻을 수 있도록을 [로그 쿼리](log-query-overview.md) Azure Monitor의 강력한 기능입니다. 효율적이 고 유용한 쿼리를 만들려면 원하는 데이터의 위치 및 구조와 같은 몇 가지 기본 개념을 이해 해야 합니다. 이 문서에서는 시작 하는 데 필요한 기본 개념을 제공 합니다.

## <a name="overview"></a>개요
Azure Monitor 로그에서 데이터를 Log Analytics 작업 영역 또는 Application Insights 응용 프로그램에 저장 됩니다. 둘 다에 의해 제공 됩니다 [Azure Data Explorer](/azure/data-explorer/) 강력한 데이터 엔진 및 쿼리 언어를 활용 하는 것을 의미 합니다.

작업 영역 및 응용 프로그램에서 데이터는 여러 종류의 데이터를 저장 하 고 자체 고유한 속성 집합에는 각 테이블로 구성 됩니다. 대부분 [데이터 원본](../platform/data-sources.md) 에서 Application Insights에서 Application Insights 응용 프로그램에서 테이블의 미리 정의 된 집합에 기록 하는 동안는 Log Analytics 작업 영역에서 고유한 테이블에 기록 합니다. 로그 쿼리는 매우 유연한 쉽게 여러 테이블의 데이터를 결합 하 여도 리소스 간 쿼리를 사용 하 여 작업 영역 및 응용 프로그램 데이터를 결합 하는 쿼리를 작성 하거나 여러 작업 영역에서 테이블의 데이터를 결합할 수 있습니다.

다음 이미지에는 쿼리 예제에에서 사용 되는 다른 테이블에 작성 하는 데이터 원본의 예로 보여 줍니다.

![테이블](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
Application Insights를 제외 하 고 Azure Monitor 로그에서 수집한 모든 데이터에 저장 되는 [Log Analytics 작업 영역](../platform/manage-access.md)합니다. 특정 요구 사항에 따라 하나 이상의 작업 영역을 만들 수 있습니다. [데이터 원본](../platform/data-sources.md) Azure 리소스에서 활동 로그 및 진단 로그, virtual machines 및 insights 및 모니터링 솔루션의 데이터에는 에이전트에 데이터를 기록 해당 온 보 딩의 일부로 구성 하는 하나 이상의 작업 영역입니다. 와 같은 다른 서비스 [Azure Security Center](/azure/security-center/) 하 고 [Azure Sentinel](/azure/sentinel/) 또한 Log Analytics 작업 영역을 사용 하 여 모니터링 데이터를 다른 사용자와 함께 로그 쿼리를 사용 하 여 분석할 수 있도록 해당 데이터를 저장 합니다. 원본입니다.

다른 종류의 데이터 작업 영역에서 다른 테이블에 저장 되 고 각 테이블에 고유한 속성 집합이 있습니다. 생성 되는 다양 한 데이터 소스, 솔루션 및 서비스에 대 한 새 테이블을 추가할 때 테이블의 표준 집합을 작업 영역에 추가 됩니다 온 보 딩 합니다. 사용 하 여 사용자 지정 테이블을 만들 수도 있습니다는 [데이터 수집기 API](../platform/data-collector-api.md)합니다.

작업 영역에서 해당 스키마에 테이블을 찾아볼 수 있습니다 합니다 **스키마** Log Analytics 작업 영역에 대 한 탭 합니다.

![작업 영역 스키마](media/scope/workspace-schema.png)

전날 별로로 수집 된 작업 영역 및 레코드 수의 테이블 목록에 다음 쿼리를 사용 합니다. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
만들 테이블의 세부 정보에 대 한 각 데이터 원본에 대 한 설명서를 참조 하세요. 에 대 한 문서를 포함 하는 예제 [에이전트 데이터 원본](../platform/agent-data-sources.md)를 [진단 로그](../platform/diagnostic-logs-schema.md), 및 [모니터링 솔루션](../insights/solutions-inventory.md)합니다.

### <a name="workspace-permissions"></a>작업 영역 사용 권한
참조 [작업 영역 사용 권한과 범위](../platform/manage-access.md#workspace-permissions-and-scope) 대 한 자세한 내용은 작업 영역에서 데이터에 대 한 액세스를 제공 합니다. 작업 영역 자체에 대 한 액세스를 부여 하는 것 외에도 사용 하 여 개별 테이블에 대 한 액세스를 제한할 수 있습니다 [테이블 수준 RBAC](../platform/manage-access.md#table-level-rbac)합니다.

## <a name="application-insights-application"></a>Application Insights 응용 프로그램
Application Insights에서 응용 프로그램을 만들 때 Azure Monitor 로그에서 해당 응용 프로그램을 자동으로 만들어집니다. 구성 없이 데이터를 수집 하는 데 필요 하 고 응용 프로그램에 자동으로 작성 됩니다 페이지 보기, 요청 및 예외와 같은 데이터를 모니터링 합니다.

Log Analytics 작업 영역을 달리 Application Insights 응용 프로그램에는 테이블의 고정된 된 집합을 있습니다. 다른 데이터 원본에 없는 추가 테이블을 만들 수 있도록 응용 프로그램을 쓸를 구성할 수 없습니다. 

| 테이블 | 설명 | 
|:---|:---|
| availabilityResults | 가용성 테스트에서 데이터를 요약 합니다. |
| browserTimings      | 들어오는 데이터를 처리 하는 데 걸리는 시간 등의 클라이언트 성능에 대 한 데이터입니다. |
| customEvents        | 응용 프로그램에서 만든 사용자 지정 이벤트입니다. |
| customMetrics       | 응용 프로그램에서 만든 사용자 지정 메트릭 |
| 종속성        | 외부 구성 요소 응용 프로그램에서 호출 합니다. |
| exceptions          | 응용 프로그램 런타임에서 throw 된 예외입니다. |
| pageViews           | 브라우저 정보를 사용 하 여 각 웹 사이트에 대 한 데이터 보기 |
| performanceCounters | 응용 프로그램을 지 원하는 계산 리소스에서 성능 측정 합니다. |
| requests            | 각 응용 프로그램 요청의 세부 정보입니다.  |
| traces              | 분산된 추적의 결과입니다. |

각 테이블에 대 한 스키마를 볼 수 있습니다 합니다 **스키마** 응용 프로그램에 대 한 Log Analytics에서 탭 합니다.

![응용 프로그램 스키마](media/scope/application-schema.png)

## <a name="standard-properties"></a>표준 속성
Azure Monitor 로그의 각 테이블에는 고유한 스키마가 모든 테이블에서 공유 하는 표준 속성 가지 있습니다. 참조 [로그를 모니터링 하는 Azure의 표준 속성](../platform/log-standard-properties.md) 각각의 세부 정보에 대 한 합니다.

| Log Analytics 작업 영역 | Application Insights 응용 프로그램 | 설명 |
|:---|:---|:---|
| TimeGenerated | timestamp  | 레코드 만든 날짜와 시간입니다. |
| 형식          | itemType   | 레코드에서 가져온 테이블의 이름입니다. |
| _ResourceId   |            | 리소스 레코드에 대 한 고유 식별자와 연결 합니다. |
| _IsBillable   |            | 수집 된 데이터는 청구 가능 여부를 지정 합니다. |
| _BilledSize   |            | 청구 되는 데이터의 바이트의 크기를 지정 합니다. |

## <a name="next-steps"></a>다음 단계
- 에 대해 자세히 알아보려면 [만들기 및 편집 하도록 Log Analytics 로그 검색](../log-query/portals.md)합니다.
- 새로운 쿼리 언어를 사용한 [쿼리 작성 자습서](../log-query/get-started-queries.md)를 확인해 보세요.
