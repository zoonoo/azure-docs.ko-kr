---
title: Azure 모니터의 로그 | 마이크로 소프트 문서
description: 모니터링 데이터의 고급 분석에 사용되는 Azure Monitor의 로그에 대해 설명합니다.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 0b288bf6d987b9db682c8d1439879cf6b499f213
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457334"
---
# <a name="logs-in-azure-monitor"></a>Azure Monitor의 로그

> [!NOTE]
> Azure Monitor에서 수집한 모든 데이터는 메트릭 및 로그의 두 가지 기본 유형 중 하나에 적합합니다. 이 문서에서는 로그에 대해 설명합니다. 메트릭에 대한 자세한 설명과 Azure [Monitor에서 수집한 모니터링 데이터를](data-platform.md) 참조하여 두 메트릭을 비교하려면 [Azure 모니터의 메트릭을](data-platform-metrics.md) 참조하십시오.

Azure 모니터의 로그는 다양한 소스의 데이터 간에 복잡한 분석을 수행하는 데 특히 유용합니다. 이 문서에서는 Azure Monitor에서 로그가 구조화되는 방법, 데이터로 수행할 수 있는 작업을 설명하고 로그에 데이터를 저장하는 다양한 데이터 원본을 식별합니다.

> [!NOTE]
> Azure 모니터 로그와 Azure의 로그 데이터 원본을 구분하는 것이 중요합니다. 예를 들어 Azure의 구독 수준 이벤트는 Azure Monitor 메뉴에서 볼 수 있는 [활동 로그에](platform-logs-overview.md) 기록됩니다. 대부분의 리소스는 다른 위치로 전달할 수 있는 [리소스 로그에](platform-logs-overview.md) 운영 정보를 씁니다. Azure Monitor Logs는 활동 로그 및 리소스 로그를 다른 모니터링 데이터와 함께 수집하여 전체 리소스 집합에 대한 심층 분석을 제공하는 로그 데이터 플랫폼입니다.

## <a name="what-are-azure-monitor-logs"></a>Azure 모니터 로그란 무엇입니까?

Azure Monitor의 로그에는 각 유형에 대해 서로 다른 속성 집합을 사용하여 레코드로 구성된 다양한 종류의 데이터가 포함됩니다. 로그는 Azure Monitor 메트릭과 같은 숫자 값을 포함할 수 있지만 일반적으로 자세한 설명이 포함된 텍스트 데이터가 포함됩니다. 구조가 다르고 정기적으로 수집되지 않는 경우가 많다는 점에서 메트릭 데이터와 더 다릅니다. 이벤트 및 추적과 같은 원격 분석은 성능 데이터 외에 Azure Monitor Log에 저장되므로 분석을 위해 모두 결합할 수 있습니다.

일반적인 유형의 로그 항목은 산발적으로 수집되는 이벤트입니다. 이벤트는 응용 프로그램이나 서비스에 의해 생성되며 일반적으로 자체적으로 완전한 컨텍스트를 제공하기에 충분한 정보를 포함합니다. 예를 들어, 이벤트는 특정 리소스가 생성 또는 수정되었거나, 트래픽 증가에 대한 응답으로 새 호스트가 시작되었거나, 애플리케이션에서 오류가 검색되었음을 나타낼 수 있습니다.

 데이터의 형식은 다양할 수 있으므로 애플리케이션은 필요한 구조를 사용하여 사용자 지정 로그를 만들 수 있습니다. 메트릭 데이터를 Logs에 저장하여 추세 및 기타 데이터 분석을 위해 다른 모니터링 데이터와 결합할 수도 있습니다.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure 모니터 로그로 무엇을 할 수 있습니까?
다음 표에는 Azure Monitor에서 로그를 사용할 수 있는 여러 가지 방법이 나열되어 있습니다.


|  |  |
|:---|:---|
| 분석 | Azure 포털에서 [로그 분석을](../log-query/get-started-portal.md) 사용하여 [로그 쿼리를](../log-query/log-query-overview.md) 작성하고 강력한 데이터 탐색기 분석 엔진을 사용하여 로그 데이터를 대화형으로 분석합니다.<br>Azure 포털에서 [Application Insights 분석 콘솔을](../app/analytics.md) 사용하여 로그 쿼리를 작성하고 응용 프로그램 인사이트에서 로그 데이터를 대화형으로 분석합니다. |
| 시각화 | [Azure 대시보드에](../../azure-portal/azure-portal-dashboards.md)테이블 또는 차트로 렌더링된 쿼리 결과를 고정합니다.<br>대화형 보고서의 여러 데이터 집합과 결합할 [통합 문서를](../app/usage-workbooks.md) 만듭니다. <br>쿼리의 결과를 [Power BI](powerbi.md)로 내보내 서로 다른 시각화를 사용하고 Azure 외부의 사용자와 공유합니다.<br>쿼리 결과를 [Grafana로](grafana-plugin.md) 내보내 대시보드를 활용하고 다른 데이터 원본과 결합합니다.|
| 경고 | 쿼리의 결과가 특정 결과와 일치할 때 알림을 보내거나 [자동화된 작업](action-groups.md)을 수행하는 [로그 경고 규칙](alerts-log.md)을 구성합니다.<br>[메트릭으로](alerts-metric-logs.md) 추출된 특정 로그 데이터 로그에 메트릭 경고 규칙을 구성합니다. |
| 장치 | [Azure CLI를](/cli/azure/ext/log-analytics/monitor/log-analytics)사용하는 명령줄에서 로그 쿼리 결과에 액세스합니다.<br>[PowerShell cmdlet을](https://docs.microsoft.com/powershell/module/az.operationalinsights)사용하여 명령줄에서 로그 쿼리 결과에 액세스합니다.<br>[REST API를](https://dev.loganalytics.io/)사용하여 사용자 지정 응용 프로그램의 로그 쿼리 결과에 액세스합니다. |
| 내보내기 | 로그 데이터를 검색하고 Logic Apps를 사용하여 외부 위치에 복사하는 워크플로를 [빌드합니다.](~/articles/logic-apps/index.yml) |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure 모니터 로그의 데이터는 어떻게 구성됩니까?
Azure 모니터 로그에 의해 수집된 데이터는 [로그 분석 작업 영역에](../platform/design-logs-deployment.md)저장됩니다. 각 작업 영역에는 각 테이블이 특정 원본의 데이터를 저장하는 여러 테이블이 포함됩니다. 모든 [테이블이 몇 가지 공통 속성을](log-standard-properties.md)공유하지만 각 테이블에는 저장하는 데이터 종류에 따라 고유한 속성 집합이 있습니다. 새 작업 영역에는 표준 테이블 집합이 있으며 작업 영역에 쓰는 다른 모니터링 솔루션 및 기타 서비스에 의해 더 많은 테이블이 추가됩니다.

Application Insights의 로그 데이터는 작업 영역과 동일한 로그 분석 엔진을 사용하지만 모니터링되는 각 응용 프로그램에 대해 별도로 저장됩니다. 각 응용 프로그램에는 응용 프로그램 요청, 예외 및 페이지 뷰와 같은 데이터를 보관하는 표준 테이블 집합이 있습니다.

로그 쿼리는 로그 분석 작업 영역또는 응용 프로그램 인사이트 응용 프로그램의 데이터를 사용합니다. 리소스 간 [쿼리를](../log-query/cross-workspace-query.md) 사용하여 다른 로그 데이터와 함께 응용 프로그램 데이터를 분석하거나 여러 작업 영역 또는 응용 프로그램을 포함한 쿼리를 만들 수 있습니다.

![작업 영역](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>로그 쿼리
Azure Monitor Logs의 데이터는 [Kusto 쿼리 언어로](../log-query/get-started-queries.md)작성된 [로그 쿼리를](../log-query/log-query-overview.md) 사용하여 검색되며, 이를 통해 수집된 데이터를 신속하게 검색, 통합 및 분석할 수 있습니다. [로그 분석을](../log-query/portals.md) 사용하여 Azure 포털에서 로그 쿼리를 작성하고 테스트합니다. 대화형으로 결과를 작업하거나 대시보드에 고정하여 다른 시각화로 볼 수 있습니다.

![Log Analytics](media/data-platform-logs/log-analytics.png)

[애플리케이션 인사이트에서 로그 분석을](../app/analytics.md) 열어 애플리케이션 인사이트 데이터를 분석합니다.

![Application Insights 분석](media/data-platform-logs/app-insights-analytics.png)

로그 분석 API 및 [응용 프로그램 인사이트 REST](https://dev.applicationinsights.io/documentation/overview)API를 사용하여 [로그](https://dev.loganalytics.io/documentation/overview) 데이터를 검색할 수도 있습니다.


## <a name="sources-of-azure-monitor-logs"></a>Azure 모니터 로그의 소스
Azure Monitor는 Azure 내와 온-프레미스 리소스의 다양한 원본에서 로그 데이터를 수집할 수 있습니다. 다음 표에는 Azure Monitor Log에 데이터를 작성하는 여러 리소스에서 사용할 수 있는 다양한 데이터 원본이 나열되어 있습니다. 각 구성에는 필요한 구성에 대한 세부 정보에 대한 링크가 있습니다.

### <a name="azure-tenant-and-subscription"></a>Azure 테넌트 및 구독

| 데이터 | Description |
|:---|:---|
| Azure Active Directory 감사 로그 | 각 디렉터리에 대한 진단 설정을 통해 구성됩니다. [Azure AD 로그 및 Azure AD 로그 통합을](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)참조하십시오.  |
| 활동 로그 | 기본적으로 별도로 저장되며 거의 실시간 경고에 사용할 수 있습니다. 활동 로그 분석 솔루션을 설치하여 로그 애널리틱스 작업 영역에 작성합니다. [로그 분석에서 Azure 활동 로그 수집 및 분석](activity-log-collect.md) |

### <a name="azure-resources"></a>Azure 리소스

| 데이터 | Description |
|:---|:---|
| 리소스 진단 | 로그 분석 작업 영역에 메트릭을 포함하여 진단 데이터에 쓸 진단 설정을 구성합니다. [로그 분석에 Azure 리소스 로그 스트림](resource-logs-collect-workspace.md)을 참조하십시오. |
| 모니터링 솔루션 | 모니터링 솔루션은 수집한 데이터를 Log Analytics 작업 영역에 기록합니다. 솔루션 목록은 [Azure의 관리 솔루션에](../insights/solutions-inventory.md) 대한 데이터 수집 세부 정보를 참조하십시오. 솔루션 설치 및 사용에 대한 자세한 내용은 [Azure 모니터의 모니터링 솔루션을](../insights/solutions.md) 참조하십시오. |
| 메트릭 | Azure Monitor 리소스에 대한 플랫폼 메트릭을 Log Analytics 작업 영역으로 전송하여 더 긴 기간 동안 로그 데이터를 유지하고 [Kusto 쿼리 언어를](/azure/kusto/query/)사용하여 다른 데이터 형식과 함께 복잡한 분석을 수행합니다. [로그 분석에 Azure 리소스 로그 스트림](resource-logs-collect-storage.md)을 참조하십시오. |
| Azure 테이블 스토리지 | 일부 Azure 리소스가 모니터링 데이터를 작성하는 Azure 저장소에서 데이터를 수집합니다. [로그 분석이 있는 이벤트에는 IIS 및 Azure 테이블 저장소에 Azure Blob 저장소 사용을 참조하십시오.](diagnostics-extension-logs.md) |

### <a name="virtual-machines"></a>Virtual Machines

| 데이터 | Description |
|:---|:---|
|  에이전트 데이터 원본 | [Windows](agent-windows.md) 및 [Linux](../learn/quick-collect-linux-computer.md) 에이전트에서 수집된 데이터 원본에는 이벤트, 성능 데이터 및 사용자 지정 로그가 포함됩니다. [Azure 모니터의 에이전트 데이터 원본을](data-sources.md) 참조하여 데이터 원본 목록 및 구성에 대한 세부 정보를 확인합니다. |
| 모니터링 솔루션 | 모니터링 솔루션은 에이전트에서 수집한 데이터를 Log Analytics 작업 영역으로 작성합니다. 솔루션 목록은 [Azure의 관리 솔루션에](../insights/solutions-inventory.md) 대한 데이터 수집 세부 정보를 참조하십시오. 솔루션 설치 및 사용에 대한 자세한 내용은 [Azure 모니터의 모니터링 솔루션을](../insights/solutions.md) 참조하십시오. |
| System Center Operations Manager | 운영 관리자 관리 그룹을 Azure Monitor에 연결하여 온-프레미스 에이전트에서 로그로 이벤트 및 성능 데이터를 수집합니다. 이 구성에 대한 자세한 내용은 [운영 관리자와 로그 분석 연결을](om-agents.md) 참조하십시오. |


### <a name="applications"></a>애플리케이션

| 데이터 | Description |
|:---|:---|
| 요청 및 예외 | 응용 프로그램 요청 및 예외에 대한 자세한 데이터는 _요청,_ _pageView_및 예외 테이블에 _있습니다._ 외부 [구성 요소에](../app/asp-net-dependencies.md) 대한 호출은 _종속성_ 테이블에 있습니다. |
| 사용 및 성능 | 응용 프로그램의 성능은 _요청,_ _브라우저타이밍_ 및 성능 카운터 테이블에서 사용할 수 _있습니다._ 사용자 [지정 메트릭에](../app/api-custom-events-metrics.md#trackevent) 대한 데이터는 사용자 지정 메트릭 테이블에 _있습니다._|
| 추적 데이터 | 분산 [추적의](../app/distributed-tracing.md) 결과는 _추적_ 테이블에 저장됩니다. |
| 가용성 테스트 | [가용성 테스트의](../app/monitor-web-app-availability.md) 요약 데이터는 _가용성Results_ 테이블에 저장됩니다. 이러한 테스트의 자세한 데이터는 별도의 저장소에 있으며 Azure 포털의 응용 프로그램 인사이트에서 액세스합니다. |

### <a name="insights"></a>자세한 정보

| 데이터 | Description |
|:---|:---|
| 컨테이너용 Azure Monitor | [컨테이너에 대 한 Azure 모니터에서](../insights/container-insights-overview.md)수집 하는 인벤토리 및 성능 데이터 입니다. 테이블 목록은 [컨테이너 데이터 수집 세부 정보를](../insights/container-insights-log-search.md#container-records) 참조하십시오. |
| VM용 Azure Monitor | [VM에 대한 Azure 모니터에서](../insights/vminsights-overview.md)수집한 맵 및 성능 데이터 이 데이터 쿼리에 대한 자세한 [내용은 Azure Monitor에서 VM에 대한 로그를 쿼리하는 방법을](../insights/vminsights-log-search.md) 참조하십시오. |

### <a name="custom"></a>사용자 지정 

| 데이터 | Description |
|:---|:---|
| REST API | REST 클라이언트의 로그 분석 작업 영역에 데이터를 씁니다. 자세한 내용은 [HTTP 데이터 수집기 API를 사용하여 Azure 모니터로 로그 데이터 보내기를](data-collector-api.md) 참조하십시오.
| 논리 앱 | Azure Log Analytics **데이터 수집기** 작업을 사용하여 논리 앱 워크플로에서 로그 분석 작업 영역에 데이터를 씁니다. |

### <a name="security"></a>보안

| 데이터 | Description |
|:---|:---|
| Azure Security Center | [Azure Security Center는](/azure/security-center/) 수집한 데이터를 로그 분석 작업 영역에 저장하여 다른 로그 데이터로 분석할 수 있습니다. 작업 영역 구성에 대한 자세한 내용은 [Azure 보안 센터의 데이터 수집을](../../security-center/security-center-enable-data-collection.md) 참조하십시오. |
| Azure Sentinel | [Azure Sentinel은](/azure/sentinel/) 데이터 원본의 데이터를 로그 분석 작업 영역에 저장합니다. [데이터 원본 연결을](/azure/sentinel/connect-data-sources)참조하십시오.  |


## <a name="next-steps"></a>다음 단계

- Azure 모니터 [데이터 플랫폼에](data-platform.md)대해 자세히 알아봅니다.
- Azure [모니터의 메트릭에](data-platform-metrics.md)대해 알아봅니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
