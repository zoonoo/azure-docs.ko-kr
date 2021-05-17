---
title: Azure Service Fabric에 대한 용량 계획 및 크기 조정
description: Service Fabric 클러스터 및 애플리케이션 계획 및 크기 조정을 위한 모범 사례를 소개합니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: 32a9c26bb9e89cf4057cc753b02ad3c006d0bae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595069"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Azure Service Fabric에 대한 용량 계획 및 크기 조정

Azure Service Fabric 클러스터를 만들거나 클러스터를 호스트하는 컴퓨팅 리소스 크기를 조정하기 전에 용량을 계획하는 것이 중요합니다. 용량 계획에 대한 자세한 내용은 [Service Fabric 클러스터 용량 계획](./service-fabric-cluster-capacity.md)을 참조하세요. 클러스터 확장성에 대한 추가 모범 사례 지침은 [Service Fabric 확장성 고려 사항](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)을 참조하세요.

노드 형식 및 클러스터 특성을 고려하는 것 외에도 프로덕션 환경에 대한 크기 조정 작업을 완료하는 데 1시간 이상 소요될 것으로 예상해야 합니다. 이 고려 사항은 추가하는 VM 수에 관계없이 적용됩니다.

## <a name="autoscaling"></a>자동 확장
크기 조정 작업은 Azure Resource Manager 템플릿을 통해 수행해야 합니다. [리소스 구성을 코드로](./service-fabric-best-practices-infrastructure-as-code.md) 처리하는 것이 가장 좋은 방법이기 때문입니다. 

가상 머신 확장 집합을 통한 자동 크기 조정을 사용하면 버전이 지정된 Resource Manager 템플릿이 가상 머신 확장 집합의 인스턴스 수를 부정확하게 정의하게 됩니다. 부정확한 정의로 인해 향후 배포에서 의도하지 않은 크기 조정 작업이 발생할 위험이 늘어납니다. 일반적으로 다음과 같은 경우 자동 크기 조정을 사용해야 합니다.

* 적절한 용량을 선언하여 Resource Manager 템플릿을 배포하는 과정에서 사용 사례가 지원되지 않는 경우
     
   수동으로 크기를 조정하는 것 외에도 [Azure 리소스 그룹 배포 프로젝트를 사용하여 Azure DevOps Services에서 연속 통합 및 지속적인 업데이트 파이프라인](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)을 구성할 수 있습니다. 이 파이프라인은 일반적으로 [Azure Monitor REST API](../azure-monitor/essentials/rest-api-walkthrough.md)에서 쿼리된 가상 머신 성능 메트릭을 사용하는 논리 앱에 의해 트리거됩니다. 파이프라인은 Resource Manager 템플릿을 최적화하는 동안 원하는 메트릭에 따라 효율적으로 크기를 조정합니다.
* 한 번에 하나의 가상 머신 확장 집합 노드만 수평으로 확장/축소해야 하는 경우
   
   한 번에 세 개 이상의 노드를 스케일 아웃하려면 [가상 머신 확장 집합을 추가하여 Service Fabric 클러스터를 스케일 아웃](virtual-machine-scale-set-scale-node-type-scale-out.md)해야 합니다. 가상 머신 확장 집합을 한 번에 한 노드씩 수평으로 확장/축소하는 것이 가장 안전합니다.
* Service Fabric 클러스터의 안정성이 Silver 이상이고 자동 크기 조정 규칙을 구성하는 확장 집합의 내구성이 Silver 이상인 경우
  
   자동 크기 조정 규칙의 최소 용량은 5개 가상 머신 인스턴스보다 크거나 같아야 합니다. 또한 주 노드 형식의 안정성 계층 최솟값보다 크거나 같아야 합니다.

> [!NOTE]
> Service Fabric 상태 저장 서비스 fabric:/System/InfastructureService/<NODE_TYPE_NAME>은 내구성이 Silver 이상인 모든 노드 형식에서 실행됩니다. Azure에서 클러스터 노드 형식에서만 실행하도록 지원되는 유일한 시스템 서비스입니다.

> [!IMPORTANT]
> Service Fabric 자동 크기 조정은 `Default` 및 `NewestVM` 가상 머신 확장 집합 [ 스케일 인 구성](../virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy.md)을 지원합니다.

## <a name="vertical-scaling-considerations"></a>수직 크기 조정 관련 고려 사항

Azure Service Fabric에서 노드 형식의 [크기를 수직으로 조정](./virtual-machine-scale-set-scale-node-type-scale-out.md)하려면 여러 단계를 수행하고 다양한 사항을 고려해야 합니다. 예를 들면 다음과 같습니다.

* 크기 조정 전에 클러스터가 정상 상태여야 합니다. 그렇지 않으면 클러스터가 더 불안정해집니다.
* 상태 저장 서비스를 호스트하는 모든 Service Fabric 클러스터 노드 형식의 경우에는 내구성 수준이 Silver 이상이어야 합니다.

> [!NOTE]
> 상태 저장 Service Fabric 시스템 서비스를 호스트하는 주 노드 형식은 내구성 수준이 Silver 이상이어야 합니다. Silver 내구성을 사용하도록 설정하면 노드 업그레이드/추가/제거 등의 클러스터 작업 속도가 느려집니다. 시스템이 작업 속도 향상이 아닌 데이터 안전 유지를 위해 최적화되기 때문입니다.

단순히 리소스 SKU를 변경하는 가상 머신 확장 집합 수직 크기 조정은 파괴적인 작업입니다. 호스트가 이미지로 다시 설치되어 로컬로 유지되는 모든 상태가 제거되기 때문입니다. 대신, 원하는 SKU가 포함된 새 확장 집합을 추가하여 클러스터 크기를 수평으로 조정한 다음 서비스를 새 확장 집합으로 마이그레이션하면 수직 크기 조정 작업을 안전하게 완료할 수 있습니다.

클러스터는 Service Fabric [노드 속성 및 배치 제약 조건](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)을 사용하여 애플리케이션 서비스를 호스트할 위치를 결정합니다. 주 노드 형식을 수직으로 크기 조정하는 경우 두 번째 주 노드 형식을 배포한 다음, 원래 주 노드 형식에 (`"isPrimary": false`)를 설정하고, 해당 노드를 사용하지 않도록 설정하고, 해당 확장 집합 및 관련 리소스를 제거합니다. 자세한 내용은 [Service Fabric 클러스터 주 노드 형식 스케일 업](service-fabric-scale-up-primary-node-type.md)을 참조하세요.

> [!NOTE]
> 프로덕션 환경 변경을 시도하기 전에 항상 테스트 환경에서 작업 유효성을 검사하세요. 기본적으로 Service Fabric 클러스터 시스템 서비스에는 주 노드 형식만 대상으로 지정하는 배치 제약 조건이 있습니다.

노드 속성 및 배치 제약 조건을 선언한 후 다음 단계를 한 번에 한 VM 인스턴스씩 수행합니다. 이렇게 하면 다른 위치에서 새 복제본을 만들 때 제거하는 VM 인스턴스에서 시스템 서비스와 상태 저장 서비스가 정상 종료됩니다.

1. PowerShell에서 `RemoveNode` 의도를 포함하여 `Disable-ServiceFabricNode`를 실행해 제거할 노드를 사용하지 않도록 설정합니다. 번호가 가장 큰 노드 형식을 제거합니다. 예를 들어 6노드 클러스터가 있으면 'MyNodeType_5' 가상 머신 인스턴스를 제거합니다.
2. `Get-ServiceFabricNode`를 실행하여 노드가 사용하지 않도록 전환되었는지 확인합니다. 그렇지 않은 경우 노드가 사용되지 않도록 설정될 때까지 기다립니다. 각 노드마다 몇 시간씩 걸릴 수 있습니다. 노드가 사용 안 함 상태로 전환될 때까지는 다음 단계를 진행하지 마세요.
3. 해당 노드 형식의 VM 수를 1개 줄입니다. 그러면 번호가 가장 큰 VM 인스턴스가 제거됩니다.
4. 필요에 따라 1~3단계를 반복하되, 주 노드 형식의 인스턴스 수를 안정성 계층이 보장하는 것보다 적게 스케일 인하지 않아야 합니다. 권장 인스턴스 목록은 [Service Fabric 클러스터 용량 계획](./service-fabric-cluster-capacity.md)을 참조하세요.
5. 모든 VM이 사라지면('다운'으로 표시됨) fabric:/System/InfrastructureService/[node name]에 오류 상태가 표시됩니다. 그런 다음 클러스터 리소스를 업데이트하여 노드 형식을 제거할 수 있습니다. ARM 템플릿 배포를 사용하거나 [Azure Resource Manager](https://resources.azure.com)를 통해 클러스터 리소스를 편집할 수 있습니다. 그러면 클러스터 업그레이드가 시작되어 오류 상태인 fabric:/System/InfrastructureService/[node type] 서비스를 제거합니다.
 6. 그런 다음 필요에 따라 VMScaleSet를 삭제할 수 있습니다. 그러나 이 경우에도 노드는 Service Fabric Explorer 보기에서 '다운'으로 표시됩니다. 마지막 단계는 `Remove-ServiceFabricNodeState` 명령을 사용하여 정리하는 것입니다.

## <a name="horizontal-scaling"></a>수평 크기 조정

[수동으로](./service-fabric-cluster-scale-in-out.md) 또는 [프로그래밍 방식으로](./service-fabric-cluster-programmatic-scaling.md) 수평 크기 조정을 수행할 수 있습니다.

> [!NOTE]
> 내구성이 Silver 또는 Gold인 노드 형식의 크기를 조정하는 경우에는 크기 조정 속도가 느려집니다.

### <a name="scaling-out"></a>확장

특정 가상 머신 확장 집합의 인스턴스 수를 늘리는 방식으로 Service Fabric 클러스터를 스케일 아웃합니다. 원하는 확장 집합의 ID와 `AzureClient`를 사용해 용량을 늘려서 프로그래밍 방식으로 클러스터를 확장할 수 있습니다.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

수동으로 클러스터를 확장하려는 경우에는 원하는 [가상 머신 확장 집합](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 리소스의 SKU 속성에서 용량을 업데이트합니다.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>규모 감축

스케일 인의 경우에는 스케일 아웃보다 많은 사항을 고려해야 합니다. 다음 예를 참고하세요.

* Service Fabric 시스템 서비스는 클러스터의 주 노드 형식에서 실행됩니다. 인스턴스 수가 안정성 계층에서 보증하는 것보다 적어지도록 해당 노드 형식의 인스턴스를 종료하거나 스케일 인하지 마세요. 
* 상태 저장 서비스의 경우에는 가용성을 유지하고 서비스 상태를 보존하려면 특정 수의 노드가 항상 작동 상태여야 합니다. 최소한 파티션 또는 서비스의 대상 복제본 세트 수와 같은 수의 노드가 필요합니다.

수동으로 규모를 감축하려면 다음 단계를 수행합니다.

1. PowerShell에서 `RemoveNode` 의도를 포함하여 `Disable-ServiceFabricNode`를 실행해 제거할 노드를 사용하지 않도록 설정합니다. 번호가 가장 큰 노드 형식을 제거합니다. 예를 들어 6노드 클러스터가 있으면 'MyNodeType_5' 가상 머신 인스턴스를 제거합니다.
2. `Get-ServiceFabricNode`를 실행하여 노드가 사용하지 않도록 전환되었는지 확인합니다. 그렇지 않은 경우 노드가 사용되지 않도록 설정될 때까지 기다립니다. 각 노드마다 몇 시간씩 걸릴 수 있습니다. 노드가 사용 안 함 상태로 전환될 때까지는 다음 단계를 진행하지 마세요.
3. 해당 노드 형식의 VM 수를 1개 줄입니다. 그러면 번호가 가장 큰 VM 인스턴스가 제거됩니다.
4. 필요에 따라 1~3단계를 반복하여 원하는 용량을 프로비저닝합니다. 단, 주 노드 형식의 인스턴스 수가 안정성 계층에서 보증되는 것보다 적어지도록 클러스터를 스케일 인해서는 안 됩니다. 권장 인스턴스 목록은 [Service Fabric 클러스터 용량 계획](./service-fabric-cluster-capacity.md)을 참조하세요.

수동으로 스케일 인하려는 경우에는 원하는 [가상 머신 확장 집합](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 리소스의 SKU 속성에서 용량을 업데이트합니다.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

프로그래밍 방식으로 스케일 인하려면 종료할 노드를 준비해야 합니다. 제거할 노드(가장 높은 인스턴스 노드)를 찾습니다. 예를 들면 다음과 같습니다.

```csharp
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

위의 코드에서 사용한 것과 동일한 `FabricClient` 인스턴스(여기서는 `client`) 및 노드 인스턴스(여기서는 `instanceIdString`)를 사용하여 해당 노드를 비활성화하고 제거합니다.

```csharp
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
> 클러스터를 스케일 인하면 제거된 노드/VM 인스턴스가 Service Fabric Explorer에서 비정상 상태로 표시되는 것을 볼 수 있습니다. 이 동작에 대한 설명은 [Service Fabric Explorer에서 관찰할 수 있는 동작](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer)을 참조하세요. 다음을 할 수 있습니다.
> * 해당 노드 이름을 사용하여 [Remove-ServiceFabricNodeState 명령](/powershell/module/servicefabric/remove-servicefabricnodestate)을 호출합니다.
> * 클러스터에 [Service Fabric 자동 크기 조정 도우미 애플리케이션](https://github.com/Azure/service-fabric-autoscale-helper/)을 배포합니다. 이 애플리케이션은 스케일 다운된 노드가 Service Fabric Explorer에서 지워지는지 확인합니다.

## <a name="reliability-levels"></a>안정성 수준

[안정성 수준](./service-fabric-cluster-capacity.md)은 Service Fabric 클러스터 리소스의 속성입니다. 이 속성은 개별 노드 형식마다 다르게 구성할 수 없습니다. 클러스터에 대한 시스템 서비스의 복제 인자를 제어하는 이 속성은 클러스터 리소스 수준의 설정입니다. 

안정성 수준에 따라 주 노드 형식이 포함해야 하는 최소 노드 수가 결정됩니다. 안정성 계층은 다음 값을 사용할 수 있습니다.

* Platinum: 대상 복제본 세트 7개와 시드 노드 9개를 사용하여 시스템 서비스를 실행합니다.
* Gold: 대상 복제본 세트 7개와 시드 노드 7개를 사용하여 시스템 서비스를 실행합니다.
* Silver: 대상 복제본 세트 5개와 시드 노드 5개를 사용하여 시스템 서비스를 실행합니다.
* Bronze: 대상 복제본 세트 3개와 시드 노드 3개를 사용하여 시스템 서비스를 실행합니다.

최소 권장 안정성 수준은 Silver입니다.

안정성 수준은 다음과 같이 [Microsoft.ServiceFabric/clusters resource](/azure/templates/microsoft.servicefabric/2018-02-01/clusters)의 속성 섹션에서 설정됩니다.

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>내구성 수준

> [!WARNING]
> Bronze 내구성으로 실행되는 노드 형식은 _권한 없음_ 이 됩니다. 상태 비저장 워크로드에 영향을 주는 인프라 작업은 중지 또는 지연되지 않습니다. 워크로드에 영향을 줄 수 있기 때문입니다. 
>
> Bronze 내구성은 상태 비저장 워크로드를 실행하는 노드 형식에만 사용해야 합니다. 프로덕션 워크로드의 경우 상태 일관성을 유지할 수 있도록 Silver 이상을 실행하세요. [용량 계획 설명서](./service-fabric-cluster-capacity.md)의 지침에 따라 올바른 안정성을 선택합니다.

내구성 수준은 두 리소스에서 설정해야 합니다. 한 리소스는 [가상 머신 확장 집합 리소스](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)의 확장 프로필입니다.

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

다른 리소스는 [Microsoft.ServiceFabric/clusters resource](/azure/templates/microsoft.servicefabric/2018-02-01/clusters)의 `nodeTypes` 아래에 있습니다. 

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
