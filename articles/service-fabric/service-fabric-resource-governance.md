---
title: 컨테이너 및 서비스에 대한 리소스 관리
description: Azure Service Fabric를 사용 하 여 프로세스나 컨테이너로 실행 되는 서비스에 대 한 리소스 요청 및 제한을 지정할 수 있습니다.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 889fce77c1a3a743e9805ec482a9c87b9bf8da65
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172869"
---
# <a name="resource-governance"></a>리소스 거버넌스

동일한 노드 또는 클러스터에서 여러 서비스를 실행 하는 경우 한 서비스에서 더 많은 리소스를 소비 하 고 프로세스의 다른 서비스를 함으로써 수 있습니다. 이 문제를 방해가 되는 이웃 문제라고 합니다. 개발자는 Azure Service Fabric를 사용 하 여 리소스 사용량을 제한 하도록 서비스 당 요청 및 제한을 지정 하 여이 동작을 제어할 수 있습니다.

> 이 문서를 진행하기 전에 [Service Fabric 애플리케이션 모델][application-model-link] 및 [Service Fabric 호스팅 모델][hosting-model-link]에 대해 익숙해져야 합니다.
>

## <a name="resource-governance-metrics"></a>리소스 관리 메트릭

리소스 관리는 Service Fabric에서 [서비스 패키지][application-model-link]에 따라 지원됩니다. 서비스 패키지에 할당된 리소스를 코드 패키지 간에 다시 나눌 수 있습니다. Service Fabric는 다음과 같은 두 가지 기본 제공 [메트릭을](service-fabric-cluster-resource-manager-metrics.md)사용 하 여 서비스 패키지 별로 CPU 및 메모리 거 버 넌 스를 지원 합니다.

* *CPU* (메트릭 이름 `servicefabric:/_CpuCores` ): 호스트 컴퓨터에서 사용할 수 있는 논리 코어입니다. 모든 노드에서 모든 코어에 동일하게 가중치를 적용합니다.

* *메모리* (메트릭 이름 `servicefabric:/_MemoryInMB` ): 메모리는 메가바이트 단위로 표현 되며, 컴퓨터에서 사용할 수 있는 실제 메모리에 매핑됩니다.

이러한 두 메트릭에 대해 [클러스터 리소스 관리자 (CRM)][cluster-resource-manager-description-link] 는 총 클러스터 용량, 클러스터의 각 노드에 대 한 로드 및 클러스터의 나머지 리소스를 추적 합니다. 이 두 메트릭은 모든 다른 사용자 또는 사용자 지정 메트릭에 동일합니다. 기존의 모든 기능을 함께 사용할 수 있습니다.

* 두 메트릭에 따라 클러스터의 [균형을 조정](service-fabric-cluster-resource-manager-balancing.md)할 수 있습니다(기본 동작).
* 두 메트릭에 따라 클러스터를 [조각 모음](service-fabric-cluster-resource-manager-defragmentation-metrics.md)할 수 있습니다.
* [클러스터를 설명][cluster-resource-manager-description-link]할 때 두 메트릭에 대해 버퍼링된 용량을 설정할 수 있습니다.

> [!NOTE]
> 이러한 메트릭에 대해서는 [동적 부하 보고가](service-fabric-cluster-resource-manager-metrics.md) 지원 되지 않습니다. 이러한 메트릭에 대 한 로드는 생성 시 정의 됩니다.

## <a name="resource-governance-mechanism"></a>리소스 거버넌스 메커니즘

버전 7.2부터 Service Fabric 런타임은 CPU 및 메모리 리소스에 대 한 요청 및 제한 사양을 지원 합니다.

> [!NOTE]
> 7.2 보다 오래 된 Service Fabric 런타임 버전은 단일 값이 **요청** 및 특정 리소스 (CPU 또는 메모리)에 대 한 **제한** 으로 모두 사용 되는 모델만 지원 합니다. 이 내용은이 문서의 **Requestsonly** 사양으로 설명 되어 있습니다.

* *요청:* CPU 및 메모리 요청 값은 및 메트릭에 대 한 [CRM (클러스터 리소스 관리자][cluster-resource-manager-description-link] )에서 사용 하는 로드를 나타냅니다 `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` . 즉, CRM은 서비스의 리소스 사용을 요청 값과 동일 하 게 고려 하 고 배치 결정을 내릴 때 이러한 값을 사용 합니다.

* *제한:* CPU 및 메모리 제한 값은 노드에서 프로세스나 컨테이너가 활성화 될 때 적용 되는 실제 리소스 제한을 나타냅니다.

Service Fabric는 CPU 및 메모리에 대 한 **RequestsAndLimits** 사양과 함께 **requestsonly를** 허용 합니다.
* RequestsOnly 지정을 사용 하는 경우 service fabric은 요청 값을 제한으로 사용 하기도 합니다.
* S 1만 지정을 사용 하는 경우 service fabric은 요청 값을 0으로 간주 합니다.
* RequestsAndLimits specification을 사용 하는 경우 제한 값은 요청 값 보다 크거나 같아야 합니다.

리소스 관리 메커니즘을 보다 잘 이해 하기 위해 CPU 리소스에 대 한 **Requestsonly** 사양을 사용 하는 예제 배치 시나리오 (메모리 거 버 넌 스의 메커니즘)를 살펴보겠습니다. 두 CPU 코어와 두 개의 서비스 패키지가 배치 될 노드를 고려 합니다. 배치 될 첫 번째 서비스 패키지는 하나의 컨테이너 코드 패키지로만 구성 되며 하나의 CPU 코어 요청만 지정 합니다. 배치 될 두 번째 서비스 패키지는 하나의 프로세스 기반 코드 패키지로 구성 되며 하나의 CPU 코어 요청만 지정 합니다. 두 서비스 패키지에 모두 RequestsOnly 사양이 있으므로 해당 제한 값은 해당 요청 값으로 설정 됩니다.

1. 먼저 하나의 CPU 코어를 요청 하는 컨테이너 기반 서비스 패키지가 노드에 배치 됩니다. 런타임은 컨테이너를 활성화 하 고 CPU 제한을 1 개 코어로 설정 합니다. 이 컨테이너는 한 번에 둘 이상의 코어를 사용할 수 없습니다.

2. 그런 다음 CPU 코어 하나를 요청 하는 프로세스 기반 서비스 패키지가 노드에 배치 됩니다. 런타임은 서비스 프로세스를 활성화 하 고 CPU 제한을 1 개 코어로 설정 합니다.

이 시점에서 요청의 합계는 노드의 용량과 같습니다. CRM은이 노드에 CPU 요청을 포함 하는 컨테이너 또는 서비스 프로세스를 더 이상 두지 않습니다. 노드에서 프로세스와 컨테이너는 각각 하나의 코어로 실행 되며 CPU를 위해 서로 경합 하지 않습니다.

이제 **RequestsAndLimits** 사양을 사용 하 여 예제를 다시 방문 하겠습니다. 이번에는 컨테이너 기반 서비스 패키지가 하나의 CPU 코어 요청 및 두 개의 CPU 코어 제한을 지정 합니다. 프로세스 기반 서비스 패키지는 하나의 요청 및 하나의 CPU 코어의 제한을 지정 합니다.
  1. 먼저 컨테이너 기반 서비스 패키지가 노드에 배치 됩니다. 런타임은 컨테이너를 활성화 하 고 CPU 제한을 두 개의 코어로 설정 합니다. 컨테이너는 세 개 이상의 코어를 사용할 수 없습니다.
  2. 그런 다음 프로세스 기반 서비스 패키지가 노드에 배치 됩니다. 런타임은 서비스 프로세스를 활성화 하 고 CPU 제한을 1 개 코어로 설정 합니다.

  이 시점에서 노드에 배치 된 서비스 패키지의 CPU 요청 합계는 노드의 CPU 용량과 같습니다. CRM은이 노드에 CPU 요청을 포함 하는 컨테이너 또는 서비스 프로세스를 더 이상 두지 않습니다. 그러나 노드에서 제한의 합계 (컨테이너의 경우 두 코어 + 프로세스의 코어 1 개)는 두 코어의 용량을 초과 합니다. 컨테이너와 프로세스가 동시에 버스트 되 면 CPU 리소스에 대 한 경합이 발생할 수 있습니다. 이러한 경합은 플랫폼의 기본 OS에 의해 관리 되 됩니다. 이 예에서 컨테이너는 CPU 코어를 2 개까지 버스트 했을 수 있으며,이로 인해 하나의 CPU 코어에 대 한 프로세스 요청이 보장 되지 않습니다.

> [!NOTE]
> 이전 예제에서 설명한 것 처럼 CPU 및 메모리에 대 한 요청 값은 **노드에 리소스를 예약 하지**않습니다. 이러한 값은 클러스터 리소스 관리자 배치 결정을 내릴 때 고려 하는 리소스 소비량을 나타냅니다. 제한 값은 노드에서 프로세스나 컨테이너가 활성화 될 때 적용 되는 실제 리소스 제한을 나타냅니다.


CPU에 대 한 경합이 있을 수 있는 몇 가지 경우가 있습니다. 이러한 상황에서 예제의 프로세스와 컨테이너는 잡음이 있는 환경 문제를 겪을 수 있습니다.

* *관리 및 비관리 서비스와 컨테이너 혼합*: 사용자가 리소스 거버넌스를 지정하지 않고 서비스를 만들면 런타임이 해당 서비스는 리소스를 소비하지 않는다고 간주하며 이 예제의 노드에 배치할 수 있습니다. 이 경우 이 새 프로세스가 이미 노드에서 실행 중인 서비스를 희생하여 일부 CPU를 효과적으로 소비하게 됩니다. 이 문제에 대 한 두 가지 해결 방법이 있습니다. 관리 및 비관리 서비스를 같은 클러스터에서 혼합하지 않거나, [배치 제약 조건](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)을 사용하여 두 가지 종류의 서비스가 동일한 노드 집합에서 종료되지 않게 하는 것입니다.

* *Service Fabric 외부에 있는 다른 프로세스가 노드에서 시작(예: 일부 OS 서비스)*: Service Fabric 외부의 프로세스도 기존 서비스와 CPU를 경합합니다. 이 문제의 해결 방법은 다음 섹션에서처럼 OS 오버헤드에 부합하는 노드 용량을 올바르게 설정하는 것입니다.

* *요청이 제한과 같지 않은 경우*(이전 RequestsAndLimits 예제에 설명 된 대로 요청은 노드의 리소스 예약을 수행 하지 않습니다. 요청 보다 큰 제한이 있는 서비스는 노드에 배치 될 경우 리소스 (사용 가능한 경우)를 해당 제한까지 소비할 수 있습니다. 이러한 경우 노드의 다른 서비스는 해당 요청 값에 대 한 리소스를 사용 하지 못할 수 있습니다.

## <a name="cluster-setup-for-enabling-resource-governance"></a>리소스 거버넌스를 사용하기 위한 클러스터 설정

노드가 시작되고 클러스터에 연결되면 Service Fabric은 사용 가능한 메모리 용량과 코어 수를 확인하고 해당 두 리소스에 대한 노드 용량을 설정합니다.

운영 체제에 대 한 버퍼 공간을 유지 하 고, 노드에서 실행 될 수 있는 다른 프로세스의 경우 Service Fabric는 노드에서 사용 가능한 리소스의 80%만 사용 합니다. 이 백분율은 구성할 수 있고 클러스터 매니페스트에서 변경할 수 있습니다.

Service Fabric이 사용 가능한 CPU의 50%와 사용 가능한 메모리의 70%를 사용하도록 지시하는 방법의 예제는 다음과 같습니다.

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

대부분의 고객 및 시나리오에서 CPU 및 메모리에 대 한 노드 용량의 자동 검색은 권장 되는 구성입니다 (자동 검색은 기본적으로 설정 됨). 그러나 노드 용량을 완전히 수동으로 설정 해야 하는 경우 클러스터의 노드를 설명 하는 메커니즘을 사용 하 여 노드 유형별으로 구성할 수 있습니다. 4 개 코어와 2gb의 메모리를 사용 하 여 노드 형식을 설정 하는 방법의 예는 다음과 같습니다.

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

사용 가능한 리소스의 자동 확인을 사용하고 노드 용량이 클러스터 매니페스트에서 수동으로 정의된 경우 Service Fabric은 노드가 사용자가 정의한 용량을 지원하기에 충분한 리소스를 갖는지 확인합니다.

* 매니페스트에서 정의된 노드 용량이 노드에서 사용 가능한 리소스 이하인 경우 Service Fabric은 매니페스트에서 지정된 용량을 사용합니다.

* 매니페스트에서 정의된 노드 용량이 사용 가능한 리소스보다 크면 Service Fabric은 사용 가능한 리소스를 노드 용량으로 사용합니다.

필요하지 않은 경우 사용 가능한 리소스의 자동 검색을 해제할 수 있습니다. 이 기능을 해제하려면 다음 설정을 변경합니다.

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

최적 성능을 얻으려면 클러스터 매니페스트에서 다음 설정도 켜야 합니다.

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Service Fabric 버전 7.0부터 사용자가 노드 리소스 용량 값을 수동으로 제공 하는 경우 노드 리소스 용량이 계산 되는 방식에 대 한 규칙이 업데이트 되었습니다. 다음 시나리오를 살펴보겠습니다.
>
> * 노드에는 총 10 개의 CPU 코어가 있습니다.
> * SF는 사용자 서비스 (기본 설정)에 대 한 총 리소스의 80%를 사용 하도록 구성 되어 있으며,이는 노드에서 실행 중인 다른 서비스 (포함 시스템 서비스)에 대해 20%의 버퍼를 유지 합니다 Service Fabric.
> * 사용자가 CPU 코어 메트릭에 대 한 노드 리소스 용량을 수동으로 재정의 하 고 5 개 코어로 설정 하기로 결정 했습니다.
>
> Service Fabric 사용자 서비스의 사용 가능한 용량이 다음과 같은 방식으로 계산 되는 방식에 대 한 규칙이 변경 되었습니다.
>
> * 7.0 Service Fabric 하기 전에는 사용자 서비스의 사용 가능한 용량이 **5 개 코어** (20%의 용량 버퍼는 무시 됨)로 계산 됩니다.
> * Service Fabric 7.0부터 사용자 서비스에 사용할 수 있는 용량은 **4 개 코어** (20%의 용량 버퍼는 무시 되지 않음)로 계산 됩니다.

## <a name="specify-resource-governance"></a>리소스 거버넌스 지정

리소스 관리 요청 및 제한은 응용 프로그램 매니페스트 (ServiceManifestImport 섹션)에 지정 되어 있습니다. 몇 가지 예를 살펴보겠습니다.

**예제 1: RequestsOnly 사양**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

이 예에서는 특성을 `CpuCores` 사용 하 여 **ServicePackageA**에 대 한 CPU 코어 1 개 요청을 지정 합니다. CPU 제한 ( `CpuCoresLimit` 특성)을 지정 하지 않았기 때문에 Service Fabric에서는 서비스 패키지의 cpu 제한으로 1 코어의 지정 된 요청 값도 사용 합니다.

**ServicePackageA** 는 **해당 노드에 배치 된 모든 서비스 패키지의 cpu 요청 합계** 를 뺀 후 남은 cpu 용량이 1 코어 보다 크거나 같은 노드에만 배치 됩니다. 노드에서 서비스 패키지는 코어 하나로 제한 됩니다. 서비스 패키지에는 두 개의 코드 패키지 (**CodeA1** 및 **CodeA2**)가 포함 되어 있으며 둘 다 특성을 지정 합니다 `CpuShares` . CpuShares 512:256의 비율을 사용 하 여 개별 코드 패키지의 CPU 제한을 계산 합니다. 따라서 CodeA1는 코어의 2/3로 제한 되며 CodeA2는 코어의 1/3로 제한 됩니다. CpuShares가 모든 코드 패키지에 대해 지정 되지 않은 경우 Service Fabric는 CPU 제한을 동일 하 게 나눕니다.

코드 패키지에 대해 지정 된 CpuShares는 서비스 패키지의 전체 CPU 한도의 상대적 비율을 나타내며, 코드 패키지에 대 한 메모리 값은 절대 용어로 지정 됩니다. 이 예제에서 특성은 `MemoryInMB` CodeA1 및 CodeA2 둘 다에 대해 1024 MB의 메모리 요청을 지정 하는 데 사용 됩니다. 메모리 제한 ( `MemoryInMBLimit` 특성)을 지정 하지 않았기 때문에 Service Fabric는 또한 지정 된 요청 값을 코드 패키지에 대 한 제한으로 사용 합니다. 서비스 패키지에 대 한 메모리 요청 (및 제한)은 해당 구성 코드 패키지의 메모리 요청 (및 제한) 값의 합계로 계산 됩니다. 따라서 **ServicePackageA**의 경우 메모리 요청 및 제한이 2048 MB로 계산 됩니다.

**ServicePackageA** 는 **해당 노드에 배치 된 모든 서비스 패키지의 메모리 요청 합계** 를 뺀 후 남은 메모리 용량이 2048 MB 보다 크거나 같은 노드에만 배치 됩니다. 노드에서 두 코드 패키지는 각각 1024 MB의 메모리로 제한 됩니다. 코드 패키지 (컨테이너 또는 프로세스)는이 제한 보다 많은 메모리를 할당할 수 없으며, 그렇게 하려고 하면 메모리 부족 예외가 발생 합니다.

**예 2: 한 사양만 지정**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
이 예제에서는 `CpuCoresLimit` 및 `MemoryInMBLimit` 특성을 사용 하며,이는 SF 버전 7.2 이상 에서만 사용할 수 있습니다. CpuCoresLimit 특성은 **ServicePackageA**에 대해 1 코어의 CPU 제한을 지정 하는 데 사용 됩니다. CPU 요청 ( `CpuCores` 특성)은 지정 되지 않으므로 0으로 간주 됩니다. `MemoryInMBLimit` 특성은 CodeA1 및 CodeA2에 대해 1024 MB의 메모리 제한을 지정 하는 데 사용 되며 `MemoryInMB` , 요청 (특성)을 지정 하지 않으면 0으로 간주 됩니다. 따라서 **ServicePackageA** 에 대 한 메모리 요청 및 제한은 각각 0과 2048로 계산 됩니다. **ServicePackageA** 에 대 한 CPU 및 메모리 요청은 모두 0 이므로 및 메트릭에 대해 CRM에서 배치를 고려할 수 있는 부하가 표시 되지 않습니다 `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` . 따라서 리소스 거 버 넌 스 관점에서 **ServicePackageA** 는 **남은 용량과 상관 없이**모든 노드에 배치할 수 있습니다. 예 1과 마찬가지로 노드의 CodeA1는 코어 및 1024 메모리의 2/3로 제한 되며 CodeA2는 코어 및 1024 MB의 1 1/3로 제한 됩니다.

**예제 3: RequestsAndLimits 사양**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
처음 두 예제를 기반으로 하는이 예제에서는 CPU와 메모리에 대 한 요청과 제한을 모두 지정 하는 방법을 보여 줍니다. **ServicePackageA** 에는 1 코어 및 3072 (1024 + 2048) MB의 CPU 및 메모리 요청이 각각 있습니다. 노드의 총 CPU (및 메모리) 용량에서 노드에 배치 된 모든 서비스 패키지의 모든 CPU (및 메모리) 요청 합계를 뺀 후에는 최소 1 코어 (및 3072 MB)의 용량을 초과 하는 노드에서만 배치할 수 있습니다. 노드에서 CodeA1는 2 코어 및 3072 MB의 메모리로 제한 되지만 CodeA2은 2 개 코어 및 4096 MB의 메모리로 제한 됩니다.

### <a name="using-application-parameters"></a>애플리케이션 매개 변수 사용

리소스 거 버 넌 스 설정을 지정 하는 경우 [응용 프로그램 매개 변수](service-fabric-manage-multiple-environment-app-configuration.md) 를 사용 하 여 여러 앱 구성을 관리할 수 있습니다. 다음 예제에서는 애플리케이션 매개 변수의 사용법을 보여 줍니다.

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

이 예제에서는 기본 매개 변수 값이 프로덕션 환경에 대해 설정되며, 각 서비스 패키지에 4개 코어와 2GB의 메모리가 할당됩니다. 애플리케이션 매개 변수 파일을 사용해 기본값을 변경할 수 있습니다. 이 예제에서는 애플리케이션을 로컬로 테스트하는 데 하나의 매개 변수 파일을 사용할 수 있습니다. 이 경우 프로덕션 환경보다 리소스가 덜 사용됩니다.

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> 애플리케이션 매개 변수를 사용해서 리소스 관리를 지정하는 방식은 Service Fabric 버전 6.1부터 사용할 수 있습니다.<br>
>
> 리소스 관리를 지정하는 데 애플리케이션 매개 변수를 사용할 경우 Service Fabric을 버전 6.1 이전 버전으로 다운그레이드할 수 없습니다.

## <a name="enforcing-the-resource-limits-for-user-services"></a>사용자 서비스에 대 한 리소스 제한 적용

Service Fabric 서비스에 리소스 관리를 적용 하는 동안 해당 리소스 관리 서비스가 리소스 할당량을 초과할 수 없도록 보장 하는 반면, 많은 사용자는 여전히 Service Fabric 서비스 중 일부를 비관리 모드로 실행 해야 합니다. 비관리 Service Fabric 서비스를 사용 하는 경우 "런어웨이" 비관리 서비스에서 Service Fabric 노드에서 사용 가능한 모든 리소스를 사용 하 여 다음과 같은 심각한 문제가 발생할 수 있는 상황이 발생할 수 있습니다.

* 노드 (Service Fabric 시스템 서비스 포함)에서 실행 되는 다른 서비스의 리소스 부족
* 비정상 상태에서 종료 되는 노드
* 클러스터 관리 Api Service Fabric 응답 하지 않음

이러한 상황이 발생 하는 것을 방지 하기 위해 Service Fabric를 사용 하면 *노드에서 실행 중인 모든 Service Fabric 사용자 서비스* (관리 및 비관리 모두)에 대해 리소스 제한을 적용 하 여 사용자 서비스가 지정 된 양의 리소스를 사용 하지 않도록 보장할 수 있습니다. 이는 ClusterManifest의 PlacementAndLoadBalancing 섹션에 있는 EnforceUserServiceMetricCapacities config 값을 true로 설정 하 여 수행 됩니다. 이 설정은 기본적으로 해제 되어 있습니다.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

추가 설명:

* 리소스 제한 적용은 `servicefabric:/_CpuCores` 및 `servicefabric:/_MemoryInMB` 리소스 메트릭에만 적용 됩니다.
* 리소스 한도 적용은 자동 검색 메커니즘을 통해 또는 사용자가 수동으로 노드 용량을 지정 하 여 ( [리소스 관리를 사용 하도록 설정 하기 위해 클러스터 설정](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) 섹션에서 설명한 대로) 리소스 메트릭의 노드 용량을 Service Fabric 사용할 수 있는 경우에만 작동 합니다.노드 용량이 구성 되지 않은 경우 Service Fabric 사용자 서비스에 대해 예약할 리소스의 양을 알 수 없기 때문에 리소스 제한 적용 기능을 사용할 수 없습니다."EnforceUserServiceMetricCapacities"가 true 이지만 노드 용량이 구성 되지 않은 경우 Service Fabric에서 상태 경고를 실행 합니다.

## <a name="other-resources-for-containers"></a>컨테이너에 대한 기타 리소스

CPU 및 메모리 외에도 컨테이너의 다른 리소스 한도를 지정할 수 있습니다. 이러한 한도는 코드 패키지 수준에서 지정되며 컨테이너를 시작하면 적용됩니다. CPU 및 메모리와는 달리 클러스터 리소스 관리자는 이러한 리소스를 인식하지 않으며 그에 대한 용량 확인이나 부하 분산을 수행하지 않습니다.

* *MemorySwapInMB*: 컨테이너가 사용할 수 있는 스왑 메모리 크기입니다.
* *MemoryReservationInMB*: 노드에서 메모리 경합이 확인된 경우에만 시행되는 메모리 거버넌스의 소프트 제한입니다.
* *CpuPercent*: 컨테이너가 사용할 수 있는 CPU의 백분율입니다. 서비스 패키지에 대해 CPU 요청 또는 제한이 지정 된 경우에는이 매개 변수가 효과적으로 무시 됩니다.
* *MaximumIOps*: 컨테이너가 사용할 수 있는 최대 IOPS입니다(읽기 및 쓰기).
* *MaximumIOBytesps*: 컨테이너가 사용할 수 있는 최대 IO(초당 바이트)입니다(읽기 및 쓰기).
* *BlockIOWeight*: 타 컨테이너에 상대적인 블록 IO 가중치입니다.

이러한 리소스를 CPU 및 메모리와 결합할 수 있습니다. 컨테이너에 대한 추가 리소스를 지정하는 방법의 예는 다음과 같습니다.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>다음 단계

* 클러스터 리소스 관리자에 대한 자세한 내용은 [Service Fabric 클러스터 리소스 관리자 소개](service-fabric-cluster-resource-manager-introduction.md)를 확인하세요.
* 애플리케이션 모델, 서비스 패키지, 코드 패키지 및 복제본 매핑 방식에 대한 자세한 내용은 [Service Fabric의 애플리케이션 모델링][application-model-link]을 참조하세요.

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md
