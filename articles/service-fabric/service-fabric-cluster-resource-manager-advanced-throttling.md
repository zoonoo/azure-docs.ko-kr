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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 8a8419497bda3f1df523d6aff28028548abc155a
ms.lasthandoff: 01/07/2017


---

# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>서비스 패브릭 클러스터 Resource Manager의 동작 제한
클러스터 Resource Manager를 올바르게 구성한 경우에도 클러스터가 중단될 수 있습니다. 예를 들어 동시 노드 또는 장애 도메인 오류가 있을 수 있습니다. 업그레이드 중에 이러한 문제가 발생하면 어떻게 하나요? 클러스터 Resource Manager에서 모든 문제를 해결하려고 시도하지만 이로 인해 클러스터에 변동이 발생할 수 있습니다. 제한은 클러스터가 리소스를 사용하여 자체적으로 안정화(노드 다시 작동, 네트워크 파티션 정상화, 수정된 비트 배포)할 수 있도록 방어벽을 제공하는 데 도움이 됩니다.

이러한 상황을 위해 Service Fabric 클러스터 Resource Manager는 여러 가지 제한을 포함합니다. 이러한 제한은 꽤 큰 장애물입니다. 이러한 설정을 기본값에서 다른 값으로 변경해서는 안 됩니다. 단, 클러스터가 병렬로 수행할 수 있는 작업량과 관련한 세심한 수학적 계산을 수행한 경우는 예외입니다.

제한은 Service Fabric 팀이 경험을 통해 양호한 기본값으로 확인한 기본값으로 설정됩니다. 이러한 기본값을 변경해야 하는 경우 예상되는 실제 부하에 맞게 조정해야 합니다. 메인라인 상황에서 클러스터 안정화에 시간이 더 오래 걸리더라도 일부 제한을 설정해야 하는지 결정할 수 있습니다.

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

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json을 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThreshold",
          "value": "1000"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      },
      {
          "name": "MovementPerPartitionThrottleThreshold",
          "value": "50"
      },
      {
          "name": "MovementPerPartitionThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

대부분의 경우 고객은 이미 리소스 제약 환경을 사용하고 있기 때문에 이러한 제한을 예전부터 사용하고 있습니다. 이러한 환경의 몇 가지 예로 개별 노드 또는 디스크로 제한된 네트워크 대역폭이 있습니다. 이 경우 처리량 제한으로 인해 많은 복제본을 동시에 만들 수 없습니다. 별도의 제한이 없이도 이러한 유형의 제한 때문에 오류에 대한 응답으로 발생하는 작업이 성공하지 못하거나 느려집니다. 이러한 상황에서 고객은 클러스터가 안정적인 상태에 도달하는 데 걸리는 시간이 더 길어진다는 것을 알고 있습니다. 고객은 또한 제한된 상태에서는 전반적인 안정성이 낮은 상태로 실행될 수 있다는 사실도 알고 있습니다.

## <a name="next-steps"></a>다음 단계
* 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)
* 클러스터 Resource Manager에는 클러스터를 설명하기 위한 많은 옵션이 있습니다. 이에 대해 자세히 알아보려면 [Service Fabric 클러스터를 설명](service-fabric-cluster-resource-manager-cluster-description.md)하는 이 문서를 확인하세요.

