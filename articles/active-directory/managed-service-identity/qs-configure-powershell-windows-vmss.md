---
title: PowerShell을 사용하여 Azure VMSS에서 관리 서비스 ID를 구성하는 방법
description: PowerShell을 사용하여 Azure VMSS에서 시스템 및 사용자 할당 ID를 구성하기 위한 단계별 지침을 제공합니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 5d4539c05d05053ac2ea6cd1c5fadbd161b41173
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257745"
---
# <a name="configure-a-vmss-managed-service-identity-using-powershell"></a>PowerShell을 사용하여 VMSS 관리 서비스 ID 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 PowerShell을 사용하여 Azure 가상 머신 확장 집합에서 관리 서비스 ID 작업을 수행하는 방법을 알아봅니다.
- 가상 머신 확장 집합에서 시스템 할당 ID를 사용하거나 사용하지 않도록 설정
- 가상 머신 확장 집합에서 사용자 할당 ID 추가 및 제거

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 문서의 관리 작업을 수행하려면 계정에 다음과 같은 역할이 할당되어야 합니다.
    - [Virtual Machine 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor): 가상 머신 확장 집합을 만들고, 가상 머신 확장 집합에서 시스템 할당 관리 및/또는 사용자 ID를 사용하도록 설정하고 제거합니다.
    - [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할: 사용자가 할당한 ID를 만듭니다.
    - [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할: 가상 머신 확장 집합에 사용자가 할당한 ID를 할당하거나 이 집합에서 사용자 할당 ID를 제거합니다.
- 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](https://www.powershellgallery.com/packages/AzureRM)을 설치합니다. 

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure PowerShell을 사용하여 시스템 할당 ID를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합을 만드는 동안 시스템 할당 ID를 사용하도록 설정

시스템 할당 ID가 설정된 VMSS를 만들려면

1. 시스템 할당 ID가 있는 VMSS를 만들려면 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) cmdlet 참조 문서의 *예제 1*을 참조하세요.  `-IdentityType SystemAssigned` 매개 변수를 `New-AzureRmVmssConfig` cmdlet에 추가:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (선택 사항) [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet에서 `-Name` 및 `-Type` 매개 변수를 사용하여 관리 서비스 ID VMSS 확장을 추가합니다. 가상 머신 확장 집합의 형식에 따라 "ManagedIdentityExtensionForWindows" 또는 "ManagedIdentityExtensionForLinux"를 전달하고 `-Name` 매개 변수를 사용하여 해당 이름을 지정합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 끝점에서 사용하는 포트를 지정합니다.

    > [!NOTE]
    > 이 단계는 Azure IMDS(Instance Metadata Service) ID 엔드포인트를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>기존 Azure 가상 머신 확장 집합에서 시스템 할당 ID를 사용하도록 설정

기존 Azure 가상 머신 확장 집합에서 시스템 할당 ID를 사용하도록 설정해야 하는 경우:

1. `Login-AzureRmAccount`를 사용하여 Azure에 로그인합니다. 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 가상 머신 확장 집합에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 기여자”:

   ```powershell
   Login-AzureRmAccount
   ```

2. 우선 [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss) cmdlet을 사용하여 가상 머신 확장 집합 속성을 검색합니다. 그런 다음, 시스템 할당 ID를 사용하도록 설정하려면 [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) cmdlet에서 `-IdentityType` 스위치를 사용합니다.

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet에서 `-Name` 및 `-Type` 매개 변수를 사용하여 관리 서비스 ID VMSS 확장을 추가합니다. 가상 머신 확장 집합의 형식에 따라 "ManagedIdentityExtensionForWindows" 또는 "ManagedIdentityExtensionForLinux"를 전달하고 `-Name` 매개 변수를 사용하여 해당 이름을 지정합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 끝점에서 사용하는 포트를 지정합니다.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 시스템 할당 ID를 사용하지 않도록 설정

시스템 할당 ID는 더 이상 필요하지 않고 사용자 할당 ID는 여전히 필요한 가상 머신 확장 집합이 있는 경우 다음 cmdlet을 사용합니다.

1. `Login-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 가상 머신 확장 집합에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 기여자”:

2. 다음 cmdlet을 실행합니다.

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

가상 머신 확장 집합에 시스템 할당 ID가 더 이상 필요하지 않고 사용자 할당 ID가 없는 경우 다음 명령을 사용합니다.

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-identity"></a>사용자 할당 ID

이 섹션에서는 Azure PowerShell을 사용하여 가상 머신 확장 집합에서 사용자 할당 ID를 추가하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합을 만드는 동안 사용자 할당 ID를 할당

PowerShell을 통해 사용자 할당 ID가 있는 새 가상 머신 확장 집합 만들기는 현재 지원되지 않습니다. 기존 가상 머신 확장 집합에 사용자 할당 ID를 추가하는 방법은 다음 섹션을 참조하세요. 업데이트를 다시 확인하세요.

### <a name="assign-a-user-identity-to-an-existing-azure-virtual-machine-scale-set"></a>기존 Azure 가상 머신 확장 집합에 사용자 ID 할당

기존 Azure 가상 머신 확장 집합에 사용자 할당 ID를 할당하려면:

1. `Connect-AzureRmAccount`를 사용하여 Azure에 로그인합니다. 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 가상 머신 확장 집합에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 기여자”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. 우선 `Get-AzureRmVM` cmdlet을 사용하여 가상 머신 확장 집합 속성을 검색합니다. 그런 다음, 가상 머신 확장 집합에 사용자 할당 ID를 할당하려면 [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) cmdlet에서 `-IdentityType` 및 `-IdentityID` 스위치를 사용합니다. `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2`를 사용자 고유의 값으로 바꿉니다.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 사용자가 할당한 관리 ID 제거

가상 머신 확장 집합에 여러 사용자 할당 ID가 있는 경우 다음 명령을 사용하여 마지막 항목을 제외한 모든 항목을 제거할 수 있습니다. `<RESOURCE GROUP>` 및 `<VMSS NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<MSI NAME>`은 사용자 할당 ID의 이름 속성으로, 가상 머신 확장 집합에 남아 있어야 합니다. 이 정보는 `az vmss show`를 사용하여 가상 머신 확장 집합의 ID 섹션에서 찾을 수 있습니다.

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
가상 머신 확장 집합에 시스템 할당 ID가 없고 모든 사용자 할당 ID를 제거하려는 경우 다음 명령을 사용합니다.

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
가상 머신 확장 집합에 시스템 할당 및 사용자 할당 ID가 모두 있는 경우, 시스템 할당만 사용하도록 전환하면 모든 사용자 할당 ID를 제거할 수 있습니다.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>관련 콘텐츠

- [관리 서비스 ID 개요](overview.md)
- 전체 Azure VM 만들기 퀵 스타트는 다음 참조:
  
  - [PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](../../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-powershell.md) 

















