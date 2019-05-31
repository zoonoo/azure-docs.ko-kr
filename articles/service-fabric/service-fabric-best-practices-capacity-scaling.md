---
title: Azure Service Fabric 용량 계획 및 크기 조정 모범 사례 | Microsoft Docs
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
ms.openlocfilehash: 9bddb6552b11dd506ee3e2c1c416c15da11048b7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258747"
---
# <a name="capacity-planning-and-scaling"></a>용량 계획 및 크기 조정

Azure Service Fabric 클러스터를 만들거나 클러스터를 호스트하는 컴퓨팅 리소스 크기를 조정하기 전에 용량을 계획해야 합니다. 용량 계획에 대한 자세한 내용은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요. 클러스터 확장성에 대 한 지침 추가 모범 사례에 대 한 참조 [Service Fabric 확장성 고려 사항](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)

노드 유형 및 클러스터 특징을 고려 하는 것 외에도 추가 하는 Vm의 수에 관계 없이 프로덕션 환경에 대 한 완료 하는 데 한 시간 보다 오래 걸리는 작업 크기 조정에 대 한 계획 해야 합니다.

## <a name="auto-scaling"></a>자동 크기 조정
크기 조정 작업 수행 해야 Azure 리소스 템플릿 배포를 통해 처리 모범 사례 이므로 [코드로 리소스 구성]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code), 자동 크기 조정 하면 가상 머신 확장 집합을 사용 하 여 및 사용자 가상 머신 확장을 정확 하 게 정의 하는 버전이 지정 된 Resource Manager 템플릿이 설정 인스턴스 수입니다. 일반에 의도 하지 않은 크기 조정 작업을 유발 하는 향후 배포 시의 위험을 증가 하는 경우 자동 크기 조정를 사용 해야 합니다.

* 적절한 용량을 선언하여 Resource Manager 템플릿을 배포하는 과정에서 사용 사례가 지원되지 않는 경우
  * 수동 크기 조정 하는 것 외에도 구성할 수 있습니다는 [연속 통합 및 배달 파이프라인에 Azure 리소스 그룹 배포 프로젝트를 사용 하 여 Azure DevOps 서비스](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), 일반적으로 활용 하는 논리 앱에 의해 트리거되면는 쿼리 하는 가상 머신 성능 메트릭 [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)효과적으로 자동 크기 조정, Azure Resource Manager 값 추가 하기 위해 최적화 하는 동안 원하는 모든 메트릭을 기반으로 합니다.
* Virtual Machine Scale Set 노드를 한 번에 하나씩만 수평으로 크기를 조정하면 되는 경우
  * 3 개 이상의 노드에서 한 번에를 확장 하려면 [가상 머신 확장 집합을 추가 하 여 Service Fabric 클러스터를 확장](https://docs.microsoft.com/azure service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), 것이 크기를 가장 안전 하 고 가상 컴퓨터 확장 집합 노드 1 개 가로 시간입니다.
* Service Fabric 클러스터의 안정성이 Silver 이상이고 자동 크기 조정 규칙을 구성하는 Scale Set의 내구성이 Silver 이상인 경우
  * 자동 크기 조정 규칙 용량 (최소) 5 개의 가상 머신 인스턴스, 보다 크거나 같은 이어야 하며 주 노드 유형에 대 한 프로그램 안정성 계층 최소값 보다 크거나 같은 이어야 합니다.

> [!NOTE]
> Azure Service Fabric 상태 저장 서비스 패브릭: / System/InfastructureService/< NODE_TYPE_NAME > 모든 Silver 있는 노드 형식에는 Azure에서 클러스터 노드 형식 중 하나에서 실행 하려면 지원 되는 유일한 시스템 서비스는 높은 내 구성을 실행 합니다. .

## <a name="vertical-scaling-considerations"></a>수직 크기 조정 관련 고려 사항

[수직적 크기 조정은](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) Azure Service Fabric의 노드 유형을 다양 한 단계 및 고려 사항은 필요 합니다. 예를 들면 다음과 같습니다.

* 크기 조정 전에 클러스터가 정상 상태여야 합니다. 그렇지 않은 경우 추가 클러스터를 불안정만 됩니다.
* **실버 내구성 수준이 이상인** 상태 저장 서비스를 호스트 하는 모든 Service Fabric 클러스터 노드 형식에 필요 합니다.

> [!NOTE]
> 상태 저장 서비스 패브릭 시스템 서비스를 호스팅하는 주 노드 유형에 실버 내구성 수준 이상 이어야 합니다. Silver 내구성을 사용하도록 설정하고 나면 노드 업그레이드/추가/제거 등의 클러스터 작업 속도가 느려집니다. 시스템이 작업 속도 향상이 아닌 데이터 안전 유지를 위해 최적화되기 때문입니다.

가상 머신 확장 집합을 확장 하는 세로 안전 하지 않은 작업입니다. 그러므로 원하는 SKU가 포함된 새 Scale Set를 추가하여 클러스터 크기를 수평으로 조정하고 원하는 SKU로 서비스를 마이그레이션하면 수직 크기 조정 작업을 안전하게 완료할 수 있습니다. 가상 머신 확장 집합 리소스 SKU를 변경 되므로 작업을 로컬로 유지 된 모든 상태를 제거 하는 호스트를 이미지 다시 합니다.

클러스터는 Service Fabric [노드 속성 및 배치 제약 조건](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)을 사용하여 애플리케이션 서비스를 호스트할 위치를 결정합니다. 주 노드 유형에, 수직 확장에 대 한 동일한 속성 값을 선언 하는 경우 `"nodeTypeRef"`, Service Fabric 확장 집합 가상 머신 확장에서 찾을 수 있는 합니다. 다음의 Resource Manager 템플릿 코드 조각에는 확장 대상인 새로 프로비저닝한 Scale Sets에 대해 같은 값을 사용하여 선언해야 하는 속성이 나와 있습니다. 이 코드 조각은 클러스터의 임시 상태 저장 명령으로만 지원됩니다. 다음을 참고하세요.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 같은 `nodeTypeRef` 속성 값을 사용하는 여러 Scale Sets가 포함된 클러스터를 정상적인 수직 크기 조정 작업을 완료하는 데 필요한 시간보다 오랫동안 실행 상태로 유지하면 안 됩니다.
> 프로덕션 환경 변경을 시도하기 전에 항상 테스트 환경에서 작업 유효성을 검사하세요. 기본적으로 서비스 패브릭 클러스터 시스템 서비스 배치 제약 조건을 대상 주 노드 형식에 적용 합니다.

노드 속성 및 배치 제약 조건을 선언한 후 다음 단계를 한 번에 한 VM 인스턴스씩 수행합니다. 이렇게 하면 다른 위치에서 새 복제본을 만들 때 제거하는 VM 인스턴스에서 시스템 서비스와 상태 저장 서비스가 정상 종료됩니다.

1. PowerShell에서 'RemoveNode' 의도를 포함하여 `Disable-ServiceFabricNode`를 실행해 제거할 노드를 사용하지 않도록 설정합니다. 번호가 가장 큰 노드 형식을 제거합니다. 예를 들어 노드 클러스터가 6개이면 "MyNodeType_5" 가상 머신 인스턴스를 제거합니다.
2. `Get-ServiceFabricNode`를 실행하여 노드가 사용하지 않도록 전환되었는지 확인합니다. 그렇지 않은 경우 노드가 사용되지 않도록 설정될 때까지 기다립니다. 각 노드별로 몇 시간씩 기다려야 할 수 있습니다. 노드가 사용 안 함 상태로 전환될 때까지는 다음 단계를 진행하지 마세요.
3. 해당 노드 형식의 VM 수를 1개 줄입니다. 그러면 번호가 가장 큰 VM 인스턴스가 제거됩니다.
4. 필요에 따라 1~3단계를 반복하되, 주 노드 형식의 인스턴스 수를 안정성 계층이 경고하는 크기보다 작게 줄이지 않아야 합니다. 권장 인스턴스 목록은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요.

> [!NOTE]
> 세로 크기 조정 작업을 수행 하는 경우 지원 되는 시나리오는 다음과 같습니다. I 수 내 Service Fabric 클러스터 및 응용 프로그램 관리 되지 않는 디스크에서 Managed Disks로 마이그레이션 응용 프로그램 가동 중지 시간 없이 합니다. 새 가상 컴퓨터를 프로 비전 하 여 managed disks를 사용 하 여 확장 집합 및 배치 제약 조건을 사용 하 여 응용 프로그램 업그레이드는 수행 대상으로 하는 프로 비전 된 용량입니다. Service Fabric 클러스터 응용 프로그램 가동 중지 시간 없이 업그레이드 도메인에서 롤아웃 되는 프로 비전 된 클러스터 노드 용량에 작업을 예약할 수 있습니다. [Azure 부하 분산 장치 기본 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) 백 엔드 풀 끝점에는 단일 가용성 집합 또는 가상 머신 확장 집합의 가상 컴퓨터 일 수 있습니다. 즉, Service Fabric 시스템 응용 프로그램 확장 집합 간에 이동 하는 경우에 기본 SKU 부하 분산 장치를 사용할 수 없습니다, 그리고 서비스 패브릭의 임시 인해 발생 하지 않고 클러스터 관리 끝점에도 클러스터 및 해당 응용 프로그램 계속 실행 됩니다. 일반적으로 사용자는 기본 SKU LB 및 표준 SKU LB 리소스 사이의 가상 IP 주소 (VIP) 교체를 수행 하는 경우 표준 SKU 부하 분산 장치를 프로 비전, 향후 모든 약 완화 하기 위해 30 초 때문 인해 VIP를 교환 하는 데 필요한 합니다.

## <a name="horizontal-scaling"></a>수평적 크기 조정

Service Fabric에서는 [수동](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) 또는 [프로그래밍 방식](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)으로 Service Fabric 수평 크기 조정을 수행할 수 있습니다.

> [!NOTE]
> 내구성이 Silver 또는 Gold인 노드 형식의 크기를 조정하는 경우에는 크기 조정 속도가 느려집니다.

### <a name="scaling-out"></a>확장

특정 가상 머신 확장 집합의 인스턴스 수가 증가 하 여 Service Fabric 클러스터를 확장 합니다. 원하는 Scale Set의 ID와 AcureClient를 사용해 용량을 늘려서 프로그래밍 방식으로 클러스터를 확장할 수 있습니다.

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

* Service Fabric System Services는 클러스터의 주 노드 형식에서 실행됩니다. 인스턴스 수가 안정성 계층에서 보증하는 수보다 적어지도록 해당 노드 형식의 인스턴스를 종료하거나 인스턴스 수를 축소하지 마세요. 
* 상태 저장 서비스의 경우에는 가용성을 유지하고 서비스 상태를 보존하려면 특정 수의 노드가 항상 작동 상태여야 합니다. 최소한 파티션/서비스의 대상 복제본 세트 수와 같은 수의 노드가 필요합니다.

수동으로 규모를 감축하려면 다음 단계를 수행합니다.

1. PowerShell에서 'RemoveNode' 의도를 포함하여 `Disable-ServiceFabricNode`를 실행해 제거할 노드를 사용하지 않도록 설정합니다. 번호가 가장 큰 노드 형식을 제거합니다. 예를 들어 노드 클러스터가 6개이면 "MyNodeType_5" 가상 머신 인스턴스를 제거합니다.
2. `Get-ServiceFabricNode`를 실행하여 노드가 사용하지 않도록 전환되었는지 확인합니다. 그렇지 않은 경우 노드가 사용되지 않도록 설정될 때까지 기다립니다. 각 노드별로 몇 시간씩 기다려야 할 수 있습니다. 노드가 사용 안 함 상태로 전환될 때까지는 다음 단계를 진행하지 마세요.
3. 해당 노드 형식의 VM 수를 1개 줄입니다. 그러면 번호가 가장 큰 VM 인스턴스가 제거됩니다.
4. 필요에 따라 1~3단계를 반복하되, 주 노드 형식의 인스턴스 수를 안정성 계층이 경고하는 크기보다 작게 줄이지 않아야 합니다. 권장 인스턴스 목록은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요.

수동으로 규모를 감축 하려면 원하는 SKU 속성에 용량을 업데이트 [가상 머신 확장 집합](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 리소스입니다.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. 1~3단계를 반복하여 원하는 용량을 프로비저닝합니다. 단, 주 노드 형식의 인스턴스 수가 안정성 계층에서 보증되는 수보다 적어지도록 클러스터를 축소해서는 안 됩니다. 안정성 계층 및 각 계층에 필요한 인스턴스 수에 대한 자세한 내용은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요.

프로그래밍 방식으로 규모를 감축하려면 종료할 노드를 준비해야 합니다. 이 과정에서는 제거할 노드(번호가 가장 큰 인스턴스 노드)를 찾아서 비활성화합니다. 예를 들면 다음과 같습니다.

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

제거할 노드를 확인한 후에는 위의 코드에서 사용한 것과 같은 `FabricClient` 인스턴스(여기서는 `client`) 및 노드 인스턴스 이름(여기서는 `instanceIdString`)를 사용하여 해당 노드를 비활성화하고 제거합니다.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> 크기를 조정할 때 Service Fabric Explorer에서 비정상 상태로 표시 제거 된 노드/v M 인스턴스가 중단 클러스터에 표시 됩니다. 이 동작의 설명에 대 한 참조 [Service Fabric Explorer에서 볼 수 있는 동작](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)합니다. 다음을 수행할 수 있습니다.
> * 호출 [Remove-servicefabricnodestate cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) 적절 한 노드 이름과 함께 합니다.
> * 배포할 [service fabric 자동 크기 조정 도우미 응용 프로그램](https://github.com/Azure/service-fabric-autoscale-helper/) 노드 아래로 확장을 보장 하는 클러스터에서 Service Fabric Explorer에서 제거 됩니다.

## <a name="reliability-levels"></a>안정성 수준

합니다 [안정성 수준](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) 에 Service Fabric 클러스터 리소스의 속성 이며 개별 노드 형식에 대해 다르게 구성 될 수 없습니다. 클러스터에 대한 시스템 서비스의 복제 인자를 제어하는 이 속성은 클러스터 리소스 수준의 설정입니다. 안정성 수준에 따라 주 노드 형식이 포함해야 하는 최소 노드 수가 결정됩니다. 안정성 계층은 다음 값을 사용할 수 있습니다.

* Platinum - 대상 복제본 세트 7개와 시드 노드 9개를 사용하여 시스템 서비스를 실행합니다.
* Gold - 대상 복제본 세트 7개와 시드 노드 7개를 사용하여 시스템 서비스를 실행합니다.
* Silver - 대상 복제본 세트 5개와 시드 노드 5개를 사용하여 시스템 서비스를 실행합니다.
* Bronze - 대상 복제본 세트 3개와 시드 노드 3개를 사용하여 시스템 서비스를 실행합니다.

최소 권장 안정성 수준은 Silver입니다.

안정성 수준은 다음과 같이 [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)의 속성 섹션에서 설정됩니다.

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>내구성 수준

> [!WARNING]
> Bronze 내구성으로 실행되는 노드 형식은 _권한 없음_이 됩니다. 즉, 상태 비저장 워크로드에 영향을 주는 인프라 작업은 중지되거나 지연되지 않으며, 이는 워크로드에 영향을 줄 수 있습니다. Bronze 내구성은 상태 비저장 워크로드를 실행하는 노드 형식에만 사용해야 합니다. 프로덕션 워크로드의 경우 상태 일관성을 유지할 수 있도록 내구성이 Silver 이상인 노드 형식을 실행하세요. [용량 계획 설명서](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)의 지침에 따라 올바른 안정성을 선택합니다.

내구성 수준은 두 리소스에서 설정해야 합니다. 확장 프로필은 [가상 머신 확장 집합 리소스](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

그리고 [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)의 `nodeTypes` 아래에서도 설정합니다. 

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
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
