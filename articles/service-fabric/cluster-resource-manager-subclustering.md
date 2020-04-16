---
title: 하위 클러스터 메트릭의 균형 조정
description: 배치 제약 조건이 균형 조정에 미치는 영향 및 처리 방법
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430644"
---
# <a name="balancing-of-subclustered-metrics"></a>하위 클러스터 메트릭의 균형 조정

## <a name="what-is-subclustering"></a>하위 클러스터링이란 무엇입니까?

하위 클러스터링은 배치 제약 조건이 다른 서비스에 공통 메트릭이 있고 둘 다 로드를 보고할 때 발생합니다. 서비스에서 보고하는 로드가 크게 다른 경우 노드의 총 부하가 큰 표준 편차를 가지며 최상의 균형이 있더라도 클러스터가 불균형한 것처럼 보입니다.

## <a name="how-subclustering-affects-load-balancing"></a>하위 클러스터링이 부하 분산에 미치는 영향

다른 노드의 서비스에서 보고하는 로드가 크게 다른 경우 아무 것도 없는 큰 불균형이 있는 것처럼 보일 수 있습니다. 또한 하위 클러스터링으로 인한 잘못된 불균형이 실제 불균형보다 크면 리소스 관리자 분산 알고리즘을 혼동하고 클러스터에서 최적이 아닌 균형을 생성할 수 있습니다.

예를 들어 4개의 서비스가 있고 모두 메트릭1에 대한 부하를 보고한다고 가정해 보겠습니다.

* 서비스 A - 배치 제약 조건 "NodeType==Type1"이 있으며 10의 부하를 보고합니다.
* 서비스 B – 배치 제약 조건 "NodeType==Type1"이 있으며 10의 부하를 보고합니다.
* 서비스 C – 배치 제약 조건 "NodeType==Type2"가 있으며 100의 부하를 보고합니다.
* 서비스 D – 배치 제약 조건 "NodeType==Type2"가 있으며 100의 부하를 보고합니다.
* 그리고 우리는 네 개의 노드가 있습니다. 그 중 두 가지는 NodeType이 "Type1"로 설정되고 다른 두 개는 "Type2"입니다.

그리고 우리는 다음과 같은 배치가 있습니다 :

<center>

![하위 클러스터 배치 예제][Image1]
</center>

클러스터가 불균형해 보일 수 있으며 노드 3과 4에 큰 부하가 있지만 이 배치는 이 상황에서 최상의 균형을 만듭니다.

리소스 관리자는 하위 클러스터 링 상황을 인식할 수 있으며 거의 모든 경우에 지정된 상황에 대한 최적의 균형을 생성할 수 있습니다.

Resource Manager가 하위 클러스터메트릭의 균형을 최적으로 조정할 수 없는 예외적인 경우 하위 클러스터링을 계속 검색하고 문제를 해결하라는 조언을 제공하는 상태 보고서를 생성합니다.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>하위 클러스터링 유형 및 처리 방법

하위 클러스터링 상황은 세 가지 범주로 분류할 수 있습니다. 특정 하위 클러스터링 상황의 범주에 따라 리소스 관리자가 처리하는 방법을 결정합니다.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>첫 번째 범주 - 분리 노드 그룹이 있는 플랫 하위 클러스터링

이 범주에는 노드를 서로 다른 그룹으로 구분할 수 있는 가장 간단한 하위 클러스터링 형식이 있으며 각 서비스는 해당 그룹 중 하나의 노드에만 배치할 수 있습니다. 각 노드는 하나의 그룹과 한 그룹에만 속합니다. 위에서 설명한 상황은 대부분의 하위 클러스터링 상황과 마찬가지로 이 범주에 속합니다. 

이 범주의 상황에 대 한 리소스 관리자는 최적의 균형을 생성할 수 있습니다 및 더 이상 개입 이 필요 하지 않습니다.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>두 번째 범주 – 계층 적 노드 그룹과 하위 클러스터링

이 상황은 한 서비스에 허용되는 노드 그룹이 다른 서비스에 허용되는 노드 그룹의 하위 집합인 경우에 발생합니다. 이 상황의 가장 일반적인 예는 일부 서비스에 배치 제약 조건이 정의되고 다른 서비스에 배치 제약 조건이 없으며 모든 노드에 배치할 수 있는 경우입니다.

예제:

* 서비스 A: 배치 제약 조건 없음
* 서비스 B: 배치 제약 조건 "노드 유형==Type1"
* 서비스 C: 배치 제약 조건 "노드 유형==Type2"

이 구성은 다른 서비스에 대해 노드 그룹 간에 하위 집합-상위 집합 관계를 만듭니다.

<center>

![하위 집합 하위 집합 하위 클러스터][Image2]
</center>

이 경우 최적이 아닌 밸런스가 만들어질 가능성이 있습니다.

리소스 관리자는 이러한 상황을 인식하고 서비스 A를 Type1 노드에 배치할 수 있는 서비스 A1과 Type2 노드에 배치할 수 있는 서비스 A1로 분할하는 상태 보고서를 생성합니다. 이렇게 하면 최적의 균형을 이룰 수 있는 첫 번째 범주 상황으로 돌아갑니다.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>세 번째 범주 - 노드 집합 간에 부분 중복이 있는 하위 클러스터링

이 상황은 일부 서비스를 배치할 수 있는 노드 집합 간에 부분적으로 겹치는 경우에 발생합니다.

예를 들어 NodeColor라는 노드 속성이 있고 세 개의 노드가 있는 경우:

* 노드 1: 노드컬러=빨간색
* 노드 2: 노드컬러=파란색
* 노드 2: 노드색상=녹색

그리고 우리는 두 가지 서비스가 있습니다 :

* 서비스 A: 배치 제약 조건 "색상==빨간색 | 색상==파란색"
* 서비스 B: 배치 제약 조건 "색상==파란색 || 색상==녹색"

따라서 서비스 A를 노드 1과 2에 배치할 수 있으며 서비스 B는 노드 2와 3에 배치할 수 있습니다.

이 경우 최적이 아닌 밸런스가 만들어질 가능성이 있습니다.

리소스 관리자는 이 상황을 인식하고 일부 서비스를 분할하도록 권장하는 상태 보고서를 생성합니다.

이 경우 리소스 관리자는 여러 분할을 수행할 수 있고 서비스를 분할하는 데 가장 적합한 방법을 예측할 수 없으므로 서비스를 분할하는 방법을 제안할 수 없습니다.

## <a name="configuring-subclustering"></a>하위 클러스터 구성

하위 클러스터링에 대한 리소스 관리자의 동작은 다음 구성 매개 변수를 수정하여 수정할 수 있습니다.
* 하위 클러스터링Enabled - 매개 변수는 리소스 관리자가 부하 분산을 수행할 때 하위 클러스터링을 고려할지 여부를 결정합니다. 이 매개 변수가 꺼져 있으면 리소스 관리자는 하위 클러스터링을 무시하고 전역 수준에서 최적의 균형을 달성하려고 시도합니다. 이 매개 변수의 기본값은 false입니다.
* 하위 클러스터링신고정책 - 리소스 관리자가 계층적 및 부분 적중 하위 클러스터링에 대한 상태 보고서를 내하는 방법을 결정합니다. 값이 0이면 하위 클러스터링에 대한 상태 보고서가 꺼져 있고"1"은 최적이 아닌 하위 클러스터링 상황에 대해 경고 상태 보고서가 생성되고 "2" 값이 "확인" 상태 보고서를 생성한다는 것을 의미합니다. 이 매개 변수의 기본값은 "1"입니다.

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
* 특정 노드에만 서비스를 배치하도록 제한할 수 있는 방법에 대해 알아보려면 [노드 속성 및 배치 제약 조건을](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) 참조하십시오.

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
