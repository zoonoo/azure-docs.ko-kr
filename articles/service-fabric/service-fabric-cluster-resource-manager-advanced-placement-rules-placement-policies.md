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
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: cc136c567279ff9da655ed3536fae6d2ae7be3df


---
# <a name="placement-policies-for-service-fabric-services"></a>서비스 패브릭 서비스에 대한 배치 정책
서비스 패브릭 클러스터가 지리적으로 널리 분포된 경우, 즉 데이터 센터나 Azure 지역이 여러 곳에 배치된 경우 또는 환경이 지정학적으로 널리 분산된 경우(또는 법적 경계나 정책 경계를 고려해야 하는 경우 또는 관련된 거리에 실제 성능/지연 영향이 있는 경우) 여러 가지 규칙을 추가로 고려해야 합니다. 이러한 사항은 노드 속성 및 배치 제약 조건을 통해 구성할 수 있지만 일부는 더 복잡합니다. 상황을 좀 더 간단히 만들기 위해 이러한 추가 명령을 제공합니다. 배치 정책은 배치 제약 조건처럼 이름이 지정된 서비스 인스턴스 단위로 구성할 수 있습니다.

## <a name="specifying-invalid-domains"></a>잘못된 도메인 지정
InvalidDomain 배치 정책을 사용하여 특정 장애 도메인이 이 워크로드에 유효하지 않다고 지정할 수 있습니다. 이 정책을 사용하면 예를 들어 지정학적 또는 회사 정책상의 이유로 특정 영역에서 특정 서비스가 실행되지 않도록 할 수 있습니다. 별도 정책을 통해 잘못된 여러 도메인을 지정할 수 있습니다.

![잘못된 도메인 예제][Image1]

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

![필수 도메인 예제][Image2]

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
기본 설정 주 도메인은 가능한 경우 주 도메인이 있어야 하는 장애 도메인을 선택할 수 있으므로 유용한 컨트롤입니다. 정상 상태인 경우 이 도메인이 주 도메인이 됩니다. 어떤 이유로 인해 도메인이나 주 복제본에 장애가 발생하거나 종료된 경우 주 도메인이 다른 위치로 마이그레이션되며 이 위치가 기본 도메인에 없는 경우 클러스터 Resource Manager가 기본 도메인으로 다시 이동될 수 있습니다. 기본적으로 이 설정은 상태 저장 서비스에만 적합합니다. 이 정책은 Azure 지역 또는 여러 데이터 센터 간에 걸쳐 있는 클러스터에서 가장 유용합니다. 이러한 상황에서 중복을 위해 모든 위치를 사용하고 있지만 주 복제본으로 이동되는 작업의 대기 시간을 줄이기 위해 주 복제본을 특정 위치에 배치할 수 있습니다. 기본적으로 쓰기 및 모든 읽기가 주 복제본에서 처리됩니다.

![기본 주 도메인 및 장애 조치(Failover)][Image3]

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
지정할 수 있는 다른 정책은 항상 복제본을 사용 가능한 장애 도메인 간에 분산하도록 하는 것입니다. 클러스터가 정상 상태인 대부분의 경우에는 기본적으로 이러한 작업이 수행되지만, 지정된 파티션의 복제본이 단일 장애 또는 업그레이드 도메인에 일시적으로 압축될 수도 있는 부정적인 경우도 있습니다. 예를 들어 클러스터의 3개의 장애 도메인(0, 1 및 2)에 9개의 노드가 있고 서비스에 복제본이 3개 있더라도 장애 도메인 1과 2에서 해당 복제본에 사용되던 노드가 다운되었으며, 용량 문제 때문에 해당 도메인의 다른 어떤 노드도 유효하지 않은 경우를 가정할 수 있습니다. 서비스 패브릭이 해당 복제본의 대체 복제본을 빌드해야 할 경우 클러스터 Resource Manager는 장애 도메인 0에 해당 복제본을 추가해야 하지만, 장애 도메인 제약 조건이 위반되는 상황이 구현됩니다. 또한 전체 복제본 세트가 손실될 가능성도 높아집니다(FD 0이 영구적으로 손실될 경우). (제약 조건 및 제약 조건 우선 순위에 대한 자세한 내용은 [이 항목](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) 을 확인하세요.)

"부하 분산 장치에서 이 복제본에 대한 제약 조건 위반을 감지함:fabric:/<some service name> 보조 파티션 <some partition ID>이(가) 제약 조건을 위반함: FaultDomain" 등과 같은 상태 경고가 발생하면 이 조건을 위반했거나 유사한 작업을 수행한 것입니다. 일반적으로 이러한 경우는 일시적이지만(노드가 오랫동안 다운된 상태를 유지하지 않으며, 오래 다운되어 대체 복사본을 빌드해야 할 경우, 올바른 장애 도메인에 유효한 다른 노드가 있음) 가용성을 얻기 위해 모든 복제본을 유실할 가능성을 용인하는 워크로드도 있습니다. 동일한 파티션의 어떤 두 복제본도 동일한 장애 또는 업그레이드 도메인에서 허용되지 않도록 하는 "RequireDomainDistribution" 정책을 지정하여 이 작업을 수행할 수 있습니다.

일부 워크로드는 항상 목표 개수의 복제본(상태의 복사본)을 유지하지만(전체 도메인 오류가 발생하지 않을 것이며, 일반적으로 로컬 상태를 복구할 수 있다고 믿음), 정확성 및 데이터 손실 걱정 없이 더 일찍 가동 중지 시간을 겪게 되는 워크로드도 있습니다. 대부분의 프로덕션 워크로드는 4개 이상의 복제본을 사용해서 실행되므로 기본값은 일시적으로 도메인에 여러 복제본이 압축되더라도 도메인 분산을 요구하지 않고 일반적으로 분산 및 장애 조치(failover)를 통해 상황을 처리하는 것입니다.

코드:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

현재 지리적으로 분산되지 않은 클러스터의 서비스에 대해 이러한 구성을 사용할 수 있나요? 물론 가능합니다. 그렇지만 그렇게 해야 할 중대한 이유도 없습니다. 특히, 실제로 지리적으로 분산된 클러스터를 실행하고 있지 않다면 유효하지 않은 필수 기본 도메인 구성은 피해야 합니다. 다양한 형식의 하드웨어 또는 워크로드 구분이 진행되지 않을 경우 지정된 워크로드를 강제로 단일 랙에서 실행하거나 일부 로컬 클러스터 세그먼트를 다른 세그먼트보다 선호하는 것은 타당하지 않으며, 이러한 경우는 정상적인 배치 제약조건을 통해 처리할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 서비스 구성에 사용할 수 있는 기타 옵션에 대한 자세한 내용은 [서비스 구성에 대해 알아보기](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Nov16_HO3-->


