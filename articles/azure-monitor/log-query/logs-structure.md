---
title: Azure Monitor 로그 구조 | Microsoft Docs
description: Azure Monitor에서 로그 데이터를 검색하려면 로그 쿼리가 필요합니다.  이 문서는 Azure Monitor에서 새 로그 쿼리가 어떻게 사용되는지를 설명하고 새로 만들기 전에 이해해야 하는 개념을 제공합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2020
ms.openlocfilehash: 58724656dd407f09687b57d0ab034f3a1f808b76
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196280"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure Monitor 로그의 구조
[로그 쿼리](log-query-overview.md) 를 사용 하 여 데이터에 대 한 정보를 신속 하 게 파악 하는 기능은 Azure Monitor의 강력한 기능입니다. 효율적이 고 유용한 쿼리를 만들려면 원하는 데이터의 위치와 구성 방법 등의 몇 가지 기본 개념을 이해 해야 합니다. 이 문서에서는 시작 하는 데 필요한 기본 개념을 제공 합니다.

## <a name="overview"></a>개요
Azure Monitor 로그의 데이터는 Log Analytics 작업 영역 또는 Application Insights 응용 프로그램에 저장 됩니다. 둘 다 [데이터 탐색기 Azure](/azure/data-explorer/) 에서 제공 되므로 강력한 데이터 엔진과 쿼리 언어를 활용 하는 것을 의미 합니다.

> [!IMPORTANT]
> [작업 영역 기반 Application Insights 리소스](../app/create-workspace-resource.md)를 사용 하는 경우 원격 분석은 다른 모든 로그 데이터가 포함 된 Log Analytics 작업 영역에 저장 됩니다. 테이블의 이름이 바뀌고 재구성 Application Insights 응용 프로그램의 테이블과 동일한 정보가 있습니다.

작업 영역과 응용 프로그램의 데이터는 각각 서로 다른 종류의 데이터를 저장 하 고 고유한 속성 집합을 포함 하는 테이블로 구성 됩니다. 대부분의 [데이터 소스](../platform/data-sources.md) 는 Log Analytics 작업 영역에서 자체 테이블에 기록 하지만 Application Insights는 Application Insights 응용 프로그램에서 미리 정의 된 테이블 집합에 기록 합니다. 로그 쿼리를 사용 하면 여러 테이블의 데이터를 쉽게 결합할 수 있으며 상호 리소스 쿼리를 사용 하 여 여러 작업 영역에 있는 테이블의 데이터를 결합 하거나 작업 영역 및 응용 프로그램 데이터를 결합 하는 쿼리를 작성할 수도 있습니다.

다음 이미지는 샘플 쿼리에서 사용 되는 여러 테이블에 쓰는 데이터 원본의 예를 보여 줍니다.

![테이블](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
Application Insights를 제외 하 고 Azure Monitor 로그에 의해 수집 된 모든 데이터는 [Log Analytics 작업 영역](../platform/manage-access.md)에 저장 됩니다. 특정 요구 사항에 따라 하나 이상의 작업 영역을 만들 수 있습니다. 활동 로그 및 Azure 리소스의 리소스 로그, virtual machines의 에이전트, insights 및 모니터링 솔루션의 데이터 등의 [데이터 원본은](../platform/data-sources.md) 온 보 딩의 일부로 구성 하는 하나 이상의 작업 영역에 데이터를 기록 합니다. [Azure Security Center](/azure/security-center/) 및 [Azure 센티널](/azure/sentinel/) 과 같은 다른 서비스 에서도 Log Analytics 작업 영역을 사용 하 여 데이터를 저장 하므로 다른 원본의 모니터링 데이터와 함께 로그 쿼리를 사용 하 여 분석할 수 있습니다.

다른 종류의 데이터는 작업 영역의 다른 테이블에 저장 되 고 각 테이블에는 고유한 속성 집합이 있습니다. 표준 테이블 집합이 만들어질 때 작업 영역에 추가 되 고, 등록 되는 다른 데이터 원본, 솔루션 및 서비스에 대 한 새 테이블이 추가 됩니다. [데이터 수집기 API](../platform/data-collector-api.md)를 사용 하 여 사용자 지정 테이블을 만들 수도 있습니다.

작업 영역에 대 한 Log Analytics의 **스키마** 탭에서 작업 영역 및 해당 스키마의 테이블을 찾아볼 수 있습니다.

![작업 영역 스키마](media/scope/workspace-schema.png)

다음 쿼리를 사용 하 여 작업 영역의 테이블과 이전 날짜에 수집 된 레코드 수를 나열 합니다. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
만든 테이블에 대 한 자세한 내용은 각 데이터 원본에 대 한 설명서를 참조 하세요. 예를 들면 [에이전트 데이터 원본](../platform/agent-data-sources.md), [리소스 로그](../platform/diagnostic-logs-schema.md)및 [모니터링 솔루션](../insights/solutions-inventory.md)에 대 한 아티클이 있습니다.

### <a name="workspace-permissions"></a>작업 영역 권한
작업 영역에서 데이터에 대 한 액세스를 제공 하기 위한 액세스 제어 전략 및 권장 사항을 이해 하려면 [Azure Monitor 로그 배포 디자인](../platform/design-logs-deployment.md) 을 참조 하세요. 작업 영역에 대 한 액세스 권한을 부여 하는 것 외에도 [테이블 수준 RBAC](../platform/manage-access.md#table-level-rbac)를 사용 하 여 개별 테이블에 대 한 액세스를 제한할 수 있습니다.

## <a name="application-insights-application"></a>응용 프로그램 Application Insights

> [!IMPORTANT]
> [작업 영역 기반 Application Insights 리소스](../app/create-workspace-resource.md) 원격 분석을 사용 하는 경우 다른 모든 로그 데이터가 포함 된 Log Analytics 작업 영역에 저장 됩니다. 테이블의 이름이 바뀌고 재구성 기본 Application Insights 리소스의 테이블과 동일한 정보가 있습니다.

Application Insights에서 응용 프로그램을 만들 때 해당 응용 프로그램이 Azure Monitor 로그에 자동으로 만들어집니다. 데이터를 수집 하는 데 필요한 구성은 없으며, 응용 프로그램은 페이지 보기, 요청 및 예외와 같은 모니터링 데이터를 자동으로 작성 합니다.

Log Analytics 작업 영역과 달리 Application Insights 응용 프로그램에는 고정 테이블 집합이 있습니다. 응용 프로그램에 쓰도록 다른 데이터 원본을 구성할 수 없으므로 추가 테이블을 만들 수 없습니다. 

| 테이블 | 설명 | 
|:---|:---|
| availabilityResults | 가용성 테스트의 요약 데이터입니다. |
| browserTimings      | 들어오는 데이터를 처리 하는 데 걸린 시간과 같은 클라이언트 성능에 대 한 데이터입니다. |
| customEvents        | 응용 프로그램에서 만든 사용자 지정 이벤트입니다. |
| customMetrics       | 응용 프로그램에서 만든 사용자 지정 메트릭입니다. |
| 종속성        | REST API, 데이터베이스 또는 파일 시스템에 대 한 호출과 같이, 다른 구성 요소 (외부 구성 요소 포함)를 통해 기록 된 다른 구성 요소 (외부 구성 요소 포함)에 대 한 호출을 호출 합니다. |
| 예외          | 응용 프로그램 런타임에서 throw 되는 예외는 서버 쪽 및 클라이언트 쪽 (브라우저) 예외를 모두 캡처합니다.|
| pageViews           | 브라우저 정보를 사용 하 여 각 웹 사이트 보기에 대 한 데이터입니다. |
| performanceCounters | 응용 프로그램을 지 원하는 계산 리소스 (예: Windows 성능 카운터)의 성능 측정 |
| requests            | 응용 프로그램에서 받은 요청입니다. 예를 들어 웹 앱이 수신 하는 각 HTTP 요청에 대해 별도의 요청 레코드가 기록 됩니다.  |
| traces              | 이를 통해 기록 추적 ()을 통해 기록 된 응용 프로그램 코드/로깅 프레임 워크를 통해 내보낸 자세한 로그 (추적) |

응용 프로그램에 대 한 Log Analytics의 **스키마** 탭에서 각 테이블에 대 한 스키마를 볼 수 있습니다.

![응용 프로그램 스키마](media/scope/application-schema.png)

## <a name="standard-properties"></a>표준 속성
Azure Monitor 로그의 각 테이블에는 자체 스키마가 있지만 모든 테이블에서 공유 하는 표준 속성은 있습니다. 각 항목에 대 한 자세한 내용은 [Azure Monitor 로그의 표준 속성](../platform/log-standard-properties.md) 을 참조 하세요.

| Log Analytics 작업 영역 | 응용 프로그램 Application Insights | 설명 |
|:---|:---|:---|
| TimeGenerated | timestamp  | 레코드를 만든 날짜와 시간입니다. |
| 유형          | itemType   | 레코드가 검색 된 테이블의 이름입니다. |
| _ResourceId   |            | 레코드가 연결 된 리소스의 고유 식별자입니다. |
| _IsBillable   |            | 수집 데이터에 대 한 청구 가능 여부를 지정 합니다. |
| _BilledSize   |            | 청구 되는 데이터의 크기 (바이트)를 지정 합니다. |

## <a name="next-steps"></a>다음 단계
- Log Analytics를 사용 하 여 [로그 검색을 만들고 편집 하](../log-query/portals.md)는 방법에 대해 알아봅니다.
- 새로운 쿼리 언어를 사용한 [쿼리 작성 자습서](../log-query/get-started-queries.md)를 확인해 보세요.