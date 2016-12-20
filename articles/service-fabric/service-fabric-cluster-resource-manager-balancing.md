---
title: "Azure Service Fabric 클러스터 리소스 관리자를 사용한 클러스터 분산 | Microsoft Docs"
description: "서비스 패브릭 클러스터 리소스 관리자를 사용한 클러스터 분산에 대한 소개"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1ca022d7412479e8e072ec319020b1091196bc04


---
# <a name="balancing-your-service-fabric-cluster"></a>서비스 패브릭 클러스터 분산
서비스 패브릭 클러스터 Resource Manager에서는 동적 로드를 보고하고, 클러스터의 변경 사항에 대응하며, 제약 조건 위반을 수정하고, 필요한 경우 클러스터 균형을 다시 조정할 수 있습니다. 그렇지만 이러한 작업이 얼마나 자주 수행되며 어떤 요인으로 시작될까요? 이와 관련된 다수의 컨트롤이 있습니다.

균형 조정과 관련된 첫 번째 컨트롤 집합은 타이머 집합입니다. 이러한 타이머는 클러스터 Resource Manager에서 해결해야 하는 항목에 대한 클러스터의 상태를 검사하는 빈도를 제어합니다. 각각 고유한 자체 타이머를 포함하는 세 가지 작업 범주가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

1. 배치 – 이 단계는 상태 저장 복제본 또는 누락된 상태 비저장 인스턴스 배치를 처리합니다. 새로운 서비스 및 상태 저장 복제본 또는 실패하여 다시 만들어야 하는 상태 비저장 인스턴스를 모두 다룹니다. 복제본 또는 인스턴스 삭제와 제거도 여기에서 다룹니다.
2. 제약 조건 검사 - 이 단계에서는 시스템 내에서 여러 배치 제약 조건(규칙)을 검사하고 위반을 수정합니다. 규칙의 예는 노드 용량을 초과하지 않고 서비스 배치 제약 조건(나중에 자세히 다룸)을 충족하도록 하는 것들입니다.
3. 분산 - 다른 메트릭에 대한 분산의 원하는 구성 수준에 따라 사전 균형 조정이 필요한지 그리고 좀더 균형이 잡힌 클러스터의 배열을 찾을지 검사합니다.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>클러스터 리소스 관리자 단계 및 타이머 구성
클러스터 Resource Manager에서 수행할 수 있는 이러한 각 형식의 수정 작업은 해당 빈도를 제어하는 다른 타이머에 의해 제어됩니다. 예를 들어, 클러스터에 새로운 서비스 작업 부하를 매시간 배치하는 것만 처리하려 하지만(모두 일괄 처리) 몇 초마다 정규 분산 검사를 수행하려는 경우, 해당 동작을 구성할 수 있습니다. 각 타이머가 실행되면 작업이 예약됩니다. 기본적으로 Resource Manager에서는 상태를 검색하여 1/10초마다 업데이트를 적용하고(노드가 다운되었다는 것을 확인한 경우처럼 마지막 검색 이후에 발생한 모든 변경 내용을 일괄 처리) 1초마다 배치 및 제약 조건 검사 플래그를, 5초마다 분산 플래그를 설정합니다.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

현재는 이러한 작업을 한 번에 하나씩만 순차적으로 수행합니다(이에 따라 이러한 구성을 "최소 간격"이라고 지칭함). 그러므로 보류 중인 요청 등에 이미 응답하여 클러스터 분산으로 진행하기 전에 새로운 복제본을 만듭니다. 지정된 기본 시간 간격을 통해 알 수 있듯이, 매우 자주 수행해야 하는 작업을 검색하고 검사할 수 있으므로 각 단계의 끝에 만드는 변경 사항은 일반적으로 더 작습니다. 클러스터의 변경 시간 내내 검색하고 한 번에 모두를 수정하지 않고 발생할 때 어느 정도를 처리하려고 하지만 일부 배치에서는 많은 것이 동시에 발생할 때 처리하려고 합니다. 이렇게 하면 서비스 패브릭 리소스 관리자는 클러스터에서 발생하는 사항에 매우 빠르게 응답합니다.

이러한 작업 대부분은 간단하지만(제약 조건을 위반하는 경우 수정하고, 서비스를 만들어야 할 경우 만듦), 클러스터 Resource Manager에서는 클러스터가 불균형 상태인지 확인하기 위해 몇 가지 추가 정보가 필요합니다. 이를 위해 *분산 임계값*과 *활동 임계값* 등의 두 가지 구성이 있습니다.

## <a name="balancing-thresholds"></a>분산 임계값
분산 임계값은 사전 균형 재조정을 트리거하기 위한 주 컨트롤입니다. 타이머는 클러스터 Resource Manage에서 확인해야 하는 빈도를 나타내며 실제 발생한다는 의미는 아닙니다. 분산 임계값은 특정 메트릭에 대해 클러스터가 얼마나 불균형한지를 정의하여 클러스터 Resource Manager에서 클러스터가 불균형하다고 간주하고 분산을 트리거하도록 합니다.

분산 임계값은 클러스터 정의의 일부로서 메트릭 단위로 정의됩니다. 메트릭에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 확인하세요.

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

메트릭에 대한 분산 임계값은 비율입니다. 가장 부하가 많은 노드의 부하량을 가장 부하가 적은 노드의 부하량으로 나눈 값이 이 값을 초과하는 경우 클러스터가 불균형하다고 간주하고 클러스터 Resource Manager에서 이 사실을 확인한 다음번에 부하 분산이 트리거됩니다(기본적으로 위에 표시된 MinLoadBalancingInterval에 따라 5초 간격).

![분산 임계값 예][Image1]

이 간단한 예제에서는 각 서비스에서 한 단위의 메트릭을 사용합니다. 위 예제에서 노드의 최대 부하는 5이고 최소 부하는 2입니다. 이 메트릭에 대한 분산 임계값이 3이라고 가정해 봅니다. 따라서 위 예제에서는 클러스터를 균형이 맞는 것으로 간주하고, 클러스터 Resource Manager에서 이 사실을 확인해도 분산이 트리거되지 않습니다. 클러스터의 비율은 5/2 = 2.5이고 3의 지정된 분산 임계값보다 작기 때문입니다.

아래 예제에서 노드의 최대 부하는 10이지만 최소 부하는 2이므로 비율은 5가 됩니다. 따라서 클러스터는 해당 메트릭에 대해 지정된 분산 임계값 3을 초과합니다. 결과적으로 다음번에 분산 타이머가 실행될 때 전역 균형 조정이 실행됩니다. 분산 검색이 시작되었다고 해서 바로 이동이 진행되는 것은 아닙니다. 클러스터 부하가 불균형 상태여도 상황이 개선될 수 없는 경우가 있습니다. 그렇지만 이와 같은 상황에서는(적어도 기본적으로) 부하가 거의 확실하게 Node3으로 분산됩니다. greedy 접근 방식을 사용하지 않으므로 노드 간의 전체 차이를 최소화하기 위해 일부 부하는 Node2로 분산되지만 대부분의 부하는 Node3으로 이동할 것으로 예상합니다.

![분산 임계값 예제 작업][Image2]

아래 분산 임계값을 가져오는 것이 명시적 목표가 아닙니다. 분산 임계값은 단지 클러스터 Resource Manager에게 개선할 수 있는 사항(있는 경우)을 결정할 클러스터를 살펴보라고 알려 주는 *트리거*일 뿐입니다.

## <a name="activity-thresholds"></a>활동 임계값
경우에 따라 노드가 상대적으로 불균형하기도 하지만 클러스터의 *총* 부하량은 낮습니다. 이러한 현상은 시간이기 때문이거나 클러스터가 새롭고 이제 막 부트스트랩되기 때문에 발생합니다. 두 경우 모두, 실제 얻는 것이 거의 없으므로 클러스터에서 부하를 분산하는 데 시간을 낭비하고 싶지 않을 것입니다. 대신, 절대적인 차이를 발생하지 않으면서 네트워크와 계산 리소스를 사용하여 부하를 이동시키려 할 것입니다. 이렇게 하지 않으려는 경우 활동 임계값으로 알려진 Resource Manager 내부의 다른 컨트롤을 통해 활동에 대한 일부 절대 하한값을 지정할 수 있습니다. 이 정도 부하량을 가진 노드가 없는 경우 조정 임계값을 충족하더라도 분산이 트리거되지 않습니다.

예를 들어 이러한 노드의 총 소비량이 포함된 보고서가 있다고 가정해 봅시다. 또한 이 메트릭에 대해 분산 임계값이 3으로 유지되었지만, 활동 임계값이 이제 1536이 되었다고 가정합니다. 첫 번째 경우에서 분산 임계값에 따라 클러스터가 불균형하지만 최소 활동 임계값을 충족하는 노드가 없기 때문에 그대로 둡니다. 아래 예제에서 Node1은 활동 임계값을 훨씬 초과했기 때문에 분산이 수행됩니다(메트릭에 대한 분산 임계값 및 활동 임계값이 둘 다 초과하므로).

![활동 임계값 예][Image3]

분산 임계값과 마찬가지로 활동 임계값은 클러스터 정의를 통한 메트릭을 기준으로 정의됩니다.

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

분산 및 활동 임계값은 모두 메트릭에 연결됩니다. 동일한 메트릭에 분산 및 활동 임계값 모두를 초과하는 경우에만 분산이 트리거됩니다. 따라서 CPU에 대한 활동 임계값 및 메모리에 대한 분산 임계값을 초과하는 경우 나머지 임계값(CPU에 대한 분산 임계값 및 메모리에 대한 활동 임계값)을 초과하지 않는 한, 분산이 트리거되지 않습니다.

## <a name="balancing-services-together"></a>분산 서비스 함께 사용
클러스터가 불균형 상태인지 여부는 클러스터 전체의 결정이라는 점이 흥미롭지만, 이를 수정하는 방법은 개별 서비스 복제본과 인스턴스가 이동하도록 하는 것입니다. 참 합리적이지 않은가요? 한 노드에 메모리가 누적된 경우, 여러 복제본 또는 인스턴스가 원인일 수 있으므로 영향을 받고 불균형한 메트릭을 사용하는 모든 상태 저장 복제본 또는 상태 비저장 인스턴스를 이동시킬 수 있습니다.

경우에 따라 고객이 우리에게 전화를 하거나 불균형되지 않은 서비스가 이동되었다고 알려주는 티켓을 제출할 것입니다. 다른 불균형 시간에서 모든 해당 서비스의 메트릭이 고르게 분산되었다고 해도 서비스가 어떻게 완벽하게 계속 작동될 수 있나요? 확인해 보겠습니다!

4개의 서비스, Service1, Service2, Service3 및 Service4를 예로 들어 보겠습니다. Service1은 Metric1 및 Metric2에 대해 보고하고, Service2는 Metric2 및 Mmetric3, Service3은 Metric3 및 Metric4, Service4는 Metric99에 대해 보고합니다. 물론 여기서 우리가 가려는 곳을 볼 수 있습니다. 우리에게는 체인이 있습니다! 클러스터 Resource Manager의 관점에서 볼 때 우리는 실제로 네 개의 서비스를 각각 가지고 있지 않고, 해당 서비스(Service1, Service2, Service3)의 한 묶음이 있고 하나는 자체적으로 꺼져 있습니다.

![분산 서비스 함께 사용][Image4]

따라서 Metric1의 불균형으로 인해 Service3에 속하는 복제본 또는 인스턴스가 이동하게 할 수 있습니다. 일반적으로 이러한 움직임은 아주 제한되어 있지만 Metric1이 정확히 얼마나 불균형되었는지 그리고 클러스터에서 이를 수정하기 위해 어떤 변경이 필요한지에 따라 커질 수 있습니다. 메트릭 1, 2 또는 3의 불균형이 Service4에서의 움직임을 허용하지 않을 것이라고 확실히 말할 수 있습니다. Service4에 속하는 복제본 또는 인스턴스를 이동시키는 것은 메트릭 1, 2 또는 3의 균형에 아무런 영향을 주지 않기 때문에 의미가 없습니다.

서비스가 추가 또는 제거되거나 해당 서비스의 메트릭 구성이 변경되었기 때문에 클러스터 Resource Manager는 어떤 서비스와 관련되어 있는지 자동으로 파악합니다. 예를 들어, 분산을 두 번 실행하는 동안 Service2가 다시 구성되어 Metric2가 제거됩니다. 이를 통해 Service1와 Service2 사이의 체인이 끊깁니다. 이제 두 개의 서비스 그룹 대신, 세 개의 서비스 그룹을 갖게 됩니다.

![분산 서비스 함께 사용][Image5]

## <a name="next-steps"></a>다음 단계
* 메트릭은 서비스 패브릭 클러스터 리소스 관리자가 클러스터의 소비와 용량을 관리하는 방법입니다. 메트릭 및 구성 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)
* 이동 비용은 특정 서비스가 다른 서비스에 비해 이동하는 데 비용이 더 많이 드는 것을 클러스터 리소스 관리자에게 알리는 한 가지 방법입니다. 이동 비용에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-movement-cost.md)
* 클러스터 리소스 관리자에는 클러스터에서 이탈을 늦추도록 구성할 수 있는 몇 가지 제한이 있습니다. 일반적으로 필요하지는 않지만 필요할 경우 [여기](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Nov16_HO3-->


