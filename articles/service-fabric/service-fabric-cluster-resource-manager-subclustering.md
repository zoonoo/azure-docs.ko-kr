---
title: 클러스터형 하위 메트릭 분산
description: 분산에 대 한 배치 제약 조건의 효과 및 처리 방법
author: nipavlo
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: nipavlo
ms.openlocfilehash: fe41f45399670d5ac003e5da6c8da0fb1e0847b2
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081664"
---
# <a name="balancing-of-subclustered-metrics"></a>클러스터형 하위 메트릭 분산

## <a name="what-is-subclustering"></a>Subclustering 이란?

하위 클러스터링은 다른 배치 제약 조건을 사용 하는 서비스에 공통 메트릭이 있고이에 대 한 로드를 모두 보고 하는 경우에 발생 합니다. 서비스에서 보고 하는 부하가 현저 하 게 다른 경우 노드의 총 부하는 높은 표준 편차를 가지 며, 가능한 균형을 가진 경우에도 클러스터의 균형이 맞지 않는 것 처럼 보입니다.

## <a name="how-subclustering-affects-load-balancing"></a>Subclustering이 부하 분산에 미치는 영향

서로 다른 노드의 서비스에서 보고 하는 부하가 현저 하 게 다를 경우에는 없는 큰 불균형이 있는 것 처럼 보일 수 있습니다. 또한 subclustering으로 인해 발생 하는 거짓 불균형이 실제 불균형 보다 큰 경우 리소스 관리자 분산 알고리즘을 혼동 하 고 클러스터에서 최적의 잔액을 생성할 수 있습니다.

예를 들어 4 개의 서비스가 있고 모두 메트릭 Metric1에 대 한 부하를 보고 한다고 가정해 보겠습니다.

* 서비스 A – 배치 제약 조건 "NodeType = = Type1"이 있으며 10의 부하를 보고 합니다.
* 서비스 B – 배치 제약 조건 "NodeType = = Type1"이 있으며 10의 부하를 보고 합니다.
* 서비스 C – 배치 제약 조건 "NodeType = = Type2"가 있으며 100 부하를 보고 합니다.
* 서비스 D – 배치 제약 조건 "NodeType = = Type2"가 있으며 100 부하를 보고 합니다.
* 4 개의 노드가 있습니다. 둘 중 두 개의 NodeType은 "Type1"로 설정 되 고 다른 두 개는 "Type2"로 설정 됩니다.

그리고 다음과 같은 배치가 있습니다.

<center>

![Subclustered 배치 예제][Image1]
</center>

클러스터가 불균형 하 게 되 면 노드 3 및 4에 대 한 부하가 클 수 있지만이 경우 이러한 배치가 이러한 상황에서 최대한의 균형을 만듭니다.

리소스 관리자는 하위 클러스터링 상황을 인식 하 고, 거의 모든 경우에는 지정 된 상황에 대해 최적의 잔액을 생성할 수 있습니다.

리소스 관리자에서 하위 클러스터링 된 메트릭의 균형을 맞출 수 없는 몇 가지 예외적인 상황에서는 여전히 하위 클러스터링을 검색 하 고 문제를 해결 하는 데 도움이 되는 상태 보고서를 생성 합니다.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Subclustering 유형 및 처리 방법

Subclustering 상황은 세 가지 범주로 분류할 수 있습니다. 특정 subclustering 상태의 범주는 리소스 관리자에서 처리 되는 방법을 결정 합니다.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>첫 번째 범주 – 비연속 노드 그룹을 사용 하는 flat subclustering

이 범주에는 노드가 여러 그룹으로 분리 될 수 있는 가장 간단한 형태의 하위 클러스터링이 있으며 각 서비스는 이러한 그룹 중 하나의 노드에만 배치 될 수 있습니다. 각 노드는 하나의 그룹과 하나의 그룹에만 속합니다. 위에서 설명한 상황은 대부분의 하위 클러스터링 상황과 마찬가지로이 범주에 속합니다. 

이 범주에 해당 하는 경우 리소스 관리자는 최적의 잔액을 얻을 수 있으며 추가 작업이 필요 하지 않습니다.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>두 번째 범주 – 계층 노드 그룹이 포함 된 subclustering

한 서비스에 허용 되는 노드 그룹이 다른 서비스에 허용 되는 노드 그룹의 하위 집합인 경우 이러한 상황이 발생 합니다. 이러한 상황의 가장 일반적인 예는 일부 서비스가 배치 제약 조건을 정의 하 고 다른 서비스에 배치 제약 조건이 없고 모든 노드에 배치할 수 있는 경우입니다.

예제:

* 서비스 A: 배치 제약 조건 없음
* Service B: 배치 제약 조건 "NodeType = = Type1"
* 서비스 C: 배치 제약 조건 "NodeType = = Type2"

이 구성은 다양 한 서비스에 대 한 노드 그룹 간에 하위 집합-상위 집합 관계를 만듭니다.

<center>

![하위 집합 하위 집합 하위 클러스터][Image2]
</center>

이 경우 만족 스 럽 지 못할 가능성이 있습니다.

리소스 관리자은 이러한 상황을 인식 하 고, 서비스 A를 2 개의 서비스로 분할 하는 상태 보고서를 생성 합니다. 서비스 A 2는 Type1 노드 및 서비스 A2에 배치 될 수 있으며,이는 Type2 노드에 배치할 수 있습니다. 이렇게 하면 최적으로 균형을 조정할 수 있는 첫 번째 범주 상황으로 다시 전환 됩니다.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>세 번째 범주 – 노드 집합 간에 부분적으로 겹치는 subclustering

이러한 상황은 일부 서비스를 배치할 수 있는 노드 집합 간에 부분적으로 겹치는 경우에 발생 합니다.

예를 들어 NodeColor 라는 노드 속성이 있고 3 개의 노드가 있습니다.

* 노드 1: NodeColor = 빨강
* 노드 2: NodeColor = Blue
* 노드 2: NodeColor = Green

그리고 다음과 같은 두 가지 서비스가 있습니다.

* 서비스 A: 배치 제약 조건을 사용 하는 경우 "Color = = Red | | Color = = Blue "
* 서비스 B: 배치 제약 조건이 있는 경우 "Color = = Blue | | Color = = Green "

이로 인해 서비스 A는 노드 1과 2에 배치 될 수 있으며 B 서비스는 노드 2와 3에 배치 될 수 있습니다.

이 경우 만족 스 럽 지 못할 가능성이 있습니다.

리소스 관리자은 이러한 상황을 인식 하 고 일부 서비스를 분할 하는 데 도움이 되는 상태 보고서를 생성 합니다.

이러한 상황에서는 여러 분할을 수행할 수 있고 서비스를 분할 하는 데 가장 적합 한 방법을 예상할 수 있는 방법이 없기 때문에 리소스 관리자 서비스 분할 방법을 제안에 제공할 수 없습니다.

## <a name="configuring-subclustering"></a>Subclustering 구성

Subclustering에 대 한 리소스 관리자 동작은 다음 구성 매개 변수를 수정 하 여 수정할 수 있습니다.
* SubclusteringEnabled-매개 변수 리소스 관리자 부하 분산을 수행할 때 하위 클러스터링을 고려 하는지 여부를 결정 합니다. 이 매개 변수가 해제 되어 있으면 리소스 관리자 하위 클러스터링을 무시 하 고 전역 수준에서 최적의 균형을 유지 하려고 합니다. 이 매개 변수의 기본값은 false입니다.
* SubclusteringReportingPolicy-리소스 관리자 계층 구조 및 부분 중복 subclustering에 대 한 상태 보고서를 내보내는 방법을 결정 합니다. 값이 0 이면 하위 클러스터링에 대 한 상태 보고서가 해제 됨을 의미 하 고 "1"은 경고 상태 보고서가 최적이 아닌 하위 클러스터링 상황에 대해 생성 되 고 값이 "2" 이면 "OK" 상태 보고서를 생성 합니다. 이 매개 변수의 기본값은 "1"입니다.

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
* 특정 노드에만 배치 되도록 서비스를 제한 하는 방법에 대 한 자세한 내용은 [노드 속성 및 배치 제약 조건](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) 을 참조 하세요.

[Image1]:./media/service-fabric-cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/service-fabric-cluster-resource-manager-subclustering/subset-superset-nodes.png
