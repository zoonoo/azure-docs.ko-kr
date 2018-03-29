---
title: Azure Service Fabric 모니터링 및 진단 개요 | Microsoft Docs
description: Azure Service Fabric 클러스터, 응용 프로그램 및 서비스에 대한 모니터링 및 진단에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: f784576547f0d85a825ad9dd107c6c84cd261092
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Azure Service Fabric 모니터링 및 진단

이 문서에서는 Service Fabric 클러스터에서 실행 중인 응용 프로그램에 대한 모니터링 및 진단 설정의 개요를 제공합니다. 모니터링 및 진단은 모든 클라우드 환경에서 워크로드를 개발, 테스트 및 배포하는 데 중요합니다. 모니터링을 사용하면 응용 프로그램 사용 현황, 리소스 사용률 및 전반적인 클러스터 상태를 추적할 수 있습니다. 이 정보를 사용하여 클러스터의 모든 문제를 진단하고 수정할 수 있으며 나중에 문제가 발생하지 않도록 방지할 수 있습니다. 

모니터링 및 진단의 주요 목표는 다음과 같습니다.
* 인프라 문제 감지 및 진단
* 응용 프로그램 문제 감지
* 리소스 사용량 이해
* 응용 프로그램, 서비스 및 인프라 성능 추적

Service Fabric 클러스터에서 모니터링 및 진단 데이터는 응용 프로그램, 플랫폼(클러스터) 및 인프라의 세 가지 수준에서 제공됩니다. 
* [응용 프로그램 수준](service-fabric-diagnostics-event-generation-app.md)에는 응용 프로그램의 성능 데이터 및 추가한 모든 사용자 지정 로깅이 포함됩니다. 응용 프로그램 모니터링 데이터는 응용 프로그램 자체의 AI(Application Insights)에서 종료되어야 합니다. AI SDK, EventFlow 또는 선택한 다른 파이프라인을 통해 제공될 수 있습니다.
* [플랫폼 수준](service-fabric-diagnostics-event-generation-infra.md)에는 클러스터 및 클러스터에서 실행 중인 응용 프로그램의 관리와 관련해서 클러스터에서 수행되는 작업의 이벤트가 포함됩니다. 플랫폼 모니터링 데이터는 OMS Log Analytics로 전송해야 하며, Service Fabric 분석 솔루션은 수신 이벤트를 시각화하는 데 도움이 됩니다. 이 데이터는 일반적으로 Windows 또는 Linux Azure 진단 확장을 통해 저장소 계정으로 전송되며, OMS Log Analytics는 여기서 데이터에 액세스합니다. 
* 인프라 수준은 [성능 모니터링](service-fabric-diagnostics-event-generation-perf.md)에 집중하며, 주요 메트릭과 성능 카운터를 통해 리소스 사용률과 부하를 확인합니다. 에이전트를 사용하여 성능 모니터링을 수행할 수 있습니다. 성능 데이터가 플랫폼 이벤트와 동일한 위치에서 종료되도록 OMS(Microsoft Monitoring) Agent를 사용하는 것이 좋습니다. 그러면 클러스터에서 변경 내용을 상호 연결함에 따라 진단 환경이 향상됩니다. 

![진단 개요 차트](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>모니터링 시나리오

이 섹션에서는 Service Fabric 클러스터 모니터링을 위한 주요 시나리오(응용 프로그램, 클러스터, 성능 및 상태 모니터링)에 대해 설명합니다. 각 시나리오에 대한 모니터링 의도 및 전반적인 접근 방법을 설명합니다. Azure 리소스에 대한 이러한 모니터링 권장 사항 및 기타 일반적인 모니터링 권장 사항에 대한 자세한 내용은 [모범 사례 - 모니터링 및 진단](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)을 참조하세요. 

또한 이러한 시나리오는 위에서 설명한 세 가지 수준의 모니터링 및 진단 데이터에 느슨하게 매핑되어 있습니다. 즉, 각 시나리오가 클러스터에서 적절하게 처리되려면 해당 수준에서 모니터링 및 진단 데이터가 수신되어야 합니다. 상태 모니터링 시나리오는 클러스터 및 클러스터에서 실행 중인 모든 항목에 걸쳐 있으므로 예외입니다.

## <a name="application-monitoring"></a>응용 프로그램 모니터링
응용 프로그램 모니터링은 빌드한 응용 프로그램의 기능 및 구성 요소가 사용되는 현황을 추적합니다. 응용 프로그램을 모니터링하여 사용자에게 영향을 주는 문제가 발견되었는지 확인하려고 합니다. 응용 프로그램 모니터링은 다음 측면에서 유용할 수 있습니다.
* 응용 프로그램 부하 및 사용자 트래픽 확인 - 사용자 수요를 충족하거나 응용 프로그램의 잠재적 병목 상태를 해결하기 위해 서비스를 확장해야 하나요?
* 클러스터에서 서비스 통신 및 원격 문제 식별
* 사용자가 응용 프로그램에서 수행 중인 작업 확인 - 응용 프로그램을 계측하면 이후의 기능 개발과 앱 오류에 대한 진단 향상에 도움이 될 수 있습니다.

Service Fabric의 응용 프로그램 수준 모니터링에는 AI(Application Insights)를 사용하는 것이 좋습니다. AI의 Service Fabric 통합에는 Visual Studio 및 Azure Portal용 도구 환경과 AI 대시보드 및 응용 프로그램 맵에서 Service Fabric 서비스 컨텍스트 및 원격 이해가 포함되므로 포괄적인 로깅 환경이 기본적으로 제공됩니다. AI를 사용하는 경우 많은 로그가 자동으로 생성 및 수집되지만 응용 프로그램에 사용자 지정 로깅을 추가하고, 나중에 문제를 처리할 수 있도록 보다 풍부한 진단 환경을 만들기 위해 제공되는 기능과 함께 사용자 지정 로깅을 AI에 표시하는 것이 좋습니다. Service Fabric과 함께 AI를 사용하는 방법에 대한 자세한 내용은 [Application Insights를 사용한 이벤트 분석](service-fabric-diagnostics-event-analysis-appinsights.md)을 참조하세요. 

응용 프로그램을 모니터링하도록 코드 계측을 시작하려면 [응용 프로그램 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-app.md)으로 이동합니다. 

### <a name="monitoring-application-availability"></a>응용 프로그램 가용성 모니터링
클러스터의 모든 것이 예상대로 실행되는 것처럼 보이고 정상으로 보고되지만 응용 프로그램에 액세스할 수 없거나 연결할 수 없는 경우가 있습니다. 자주 발생하지는 않지만 최대한 빨리 완화할 수 있도록 이러한 경우가 발생할 때 알고 있어야 합니다. 대체로 가용성 모니터링은 시스템 구성 요소의 가용성을 추적하여 시스템의 전반적인 “가동 시간”을 이해하는 것과 관련이 있습니다. 클러스터에서는 응용 프로그램 관점의 가용성에 집중합니다. 플랫폼은 응용 프로그램 수명 주기 관리 시나리오에서 가동 중단 시간이 발생하지 않도록 합니다. 하지만 클러스터의 다양한 문제가 응용 프로그램 가동 시간에 영향을 줄 수 있으며, 일반적으로 응용 프로그램 소유자라면 즉시 알고 싶을 것입니다. 다른 모든 응용 프로그램과 함께 Watchdog를 클러스터에 배포하는 것이 좋습니다. Watchdog의 용도는 설정된 시간 간격마다 응용 프로그램에 적합한 끝점을 확인하고 액세스할 수 있음을 보고하는 것입니다. 지정된 시간 간격마다 끝점에 Ping을 수행하고 보고서를 반환하는 외부 서비스를 이 용도로 사용할 수도 있습니다.

## <a name="cluster-monitoring"></a>클러스터 모니터링
Service Fabric 클러스터 모니터링은 플랫폼 및 플랫폼에서 실행 중인 모든 워크로드가 의도한 대로 실행되고 있는지 확인하는 데 중요합니다. Service Fabric의 목표 중 하나는 하드웨어 오류가 발생해도 응용 프로그램이 계속 실행되도록 유지하는 것입니다. 이는 플랫폼의 시스템 서비스가 인프라 문제를 감지하고 워크로드를 클러스터의 다른 노드로 빠르게 장애 조치(failover)할 수 있기 때문에 가능합니다. 그러나 이러한 특정 상황에서 시스템 서비스 자체에 문제가 있다면 어떻게 될까요? 또는 워크로드를 이동할 때 서비스 배치 규칙에 위배되면 어떻게 될까요? 클러스터를 모니터링하면 클러스터에서 발생하는 활동에 대한 정보가 계속 제공되므로 효과적으로 문제를 진단하고 해결하는 데 도움이 됩니다. 확인해야 할 몇 가지 주요 사항은 다음과 같습니다.
* 응용 프로그램 배치 및 클러스터의 작업 부하 분산 측면에서 Service Fabric이 예상대로 동작하고 있나요? 
* 클러스터 구성을 수정하기 위해 수행 중인 작업이 예상대로 승인 및 적용되고 있나요? 이는 클러스터를 확장할 때 특히 관련이 있습니다.
* Service Fabric이 사용자 데이터와 클러스터 내부의 서비스-서비스 통신을 올바르게 처리하고 있나요?

Service Fabric은 운영 채널과 데이터 및 메시징 채널을 통해 포괄적인 이벤트 집합을 기본적으로 제공합니다. Windows에서는 채널 선택에 사용되는 관련 `logLevelKeywordFilters` 집합이 있는 단일 ETW 공급자 형태로 제공됩니다. Linux에서는 모든 플랫폼 이벤트가 LTTng를 통해 수신되어 하나의 테이블에 배치되며, 필요에 따라 여기서 이벤트를 필터링할 수 있습니다. 

이러한 채널에는 클러스터 상태 이해에 도움이 되는 구조화된 조정 이벤트가 포함되어 있습니다. “진단”은 클러스터 생성 시 기본적으로 사용되므로, 나중에 쿼리할 수 있도록 이러한 채널의 이벤트가 전송되는 Azure Storage 테이블이 설정됩니다. 클러스터 모니터링에 대한 자세한 내용은 [플랫폼 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-infra.md)을 참조하세요. OMS Log Analytics를 사용하여 클러스터를 모니터링하는 것이 좋습니다. OMS Log Analytics는 Service Fabric 클러스터 모니터링을 위한 사용자 지정 대시보드를 제공하며 클러스터의 이벤트를 쿼리하고 경고를 설정할 수 있게 해주는 Service Fabric 특정 솔루션인 Service Fabric 분석을 제공합니다. 자세한 내용은 [OMS를 사용한 이벤트 분석](service-fabric-diagnostics-event-analysis-oms.md)을 참조하세요. 

### <a name="watchdogs"></a>Watchdog
일반적으로 Watchdog는 전체 서비스의 상태와 부하를 감시하고, 끝점에 Ping을 수행하고, 클러스터에 있는 모든 항목의 상태를 보고할 수 있는 별도의 서비스입니다. 이렇게 하면 단일 서비스 보기를 기반으로 하여 감지되지 않는 오류를 방지할 수 있습니다. Watchdog은 사용자 상호 작용 없이 교정 작업을 수행하는 코드(예: 특정 시간 간격으로 저장소의 로그 파일 정리)를 호스트하는 데 적합합니다. [여기](https://github.com/Azure-Samples/service-fabric-watchdog-service)에서 샘플 watchdog 서비스 구현을 찾을 수 있습니다.

## <a name="performance-monitoring"></a>성능 모니터링
기본 인프라 모니터링은 클러스터 상태 및 리소스 사용률을 이해하는 데 중요한 부분입니다. 시스템 성능 측정은 여러 요인에 따라 달라지며, 각 요인은 일반적으로 KPI(핵심 성과 지표)를 통해 측정됩니다. 클러스터의 노드에서 성능 카운터로 수집할 수 있는 메트릭에 Service Fabric 관련 KPI를 매핑할 수 있습니다.
이 KPI는 다음 측면에서 유용할 수 있습니다.
* 리소스 사용률 및 부하 이해 - 클러스터를 확장하거나 서비스 프로세스를 최적화하는 데 사용됩니다.
* 인프라 문제 예측 - 문제에 앞서 성능이 갑자기 변경(저하)되는 경우가 많으므로 네트워크 I/O, CPU 사용률 등의 KPI를 사용하여 인프라 문제를 예측하고 진단할 수 있습니다.

인프라 수준에서 수집해야 하는 성능 카운터 목록은 [성능 메트릭](service-fabric-diagnostics-event-generation-perf.md)에서 확인할 수 있습니다. 

응용 프로그램 수준 성능 모니터링을 위해 Service Fabric은 Reliable Services 및 Reliable Actors 프로그래밍 모델에 대한 일련의 성능 카운터를 제공합니다. 두 모델 중 하나를 사용하는 경우, 이러한 성능 카운터는 행위자가 올바르게 스핀 업/스핀 다운하고 있는지 또는 Reliable Services 요청이 빠르게 처리되고 있는지 확인하는 데 유용한 KPI를 제공할 수 있습니다. [Reliable Services 원격 모니터링](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) 및 [Reliable Actors 성능 모니터링](service-fabric-reliable-actors-diagnostics.md#performance-counters)을 참조하세요. 이외에도 Application Insights에는 응용 프로그램에서 구성된 경우 수집되는 일련의 성능 메트릭이 있습니다.

OMS 에이전트를 사용하여 적절한 성능 카운터를 수집하고 OMS Log Analytics에서 이러한 KPI를 볼 수 있습니다. Azure 진단 에이전트 확장(또는 다른 유사한 에이전트)을 사용하여 분석할 메트릭을 수집 및 저장할 수도 있습니다. 

## <a name="health-monitoring"></a>상태 모니터링
Service Fabric 플랫폼에는 클러스터의 엔터티 상태에 대한 확장 가능한 상태 보고를 제공하는 상태 모델이 포함되어 있습니다. 각 노드, 응용 프로그램, 서비스, 파티션, 복제본 또는 인스턴스에는 지속적으로 업데이트 가능한 상태가 있습니다. 시스템 상태는 “정상”, “경고” 또는 “오류”일 수 있습니다. 클러스터의 문제에 따라 각 엔터티에 대해 내보내지는 상태 보고서를 통해 상태가 변경됩니다. 엔터티 상태는 아래와 같이 SFX(Service Fabric Explorer)에서 언제든지 확인하거나 플랫폼의 상태 API를 통해 쿼리할 수 있습니다. 자체 상태 보고서를 추가하거나 상태 API를 사용하여 상태 보고서를 사용자 지정하고 엔터티 상태를 수정할 수도 있습니다. 상태 모델에 대한 자세한 내용은 [Service Fabric 상태 모니터링 소개](service-fabric-health-introduction.md)를 참조하세요.

![SFX 상태 대시보드](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

SFX에서 최신 상태 보고서를 볼 수 있는 것은 물론, 각 보고서가 이벤트로도 제공됩니다. 운영 채널을 통해 상태 이벤트를 수집([Azure 진단을 사용한 이벤트 집계](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) 참조)하고 나중에 경고 및 쿼리하기 위해 OMS Log Analytics에 저장할 수 있습니다. 이는 응용 프로그램 가용성에 영향을 줄 수 있는 문제 감지에 도움이 되므로 적절한 오류 시나리오에 대한 경고(OMS를 통한 사용자 지정 경고)를 설정하는 것이 좋습니다.

## <a name="monitoring-workflow"></a>모니터링 워크플로 

모니터링 및 진단의 전체 워크플로는 다음 세 단계로 구성됩니다.

1. **이벤트 생성**: 인프라, 플랫폼(클러스터) 및 응용 프로그램 수준의 이벤트(로그, 추적, 사용자 지정 이벤트)가 포함됩니다.
2. **이벤트 집계**: 이 단계는 실제로 이벤트가 Azure 진단 확장, EventFlow 등의 도구에서 종료되기 위한 파이프라인으로, 여기서 이벤트를 분석할 수 있습니다.
3. **분석**: 도구에서 시각화, 쿼리, 경고 등의 이벤트에 액세스할 수 있어야 분석이 가능합니다.

이 세 영역을 포괄하는 여러 제품을 사용할 수 있으며 각각에 대해 서로 다른 기술을 자유롭게 선택할 수 있습니다. 이에 따라 각 부분이 서로 연동되면서 클러스터에 종단 간 모니터링 솔루션을 제공하는지 확인하는 것이 중요합니다.

## <a name="event-generation"></a>이벤트 생성

모니터링 및 진단 워크플로의 첫 번째 단계는 이벤트 및 로그 데이터 생성을 설정하는 것입니다. 이러한 이벤트, 로그 및 성능 카운터는 응용 프로그램 수준(클러스터에 배포된 앱과 서비스에 추가되는 모든 계측), 플랫폼(Service Fabric 작업에 따라 클러스터에서 내보내지는 이벤트), 인프라 수준(각 노드의 성능 메트릭) 등 모두 세 가지 수준에서 내보내집니다. Service Fabric에서 몇 가지 기본 계측은 사용하도록 설정되지만, 각 수준에서 수집되는 진단 데이터를 사용자 지정할 수 있습니다. 

제공되는 계측 및 계측을 추가하는 방법은 [플랫폼 수준 이벤트](service-fabric-diagnostics-event-generation-infra.md) 및 [응용 프로그램 수준 이벤트](service-fabric-diagnostics-event-generation-app.md)를 참조하세요. 여기서 결정해야 할 주요 사항은 응용 프로그램 계측 방법입니다. .NET 응용 프로그램의 경우 ILogger를 사용하는 것이 좋지만 EventSource, Serilog 및 다른 유사한 라이브러리를 탐색할 수도 있습니다. Java의 경우 Log4j를 사용하는 것이 좋습니다. 이외에도 응용 프로그램 특성에 따라 사용할 수 있는 다른 여러 옵션이 있습니다. 특정 사용 사례에 가장 적합한 옵션을 살펴보거나 가장 사용하기 편한 옵션을 선택하세요. 

사용하려는 로깅 공급자를 결정한 후에는 로그를 올바르게 집계하고 저장해야 합니다.

## <a name="event-aggregation"></a>이벤트 집계

응용 프로그램과 클러스터에서 생성되는 로그 및 이벤트를 수집하려면 일반적으로 [Azure 진단 확장](service-fabric-diagnostics-event-aggregation-wad.md)(에이전트 기반 로그 수집과 유사) 또는 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)(In-Process 로그 수집)를 사용하는 것이 좋습니다. Application Insights를 사용하고 .NET 또는 Java로 개발 중인 경우 EventFlow 대신 Application Insights SDK를 사용하는 것이 좋습니다.

이러한 도구 중 하나만 사용하여 유사한 작업을 수행할 수도 있지만 대부분의 경우 Azure 진단 확장 에이전트를 In-process 컬렉션 파이프라인(AI SDK 또는 EventFlow)과 결합하는 것이 보다 안정적이고 포괄적인 모니터링 워크플로에 도움이 됩니다. Azure 진단 확장 에이전트는 플랫폼 수준 이벤트에 사용되는 반면, AI SDK 또는 EventFlow(In-process 컬렉션)는 응용 프로그램 수준 로그에 사용됩니다. 

이러한 도구 중 하나만 사용하려는 경우 다음 몇 가지 주요 사항에 유의하세요.
* 로그 원본 및 대상 집합이 자주 변경되지 않으며 원본과 대상 간의 매핑이 단순하다면 Azure 진단 확장을 사용하여 응용 프로그램 로그를 수집하는 것이 Service Fabric 서비스에 좋은 옵션입니다. 그 이유는 Azure 진단 구성 작업이 리소스 관리자 레이어에서 수행되므로 구성을 대폭 변경하는 경우 전체 클러스터를 업데이트해야 하기 때문입니다. 따라서 AI SDK 또는 EventFlow를 사용하는 것보다 효율성이 떨어지는 경우가 많습니다.
* EventFlow를 사용하면 서비스에서 분석 및 시각화 플랫폼 및/또는 저장소로 직접 로그를 보낼 수 있습니다. 같은 용도로 다른 라이브러리(ILogger, Serilog 등)를 사용할 수도 있지만 EventFlow는 Service Fabric 서비스를 지원하고 In-process 로그 수집에 적합하게 특별히 설계되었다는 혜택이 있습니다. 이 덕분에 구성 및 배포 용이성 측면에서 여러 가지 장점이 있으며, 다양한 로깅 라이브러리 및 분석 도구를 보다 유연성 있게 지원합니다. 

## <a name="event-analysis"></a>이벤트 분석

모니터링 및 진단 데이터의 분석 및 시각화와 관련하여 시장에 몇 가지 훌륭한 플랫폼이 나와 있습니다. 권장되는 두 가지 플랫폼은 Service Fabric과 통합된다는 점에서 [OMS](service-fabric-diagnostics-event-analysis-oms.md)와 [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)입니다. [Elastic Stack](https://www.elastic.co/products)(특히 오프라인 환경에서 클러스터를 실행하려는 경우), [Splunk](https://www.splunk.com/) 또는 기타 원하는 플랫폼도 살펴봐야 합니다. 

플랫폼을 선택할 때는 사용자 인터페이스 및 쿼리 옵션에 대한 친숙도, 데이터를 시각화하고 쉽게 읽을 수 있는 대시보드를 만드는 기능 및 자동 경고와 같이 모니터링을 향상시키기 위해 제공하는 추가 도구를 고려해야 합니다.

선택한 플랫폼 외에도 Service Fabric 클러스터를 Azure 리소스로 설정하는 경우 컴퓨터에 대한 Azure의 기본 제공 성능 모니터링 기능에 액세스할 수 있습니다.

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)를 사용하여 Service Fabric 클러스터의 기반이 되는 다양한 Azure 리소스를 모니터링할 수 있습니다. [가상 머신 확장 집합](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) 및 개별 [가상 머신](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)에 대한 일단의 메트릭이 자동으로 수집되어 Azure Portal에 표시됩니다. 수집된 정보를 보려면 Azure Portal에서 Service Fabric 클러스터가 포함된 리소스 그룹을 선택합니다. 그런 다음 보려는 가상 머신 확장 집합을 선택합니다. **모니터링** 섹션(왼쪽 탐색)에서 **메트릭**을 선택하여 값의 그래프를 봅니다.

![수집된 메트릭 정보의 Azure Portal 보기](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

차트를 사용자 지정하려면 [Microsoft Azure의 메트릭](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)의 지침을 따릅니다. 또한 [Azure Monitor에서 Azure 서비스에 대한 알림 만들기](../monitoring-and-diagnostics/monitoring-overview-alerts.md)에서 설명한 대로 이러한 메트릭을 기반으로 하여 알림을 만들 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* Service Fabric이 제공하는 플랫폼 및 이벤트 모니터링에 대한 자세한 내용은 [플랫폼 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-infra.md)을 참조하세요.
* 응용 프로그램 계측을 시작하려면 [응용 프로그램 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-app.md)을 참조하세요.
* [Service Fabric에서 ASP.NET Core 응용 프로그램 모니터링 및 진단](service-fabric-tutorial-monitoring-aspnet.md)에서 응용 프로그램용 AI를 설정하는 단계를 진행합니다.
* 모니터링 컨테이너에 OMS Log Analytics를 설정하는 방법을 알아봅니다. [Azure Service Fabric의 Windows 컨테이너에 대한 모니터링 및 진단](service-fabric-tutorial-monitoring-wincontainers.md)

