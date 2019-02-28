---
title: Azure Monitor에서 수집된 데이터 모니터링 | Microsoft Docs
description: Azure Monitor에서 수집된 데이터 모니터링은 간단한 메트릭으로 구분되며 고급 분석에 사용되는 실시간 시나리오 및 로그를 지원할 수 있습니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: bwren
ms.openlocfilehash: 9b3e35e8372e5488fd97da3f035c29940cb1f293
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269198"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>Azure Monitor에서 수집된 데이터 모니터링
[Azure Monitor](../overview.md)는 사용하는 애플리케이션 및 리소스를 모니터링하는 데 도움이 되는 서비스입니다. 이 기능의 중심은 원격 분석의 저장소 및 모니터링된 리소스의 기타 데이터입니다. 이 문서에서는 이 데이터가 저장되고 Azure Monitor에서 사용되는 방법에 대한 전체 설명을 제공합니다.

Azure Monitor에서 수집된 모든 데이터는 두 가지 기본 유형, [메트릭](#metrics) 및 [로그](#logs) 중 하나에 맞습니다. 메트릭은 시간상 특정 지점에서 시스템의 일부 측면을 설명하는 숫자 값입니다. 메트릭은 간단하며 실시간에 가까운 시나리오를 지원할 수 있습니다. 로그에는 각 형식에 대해 다양한 속성 집합이 포함된 레코드로 구성된 다양한 데이터 형식이 포함됩니다. 이벤트 및 추적과 같은 원격 분석은 분석을 위해 모두 결합될 수 있도록 성능 데이터 외에 로그로 저장됩니다.

![Azure Monitor 개요](media/data-collection/overview.png)

## <a name="metrics"></a>메트릭
메트릭은 특정 시간에 시스템의 일부 측면을 설명하는 숫자 값입니다. 메트릭은 간단하며 실시간에 가까운 시나리오를 지원할 수 있습니다. 메트릭은 값이 변경되었는지 여부와 상관 없이 정기적으로 수집됩니다. 메트릭은 자주 샘플링할 수 있고 경고는 비교적 간단한 논리를 사용하여 신속하게 발생시킬 수 있기 때문에, 메트릭은 경고에 유용합니다.

예를 들어 1분마다 가상 머신에서 프로세서의 사용률을 수집하거나 10분마다 애플리케이션에 로그인한 사용자의 수를 수집할 수 있습니다. 수집된 값 중 하나 또는 두 값 간의 차이가 정의된 임계값을 초과할 때 경고를 발생시킬 수 있습니다.

Azure에서 특정 메트릭 특성은 다음을 포함합니다.

* 1분 빈도로 수집됩니다. 달리 지정되지 않은 한, 그 외에는 메트릭의 정의에 있습니다.
* 범주의 역할을 하는 메트릭 이름 및 네임스페이스로 고유하게 식별됩니다.
* 93일 동안 저장됩니다. 장기 추세 분석을 위해 로그에 메트릭을 복사할 수 있습니다.

각 메트릭 값에는 다음과 같은 속성이 있습니다.
* 값이 수집된 시간
* 값이 나타내는 측정값 유형
* 값이 연결된 리소스
* 값 자체
* 일부 메트릭에는 다음 섹션에 설명된 대로 여러 차원이 있을 수 있습니다. 사용자 지정 메트릭에는 최대 10개의 차원이 있을 수 있습니다.

### <a name="multi-dimensional-metrics"></a>다차원 메트릭
메트릭의 차원은 메트릭 값을 설명하도록 추가 데이터를 전송하는 이름 값 쌍입니다. 예를 들어 _사용 가능한 디스크 공간_ 메트릭에는 _C:_, _D:_ 값을 가진 _드라이브_라는 차원이 있을 수 있습니다. 이 값을 사용하면 모든 드라이브 또는 각 드라이브에 개별적으로 사용 가능한 디스크 공간을 볼 수 있습니다.

아래 예제에서는 _네트워크 처리량_이라는 가상의 메트릭에 대한 두 개의 데이터 세트를 보여줍니다. 첫 번째 데이터 세트에는 차원이 없습니다. 두 번째 데이터 세트에는 두 가지 차원, 즉 _IP 주소_와 _방향_(Direction)의 값이 표시됩니다.

### <a name="network-throughput"></a>네트워크 처리량

| 타임 스탬프     | 메트릭 값 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8Kbps |
| 8/9/2017 8:15 | 1,141.4Kbps |
| 8/9/2017 8:16 | 1,110.2Kbps |

이 비차원 메트릭은 "지정된 시간에 내 네트워크 처리량은 무엇이었나요?"와 같은 기본적인 질문에만 응답할 수 있습니다.

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>네트워크 처리량 +2 차원("IP" 및 "방향")

| 타임 스탬프     | 차원 "IP"   | 차원 "방향" | 메트릭 값|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | 방향="전송"    | 646.5Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | 방향="수신" | 420.1Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 방향="전송"    | 150.0Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 방향="수신" | 115.2Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 방향="전송"    | 515.2Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 방향="수신" | 371.1Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 방향="전송"    | 155.0Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 방향="수신" | 100.1Kbps |

이 메트릭은 "각 IP 주소에 대한 네트워크 처리량은 무엇이었나요?", "전송된 데이터 및 수신된 데이터의 양은 얼마인가요?"와 같은 질문에 대답할 수 있습니다. 다차원 메트릭은 비차원 메트릭에 대해 추가 분석 및 진단 값을 가집니다.

### <a name="value-of-metrics"></a>메트릭 값
일반적으로 개별 메트릭은 자체적으로 간략한 정보를 제공합니다. 단순 임계값과의 비교 이외에 컨텍스트 없는 단일 값을 제공합니다. 다른 메트릭과 결합하여 패턴 및 추세를 식별하거나 특정 값에 대한 컨텍스트를 제공하는 로그와 결합할 때 유용합니다.

예를 들어, 지정된 시간에 애플리케이션의 특정 사용자 수는 애플리케이션의 상태를 거의 알려주지 못할 수도 있습니다. 하지만 동일한 메트릭의 여러 값으로 표시된 사용자의 급격한 감소는 문제를 나타낼 수 있습니다. 애플리케이션에서 throw되고 별도의 메트릭으로 표시되는 과도한 예외는 감소를 유발하는 애플리케이션 문제를 식별할 수 있습니다. 애플리케이션에서 해당 구성 요소의 오류를 식별하기 위해 만드는 이벤트는 근본 원인을 파악하는 데 도움이 될 수 있습니다.

### <a name="sources-of-metric-data"></a>메트릭 데이터의 원본
Azure Monitor에서 수집되는 메트릭의 세 가지 기본 원본이 있습니다. 이러한 모든 메트릭을 해당 원본에 관계없이 함께 평가할 수 있는 메트릭 저장소에서 사용할 수 있습니다.

**플랫폼 메트릭**은 Azure 리소스에서 생성되고 해당 상태 및 성능에 대한 가시성을 제공합니다. 각 리소스 유형은 필요한 구성 없이 [고유 메트릭 집합](metrics-supported.md)을 만듭니다. 

**애플리케이션 메트릭**은 모니터링된 애플리케이션에 대한 Application Insights에 의해 생성되며 성능 문제를 감지하고 애플리케이션이 사용되는 추세를 추적하는 데 도움이 됩니다. _서버 응답 시간_ 및 _브라우저 예외_와 같은 값을 포함합니다.

**사용자 지정 메트릭**은 자동으로 사용할 수 있는 표준 메트릭 외에 정의하는 메트릭입니다. 사용자 지정 메트릭은 해당 리소스와 동일한 지역에서 단일 리소스에 대해 생성되어야 합니다. 다음 메서드를 사용하여 사용자 지정 메트릭을 만들 수 있습니다.
- Application Insights에서 모니터링되는 [애플리케이션에서 사용자 지정 메트릭을 정의합니다](../../azure-monitor/app/api-custom-events-metrics.md). 애플리케이션 메트릭의 표준 집합 이외의 것입니다.
- [Windows 진단 확장(WAD)](../../azure-monitor/platform/diagnostics-extension-overview.md)을 사용하여 Windows 가상 머신에서 사용자 지정 메트릭을 게시합니다.
- [InfluxData Telegraf 에이전트](https://www.influxdata.com/time-series-platform/telegraf/)를 사용하여 Linux 가상 머신에서 사용자 지정 메트릭을 게시합니다.
- 사용자 지정 메트릭 API를 사용하여 Azure 서비스에서 사용자 지정 메트릭을 작성합니다.

![메트릭 개요](media/data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>메트릭으로 무엇을 할 수 있나요?
메트릭을 사용하여 수행할 수 있는 작업은 다음과 같습니다.

- [메트릭 분석](metrics-charts.md)을 사용하여 수집된 메트릭을 분석하고 차트에 그립니다. 차트를 [Azure 대시보드](../../azure-portal/azure-portal-dashboards.md)에 고정하여 리소스(예: VM, 웹 사이트 또는 논리 앱)의 성능을 추적합니다.
- 메트릭이 임계값을 초과하면 알림을 보내거나 [자동화된 작업](action-groups.md)을 수행하는 [메트릭 경고 규칙](alerts-metric.md)을 구성합니다.
- [자동 크기 조정](autoscale-overview.md)을 사용하여 임계값을 초과하는 메트릭을 기준으로 리소스를 늘리거나 줄입니다.
- 메트릭을 로그에 라우팅하여 로그 데이터와 함께 메트릭 데이터를 분석하고 93일 이상 메트릭 값을 저장합니다. 
- 메트릭을 [이벤트 허브](stream-monitoring-data-event-hubs.md)로 스트리밍하여 [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) 또는 외부 시스템에 라우팅합니다.
- 규정 준수, 감사 또는 오프라인 보고의 목적으로 리소스의 성능 또는 상태 기록을 [보관](../../azure-monitor/learn/tutorial-archive-data.md)합니다.
- [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0) 또는 [REST API](rest-api-walkthrough.md)를 사용하여 명령줄 또는 사용자 지정 애플리케이션에서 메트릭 값에 액세스합니다.



### <a name="viewing-metrics"></a>메트릭 보기
Azure Monitor의 메트릭은 빠른 검색에 최적화된 시계열 데이터베이스에 저장되고 93일 동안 메트릭 값을 저장합니다. 장기 분석 및 추세 분석을 위해 로그에 메트릭을 복사합니다.

메트릭 데이터는 위에서 설명한 것처럼 다양한 방법으로 사용됩니다. [메트릭 분석](metrics-charts.md)을 사용하여 메트릭 저장소에서 데이터를 직접 분석하고 시간 경과에 따라 여러 가지 메트릭의 값을 기록합니다. 대화형으로 차트를 보거나 다른 시각화 요소를 사용하여 보려는 대시보드에 고정할 수 있습니다. [Azure 모니터링 REST API](rest-api-walkthrough.md)를 사용하여 메트릭을 검색할 수도 있습니다.

![메트릭 분석](media/data-collection/metrics-explorer.png)

## <a name="logs"></a>로그

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

로그에는 각 형식에 대해 다양한 속성 집합이 포함된 레코드로 구성된 다양한 데이터 형식이 포함됩니다. 로그는 메트릭과 같은 숫자 값을 포함할 수 있지만 일반적으로 자세한 설명이 포함된 텍스트 데이터를 포함합니다. 해당 구조가 다양하다는 점에서 메트릭과는 구별되며 정기적으로 수집되지 않는 경우가 많습니다.

로그 항목의 일반적인 유형은 산발적으로 수집되는 이벤트입니다. 이벤트는 애플리케이션 또는 서비스에서 만들어지며 대개 자체적으로 완전한 컨텍스트를 제공하기에 충분한 정보를 포함합니다. 예를 들어, 이벤트는 특정 리소스가 생성 또는 수정되었거나, 트래픽 증가에 대한 응답으로 새 호스트가 시작되었거나, 애플리케이션에서 오류가 검색되었음을 나타낼 수 있습니다.

로그는 복잡한 분석 및 시간 경과에 따른 추세 분석을 위해 다양한 원본의 데이터를 결합하는 데 특히 유용합니다. 데이터의 형식은 다양할 수 있으므로 애플리케이션은 필요한 구조를 사용하여 사용자 지정 로그를 만들 수 있습니다. 메트릭은 추세 분석 및 다른 데이터 분석을 위해 다른 모니터링 데이터와 결합하도록 로그에서 복제됩니다.



### <a name="sources-of-log-data"></a>로그 데이터의 원본
Azure Monitor는 Azure 내와 온-프레미스 리소스의 다양한 원본에서 로그 데이터를 수집할 수 있습니다. 로그 데이터의 원본에는 다음이 포함됩니다.

- 해당 작업에 대한 정보를 제공하는 해당 구성 및 상태에 대한 정보와 [진단 로그](diagnostic-logs-stream-log-store.md)를 포함하는 Azure 리소스의 [활동 로그](collect-activity-logs.md)
- 구성하는 [데이터 원본](data-sources.md)에 따라 게스트 운영 체제 및 애플리케이션에서 Azure Monitor로 원격 분석을 전송하는 [Windows](agent-windows.md) 및 [Linux](../learn/quick-collect-linux-computer.md) 가상 머신에 대한 에이전트
- [Application Insights](https://docs.microsoft.com/azure/application-insights/)에서 수집된 애플리케이션 데이터
- 특정 애플리케이션 또는 [모니터링 솔루션](../insights/solutions.md)의 서비스 또는 Container Insights, VM Insights 또는 Resource Group Insights와 같은 기능에 대한 정보를 제공하는 데이터
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)에서 수집된 보안 데이터
- Azure 리소스의 [메트릭](#metrics) 이를 통해 메트릭을 93일 이상 저장하고 다른 로그 데이터와 함께 분석할 수 있습니다.
- [Azure Storage](azure-storage-iis-table.md)에 작성된 원격 분석
- [HTTP 데이터 수집기 API](data-collector-api.md)를 사용하는 REST API 클라이언트 또는 [Azure Logic App](https://docs.microsoft.com/azure/logic-apps/) 워크플로의 사용자 지정 데이터

![로그 개요](media/data-collection/logs-overview.png)

### <a name="what-can-you-do-with-logs"></a>로그로 무엇을 할 수 있나요?
로그를 사용하여 수행할 수 있는 작업은 다음과 같습니다.

- Azure Portal에서 [Log Analytics](../log-query/get-started-portal.md)를 사용하여 로그 데이터를 분석하는 쿼리를 작성합니다. 테이블 또는 차트로 렌더링된 결과를 [Azure 대시보드](../../azure-portal/azure-portal-dashboards.md)에 고정합니다.
- 쿼리의 결과가 특정 결과와 일치할 때 알림을 보내거나 [자동화된 작업](action-groups.md)을 수행하는 [로그 경고 규칙](alerts-log.md)을 구성합니다.
- [Logic Apps](~/articles/logic-apps/index.yml)를 사용하여 로그 데이터를 기준으로 워크플로를 작성합니다.
- 쿼리의 결과를 [Power BI](powerbi.md)로 내보내 서로 다른 시각화를 사용하고 Azure 외부의 사용자와 공유합니다.
- 명령줄 또는 사용자 지정 애플리케이션에서 [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/?view=azurermps-6.8.1) 또는 [REST API](https://dev.loganalytics.io/)를 사용하여 메트릭 값에 액세스합니다.

### <a name="viewing-log-data"></a>로그 데이터 보기
Azure Monitor의 모든 로그 데이터는 [Kusto 쿼리 언어](../log-query/get-started-queries.md)로 작성된 [로그 쿼리](../log-query/log-query-overview.md)를 사용하여 검색되므로 수집된 데이터를 빠르게 검색, 통합 및 분석할 수 있습니다. [Log Analytics](../log-query/portals.md)를 사용하여 Azure Portal에서 쿼리를 기록하고 테스트합니다. 결과를 대화형으로 사용하거나 대시보드에 고정하여 다른 시각화를 통해 볼 수 있습니다. [Azure 모니터링 REST API](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md)를 사용하여 로그를 검색할 수도 있습니다.

> [!IMPORTANT]
> Application Insights의 데이터는 Azure Monitor의 다른 로그 데이터와 별도의 파티션에 저장됩니다. 다른 로그 데이터와 동일한 기능을 지원하지만 이 데이터에 액세스하려면 [Application Insights 콘솔](../app/analytics.md) 또는 [Application Insights API](https://dev.applicationinsights.io/)를 사용해야 합니다. [리소스 간 쿼리](../log-query/cross-workspace-query.md)를 사용하여 다른 로그 데이터와 함께 애플리케이션 데이터를 분석할 수 있습니다.

![로그](media/data-collection/logs.png)

## <a name="convert-monitoring-data"></a>모니터링 데이터 변환

### <a name="metrics-to-logs"></a>메트릭에서 로그로
로그 메트릭을 복사하여 Azure Monitor의 풍부한 쿼리 언어를 사용하여 다른 데이터 형식으로 복잡한 분석을 수행할 수 있습니다. 또한 메트릭보다 오랫동안 로그 데이터를 유지할 수 있으므로 시간 경과에 따른 추세를 수행할 수 있습니다. 기타 데이터를 통해 추세 및 분석에 대한 로그를 사용하는 동안 메트릭을 사용하여 거의 실시간 분석 및 경고를 지원합니다.

Azure 리소스에서 메트릭을 수집하기 위한 지침은 [Azure Monitor에서 사용할 Azure 서비스 로그 및 메트릭 수집](collect-azure-metrics-logs.md)에서 확인할 수 있습니다. Azure PaaS 리소스에서 리소스 메트릭을 수집하기 위한 지침은 [Azure Monitor로 Azure PaaS 리소스 메트릭의 수집 구성](collect-azurepass-posh.md)에서 확인할 수 있습니다.

### <a name="logs-to-metrics"></a>메트릭에서 로그로
위의 설명과 같이 메트릭은 로그보다 응답 속도가 빠르기 때문에 낮은 대기 시간과 낮은 비용으로 경고를 만들 수 있습니다. 메트릭에 적합한 상당한 양의 숫자 데이터가 로그로 저장되지만 Azure Monitor에 메트릭으로 저장되지는 않습니다. 일반적인 예로 에이전트 및 관리 솔루션에서 수집된 성능 데이터가 있습니다. 이러한 값 중 일부는 메트릭 탐색기를 사용하여 경고 및 분석에 사용할 수 있는 메트릭으로 복사할 수 있습니다.

이 기능에 대한 설명은 [Azure Monitor에서 로그 메트릭 경고 만들기](alerts-metric-logs.md)에서 확인할 수 있습니다. 지원되는 값 목록은 [Azure Monitor에서 지원되는 메트릭](metrics-supported.md#microsoftoperationalinsightsworkspaces)에서 확인할 수 있습니다.

## <a name="stream-data-to-external-systems"></a>외부 시스템으로 데이터 스트리밍
Azure에서 도구를 사용하여 모니터링 데이터를 분석하는 것 외에, SIEM(보안 정보 및 이벤트 관리) 제품과 같은 외부 도구로 전달하기 위한 요구 사항이 있을 수 있습니다. 이 전달은 일반적으로 [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)를 통해 모니터링된 리소스에서 직접 수행됩니다.

다양한 모니터링 데이터 유형에 대한 지침은 [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](stream-monitoring-data-event-hubs.md)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
