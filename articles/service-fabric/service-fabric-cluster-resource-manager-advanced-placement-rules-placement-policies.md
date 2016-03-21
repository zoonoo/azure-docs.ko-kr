<properties
   pageTitle="Service Fabric Cluster Resource Manager - 배치 정책"
   description="서비스 패브릭 서비스에 대한 추가 배치 정책 및 규칙 개요"
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

# 배치 정책
Service Fabric Cluster가 지리적으로 널리 분포되어 있는 경우, 즉 데이터 센터나 Azure 지역이 여러 곳에 배치된 경우 또는 환경이 지정학적으로 널리 분산된 경우(또는 법적 경계나 정책 경계를 고려해야 하는 경우) 여러 가지 규칙을 추가로 고려해야 합니다. 이러한 사항은 노드 속성 및 배치 제약 조건(앞 부분에서 설명)을 통해 구성할 수 있지만 일부는 더 복잡합니다. 어떤 경우이든 간단한 방법을 사용할 수 있습니다. 배치 정책은 배치 제약 조건처럼 서비스 단위로 구성할 수 있습니다.

-	InvalidDomain - 특정 장애 도메인이 이 워크로드에 유효하지 않다는 것을 지정할 수 있습니다. 이 기능은 예를 들어 지정학적 또는 회사 정책상의 이유로 특정 영역에서 특정 서비스가 실행되지 않아야 하는 경우 유용합니다.

![잘못된 도메인 예제][Image1]

코드:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```

-	RequiredDomain – 상당히 간단합니다. 모든 복제본이 지정된 도메인에 있어야 합니다.

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

-	PreferPrimaryDomain – 가능한 경우 주 도메인이 있어야 하는 장애 도메인을 선택할 수 있으므로 기본 주 도메인은 유용한 컨트롤입니다. 정상 상태인 경우 이 도메인이 주 도메인이 됩니다. 어떤 이유로 인해 도메인이나 주 복제본에 장애가 발생하거나 종료된 경우 주 도메인이 다른 위치로 마이그레이션되며 사용 가능해지면 기본 도메인으로 다시 이동합니다. 기본적으로 이 설정은 상태 저장 서비스에만 적합합니다.

![기본 주 도메인 및 장애 조치(Failover)][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

-	RequireDomainDistribution – 도메인 분산 필요는 몇 가지 상황을 방지하는 데 사용될 수 있는 또 다른 옵션입니다. 위의 예제에서 일시적으로 여러 복제본(쿼럼)이 동일한 데이터 센터에 압축될 수 있습니다. 이 경우 주 도메인이 대부분의 복제본과 다른 DC에 있으므로 안전할 수 있지만 피해야 하는 구성이 일시적으로 적용될 수 있습니다. 당시 클러스터의 다른 조건에 따라 달라질 수 있지만 복제본이 항상 별도 도메인에 있도록 설정하여 이러한 경우를 방지할 수 있습니다. RequireDomainDistribution 플래그는 이 기능을 제공하지만 압축될 경우 복제본을 배치하지 않는다는 의미가 될 수 있습니다. 즉, 실행 가능한 도메인의 노드가 나타날 때까지 일시적으로 목표보다 적은 수의 복제본이 실행됩니다. 항상 목표 개수의 복제본을 유지하려는 고객이 있는 반면, 가용성을 유지하려는 고객도 있습니다. 복제본을 장애 도메인에 압축하는 경우 도메인에 오류가 발생하면 모든 복제본이 동시에 손실됩니다. 복제본 수가 적은 경우 쿼럼이 손실될 수 있습니다. 대부분의 경우 복제본이 4개 이상 실행되므로 기본값은 일시적으로 도메인에 여러 복제본이 압축되더라도 도메인 분산을 요구하지 않고 일반적으로 분산 및 장애 조치(failover)를 통해 상황을 처리하는 것입니다.

코드:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

단일 서비스에 잘못된 도메인 또는 필수 도메인을 여러 개 구성할 수 있지만 다른 두 도메인(기본 도메인 및 도메인 분산)은 서비스당 한 번만 구성할 수 있습니다.

현재 지리적으로 분산되지 않은 클러스터의 서비스에 대해 이러한 구성을 사용할 수 있나요? 물론 가능합니다. 하지만 그럴 필요가 없습니다. 특히, 실제로 지리적으로 분산된 클러스터를 실행하지 않는 경우 필수 도메인, 잘못된 도메인, 기본 도메인 구성은 피해야 합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
- [서비스 구성에 대해 알아보기](service-fabric-cluster-resource-manager-configure-services.md)



[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0309_2016-->