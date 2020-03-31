---
title: Azure 모니터 로그 구조 | 마이크로 소프트 문서
description: Azure Monitor에서 로그 데이터를 검색하려면 로그 쿼리가 필요합니다.  이 문서는 Azure Monitor에서 새 로그 쿼리가 어떻게 사용되는지를 설명하고 새로 만들기 전에 이해해야 하는 개념을 제공합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662079"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure 모니터 로그 구조
[로그 쿼리를](log-query-overview.md) 사용하여 데이터에 대한 통찰력을 빠르게 얻을 수 있는 기능은 Azure Monitor의 강력한 기능입니다. 효율적이고 유용한 쿼리를 만들려면 원하는 데이터의 위치와 구조화 방식과 같은 몇 가지 기본 개념을 이해해야 합니다. 이 문서에서는 시작하는 데 필요한 기본 개념을 제공합니다.

## <a name="overview"></a>개요
Azure 모니터 로그의 데이터는 로그 분석 작업 영역 또는 응용 프로그램 인사이트 응용 프로그램에 저장됩니다. 둘 다 [Azure Data Explorer에](/azure/data-explorer/) 의해 구동되므로 강력한 데이터 엔진 및 쿼리 언어를 활용합니다.

작업 영역과 응용 프로그램의 데이터는 서로 다른 종류의 데이터를 저장하고 고유한 속성 집합을 포함하는 테이블로 구성됩니다. 대부분의 [데이터 원본은](../platform/data-sources.md) Log Analytics 작업 영역에서 자신의 테이블에 쓰고, 응용 프로그램 인사이트는 응용 프로그램 인사이트 응용 프로그램에서 미리 정의된 테이블 집합에 씁니다. 로그 쿼리는 매우 유연하여 여러 테이블의 데이터를 쉽게 결합하고 리소스 간 쿼리를 사용하여 여러 작업 영역의 테이블의 데이터를 결합하거나 작업 영역 및 응용 프로그램 데이터를 결합하는 쿼리를 작성할 수 있습니다.

다음 이미지는 샘플 쿼리에 사용되는 다른 테이블에 쓰는 데이터 원본의 예를 보여 주며 있습니다.

![테이블](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
응용 프로그램 인사이트를 제외한 Azure 모니터 로그에 의해 수집된 모든 데이터는 [로그 분석 작업 영역에](../platform/manage-access.md)저장됩니다. 특정 요구 사항에 따라 하나 이상의 작업 영역을 만들 수 있습니다. Azure 리소스의 활동 로그 및 리소스 로그, 가상 컴퓨터의 에이전트 및 인사이트 및 모니터링 솔루션의 데이터와 같은 [데이터 원본은](../platform/data-sources.md) 온보딩의 일부로 구성하는 하나 이상의 작업 영역에 데이터를 씁니다. Azure 보안 [센터](/azure/security-center/) 및 [Azure Sentinel과](/azure/sentinel/) 같은 다른 서비스도 Log Analytics 작업 영역을 사용하여 데이터를 저장하므로 다른 소스의 모니터링 데이터와 함께 로그 쿼리를 사용하여 분석할 수 있습니다.

서로 다른 종류의 데이터가 작업 공간의 다른 테이블에 저장되며 각 테이블에는 고유한 속성 집합이 있습니다. 표준 테이블 집합이 만들 때 작업 영역에 추가되고 온보딩되는 다양한 데이터 원본, 솔루션 및 서비스에 대해 새 테이블이 추가됩니다. [데이터 수집기 API를](../platform/data-collector-api.md)사용하여 사용자 지정 테이블을 만들 수도 있습니다.

작업 영역에 대한 로그 애널리틱스의 **스키마** 탭에서 작업 영역의 테이블과 해당 스키마를 찾아볼 수 있습니다.

![작업 영역 스키마](media/scope/workspace-schema.png)

다음 쿼리를 사용하여 작업 영역의 테이블과 전날 동안 각각에 수집된 레코드 수를 나열합니다. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
각 데이터 원본에 대한 설명서를 참조하여 만든 테이블에 대한 자세한 내용을 확인하십시오. 예를 들어 [에이전트 데이터 원본,](../platform/agent-data-sources.md) [리소스 로그](../platform/diagnostic-logs-schema.md)및 모니터링 [솔루션에](../insights/solutions-inventory.md)대한 문서가 있습니다.

### <a name="workspace-permissions"></a>작업 영역 권한
작업 영역에서 데이터에 대한 액세스를 제공하는 액세스 제어 전략 및 권장 사항을 이해하려면 [Azure Monitor Logs 배포 설계를](../platform/design-logs-deployment.md) 참조하십시오. 작업 영역 자체에 대한 액세스 권한을 부여하는 것 외에도 테이블 [수준 RBAC를](../platform/manage-access.md#table-level-rbac)사용하여 개별 테이블에 대한 액세스를 제한할 수 있습니다.

## <a name="application-insights-application"></a>애플리케이션 인사이트 애플리케이션
응용 프로그램 Insights에서 응용 프로그램을 만들 면 해당 응용 프로그램이 Azure 모니터 로그에 자동으로 만들어집니다. 데이터를 수집하는 데 구성이 필요하지 않으며 응용 프로그램은 페이지 뷰, 요청 및 예외와 같은 모니터링 데이터를 자동으로 작성합니다.

로그 분석 작업 영역과 달리 응용 프로그램 인사이트 응용 프로그램에는 고정된 테이블 집합이 있습니다. 추가 테이블을 만들 수 없도록 응용 프로그램에 쓸 다른 데이터 원본을 구성할 수 없습니다. 

| 테이블 | 설명 | 
|:---|:---|
| 가용성 결과 | 가용성 테스트의 요약 데이터입니다. |
| 브라우저타이밍      | 들어오는 데이터를 처리하는 데 걸린 시간과 같은 클라이언트 성능에 대한 데이터입니다. |
| 사용자 지정 이벤트        | 응용 프로그램에서 만든 사용자 지정 이벤트입니다. |
| 사용자 지정 메트릭       | 응용 프로그램에서 만든 사용자 지정 메트릭입니다. |
| 종속성        | 응용 프로그램에서 외부 구성 요소로 호출합니다. |
| 예외          | 응용 프로그램 런타임에서 throw된 예외입니다. |
| Pageviews           | 브라우저 정보가 있는 각 웹 사이트 보기에 대한 데이터입니다. |
| performanceCounters | 응용 프로그램을 지원하는 계산 리소스의 성능 측정입니다. |
| requests            | 각 응용 프로그램 요청에 대한 세부 정보입니다.  |
| traces              | 분산 추적의 결과입니다. |

응용 프로그램에 대한 로그 분석의 **스키마** 탭에서 각 테이블에 대한 스키마를 볼 수 있습니다.

![응용 프로그램 스키마](media/scope/application-schema.png)

## <a name="standard-properties"></a>표준 속성
Azure Monitor Logs의 각 테이블에는 자체 스키마가 있지만 모든 테이블에서 공유하는 표준 속성이 있습니다. 각각에 대한 자세한 내용은 [Azure 모니터 로그에서 표준 속성을](../platform/log-standard-properties.md) 참조하십시오.

| Log Analytics 작업 영역 | 애플리케이션 인사이트 애플리케이션 | 설명 |
|:---|:---|:---|
| TimeGenerated | timestamp  | 레코드가 만들어진 날짜 및 시간입니다. |
| Type          | itemType   | 레코드가 검색된 테이블의 이름입니다. |
| _ResourceId   |            | 레코드가 연결된 리소스에 대한 고유 식별자입니다. |
| _IsBillable   |            | 수집된 데이터가 청구 가능한지 여부를 지정합니다. |
| _BilledSize   |            | 청구될 데이터의 바이트 로 크기를 지정합니다. |

## <a name="next-steps"></a>다음 단계
- 로그 분석을 사용하여 [로그 검색을 만들고 편집하는](../log-query/portals.md)방법에 대해 알아봅니다.
- 새로운 쿼리 언어를 사용한 [쿼리 작성 자습서](../log-query/get-started-queries.md)를 확인해 보세요.
