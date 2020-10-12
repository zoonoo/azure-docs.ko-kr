---
title: Azure 리소스 이동의 PowerShell을 사용 하 여 지역 간 리소스 이동
description: Azure 리소스 이동의 PowerShell을 사용 하 여 지역 간에 리소스를 이동 하는 방법을 알아봅니다.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 3236e0a95c6a4b4f57ac38ed067011c3d6848b5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89670542"
---
# <a name="move-resources-across-regions-in-powershell"></a>PowerShell에서 지역 간 리소스 이동

Azure 리소스 이동의 PowerShell을 사용 하 여 Azure 리소스를 다른 지역으로 이동 하는 방법을 알아봅니다. 

> [!NOTE]
> Azure Resource Mover는 현재 미리 보기로 제공됩니다.



## <a name="before-you-start"></a>시작하기 전에

- Azure 구독에는 리소스 이동 기에 대 한 액세스 권한이 있어야 하며 구독에 대 한 [소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) 또는 [사용자 액세스 관리자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) 권한이 있어야 합니다.
- 리소스 이동 기는 변경 및 업그레이드를 추적 하지 않으므로 리소스를 이동 하기 전에 필요한 사항을 변경 해야 합니다.
- PowerShell을 사용 하 여 리소스를 이동 하는 경우 현재 대상 지역 설정을 편집할 수 없습니다. 포털에서 이러한 설정을 직접 수정 합니다.
- 이동 컬렉션에 리소스를 추가 하는 경우 다른 지역으로 이동 하기 위해 이동에 대 한 메타 데이터는 목적을 위해 만들어진 리소스 그룹에 저장 됩니다. 현재이 리소스 그룹은 미국 동부 2 또는 북아메리카 유럽 지역에 있을 수 있습니다. 이러한 지역 중 하나에 있는 메타 데이터를 사용 하 여 모든 공용 지역 간에 Azure 리소스를 이동할 수 있습니다.

## <a name="sample-values"></a>샘플 값

스크립트 예제에서 다음 값을 사용 하 고 있습니다.

**설정** | **값** 
--- | ---
구독 ID | subscription-id
리소스 이동 기 서비스 위치 | 미국 동부 2
메타 데이터 리소스 그룹 | RegionMoveRG-centralus-westcentralus
메타 데이터 리소스 그룹 위치 | 미국 동부 2
컬렉션 이름 이동 | MoveCollection-centralus-westcentralus
원본 영역 |  centralus
원본 리소스 그룹 | PSDemoRM
대상 지역 | westcentralus
대상 리소스 그룹 | PSDemoRMtgt
이동할 VM | PSDemoVM

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 구독에 로그인합니다.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>메타 데이터에 대 한 리소스 그룹 만들기

컬렉션 이동 메타 데이터와 구성 정보를 저장할 리소스 그룹을 만듭니다.

```azurepowershell-interactive
# Sign in to an Azure subscription
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**예상 출력**:

![리소스 그룹을 만든 후 출력 텍스트](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>리소스 공급자 등록

MoveCollection 리소스를 만들 수 있도록 리소스 공급자를 등록 합니다.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>이동 컬렉션 만들기

MoveCollection 개체는 이동 하려는 리소스에 대 한 메타 데이터 및 구성 정보를 저장 합니다.

- MoveCollection 개체가 Azure 리소스 이동 서비스가 있는 구독에 액세스 하려면 구독에서 신뢰 하는 [시스템 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (이전에는 MSI (관리 서비스 식별)로 알려짐)가 필요 합니다.
- Id에는 참가자 또는 원본 구독에 대 한 사용자 액세스 관리자 역할이 할당 됩니다.
- Id에 역할을 할당 하려면 구독 (예: 소유자 또는 사용자 액세스 관리자)에 다음과 같은 권한이 있는 역할이 필요 합니다.
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft.Authorization/roleAssignments/delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus " -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**예상 출력**:

![이동 컬렉션을 만든 후 출력 텍스트](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>관리 id 할당 

구독 ID를 설정 하 고, MoveCollection 개체의 id 주체를 검색 하 고,이에 대 한 Azure 역할을 할당 합니다.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -Name "MoveCollection-centralus-westcentralus "

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"

```

## <a name="add-resources-to-the-move-collection"></a>이동 컬렉션에 리소스 추가

이동 하려는 원본 리소스의 ID를 검색 합니다. 그런 다음 이동 컬렉션에 추가 합니다.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**예상 출력**

![리소스 ID를 검색 한 후 출력 텍스트](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “ Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**예상 출력** 
 ![ 리소스를 추가한 후 출력 텍스트](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>종속성 오류 해결

추가한 리소스의 유효성을 검사 하 고 다른 리소스에 대 한 종속성을 확인 합니다.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**종속성이 있는 경우 출력**

이동 컬렉션의 리소스에 다른 리소스에 대 한 종속성이 있는 경우 실패 한 메시지가 발급 됩니다.

![종속성 검사 후 출력 텍스트](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>종속성 검색

이동 하려는 리소스가 다른 리소스에 대 한 종속성을 가지는 경우 누락 된 종속성에 대 한 정보를 검색할 수 있습니다.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**예상 출력**

![종속성 검색 후 출력 텍스트](./media/move-region-powershell/missing-dependencies.png)

이 예제에서는 두 개의 종속성이 누락 된 것으로 식별 됩니다.

- 원본 리소스 그룹: PSDemoRM
- VM의 NIC: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>컬렉션에 종속성 추가


검색 한 리소스 Id를 사용 하 여 누락 된 리소스의 이름을 식별 하 고 이동 컬렉션에 추가 합니다.

### <a name="add-the-nic"></a>NIC 추가

NIC 이름 및 형식을 검색 하 고 컬렉션에 추가 합니다.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/ <subscription-id> /resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”

```

**예상 출력**

![NIC를 검색 한 후 출력 텍스트](./media/move-region-powershell/output-retrieve-nic.png)

이제 이동 컬렉션에 NIC를 추가 합니다. 이 예에서는 대상 NIC와 동일한 이름을 제공 합니다. 설정 및 대/소문자를 일관 되 게 유지 합니다.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**예상 출력**

![컬렉션에 NIC 추가 후 출력 텍스트](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>원본 리소스 그룹 추가

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**예상 출력**

![컬렉션에 리소스 그룹을 추가한 후 출력 텍스트](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>종속성 다시 확인

누락 된 종속성을 다시 확인 합니다.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**출력**

누락 된 추가 종속성이 있습니다.

![종속성을 확인 한 후 텍스트 출력](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>추가 종속성 검색

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**예상 출력**


![추가 종속성을 검색 한 후 출력 텍스트](./media/move-region-powershell/additional-missing-dependencies.png)

이 예제에서는 세 가지 추가 종속성이 누락 된 것으로 식별 됩니다.

- 공용 IP 주소: psdemovm-IP
- Azure 가상 네트워크: psdemorm
- NSG (네트워크 보안 그룹): psdemovm-nsg

## <a name="add-additional-dependencies"></a>추가 종속성 추가

1. [지침에 따라](#add-dependencies-to-collection) 이동 컬렉션에 이러한 리소스를 추가 합니다.
2. 리소스를 추가한 후 모든 종속성이 추가 될 때까지 유효성을 다시 검사 합니다.


## <a name="prepare-and-move-the-source-resource-group"></a>원본 리소스 그룹 준비 및 이동

소스 영역에서 리소스를 이동 하기 전에 준비 합니다. 준비 프로세스는 이동 하는 리소스에 따라 달라 집니다. 예를 들어 상태 비저장 리소스에 대해 준비는 ARM (Azure Resource Manager) 템플릿을 준비 하 고 내보낼 수 있습니다. 또는 상태 저장 리소스의 경우 복제가 시작 될 수 있습니다. 

원본 리소스를 준비 하려면 먼저 원본 리소스 그룹을 준비 하 고 이동 해야 합니다.

### <a name="prepare"></a>준비

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”
```

**예상 출력**

![원본 리소스 그룹을 준비한 후 출력 텍스트](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>이동 시작

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”

**Expected output**

![Output text after initiating move of the source resource group](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**예상 출력**

![원본 리소스 그룹을 커밋한 후 출력 텍스트](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>리소스 준비

원본 리소스 그룹을 대상 영역으로 이동한 후 이동 컬렉션에서 리소스 목록을 검색 하 고 이동 합니다.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “ <subscription-id> “ -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**예상 출력**

![컬렉션에서 리소스를 검색 한 후의 출력 텍스트](./media/move-region-powershell/collection-resources.png)

이제 리소스를 준비 합니다.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
또는 이름 대신 리소스 ID를 사용 하 여 리소스를 준비 하 고 이동할 수 있습니다.

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**예상 출력**

![컬렉션에서 리소스를 준비한 후 출력 텍스트](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>리소스 이동 시작

리소스를 준비한 후에는 이동을 시작 합니다.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**예상 출력** 
 ![ 리소스 이동을 시작한 후 출력 텍스트](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>이동 취소 또는 커밋

처음 이동한 후에는 이동을 커밋할지 아니면 취소할지 결정할 수 있습니다. 

- **취소**: 테스트하는 중에 원본 리소스를 실제로 이동하지 않으려는 경우 이동을 취소할 수 있습니다. 이동을 취소하면 리소스가 *이동 시작 보류 중* 상태로 돌아갑니다. 그런 다음 필요한 경우 이동을 다시 시작할 수 있습니다.
- **커밋**: 커밋은 대상 지역으로의 이동을 완료합니다. 커밋 후 원본 리소스는 *원본 삭제 보류 중* 상태가 되며 삭제 여부를 결정할 수 있습니다.

### <a name="discard"></a>취소

이동을 취소 하려면:

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**예상 출력** 
 ![ 이동을 삭제 한 후 출력 텍스트](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Commit

이동을 커밋하려면:

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**예상 출력**

![이동을 커밋한 후 출력 텍스트](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>원본 리소스 삭제

이동을 커밋하고 리소스가 대상 지역에서 예상 대로 작동 하는지 확인 한 후에는 PowerShell 또는 [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources)를 [사용 하 여](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources) [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources)에서 원본 리소스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이동 컬렉션에서 리소스를 제거 하 [는 방법에 대해 알아봅니다](remove-move-resources.md) .