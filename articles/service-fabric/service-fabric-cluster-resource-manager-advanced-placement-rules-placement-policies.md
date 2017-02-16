---
title: "Service Fabric 클러스터 리소스 관리자 - 배치 정책 | Microsoft Docs"
description: "서비스 패브릭 서비스에 대한 추가 배치 정책 및 규칙 개요"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 7fa35de8b99132ad1c10229a9bb2231f11fdbaa0


---
# <a name="placement-policies-for-service-fabric-services"></a>서비스 패브릭 서비스에 대한 배치 정책
일부 드문 시나리오에는 구성해야 하는 여러 가지 추가 규칙이 있습니다. 이러한 시나리오의 몇 가지 예는 다음과 같습니다.
* 온-프레미스 데이터 센터 또는 Azure 지역 등에 걸쳐 Service Fabric 클러스터의 지리적 거리가 확대되는 경우
* 사용자 환경이 지정학적 컨트롤 또는 관심을 갖는 법적, 정책적 경계의 여러 영역에 확대되는 경우
* 먼 거리를 이동하거나 느리고 신뢰할 수 없는 네트워크를 전송하는 클러스터에서 이뤄지는 통신으로 인해 실제 성능/대기 시간을 고려해야 합니다.

이러한 유형의 상황에서 지정된 서비스가 특정 지역에서 항상 실행되거나 실행되지 않는 것이 중요할 수 있습니다. 마찬가지로 최종 사용자의 대기 시간을 최소화하기 위해 특정 지역에 주 복제본을 배치하는 것이 중요할 수 있습니다.

고급 배치 정책은 다음과 같습니다.

1. 잘못된 도메인
2. 필수 도메인
3. 기본 설정 도메인
4. 복제본 압축 불가

다음 컨트롤은 대부분 노드 속성 및 배치 제약 조건을 통해 구성할 수 있지만 일부는 더 복잡합니다. Service Fabric Cluster Resource Manager는 좀 더 간단하게 이러한 추가 배치 정책을 제공합니다. 배치 정책은 배치 제약 조건처럼 이름이 지정된 서비스 인스턴스 단위로 구성하고 동적으로 업데이트할 수 있습니다.

## <a name="specifying-invalid-domains"></a>잘못된 도메인 지정
InvalidDomain 배치 정책을 사용하여 특정 장애 도메인이 이 워크로드에 유효하지 않다고 지정할 수 있습니다. 이 정책을 사용하면 예를 들어 지정학적 또는 회사 정책상의 이유로 특정 영역에서 특정 서비스가 실행되지 않도록 할 수 있습니다. 별도 정책을 통해 잘못된 여러 도메인을 지정할 수 있습니다.

<center>
![잘못된 도메인 예제][Image1]
</center>

코드:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>필수 도메인 지정
필수 도메인 배치 정책에서는 서비스에 대한 모든 상태 저장 복제본 또는 상태 비저장 서비스 인스턴스가 지정된 도메인에 있어야 합니다. 별도 정책을 통해 여러 필수 도메인을 지정할 수 있습니다.

<center>
![필수 도메인 예제][Image2]
</center>

코드:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>주 복제본에 대한 기본 설정 도메인 지정
기본 설정 주 도메인은 가능한 경우 주 복제본이 있어야 하는 장애 도메인을 선택할 수 있으므로 유용한 컨트롤입니다. 정상 상태인 경우 주 복제본은 이 도메인에 위치하게 됩니다. 어떤 이유로 인해 도메인이나 주 복제본에 장애가 발생하거나 종료된 경우 주 복제본은 다른 위치로 마이그레이션됩니다. 새 위치가 기본 설정 도메인이 아닌 경우 Cluster Resource Manager는 새 위치를 가능한 한 빨리 기본 도메인으로 이동시킵니다. 이 정책은 Azure 지역 또는 여러 데이터 센터 간에 걸쳐 있는 클러스터에서 가장 유용합니다. 이 정책은 Azure 지역 또는 여러 데이터 센터에 걸쳐 확장되는 클러스터에서 가장 유용하지만 기본적으로 특정 위치에 주 복제본을 배치합니다. 주 복제본을 해당 사용자에게 가깝게 배치하면 읽기의 경우 특히 대기 시간을 줄일 수 있습니다.

<center>
![기본 설정 주 도메인 및 장애 조치(Failover)][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>모든 도메인에서 복제본을 분산하고 압축을 허용하지 않음
복제본은 _일반적으로_ 클러스터 상태가 정상인 도메인 간에 배포되지만 지정된 파티션의 복제본이 일시적으로 단일 도메인에 압축되는 경우가 있습니다. 예를 들어, 클러스터에&3;개의 장애 도메인에 있는&9;개의 노드가 있고(fd:/0, fd:/1 및 fd:/2) 서비스에&3;개의 복제본이 있다고 가정하겠습니다. fd:/1 및 fd:/2의 해당 복제본에 사용되는 노드가 중단되었다고 가정하겠습니다. 이제 일반적으로 Cluster Resource Manager는 이러한 동일한 장애 도메인에서 다른 노드를 기본으로 사용합니다. 이 경우에 용량 문제로 인해 해당 도메인의 다른 노드가 유효하지 않다고 가정하겠습니다. Cluster Resource Manager가 해당 복제본에 대한 대체 항목을 빌드하는 경우 fd:/0에서 노드를 선택해야 합니다. 그러나 _해당 작업_을 수행하면 장애 도메인 제약을 위반하는 상황이 발생합니다. 또한 전체 복제본 세트가 중단되거나 손실될 가능성도 높아집니다(FD 0이 영구적으로 손실될 경우). 제약 조건 및 제약 조건 우선 순위에 대한 자세한 내용은 [이 항목](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)을 확인하세요.

`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`와 같은 상태 경고가 표시된 경우 이 조건 또는 비슷한 조건에 도달했습니다. 드문 경우지만 발생할 수 있습니다. 그리고 노드가 다시 돌아오기 때문에 일반적으로 이러한 상황은 일시적입니다. 노드가 중단되고 Cluster Resource Manager가 대체 노드를 빌드해야 하는 경우 일반적으로 이상적인 장애 도메인에서 사용할 수 있는 다른 노드가 있습니다.

일부 워크로드가 더 적은 도메인에 압축되는 경우 복제본의 대상 수가 있습니다. 이러한 워크로드는 동시에 발생하는 영구적인 도메인 오류 전체에 대해 안정적이며 일반적으로 로컬 상태를 복구할 수 있습니다. 다른 워크로드는 위험 정확성 또는 데이터의 손실이 발생하기 전에 가동 중지 시간을 감수합니다. 대부분의 프로덕션 워크로드가 세 개 이상의 복제본, 세 개 이상의 장애 도메인 및 장애 도메인당 여러 개의 유효한 노드에서 실행되기 때문에 기본적으로 도메인 배포는 필요하지 않습니다. 그러면 도메인에 일시적으로 압축된 여러 개의 복제본이 있을 수 있다는 것을 의미하지만 보통 균형 조정 및 장애 조치로 이러한 경우를 처리할 수 있습니다.

지정된 워크로드에 대해 이러한 압축을 사용하지 않도록 설정하려면 서비스에서 "RequireDomainDistribution" 정책을 지정할 수 있습니다. 이 정책을 설정하면 Cluster Resource Manager를 통해 동일한 장애 도메인 또는 업그레이드 도메인에서 동일한 파티션의 복제본이 두 개가 되지 않도록 합니다.

코드:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

현재 지리적으로 분산되지 않은 클러스터의 서비스에 대해 이러한 구성을 사용할 수 있나요? 물론 가능합니다. 하지만 그럴 필요가 없습니다. 실제로 지리적 거리가 분산된 클러스터를 실행하지 않는 경우 필수 도메인, 잘못된 도메인, 기본 설정 도메인 구성은 피해야 합니다. 지정된 워크로드를 단일 랙에서 실행하거나 다른 로컬 클러스터의 일부 세그먼트를 기본으로 사용하려는 것은 바람직하지 않습니다. 여러 하드웨어 구성을 도메인 간에 분산하고 일반 배치 제약 조건 및 노드 속성을 통해 처리해야 합니다.

## <a name="next-steps"></a>다음 단계
* 서비스를 구성하는 데 사용할 수 있는 다른 옵션에 대한 자세한 내용은 [서비스 구성에 대해 알아보기](service-fabric-cluster-resource-manager-configure-services.md)로 이동하세요.

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Jan17_HO1-->


