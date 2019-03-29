---
title: PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID를 구성하는 방법
description: PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID를 구성하는 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0eacd90c3b748920e5f43bf669a36df7a3f17c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446998"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 다음과 같은 관리 ID 작업을 수행하는 방법을 알아봅니다.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](/powershell/azure/install-az-ps)을 설치합니다.

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure PowerShell을 사용하여 시스템 할당 관리 ID를 사용 및 사용하지 않도록 설정하는 방법을 알아봅니다.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM 생성 중에 시스템 할당 관리 ID를 사용하도록 설정

시스템 할당 관리 ID를 사용하도록 설정된 Azure VM을 만들려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. 다음 Azure VM 빠른 시작, ("Azure에 로그인", "리소스 그룹 만들기", "네트워킹 그룹 만들기", "VM 만들기") 필요한 섹션만 완료 중 하나를 참조 하십시오.
    
    "VM 만들기" 섹션으로 이동하려면 [New-AzVMConfig](/powershell/module/az.compute/new-azvm) cmdlet 구문을 조금 수정합니다. 시스템 할당 ID가 사용된 VM을 프로비전하려면 `-AssignIdentity:$SystemAssigned` 매개 변수를 추가해야 합니다. 예를 들면 다음과 같습니다.
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [PowerShell을 사용하여 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-powershell.md)
   - [PowerShell을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-powershell.md)

> [!NOTE]
> Azure 리소스 VM 확장에 대 한 관리 되는 id 필요에 따라 프로 비전 할 수 있지만 곧 사용 되지 것입니다. Azure 인스턴스 메타 데이터 id 끝점을 사용 하 여 인증 하는 것이 좋습니다. 자세한 내용은 [VM 확장에서 인증에 대 한 Azure IMDS 엔드포인트로 마이그레이션](howto-migrate-vm-extension.md)합니다.

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>기존 Azure VM에서 시스템 할당 관리 ID를 사용하도록 설정

원래 시스템 할당 관리 ID 없이 프로비전된 VM에서 해당 ID를 사용하도록 설정하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. `Connect-AzAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다.

   ```powershell
   Connect-AzAccount
   ```

2. 먼저 `Get-AzVM` cmdlet을 사용하여 VM 속성을 검색합니다. 그런 다음, 시스템 할당 관리 ID를 사용하도록 설정하려면 [Update-AzVM](/powershell/module/az.compute/update-azvm) cmdlet에서 `-AssignIdentity` 스위치를 사용합니다.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

> [!NOTE]
> Azure 리소스 VM 확장에 대 한 관리 되는 id 필요에 따라 프로 비전 할 수 있지만 곧 사용 되지 것입니다. Azure 인스턴스 메타 데이터 id 끝점을 사용 하 여 인증 하는 것이 좋습니다. 자세한 내용은 [VM 확장에서 인증에 대 한 Azure IMDS 엔드포인트로 마이그레이션](howto-migrate-vm-extension.md)합니다.

### <a name="add-vm-system-assigned-identity-to-a-group"></a>그룹에 VM 시스템이 할당한 ID 추가

VM에서 시스템이 할당한 ID를 사용하도록 설정한 후에는 그룹에 해당 ID를 추가할 수 있습니다.  다음 절차에서는 VM의 시스템이 할당한 ID를 그룹에 추가합니다.

1. `Connect-AzAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다.

   ```powershell
   Connect-AzAccount
   ```

2. VM 서비스 주체의 `ObjectID`(반환된 값의 `Id` 필드에 지정되어 있음)를 검색하여 적어 둡니다.

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. 그룹의 `ObjectID`(반환된 값의 `Id` 필드에 지정되어 있음)를 검색하여 적어 둡니다.

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. VM 서비스 주체를 그룹에 추가합니다.

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Azure VM에서 시스템 할당 관리 ID를 사용하지 않도록 설정

VM에서 시스템 할당 관리 ID를 사용하지 않도록 설정하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

시스템 할당 관리 ID는 더 이상 필요하지 않고 사용자 할당 관리 ID는 여전히 필요한 Virtual Machine이 있는 경우 다음 cmdlet을 사용합니다.

1. `Connect-AzAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다.

   ```powershell
   Connect-AzAccount
   ```

2. `Get-AzVM` cmdlet을 사용하여 VM 속성을 검색하고 `-IdentityType` 매개 변수를 `UserAssigned`로 설정합니다.

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

시스템 할당 관리 ID는 더 이상 필요하지 않고 사용자 할당 관리 ID가 없는 가상 머신이 있는 경우 다음 명령을 사용합니다.

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

> [!NOTE]
> (사용 되지 않음)에 VM 확장을 Azure 리소스에 대 한 관리 되는 id를 프로 비전 하는 경우 사용 하 여 제거 해야 합니다 [제거 AzVMExtension](/powershell/module/az.compute/remove-azvmextension)합니다. 자세한 내용은 [VM 확장에서 인증에 대 한 Azure IMDS로 마이그레이션](howto-migrate-vm-extension.md)합니다.

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

이 섹션에서는 Azure PowerShell을 사용하여 VM에서 사용자 할당 관리 ID를 추가하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>생성 중에 VM에 사용자 할당 관리 ID 할당

VM에 사용자 할당 ID를 할당하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 및 [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할 할당이 필요합니다. 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. 다음 Azure VM 빠른 시작, ("Azure에 로그인", "리소스 그룹 만들기", "네트워킹 그룹 만들기", "VM 만들기") 필요한 섹션만 완료 중 하나를 참조 하십시오. 
  
    “VM 만들기” 섹션으로 이동할 때 [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) cmdlet 구문을 조금 수정합니다. 사용자 할당 ID가 있는 VM을 프로비전하려면 `-IdentityType UserAssigned` 및 `-IdentityID ` 매개 변수를 추가합니다.  `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>`을 사용자 고유의 값을 바꿉니다.  예를 들면 다음과 같습니다.
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [PowerShell을 사용하여 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-powershell.md)
    - [PowerShell을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-powershell.md)

> [!NOTE]
> Azure 리소스 VM 확장에 대 한 관리 되는 id 필요에 따라 프로 비전 할 수 있지만 곧 사용 되지 것입니다. Azure 인스턴스 메타 데이터 id 끝점을 사용 하 여 인증 하는 것이 좋습니다. 자세한 내용은 [VM 확장에서 인증에 대 한 Azure IMDS 엔드포인트로 마이그레이션](howto-migrate-vm-extension.md)합니다.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>기존 Azure VM에 사용자 할당 관리 ID 할당

VM에 사용자 할당 ID를 할당하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 및 [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할 할당이 필요합니다. 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. `Connect-AzAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다.

   ```powershell
   Connect-AzAccount
   ```

2. [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) cmdlet을 사용하여 사용자 할당 관리 ID를 만듭니다.  다음 단계에서 필요하므로 출력에서 `Id`를 메모해 둡니다.

   > [!IMPORTANT]
   > 만드는 사용자 할당 관리 되는 id만 지원 영숫자, 밑줄 및 하이픈 (0-9 또는 a-z 또는 A-z, \_ 또는-) 문자입니다. 또한 이름은 3에서 제대로 작동 하려면 VM/VMSS에 대 한 할당에 대해 128 문자 길이를 제한 해야 합니다. 자세한 내용은 [FAQ 및 알려진 문제](known-issues.md)를 참조하세요.

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. `Get-AzVM` cmdlet을 사용하여 VM 속성을 검색합니다. 그런 다음, Azure VM에 사용자 할당 관리 ID를 할당하고 [Update-AzVM](/powershell/module/az.compute/update-azvm) cmdlet에서 `-IdentityType` 및 `-IdentityID` 스위치를 사용합니다.  `-IdentityId` 매개 변수의 값은 이전 단계에서 메모해 둔 `Id`입니다.  `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>`을 사용자 고유의 값으로 바꿉니다.

   > [!WARNING]
   > VM에 할당된 이전에 사용자가 할당된 관리 ID를 유지하려면 VM 개체의 `Identity` 속성(예: `$vm.Identity`)을 쿼리합니다.  사용자가 할당한 관리 ID가 반환되면 이 관리 ID와 VM에 할당하려는 새 사용자 할당 관리 ID를 함께 다음 명령에 포함합니다.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

> [!NOTE]
> Azure 리소스 VM 확장에 대 한 관리 되는 id 필요에 따라 프로 비전 할 수 있지만 곧 사용 되지 것입니다. Azure 인스턴스 메타 데이터 id 끝점을 사용 하 여 인증 하는 것이 좋습니다. 자세한 내용은 [VM 확장에서 인증에 대 한 Azure IMDS 엔드포인트로 마이그레이션](howto-migrate-vm-extension.md)합니다.

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Azure VM에서 사용자 할당 관리 ID 제거

VM의 사용자 할당 ID를 제거하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 역할 할당이 필요합니다.

VM에 여러 사용자 할당 관리 ID가 있는 경우 다음 명령을 사용하여 마지막 것을 제외하고 모두 제거할 수 있습니다. `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY NAME>`은 사용자 할당 관리 ID의 이름 속성으로, VM에 남아 있어야 합니다. 이 정보는 VM 개체의 `Identity` 속성을 쿼리하여 찾을 수 있습니다.  예 `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
VM에 시스템 할당 관리 ID가 없고 모든 사용자 할당 관리 ID를 제거하려는 경우 다음 명령을 사용합니다.

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
VM에 시스템 할당 및 사용자 할당 관리 ID가 모두 있는 경우 시스템 할당 관리 ID만 사용하도록 전환하여 모든 사용자 할당 관리 ID를 제거할 수 있습니다.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID 개요](overview.md)
- 전체 Azure VM 만들기 퀵 스타트는 다음 참조:
  
  - [PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](../../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-powershell.md) 
