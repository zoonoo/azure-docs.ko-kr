<properties
   pageTitle="Service Fabric Cluster Resource Manager - 이동 비용"
   description="서비스 패브릭 서비스의 이동 비용 개요"
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

# 이동 비용
클러스터를 변경하려고 할 때 고려해야 할 또 다른 중요 요소와 특정 솔루션의 점수가 해당 솔루션의 전체 비용입니다.

서비스 인스턴스 또는 복제본을 이동하려면 최소한의 CPU 시간 및 네트워크 대역폭이 필요합니다. 또한 상태 저장 서비스의 경우 이전 복제본을 종료하기 전에 상태 복사본을 만들 디스크 공간도 필요합니다. 누구나 Resource Manager에서 발생하는 솔루션 비용을 최소화하면서 클러스터에서 리소스 할당을 크게 향상시키는 솔루션을 원할 것입니다.

서비스 패브릭 Resource Manager에는 비용을 계산하고 제한하는 방법이 두 가지 있습니다. 첫 번째는 Resource Manager가 클러스터에 대한 새 레이아웃을 계획할 때 모든 단일 이동 수를 계산하는 것입니다. 간단한 예로 전체적인 균형(점수)이 거의 같은 두 개의 솔루션이 있는 경우 비용(총 이동 수)이 더 낮은 솔루션을 선택합니다. 이 방법은 기본 또는 정적 부하 상태에서 발생한 것과 동일한 문제가 발생할 때까지는 문제가 없습니다. 모든 이동이 동일한 복잡한 시스템에서는 일부 이동에 비용이 더 많이 들 경우는 매우 드뭅니다. 부하와 마찬가지로 지정된 시간에 이동하는 데 필요한 비용을 자체 보고하는 방법을 서비스에 제공합니다.

코드

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost는 0, 낮음, 보통 및 높음 수준을 제공합니다. 이 수준은 서로 상대적입니다. 단, 0은 복제본 이동이 무료이므로 솔루션 점수에 포함되지 않습니다. 이동 비용을 높음으로 설정해도 복제본이 절대 이동되지 않는다는 것이 아니라 합당한 이유가 있는 경우에만 이동한다는 의미입니다.

![이동할 복제본 선택의 요소인 이동 비용][Image1]

이동 비용을 사용하면 전체적으로 중단을 최소화하면서 동등한 균형을 제공하는 솔루션을 찾을 수 있습니다. 서비스의 비용 개념은 여러 가지 요소에 따라 상대적일 수 있지만 일반적인 요소는 다음과 같습니다.

1.	서비스에서 이동해야 하는 상태 또는 데이터의 양
2.	클라이언트 연결 해제 비용(주 복제본의 이동 비용이 보조 복제본의 이동 비용보다 높아짐)
3.	진행 중인 일부 작업의 중단 비용(일부 데이터 저장소 수준 작업은 비용이 많이 들고 특정 시점 이후 별도로 중단할 필요가 없음). 따라서 작업 기간에 서비스 복제본 또는 인스턴스가 이동할 가능성을 줄이기 위해 비용을 줄이고 작업이 완료될 때 다시 정상 상태로 전환합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
- [매트릭에 대해 알아보기](service-fabric-cluster-resource-manager-metrics.md)
- [Cluster에서 Cluster Resource Manager가 부하를 분산하는 방법 알아보기](service-fabric-cluster-resource-manager-balancing.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0309_2016-->