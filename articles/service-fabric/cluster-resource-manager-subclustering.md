---
title: 하위 클러스터형 메트릭 분산
description: 분산에 대한 배치 제약 조건의 효과 및 처리 방법
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "82183122"
---
# <a name="balancing-of-subclustered-metrics"></a>하위 클러스터형 메트릭 분산

## <a name="what-is-subclustering"></a>하위 클러스터링이란?

하위 클러스터링은 다른 배치 제약 조건을 사용하는 서비스에 공통 메트릭이 있고 모두 이에 대한 부하를 보고하는 경우에 발생합니다. 서비스에서 보고하는 부하가 현저하게 다른 경우 노드의 총 부하는 표준 편차가 크고 가능한 최적의 분산을 가지고 있더라도 클러스터가 불균형한 것처럼 보입니다.

## <a name="how-subclustering-affects-load-balancing"></a>하위 클러스터링이 부하 분산에 미치는 영향

서로 다른 노드의 서비스에서 보고하는 부하가 현저하게 다른 경우에는 큰 불균형이 없더라도 있는 것처럼 보일 수 있습니다. 또한 하위 클러스터링으로 인해 발생하는 거짓 불균형이 실제 불균형보다 큰 경우 잠재적으로 Resource Manager 분산 알고리즘을 혼동하게 하고 클러스터에서 최적이 아닌 분산을 생성할 수 있습니다.

예를 들어 4개의 서비스가 있고 모두 메트릭 Metric1에 대한 부하를 보고한다고 가정합니다.

* 서비스 A – 배치 제약 조건 “NodeType==Frontend”가 있으며 10의 부하를 보고합니다.
* 서비스 B – 배치 제약 조건 “NodeType==Frontend”가 있으며 10의 부하를 보고합니다.
* 서비스 C – 배치 제약 조건 “NodeType==Backend”가 있으며 100의 부하를 보고합니다.
* 서비스 D – 배치 제약 조건 “NodeType==Backend”가 있으며 100의 부하를 보고합니다.
* 4개의 노드가 있습니다. 이들 중 두 가지는 NodeType이 “Frontend”로, 다른 두 가지는 “Backend”로 설정되어 있습니다.

그리고 다음과 같은 배치가 있습니다.

<center>

![하위 클러스터형 배치 예제][Image1]
</center>

노드 3 및 4에 대한 부하가 커서 클러스터가 불균형하게 보일 수 있습니다. 그러나 이러한 배치는 해당 상황에서 최적의 균형을 만듭니다.

Resource Manager는 하위 클러스터링 상황을 인식하고 거의 모든 경우에 주어진 상황에 대해 최적의 균형을 생산할 수 있습니다.

Resource Manager에서 하위 클러스터형 메트릭의 최적의 균형을 맞출 수 없는 일부 예외적인 상황에서도 여전히 하위 클러스터링을 검색하며 문제를 해결하는 데 도움이 되는 상태 보고서를 생성합니다.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>하위 클러스터링 유형 및 처리 방법

하위 클러스터링 상황은 세 가지 범주로 분류할 수 있습니다. 특정 클러스터링 상황의 범주는 Resource Manager에서 처리되는 방법을 결정합니다.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>첫 번째 범주 – 비연속 노드 그룹을 사용하는 플랫 하위 클러스터링

해당 범주에는 노드가 여러 그룹으로 나누어질 수 있는 가장 간단한 형식의 하위 클러스터링이 있으며 각 서비스는 이러한 그룹 중 하나의 노드에만 배치될 수 있습니다. 각 노드는 하나의 그룹에만 속합니다. 위에서 설명한 상황은 대부분의 하위 클러스터링 상황과 마찬가지로 이 범주에 속합니다. 

해당 범주에 있는 상황에서 Resource Manager는 최적의 균형을 생성할 수 있으며 추가 작업이 필요하지 않습니다.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>두 번째 범주 – 계층 노드 그룹이 포함된 하위 클러스터링

한 서비스에 허용되는 노드 그룹이 다른 서비스에 허용되는 노드 그룹의 하위 집합인 경우 이러한 상황이 발생합니다. 해당 상황의 가장 일반적인 예는 일부 서비스에 정의된 배치 제약 조건이 있고 다른 서비스에는 배치 제약 조건이 없으며 모든 노드에 배치할 수 있는 경우입니다.

예:

* Service A: 배치 제약 조건 없음
* Service B: 배치 제약 조건 “NodeType==Frontend”
* Service C: 배치 제약 조건 “NodeType==Backend”

해당 구성은 서로 다른 서비스에 대한 노드 그룹 간에 하위 집합-상위 집합 관계를 만듭니다.

<center>

![하위 집합 상위 집합 하위 클러스터][Image2]
</center>

이 경우 최적이 아닌 균형이 달성될 수 있습니다.

Resource Manager는 이러한 상황을 인식하고 서비스 A를 프런트 엔드 노드에 배치할 수 있는 Service A1과 백 엔드 노드에 배치할 수 있는 서비스 A2로 분할할 것을 제안하는 상태 보고서를 생성합니다. 이렇게 하면 최적 균형을 달성할 수 있는 첫 번째 범주 상황으로 다시 돌아갑니다.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>세 번째 범주 – 노드 집합 간에 부분적으로 겹치는 하위 클러스터링

해당 상황은 일부 서비스를 배치할 수 있는 노드 집합 간에 부분적으로 겹치는 경우에 발생합니다.

예를 들어 NodeColor라는 노드 속성이 있고 3개의 노드가 있습니다.

* Node 1: NodeColor=Red
* Node 2: NodeColor=Blue
* Node 2: NodeColor=Green

그리고 다음과 같은 두 가지 서비스가 있습니다.

* Service A: “Color==Red || Color==Blue” 배치 제약 조건 사용
* Service B: “Color==Blue || Color==Green” 배치 제약 조건 사용

이로 인해 서비스 A는 노드 1 및 2에 배치될 수 있으며 서비스 B는 노드 2 및 3에 배치될 수 있습니다.

이 경우 최적이 아닌 균형이 달성될 수 있습니다.

Resource Manager는 이러한 상황을 인식하고 일부 서비스를 분할할 것을 제안하는 상태 보고서를 생성합니다.

해당 상황에서는 여러 분할을 수행할 수 있고 서비스를 분할하는 데 가장 적합한 방법을 예상할 방법이 없으므로 Resource Manager가 서비스 분할 방법을 제안할 수 없습니다.

## <a name="configuring-subclustering"></a>하위 클러스터링 구성

하위 클러스터링에 대한 Resource Manager 동작은 다음 구성 매개 변수 수정을 통해 수정할 수 있습니다.
* SubclusteringEnabled - 부하 분산을 수행할 때 Resource Manager에서 하위 클러스터링을 사용할지 여부를 결정하는 매개 변수입니다. 해당 매개 변수가 해제되어 있으면 Resource Manager는 하위 클러스터링을 무시하고 전역 수준에서 최적의 균형을 달성하려고 합니다. 해당 매개 변수의 기본값은 False입니다.
* SubclusteringReportingPolicy - Resource Manager가 계층 구조 및 부분 중복 하위 클러스터링에 대한 상태 보고서를 내보내는 방법을 결정합니다. 값이 0이면 하위 클러스터링에 대한 상태 보고서가 해제되었음을 의미하고, “1”은 경고 상태 보고서가 최적이 아닌 하위 클러스터링 상황에 대해 생성됨을 의미하며 값이 “2”면 “정상” 상태 보고서를 생성합니다. 해당 매개 변수의 기본값은 “1”입니다.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>다음 단계
* 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)
* 서비스가 특정 노드에만 배치되도록 제한하는 방법에 대한 자세한 내용은 [노드 속성 및 배치 제약 조건](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)을 참조하세요.

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
