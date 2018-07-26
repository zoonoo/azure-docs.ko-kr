---
title: Azure의 모니터링 데이터 수집 | Microsoft Docs
description: Azure의 응용 프로그램 및 서비스와 분석에 사용된 도구에서 수집된 모니터링 데이터의 개요입니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: d3ebd512f8244de74c009ac8a2936ed8e817dad9
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992465"
---
# <a name="collecting-monitoring-data-in-azure"></a>Azure의 모니터링 데이터 수집
이 문서에서는 Azure의 응용 프로그램 및 서비스와 분석에 사용된 도구에서 수집된 모니터링 데이터의 개요를 제공합니다. 

## <a name="types-of-monitoring-data"></a>모니터링 데이터의 형식
모든 모니터링 데이터는 두 가지 기본 형식, 메트릭 또는 로그 중 하나에 적합합니다. 각 형식에는 고유한 특징이 있으며 아래 설명된 대로 특정 시나리오에 가장 적합합니다.

### <a name="metrics"></a>메트릭
메트릭은 시간상 특정 지점에서 시스템의 일부 측면을 설명하는 숫자 값입니다. 자체 값, 값이 수집된 시간, 값이 나타내는 측정 형식 및 값이 연결된 특정 리소스를 비롯한 고유한 데이터 값이 포함됩니다. 메트릭은 값이 변경되었는지 여부와 상관 없이 정기적으로 수집됩니다. 예를 들어 1분 단위 가상 머신에서의 프로세서의 사용률 또는 10분 단위 응용 프로그램에 로그인한 사용자의 수를 수집할 수 있습니다.

메트릭은 간단하며 실시간에 가까운 시나리오를 지원할 수 있습니다. 메트릭은 자주 샘플링할 수 있고 경고는 비교적 간단한 논리를 사용하여 신속하게 발생하므로, 메트릭은 경고에 특히 유용합니다. 예를 들어 메트릭이 임계값을 초과할 때 경고를 발생시키거나 두 메트릭 값 간 차이가 특정 값에 도달할 때 경고를 발생시킬 수 있습니다.

일반적으로 개별 메트릭은 자체적으로 간략한 정보를 제공합니다. 단순 임계값과의 비교 이외에 컨텍스트 없는 단일 값을 제공합니다. 그럼에도 패턴 및 추세를 식별하기 위해 다른 메트릭과 결합하거나 특정 값 주위 컨텍스트를 제공하는 로그와 결합할 때 유용합니다. 예를 들어, 지정된 시간에 응용 프로그램의 특정 사용자 수는 응용 프로그램의 상태에 대해 간략한 정보를 제공할 수 있습니다. 동일한 메트릭의 여러 값으로 표시된 갑작스런 사용자 감소는 문제를 의미할 수도 있습니다. 응용 프로그램에서 throw되고 개별 메트릭으로 표시된 과도한 예외는 감소를 유발하는 응용 프로그램 문제를 식별할 수 있습니다. 응용 프로그램에서 생성되어 응용 프로그램의 특정 구성 요소에서 오류를 식별하는 이벤트는 근본 원인을 식별하는 데 유용합니다.

로그를 기반으로 한 경고는 메트릭을 기반으로 한 경고만큼 신속하게 대응하지는 못하지만 더 복잡한 논리를 포함할 수 있습니다. 여러 원본의 데이터에서 복잡한 분석을 수행하는 모든 쿼리의 결과를 기반으로 경고를 만들 수 있습니다.

### <a name="logs"></a>로그
로그에는 각 형식에 대해 다양한 속성 집합이 포함된 레코드로 구성된 다양한 데이터 형식이 포함됩니다. 로그는 메트릭과 같은 숫자 값을 포함할 수 있지만 일반적으로 자세한 설명이 포함된 텍스트 데이터를 포함합니다. 해당 구조가 다양하다는 점에서 메트릭과는 구별되며 정기적으로 수집되지 않는 경우가 많습니다.

로그 항목의 일반적인 형식은 이벤트입니다. 이벤트는 응용 프로그램 또는 서비스에서 만들어지므로 산발적으로 수집되며 보통 자체적으로 완전한 컨텍스트를 제공할 수 있는 충분한 정보를 포함합니다.  예를 들어, 이벤트는 특정 리소스가 생성되거나 수정되었거나, 증가한 트래픽에 대한 응답으로 새 호스트가 시작되었거나, 또는 응용 프로그램에서 오류가 발견되었음을 나타낼 수 있습니다.

로그는 복잡한 분석 및 시간 경과 따른 추세를 위해 다양한 원본의 데이터를 결합하는 데 특히 유용합니다. 데이터의 형식은 다양할 수 있으므로 응용 프로그램은 필요한 구조를 사용하여 사용자 지정 로그를 만들 수 있습니다. 메트릭은 추세 및 다른 데이터 분석을 위해 다른 모니터링 데이터와 결합하도록 로그에서 복제할 수도 있습니다.


## <a name="monitoring-tools-in-azure"></a>Azure의 모니터링 도구
Azure의 모니터링 데이터는 다음 섹션에서 설명하는 여러 소스를 사용하여 수집 및 분석됩니다.

### <a name="azure-metrics"></a>Azure Metrics
Azure 리소스 및 응용 프로그램의 메트릭은 Azure Metrics에 수집됩니다. 메트릭 데이터는 선택한 머신에 대한 CPU 및 네트워크 사용률로 그러한 메트릭의 그래프를 포함하는 가상 머신과 같은 특정 Azure 리소스에 대해 Azure Portal의 페이지로 통합됩니다. 시간이 지남에 따라 여러 메트릭 값을 차트로 기록할 수 있는 [메트릭 탐색기](../monitoring-and-diagnostics/monitoring-metric-charts.md)를 사용하여 분석할 수도 있습니다.  대화형으로 차트를 보거나 다른 시각화 요소를 사용하여 보려는 대시보드에 고정할 수 있습니다. [Azure 모니터링 REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md)를 사용하여 메트릭을 검색할 수도 있습니다.

[Azure의 모니터링 데이터 원본](monitoring-data-sources.md)에서 다른 종류의 Azure 리소스에 의해 수집되는 메트릭 데이터에 대한 세부 정보를 얻을 수 있습니다. 

![메트릭 탐색기](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Azure 동작 로그 
[Azure 활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)는 Azure 서비스의 구성 및 상태에 대한 로그를 저장합니다. 이러한 로그는 활동 로그 탐색기를 사용하여 Azure Portal에서 볼 수 있지만, 일반적으로 다른 로그 데이터를 통해 분석하기 위해 [Log Analytics로 복사](../log-analytics/log-analytics-activity.md)됩니다.

활동 로그 탐색기를 사용하여 특정 조건과 일치하는 필터링된 활동 로그를 볼 수 있습니다.  대부분의 리소스에는 해당 리소스에 대한 활동 로그 탐색기를 표시하는 활동 로그 옵션이 Azure Portal의 해당 메뉴에 있습니다. [Azure 모니터링 REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md)를 사용하여 활동 로그를 검색할 수도 있습니다.

![활동 로그 탐색기](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics는 Azure에서 관리를 위한 일반적인 데이터 플랫폼을 제공합니다. Azure에서 로그의 저장 및 분석을 위한 기본적인 서비스로, 가상 머신의 에이전트, 관리 솔루션 및 다양한 Azure 리소스를 포함한 다양한 원본에서 데이터를 수집합니다. 메트릭 및 활동 로그를 포함한 다른 원본의 데이터는 모니터링 데이터의 완전한 중앙 리포지토리를 만들기 위해 Log Analytics로 복사할 수 있습니다.

Log Analytics에는 수집하는 데이터를 분석하기 위한 풍부한 쿼리 언어가 있습니다.  [로그 검색 포털](../log-analytics/log-analytics-log-search-portals.md)을 사용하여 대화형으로 쿼리를 작성 및 테스트하고 해당 결과를 분석할 수 있습니다. 또한 [뷰를 만들어서](../log-analytics/log-analytics-view-designer.md) 로그 검색의 결과를 시각화하거나 Azure 대시보드에 직접 쿼리 결과를 붙여넣을 수 있습니다.  관리 솔루션에는 수집하는 데이터의 분석을 위해 Log Analytics에서의 로그 검색 및 보기 기능이 포함됩니다. Application Insights와 같은 다른 서비스는 Log Analytics에 데이터를 저장하고 분석을 위한 추가 도구를 제공합니다.  

![로그](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights는 다양한 플랫폼에 설치된 웹 응용 프로그램에 대한 원격 분석을 수집합니다. 해당 데이터의 분석 및 시각화를 위해 Azure Metrics 및 Log Analytics에 데이터를 저장하고, 이 데이터를 분석하는 기존 도구 외에도 풍부한 도구 집합을 제공합니다. 따라서 기타 모니터링에 사용하는 경고, 로그 검색 및 대시보드와 같은 일반적인 서비스 집합을 활용할 수 있습니다.


![App Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>서비스 맵
서비스 맵은 프로세스 및 종속성을 사용하여 가상 머신의 시각적 표현을 제공합니다. 다른 관리 데이터를 사용하여 분석할 수 있도록 이 데이터의 대부분을 Log Analytics에 저장합니다. 또한 서비스 맵 콘솔은 Log Analytics에서 데이터를 검색하여 분석 중인 가상 머신의 컨텍스트에 표시합니다.

![서비스 맵](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>모니터링 데이터 전송

### <a name="metrics-to-logs"></a>메트릭에서 로그로
메트릭은 풍부한 쿼리 언어를 사용하여 다른 데이터 형식을 통한 복잡한 분석을 수행하도록 Log Analytics로 복제될 수도 있습니다. 또한 메트릭에 비해 더 오랫동안 로그 데이터를 유지할 수 있어 시간 경과에 따른 추세를 수행할 수 있습니다. 메트릭 또는 기타 성능 데이터가 Log Analytics에 저장된 경우 해당 데이터는 로그의 역할을 합니다. 기타 데이터를 통해 추세 및 분석에 대한 로그를 사용하는 동안 메트릭을 사용하여 거의 실시간 분석 및 경고를 지원합니다.

Azure 리소스에서 메트릭을 수집하기 위한 지침은 [Log Analytics에서 사용할 Azure 서비스 로그 및 메트릭 수집](../log-analytics/log-analytics-azure-storage.md)에서 확인할 수 있습니다. Azure PaaS 리소스에서 리소스 메트릭을 수집하기 위한 지침은 [Log Analytics로 Azure PaaS 리소스 메트릭의 수집 구성](../log-analytics/log-analytics-collect-azurepass-posh.md)에서 확인할 수 있습니다.

### <a name="logs-to-metrics"></a>메트릭에서 로그로
위에 나온 설명과 같이 메트릭은 로그보다 더 반응성이 뛰어나므로 사용자는 더 낮은 대기 시간 및 더 낮은 비용으로 경고를 만들 수 있습니다. Log Analytics는 메트릭에 적합한 상당한 양의 데이터를 수집하지만, Azure Metrics에 저장되지는 않습니다. 일반적인 예로 에이전트 및 관리 솔루션에서 수집된 성능 데이터가 있습니다. 이러한 값 중 일부는 메트릭 탐색기를 사용하여 경고 및 분석에 사용할 수 있는 Azure Metrics로 복사할 수 있습니다.

이 기능에 대한 설명은 [제한된 공개 미리 보기에서 공개된 로그에 대한 더 빠른 메트릭 경고](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/)에서 확인할 수 있습니다. 지원되는 값의 목록은 [새 메트릭 경고에 지원되는 메트릭 및 생성 방법](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)에서 확인할 수 있습니다.

### <a name="event-hub"></a>이벤트 허브
모니터링 데이터를 분석하기 위해 Azure에서 도구를 사용하는 것 외에, SIEM(보안 정보 및 이벤트 관리) 제품과 같은 외부 도구로 전달할 수도 있습니다. 이는 일반적으로 [Azure Event Hub](https://docs.microsoft.com/azure/event-hubs/)를 사용하여 수행됩니다. 

다양한 모니터링 데이터 유형에 대한 지침은 [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](monitoring-data-sources.md)에 대해 알아봅니다. 