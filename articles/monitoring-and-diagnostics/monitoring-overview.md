---
title: "Azure 응용 프로그램 및 리소스 모니터링 | Microsoft Docs"
description: "Azure 서비스 및 응용 프로그램에 대한 전체 모니터링 전략에 작용하는 다른 Microsoft 서비스 및 기능을 대략적으로 설명합니다."
author: robb
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: robb,bwren
ms.openlocfilehash: 3ab7d2d5c3b95d215f3ee9eb9346e8a7895e734c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Azure 응용 프로그램 및 리소스 모니터링

모니터링은 비즈니스 응용 프로그램 및 종속 리소스의 성능, 상태 및 가용성을 결정하기 위해 데이터를 수집 및 분석하는 작업입니다. 효과적인 모니터링 전략은 응용 프로그램의 다양한 구성 요소에 대한 자세한 작동을 이해하고, 문제가 심각해지기 전에 해결할 수 있도록 중대한 문제를 사전에 알림으로써 작동 시간을 늘리는 데 도움을 줍니다.

Azure에는 모니터링 공간에서는 개별적으로 특정 역할 및 태스크를 수행하지만, 응용 프로그램의 원격 분석 및 이러한 기능을 지원하는 기본 Azure 리소스를 수집, 분석 및 작동하기 위한 포괄적인 솔루션을 함께 전달하는 여러 서비스가 포함되어 있습니다.  이러한 서비스가 하이브리드 모니터링 환경을 제공하기 위해 중요한 온-프레미스 리소스를 모니터링하기 위해 작동할 수도 있습니다.   사용 가능한 도구와 데이터를 이해하는 것이 응용 프로그램에 대한 전체 모니터링 전략을 개발하는 첫 번째 단계입니다. 

다음 다이어그램에서는 함께 Azure 리소스의 모니터링 기능을 제공하는 다양한 구성 요소에 대한 개념적 보기를 나타냅니다.  이러한 각 구성 요소는 다음 섹션에 설명되어 있으며 자세한 기술 정보에 대한 링크도 제공됩니다.

![모니터링 개요](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>기본 모니터링
기본 모니터링은 Azure 리소스 간의 기본적인 필수 모니터링을 제공합니다.  이러한 서비스는 최소 구성이 필요하며, 프리미엄 모니터링 서비스에 사용되는 핵심 원격 분석을 수집합니다.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)는 [메트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) 및 [진단 로그](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)를 수집하도록 하여 Azure 서비스에 대해 기본적인 모니터링을 수행할 수 있도록 합니다.  예를 들어, 활동 로그는 새 리소스가 만들어지거나 수정되면 사용자에게 알려줍니다.  사용할 수 있는 다양한 리소스 및 가상 머신 내부의 운영 체제에 대한 성능 통계를 제공하는 메트릭을 사용할 수 있습니다.  Azure Portal의 탐색기 중 하나에서 이 데이터를 보거나, 추세 파악 및 자세한 분석을 위해 Log Analytics로 전송하거나, 중대한 문제를 사전에 알리기 위한 경고 규칙을 만들 수 있습니다.

### <a name="service-health"></a>서비스 상태
응용 프로그램의 상태는 종속되는 Azure 서비스에 따라 좌우됩니다.  [Azure Service Health](../service-health/service-health-overview.md)는 응용 프로 그램에 영향을 미칠 수 있는 Azure 서비스의 문제를 식별하고, 일정 유지 관리를 계획하는 데 도움을 줍니다.

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md)는 리소스 구성 및 사용량 원격 분석을 지속적으로 모니터링하여 모범 사례에 따른 개인 설정된 권장 지침을 제공합니다.  이러한 권장 지침을 따르면 응용 프로그램을 지원하는 리소스의 성능, 보안 및 가용성을 개선하는 데 도움이 됩니다.


## <a name="premium-monitoring-services"></a>프리미엄 모니터링 서비스
다음과 같은 Azure 서비스는 모니터링 데이터를 수집 및 분석하기 위한 풍부한 기능을 제공합니다.  이러한 기능은 기본 모니터링을 토대로 구축되며, Azure의 일반적인 기능을 활용하고, 수집된 데이터에 대한 강력한 분석 기능을 제공하여 응용 프로그램 및 인프라에 대한 고유한 통찰력을 얻도록 합니다.  이러한 기능은 다양한 사용자를 대상으로 하는 특정 시나리오의 컨텍스트 내에서 데이터를 제공합니다.

### <a name="application-insights"></a>Application Insights
[Application Insights](http://azure.microsoft.com/documentation/services/application-insights)를 사용하여 클라우드 또는 온-프레미스에서 호스트되는 응용 프로그램의 가용성, 성능 및 사용량을 모니터링할 수 있습니다.  응용 프로그램을 Application Insights에서 적절히 설정하여, 사용자가 보고할 때까지 기다리지 않고도 오류를 빠르게 식별하고 분석할 수 있는 통찰력을 얻을 수 있습니다. 수집한 정보를 사용하여, 응용 프로그램의 유지 관리 및 개선에 대해 현명한 결정을 내릴 수 있습니다.  Application Insights는 수집한 데이터와 상호 작용하기 위한 포괄적인 도구 외에, 경고, 대시보드 및 심층 분석과 같은 공유 기능을 Log Analytics 쿼리 언어와 함께 사용하기 위해 해당 데이터를 공통 리포지토리에 저장합니다.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)는 다양한 리소스의 데이터를 강력한 쿼리 언어로 분석할 수 있는 단일 리포지토리로 수집함으로써 Azure 모니터링에서 중심 역할을 수행합니다.  Application Insights 및 Azure Security Center는 해당 데이터를 Log Analytics 데이터 저장소에 저장한 후 해당 분석 엔진을 활용합니다.  Azure Monitor과 클라우드 또는 온-프레미스의 가상 머신에 설치된 관리 솔루션 및 에이전트에서 수집된 이러한 데이터 조합을 통해 전체 환경을 완벽하게 이해할 수 있습니다. 


### <a name="service-map"></a>서비스 맵
[서비스 맵](../operations-management-suite/operations-management-suite-service-map.md)은 다른 컴퓨터의 다른 프로세스 및 종속성과 외부 프로세스를 통해 가상 머신을 분석하여 IaaS 환경을 이해할 수 있습니다.  또한 Log Analytics에서 이벤트, 성능 데이터 및 관리 솔루션을 통합하므로, 각 컴퓨터의 컨텍스트와 나머지 환경과의 관계를 토대로 이 데이터를 볼 수 있습니다.  서비스 맵은 [Application Insights의 응용 프로그램 맵](../application-insights/app-insights-app-map.md)과 비슷하지만 응용 프로그램을 지원하는 인프라 구성 요소에 중점을 둡니다.

### <a name="network-watcher"></a>Network Watcher
[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)는 Azure의 여러 다른 네트워크 시나리오에 대한 시나리오 기반 모니터링 및 진단을 제공합니다.  추가 분석을 위해 Azure 메트릭 및 진단에 데이터를 저장하고, [Log Analytics의 관리 솔루션](../log-analytics/log-analytics-azure-networking-analytics.md)과 함께 작동하여 네트워크 리소스에 대한 전체 모니터링을 제공합니다.


### <a name="management-solutions"></a>관리 솔루션
[관리 솔루션](../log-analytics/log-analytics-add-solutions.md)은 특정 응용 프로그램이나 서비스를 깊이 있게 이해하도록 하는 패키지된 논리 집합입니다.  이 솔루션은 수집한 모니터링 데이터를 저장 및 분석하기 위해 Log Analytics를 활용합니다.  관리 솔루션은 Microsoft 및 다양한 Azure 및 타사 서비스에 대한 모니터링을 제공하는 파트너 업체를 통해 받을 수 있습니다. 모니터링 솔루션의 예에는 컨테이너 호스트를 보고 관리하는 데 도움이 되는 [컨테이너 모니터링](../log-analytics/log-analytics-containers.md)과 SQL Azure Database에 대한 성능 메트릭을수집하 고 시각화하는 [Azure SQL 분석](../log-analytics/log-analytics-azure-sql.md)이 포함됩니다.


## <a name="shared-functionality"></a>공유 기능
다음과 같은 Azure 도구는 프리미엄 모니터링 서비스에 대한 중요한 기능을 제공합니다.  이러한 도구는 여러 서비스에서 공유되므로 서비스 간에 공통 기능 및 구성을 활용할 수 있습니다.

### <a name="alerts"></a>Alerts
[Azure 경고](../monitoring-and-diagnostics/monitoring-overview-alerts.md)는 위험 조건을 사전에 알리고 정정 작업을 수행합니다.  경고 규칙은 메트릭 및 로그를 포함하여 여러 원본의 데이터를 활용할 수 있습니다. 또한 고유한 수신자 집합과 경고에 대한 작업을 포함하는 [작업 그룹](../monitoring-and-diagnostics/monitoring-action-groups.md)을 사용합니다.  사용자의 요구 사항에 따라, 웹후크를 사용하여 경고가 외부 작업을 시작하고 ITSM 도구와 통합되도록 할 수 있습니다.

### <a name="dashboards"></a>대시보드
[Azure 대시보드](../azure-portal/azure-portal-dashboards.md)에서는 다양한 종류의 데이터를 Azure Portal의 단일 창으로 결합하고, 다른 Azure 사용자와 공유할 수 있습니다.  예를 들어, 메트릭 그래프, 활동 로그 표, Application Insights의 사용 현황 차트 및 Log Analytics의 로그 검색 결과를 나타내는 타일이 조합된 대시보드를 만들 수 있습니다.

Log Analytics 데이터를 [Power BI](https://docs.microsoft.com/power-bi/)로 내보내 추가적인 시각화 요소를 활용하고, 해당 데이터를 조직 내부 및 외부의 사용자가 사용하도록 할 수도 있습니다.

### <a name="metrics-explorer"></a>메트릭 탐색기
[메트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md)은 Azure 리소스에서 생성된 숫자 값으로, 리소스의 작동 및 성능을 이해하는 데 도움이 됩니다. 다른 원본의 데이터로 분석하기 위해 Log Analytics으로 메트릭을 전송할 수 있습니다.



### <a name="activity-logs"></a>활동 로그
[활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)는 Azure 리소스의 작동에 대한 데이터를 제공합니다.  여기에는 리소스에 대한 구성 변경 내용, 서비스 상태 문제, 리소스를 보다 잘 활용하기 위한 권장 지침 및 자동 크기 조정 작업과 관련된 정보와 같은 정보가 포함됩니다.  Azure Portal의 해당 페이지에서 특정 리소스에 대한 로그를 보거나, 활동 로그 탐색기에서 여러 리소스에 대한 로그를 볼 수 있습니다.  관리 솔루션, 가상 컴퓨터의 에이전트 및 기타 원본에서 수집된 데이터를 통해 분석할 수 있도록 Log Analytics에 활동 로그를 전송할 수도 있습니다.


## <a name="example-scenarios"></a>예제 시나리오
다음은 다양한 시나리오에서 Azure의 여러 다른 모니터링 도구를 활용하는 방법을 보여 주는 개략적인 예제입니다.

### <a name="monitoring-a-web-application"></a>웹 응용 프로그램 모니터링
App Services, Azure Storage 및 SQL Database를 사용하여 Azure에 배포된 웹 응용 프로그램을 고려합니다.  먼저 Azure Portal의 해당 페이지에서 이러한 개별 리소스에 대한 [메트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md) 및 [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)에 액세스합니다.  여기에는 구성 변경을 식별하는 것 외에도, 응용 프로그램에 대한 요청 수 및 평균 응답 시간과 같은 중요한 정보가 포함됩니다.

다양한 리소스에 대한 메트릭 및 로그를 보기 위해서는 포털의 모니터로 이동할 수 있습니다.  메트릭에 대한 표준 매개 변수를 결정할 때 평균 응답 시간이 임계값을 초과하도록 증가할 경우 사전에 사용자에게 알려주는 [경고 규칙을 만듭니다](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md).  응용 프로그램의 일별 성능을 빠르게 나타내려면 중요 KPI의 메트릭 그래프를 표시하는 Azure 대시보드를 만듭니다.

응용 프로그램의 심층 모니터링을 수행하려면 [Application Insights용으로 구성](../application-insights/quick-monitor-portal.md)합니다.  이제 응용 프로그램의 작동 및 성능을 좀 더 자세히 분석하는 추가 데이터를 수집할 수 있습니다.  Application Insights는 앱의 구성 요소 간 기본 관계를 파악하여, [종단 간 추적](../application-insights/app-insights-transaction-diagnostics.md) 기능과 결합된 [응용 프로그램 맵](../application-insights/app-insights-app-map.md)을 통해 구현된 시각적 표현으로 문제가 발생한 정확한 구성 요소, 종속성 또는 예외를 진단할 수 있도록 합니다.  사전에 여러 다른 지역에서 응용 프로그램을 테스트하는 [가용성 테스트](../application-insights/app-insights-monitor-web-app-availability.md)를 만듭니다.  프로그램 개발자를 지원하기 위해, 특정 코드 줄을 따라 요청 및 예외를 추적할 수 있는 [Profiler를 사용하도록 설정](../application-insights/enable-profiler-compute.md)합니다.  

응용 프로그램에서 사용되는 서비스에 대한 추가 가시성을 확보하기 위해 [SQL 분석 솔루션](../log-analytics/log-analytics-azure-sql.md)을 추가하여 Log Analytics에 추가 데이터를 수집합니다. 일정 시간이 지난 후 사이트의 성능이 임계값 아래로 떨어지는 기간 동안 근본 원인을 조사하기로 결정합니다.  Log Analytics를 사용하여 Application Insights에서 수집된 사용량 및 성능 데이터와 응용 프로그램을 지원하는 Azure 리소스의 구성 및 성능 데이터 간의 상관 관계를 파악하는 쿼리를 작성합니다.


### <a name="monitoring-virtual-machines"></a>가상 머신 모니터링
Azure에서는 Windows 및 Linux 가상 머신이 혼합되어 실행됩니다.  Azure Monitor를 사용하여 [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) 및 [호스트 수준 메트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md)을 확인하고, 게스트 운영 체제에서 메트릭을 수집할 수 있도록 [Azure 진단 확장](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension)을 가상 머신에 추가합니다.  그런 후 프로세서 사용량 및 메모리와 같은 기본 메트릭이 임계값을 초과할 때 사용자에게 사전에 알리는 [경고 규칙](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)을 만듭니다.

비즈니스 응용 프로그램을 실행하는 가상 머신에 대한 자세한 정보를 수집하려면 각 컴퓨터에서 [Log Analytics 작업 영역을 만들고 VM 확장을 사용하도록 설정](../log-analytics/log-analytics-quick-collect-azurevm.md)합니다.  응용 프로그램에 대해 [여러 다른 데이터 원본 컬렉션](../log-analytics/log-analytics-data-sources.md)을 구성하고, 일별 작업 및 성능을 보고하는 [보기를 만듭니다](../log-analytics/log-analytics-view-designer.md).  그런 후 특정 오류 이벤트가 수신될 때 알리는 [경고 규칙을 만듭니다](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md).  설치된 에이전트의 상태를 지속적으로 모니터링하려면 [에이전트 상태 관리 솔루션](../operations-management-suite/oms-solution-agenthealth.md)을 추가합니다.

응용 프로그램을 보다 자세히 이해하려면 [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md)에 추가할 수 있게 가상 머신에 [종속성 에이전트를 추가](../operations-management-suite/operations-management-suite-service-map-configure.md)합니다.  중요 프로세스를 검색하고 다른 서비스와 컴퓨터 간의 연결을 식별합니다.  보고된 작동 중단 후, 서비스 맵을 통해 법정 분석을 수행하여 문제가 발생한 특정 컴퓨터를 식별합니다.  그런 다음 미래의 문제를 식별하기 위한 [Log Analytics 데이터에 대한 쿼리](../log-analytics/log-analytics-log-search-new.md)를 만들고, 특정 조건이 감지될 때 사전에 알리는 경고 규칙을 만듭니다.



## <a name="next-steps"></a>다음 단계
자세한 정보

* [Ignite 2016의 비디오에서 Azure Monitor](https://myignite.microsoft.com/videos/4977)
* [Azure Monitor 시작](monitoring-get-started.md)
* [Azure 진단](../azure-diagnostics.md) - 클라우드 서비스, Virtual Machine, 가상 머신 확장 집합 또는 Service Fabric 응용 프로그램에서 문제를 진단하려는 경우
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) - App Service 웹앱에서 문제를 진단하려는 경우
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) - 수집한 모니터링 데이터를 분석하려는 경우
