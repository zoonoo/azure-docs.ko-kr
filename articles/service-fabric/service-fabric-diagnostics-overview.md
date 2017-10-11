---
title: "Azure Service Fabric 모니터링 및 진단 개요 | Microsoft Docs"
description: "Azure Service Fabric 클러스터, 응용 프로그램 및 서비스에 대한 모니터링 및 진단에 대해 알아봅니다."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 88f4a23f89a1c8fd88db1df3a7ff03ae5df64c0f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2017
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Azure Service Fabric 모니터링 및 진단

모니터링 및 진단은 모든 환경에서 응용 프로그램 및 서비스를 개발, 테스트 및 배포하는 데 중요합니다. Service Fabric 솔루션은 로컬 개발 환경 또는 프로덕션 환경에서 응용 프로그램과 서비스가 예상대로 작동하는지 확인하는 데 도움이 되는 모니터링 및 진단을 계획하고 구현할 때 가장 효과적입니다.

모니터링 및 진단의 주요 목표는 다음과 같습니다.
* 하드웨어 및 인프라 문제 감지 및 진단
* 소프트웨어 및 앱 문제 감지, 서비스 가동 중지 시간 감소
* 리소스 사용량 이해 및 적절한 작업 의사 결정 유도
* 응용 프로그램, 서비스 및 인프라 성능 최적화
* 비즈니스 통찰력 생성 및 향상 가능 영역 식별


모니터링 및 진단의 전체 워크플로는 다음 세 단계로 구성됩니다.

1. **이벤트 생성**: 인프라, 클러스터 및 응용 프로그램/서비스 수준의 이벤트(로그, 추적, 사용자 지정 이벤트)가 포함됩니다.
2. **이벤트 집계**: 생성된 이벤트를 표시하려면 먼저 수집하고 집계해야 합니다.
3. **분석**: 필요에 따라 분석하고 표시할 수 있도록 이벤트를 특정 형식으로 시각화하고 액세스할 수 있어야 합니다.

이 세 영역을 포괄하는 여러 제품을 사용할 수 있으며 각각에 대해 서로 다른 기술을 자유롭게 선택할 수 있습니다. 이에 따라 각 부분이 서로 연동되면서 클러스터에 종단 간 모니터링 솔루션을 제공하는지 확인하는 것이 중요합니다.

## <a name="event-generation"></a>이벤트 생성

모니터링 및 진단 워크플로의 첫 번째 단계는 이벤트 및 로그 생성입니다. 플랫폼 계층(클러스터, 컴퓨터 또는 Service Fabric 작업 포함) 또는 응용 프로그램 계층(앱에 추가된 계측 및 클러스터에 배포된 서비스)과 같은 두 수준에서 이러한 이벤트, 로그 및 추적을 생성할 수 있습니다. Service Fabric은 기본적으로 일부 계측을 제공하지만 각 수준의 이벤트를 사용자 지정할 수 있습니다.

제공되는 계측 및 계측을 추가하는 방법은 [플랫폼 수준 이벤트](service-fabric-diagnostics-event-generation-infra.md) 및 [응용 프로그램 수준 이벤트](service-fabric-diagnostics-event-generation-app.md)를 참조하세요.

사용하려는 로깅 공급자를 결정한 후에는 로그를 올바르게 집계하고 저장해야 합니다.

## <a name="event-aggregation"></a>이벤트 집계

응용 프로그램과 클러스터에서 생성되는 로그 및 이벤트를 수집하려면 일반적으로 [Azure 진단](service-fabric-diagnostics-event-aggregation-wad.md)(에이전트 기반 로그 수집과 비슷) 또는 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)(In-Process 로그 수집)를 사용하는 것이 좋습니다.

로그 원본과 대상의 집합이 자주 변경되지 않으며, 원본과 대상 간의 매핑이 단순하다면 Azure 진단 확장을 사용한 응용 프로그램 로그 수집이 Service Fabric 서비스에 좋은 옵션입니다. 그 이유는 Azure 진단 구성 작업이 Resource Manager 계층에서 발생하기 때문입니다. 따라서 구성을 대폭 변경하려면 클러스터를 업데이트/재배포해야 합니다. 또한 로그를 다양한 분석 플랫폼에서 액세스할 수 있고 좀 더 영구적으로 저장할 수 있는 위치에 저장하면 가장 잘 활용할 수 있습니다. 이는 EventFlow와 같은 옵션을 사용하는 것보다 파이프라인의 효율성이 떨어지는 것을 의미합니다.

[EventFlow](https://github.com/Azure/diagnostics-eventflow)를 사용하면 서비스에서 분석 및 시각화 플랫폼 및/또는 저장소로 직접 로그를 보낼 수 있습니다. 같은 용도로 다른 라이브러리(ILogger, Serilog 등)를 사용할 수도 있지만 EventFlow에는 Service Fabric 서비스를 지원하고 프로세스에서 로그 수집을 위해 특별히 설계된 장점이 있습니다. 이 기능에는 몇 가지 잠재적 이점이 있습니다.

* 쉬운 구성 및 배포
    * 진단 데이터 수집 구성이 서비스 구성의 일부입니다. 응용 프로그램 나머지와 항상 "동기화" 상태로 쉽게 유지할 수 있습니다.
    * 응용 프로그램당 또는 서비스당 구성은 쉽게 달성할 수 있습니다.
    * EventFlow를 통해 데이터 대상을 구성하려면 적절한 NuGet 패키지를 추가하고 *eventFlowConfig.json* 파일을 변경하기만 하면 됩니다.
* 유연성
    * 응용 프로그램은 대상 데이터 저장소 시스템을 지원하는 클라이언트 라이브러리가 있는 한 데이터를 필요한 어느 곳이든 보낼 수 있습니다. 새로운 대상을 원하는 대로 추가할 수 있습니다.
    * 복잡한 캡처, 필터링 및 데이터 집계 규칙을 구현할 수 있습니다.
* 내부 응용 프로그램 데이터 및 컨텍스트에 대한 액세스
    * 응용 프로그램/서비스 프로세스 내에서 실행되는 진단 하위 시스템은 컨텍스트 정보와 함께 추적을 쉽게 보강할 수 있습니다.

한 가지 주의해야 할 점은 이 두 가지 옵션은 상호 배타적이지 않으므로 두 옵션을 각각 사용하여 유사한 작업을 수행할 수도 있지만 둘 다 설정할 수도 있습니다. 대부분의 경우 에이전트를 In-Process 컬렉션과 결합하면 보다 안정적인 모니터링 워크플로를 만들 수 있습니다. 플랫폼 수준 로그를 위해 Azure 진단 확장(에이전트)을 선택할 수 있는 반면 응용 프로그램 수준 로그를 위해 EventFlow(In-Process 컬렉션)를 사용할 수 있습니다. 가장 적합한 방법을 파악한 후에는 데이터를 표시하고 분석하는 방법을 결정해야 합니다.

## <a name="event-analysis"></a>이벤트 분석

모니터링 및 진단 데이터의 분석 및 시각화와 관련하여 시장에 몇 가지 훌륭한 플랫폼이 나와 있습니다. 그중에서 Service Fabric과의 통합성이 뛰어난 [OMS](service-fabric-diagnostics-event-analysis-oms.md) 및 [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)를 추천합니다. 그러나 [Elastic Stack](https://www.elastic.co/products)(특히 오프라인 환경에서 클러스터를 실행할 경우), [Splunk](https://www.splunk.com/) 또는 다른 플랫폼을 선택할 수 있습니다.

플랫폼을 선택할 때는 사용자 인터페이스 및 쿼리 옵션에 대한 친숙도, 데이터를 시각화하고 쉽게 읽을 수 있는 대시보드를 만드는 기능 및 자동 경고와 같이 모니터링을 향상시키기 위해 제공하는 추가 도구를 고려해야 합니다.

선택하는 플랫폼 외에도 Service Fabric 클러스터를 Azure 리소스로 설정하면 특정 성능 및 메트릭 모니터링에 유용한 Azure의 컴퓨터용 기본 모니터링 기능에 액세스할 수 있습니다.

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)를 사용하여 Service Fabric 클러스터의 기반이 되는 다양한 Azure 리소스를 모니터링할 수 있습니다. [가상 컴퓨터 확장 집합](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) 및 개별 [가상 컴퓨터](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)에 대한 일단의 메트릭이 자동으로 수집되어 Azure Portal에 표시됩니다. 수집된 정보를 보려면 Azure Portal에서 Service Fabric 클러스터가 포함된 리소스 그룹을 선택합니다. 그런 다음 보려는 가상 컴퓨터 확장 집합을 선택합니다. **모니터링** 섹션에서 **메트릭**을 선택하여 값의 그래프를 봅니다.

![수집된 메트릭 정보의 Azure Portal 보기](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

차트를 사용자 지정하려면 [Microsoft Azure의 메트릭](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)의 지침을 따릅니다. 또한 [Azure Monitor에서 Azure 서비스에 대한 알림 만들기](../monitoring-and-diagnostics/insights-alerts-portal.md)에서 설명한 대로 이러한 메트릭을 기반으로 하여 알림을 만들 수 있습니다. [Azure 메트릭 경고에 대한 웹후크 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md)에서 설명한 대로 웹후크를 사용하여 알림 서비스에 알림을 보낼 수 있습니다. Azure Monitor는 하나의 구독만 지원합니다. 여러 구독을 모니터링해야 하거나 추가 기능이 필요한 경우 Microsoft Operations Management Suite의 일부인 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/)는 온-프레미스 및 클라우드 기반 인프라에 포괄적인 IT 관리 솔루션을 제공합니다. Azure Monitor의 데이터를 Log Analytics로 직접 라우팅할 수 있으므로 전체 환경에 대한 메트릭과 로그를 한 곳에서 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

### <a name="watchdogs"></a>Watchdog

Watchdog은 서비스 전체에서 상태와 부하를 감시하고 상태 모델 계층 구조 상의 모든 항목에 대한 상태를 보고할 수 있는 별도의 서비스입니다. 이렇게 하면 단일 서비스 보기를 기반으로 하여 감지되지 않는 오류를 방지할 수 있습니다. Watchdog은 사용자 상호 작용 없이 교정 작업을 수행하는 코드(예: 특정 시간 간격으로 저장소의 로그 파일 정리)를 호스트하는 데 적합합니다. [여기](https://github.com/Azure-Samples/service-fabric-watchdog-service)에서 샘플 watchdog 서비스 구현을 찾을 수 있습니다.

[플랫폼 수준](service-fabric-diagnostics-event-generation-infra.md) 및 [응용 프로그램 수준](service-fabric-diagnostics-event-generation-app.md)에서 이벤트 및 로그를 생성하는 방법에 대해 알아봅니다.