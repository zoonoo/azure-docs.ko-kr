---
title: Azure Service Fabric 독립 실행형 클러스터 크기 조정 | Microsoft Docs
description: Service Fabric 독립 실행형 클러스터의 스케일 인 또는 스케일 아웃, 규모 확장 또는 규모 축소에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 4a3b3cf128f9d03f93bbde7f1f6edd5b19328b03
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500579"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Service Fabric 독립 실행형 클러스터 크기 조정
Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 머신 또는 VM을 노드라고 합니다. 클러스터는 잠재적으로 수천 개의 노드를 포함할 수 있습니다. Service Fabric 클러스터를 만든 후에 수평으로(노드 수 변경) 또는 수직으로(노드의 리소스 변경) 클러스터 크기를 조정할 수 있습니다.  클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다.  클러스터의 크기를 조정하면 애플리케이션 크기도 자동으로 조정됩니다.

클러스터 크기를 조정하는 이유 애플리케이션 수요는 시간이 지남에 따라 달라집니다.  늘어난 애플리케이션 워크로드나 네트워크 트래픽을 충족하기 위해 클러스터 리소스를 늘리고, 수요가 줄어들면 클러스터 리소스를 줄여야 할 수 있습니다.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>스케일 인/스케일 아웃 또는 수평적 크기 조정
클러스터의 노드 수를 변경합니다.  새 노드가 클러스터에 조인하면 [클러스터 리소스 관리자](service-fabric-cluster-resource-manager-introduction.md)에서 서비스를 이 노드로 이동하므로 기존 노드의 부하가 감소합니다.  클러스터의 리소스가 효율적으로 사용되지 않을 경우에도 노드 수를 줄일 수 있습니다.  노드가 클러스터를 떠나면 서비스는 해당 노드를 외부로 이동하므로 나머지 노드에서 부하가 늘어납니다.  해당 VM의 워크로드가 아닌 사용하는 VM 수에 따라 비용을 지불하게 되므로 Azure에서 실행되는 클러스터의 노드 수를 줄이면 비용을 절약할 수 있습니다.  

- 장점 이론적으로는 제한 없이 확장할 수 있습니다.  애플리케이션이 확장 가능하게 디자인된 경우, 더 많은 노드를 추가하여 제한없이 증가할 수 있습니다.  클라우드 환경의 도구를 사용하면 노드를 손쉽게 추가 또는 제거할 수 있으므로 용량을 쉽게 조정할 수 있고, 사용한 리소스에 대해서만 비용을 지불하게 됩니다.  
- 단점 애플리케이션을 [확장 가능하게 디자인](service-fabric-concepts-scalability.md)해야 합니다.  애플리케이션 데이터베이스 및 지속성도 확장하려면 추가적인 아키텍처 작업이 필요할 수 있습니다.  그렇지만 Service Fabric 상태 저장 서비스의 [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)을 사용하면 애플리케이션 데이터를 훨씬 더 쉽게 확장할 수 있습니다.

독립 실행형 클러스터를 사용하면 온-프레미스 또는 선택한 클라우드 공급자에 Service Fabric 클러스터를 배포할 수 있습니다.  노드 형식은 배포에 따라, 물리적 컴퓨터 또는 가상 머신으로 구성됩니다. Azure에서 실행되는 클러스터에 비해, 독립 실행형 클러스터의 크기 조정 프로세스는 좀 더 복잡합니다.  클러스터의 노드 수를 수동으로 변경한 후 클러스터 구성 업그레이드를 실행해야 합니다.

노드를 제거하여 여러 업그레이드를 시작할 수 있습니다. 일부 노드는 `IsSeedNode=”true”` 태그로 표시되고 [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest)를 사용하여 클러스터 매니페스트를 쿼리하여 확인할 수 있습니다. 시드 노드는 이러한 시나리오에서 이동되어야 하므로 이러한 노드의 제거는 다른 항목보다 더 오래 걸릴 수 있습니다. 클러스터는 최소 3가지 주 노드 유형 노드를 유지해야 합니다.

> [!WARNING]
> 클러스터에 대한 [안정성 계층의 클러스터 크기](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) 아래로 노드 개수를 낮추지 않는 것이 좋습니다. 이렇게 낮추면 Service Fabric 시스템 서비스가 클러스터 전체에 복제되는 기능을 방해하여 클러스터가 불안정해지거나 손상될 가능성이 있습니다.
>

독립 실행형 클러스터 크기를 조정할 때는 다음 지침에 유의하세요.
- 주 노드 교체는 일괄 처리로 제거한 다음 추가하는 대신 차례로 수행되어야 합니다.
- 노드 유형을 제거하기 전에 노드 유형을 참조하는 노드가 있는지 확인합니다. 해당 노드 유형을 제거하기 전에 이러한 노드를 제거합니다. 모든 해당 노드가 제거된 후 클러스터 구성에서 NodeType을 제거하고 [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)를 사용하여 구성 업그레이드를 시작할 수 있습니다.

자세한 내용은 [독립 실행형 클러스터 크기 조정](service-fabric-cluster-windows-server-add-remove-nodes.md)을 참조하세요.

## <a name="scaling-up-and-down-or-vertical-scaling"></a>규모 확장 및 축소 또는 수직적 크기 조정 
클러스터에 있는 노드의 리소스(CPU, 메모리 또는 저장소)를 변경합니다.
- 장점 소프트웨어 및 애플리케이션 아키텍처가 동일하게 유지됩니다.
- 단점 개별 노드에서 늘릴 수 있는 리소스 양이 제한되어 있으므로 확장 한계가 있습니다. 리소스를 추가하거나 제거하기 위해 물리적 컴퓨터 또는 가상 머신을 오프라인으로 전환해야 하므로 가동 중지 시간이 발생합니다.

## <a name="next-steps"></a>다음 단계
* [애플리케이션 확장성](service-fabric-concepts-scalability.md)에 대해 알아봅니다.
* [Azure 클러스터를 스케일 인 또는 스케일 아웃](service-fabric-tutorial-scale-cluster.md)합니다.
* 유용한 Azure Compute SDK를 사용하여 [Azure 클러스터의 크기를 프로그래밍 방식으로 조정](service-fabric-cluster-programmatic-scaling.md)합니다.
* [독립 실행형 클러스터 스케일 인 또는 스케일 아웃](service-fabric-cluster-windows-server-add-remove-nodes.md).

