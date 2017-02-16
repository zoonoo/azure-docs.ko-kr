---
title: "Service Fabric 클러스터 리소스 관리자 - 관리 통합 | Microsoft Docs"
description: "클러스터 리소스 관리자과 서비스 패브릭 관리 간에 통합 지점의 개요입니다."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 53b97327e41c68fbbbe20b5bc5cf99bdae15e1f9


---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>클러스터 리소스 관리자와 서비스 패브릭 클러스터 관리 통합
서비스 패브릭 클러스터 리소스 관리자는 관리 작업(예: 응용 프로그램 업그레이드)을 처리하는 서비스 패브릭의 주요 구성 요소가 아니지만 관련이 있습니다. Cluster Resource Manager에서 관리에 유용하게 사용할 수 있는 첫 번째 방법은 내부에 있는 서비스와 클러스터의 필요한 상태를 추적하는 것입니다. Cluster Resource Manager는 원하는 구성에 클러스터를 배치할 수 없는 경우 상태 보고서를 보냅니다. 용량이 충분하지 않거나 서비스를 배치할 위치에 대한 규칙 충돌하는 경우를 예로 듭니다. 통합의 다른 부분은 업그레이드 작동 방법와 관련이 있습니다. 업그레이드 중에 Cluster Resource Manager는 해당 동작을 약간 변경합니다. 아래에서 이러한 통합 지점에 대해 설명하겠습니다.

## <a name="health-integration"></a>상태 통합
Cluster Resource Manager는 노드 및 클러스터에서 사용 가능한 서비스와 용량에 대해 정의한 규칙을 지속적으로 추적합니다. 해당 규칙을 충족할 수 없거나 용량이 부족한 경우 상태 경고 및 오류를 내보냅니다. 예를 들어, 노드가 용량을 초과하면 Cluster Resource Manager는 서비스를 이동하여 상황을 수정하려고 합니다. 상황을 수정할 수 없는 경우 용량을 초과한 노드와 해당 메트릭을 나타내는 상태 경고를 내보냅니다.

Resource Manager 상태 경고의 또 다른 예는 배치 제약 조건 위반에 대한 것입니다. 예를 들어, 배치 제약 조건(예: `“NodeColor == Blue”`)을 정의하고 Resource Manager가 해당 제약 조건 위반을 감지한 경우 상태 경고를 내보냅니다. 이는 사용자 지정 제약 조건 및 기본 제약 조건(예: 장애 도메인 및 업그레이드 도메인 제약 조건)에 적용됩니다.

이러한 상태 보고서의 예를 들면 다음과 같습니다. 이 경우에 상태 보고서는 시스템 서비스의 파티션 중 하나에 대한 내용입니다. 상태 메시지는 해당 파티션의 복제본이 너무 적은 업그레이드 도메인에 일시적으로 압축되었음을 나타냅니다.

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

상태 메시지는 다음과 같은 사항을 알려줍니다.

1. 모든 복제본은 자체로 정상입니다(서비스 패브릭의 첫 번째 우선 순위임).
2. 업그레이드 도메인 배포 제약 조건은 현재 위반되었습니다(특정 업그레이드 도메인에 이 파티션에 대한 복제본이 있어야 하는 것 보다 많음을 의미함).
3. 위반의 원인이 되는 복제본을 포함하는 노드(해당 노드 ID: 3d1a4a68b2592f55125328cd0f8ed477)
4. 보고서 생성 시기(2015/8/10 오후 7:13:02)

이런 정보로 인해 무언가 문제가 발생했음을 알려주기 위해 프로덕션에서 경고가 생성됩니다. 이 경우에 Resource Manager가 업그레이드 도메인에 복제본을 압축해야 했던 이유를 알아낼 수 있는지 확인하려고 합니다. 예를 들어 다른 업그레이드 도메인에서 노드가 중지되었기 때문일 수 있습니다.

서비스를 만들거나 Resource Manager가 일부 서비스를 배치할 수 있는 위치를 찾으려고 시도함에도 불구하고 해결할 수 있는 방법이 없습니다. 여러 가지 이유가 있을 수 있지만 일반적으로 다음 두 가지 조건 중 하나로 인한 것입니다.

1. 일부 일시적 조건 때문에 서비스 인스턴스 또는 복제본을 올바르게 배치할 수 없습니다.
2. 서비스의 요구 사항은 해당 요구 사항을 충족시킬 수 없는 방법으로 잘못 구성되어 있습니다.

이러한 각 조건에서 진행 상황 및 서비스를 배치할 수 없는 이유를 확인할 수 있는 정보를 제공하는 클러스터 Resource Manager의 상태 보고서가 표시됩니다. 이 프로세스는 "제약 조건 제거 시퀀스"라고 합니다. 이 프로세스를 실행하는 동안 서비스에 영향을 주는 구성된 제약 조건이 설명되고 제거한 항목이 기록됩니다. 이와 같이 서비스를 배치할 수 없는 경우 제거된 노드 및 원인을 확인할 수 있습니다.

## <a name="constraint-types"></a>제약 조건 형식
이러한 상태 보고서에서 확인할 수 있는 다양한 제약 조건을 살펴보겠습니다. 대부분의 경우 이러한 제약 조건은 소프트웨어에 있거나 기본적으로 최적화되므로 노드를 제거하지 않습니다. 그러나 이러한 제약 조건이 하드 제약 조건으로 구성되거나, 드문 경우지만 노드가 제거되도록 하는 경우 이러한 제약 조건과 관련된 상태 메시지가 표시될 수 있습니다.

* **ReplicaExclusionStatic** 및 **ReplicaExclusionDynamic** – 이 제약 조건은 동일한 파티션에서 두 개의 상태 저장 복제본 또는 상태 비저장 인스턴스를 동일한 노드에 배치해야 함을(허용되지 않음) 나타냅니다. ReplicaExclusionStatic 및 ReplicaExclusionDynamic은 거의 동일한 규칙입니다. ReplicaExclusionDynamic 제약 조건은 "유일하게 제안된 솔루션이 이미 여기에 복제본을 배치했기 때문에 이 복제본을 여기에 배치할 수 없습니다"라고 합니다. 제안된 충돌이 아닌 실제 충돌임을 나타내는 ReplicaExclusionStatic 제약 조건과 다릅니다. 이 경우에 복제본이 이미 노드에 있습니다. 혼란스러운가요? 예. 문제가 됩니까? 번호 ReplicaExclusionStatic 또는 ReplicaExclusionDynamic 제약 조건이 포함된 제약 조건 제거 시퀀스가 표시되는 경우 Cluster Resource Manager는 노드가 충분하지 않다고 여깁니다. 추가 제약 조건은 일반적으로 노드가 너무 적었다는 점을 알려줍니다.
* **PlacementConstraint**: 이 메시지가 표시되는 경우 서비스의 배치 제약 조건에 일치하지 않는 일부 노드를 제거했음을 의미합니다. 현재 구성된 배치 제약 조건을 이 메시지의 일부분으로 추적합니다. 배치 제약 조건이 정의된 경우 정상입니다. 그러나 너무 많은 노드를 제거하는 배치 제약 조건의 버그가 있는 경우 여기서 확인할 수 있습니다.
* **NodeCapacity**: 이 제약 조건은 이 방법이 노드의 용량을 초과할 수 있기 때문에 Cluster Resource Manager에서 복제본을 표시된 노드에 배치할 수 없었다는 의미입니다.
* **선호도**: 이 제약 조건은 선호도 제약 조건을 위반하기 때문에 영향을 받는 노드에 복제본을 배치할 수 없었다는 것을 나타냅니다. 선호도에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)에 있습니다.
* **FaultDomain** 및 **UpgradeDomain**: 이 제약 조건은 표시된 노드에 복제본을 배치하여 특정 장애 또는 업그레이드 도메인에서 압축이 일어나는 경우 노드를 제거합니다. 이 제약 조건을 설명하는 몇 가지 예는 [장애 및 업그레이드 도메인 제약 조건 및 결과 동작](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: 이 제약 조건이 기본적으로 최적화 수준에 설정되었기 때문에 일반적으로 솔루션에서 노드를 제거해서는 안됩니다. 또한 일반적으로 업그레이드하는 동안 기본 설정 위치 제약 조건이 나타납니다. 이 항목은 업그레이드하는 동안 복제본을 업그레이드 시작 시 원래 위치로 다시 이동하는 데 사용합니다.

### <a name="constraint-priorities"></a>제약 조건 우선 순위
이러한 모든 제약 조건에서 다음과 같이 생각했을 수 있습니다. "내 시스템에서 배치 제약 조건이 가장 중요하다고 생각합니다. 배치 제약 조건이 위반되지 않는 다는 것을 보장한다면 심지어 선호도 및 용량과 같은 다른 제약 조건을 위반하겠습니다."

가능한 것으로 판명됩니다! 제약 조건은 몇 가지 다른 수준의 적용으로 구성될 수 있지만 “hard”(0), “soft”(1), “optimization”(2) 및 “off”(-1)가 됩니다. 기본적으로 정의한 대부분의 제약 조건은 hard입니다. 예를 들어, 대부분의 사람들은 일반적으로 용량을 완화할 것으로 생각하지 않고 거의 모든 제약 조건은 hard 또는 soft입니다.

다양한 제약 조건 우선 순위로 인해 일부 제약 조건 위반 경고가 다른 경고보다 더 자주 표시됩니다. 일시적으로 완화(위반)하려는 특정 제약 조건이 있습니다. 이러한 수준이 실제로 지정한 제약 조건이 위반된다는 의미는 아니며 단순히 우선적으로 적용되는 순서가 있음을 나타냅니다. 그러면 제약 조건을 모두 충족할 수 없는 경우 Cluster Resource Manager가 적절한 균형을 유지할 수 있습니다.

고급 시나리오에서는 제약 조건 우선 순위를 변경할 수 있습니다. 예를 들어, 노드 용량 문제를 해결하기 위해 선호도를 위반하려고 합니다. 이를 위해 선호도 제약 조건의 우선순위를 "soft"(1)로 설정하고 용량 제약 조건을 "hard"(0)로 설정해 둘 수 있습니다.

다른 제약 조건에 대한 기본 우선 순위 값은 다음 구성에 지정되어 있습니다.

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>장애 도메인 및 업그레이드 도메인 제약 조건
Cluster Resource Manager는 Resource Manager의 엔진 내에서 제약 조건으로 장애 및 업그레이드 도메인 간에 분산되는 서비스를 유지하고자 원하는 것을 모델링합니다. 사용하는 방법에 대한 자세한 내용은 [클러스터 구성](service-fabric-cluster-resource-manager-cluster-description.md) 문서를 확인하세요.

장애 도메인 및 업그레이드 도메인이 잘못되는 것을 방지하도록 엄격히 관리해야 하는 경우가 있었습니다. 짧게나마 완전히 무시해야 하는 경우도 있었습니다. 일반적으로 제약 조건 우선 순위 인프라의 유연성은 훌륭하게 작동하지만 자주 필요하지는 않습니다. 모든 항목은 대부분 해당 기본 우선 순위에 위치합니다. 업그레이드 도메인은 soft 제약 조건을 남겨 둡니다. Cluster Resource Manager는 업그레이드, 오류 또는 제약 조건 위반을 처리하기 위해 업그레이드 도메인에 두 개의 복제본을 압축할 수 있습니다. 이 문제는 일반적으로 시스템이 올바른 배치를 방지하는 데에 여러 번 실패하거나 이탈하는 경우에 발생합니다. 환경이 오류 및 업그레이드 제약 조건을 올바르게 구성하면 업그레이드 중이라도 구성된 항목이 그대로 유지됩니다. Cluster Resource Manager가 제약 조건을 감시하여 위반이 감지되는 경우 즉시 보고하게 됩니다.

## <a name="the-preferred-location-constraint"></a>기본 설정 위치 제약 조건
PreferredLocation 제약 조건은 약간 다르기 때문에 "Optimization"으로 설정된 유일한 제약 조건입니다. 업그레이드 전에 찾은 위치로 서비스를 다시 배치하기 위해 업그레이드가 진행 중인 동안 이 제약 조건을 사용합니다. 실제로 작동하지 않는 이유가 많이 있지만 최적화가 가장 큰 원인입니다.

## <a name="upgrades"></a>업그레이드
응용 프로그램 및 클러스터를 업그레이드하는 동안 Cluster Resource Manager가 유용합니다. 이 때 다음과 같은 두 개의 작업이 있습니다.

* 규칙 및 클러스터의 성능이 손상되지 않는지 확인하는 작업
* 원활한 업그레이드를 돕는 작업

### <a name="keep-enforcing-the-rules"></a>규칙 적용 유지
알고 있어야 하는 중요한 점은 배치 제약 조건과 같은 사항의 엄격한 제약 조건이 업그레이드하는 동안 계속 적용된다는 것입니다. 배치 제약 조건을 통해 업그레이드 중에도 허용된 작업을 실행할 수 있습니다. 환경이 매우 제한된 경우 업그레이드에 시간이 오래 걸릴 수 있습니다. 업데이트하기 위해 서비스나 서비스가 위치한 노드를 중지해야 하는 경우 서비스를 이동시킬 위치에 대한 옵션이 적기 때문입니다.

### <a name="smart-replacements"></a>스마트 대체
업그레이드가 시작되면 Resource Manager는 클러스터 현재 정렬의 스냅숏을 생성합니다. 각 업그레이드 도메인이 완료되면 원래 정렬에 항목을 반환하려고 시도합니다. 이러한 방식으로 업그레이드하는 동안 최대 두 번의 전환이 있습니다(영향을 받는 노드 이동 및 다시 돌려놓기). 클러스터를 업그레이드하기 이전의 상태로 돌려놓으면 업그레이드도 클러스터의 레이아웃에 영향을 주지 않게 됩니다. 업그레이드하기 전에 클러스터가 잘 정렬된 경우 업그레이드 이후에도 흐트러지지 않고 잘 정렬됩니다.

### <a name="reduced-churn"></a>이탈 감소
업그레이드하는 동안 발생하는 다른 작업은 클러스터 리소스 관리자가 업그레이드된 엔터티에 대한 부하 분산을 해제하는 것입니다. 즉, 두 개의 응용 프로그램 인스턴스를 가지고 그 중 하나에서 업그레이드하면 해당 응용 프로그램 인스턴스에 대한 부하 분산이 일시 중지되지만 다른 응용 프로그램 인스턴스에는 영향을 주지 않습니다. 부하 분산은 업그레이드를 위해 서비스를 비운 노드로 이동하는 것과 같이 업그레이드 자체에 대한 불필요한 반응을 방지합니다. 문제가 발생한 업그레이드가 클러스터 업그레이드인 경우 전체 클러스터는 업그레이드하는 동안 부하가 분산되지 않습니다. 제약 조건은 규칙이 계속 활성화되고 균형 조정만 사용하지 않도록 합니다.

### <a name="relaxed-rules"></a>관대한 규칙
일반적으로 클러스터가 제한되거나 가득 찬 경우에도 완료하도록 업그레이드하려고 합니다. 업그레이드하는 동안 클러스터의 용량을 관리하는 것이 평소보다 훨씬 더 중요합니다. 클러스터를 통해 업그레이드를 롤업하는 시점에서 일반적으로 용량의 5~20%가 줄어들기 때문입니다. 해당 작업은 일반적으로 이동해야 합니다. 이 경우에 [버퍼링된 용량](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity)이라는 개념을 실제로 고려해야 합니다. 버퍼링된 용량이 정상적인 작업(일부 오버헤드 발생) 중에 적용되는 반면 Cluster Resource Manager는 업그레이드 중에 전체 용량(버퍼 사용)을 가득 채우게 됩니다.

## <a name="next-steps"></a>다음 단계
* 처음부터 시작 및 [서비스 패브릭 클러스터 Resource Manager 소개](service-fabric-cluster-resource-manager-introduction.md)



<!--HONumber=Jan17_HO1-->


