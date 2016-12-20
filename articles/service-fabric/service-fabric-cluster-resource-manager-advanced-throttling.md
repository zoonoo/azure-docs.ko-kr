---
title: "Service Fabric 클러스터 리소스 관리자의 제한 | Microsoft Docs"
description: "서비스 패브릭 클러스터 리소스 관리자에서 제공하는 제한을 구성하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ce3e53a4edfa8b97ce4fdb6b553d0b6c917f54e4


---
# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>서비스 패브릭 클러스터 Resource Manager의 동작 제한
클러스터 Resource Manager를 올바르게 구성한 경우에도 클러스터가 중단될 수 있습니다. 예를 들어 동시 노드 또는 장애 도메인 오류가 있을 수 있습니다. 업그레이드 중에 이러한 문제가 발생하면 어떻게 하나요? Resource Manager에서 이러한 문제를 수정하기 위해 최선을 다하겠지만 이러한 경우 클러스터 자체에서 안정화(노드 다시 작동, 네트워크 상태 정상화, 수정된 비트 배포)되도록 방어벽을 고려해 보려고 할 수 있습니다. 이러한 상황을 위해 서비스 패브릭 클러스터 Resource Manager는 여러 가지 제한을 포함합니다. 이러한 제한은 작업을 중단할 수 있으므로 클러스터에서 실제로 수행할 수 있는 병렬 작업의 양을 신중히 계산할 뿐만 아니라 계획되지 않은 거시적 재구성 이벤트(즉, "매우 좋지 않은 날")에 자주 응답해야 하는 경우에만 사용해야 합니다.

일반적으로 자체적으로 수정할 때 클러스터를 제한하여 리소스 사용을 금지하는 대신 다른 옵션(정규 코드 업데이트 및 시작할 클러스터의 예정 시간 초과 방지)을 통해 매우 나쁜 날을 방지하는 것이 좋습니다. 이러한 제한은 경험을 통해 문제가 없는 것으로 확인된 기본값을 갖지만, 예상되는 실제 부하에 맞게 조정할 수도 있습니다. 클러스터를 과도하게 제한하거나 과부하를 부여하지 않는 것이 가장 좋은 방법이지만 클러스터가 안정화되는 데 더 오랜 시간이 걸리더라도 수정될 때까지 몇 가지 제한을 설정해야 하는 경우가 있습니다.

## <a name="configuring-the-throttles"></a>제한 구성
기본적으로 포함된 제한은 다음과 같습니다.

* GlobalMovementThrottleThreshold – 일정 시간 동안 클러스터 내에서 발생하는 총 이동 수를 제어합니다(GlobalMovementThrottleCountingInterval, 값(초)으로 정의).
* MovementPerPartitionThrottleThreshold – 일정 시간 동안 서비스 파티션에 대해 발생하는 총 이동 수를 제어합니다(MovementPerPartitionThrottleCountingInterval, 값(초))

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

대부분의 경우 고객이 이미 리소스 제한 환경에 있기 때문에(예: 네트워크 대역폭이 적용되는 병렬 복제본 빌드의 요구 사항과 관련이 없는 개별 노드 또는 디스크로 제한됨) 이러한 제한을 사용한다는 것을 알고 있습니다. 즉, 이러한 작업은 성공하지 못하거나 느려질 수 있습니다.  이러한 상황에서 고객은 제한된 동안 전반적인 실행 안정성이 떨어질 수 있다는 사실을 이해하는 것은 물론, 클러스터가 안정적인 상태가 될 때까지 걸리는 시간을 연장할 수 있다는 사실을 통해 안심하고 작업할 수 있었습니다.

## <a name="next-steps"></a>다음 단계
* 클러스터 리소스 관리자가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)
* 클러스터 리소스 관리자에는 클러스터를 설명하기 위한 많은 옵션이 있습니다. 이에 대해 자세히 알아보려면 [서비스 패브릭 클러스터를 설명](service-fabric-cluster-resource-manager-cluster-description.md)




<!--HONumber=Nov16_HO3-->


