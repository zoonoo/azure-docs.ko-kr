---
title: Azure Monitor의 메트릭 | Microsoft Docs
description: 거의 실시간 시나리오를 지원할 수 있는 경량 모니터링 데이터를 Azure Monitor의 메트릭에 대해 설명 합니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: ea95b91d57255db8f638e600d57a98db314cd80f
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113532"
---
# <a name="metrics-in-azure-monitor"></a>Azure Monitor의 메트릭

> [!NOTE]
> Azure Monitor 데이터 플랫폼은 다음과 같은 두 가지 기본 데이터 형식에 기반 합니다. 메트릭 및 로그. 이 문서에서는 메트릭을 설명 합니다. 로그 및 [Azure Monitor](data-platform.md) 에 대 한 자세한 내용은 [Azure Monitor의 로그](data-platform-logs.md) 를 참조 하십시오.

Azure Monitor 메트릭은 거의 실시간 시나리오를 지원할 수 있으며, 특히 문제를 경고 하 고 신속 하 게 검색 하는 데 유용 합니다. 이 문서에서는 메트릭이 구조화 되는 방법, 수행할 수 있는 작업 및 메트릭에 데이터를 저장 하는 다양 한 데이터 원본을 식별 하는 방법을 설명 합니다.

## <a name="what-are-metrics"></a>메트릭이 무엇인가요?
메트릭은 특정 시간에 시스템의 일부 측면을 설명하는 숫자 값입니다. 메트릭은 정기적으로 수집 되며, 자주 샘플링 될 수 있고 비교적 간단한 논리를 사용 하 여 경고를 신속 하 게 발생 시킬 수 있기 때문에 경고에 유용 합니다.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure Monitor 메트릭을 사용 하 여 무엇을 할 수 있나요?
다음 표에서는 Azure Monitor에서 메트릭 데이터를 사용할 수 있는 여러 가지 방법을 보여 줍니다.

|  |  |
|:---|:---|
| 분석 | [메트릭 탐색기](metrics-charts.md) 를 사용 하 여 차트에서 수집 된 메트릭을 분석 하 고 다른 리소스의 메트릭을 비교할 수 있습니다. |
| 시각화 | 메트릭 탐색기에서 [Azure 대시보드에](../learn/tutorial-app-dashboards.md)차트를 고정 합니다.<br>대화형 보고서에서 여러 데이터 집합과 결합할 [통합 문서](../app/usage-workbooks.md) 를 만듭니다. 쿼리 결과를 [Grafana](grafana-plugin.md) 로 내보내 해당 일점 탑승를 활용 하 고 다른 데이터 원본과 결합 합니다. |
| 알림 | 메트릭 값이 임계값을 초과 하는 경우 알림을 보내거나 [자동화 된 작업](action-groups.md) 을 수행 하는 [메트릭 경고 규칙](alerts-metric.md) 을 구성 합니다. |
| 자동화 |  임계값을 초과 하는 메트릭 값을 기준으로 리소스를 늘리거나 줄이려면 [자동 크기 조정을](autoscale-overview.md) 사용 합니다. |
| 내보내기 | [로그에 메트릭을 라우트](diagnostic-logs-stream-log-store.md) 하 여 Azure Monitor 메트릭의 데이터를 Azure Monitor 로그의 데이터와 함께 분석 하 고 메트릭 값을 93 일 보다 오래 저장 합니다.<br>[이벤트 허브](stream-monitoring-data-event-hubs.md) 로 메트릭을 스트리밍하려면 외부 시스템으로 라우팅합니다. |
| 검색 | [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights) 을 사용 하 여 명령줄에서 메트릭 값 액세스<br>[REST API](rest-api-walkthrough.md)를 사용 하 여 사용자 지정 응용 프로그램에서 메트릭 값에 액세스 합니다.<br>[CLI](/cli/azure/monitor/metrics)를 사용 하 여 명령줄에서 메트릭 값에 액세스 합니다. |
| 보관 | 규정 준수, 감사 또는 오프라인 보고의 목적으로 리소스의 성능 또는 상태 기록을 [보관](..//learn/tutorial-archive-data.md)합니다. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Azure Monitor 메트릭의 데이터는 어떻게 구성 되나요?
Azure Monitor 메트릭에 의해 수집 된 데이터는 타임 스탬프 데이터를 분석 하기 위해 최적화 된 시계열 데이터베이스에 저장 됩니다. 각 메트릭 값 집합은 다음 속성을 포함 하는 시계열입니다.

* 값이 수집 된 시간입니다.
* 값이 연결 된 리소스입니다.
* 메트릭에 대 한 범주 처럼 동작 하는 네임 스페이스입니다.
* 메트릭 이름
* 값 자체
* 일부 메트릭에는 [다차원 메트릭에](#multi-dimensional-metrics)설명 된 대로 여러 차원이 있을 수 있습니다. 사용자 지정 메트릭에는 최대 10개의 차원이 있을 수 있습니다.

## <a name="multi-dimensional-metrics"></a>다차원 메트릭
메트릭 데이터에 대 한 문제 중 하나는 수집 된 값에 대 한 컨텍스트를 제공 하기 위해 제한 된 정보를 포함 하는 경우가 많습니다. Azure Monitor는 다차원 메트릭에 대해 이러한 문제를 해결 합니다. 메트릭의 차원은 메트릭 값을 설명하도록 추가 데이터를 전송하는 이름 값 쌍입니다. 예를 들어 _사용 가능한 메트릭 디스크 공간_ 에는 값이 _C:_ , _D:_ 인 _drive_ 라는 차원이 있을 수 있습니다 .이 경우 모든 드라이브 또는 각 드라이브에서 사용 가능한 디스크 공간을 개별적으로 볼 수 있습니다.

아래 예제에서는 _네트워크 처리량_이라는 가상의 메트릭에 대한 두 개의 데이터 세트를 보여줍니다. 첫 번째 데이터 세트에는 차원이 없습니다. 두 번째 데이터 세트에는 두 가지 차원, 즉 _IP 주소_와 _방향_(Direction)의 값이 표시됩니다.

### <a name="network-throughput"></a>네트워크 처리량

| timestamp     | 메트릭 값 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8Kbps |
| 8/9/2017 8:15 | 1,141.4Kbps |
| 8/9/2017 8:16 | 1,110.2Kbps |

이 비차원 메트릭은 "지정된 시간에 내 네트워크 처리량은 무엇이었나요?"와 같은 기본적인 질문에만 응답할 수 있습니다.

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>네트워크 처리량 +2 차원("IP" 및 "방향")

| timestamp     | 차원 "IP"   | 차원 "방향" | 메트릭 값|
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
[메트릭 탐색기](metrics-charts.md) 를 사용 하 여 메트릭 데이터베이스의 데이터를 대화형으로 분석 하 고 시간 경과에 따라 여러 메트릭의 값을 차트로 볼 수 있습니다. 차트를 대시보드에 고정 하 여 다른 시각화를 사용 하 여 볼 수 있습니다. [Azure 모니터링 REST API](rest-api-walkthrough.md)를 사용하여 메트릭을 검색할 수도 있습니다.

![메트릭 탐색기](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure Monitor 메트릭의 원본
Azure Monitor에서 수집되는 메트릭의 세 가지 기본 원본이 있습니다. 이러한 메트릭은 Azure Monitor 메트릭 데이터베이스에서 수집 되 고 나면 원본에 관계 없이 함께 평가할 수 있습니다.

**플랫폼 메트릭**은 Azure 리소스에서 생성되고 해당 상태 및 성능에 대한 가시성을 제공합니다. 각 리소스 유형은 필요한 구성 없이 [고유 메트릭 집합](metrics-supported.md)을 만듭니다. 플랫폼 메트릭은 메트릭 정의에 달리 지정 되지 않는 한 1 분 간격으로 Azure 리소스에서 수집 됩니다. 

**게스트 OS 메트릭은** 가상 컴퓨터의 게스트 운영 체제에서 수집 됩니다. [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/)를 사용 하 여 [WAD (windows 진단 확장)](../platform/diagnostics-extension-overview.md) 및 Linux 가상 머신에 대 한 windows 가상 머신에 대 한 게스트 OS 메트릭을 사용 하도록 설정 합니다.

**애플리케이션 메트릭**은 모니터링된 애플리케이션에 대한 Application Insights에 의해 생성되며 성능 문제를 감지하고 애플리케이션이 사용되는 추세를 추적하는 데 도움이 됩니다. _서버 응답 시간_ 및 _브라우저 예외_와 같은 값을 포함합니다.

**사용자 지정 메트릭은** 자동으로 사용할 수 있는 표준 메트릭에 더하여 정의한 메트릭입니다. Application Insights에서 모니터링 하는 [응용 프로그램에서](../app/api-custom-events-metrics.md) 사용자 지정 메트릭을 정의 하거나 [사용자 지정 메트릭 API](metrics-store-custom-rest-api.md)를 사용 하 여 Azure 서비스에 대 한 사용자 지정 메트릭을 만들 수 있습니다.

## <a name="retention-of-metrics"></a>메트릭 보존
Azure의 대부분 리소스에 대해 메트릭은 93 일 동안 저장 됩니다. 몇 가지 예외가 있습니다.

**게스트 OS 메트릭**
-   **클래식 게스트 OS 메트릭**. 이러한 카운터는 [Windows 진단 확장 (WAD)](../platform/diagnostics-extension-overview.md) 또는 [Linux 진단 확장 (꼬마)](../../virtual-machines/extensions/diagnostics-linux.md) 을 통해 수집 되 고 Azure storage 계정으로 라우팅되는 성능 카운터입니다. 이러한 메트릭에 대 한 보존 기간은 14 일입니다.
-   **Azure Monitor 메트릭으로 전송 되는 게스트 OS 메트릭입니다**. 이러한 카운터는 Windows 진단 확장 (WAD)을 통해 수집 되 고 [Azure Monitor 싱크로](diagnostics-extension-overview.md#data-storage)전송 되거나 Linux 컴퓨터의 [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/) 를 통해 전송 되는 성능 카운터입니다. 이러한 메트릭에 대 한 보존은 93 일입니다.
-   **Log Analytics 에이전트가 수집 하는 게스트 OS 메트릭입니다**. 이러한 카운터는 Log Analytics 에이전트가 수집 하 여 Log Analytics 작업 영역으로 전송 하는 성능 카운터입니다. 이러한 메트릭에 대 한 보존은 31 일 이며 최대 2 년까지 연장할 수 있습니다.

**로그 기반 메트릭을 Application Insights**합니다. 
- 장면 뒤에서 [로그 기반 메트릭은](../app/pre-aggregated-metrics-log-metrics.md) 로그 쿼리로 변환 됩니다. 해당 보존은 기본 로그의 이벤트 보존과 일치 합니다. Application Insights 리소스의 경우 로그는 90 일 동안 저장 됩니다.


> [!NOTE]
> 장기적으로 추세를 위해 [Azure Monitor 리소스에 대 한 플랫폼 메트릭을 Log Analytics 작업 영역으로 보낼](diagnostic-logs-stream-log-store.md) 수 있습니다.





## <a name="next-steps"></a>다음 단계

- [Azure Monitor data platform](data-platform.md)에 대해 자세히 알아보세요.
- [Azure Monitor의 로그 데이터](data-platform-logs.md)에 대해 알아봅니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
