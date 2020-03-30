---
title: Azure 모니터 데이터 플랫폼 | 마이크로 소프트 문서
description: Azure Monitor에서 수집된 데이터 모니터링은 간단한 메트릭으로 구분되며 고급 분석에 사용되는 실시간 시나리오 및 로그를 지원할 수 있습니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a1b5859341237c1b177ee8deaf636a67f4824948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666550"
---
# <a name="azure-monitor-data-platform"></a>Azure 모니터 데이터 플랫폼

클라우드 및 온-프레미스 서비스에 모두 의존하는 분산 응용 프로그램을 실행하는 오늘날의 복잡한 컴퓨팅 환경에서 관찰을 구현하려면 분산 시스템의 모든 계층과 모든 구성 요소에서 운영 데이터를 수집해야 합니다. 이 데이터에 대한 심층적인 통찰력을 수행하고 이를 조직의 다양한 이해 관계자를 지원하기 위해 서로 다른 관점을 가진 단일 유리 창으로 통합할 수 있어야 합니다.

[Azure Monitor는](../overview.md) 다양한 소스의 데이터를 수집하고 분석, 시각화 및 경고에 사용할 수 있는 공통 데이터 플랫폼으로 집계합니다. 여러 원본의 데이터에 대한 일관된 환경을 제공하므로 모니터링되는 모든 리소스와 Azure Monitor에 데이터를 저장하는 다른 서비스의 데이터에 대해서도 심층적인 통찰력을 얻을 수 있습니다.


![Azure Monitor 개요](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure 모니터의 관찰 가능성 데이터
메트릭, 로그 및 분산 추적은 일반적으로 관찰의 세 가지 기둥이라고 합니다. 모니터링 도구가 모니터링 시스템을 충분히 관찰할 수 있도록 수집하고 분석해야 하는 다양한 종류의 데이터입니다. 모니터링되는 전체 리소스 집합에 걸쳐 여러 기둥의 데이터를 상호 연관시키고 데이터를 집계하여 관찰 가능성을 달성할 수 있습니다. Azure Monitor는 여러 원본의 데이터를 함께 저장하므로 공통 도구 집합을 사용하여 데이터를 상호 연관시키고 분석할 수 있습니다. 또한 다른 서비스에 대한 데이터를 호스팅하는 것 외에도 여러 Azure 구독 및 테넌트 간에 데이터를 상호 연관시다.

Azure 리소스는 상당한 양의 모니터링 데이터를 생성합니다. Azure Monitor는 다른 원본의 모니터링 데이터와 함께 이 데이터를 메트릭 또는 로그 플랫폼으로 통합합니다. 각각은 특정 모니터링 시나리오에 최적화되어 있으며 각각 Azure Monitor에서 서로 다른 기능을 지원합니다. 데이터 분석, 시각화 또는 경고와 같은 기능을 사용하면 가장 효율적이고 비용 효율적인 방식으로 필요한 시나리오를 구현할 수 있도록 차이점을 이해해야 합니다. [VM에 대한](../insights/vminsights-overview.md) [응용 프로그램 인사이트](../app/app-insights-overview.md) 또는 Azure Monitor와 같은 Azure 모니터의 인사이트는 두 가지 유형의 데이터 간의 차이점을 이해하지 않고도 특정 모니터링 시나리오에 집중할 수 있는 분석 도구를 가지고 있습니다. 


### <a name="metrics"></a>메트릭
[메트릭](data-platform-metrics.md)은 시간상 특정 지점에서 시스템의 일부 측면을 설명하는 숫자 값입니다. 정기적으로 수집되며 타임스탬프, 이름, 값 및 하나 이상의 정의 레이블로 식별됩니다. 메트릭은 다른 메트릭과 비교하여 다양한 알고리즘을 사용하여 집계하고 시간에 따라 추세를 분석할 수 있습니다. 

Azure Monitor의 메트릭은 타임스탬프가 찍힌 데이터를 분석하는 데 최적화된 타임시리즈 데이터베이스에 저장됩니다. 따라서 메트릭은 문제를 경고하고 신속하게 감지하는 데 특히 적합합니다. 시스템의 성능을 알 수 있지만 일반적으로 문제의 근본 원인을 식별하기 위해 로그와 결합해야 합니다.

메트릭은 Azure [메트릭 탐색기를](../platform/metrics-getting-started.md)사용 하 고 Azure 포털에서 대화형 분석에 사용할 수 있습니다. 다른 데이터와 함께 시각화를 위해 [Azure 대시보드에](../learn/tutorial-app-dashboards.md) 추가하고 거의 실시간 경고에 사용할 수 [있습니다.](alerts-metric.md)

Azure 모니터 의 메트릭에서 데이터 원본을 포함한 Azure [모니터 메트릭에](data-platform-metrics.md)대해 자세히 읽어보십시오.

### <a name="logs"></a>로그
[로그는](data-platform-logs.md) 시스템 내에서 발생한 이벤트입니다. 그들은 데이터의 다른 종류를 포함 할 수 있으며 타임 스탬프와 구조화 또는 자유 형식 텍스트 수 있습니다. 환경의 이벤트가 로그 항목을 생성할 때 산발적으로 생성될 수 있으며, 부하가 많은 시스템은 일반적으로 더 많은 로그 볼륨을 생성합니다.

Azure Monitor의 로그는 강력한 분석 엔진과 [풍부한 쿼리 언어를](/azure/kusto/query/)제공하는 Azure Data [Explorer를](/azure/data-explorer/) 기반으로 하는 로그 분석 작업 영역에 저장됩니다. 로그는 일반적으로 식별되는 문제의 완전한 컨텍스트를 제공하기에 충분한 정보를 제공하며 문제의 근본 사례를 식별하는 데 유용합니다.

> [!NOTE]
> Azure 모니터 로그와 Azure의 로그 데이터 원본을 구분하는 것이 중요합니다. 예를 들어 Azure의 구독 수준 이벤트는 Azure Monitor 메뉴에서 볼 수 있는 [활동 로그에](platform-logs-overview.md) 기록됩니다. 대부분의 리소스는 다른 위치로 전달할 수 있는 [리소스 로그에](platform-logs-overview.md) 운영 정보를 씁니다. Azure Monitor Logs는 활동 로그 및 리소스 로그를 다른 모니터링 데이터와 함께 수집하여 전체 리소스 집합에 대한 심층 분석을 제공하는 로그 데이터 플랫폼입니다.


 Azure 포털의 [Log Analytics를](../log-query/portals.md) 사용하여 [로그 쿼리를](../log-query/log-query-overview.md) 대화식으로 작업하거나 다른 데이터와 함께 시각화를 위해 [Azure 대시보드에](../learn/tutorial-app-dashboards.md) 결과를 추가할 수 있습니다. 일정 쿼리 의 결과에 따라 경고를 트리거하는 [로그 경고를](alerts-log.md) 만들 수도 있습니다.

Azure 모니터 의 로그에 있는 데이터 원본을 포함하여 [Azure 모니터 로그에](data-platform-logs.md)대해 자세히 알아보기.

### <a name="distributed-traces"></a>분산 트레이스
추적은 분산 시스템을 통해 사용자 요청을 따르는 일련의 관련 이벤트입니다. 응용 프로그램 코드의 동작과 다른 트랜잭션의 성능을 결정하는 데 사용할 수 있습니다. 로그는 분산 시스템의 개별 구성 요소에 의해 생성되는 경우가 많지만 추적은 전체 구성 요소 집합에서 응용 프로그램의 작업 및 성능을 측정합니다.

Azure 모니터의 분산 추적은 [응용 프로그램 인사이트 SDK를](../app/distributed-tracing.md)사용하여 활성화되고 추적 데이터는 응용 프로그램 인사이트에 의해 수집된 다른 응용 프로그램 로그 데이터와 함께 저장됩니다. 이렇게 하면 로그 쿼리, 대시보드 및 경고를 비롯한 다른 로그 데이터와 동일한 분석 도구에서 사용할 수 있습니다.

분산 추적이란 [무엇입니까?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Azure 모니터 메트릭 및 로그 비교

다음 표에서는 Azure 모니터의 메트릭 및 로그를 비교합니다.

| 특성  | 메트릭 | 로그 |
|:---|:---|:---|
| 이점 | 가볍고 경고와 같은 거의 실시간 시나리오가 가능합니다. 문제를 빠르게 감지하는 데 이상적입니다. | 풍부한 쿼리 언어로 분석되었습니다. 근본 원인을 심층 분석 및 식별하는 데 이상적입니다. |
| 데이터 | 숫자 값만 | 텍스트 또는 숫자 데이터 |
| 구조 | 샘플 시간, 모니터링 중인 리소스, 숫자 값을 포함한 표준 속성 집합입니다. 일부 메트릭에는 추가 정의를 위한 여러 차원이 포함됩니다. | 로그 유형에 따라 고유한 속성 집합입니다. |
| 컬렉션 | 일정한 간격으로 수집됩니다. | 이벤트가 레코드를 만들 수 있도록 트리거할 때 산발적으로 수집될 수 있습니다. |
| Azure Portal에서 보기 | 메트릭 탐색기 | Log Analytics |
| 데이터 원본에는 다음이 포함됩니다. | Azure 리소스에서 수집된 플랫폼 메트릭입니다.<br>애플리케이션 인사이트로 모니터링되는 응용 프로그램입니다.<br>응용 프로그램 또는 API에 의해 정의된 사용자 지정입니다. | 응용 프로그램 및 리소스 로그입니다.<br>모니터링 솔루션.<br>에이전트 및 VM 확장.<br>응용 프로그램 요청 및 예외.<br>Azure 보안 센터.<br>데이터 수집기 API. |

## <a name="collect-monitoring-data"></a>모니터링 데이터 수집
Azure Monitor의 다양한 [데이터 원본은](data-sources.md) 로그 분석 작업 영역(로그) 또는 Azure Monitor 메트릭 데이터베이스(메트릭) 또는 둘 다에 기록됩니다. 일부 원본은 이러한 데이터 저장소에 직접 쓰고 다른 소스는 Azure 저장소와 같은 다른 위치에 쓰고 로그 또는 메트릭을 채우기 위해 일부 구성이 필요할 수 있습니다. 

각 유형을 채우는 다양한 데이터 원본의 목록은 [Azure 모니터및](data-platform-metrics.md) [Azure 모니터의 메트릭](data-platform-logs.md) 및 로그를 참조하십시오.


## <a name="stream-data-to-external-systems"></a>외부 시스템으로 데이터 스트리밍
Azure에서 도구를 사용하여 모니터링 데이터를 분석하는 것 외에, SIEM(보안 정보 및 이벤트 관리) 제품과 같은 외부 도구로 전달하기 위한 요구 사항이 있을 수 있습니다. 이 전달은 일반적으로 [Azure Event Hubs](/azure/event-hubs/)를 통해 모니터링된 리소스에서 직접 수행됩니다. 일부 소스는 이벤트 허브로 직접 데이터를 전송하도록 구성할 수 있으며 논리 앱과 같은 다른 프로세스를 사용하여 필요한 데이터를 검색할 수 있습니다. 자세한 내용은 외부 도구에서 사용할 수 [있도록 Azure 모니터링 데이터 스트림을 이벤트 허브로 스트리밍을](stream-monitoring-data-event-hubs.md) 참조하세요.



## <a name="next-steps"></a>다음 단계

- [Azure 모니터의 메트릭에](data-platform-metrics.md)대해 자세히 알아보기.
- [Azure 모니터의 로그에](data-platform-logs.md)대해 자세히 알아보기.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
