---
title: 용량 계획 및 Azure Service Fabric에 대 한 크기 조정 | Microsoft Docs
description: Service Fabric 클러스터 및 애플리케이션 계획 및 크기 조정을 위한 모범 사례를 소개합니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 8ba4763e8d4835911d33d21c0f5bb431851a649b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444710"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>용량 계획 및 Azure Service Fabric에 대 한 크기 조정

모든 Azure Service Fabric 클러스터를 만들기 전이나 계산 클러스터를 호스트 하는 리소스 크기 조정 것이 용량을 계획 해야 합니다. 용량 계획에 대한 자세한 내용은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요. 추가 모범 사례 지침은 클러스터 확장성 [Service Fabric 확장성 고려 사항](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)합니다.

노드 유형 및 클러스터 특징을 고려 하는 것 외에도 프로덕션 환경에 대 한 완료 하는 데 한 시간 보다 더 오래 걸릴 수 크기 조정 작업을 예상 해야 합니다. 이 고려 사항은 추가 하는 Vm의 수에 관계 없이 마찬가지입니다.

## <a name="autoscaling"></a>자동 확장
모범 사례를 처리 이기 때문에 Azure Resource Manager 템플릿을 통해 크기 조정 작업을 수행 해야 [코드와 리소스 구성을]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)합니다. 

가상 머신 확장 집합을 통해 자동 크기 조정을 사용 하 여 가상 머신 확장 집합에 인스턴스 수를 정확 하 게 정의 버전 Resource Manager 템플릿을 확인 합니다. 부정확 한 정의 향후 배포 의도 하지 않은 크기 조정 작업을 하면 된다는 위험이 증가 합니다. 일반적으로 하는 경우 자동 크기 조정을 사용 해야 합니다.

* 적절한 용량을 선언하여 Resource Manager 템플릿을 배포하는 과정에서 사용 사례가 지원되지 않는 경우
     
   수동 크기 조정 하는 것 외에도 구성할 수 있습니다는 [연속 통합 및 배달 파이프라인 Azure DevOps 서비스에서 Azure 리소스 그룹 배포 프로젝트를 사용 하 여](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)입니다. 이 파이프라인은 일반적으로 가상 머신 성능 메트릭에서 쿼리를 사용 하는 논리 앱을 트리거한 합니다 [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)합니다. 효과적으로 자동 크기 조정 기반 파이프라인을 모든 메트릭에 대 한 원하는 Resource Manager 템플릿 용을 최적화 합니다.
* 한 번에 하나의 가상 머신 확장 집합 노드에 수평으로 확장 해야 합니다.
   
   한 번에 세 개 이상의 노드에서 규모를 수행 해야 합니다 [가상 머신 확장 집합을 추가 하 여 Service Fabric 클러스터 확장](virtual-machine-scale-set-scale-node-type-scale-out.md)합니다. 크기를 조정 하는 가장 안전한 이며 가상 머신 확장의 확장 설정 하는 가로 방향으로, 한 번에 하나의 노드.
* 실버 안정성 있는 Service Fabric 클러스터 및 실버 내구성 이상 또는 자동 크기 조정 규칙을 구성할 수 있는 모든 규모 증가.
  
   가상 머신 인스턴스를 5 보다 크거나 같은 자동 크기 조정 규칙에 대 한 최소 용량 이어야 합니다. 주 노드 유형에 대 한 프로그램 안정성 계층 최소값 보다 크거나 같은 수도 있어야 합니다.

> [!NOTE]
> Service Fabric 상태 저장 service fabric: / System/InfastructureService/< NODE_TYPE_NAME > 실버 이상 내구성 있는 모든 노드 형식에 대해 실행 합니다. Azure에서 클러스터 노드 형식 중 하나에서 실행 하려면 지원 되는 유일한 시스템 서비스입니다.

## <a name="vertical-scaling-considerations"></a>수직 크기 조정 관련 고려 사항

[수직적 크기 조정은](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) Azure Service Fabric의 노드 유형을 다양 한 단계 및 고려 사항은 필요 합니다. 예를 들면 다음과 같습니다.

* 크기 조정 전에 클러스터가 정상 상태여야 합니다. 이 고, 그렇지 추가 클러스터 불안정 해질 수 있습니다.
* 실버 내구성 수준이 이상인가 상태 저장 서비스를 호스트 하는 모든 Service Fabric 클러스터 노드 유형에 필요 합니다.

> [!NOTE]
> 상태 저장 서비스 패브릭 시스템 서비스를 호스팅하는 주 노드 유형에 실버 내구성 수준 이상 이어야 합니다. 실버 지 속성, 업그레이드와 같은 클러스터 작업을 사용 하도록 설정한 후 추가 또는 노드 및 등에 제거 낮아집니다 시스템 작업의 속도 통해 데이터 보안에 대 한 최적화 하므로.

가상 머신 확장 집합을 확장 하는 세로 안전 하지 않은 작업입니다. 새 확장 원하는 SKU를 사용 하 여 집합을 추가 하 여 클러스터를 수평으로 확장 하십시오. 그런 다음, 안전한 세로 크기 조정 작업을 완료 하 여 원하는 SKU로 서비스를 마이그레이션하세요. 가상 머신 확장 집합 리소스 SKU를 변경 되므로 작업을 지속 된 상태를 로컬로 제거 하 여 호스트를 이미지로 다시 설치 합니다.

클러스터에서 Service Fabric [노드 속성 및 배치 제약 조건을](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) 응용 프로그램의 서비스를 호스트할 위치를 결정 합니다. 주 노드 유형에 수직적으로 확장 하 고, 경우에 동일한 속성 값에 대 한 선언 `"nodeTypeRef"`합니다. 가상 머신 확장 집합에 대 한 Service Fabric 확장에서 이러한 값을 찾을 수 있습니다. 

Resource Manager 템플릿의 다음 코드 조각은 선언에서는 속성을 보여 줍니다. 이를 확장 하 고 클러스터에 대 한 임시 상태 저장 서비스 으로만 지원 됩니다는 새로 프로 비전 된 확장 집합에 대 한 동일한 값을 갖습니다.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 에 클러스터를 동일한 것을 사용 하는 여러 확장 집합을 사용 하 여 실행을 종료 하지 `nodeTypeRef` 성공적인 세로 크기 조정 작업을 완료 하는 데 필요한 속성 값 보다 깁니다.
>
> 프로덕션 환경의 변화를 시도 하기 전에 테스트 환경에서 작업을 항상 확인 합니다. 기본적으로 Service Fabric 클러스터 시스템 서비스 배치 제약 조건을 대상 주 노드 유형에 적용 합니다.

노드 속성 및 배치 제약 조건을 선언한 후 다음 단계를 한 번에 한 VM 인스턴스씩 수행합니다. 이렇게 하면 시스템 서비스 (및 상태 저장 서비스)를 다른 곳에서 새 복제본 생성 될 때 제거 하려는 VM 인스턴스에서 정상적으로 종료할 수 있습니다.

1. PowerShell에서 실행 `Disable-ServiceFabricNode` 의도로 `RemoveNode` 제거 하려는 노드를 사용 하지 않도록 설정 합니다. 번호가 가장 큰 노드 형식을 제거합니다. 예를 들어 6 개의 노드 클러스터가 있는 경우 "MyNodeType_5" 가상 머신 인스턴스를 제거 합니다.
2. `Get-ServiceFabricNode`를 실행하여 노드가 사용하지 않도록 전환되었는지 확인합니다. 그렇지 않은 경우 노드가 사용되지 않도록 설정될 때까지 기다립니다. 각 노드에 대해 하는 몇 시간 걸릴 수 있습니다. 노드가 사용 안 함 상태로 전환될 때까지는 다음 단계를 진행하지 마세요.
3. 해당 노드 형식에 따라 Vm의 수를 줄입니다. 그러면 번호가 가장 큰 VM 인스턴스가 제거됩니다.
4. 필요에 따라 1~3단계를 반복하되, 주 노드 형식의 인스턴스 수를 안정성 계층이 경고하는 크기보다 작게 줄이지 않아야 합니다. 권장 인스턴스 목록은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요.
5. ("Down"으로 표시 됨) 모든 Vm이 삭제 되 면 패브릭: / 시스템/InfrastructureService / [이름 노드]는 오류 상태를 표시 합니다. 그런 다음 노드 유형을 제거 하려면 클러스터 리소스를 업데이트할 수 있습니다. ARM 템플릿 배포를 사용 하거나 클러스터 리소스를 통해 편집 합니다 [Azure resource manager](https://resources.azure.com)합니다. 패브릭을 제거 하는 클러스터 업그레이드가 시작 됩니다. / 시스템/InfrastructureService / [노드 유형] 서비스는 오류 상태입니다.
 6. 이후에 VMScaleSet을 선택적으로 삭제할 수 있습니다에 "축소" Service Fabric Explorer에서 하지만 볼 노드를 계속 표시 됩니다. 마지막 단계를 사용 하 여 정리 하는 것 `Remove-ServiceFabricNodeState` 명령입니다.

### <a name="example-scenario"></a>예제 시나리오
세로 크기 조정 작업을 수행 하는 경우에 대 한 지원 되는 시나리오: 응용 프로그램 가동 중지 시간 없이 managed disks로 관리 되지 않는 디스크에서 Service Fabric 클러스터 및 응용 프로그램을 마이그레이션할 합니다. 

Managed disks 사용 하 여 새 가상 머신 확장 집합을 프로 비전 하 고 프로 비전 된 용량을 대상으로 하는 배치 제약 조건 사용 하 여 응용 프로그램 업그레이드를 수행할 수 있습니다. Service Fabric 클러스터 응용 프로그램 가동 중지 시간 없이 업그레이드 도메인에서 롤아웃 되는 프로 비전 된 클러스터 노드 용량에 대 한 워크 로드가 예약할 수 있습니다. 

에 대 한 백 엔드 풀 끝점을 [Azure Load Balancer 기본 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) 단일 가용성 집합 또는 가상 머신 확장 집합에서 가상 컴퓨터 일 수 있습니다. 즉, Service Fabric 클러스터 관리 끝점에 액세스할 수 없으므로 임시 없이 확장 집합 간에 서비스 패브릭 시스템 응용 프로그램을 이동 하는 경우에 기본 SKU 부하 분산 장치를 사용할 수 없습니다. 클러스터 및 해당 응용 프로그램은 계속 실행 하는 경우에 마찬가지입니다.

사용자가 기본 SKU 부하 분산 장치 표준 SKU 부하 분산 장치 리소스 사이의 가상 IP (VIP) 주소 교환을 수행 하는 경우 표준 SKU 부하 분산 장치는 일반적으로 프로 비전 합니다. 이 기술은 30 초 정도 VIP를 교환 하는 데 필요한 모든 향후 진보를 제한 합니다.

## <a name="horizontal-scaling"></a>수평적 크기 조정

수평적 크기 조정 중 하나를 수행할 수 있습니다 [수동으로](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) 하거나 [프로그래밍 방식으로](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)입니다.

> [!NOTE]
> 실버 또는 골드 내구성 있는 노드 형식 크기를 조정 하는 경우에 크기 조정 느려질 수 있습니다.

### <a name="scaling-out"></a>확장

특정 가상 머신 확장 집합의 인스턴스 수가 증가 하 여 Service Fabric 클러스터를 확장 합니다. 확장할 수 있습니다 프로그래밍 방식으로 사용 하 여 `AzureClient` 및 원하는 확장 집합 용량 증가 대 한 ID입니다.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

수동으로 규모를 원하는 SKU 속성에 용량을 업데이트 [가상 머신 확장 집합](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 리소스입니다.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>규모 감축

규모를 감축하려면 확장할 때보다 더 많은 사항을 고려해야 합니다. 예를 들면 다음과 같습니다.

* Service Fabric 시스템 서비스가 클러스터의 주 노드 형식에서 실행합니다. 인스턴스 수가 안정성 계층에서 보증하는 수보다 적어지도록 해당 노드 형식의 인스턴스를 종료하거나 인스턴스 수를 축소하지 마세요. 
* 상태 저장 서비스에 대 한 특정는 항상 가용성을 유지 관리 하 고 서비스의 상태를 유지 하는 노드 수가 필요 합니다. 최소한 해야 노드 수가 같은 파티션 또는 서비스의 대상 복제본 세트 수 합니다.

수동으로 규모를 감축하려면 다음 단계를 수행합니다.

1. PowerShell에서 실행 `Disable-ServiceFabricNode` 의도로 `RemoveNode` 제거 하려는 노드를 사용 하지 않도록 설정 합니다. 번호가 가장 큰 노드 형식을 제거합니다. 예를 들어 6 개의 노드 클러스터가 있는 경우 "MyNodeType_5" 가상 머신 인스턴스를 제거 합니다.
2. `Get-ServiceFabricNode`를 실행하여 노드가 사용하지 않도록 전환되었는지 확인합니다. 그렇지 않은 경우 노드가 사용되지 않도록 설정될 때까지 기다립니다. 각 노드에 대해 하는 몇 시간 걸릴 수 있습니다. 노드가 사용 안 함 상태로 전환될 때까지는 다음 단계를 진행하지 마세요.
3. 해당 노드 형식에 따라 Vm의 수를 줄입니다. 그러면 번호가 가장 큰 VM 인스턴스가 제거됩니다.
4. 1 ~ 3 용량을 프로 비전 할 때까지 필요에 따라 단계를 반복 합니다. 단, 주 노드 형식의 인스턴스 수가 안정성 계층에서 보증되는 수보다 적어지도록 클러스터를 축소해서는 안 됩니다. 권장 인스턴스 목록은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요.

수동으로 규모를 감축 하려면 원하는 SKU 속성에 용량을 업데이트 [가상 머신 확장 집합](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 리소스입니다.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

프로그래밍 방식으로 크기 조정 하는 종료에 대 한 노드를 준비 해야 합니다. 해당 노드를 제거 (가장 높은 인스턴스 노드)를 찾습니다. 예를 들면 다음과 같습니다.

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

비활성화 하 고 사용 하 여 노드를 제거 `FabricClient` 인스턴스 (`client` 여기서) 및 노드 인스턴스 (`instanceIdString` 이 경우) 앞의 코드에서는:

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> 클러스터를 축소 하는 경우 Service Fabric Explorer에서 비정상 상태로 표시 제거 된 노드/v M 인스턴스가 표시 됩니다. 이 동작의 설명에 대 한 참조 [Service Fabric Explorer에서 볼 수 있는 동작](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)합니다. 다음을 수행할 수 있습니다.
> * 호출 된 [Remove-servicefabricnodestate 명령](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) 적절 한 노드 이름과 함께 합니다.
> * 배포 된 [Service Fabric 자동 크기 조정 도우미 응용 프로그램](https://github.com/Azure/service-fabric-autoscale-helper/) 클러스터에서. 이 응용 프로그램은 규모가 축소 된 노드는 Service Fabric Explorer에서 지워집니다.

## <a name="reliability-levels"></a>안정성 수준

합니다 [안정성 수준](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) Service Fabric 클러스터 리소스의 속성입니다. 개별 노드 형식에 대해 다르게 구성 될 수 없습니다. 클러스터에 대한 시스템 서비스의 복제 인자를 제어하는 이 속성은 클러스터 리소스 수준의 설정입니다. 

안정성 수준에 따라 주 노드 형식이 포함해야 하는 최소 노드 수가 결정됩니다. 안정성 계층은 다음 값을 사용할 수 있습니다.

* 플래티넘: 7 및 9 개의 시드 노드의 대상 복제본 세트 수를 사용 하 여 시스템 서비스를 실행합니다.
* 골드: 7 개의 일곱 개의 시드 노드와의 대상 복제본 세트 수를 사용 하 여 시스템 서비스를 실행합니다.
* silver: 시드 노드 5와 5 개의 대상 복제본 세트 수를 사용 하 여 시스템 서비스를 실행합니다.
* Bronze: 시드 노드 3 및 3 개의 대상 복제본 세트 수를 사용 하 여 시스템 서비스를 실행합니다.

최소 권장 안정성 수준은 Silver입니다.

안정성 수준은 다음과 같이 [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)의 속성 섹션에서 설정됩니다.

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>내구성 수준

> [!WARNING]
> Bronze 내구성으로 실행되는 노드 형식은 _권한 없음_이 됩니다. 상태 비저장 워크 로드에 영향을 주는 인프라 작업은 중지 되거나 되지 지연 워크 로드에 영향을 줄 수 있는 합니다. 
>
> Bronze 내구성은 상태 비저장 워크로드를 실행하는 노드 형식에만 사용해야 합니다. 프로덕션 워크 로드에 대 한 실버 이상 상태 일관성 확인을 실행 합니다. [용량 계획 설명서](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)의 지침에 따라 올바른 안정성을 선택합니다.

내구성 수준은 두 리소스에서 설정해야 합니다. 확장 프로필은 하나는 [가상 머신 확장 집합 리소스](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

다른 리소스는 아래 `nodeTypes` 에 [microsoft.servicefabric/clusters 리소스](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric 지원 옵션](service-fabric-support.md)에 대해 알아봅니다.

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
