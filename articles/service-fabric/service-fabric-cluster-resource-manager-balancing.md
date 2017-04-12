---
title: "Azure Service Fabric 클러스터 분산 | Microsoft Docs"
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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: ee77a01b2bb17ab70099e891e00255d1cec676f6


---
# <a name="balancing-your-service-fabric-cluster"></a>서비스 패브릭 클러스터 분산
Service Fabric Cluster Resource Manager에서는 동적 로드 변경, 노드나 서비스의 추가 또는 제거에 대응, 제약 위반 수정 및 클러스터 분산 변경을 지원합니다. 그렇지만 이러한 작업이 얼마나 자주 수행되며 어떤 요인으로 시작될까요?

균형 조정과 관련된 첫 번째 컨트롤 집합은 타이머 집합입니다. 이러한 타이머는 클러스터 Resource Manager에서 해결해야 하는 항목에 대한 클러스터의 상태를 검사하는 빈도를 제어합니다. 각각 고유한 자체 타이머를 포함하는 세 가지 작업 범주가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

1. 배치 – 이 단계는 상태 저장 복제본 또는 누락된 상태 비저장 인스턴스 배치를 처리합니다. 여기에는 새로운 서비스와, 상태 저장 복제본 또는 실패한 상태 비저장 인스턴스를 모두 다룹니다. 복제본 또는 인스턴스 삭제와 제거도 여기에서 다룹니다.
2. 제약 조건 검사 - 이 단계에서는 시스템 내에서 여러 배치 제약 조건(규칙)을 검사하고 위반을 수정합니다. 규칙의 예로는 노드 용량을 초과하지 않고 서비스 배치 제약 조건을 충족하도록 하는 것을 들 수 있습니다.
3. 분산 - 다른 메트릭에 대한 분산의 원하는 구성 수준에 따라 사전 분산 변경이 필요한지 확인합니다. 필요한 경우 더 균형이 맞는 클러스터에서 배치를 찾습니다.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>클러스터 리소스 관리자 단계 및 타이머 구성
Cluster Resource Manager에서 수행할 수 있는 이러한 각 형식의 수정 작업은 해당 빈도를 제어하는 다른 타이머에 의해 제어됩니다. 각 타이머가 실행되면 작업이 예약됩니다. 기본적으로 Resource Manager는

* 1/10초마다 상태를 검색하고 업데이트를 적용합니다(예: 노드가 다운된 기록).
* 배치 및 제약 조건 확인 플래그를&1;초마다 설정합니다.
* 분산 플래그를&5;초마다 설정합니다.

다음 구성 정보에서 이것이 반영된 것을 확인할 수 있습니다.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json, Azure 호스티드 클러스터의 경우 Template.json을 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

현재는 Cluster Resource Manager는 이 작업 중 하나를 한 번에 하나씩 순차적으로 수행합니다(이에 따라 이러한 타이머를 "최소 간격"이라고 지칭함). 예를 들어 클러스터 분산에 앞서 서비스를 만들기 위해 Cluster Resource Manager가 보류 중인 요청을 처리합니다. 지정한 기본 타이머 간격에서 볼 수 있듯이 Cluster Resource Manager는 필요한 모든 항목을 자주 확인하므로 각 단계의 마지막에 수행되는 변경 사항의 집합이 보통은 작습니다. 작게 자주 변경하면 Cluster Resource Manager가 클러스터에서 일어나는 일에 민첩하게 반응할 수 있습니다. 여러 동일한 형식의 이벤트는 동시에 발생하는 경향이 있으므로 기본 타이머가 일부 일괄 처리를 제공합니다. 기본적으로 Cluster Resource Manager는 클러스터에서 변경 시간 내내 검색하는 것이 아니라 모든 변경을 한꺼번에 처리합니다. 이렇게 하면 갑작스러운 이탈이 발생하게 됩니다.

Cluster Resource Manager는 클러스터의 분산 여부를 판단하기 위해 추가적인 정보도 필요로 합니다. 이를 위해 *분산 임계값*과 *활동 임계값* 등의 두 가지 구성이 있습니다.

## <a name="balancing-thresholds"></a>분산 임계값
분산 임계값은 사전 균형 조정을 트리거하기 위한 주요 컨트롤입니다. MinLoadBalancingInterval 간격 타이머는 단순히 Cluster Resource Manager가 확인하는 주기를 의미하며 특정 작업이 발생한 것은 아닙니다. 분산 임계값은 특정 메트릭에 대해 클러스터가 얼마나 불균형한지를 정의하여 클러스터 Resource Manager에서 클러스터가 불균형하다고 간주하고 분산을 트리거하도록 합니다.

분산 임계값은 클러스터 정의의 일부로서 메트릭 단위로 정의됩니다. 메트릭에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 확인하세요.

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json, Azure 호스티드 클러스터의 경우 Template.json을 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

메트릭에 대한 분산 임계값은 비율입니다. 가장 부하가 많은 노드의 부하의 양을 가장 부하가 적은 노드의 부하의 양으로 나눈 값이 이 비율을 초과하는 경우, 클러스터가 불균형 상태라고 간주됩니다. 결과적으로 Cluster Resource Manager가 다음 번에 확인할 때 분산이 트리거됩니다.

<center>
![분산 임계값 예][Image1]
</center>

이 예제에서는 각 서비스에서 한 단위의 메트릭을 사용합니다. 위 예제에서 노드의 최대 부하는&5;이고 최소 부하는&2;입니다. 이 메트릭에 대한 분산 임계값이&3;이라고 가정해 봅니다. 클러스터의 비율이 5/2 = 2.5이며 지정된 분산 임계값인 3보다 낮으므로 클러스터는 균형이 맞습니다. Cluster Resource Manager가 확인할 때 분산이 트리거되지 않습니다.

아래 예제에서 노드의 최대 부하는&10;이지만 최소는&2;이므로 비율은&5;입니다. 5는 해당 메트릭에 지정된 분산 임계값인&3;보다 큽니다. 결과적으로 다음번에 분산 타이머가 실행될 때 분산 변경 실행이 예약됩니다. 이런 상황에서는 일부 부하가 거의 확실히 노드&3;에 배포됩니다. Service Fabric Cluster Resource Manager는 무리하는 방식을 사용하지 않으므로 일부 부하가 노드&2;에도 분산됩니다. 이렇게 하면 노드 간에 전반적인 차이를 최소화할 수 있으며 이것이 Cluster Resource Manager의 목표 중 하나이기도 합니다.

<center>
![분산 임계값 예제 작업][Image2]
</center>

분산 임계값 이하를 달성하는 것이 명시적 목표는 아닙니다. 분산 임계값은 단지 클러스터 Resource Manager에게 개선할 수 있는 사항(있는 경우)을 결정할 클러스터를 살펴보라고 알려 주는 *트리거*일 뿐입니다. 사실 분산 검색이 실행되었다고 해서 모든 것이 바뀌는 것은 아닙니다. 어떤 경우 클러스터 균형이 맞지 않아도 개선하지 못할 수 있습니다.

## <a name="activity-thresholds"></a>활동 임계값
경우에 따라 노드가 상대적으로 불균형하기도 하지만 클러스터의 *총* 부하량은 낮습니다. 이러한 현상은 일시적인 감소이거나, 새 클러스터가 막 부트스트랩되었기 때문에 발생합니다. 어느 경우든 얻을 수 있는 이득이 거의 없기 때문에 클러스터 분산에 시간을 투자하지 않으려 할 수 있습니다. 클러스터가 분산을 거쳤다는 것은 *확연한* 차이 없이 네트워크와 연산 리소스를 사용하여 변화를 준 것입니다. 이를 방지하기 위해 활동 임계값이라고 하는 다른 제어가 있습니다. 활동 임계값을 사용하면 활동에 대해 일부 절대 하한값을 지정할 수 있습니다. 이 임계값을 초과하는 노드가 없으면 분산 임계값에 도달해도 분산이 트리거되지 않습니다.

예제로 다음 다이어그램을 살펴보겠습니다. 또한 이 메트릭에 대해 분산 임계값이 3으로 유지되었지만, 활동 임계값이 이제 1536이 되었다고 가정합니다. 첫 번째 경우에서 분산 임계값에 따르면 클러스터는 불균형 상태이지만 활동 임계값을 충족하는 노드가 없으므로 아무 일도 발생하지 않습니다. 아래 예제에서 노드&1;은 활동 임계값을 초과했습니다. 메트릭에 대한 분산 임계값과 활동 임계값이 모두 초과되었으므로 분산이 예약됩니다.

<center>
![활동 임계값 예][Image3]
</center>

분산 임계값과 마찬가지로 활동 임계값은 클러스터 정의를 통한 메트릭을 기준으로 정의됩니다.

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json, Azure 호스티드 클러스터의 경우 Template.json을 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

분산 및 활동 임계값은 모두 특정 메트릭에 연결됩니다. 분산은 동일한 메트릭에 대해 분산 임계값과 활동 임계값을 모두 초과한 경우에만 트리거됩니다.

## <a name="balancing-services-together"></a>분산 서비스 함께 사용
클러스터의 불균형 여부가 클러스터 전체에 해당하는 결정이 아니라는 점이 흥미롭습니다. 그러나 해결하는 방법은 개별 서비스 복제본과 인스턴스를 움직이는 것입니다. 참 합리적이지 않은가요? 메모리가 한 노드에 쌓여 있다면 여러 복제본이나 인스턴스가 여기에 참여할 수 있습니다. 불균형을 해결하려면 불균형 상태인 메트릭을 사용하는 상태 저장 복제본이나 상태 비저장 인스턴스를 움직여야 합니다.

경우에 따라 불균형 상태가 아닌 서비스가 움직이는 경우도 있습니다. 다른 불균형 시간에서 모든 해당 서비스의 메트릭이 고르게 분산되었다고 해도 서비스가 어떻게 완벽하게 계속 작동될 수 있나요? 확인해 보겠습니다!

4개의 서비스, Service1, Service2, Service3 및 Service4를 예로 들어 보겠습니다. Service1은 Metric1 및 Metric2에 대해 보고하고, Service2는 Metric2 및 Mmetric3, Service3은 Metric3 및 Metric4, Service4는 Metric99에 대해 보고합니다. 물론 여기서 우리가 가려는 곳을 볼 수 있습니다. 우리에게는 체인이 있습니다! 4개의 독립된 서비스가 실제로 있는 것이 아니라, 해당 서비스(Service1, Service2, Service3)의 한 묶음이 있고 하나는 자체적으로 꺼져 있습니다.

<center>
![분산 서비스 함께 사용][Image4]
</center>

따라서 Metric1의 불균형으로 인해 Service3(Metric1을 보고하지 않음)에 속하는 복제본 또는 인스턴스가 이동하게 할 수 있습니다. 일반적으로 이러한 움직임은 제한되어 있지만 Metric1이 정확히 얼마나 불균형되었는지 그리고 클러스터에서 이를 수정하기 위해 어떤 변경이 필요한지에 따라 커질 수 있습니다. 메트릭 1, 2, 3에서의 불균형이 Service4에서의 이동을 초래하지 않는다고 확신할 수 있습니다. Service4에 속한 복제본이나 인스턴스를 움직여도 메트릭 1, 2 또는 3의 균형에 거의 영향이 없기 때문에 의미가 없습니다.

서비스가 추가 또는 제거되거나 해당 메트릭 구성이 변경되었을 수 있기 때문에 Cluster Resource Manager는 어떤 서비스와 관련되어 있는지 자동으로 파악합니다. 예를 들어 Service2의 두 분산 실행 사이에 재구성되어 Metric2가 제거되었을 수 있습니다. 그러면 Service1와 Service2 사이의 체인이 끊깁니다. 이제 관련 서비스의 두 그룹이 아니라&3;개가 됩니다.

<center>
![분산 서비스 함께 사용][Image5]
</center>

## <a name="next-steps"></a>다음 단계
* 메트릭은 서비스 패브릭 클러스터 리소스 관리자가 클러스터의 소비와 용량을 관리하는 방법입니다. 메트릭 및 구성 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.
* 이동 비용은 특정 서비스가 다른 서비스에 비해 이동하는 데 비용이 더 많이 드는 것을 클러스터 리소스 관리자에게 알리는 한 가지 방법입니다. 이동 비용에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-movement-cost.md)를 참조하세요.
* 클러스터 리소스 관리자에는 클러스터에서 이탈을 늦추도록 구성할 수 있는 몇 가지 제한이 있습니다. 일반적으로 필요하지는 않지만 필요할 경우 [여기](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Jan17_HO4-->


