---
title: Azure Service Fabric 모니터링 모범 사례
description: Azure Service Fabric을 사용한 클러스터 및 애플리케이션 모니터링 모범 사례와 디자인 고려 사항입니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 0eb9ce24f9ead44b7ba5a4d28d24177e62cb7757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950521"
---
# <a name="monitoring-and-diagnostic-best-practices-for-azure-service-fabric"></a>Azure Service Fabric 모니터링 및 진단 모범 사례

[모니터링 및 진단](./service-fabric-diagnostics-overview.md)은 모든 클라우드 환경에서 워크로드를 개발, 테스트 및 배포하는 데 중요합니다. 예를 들어, 애플리케이션이 사용되는 방식, Service Fabric 플랫폼이 수행한 작업, 성능 카운터를 통한 리소스 사용률 및 클러스터의 전반적인 상태를 추적할 수 있습니다. 이 정보를 사용하여 문제를 진단하고 수정할 수 있으며 나중에 문제가 발생하지 않도록 방지할 수 있습니다.

## <a name="application-monitoring"></a>애플리케이션 모니터링

애플리케이션 모니터링은 애플리케이션의 기능 및 구성 요소가 사용되는 방식을 추적합니다. 애플리케이션을 모니터링하여 사용자에게 영향을 주는 문제가 발견되었는지 확인합니다. 애플리케이션 모니터링은 애플리케이션의 비즈니스 논리마다 고유하므로, 애플리케이션 및 해당 서비스를 개발하는 사용자의 책임입니다. [Application Insights](./service-fabric-tutorial-monitoring-aspnet.md), Azure의 애플리케이션 모니터링 도구를 사용하여 애플리케이션 모니터링을 설정하는 것이 좋습니다.

## <a name="cluster-monitoring"></a>클러스터 모니터링

Service Fabric의 목표 중 하나는 애플리케이션을 하드웨어 오류에 대해 복원력 있게 만드는 것입니다. 이 목표는 플랫폼의 시스템 서비스가 인프라 문제를 감지하고 워크로드를 클러스터의 다른 노드로 빠르게 장애 조치(failover)할 수 있기 때문에 실현이 가능합니다. 그러나 시스템 서비스 자체에 문제가 있다면 어떻게 될까요? 또는 작업을 배포 또는 이동할 때 서비스 배치 규칙에 위배되면 어떻게 될까요? Service Fabric은 Service Fabric 플랫폼이 애플리케이션, 서비스, 컨테이너 및 노드와 상호 작용하는 방법을 사용자에게 알리도록 이러한 문제 및 기타 문제에 대한 진단을 제공합니다.

Windows 클러스터의 경우 [진단 에이전트](./service-fabric-diagnostics-event-aggregation-wad.md)와 [Azure Monitor 로그](./service-fabric-diagnostics-oms-setup.md)를 사용하여 클러스터 모니터링을 설정하기를 권합니다.

Linux 클러스터의 경우 Azure Monitor 로그 또한 Azure 플랫폼 및 인프라 모니터링에 권장되는 도구입니다. Linux 플랫폼 진단은 [Syslog의 Service Fabric Linux 클러스터 이벤트](./service-fabric-diagnostics-oms-syslog.md)에서 설명한 것처럼 다른 구성이 필요합니다.

## <a name="infrastructure-monitoring"></a>인프라 모니터링

클러스터 수준 이벤트 모니터링에는 [Azure Monitor 로그](./service-fabric-diagnostics-oms-agent.md)가 권장됩니다. 이전 링크에서 설명한 대로 작업 영역으로 Log Analytics 에이전트를 구성하면 CPU 사용률과 같은 성능 메트릭, 프로세스 수준 CPU 사용률과 같은 .NET 성능 카운터, 신뢰할 수 있는 서비스의 # 예외와 같은 Service Fabric 성능 카운터 및 CPU 사용률과 같은 카운터 메트릭을 수집할 수 있습니다.  Azure Monitor 로그에서 사용할 수 있도록 stdout 또는 stderr에 컨테이너 로그를 작성해야 합니다.

## <a name="watchdogs"></a>Watchdog

일반적으로 Watchdog는 전체 서비스의 상태와 부하를 감시하고, 엔드포인트에 Ping을 수행하고, 클러스터에 있는 예기치 않은 상태 이벤트를 보고하는 별도의 서비스입니다. 이렇게 하면 단일 서비스의 성능만을 기준으로 감지되지 않는 오류를 방지할 수 있습니다. 또한 Watchdog은 특정 시간 간격으로 스토리지에서 로그 파일을 정리하는 것처럼 사용자 상호 작용이 필요하지 않은 수정 작업을 수행하는 코드를 호스트하기 좋은 위치입니다. 사용하기 쉬운 Watchdog 확장성 모델을 포함하고 Windows 및 Linux 클러스터 모두에서 실행되는 완전히 구현된 오픈 소스 SF Watchdog 서비스를 사용하려면 [FabricObserver](https://aka.ms/sf/FabricObserver) 프로젝트를 참조하세요. FabricObserver는 프로덕션에 최적화된 소프트웨어입니다. 테스트 및 프로덕션 클러스터에 FabricObserver를 배포하고 플러그 인 모델을 통해 요구 사항에 맞게 확장하거나, 이를 분기하고 사용자 고유의 기본 제공 관찰자를 작성하면 좋습니다. 전자(플러그 인)가 권장되는 방법입니다.

## <a name="next-steps"></a>다음 단계

* 애플리케이션 계측을 시작하려면 [애플리케이션 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-app.md)을 참조하세요.
* [Service Fabric에서 ASP.NET Core 애플리케이션 모니터링 및 진단](service-fabric-tutorial-monitoring-aspnet.md)에서 애플리케이션용 Application Insights를 설정하는 단계를 진행합니다.
* Service Fabric이 제공하는 플랫폼 및 이벤트 모니터링에 대한 자세한 내용은 [플랫폼 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-infra.md)을 참조하세요.
* Service Fabric와 Azure Monitor 로그 통합 구성: [클러스터에 대한 Azure Monitor 로그 설정](service-fabric-diagnostics-oms-setup.md)
* [Azure Service Fabric에서 Windows 컨테이너에 대한 모니터링 및 진단](service-fabric-tutorial-monitoring-wincontainers.md)으로 컨테이너 모니터링에 Azure Monitor 로그를 설정하는 방법에 대해 알아봅니다.
* [일반적인 시나리오 진단](service-fabric-diagnostics-common-scenarios.md)에서 Service Fabric을 사용하여 예제 진단 문제 및 솔루션을 참조하세요.
* [모범 사례 - 모니터링 및 진단](/azure/architecture/best-practices/monitoring)으로 Azure 리소스에 대한 일반적인 모니터링 권장 사항을 알아봅니다.
