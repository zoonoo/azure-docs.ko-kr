---
title: Azure Resource Mover의 이동 컬렉션에서 리소스 제거
description: Azure Resource Mover의 이동 컬렉션에서 리소스를 제거하는 방법에 대해 알아봅니다.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/22/2020
ms.author: raynew
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 71397e4635e447701863122e58576dfb6507bcaf
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110698377"
---
# <a name="manage-move-collections-and-resource-groups"></a>이동 컬렉션 및 리소스 그룹 관리

이 문서에서는 [Azure Resource Mover](overview.md)에 있는 이동 컬렉션에서 리소스를 제거하거나 이동 컬렉션/리소스 그룹을 제거하는 방법을 설명합니다. 이동 컬렉션은 Azure 지역 간에 Azure 리소스를 이동할 때 사용됩니다.

## <a name="remove-a-resource-portal"></a>리소스(포털) 제거

다음과 같이 Resource Mover 포털에서 이동 컬렉션의 리소스를 제거할 수 있습니다.

1. **전체 지역** 의 컬렉션에서 제거하려는 모든 리소스를 선택하고 **제거** 를 선택합니다. 

    ![제거하기 위해 선택하는 단추](./media/remove-move-resources/portal-select-resources.png)

2. **리소스 제거** 에서 **제거** 를 클릭합니다.

    ![이동 컬렉션에서 리소스를 제거하기 위해 선택하는 단추](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>이동 컬렉션/리소스 그룹 제거(포털)

포털에서 이동 컬렉션/리소스 그룹을 제거할 수 있습니다.

1. 위 절차의 지침에 따라 컬렉션에서 리소스를 제거합니다. 리소스 그룹을 제거하는 경우 리소스가 포함되어 있지 않은지 확인합니다.
2. 이동 컬렉션 또는 리소스 그룹을 삭제합니다.  

## <a name="remove-a-resource-powershell"></a>리소스 제거(PowerShell)

PowerShell cmdlet을 사용하면 MoveCollection에서 단일 리소스를 제거하거나 여러 리소스를 제거할 수 있습니다.

### <a name="remove-a-single-resource"></a>단일 리소스 제거

다음과 같이 리소스(이 예제에서는 가상 네트워크 *psdemorm-vnet*)를 제거합니다.

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -Name "psdemorm-vnet"
```
**cmdlet 실행 후 출력**

![이동 컬렉션에서 리소스를 제거한 후 텍스트 출력](./media/remove-move-resources/powershell-remove-single-resource.png)

### <a name="remove-multiple-resources"></a>여러 리소스 제거

다음과 같이 여러 리소스를 제거합니다.

1. 종속성의 유효성을 검사합니다.

    ````azurepowershell-interactive
    $resp = Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemorm-vnet') -ValidateOnly
    ```

    **Output after running cmdlet**

    ![Output text after removing multiple resources from a move collection](./media/remove-move-resources/remove-multiple-validate-dependencies.png)

2. Retrieve the dependent resources that need to be removed (along with our example virtual network psdemorm-vnet):

    ````azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```

    **Output after running cmdlet**

    ![Output text after removing multiple resources from a move collection](./media/remove-move-resources/remove-multiple-get-dependencies.png)


3. Remove all resources, along with the virtual network:

    
    ````azurepowershell-interactive
    Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```

    **Output after running cmdlet**

    ![Output text after removing all resources from a move collection](./media/remove-move-resources/remove-multiple-all.png)


## Remove a collection (PowerShell)

Remove an entire move collection from the subscription, as follows:

1. Follow the instructions above to remove resources in the collection using PowerShell.
2. Run:

    ```azurepowershell-interactive
    Remove-AzResourceMoverMoveCollection -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```

    **Output after running cmdlet**
    
    ![Output text after removing a move collection](./media/remove-move-resources/remove-collection.png)

## VM resource state after removing

What happens when you remove a VM resource from a move collection depends on the resource state, as summarized in the table.

###  Remove VM state
**Resource state** | **VM** | **Networking**
--- | --- | --- 
**Added to move collection** | Delete from move collection. | Delete from move collection. 
**Dependencies resolved/prepare pending** | Delete from move collection  | Delete from move collection. 
**Prepare in progress**<br/> (or any other state in progress) | Delete operation fails with error.  | Delete operation fails with error.
**Prepare failed** | Delete from the move collection.<br/>Delete anything created in the target region, including replica disks. <br/><br/> Infrastructure resources created during the move need to be deleted manually. | Delete from the move collection.  
**Initiate move pending** | Delete from move collection.<br/><br/> Delete anything created in the target region, including VM, replica disks etc.  <br/><br/> Infrastructure resources created during the move need to be deleted manually. | Delete from move collection.
**Initiate move failed** | Delete from move collection.<br/><br/> Delete anything created in the target region, including VM, replica disks etc.  <br/><br/> Infrastructure resources created during the move need to be deleted manually. | Delete from move collection.
**Commit pending** | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. 
**Commit failed** | We recommend that you discard the  so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there.
**Discard completed** | The resource goes back to the **Initiate move pending** state.<br/><br/> It's deleted from the move collection, along with anything created at target - VM, replica disks, vault etc.  <br/><br/> Infrastructure resources created during the move need to be deleted manually. <br/><br/> Infrastructure resources created during the move need to be deleted manually. |  The resource goes back to the **Initiate move pending** state.<br/><br/> It's deleted from the move collection.
**Discard failed** | We recommend that you discard the moves so that the target resources are deleted first.<br/><br/> After that, the resource goes back to the **Initiate move pending** state, and you can continue from there. | We recommend that you discard the moves so that the target resources are deleted first.<br/><br/> After that, the resource goes back to the **Initiate move pending** state, and you can continue from there.
**Delete source pending** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region.  | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region.
**Delete source failed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region. | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region.
**Move completed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target or source region. |  Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target or source region.

## SQL resource state after removing

What happens when you remove an Azure SQL resource from a move collection depends on the resource state, as summarized in the table.

**Resource state** | **SQL** 
--- | --- 
**Added to move collection** | Delete from move collection. 
**Dependencies resolved/prepare pending** | Delete from move collection 
**Prepare in progress**<br/> (or any other state in progress)  | Delete operation fails with error. 
**Prepare failed** | Delete from move collection<br/><br/>Delete anything created in the target region. 
**Initiate move pending** |  Delete from move collection<br/><br/>Delete anything created in the target region. The SQL database exists at this point and will be deleted. 
**Initiate move failed** | Delete from move collection<br/><br/>Delete anything created in the target region. The SQL database exists at this point and must be deleted. 
**Commit pending** | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there.
**Commit failed** | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. 
**Discard completed** |  The resource goes back to the **Initiate move pending** state.<br/><br/> It's deleted from the move collection, along with anything created at target, including SQL databases. 
**Discard failed** | We recommend that you discard the moves so that the target resources are deleted first.<br/><br/> After that, the resource goes back to the **Initiate move pending** state, and you can continue from there. 
**Delete source pending** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region. 
**Delete source failed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region. 
**Move completed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target or source region.

## Next steps

Try [moving a VM](tutorial-move-region-virtual-machines.md) to another region with Resource Mover.
