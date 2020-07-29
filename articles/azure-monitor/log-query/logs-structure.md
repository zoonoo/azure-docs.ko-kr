---
title: Azure Monitor 로그의 구조| Microsoft Docs
description: Azure Monitor에서 로그 데이터를 검색하려면 로그 쿼리가 필요합니다.  이 문서는 Azure Monitor에서 새 로그 쿼리가 어떻게 사용되는지를 설명하고 새로 만들기 전에 이해해야 하는 개념을 제공합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2020
ms.openlocfilehash: 4e36a21e9dbab6f9bf814cdeb86f175ded38ea8e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327296"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure Monitor 로그의 구조
[로그 쿼리](log-query-overview.md)를 사용하여 데이터에 대한 인사이트를 신속하게 파악할 수 있는 기능은 Azure Monitor의 강력한 기능입니다. 효율적이고 유용한 쿼리를 만들려면 원하는 데이터의 위치 및 구조와 같은 몇 가지 기본 개념을 이해해야 합니다. 이 문서에는 시작하는 데 필요한 기본 개념이 제공됩니다.

## <a name="overview"></a>개요
Azure Monitor 로그의 데이터는 Log Analytics 작업 영역 또는 Application Insights 애플리케이션에 저장됩니다. 두 가지 모두 [Azure Data Explorer](/azure/data-explorer/)를 기반으로 하기 때문에 강력한 데이터 엔진과 쿼리 언어가 활용됩니다.

> [!IMPORTANT]
> [작업 영역 기반 Application Insights 리소스](../app/create-workspace-resource.md)를 사용하는 경우 원격 분석은 다른 모든 로그 데이터와 함께 Log Analytics 작업 영역에 저장됩니다. 테이블 이름과 구조가 변했지만 Application Insights 애플리케이션의 테이블과 동일한 정보를 포함합니다.

작업 영역과 애플리케이션의 데이터가 테이블로 구성되었으며, 여기에는 각각 다른 종류의 데이터가 저장되고 고유한 속성 세트가 포함됩니다. 대부분의 [데이터 소스](../platform/data-sources.md)는 Log Analytics 작업 영역의 자체 테이블에 기록하지만, Application Insights는 Application Insights 애플리케이션의 미리 정의된 일련의 테이블에 기록합니다. 로그 쿼리는 매우 유연하기 때문에 여러 테이블의 데이터를 쉽게 결합할 수 있으며, 교차 리소스 쿼리를 사용하여 여러 작업 영역에 있는 테이블의 데이터를 결합하거나 작업 영역과 애플리케이션 데이터를 결합하는 쿼리를 작성할 수도 있습니다.

다음 이미지는 샘플 쿼리에 사용되는 여러 테이블에 쓰는 데이터 소스의 예를 보여줍니다.

![테이블](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
Application Insights를 제외한 Azure Monitor 로그를 통해 수집된 모든 데이터는 [Log Analytics 작업 영역](../platform/manage-access.md)에 저장됩니다. 특정 요구 사항에 따라 하나 이상의 작업 영역을 만들 수 있습니다. Azure 리소스의 활동 로그 및 리소스 로그, 가상 머신의 에이전트, 인사이트 및 모니터링 솔루션의 데이터와 같은 [데이터 소스](../platform/data-sources.md)는 온보딩의 일환으로 구성한 하나 이상의 작업 영역에 데이터를 씁니다. [Azure Security Center](../../security-center/index.yml) 및 [Azure Sentinel](../../sentinel/index.yml)과 같은 다른 서비스도 Log Analytics 작업 영역을 사용하여 데이터를 저장하기 때문에 다른 소스의 모니터링 데이터와 함께 로그 쿼리를 사용하여 데이터를 분석할 수 있습니다.

서로 다른 종류의 데이터가 작업 영역의 서로 다른 테이블에 저장되며 각 테이블에는 고유한 속성 세트가 있습니다. 표준 테이블 세트는 만들어질 때 작업 영역에 추가되고, 다양한 데이터 소스, 솔루션 및 서비스가 온보딩될 때 새로운 테이블이 추가됩니다. [데이터 수집기 API](../platform/data-collector-api.md)를 사용하여 사용자 지정 테이블을 만들 수도 있습니다.

작업 영역에서 테이블을 찾아보고, 작업 영역에 대한 Log Analytics의 **스키마** 탭에서 해당 스키마를 찾아볼 수 있습니다.

![작업 영역 스키마](media/scope/workspace-schema.png)

다음 쿼리를 사용하여 작업 영역의 테이블과 전날에 수집된 레코드 수를 나열할 수 있습니다. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
생성되는 테이블에 대한 자세한 내용은 각 데이터 소스의 설명서를 참조하세요. [에이전트 데이터 소스](../platform/agent-data-sources.md), [리소스 로그](../platform/resource-logs-schema.md) 및 [모니터링 솔루션](../monitor-reference.md)에 대한 문서가 예에 포함되어 있습니다.

### <a name="workspace-permissions"></a>작업 영역 권한
작업 영역의 데이터에 대한 액세스를 제공하는 액세스 제어 전략 및 권장 사항을 이해하려면 [Azure Monitor 로그 배포 디자인](../platform/design-logs-deployment.md)을 참조하세요. 작업 영역 자체에 대한 액세스 권한을 부여하는 것 외에 [테이블 수준 RBAC](../platform/manage-access.md#table-level-rbac)를 사용하여 개별 테이블에 대한 액세스를 제한할 수 있습니다.

## <a name="application-insights-application"></a>Application Insights 애플리케이션

> [!IMPORTANT]
> [작업 영역 기반 Application Insights 리소스](../app/create-workspace-resource.md)를 사용하는 경우 원격 분석은 다른 모든 로그 데이터와 함께 Log Analytics 작업 영역에 저장됩니다. 테이블 이름과 구조가 변했지만 클래식 Application Insights 리소스의 테이블과 동일한 정보를 포함합니다.

Application Insights에서 애플리케이션을 만들면 해당 애플리케이션이 Azure Monitor 로그에 자동으로 만들어집니다. 데이터를 수집하는 데 필요한 구성은 없으며, 페이지 보기, 요청 및 예외와 같은 모니터링 데이터가 애플리케이션에서 자동으로 작성됩니다.

Log Analytics 작업 영역과 달리 Application Insights 애플리케이션에는 고정된 테이블 세트가 있습니다. 애플리케이션에 쓰도록 다른 데이터 소스를 구성할 수 없으므로 추가 테이블을 만들 수 없습니다. 

| 테이블 | Description | 
|:---|:---|
| availabilityResults | 가용성 테스트에 대한 요약 데이터입니다. |
| browserTimings      | 들어오는 데이터를 처리하는 데 걸리는 시간 등, 클라이언트 성능에 대한 데이터입니다. |
| customEvents        | 애플리케이션에서 생성된 사용자 지정 이벤트입니다. |
| customMetrics       | 애플리케이션에서 생성된 사용자 지정 메트릭입니다. |
| 종속성        | 애플리케이션에서 TrackDependency()를 통해 기록된 다른 구성 요소(외부 구성 요소 포함)에 대한 호출입니다. – 예: REST API, 데이터베이스 또는 파일 시스템에 대한 호출. |
| 예외          | 애플리케이션 런타임에서 throw된 예외는 서버 측 및 클라이언트 측(브라우저) 예외를 모두 캡처합니다.|
| pageViews           | 브라우저 정보가 포함된 각 웹 사이트 보기에 대한 데이터입니다. |
| performanceCounters | 애플리케이션을 지원하는 컴퓨팅 리소스의 성능 측정입니다(예: Windows 성능 카운터). |
| requests            | 애플리케이션이 받은 요청입니다. 예를 들어, 웹앱이 수신하는 각 HTTP 요청에 대해 별도의 요청 레코드가 기록됩니다.  |
| traces              | TrackTrace()를 통해 기록된 애플리케이션 코드/로깅 프레임워크를 통해 생성된 자세한 로그(추적)입니다. |

애플리케이션에 대한 Log Analytics의 **스키마** 탭에서 각 테이블의 스키마를 볼 수 있습니다.

![애플리케이션 스키마](media/scope/application-schema.png)

## <a name="standard-properties"></a>표준 속성
Azure Monitor 로그의 각 테이블에는 자체 스키마가 있지만 모든 테이블이 공유하는 표준 속성이 있습니다. 각 항목에 대한 자세한 내용은 [Azure Monitor 로그의 표준 속성](../platform/log-standard-properties.md)을 참조하세요.

| Log Analytics 작업 영역 | Application Insights 애플리케이션 | Description |
|:---|:---|:---|
| TimeGenerated | timestamp  | 레코드가 생성된 날짜 및 시간입니다. |
| Type          | itemType   | 레코드가 검색된 테이블의 이름입니다. |
| _ResourceId   |            | 리소스와 연결된 리소스의 고유 식별자입니다. |
| _IsBillable   |            | 수집된 데이터의 청구 가능 여부를 지정합니다. |
| _BilledSize   |            | 청구되는 데이터의 크기(바이트)를 지정합니다. |

## <a name="next-steps"></a>다음 단계
- [Log Analytics를 사용하여 로그 검색을 만들고 편집](./log-query-overview.md)하는 방법을 알아봅니다.
- 새로운 쿼리 언어를 사용한 [쿼리 작성 자습서](./get-started-queries.md)를 확인해 보세요.

