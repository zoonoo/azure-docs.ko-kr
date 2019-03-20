---
title: Service Fabric 클러스터 규모 조정 | Microsoft Docs
description: Service Fabric 클러스터의 각 노드 형식/가상 머신 확장 집합에 대 한 자동 크기 조정 규칙을 설정 하 여 수요에 맞게 확장 합니다. 서비스 패브릭 클러스터에 노드 추가 또는 제거
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2019
ms.author: aljo
ms.openlocfilehash: 4c3a9f00ed92194c4f81ab44cb9ca86d4a85fea1
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224348"
---
# <a name="scale-a-cluster-in-or-out"></a>클러스터 규모 확장 또는 규모 감축

> [!WARNING]
> 이 섹션을 읽어보고 크기 조정

애플리케이션 워크로드에서 의도적인 계획이 필요한 원본에 대한 계산 리소스 크기 조정은 프로덕션 작업을 완료하는 데 항상 거의 한 시간 이상이 걸리며, 워크로드 및 비즈니스 컨텍스트를 이해해야 합니다. 사실상 이 작업을 전에 수행한 적이 없는 경우 이 문서의 나머지 부분을 계속하기 전에 [Service Fabric 클러스터 용량 계획 고려 사항](service-fabric-cluster-capacity.md)을 읽고 이해하여 시작하는 것이 좋습니다. 이 권장 사항은 의도하지 않은 LiveSite 문제를 방지하기 위한 것이며, 비 프로덕션 환경에 대해 수행하려는 작업을 성공적으로 테스트하는 것이 좋습니다. 언제든지 [프로덕션 문제를 보고하거나 Azure에 대한 유료 지원을 요청](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure)할 수 있습니다. 적절한 컨텍스트를 소유하는 이러한 작업을 수행하도록 할당된 엔지니어의 경우 이 문서는 크기 조정 작업을 설명하지만 크기 조정할 리소스(CPU, 저장소, 메모리), 크기 조정할 방향(세로 또는 가로로) 및 수행할 작업(리소스 템플릿 배포, 포털, PowerShell/CLI)과 같은 사용 사례에 적절한 작업을 결정하고 이해해야 합니다.

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>자동 크기 조정 규칙을 사용하거나 수동으로 Service Fabric 클러스터 크기 조정
가상 머신 확장 집합은 가상 머신의 컬렉션을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 계산 리소스입니다. Service Fabric 클러스터에 정의된 모든 노드 형식은 별도의 가상 머신 확장 집합으로 설정됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다. 에 대 한 자세한 내용은 합니다 [Service Fabric 노드 형식은](service-fabric-cluster-nodetypes.md) 문서. 클러스터에서 Service Fabric 노드 형식은 백 엔드에서 가상 머신 확장 집합 구성 되므로, 각 노드 형식/가상 머신 확장 집합에 대 한 자동 크기 조정 규칙을 설정 해야 합니다.

> [!NOTE]
> 사용자의 구독에 이 클러스터를 형성할 새 VM을 추가하기에 충분한 코어가 있어야 합니다. 현재는 모델 유효성 검사가 없으므로 할당량 한도에 도달하면 배포 시간 오류가 발생합니다. 또한 단일 노드 형식은 VMSS당 100개의 노드를 초과할 수 없습니다. 대상 규모를 달성하려면 VMSS를 추가해야 할 수 있으며, 자동 크기 조정은 자동으로 VMSS를 추가할 수 없습니다. 실시간 클러스터 대신 VMSS를 추가하는 것은 어려운 작업이며, 일반적으로 이로 인해 사용자가 생성 시 프로비전된 적절한 노드 형식을 사용하여 새 클러스터를 프로비전합니다. 적절하게 [클러스터 용량을 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)합니다. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>크기를 조정할 노드 형식/Virtual Machine 확장 집합 선택
현재는 포털에서 Service Fabric 클러스터를 만들어 가상 머신 확장 집합에 대한 자동 크기 조정 규칙을 지정할 수 없으므로 Azure PowerShell(1.0+)을 사용하여 노드 형식을 나열한 후 자동 크기 조정 규칙을 추가하도록 합니다.

클러스터를 구성하는 가상 머신 확장 집합 목록을 가져오려면 다음 cmdlet을 실행합니다.

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>노드 형식/가상 머신 확장 집합에 대 한 자동 크기 조정 규칙 설정
클러스터에 여러 노드 형식이 있는 경우 각 노드 형식/가상 머신 확장에 대 한 설정 (내부 또는 외부)를 확장 하려는 반복 합니다. 자동 크기 조정을 수행하기 전에 포함해야 할 노드 수를 고려합니다. 기본 노드 형식에 대해 포함해야 할 최소 노드 수는 선택한 안정성 수준에 따라 달라집니다. [안정성 수준](service-fabric-cluster-capacity.md)에 대해 자세히 알아보세요.

> [!NOTE]
> 기본 노드 형식을 최소 수보다 적게 축소하면 클러스터가 불안정해지거나 중단됩니다. 이 경우 애플리케이션 및 시스템 서비스에 대한 데이터가 손실될 수 있습니다.
> 
> 

현재는 자동 크기 조정 기능이 애플리케이션에서 Service Fabric에 보고할 수 있는 로드에 따라 결정되지 않습니다. 따라서 현재는 자동 크기 조정이 각 가상 머신 확장 집합 인스턴스에서 내보낸 성능 카운터에 의해서만 결정됩니다.  

다음이 지침을 따르세요 [각 가상 머신 확장 집합에 대 한 자동 크기 조정 설정에](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)입니다.

> [!NOTE]
> 규모 확장 시나리오에서에서 노드 형식에 있는 경우가 아니면를 [내구성 수준이] [ durability] 골드 또는 실버 호출 해야 합니다 [Remove-servicefabricnodestate cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) 사용 하 여를 적절 한 노드 이름입니다. 브론즈 지 속성에 하지 것이 좋습니다 한 번에 둘 이상의 노드를 축소 합니다.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>노드 형식/가상 머신 확장 집합에 수동으로 Vm 추가

규모를 확장할 때 확장 집합에 더 많은 가상 컴퓨터 인스턴스를 추가합니다. 이러한 인스턴스는 Service Fabric 에서 사용하는 노드가 됩니다. Service Fabric은 확장 집합에 더 많은 인스턴스가 추가되고(규모 확장) 자동으로 반응할 때를 압니다. 

> [!NOTE]
> 순식간에 추가 하지 않는 시간이 Vm을 추가 합니다. 따라서 10 분 동안 VM 용량을 세우도록 복제본/서비스 인스턴스에 대 한 사용할 수 있도록 미리, 용량을 추가 하려고 합니다.
> 

### <a name="add-vms-using-a-template"></a>템플릿을 사용 하 여 Vm 추가
샘플/지침에 따라 합니다 [빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) 각 노드 형식의 Vm 수를 변경 합니다. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>PowerShell 또는 CLI 명령을 사용 하 여 Vm 추가
다음 코드는 이름별로 확장 집합을 가져오고 확장 집합의 **용량**을 1단위로 늘립니다.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

이 코드는 용량을 6으로 설정합니다.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>노드 형식/가상 머신 확장 집합에서 수동으로 Vm 제거
노드 형식 규모를 감축 하는 경우에 확장 집합에서 VM 인스턴스를 제거 합니다. 노드 형식의 내구성 수준이 Bronze 인 경우 Service Fabric은 발생 한 내용과는 못하며 노드가 누락 되었다고 보고 인식 되지 않습니다. 그러면 Service Fabric은 클러스터의 비정상 상태를 보고합니다. 잘못 된 상태를 방지 하려면 클러스터에서 노드를 제거 하 고 노드 상태가 제거 명시적으로 해야 합니다.

서비스 패브릭 시스템 서비스는 클러스터의 주 노드 형식에서 실행합니다. 주 노드 형식의 규모를 축소 하는 경우 없습니다 축소 보다 작은 값으로 인스턴스 수를 [안정성 계층](service-fabric-cluster-capacity.md) 를 보증 합니다. 
 
상태 저장 서비스의 경우, 서비스의 가용성을 유지 관리하고 상태를 유지하기 위해 특정 수의 노드가 항상 활성화되어야 합니다. 최소한, 노드 수를 파티션/서비스의 대상 복제본 집합 수와 같게 유지해야 합니다.

### <a name="remove-the-service-fabric-node"></a>Service Fabric 노드 제거

노드 상태를 수동으로 제거 하기 위한 단계를 사용 하 여 노드 형식에만 적용을 *동* 내구성 계층입니다.  에 대 한 *Silver* 하 고 *골드* 내구성 계층이 단계는 자동으로 수행 플랫폼에서 합니다. 내구성에 자세한 내용은 [Service Fabric 클러스터 용량 계획][durability]을 참조하세요.

클러스터의 노드를 업그레이드 도메인과 장애 도메인 전체에 고르게 분산하여 균등하게 사용하려면 가장 최근에 생성된 노드를 먼저 제거해야 합니다. 즉, 노드는 생성된 순서의 역순으로 제거되어야 합니다. 가장 최근에 생성된 노드는 `virtual machine scale set InstanceId` 속성 값이 최대인 노드입니다. 아래 코드 예제에는 가장 최근에 생성된 노드를 반환합니다.

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Service Fabric 클러스터는 이 노드가 제거될 것임을 알아야 합니다. 수행해야 하는 세 가지 단계가 있습니다.

1. 더 이상 데이터가 복제되지 않도록 노드를 사용하지 않도록 설정합니다.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. 노드를 중지하여 Service Fabric 런타임이 완전히 종료되고 앱이 종료 요청을 받도록 하십시오.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. 클러스터에서 노드를 제거합니다.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

이 세 단계가 노드에 적용된 후에는 확장 집합에서 제거할 수 있습니다. [bronze][durability] 외의 내구성 계층을 사용하는 경우 확장 집합 인스턴스가 제거될 때 이러한 단계가 완료됩니다.

다음 코드 블록은 마지막으로 만든 노드를 가져오고 클러스터에서 해당 노드를 사용하지 않도록 설정하고 중지 및 제거합니다.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

아래 **sfctl** 코드에서 `sfctl node list --query "sort_by(items[*], &name)[-1].name"` 명령을 사용하여 마지막으로 만든 노드의 **node-name** 값을 가져옵니다.

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> 다음 **sfctl** 쿼리를 사용하여 각 단계의 상태를 확인합니다.
>
> **비활성화 상태 확인**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **중지 상태 확인**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>확장 집합 규모 감축

이제 클러스터에서 Service Fabric 노드가 제거되었으며 가상 머신 확장 집합의 규모를 감축할 수 있습니다. 아래 예제에서는 확장 집합 용량이 1식 줄어듭니다.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

이 코드는 용량을 5로 설정합니다.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Service Fabric Explorer에서 볼 수 있는 동작
클러스터를 강화하는 경우 Service Fabric Explorer는 클러스터의 일부로 노드 수(가상 머신 확장 집합 인스턴스)를 반영합니다.  그러나 클러스터 규모를 축소할 때 적절한 노드 이름과 함께 [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) 를 호출하지 않으면 제거된 노드/VM 인스턴스가 계속 비정상 상태로 표시됩니다.   

다음은 이 동작에 대한 설명입니다.

Service Fabric Explorer에 나열된 노드는 서비스 패브릭 시스템 서비스(특히 FM)가 클러스터에 포함된 노드 수에 대해 알고 있는 내용이 반영된 것입니다. 가상 머신 확장 집합의 규모를 축소하면 VM이 삭제되지만 FM 시스템 서비스는 해당 노드(삭제된 VM에 매핑된 노드)가 돌아올 것으로 생각합니다. 따라서 Service Fabric Explorer는 성능 상태가 오류 또는 알 수 없음이어도 해당 노드를 계속 표시합니다.

VM이 제거될 때 노드가 제거되는지 확인하기 위한 두 가지 옵션이 있습니다.

1. 클러스터에서 노드 형식에 대해 골드 또는 실버 내구성 수준을 선택하면 인프라 통합이 제공됩니다. 그러면 규모를 축소할 때 시스템 서비스(FM)에서 해당 노드를 자동으로 제거합니다.
[여기에서 내구성 수준에 대한 세부 정보](service-fabric-cluster-capacity.md)

2. VM 인스턴스가 규모 축소되면 [Remove-ServiceFabricNodeState cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate)을 호출해야 합니다.

> [!NOTE]
> 가용성을 유지하고 상태를 보존하기 위해 서비스 패브릭 클러스터에서 특정 수의 노드가 항상 작동 상태를 유지해야 하며, 이 숫자를 "유지 관리 쿼럼"이라고 합니다. 따라서 [상태 전체 백업](service-fabric-reliable-services-backup-restore.md)을 처음으로 수행하는 경우 외에는 일반적으로 클러스터의 모든 컴퓨터를 종료하는 것은 안전하지 않습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
또한 클러스터 용량 계획, 클러스터 업그레이드 및 서비스 분할에 대해 자세히 알아 보려면 다음을 읽습니다.

* [클러스터 용량 계획](service-fabric-cluster-capacity.md)
* [클러스터 업그레이드](service-fabric-cluster-upgrade.md)
* [최대 크기를 위해 상태 저장 서비스 분할](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
