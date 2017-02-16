---
title: "Service Fabric 클러스터 리소스 관리자 소개 | Microsoft Docs"
description: "서비스 패브릭 클러스터 리소스 관리자를 소개합니다."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: ec470466f006265af5c4ccfddeeba975e6e648b5


---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>서비스 패브릭 클러스터 리소스 관리자 소개
일반적으로 IT 시스템 또는 일련의 서비스를 관리하는 작업은 해당 특정 서비스 또는 시스템 전용인 몇 대의 물리적 또는 가상 컴퓨터를 가져오는 것입니다. 대부분의 주요 서비스는 캐시와 같은 몇 가지 기타 특별한 구성 요소와 함께 "웹" 계층 및 "데이터" 또는 "저장소" 계층으로 나뉩니다. 다른 유형의 응용 프로그램에는 요청이 들어오고 나가는 메시징 계층이 있습니다. 이 계층은 메시징의 일부로 필요한 분석 또는 변환에 대한 작업 계층에 연결됩니다. 각 워크로드 형식에 특정 컴퓨터가 전용으로 지정되어 있습니다. 데이터베이스에도 전용으로 사용하는 컴퓨터가 몇 대 있고 웹 서버에도 몇 대가 있습니다. 특정 유형의 워크로드로 인해 컴퓨터가 너무 뜨거워지는 문제가 발생한 경우 동일한 구성으로 더 많은 컴퓨터를 추가했습니다. 그러나 대부분 컴퓨터의 일부를 더 큰 컴퓨터로 바꾸었습니다. 간편성. 컴퓨터에 오류가 발생한 경우 컴퓨터를 복원할 수 있을 때까지 전체 응용 프로그램 중 해당 부분은 낮은 최대 용량으로 실행합니다. (재미는 없더라도)아직 상당히 쉽습니다.

그러나 이제 규모를 확장하고 컨테이너 및/또는 마이크로 서비스 플런지를 사용해야 한다고 가정하겠습니다. 갑자기 수십, 수백 또는 수천 대의 컴퓨터가 생겼습니다. 다른 유형의 서비스(전체 컴퓨터의 리소스를 사용하지 않음)가 여러 개, 아마도 해당 서비스의 다른 인스턴스가 수백 개나 있을 것입니다. 명명된 인스턴스 각각에는 HA(고가용성)을 위해 하나 이상의 인스턴스 또는 복제본이 있습니다.

갑자기 환경을 관리하는 작업은 단일 유형의 워크로드에 전용으로 사용하는 몇 대의 컴퓨터를 관리하는 것만큼 간단하지 않습니다. 서버는 가상이며 이름이 없습니다(결국 *애완 동물에서 가축으로* 사고방식을 [전환](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) ). 구성은 컴퓨터에 대해서가 아니라 서비스 자체에 대한 것입니다. 전용 하드웨어는 과거의 방식이며, 서비스 자체는 더 작은 상용 하드웨어 부분에 걸쳐 있는 작은 분산 시스템이 되었습니다.

이전의 모놀리식 계층화된 앱을 상용 하드웨어에서 실행되는 별도 서비스로 나누는 과정에서 이제 다루어야 할 더 많은 조합이 만들어졌습니다. 어떤 형식의 워크로드가 어떤 하드웨어에서 또는 몇 개의 하드웨어에서 실행될 수 있는지를 누가 결정하나요? 어떤 워크로드는 동일한 하드웨어에서 제대로 작동하고 어떤 워크로드는 충돌하나요? 컴퓨터가 중단되는 경우... 무엇이 실행되나요? 워크로드가 다시 실행되기 시작하는지는 누가 확인하나요? 다시 (가상) 컴퓨터가 돌아오기를 기다리거나 워크로드가 다른 컴퓨터로 자동으로 장애 조치되고 계속 실행되나요? 사람의 개입이 필요하나요? 이러한 환경에서 업그레이드는 어떤가요?

이 기능을 다루는 개발자와 연산자의 경우 이러한 복잡성을 관리하는 데 도움이 필요합니다. 여러 가지 기능을 제공하고 사람들의 복잡한 문제를 해결하는 것이 전부가 아니라는 사실을 이해하게 됩니다.

그렇다면 어떻게 해야 할까요?

## <a name="introducing-orchestrators"></a>Orchestrator 소개
"Orchestrator"는 관리자가 이러한 형식의 환경을 관리하는 데 도움이 되는 소프트웨어에 대한 일반 용어입니다. Orchestrator는 "내 환경에서 실행되는 이 서비스의 복사본&5;개가 필요합니다"와 같은 요청을 실현하는 구성 요소입니다. 발생한 상황과 관계 없이 원하는 상태와 일치하는 환경을 만들려고 합니다.

Orchestrator(사람 아님)는 컴퓨터가 실패하거나 예기치 않은 이유로 워크로드를 종료하는 경우 행동에 옮깁니다. Orchestrator는 대부분 오류 처리 이상의 작업을 수행합니다. 다른 기능으로는 새 배포를 관리하고 업그레이드를 처리하며 리소스 사용을 처리하는 작업이 있습니다. 모든 Orchestrator는 기본적으로 환경에서 구성의 원하는 일부 상태를 유지하려고 합니다. Orchestrator가 어려운 작업을 수행하게 하도록 요청할 수 있습니다. Mesos, Fleet, Docker Datacenter/Docker Swarm, Kubernetes 및 Service Fabric에 기반하는 Chronos 또는 Marathon은 모두 Orchestrator의 예이거나 Orchestrator를 기본 제공합니다. 다른 유형의 환경 및 조건에서 실제 배포를 관리하는 경우 복잡성이 증가하고 변경되면서 더 많이 생성됩니다.

## <a name="orchestration-as-a-service"></a>서비스인 Orchestration
서비스 패브릭 클러스터 내의 Orchestrator 작업은 클러스터 리소스 관리자가 기본적으로 처리합니다. 서비스 패브릭 클러스터 리소스 관리자는 서비스 패브릭 내의 시스템 서비스 중 하나이며 각 클러스터 내에서 자동으로 시작됩니다. 일반적으로 클러스터 Resource Manager의 작업은 세 부분으로 나뉩니다.

1. 규칙 적용
2. 환경 최적화
3. 다른 프로세스 지원

Cluster Resource Manager가 작동하는 방법을 보려면 다음 Microsoft Virtual Academy 비디오를 보세요. <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>잘못된 정의
일반적인 N 계층 응용 프로그램에는 "부하 분산 장치"의 몇 가지 개념이 있습니다. 일반적으로 네트워킹 스택이 배치된 위치에 따른 NLB(네트워크 부하 분산 장치) 또는 ALB(응용 프로그램 부하 분산 장치)입니다. 일부 부하 분산 장치는 F5의 BigIP 제품과 같은 하드웨어 기반이며 나머지는 Microsoft의 NLB와 같은 소프트웨어 기반입니다. 다른 환경에서는 이 역할에서 HAProxy 또는 nginx와 같은 항목을 확인할 수 있습니다. 이러한 아키텍처에서 부하 분산 작업은 상태 비저장 워크로드가 거의 동일한 양의 작업을 수신하도록 하는 것입니다. 부하를 분산하는 전략은 다양합니다. 일부 분산 장치는 여러 호출을 각각 다른 서버로 보냅니다. 다른 기능은 세션 고정/인력을 제공했습니다. 고급 분산 장치에서는 실제 추정 또는 보고를 사용하여 예상된 비용 및 현재 컴퓨터 부하에 따라 호출을 라우팅합니다.

네트워크 분산 장치 또는 메시지 라우터는 웹/작업자 계층이 거의 분산되어 있는지 확인하려고 합니다. 데이터 계층을 분산하기 위한 전략은 서로 다르며 데이터 저장소 메커니즘에 따릅니다. 일반적으로 데이터 분할, 캐싱, 관리된 뷰, 저장 프로시저 및 기타 저장 저장소 지정 매커니즘 등에 집중합니다.

이러한 전략 중 일부는 흥미롭지만 서비스 패브릭 클러스터 리소스 관리자는 네트워크 부하 분산 장치 또는 캐시와 전혀 다릅니다. Network Load Balancer를 통해 프런트엔드가 서비스를 실행하는 위치에 트래픽을 이동하여 부하를 분산하도록 합니다. Service Fabric Cluster Resource Manager에서는 다른 전략을 사용합니다. Service Fabric은 근본적으로 *서비스*를 트래픽 또는 로드를 따라 가장 합리적인 위치로 이동시킵니다. 예를 들어, 거기 있는 서비스는 많은 작업을 수행하지 않기 때문에 현재 콜드 상태인 노드에 서비스를 이동할 수 있습니다. 존재하던 서비스가 삭제되었거나 다른 곳으로 이동되었기 때문에 노드는 콜드 상태일 수 있습니다. 또 다른 예로 Cluster Resource Manager는 컴퓨터에서 서비스를 이동할 수도 있습니다. 아마도 실행 중인 서비스에 의한 소모량 급증으로 인해 컴퓨터는 업그레이드되거나 오버로드됩니다.

Cluster Resource Manager가 서비스를 이동시키는 일을 담당하므로(서비스가 이미 있는 위치로 네트워크 트래픽을 전달하지 않음) 네트워크 부하 분산 장치에서 찾을 수 있는 기능과 비교할 때 다른 기능 집합을 포함합니다. 자세히 보면 클러스터의 하드웨어 리소스가 효율적으로 활용되도록 하기 위해 기본적으로 다른 전략을 사용합니다.

## <a name="next-steps"></a>다음 단계
* Cluster Resource Manager 내의 아키텍처 및 정보 흐름에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-architecture.md)를 확인하세요.
* Cluster Resource Manager에는 클러스터를 설명하기 위한 많은 옵션이 있습니다. 이에 대해 자세히 알아보려면 [Service Fabric 클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md)에 대한 문서를 확인하세요.
* 서비스 구성에 사용할 수 있는 기타 옵션에 대한 자세한 내용은 [서비스 구성에 대해 알아보기](service-fabric-cluster-resource-manager-configure-services.md)에서 사용할 수 있는 기타 Cluster Resource Manager 구성에 대한 항목을 확인하세요.
* 메트릭은 서비스 패브릭 클러스터 리소스 관리자가 클러스터의 소비와 용량을 관리하는 방법입니다. 메트릭 및 구성 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)
* 클러스터 리소스 관리자는 서비스 패브릭의 관리 기능으로 작동합니다. 해당 통합에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-management-integration.md)
* 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)



<!--HONumber=Jan17_HO1-->


