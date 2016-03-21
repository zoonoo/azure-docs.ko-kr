<properties
   pageTitle="Service Fabric Cluster Resource Manager 소개"
   description="Service Fabric Cluster Resource Manager를 소개합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>


# 제한
Resource Manager를 올바르게 구성한 경우에도 많은 노드 또는 장애 도메인 오류, 업그레이드 진행, 업데이트 적용 등으로 인해 클러스터가 중단될 수 있습니다. Resource Manager에서 이러한 문제를 수정하기 위해 최선을 다하겠지만 이러한 경우 클러스터 자체에서 안정화(노드 다시 작동, 네트워크 상태 정상화, 수정된 비트 배포)되도록 방어벽을 고려해 보려고 할 수 있습니다. 이러한 상황을 위해 Resource Manager는 여러 가지 제한을 포함합니다. 이러한 제한은 꽤 "큰 장애물"일 수 있으므로 클러스터에서 실제로 수행할 수 있는 병렬 작업의 양을 신중히 계산할 뿐만 아니라 계획되지 않은 거시적 재구성 이벤트("매우 좋지 않은 날")에 자주 응답해야 하는 경우에만 사용해야 합니다.

일반적으로 자체적으로 수정할 때 클러스터를 제한하여 리소스 사용을 금지하는 대신 다른 옵션(정규 코드 업데이트 및 시작할 클러스터의 예정 시간 초과 방지)을 통해 매우 나쁜 날을 방지하는 것이 좋습니다. 즉, 클러스터가 안정화되는 데 더 오랜 시간이 걸리더라도 수정될 때까지 몇 가지 제한을 설정해야 하는 경우가 있습니다.

기본적으로 포함된 제한은 다음과 같습니다.
-	GlobalMovementThrottleThreshold – 일정 시간 동안 클러스터 내에서 발생하는 총 이동 수를 제어합니다(GlobalMovementThrottleCountingInterval, 값(초)으로 정의).
-	MovementPerPartitionThrottleThreshold – 일정 시간 동안 서비스 파티션에 대해 발생하는 총 이동 수를 제어합니다(MovementPerPartitionThrottleCountingInterval, 값(초))

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

대부분의 고객은 다음과 같은 이유 때문에 이러한 제한을 사용합니다.
-	이미 제약 조건이 있어 이러한 작업이 성공하지 않거나 느려지는 환경인 경우(예: 개별 노드로의 네트워크 대역폭 제한)
-	제한 상태에서 전반적인 실행 안정성이 떨어질 수 있음을 포함하여 클러스터가 안정적인 상태가 될 때까지 걸리는 시간이 연장될 수 있음을 알고 편안함을 느끼는 경우

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
- [Cluster에서 Cluster Resource Manager가 부하를 분산하는 방법 알아보기](service-fabric-cluster-resource-manager-balancing.md)

<!---HONumber=AcomDC_0309_2016-->