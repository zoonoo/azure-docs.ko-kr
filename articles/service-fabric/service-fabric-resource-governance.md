---
title: "컨테이너 및 서비스에 대한 Azure Service Fabric 리소스 관리 | Microsoft Docs"
description: "Azure Service Fabric을 사용하면 컨테이너 내부 또는 외부에서 실행 중인 서비스에 대해 리소스 제한을 지정할 수 있습니다."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/27/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e490dfba28b8a270bf0e6022333f148c965bc6dc
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="resource-governance"></a>리소스 관리 

동일한 노드 또는 클러스터에서 여러 서비스를 실행하는 경우 한 서비스가 다른 서비스보다 많은 리소스를 사용할 수 있습니다. 이 문제를 방해가 되는 이웃 문제라고 합니다. Service Fabric을 사용하면 개발자가 서비스당 예약 및 제한을 지정하여 리소스를 보장하고 리소스 사용량을 제한할 수도 있습니다. 

## <a name="resource-governance-metrics"></a>리소스 관리 메트릭 

리소스 관리는 Service Fabric에서 [서비스 패키지](service-fabric-application-model.md)별로 지원됩니다. 서비스 패키지에 할당된 리소스를 코드 패키지 간에 다시 나눌 수 있습니다. 지정된 리소스 제한은 리소스 예약을 의미하기도 합니다. Service Fabric은 두 개의 기본 제공 [메트릭](service-fabric-cluster-resource-manager-metrics.md)을 사용하여 서비스 패키지당 CPU 및 메모리 지정을 지원합니다.

* CPU(메트릭 이름 `ServiceFabric:/_CpuCores`): 코어는 호스트 컴퓨터에서 사용할 수 있는 논리 코어이며 모든 노드의 모든 코어에 동일한 가중치가 지정됩니다.
* 메모리(메트릭 이름 `ServiceFabric:/_MemoryInMB`): 메모리는 메가바이트 단위로 표현되며, 컴퓨터에서 사용할 수 있는 실제 메모리에 매핑됩니다.

소프트 예약 보증만 제공됩니다. 런타임은 사용 가능한 리소스를 초과할 경우 새 서비스 패키지 열기를 거부합니다. 그러나 다른 실행 파일이나 컨테이너가 노드에 배치되면 원래 예약 보증을 위반할 수 있습니다.

이러한 두 메트릭을 위해 [클러스터 리소스 관리자](service-fabric-cluster-resource-manager-cluster-description.md)는 총 클러스터 용량, 클러스터의 각 노드 부하, 클러스터에 남은 리소스를 추적합니다. 두 메트릭은 다른 사용자 또는 사용자 지정 메트릭과 동일하며, 기존의 모든 기능을 메트릭과 함께 사용할 수 있습니다.
* 두 메트릭에 따라 클러스터의 [균형을 조정](service-fabric-cluster-resource-manager-balancing.md)할 수 있습니다(기본 동작).
* 두 메트릭에 따라 클러스터를 [조각 모음](service-fabric-cluster-resource-manager-defragmentation-metrics.md)할 수 있습니다.
* [클러스터를 설명](service-fabric-cluster-resource-manager-cluster-description.md)할 때 두 메트릭에 대해 버퍼링된 용량을 설정할 수 있습니다.

두 메트릭에 대한 [동적 부하 보고](service-fabric-cluster-resource-manager-metrics.md)는 지원되지 않으며 해당 메트릭에 대한 부하는 생성 시 정의됩니다.

## <a name="cluster-set-up-for-enabling-resource-governance"></a>리소스 관리를 사용하기 위한 클러스터 설정

클러스터의 각 노드 형식에서 수동으로 용량을 다음과 같이 정의해야 합니다.

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="ServiceFabric:/_CpuCores" Value="4"/>
        <Capacity Name="ServiceFabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```
 
리소스 관리는 사용자 서비스에서만 허용되고 시스템 서비스에서는 허용되지 않습니다. 용량을 지정할 때 일부 코어와 메모리는 시스템 서비스에 할당되지 않은 상태로 두어야 합니다. 최적 성능을 얻으려면 클러스터 매니페스트에서 다음 설정도 켜야 합니다. 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specifying-resource-governance"></a>리소스 관리 지정 

리소스 관리 제한은 다음 예제와 같이 응용 프로그램 매니페스트(ServiceManifestImport 섹션)에서 지정됩니다.

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
  <Parameters>
  </Parameters>
  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric will sum the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
이 예제에서 서비스 패키지 ServicePackageA는 이 패키지가 배치된 노드의 코어 하나를 가져옵니다. 이 서비스 패키지에는 코드 패키지 2개(CodeA1 및 CodeA2)가 포함되어 있으며, 둘 다 `CpuShares` 매개 변수를 지정합니다. 비율이 CpuShares 512:256이면 코어가 코드 패키지 2개에 나뉩니다. 따라서 이 예제에서 CodeA1은 코어의 2/3를 받고, CodeA2는 코어의 1/3을 받습니다(동일한 비율의 소프트 보장 예약). 코드 패키지에 대한 CpuShares를 지정하지 않은 경우 Service Fabric은 코어를 균일하게 나눕니다.

메모리 제한은 절대값이므로 두 코드 패키지가 모두 1024MB 메모리로 제한됩니다(동일한 값의 소프트 보장 예약). 코드 패키지(컨테이너 또는 프로세스)는 이 제한보다 많은 메모리를 할당할 수 없으며, 할당하려고 하면 메모리 부족 예외가 발생합니다. 리소스 제한 적용이 작동하려면 서비스 패키지 내의 모든 코드 패키지에 메모리 제한이 지정되어 있어야 합니다.


## <a name="next-steps"></a>다음 단계
* 클러스터 리소스 관리자에 대한 자세한 내용은 이 [문서](service-fabric-cluster-resource-manager-introduction.md)를 참조하세요.
* 응용 프로그램 모델, 서비스 패키지, 코드 패키지 및 복제본이 매핑되는 방식에 대한 자세한 내용은 이 [문서](service-fabric-application-model.md)를 참조하세요.

