---
title: Azure 서비스 패브릭에 대한 용량 계획 및 확장
description: Service Fabric 클러스터 및 애플리케이션 계획 및 크기 조정을 위한 모범 사례를 소개합니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258994"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Azure 서비스 패브릭에 대한 용량 계획 및 확장

Azure Service Fabric 클러스터를 만들거나 클러스터를 호스트하는 계산 리소스를 확장하기 전에 용량을 계획하는 것이 중요합니다. 용량 계획에 대한 자세한 내용은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요. 클러스터 확장성에 대한 추가 모범 사례 지침은 [서비스 패브릭 확장성 고려 사항을](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)참조하십시오.

노드 유형 및 클러스터 특성을 고려하는 것 외에도 프로덕션 환경에 대해 크기 조정 작업을 완료하는 데 1시간 이상 걸릴 것으로 예상해야 합니다. 이 고려 사항은 추가하는 VM 수에 관계없이 사실입니다.

## <a name="autoscaling"></a>자동 확장
[리소스 구성을 코드로]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)처리하는 것이 가장 좋은 방법입니다. 

가상 시스템 크기 조정 집합을 통한 자동 크기 조정을 사용하면 버전이 변경된 Resource Manager 템플릿이 가상 시스템 크기 집합에 대한 인스턴스 수를 부정확하게 정의할 수 있습니다. 정의가 부정확하면 향후 배포로 인해 의도하지 않은 크기 조정 작업이 발생할 위험이 높아집니다. 일반적으로 다음과 같은 경우 자동 크기 조정을 사용해야 합니다.

* 적절한 용량을 선언하여 Resource Manager 템플릿을 배포하는 과정에서 사용 사례가 지원되지 않는 경우
     
   수동 크기 조정 외에도 Azure [리소스 그룹 배포 프로젝트를 사용하여 Azure DevOps 서비스에서 지속적인 통합 및 배달 파이프라인을 구성할](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)수 있습니다. 이 파이프라인은 일반적으로 [Azure Monitor REST API에서](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)쿼리된 가상 시스템 성능 메트릭을 사용하는 논리 앱에 의해 트리거됩니다. 파이프라인은 리소스 관리자 템플릿에 최적화하면서 원하는 메트릭에 따라 효과적으로 자동 조정됩니다.
* 한 번에 하나의 가상 시스템 스케일 세트 노드만 수평으로 확장해야 합니다.
   
   한 번에 세 개 이상의 노드로 확장하려면 [가상 시스템 크기 집합을 추가하여 Service Fabric 클러스터를 확장해야](virtual-machine-scale-set-scale-node-type-scale-out.md)합니다. 한 번에 하나의 노드를 수평으로 확장하여 확장할 수 있습니다.
* 서비스 패브릭 클러스터의 실버 안정성 또는 그 이상, 자동 크기 조정 규칙을 구성하는 모든 규모에서 실버 내구성 이상.
  
   자동 크기 조정 규칙에 대한 최소 용량은 5개의 가상 시스템 인스턴스와 같거나 커야 합니다. 또한 기본 노드 유형에 대한 안정성 계층 최소값과 같거나 커야 합니다.

> [!NOTE]
> 서비스 패브릭 상태 저장 서비스 패브릭:/시스템/InfastructureService/<NODE_TYPE_NAME>> 실버 또는 더 높은 내구성이 있는 모든 노드 유형에서 실행됩니다. 클러스터 노드 유형에서 Azure에서 실행되도록 지원되는 유일한 시스템 서비스입니다.

## <a name="vertical-scaling-considerations"></a>수직 크기 조정 관련 고려 사항

Azure 서비스 패브릭에서 노드 유형을 [수직으로 확장하려면](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) 여러 단계와 고려 사항이 필요합니다. 예를 들어:

* 크기 조정 전에 클러스터가 정상 상태여야 합니다. 그렇지 않으면 클러스터를 더 불안정하게 만들 수 있습니다.
* 상태 저장 서비스를 호스트하는 모든 Service Fabric 클러스터 노드 유형에는 실버 내구성 수준 이상이 필요합니다.

> [!NOTE]
> 상태 저장 서비스 패브릭 시스템 서비스를 호스팅하는 기본 노드 유형은 실버 내구성 수준 이상이어야 합니다. Silver 내구성을 사용하도록 설정하면 시스템이 작업 속도에 비해 데이터 안전을 최적화하기 때문에 업그레이드, 노드 추가 또는 제거 와 같은 클러스터 작업이 느려집니다.

가상 시스템 크기 집합을 수직으로 조정하는 것은 파괴적인 작업입니다. 대신 원하는 SKU로 새 축척 집합을 추가하여 클러스터의 수평으로 확장합니다. 그런 다음 서비스를 원하는 SKU로 마이그레이션하여 안전한 수직 크기 조정 작업을 완료합니다. 가상 시스템 규모 집합 리소스 SKU를 변경하는 것은 호스트를 이미지화하여 로컬로 유지되는 모든 상태를 제거하기 때문에 파괴적인 작업입니다.

클러스터는 Service Fabric [노드 속성 및 배치 제약 조건을](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) 사용하여 응용 프로그램의 서비스를 호스팅할 위치를 결정합니다. 기본 노드 유형을 세로로 확장할 때 에 대해 `"nodeTypeRef"`동일한 속성 값을 선언합니다. 가상 시스템 배율 집합에 대한 서비스 패브릭 확장에서 이러한 값을 찾을 수 있습니다. 

리소스 관리자 템플릿의 다음 코드 조각에는 선언할 속성이 표시됩니다. 새로 프로비전된 스케일 집합에 대해 확장하려는 값과 동일하며 클러스터에 대한 임시 상태 저장 서비스로만 지원됩니다.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 성공적인 수직 크기 조정 작업을 완료하는 데 필요한 `nodeTypeRef` 것보다 더 오래 동일한 속성 값을 사용하는 여러 축척 집합으로 클러스터를 실행하지 마십시오.
>
> 프로덕션 환경변경을 시도하기 전에 항상 테스트 환경에서 작업의 유효성을 검사합니다. 기본적으로 Service Fabric 클러스터 시스템 서비스에는 대상 기본 노드 유형에만 배치 제약 조건이 있습니다.

노드 속성 및 배치 제약 조건을 선언한 후 다음 단계를 한 번에 한 VM 인스턴스씩 수행합니다. 이렇게 하면 새 복제본이 다른 곳에서 생성될 때 제거하는 VM 인스턴스에서 시스템 서비스(및 상태 저장 서비스)를 정상적으로 종료할 수 있습니다.

1. PowerShell에서 제거할 `Disable-ServiceFabricNode` 노드를 비활성화할 의도로 `RemoveNode` 실행합니다. 번호가 가장 큰 노드 형식을 제거합니다. 예를 들어 6노드 클러스터가 있는 경우 "MyNodeType_5" 가상 시스템 인스턴스를 제거합니다.
2. `Get-ServiceFabricNode`를 실행하여 노드가 사용하지 않도록 전환되었는지 확인합니다. 그렇지 않은 경우 노드가 사용되지 않도록 설정될 때까지 기다립니다. 각 노드에 대해 몇 시간이 걸릴 수 있습니다. 노드가 사용 안 함 상태로 전환될 때까지는 다음 단계를 진행하지 마세요.
3. 해당 노드 유형에서 VM 수를 하나씩 줄입니다. 그러면 번호가 가장 큰 VM 인스턴스가 제거됩니다.
4. 필요에 따라 1~3단계를 반복하되, 주 노드 형식의 인스턴스 수를 안정성 계층이 경고하는 크기보다 작게 줄이지 않아야 합니다. 권장 인스턴스 목록은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요.
5. 모든 VM이 사라지면 패브릭:/시스템/인프라 서비스/[노드 이름]에 오류 상태가 표시됩니다. 그런 다음 클러스터 리소스를 업데이트하여 노드 유형을 제거할 수 있습니다. ARM 템플릿 배포를 사용하거나 [Azure 리소스 관리자를](https://resources.azure.com)통해 클러스터 리소스를 편집할 수 있습니다. 이렇게 하면 패브릭:/시스템/인프라 서비스/[노드 유형] 오류 상태인 서비스가 제거되는 클러스터 업그레이드가 시작됩니다.
 6. 그 후 선택적으로 VMScaleSet을 삭제할 수 있습니다., 당신은 여전히 서비스 패브릭 탐색기 보기에서 "아래로"로 노드를 볼 수 있습니다. 마지막 단계는 명령으로 `Remove-ServiceFabricNodeState` 정리하는 것입니다.

## <a name="horizontal-scaling"></a>수평적 크기 조정

[수동으로](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) 또는 프로그래밍 방식으로 수평 크기 조정을 수행할 수 [있습니다.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)

> [!NOTE]
> 실버 또는 골드 내구성이 있는 노드 유형을 배율 조정하는 경우 배율이 느려집니다.

### <a name="scaling-out"></a>확장

특정 가상 시스템 규모 집합에 대한 인스턴스 수를 늘려 서비스 패브릭 클러스터를 확장합니다. 원하는 축척 에 대한 `AzureClient` ID를 사용하여 프로그래밍 방식으로 확장하여 용량을 늘릴 수 있습니다.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

수동으로 확장하려면 원하는 [가상 시스템 크기 집합](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 리소스의 SKU 속성에서 용량을 업데이트합니다.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>규모 감축

확장은 확장보다 더 많은 고려가 필요합니다. 예를 들어:

* 서비스 패브릭 시스템 서비스는 클러스터의 기본 노드 유형에서 실행됩니다. 인스턴스 수가 안정성 계층에서 보증하는 수보다 적어지도록 해당 노드 형식의 인스턴스를 종료하거나 인스턴스 수를 축소하지 마세요. 
* 상태 저장 서비스의 경우 가용성을 유지하고 서비스 상태를 유지하기 위해 항상 필요한 특정 수의 노드가 필요합니다. 최소한 파티션 또는 서비스의 대상 복제본 집합 개수와 동일한 노드 수가 필요합니다.

수동으로 규모를 감축하려면 다음 단계를 수행합니다.

1. PowerShell에서 제거할 `Disable-ServiceFabricNode` 노드를 비활성화할 의도로 `RemoveNode` 실행합니다. 번호가 가장 큰 노드 형식을 제거합니다. 예를 들어 6노드 클러스터가 있는 경우 "MyNodeType_5" 가상 시스템 인스턴스를 제거합니다.
2. `Get-ServiceFabricNode`를 실행하여 노드가 사용하지 않도록 전환되었는지 확인합니다. 그렇지 않은 경우 노드가 사용되지 않도록 설정될 때까지 기다립니다. 각 노드에 대해 몇 시간이 걸릴 수 있습니다. 노드가 사용 안 함 상태로 전환될 때까지는 다음 단계를 진행하지 마세요.
3. 해당 노드 유형에서 VM 수를 하나씩 줄입니다. 그러면 번호가 가장 큰 VM 인스턴스가 제거됩니다.
4. 원하는 용량을 프로비저닝할 때까지 필요에 따라 1~3단계를 반복합니다. 단, 주 노드 형식의 인스턴스 수가 안정성 계층에서 보증되는 수보다 적어지도록 클러스터를 축소해서는 안 됩니다. 권장 인스턴스 목록은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요.

수동으로 확장하려면 원하는 [가상 시스템 크기 집합](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 리소스의 SKU 속성에서 용량을 업데이트합니다.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

프로그래밍 방식으로 확장할 수 있도록 노드를 준비해야 합니다. 제거할 노드(가장 높은 인스턴스 노드)를 찾습니다. 예를 들어:

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

이전 코드에서 사용한 것과 동일한 `FabricClient` 인스턴스(이`client` 경우) 및`instanceIdString` 노드 인스턴스(이 경우)를 사용하여 노드를 비활성화하고 제거합니다.

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
> 클러스터를 축소하면 서비스 패브릭 탐색기에서 제거된 노드/VM 인스턴스가 비정상 상태로 표시됩니다. 이 동작에 대한 설명은 [서비스 패브릭 탐색기에서 관찰할 수 있는 동작을](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)참조하십시오. 다음을 수행할 수 있습니다.
> * 적절한 노드 이름으로 [제거 서비스패브릭노드스테이트 명령을](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) 호출합니다.
> * [클러스터에 Service Fabric 자동 크기 조정 도우미 응용 프로그램을](https://github.com/Azure/service-fabric-autoscale-helper/) 배포합니다. 이 응용 프로그램은 확장 된 노드가 서비스 패브릭 탐색기에서 지워지도록 합니다.

## <a name="reliability-levels"></a>안정성 수준

[안정성 수준은](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) Service Fabric 클러스터 리소스의 속성입니다. 개별 노드 유형에 대해 다르게 구성할 수 없습니다. 클러스터에 대한 시스템 서비스의 복제 인자를 제어하는 이 속성은 클러스터 리소스 수준의 설정입니다. 

안정성 수준에 따라 주 노드 형식이 포함해야 하는 최소 노드 수가 결정됩니다. 안정성 계층은 다음 값을 사용할 수 있습니다.

* 플래티넘: 7개 및 9개의 시드 노드의 대상 복제본 집합 개수로 시스템 서비스를 실행합니다.
* Gold: 7개 및 7개의 시드 노드의 대상 복제본 집합 개수로 시스템 서비스를 실행합니다.
* Silver: 5개 및 5개의 시드 노드로 구성된 대상 복제본 집합 개수로 시스템 서비스를 실행합니다.
* 브론즈: 3개 및 3개의 시드 노드의 대상 복제본 집합 개수로 시스템 서비스를 실행합니다.

최소 권장 안정성 수준은 Silver입니다.

안정성 수준은 다음과 같이 [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)의 속성 섹션에서 설정됩니다.

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>내구성 수준

> [!WARNING]
> Bronze 내구성으로 실행되는 노드 형식은 _권한 없음_이 됩니다. 상태 비저장 워크로드에 영향을 주는 인프라 작업은 중지되거나 지연되지 않으며 워크로드에 영향을 줄 수 있습니다. 
>
> Bronze 내구성은 상태 비저장 워크로드를 실행하는 노드 형식에만 사용해야 합니다. 프로덕션 워크로드의 경우 상태 일관성을 보장하기 위해 Silver 또는 그 이상을 실행합니다. [용량 계획 설명서](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)의 지침에 따라 올바른 안정성을 선택합니다.

내구성 수준은 두 리소스에서 설정해야 합니다. 하나는 [가상 시스템 규모 집합 리소스의](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)확장 프로필입니다.

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

다른 리소스는 `nodeTypes` [Microsoft.ServiceFabric/클러스터 리소스에](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)있습니다. 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>다음 단계

* Windows 서버를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기.](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기.](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric 지원 옵션](service-fabric-support.md)에 대해 알아봅니다.

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
