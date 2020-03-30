---
title: Azure 모니터의 메트릭 | 마이크로 소프트 문서
description: 거의 실시간 시나리오를 지원할 수 있는 경량 모니터링 데이터인 Azure Monitor의 메트릭을 설명합니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 80bbf83da17d833c4f8bb1abac9610d70e9a23cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274828"
---
# <a name="metrics-in-azure-monitor"></a>Azure Monitor의 메트릭

> [!NOTE]
> Azure Monitor 데이터 플랫폼은 메트릭 및 로그라는 두 가지 기본 데이터 유형을 기반으로 합니다. 이 문서에서는 메트릭에 대해 설명합니다. 로그에 대한 자세한 설명은 [Azure 모니터의 로그](data-platform-logs.md) 및 [Azure Monitor 데이터 플랫폼을](data-platform.md) 참조하여 두 가지를 비교합니다.

Azure Monitor의 메트릭은 가볍고 거의 실시간 시나리오를 지원할 수 있으므로 문제를 경고하고 빠르게 검색하는 데 특히 유용합니다. 이 문서에서는 메트릭의 구조화 방법, 메트릭을 사용하여 수행할 수 있는 작업을 설명하고 메트릭에 데이터를 저장하는 다양한 데이터 원본을 식별합니다.

## <a name="what-are-metrics"></a>메트릭이 무엇인가요?
메트릭은 특정 시간에 시스템의 일부 측면을 설명하는 숫자 값입니다. 메트릭은 정기적으로 수집되며 자주 샘플링할 수 있으며 비교적 간단한 논리로 경고를 신속하게 발생시킬 수 있으므로 경고에 유용합니다.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure 모니터 메트릭으로 무엇을 할 수 있습니까?
다음 표에는 Azure Monitor에서 메트릭 데이터를 사용할 수 있는 여러 가지 방법이 나열되어 있습니다.

|  |  |
|:---|:---|
| 분석 | [메트릭 탐색기를](metrics-charts.md) 사용하여 차트에서 수집된 메트릭을 분석하고 다양한 리소스의 메트릭을 비교합니다. |
| 시각화 | 메트릭 탐색기에서 [Azure 대시보드에](../learn/tutorial-app-dashboards.md)차트를 고정합니다.<br>대화형 보고서의 여러 데이터 집합과 결합할 [통합 문서를](../app/usage-workbooks.md) 만듭니다. 쿼리 결과를 [Grafana로](grafana-plugin.md) 내보내 대시보드를 활용하고 다른 데이터 원본과 결합합니다. |
| 경고 | 메트릭 [값이](alerts-metric.md) 임계값을 초과할 때 알림을 보내거나 [자동화된 작업을](action-groups.md) 취하는 메트릭 경고 규칙을 구성합니다. |
| 자동화 |  [자동 조정을](autoscale-overview.md) 사용하여 임계값을 초과하는 메트릭 값을 기반으로 리소스를 늘리거나 줄일 수 있습니다. |
| 내보내기 | [메트릭을 로그로 라우팅하여](resource-logs-collect-storage.md) Azure 모니터 로그의 데이터와 함께 Azure Monitor 메트릭의 데이터를 분석하고 93일 이상 메트릭 값을 저장합니다.<br>메트릭을 이벤트 [허브로](stream-monitoring-data-event-hubs.md) 스트리밍하여 외부 시스템으로 라우팅합니다. |
| 장치 | [PowerShell cmdlet을](https://docs.microsoft.com/powershell/module/az.applicationinsights) 사용하여 명령줄에서 메트릭 값에 액세스<br>[REST API를](rest-api-walkthrough.md)사용하여 사용자 지정 응용 프로그램에서 메트릭 값에 액세스합니다.<br>[CLI를](/cli/azure/monitor/metrics)사용하여 명령줄에서 메트릭 값에 액세스합니다. |
| 보관 | 규정 준수, 감사 또는 오프라인 보고의 목적으로 리소스의 성능 또는 상태 기록을 [보관](..//learn/tutorial-archive-data.md)합니다. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Azure 모니터 메트릭의 데이터는 어떻게 구성됩니까?
Azure Monitor 메트릭에서 수집한 데이터는 타임스탬프가 찍힌 데이터를 분석하는 데 최적화된 타임시리즈 데이터베이스에 저장됩니다. 메트릭 값의 각 집합은 다음과 같은 속성이 있는 열렬입니다.

* 값이 수집된 시간
* 값이 연관된 리소스
* 메트릭의 범주처럼 작동하는 네임스페이스
* 메트릭 이름
* 값 자체
* 일부 메트릭에는 [다차원 메트릭에](#multi-dimensional-metrics)설명된 대로 여러 차원이 있을 수 있습니다. 사용자 지정 메트릭에는 최대 10개의 차원이 있을 수 있습니다.

## <a name="multi-dimensional-metrics"></a>다차원 메트릭
메트릭 데이터의 과제 중 하나는 수집된 값에 대한 컨텍스트를 제공하기 위해 정보가 제한된 경우가 많다는 것입니다. Azure Monitor는 다차원 메트릭을 통해 이 문제를 해결합니다. 메트릭의 차원은 메트릭 값을 설명하도록 추가 데이터를 전송하는 이름 값 쌍입니다. 예를 들어 _사용 가능한 디스크 공간메트릭에는_ _C:_: _D:_ 값이 있는 _드라이브라는_ 차원이 있을 수 있으며, 이를 통해 모든 드라이브에서 사용 가능한 디스크 공간을 볼 수 있습니다.

아래 예제에서는 _네트워크 처리량_이라는 가상의 메트릭에 대한 두 개의 데이터 세트를 보여줍니다. 첫 번째 데이터 세트에는 차원이 없습니다. 두 번째 데이터 집합은 _IP 주소와_ _방향이라는_두 차원의 값을 표시합니다.

### <a name="network-throughput"></a>네트워크 처리량

| 타임스탬프     | 메트릭 값 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8Kbps |
| 8/9/2017 8:15 | 1,141.4Kbps |
| 8/9/2017 8:16 | 1,110.2Kbps |

이 비차원 메트릭은 "지정된 시간에 내 네트워크 처리량은 무엇이었나요?"와 같은 기본적인 질문에만 응답할 수 있습니다.

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>네트워크 처리량 +2 차원("IP" 및 "방향")

| 타임스탬프     | 차원 "IP"   | 차원 "방향" | 메트릭 값|
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

## <a name="interacting-with-azure-monitor-metrics"></a>Azure Monitor 메트릭과 상호 작용
[메트릭 탐색기를](metrics-charts.md) 사용하여 메트릭 데이터베이스의 데이터를 대화식으로 분석하고 시간에 따라 여러 메트릭의 값을 차트로 차트화할 수 있습니다. 차트를 대시보드에 고정하여 다른 시각화로 볼 수 있습니다. [Azure 모니터링 REST API](rest-api-walkthrough.md)를 사용하여 메트릭을 검색할 수도 있습니다.

![메트릭 탐색기](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure 모니터 메트릭의 소스
Azure Monitor에서 수집되는 메트릭의 세 가지 기본 원본이 있습니다. 이러한 메트릭이 Azure Monitor 메트릭 데이터베이스에 수집되면 원본에 관계없이 함께 평가할 수 있습니다.

**플랫폼 메트릭**은 Azure 리소스에서 생성되고 해당 상태 및 성능에 대한 가시성을 제공합니다. 각 리소스 유형은 필요한 구성 없이 [고유 메트릭 집합](metrics-supported.md)을 만듭니다. 플랫폼 메트릭은 메트릭 정의에 달리 명시되지 않는 한 Azure 리소스에서 1분 빈도로 수집됩니다. 

**게스트 OS 메트릭은** 가상 시스템의 게스트 운영 체제에서 수집됩니다. [Windows 진단 확장(WAD)을](../platform/diagnostics-extension-overview.md) 사용하는 Windows 가상 컴퓨터및 [InfluxData Telegraf 에이전트가](https://www.influxdata.com/time-series-platform/telegraf/)있는 Linux 가상 컴퓨터에 대한 게스트 OS 메트릭을 활성화합니다.

**애플리케이션 메트릭**은 모니터링된 애플리케이션에 대한 Application Insights에 의해 생성되며 성능 문제를 감지하고 애플리케이션이 사용되는 추세를 추적하는 데 도움이 됩니다. _서버 응답 시간_ 및 _브라우저 예외_와 같은 값을 포함합니다.

**맞춤 측정항목은** 자동으로 사용할 수 있는 표준 측정항목 외에 정의한 측정항목입니다. 응용 프로그램에서 모니터링되는 [사용자 지정 메트릭을 응용](../app/api-custom-events-metrics.md) 프로그램에서 정의하거나 사용자 지정 메트릭 API를 사용하여 Azure 서비스에 대한 사용자 지정 메트릭을 만들 수 [있습니다.](metrics-store-custom-rest-api.md)

## <a name="retention-of-metrics"></a>메트릭 보존
Azure의 대부분의 리소스에서 메트릭은 93일 동안 저장됩니다. 몇 가지 예외가 있습니다.

**게스트 OS 측정항목**
-   **클래식 게스트 OS 메트릭 .** 이러한 성능 카운터는 [WINDOWS 진단 확장(WAD)](../platform/diagnostics-extension-overview.md) 또는 [LAD(Linux 진단 확장)에서](../../virtual-machines/extensions/diagnostics-linux.md) 수집하여 Azure 저장소 계정으로 라우팅됩니다. 이러한 측정항목에 대한 보존기간은 14일입니다.
-   **Azure 모니터 메트릭으로 전송된 게스트 OS 메트릭입니다.** 이러한 성능 카운터는 [WINDOWS 진단 확장(WAD)에서](diagnostics-extension-overview.md) 수집하여 [Azure 모니터 데이터 싱크또는](diagnostics-extension-overview.md#data-destinations)Linux 컴퓨터의 [InfluxData Telegraf 에이전트를](https://www.influxdata.com/time-series-platform/telegraf/) 통해 전송됩니다. 이러한 측정항목에 대한 보존기간은 93일입니다.
-   **로그 분석 에이전트에서 수집한 게스트 OS 메트릭입니다.** 로그 분석 에이전트가 수집하고 로그 분석 작업 영역으로 전송하는 성능 카운터입니다. 이러한 측정항목에 대한 보존기간은 31일이며 최대 2년까지 연장할 수 있습니다.

**응용 프로그램 인사이트 로그 기반 메트릭**. 
- 장면 뒤에 [로그 기반 메트릭은](../app/pre-aggregated-metrics-log-metrics.md) 로그 쿼리로 변환됩니다. 보존은 기본 로그의 이벤트 보존과 일치합니다. Application Insights 리소스의 경우 로그는 90일 동안 저장됩니다.


> [!NOTE]
> Azure [Monitor 리소스에 대한 플랫폼 메트릭을](resource-logs-collect-storage.md) 장기 추세를 위해 Log Analytics 작업 영역으로 보낼 수 있습니다.





## <a name="next-steps"></a>다음 단계

- Azure 모니터 [데이터 플랫폼에](data-platform.md)대해 자세히 알아봅니다.
- Azure [모니터에서 로그 데이터에](data-platform-logs.md)대해 알아봅니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
