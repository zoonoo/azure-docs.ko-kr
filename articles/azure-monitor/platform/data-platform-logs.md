---
title: Azure Monitor의 로그 | Microsoft Docs
description: 모니터링 데이터의 고급 분석에 사용되는 Azure Monitor의 로그를 설명합니다.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 413616034dfe7d1f13612ba12ba86014af62c704
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325630"
---
# <a name="logs-in-azure-monitor"></a>Azure Monitor의 로그

> [!NOTE]
> Azure Monitor가 수집하는 모든 데이터는 두 가지 기본 유형인 메트릭 및 로그 중 한 쪽에 적합합니다. 이 문서에서는 로그를 설명합니다. 메트릭에 대한 자세한 설명은 [Azure Monitor의 메트릭](data-platform-metrics.md)을, 둘의 비교는 [Azure Monitor에서 수집한 모니터링 데이터](data-platform.md)를 참조하세요.

Azure Monitor의 로그는 다양한 원본의 데이터에서 복잡한 분석을 수행하는 데 특히 유용합니다. 이 문서에서는 Azure 모니터에서 메트릭이 구성되는 방법, 데이터로 수행할 수 있는 작업을 설명하고, 로그에 데이터를 저장하는 다양한 데이터 원본을 식별합니다.

> [!NOTE]
> Azure에서 Azure Monitor 로그와 로그 데이터의 원본을 구분하는 것이 중요합니다. 예를 들어 Azure의 구독 수준 이벤트는 Azure Monitor 메뉴에서 볼 수 있는 [활동 로그](platform-logs-overview.md)에 기록됩니다. 대부분의 리소스는 다른 위치로 전달할 수 있는 [리소스 로그](platform-logs-overview.md)에 작업 정보를 기록합니다. Azure Monitor 로그는 다른 모니터링 데이터와 함께 활동 로그 및 리소스 로그를 수집하여 전체 리소스 집합에 대한 심층 분석을 제공하는 로그 데이터 플랫폼입니다.

## <a name="what-are-azure-monitor-logs"></a>Azure Monitor 로그란 무엇입니까?

Azure Monitor의 로그에는 각 형식에 대해 다양한 속성 세트가 포함된 레코드로 구성된 다양한 데이터 형식이 포함됩니다. 로그에는 Azure Monitor 메트릭과 같은 숫자 값이 포함될 수 있지만 일반적으로 자세한 설명이 포함된 텍스트 데이터가 포함됩니다. 해당 구조가 다양하다는 점에서 메트릭 데이터와는 구별되며 정기적으로 수집되지 않는 경우가 많습니다. 이벤트 및 추적과 같은 원격 분석은 분석을 위해 모두 결합될 수 있도록 성능 데이터 외에도 Azure Monitor 로그로 저장됩니다.

로그 항목의 일반적인 유형은 산발적으로 수집되는 이벤트입니다. 이벤트는 애플리케이션 또는 서비스에서 만들어지며 대개 자체적으로 완전한 컨텍스트를 제공하기에 충분한 정보를 포함합니다. 예를 들어, 이벤트는 특정 리소스가 생성 또는 수정되었거나, 트래픽 증가에 대한 응답으로 새 호스트가 시작되었거나, 애플리케이션에서 오류가 검색되었음을 나타낼 수 있습니다.

 데이터의 형식은 다양할 수 있으므로 애플리케이션은 필요한 구조를 사용하여 사용자 지정 로그를 만들 수 있습니다. 메트릭 데이터는 추세 및 다른 데이터 분석을 위해 다른 모니터링 데이터와 결합하도록 로그에 저장할 수도 있습니다.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure Monitor 로그로 무엇을 할 수 있나요?
다음 표에는 Azure Monitor에서 로그를 사용할 수 있는 여러 방법이 나와 있습니다.


|  | 설명 |
|:---|:---|
| **분석** | Azure Portal에서 [Log Analytics](../log-query/get-started-portal.md)를 사용하여 [로그 쿼리](../log-query/log-query-overview.md)를 작성하고 강력한 데이터 탐색기 분석 엔진을 사용하여 로그 데이터를 대화형으로 분석합니다.<br>Azure Portal의 [Application Insights 분석 콘솔](../log-query/log-query-overview.md)을 사용하여 로그 쿼리를 작성하고 Application Insights의 로그 데이터를 대화형으로 분석합니다. |
| **시각화** | 테이블 또는 차트로 렌더링된 쿼리 결과를 [Azure 대시보드](../../azure-portal/azure-portal-dashboards.md)에 고정합니다.<br>[통합 문서](./workbooks-overview.md)를 만들어 대화형 보고서의 여러 데이터 집합을 결합합니다. <br>쿼리의 결과를 [Power BI](powerbi.md)로 내보내 서로 다른 시각화를 사용하고 Azure 외부의 사용자와 공유합니다.<br>쿼리의 결과를 [Grafana](grafana-plugin.md)로 내보내 대시보드를 활용하고 다른 데이터 원본과 결합합니다.|
| **경고** | 쿼리의 결과가 특정 결과와 일치할 때 알림을 보내거나 [자동화된 작업](action-groups.md)을 수행하는 [로그 경고 규칙](alerts-log.md)을 구성합니다.<br>메트릭으로 추출된 특정 로그 데이터 로그에서 [메트릭 경고 규칙](alerts-metric-logs.md)을 구성합니다. |
| **장치** | [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)를 사용하여 명령줄에서 로그 쿼리 결과에 액세스합니다.<br>[PowerShell cmdlet](/powershell/module/az.operationalinsights)을 사용하여 명령줄에서 로그 쿼리 결과에 액세스합니다.<br>[REST API](https://dev.loganalytics.io/)를 사용하여 사용자 지정 애플리케이션에서 로그 쿼리 결과에 액세스합니다. |
| **내보내기** | 워크플로를 빌드하여 로그 데이터를 검색하고 [Logic Apps](../../logic-apps/index.yml)를 사용하여 이를 외부 위치에 복사합니다. |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure Monitor 로그의 데이터는 어떻게 구성되나요?
Azure Monitor 로그에서 수집된 데이터는 [Log Analytics 작업 영역](./design-logs-deployment.md)에 저장됩니다. 각 작업 영역에는 각각 특정 원본의 데이터를 저장하는 여러 테이블이 있습니다. 모든 테이블이 [일부 공통 속성](log-standard-properties.md)을 공유하지만 각각에는 저장한 데이터에 따라 고유한 속성 집합이 있습니다. 새 작업 영역에는 표준 테이블 집합이 있고, 다른 모니터링 솔루션 및 작업 영역에 기록하는 다른 서비스에 따라 테이블이 추가됩니다.

Application Insights의 로그 데이터는 작업 영역과 동일한 Log Analytics 엔진을 사용하지만 각각의 모니터링되는 애플리케이션에 별도로 저장됩니다. 각 애플리케이션에는 애플리케이션 요청, 예외 및 페이지 보기와 같은 데이터를 저장하는 표준 테이블 집합이 있습니다.

로그 쿼리는 Log Analytics 작업 영역 또는 Application Insights 애플리케이션의 데이터를 사용합니다. [교차 리소스 쿼리](../log-query/cross-workspace-query.md)를 사용하여 다른 로그 데이터와 함께 애플리케이션 데이터를 분석하거나 여러 작업 영역 또는 애플리케이션을 포함하는 쿼리를 만들 수 있습니다.

![작업 영역](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>로그 쿼리
Azure Monitor 로그의 데이터는 [Kusto 쿼리 언어](../log-query/get-started-queries.md)로 작성된 [로그 쿼리](../log-query/log-query-overview.md)를 사용하여 검색되므로 수집된 데이터를 빠르게 검색, 통합 및 분석할 수 있습니다. [Log Analytics](../log-query/log-query-overview.md)를 사용하여 Azure Portal에서 로그 쿼리를 기록하고 테스트합니다. 결과를 대화형으로 사용하거나 대시보드에 고정하여 다른 시각화를 통해 볼 수 있습니다.

![Log Analytics](media/data-platform-logs/log-analytics.png)

[Application Insights의 Log Analytics](../log-query/log-query-overview.md)를 열어 Application Insights 데이터를 분석합니다.

![Application Insights 분석](media/data-platform-logs/app-insights-analytics.png)

[Log Analytics API](https://dev.loganalytics.io/documentation/overview) 및 [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview)를 사용하여 로그 데이터를 검색할 수도 있습니다.


## <a name="sources-of-azure-monitor-logs"></a>Azure Monitor 로그의 원본
Azure Monitor는 Azure 내와 온-프레미스 리소스의 다양한 원본에서 로그 데이터를 수집할 수 있습니다. 다음 표에서는 Azure Monitor 로그에 데이터를 쓰는 다양한 리소스에서 사용할 수 있는 다양한 데이터 원본이 있습니다. 각각에는 모든 필수 구성에 대한 세부 정보 링크가 있습니다.

### <a name="azure-tenant-and-subscription"></a>Azure 테넌트 및 구독

| 데이터 | Description |
|:---|:---|
| Azure Active Directory 감사 로그 | 각 디렉터리에 대한 진단 설정을 통해 구성됩니다. [Azure Monitor 로그에 Azure AD 로그 통합](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)을 참조하세요.  |
| 활동 로그 | 기본적으로 개별 저장되며 실시간에 가까운 경고에 사용할 수 있습니다. Log Analytics 작업 영역에 쓸 활동 로그 분석 솔루션을 설치합니다. [Log Analytics에서 Azure 활동 로그 수집 및 분석](./activity-log.md)을 참조하세요. |

### <a name="azure-resources"></a>Azure 리소스

| 데이터 | Description |
|:---|:---|
| 리소스 진단 | Log Analytics 작업 영역에 대한 메트릭을 포함하여 진단 데이터에 쓰도록 진단 설정을 구성합니다. [Azure 리소스 로그를 Log Analytics로 스트리밍](./resource-logs.md#send-to-log-analytics-workspace)을 참조하세요. |
| 모니터링 솔루션 | 모니터링 솔루션은 Log Analytics 작업 영역에 수집하는 데이터를 작성합니다. 솔루션 목록은 [Azure의 관리 솔루션에 대한 데이터 수집 상세 정보](../monitor-reference.md)를 참조하세요. 솔루션 설치 및 사용에 대한 자세한 내용은 [Azure Monitor의 모니터링 솔루션](../insights/solutions.md)을 참조하세요. |
| 메트릭 | Azure Monitor 리소스에 대한 플랫폼 메트릭을 Log Analytics 작업 영역으로 전송하여 로그 데이터를 더욱 오랜 기간 동안 보존하고 [Kusto 쿼리 언어](/azure/kusto/query/)를 사용하는 다른 데이터 종류와의 복잡한 분석을 수행합니다. [Azure 리소스 로그를 Log Analytics로 스트리밍](./resource-logs.md#send-to-azure-storage)을 참조하세요. |
| Azure 테이블 스토리지 | 일부 Azure 리소스가 모니터링 데이터를 쓰는 Azure 스토리지에서 데이터를 수집합니다. [Log Analytics에서 이벤트에 대해 IIS와 Azure Table Storage에 Azure Blob Storage 사용](diagnostics-extension-logs.md)을 참조하세요. |

### <a name="virtual-machines"></a>Virtual Machines

| 데이터 | Description |
|:---|:---|
|  에이전트 데이터 원본 | [Windows](agent-windows.md) 및 [Linux](../learn/quick-collect-linux-computer.md) 에이전트에서 수집한 데이터 원본에는 이벤트, 성능 데이터 및 사용자 지정 로그가 포함됩니다. 데이터 원본의 목록과 구성 세부 사항에 대한 자세한 내용은 [Azure Monitor의 에이전트 데이터 원본](data-sources.md)을 참조하세요. |
| 모니터링 솔루션 | 모니터링 솔루션은 에이전트에서 Log Analytics 작업 영역에 수집하는 데이터를 작성합니다. 솔루션 목록은 [Azure의 관리 솔루션에 대한 데이터 수집 상세 정보](../monitor-reference.md)를 참조하세요. 솔루션 설치 및 사용에 대한 자세한 내용은 [Azure Monitor의 모니터링 솔루션](../insights/solutions.md)을 참조하세요. |
| System Center Operations Manager | Operations Manager 관리 그룹을 Azure Monitor에 연결하여 온-프레미스 에이전트에서 로그로 이벤트 및 성능 데이터를 수집합니다. 이 구성에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](om-agents.md)을 참조하세요. |


### <a name="applications"></a>애플리케이션

| 데이터 | Description |
|:---|:---|
| 요청 및 예외 | 애플리케이션 요청 및 예외에 대한 세부 데이터는 _requests_, _pageViews_ 및 _exceptions_ 테이블에 있습니다. [외부 구성 요소](../app/asp-net-dependencies.md) 호출은 _dependencies_ 테이블에 있습니다. |
| 사용량 및 성능 | 애플리케이션 성능은 _requests_, _browserTimings_ 및 _performanceCounters_ 테이블에서 확인할 수 있습니다. [사용자 지정 메트릭](../app/api-custom-events-metrics.md#trackevent)에 대한 데이터는 _customMetrics_ 테이블에 있습니다.|
| 추적 데이터 | [분산 추적](../app/distributed-tracing.md)의 결과는 _traces_ 테이블에 저장됩니다. |
| 가용성 테스트 | [가용성 테스트](../app/monitor-web-app-availability.md)의 요약 데이터는 _availabilityResults_ 테이블에 저장됩니다. 이러한 테스트의 세부 데이터는 별도의 스토리지에 있으며 Azure Portal의 Application Insights에서 액세스할 수 있습니다. |

### <a name="insights"></a>자세한 정보

| 데이터 | Description |
|:---|:---|
| 컨테이너용 Azure Monitor | [컨테이너용 Azure Monitor](../insights/container-insights-overview.md)에서 인벤토리 및 성능 데이터가 수집됩니다. 테이블 목록은 [컨테이너 데이터-컬렉션 세부 정보](../insights/container-insights-log-search.md#container-records)를 참조하세요. |
| VM용 Azure Monitor | [VM용 Azure Monitor](../insights/vminsights-overview.md)에서 맵 및 성능 데이터가 수집됩니다. 이 데이터의 쿼리에 대한 자세한 내용은 [VM용 Azure Monitor에서 로그를 쿼리하는 방법](../insights/vminsights-log-search.md)을 참조하세요. |

### <a name="custom"></a>사용자 지정 

| 데이터 | Description |
|:---|:---|
| REST API | 모든 REST 클라이언트에서 Log Analytics 작업 영역에 데이터를 씁니다. 자세한 내용은 [HTTP 데이터 수집기 API로 Azure Monitor에 로그 데이터 전송](data-collector-api.md)을 참조하세요.
| 논리 앱 | **Azure Log Analytics Data Collector** 작업을 통해 Logic App 워크플로에서 Log Analytics 작업 영역으로 데이터를 씁니다. |

### <a name="security"></a>보안

| 데이터 | Description |
|:---|:---|
| Azure Security Center | [Azure Security Center](../../security-center/index.yml)는 수집하는 데이터를 다른 로그 데이터를 사용하여 분석할 수 있는 Log Analytics 작업 영역에 저장합니다. 작업 영역 구성에 대한 자세한 내용은 [Azure Security Center의 데이터 컬렉션](../../security-center/security-center-enable-data-collection.md)을 참조하세요. |
| Azure Sentinel | [Azure Sentinel](../../sentinel/index.yml)은 데이터 원본에서 Log Analytics 작업 영역으로 데이터를 저장합니다. [데이터 원본에 연결](../../sentinel/connect-data-sources.md)을 참조하세요.  |


## <a name="next-steps"></a>다음 단계

- [Azure Monitor 데이터 플랫폼](data-platform.md)에 대해 자세히 알아봅니다.
- [Azure Monitor의 메트릭](data-platform-metrics.md)을 알아봅니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.

