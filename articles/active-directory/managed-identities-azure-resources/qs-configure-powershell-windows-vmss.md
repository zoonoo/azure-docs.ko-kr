---
title: PowerShell을 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID를 구성하는 방법
description: PowerShell을 사용하여 가상 머신 확장 집합에서 시스템 및 사용자 할당 관리 ID를 구성하기 위한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dee7759dccf3093e9ba9f66bffcceaf603a11d4
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226881"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>PowerShell을 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 PowerShell을 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 작업을 수행하는 방법을 알아봅니다.
- 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하거나 사용하지 않도록 설정
- 가상 머신 확장 집합에서 사용자 할당 관리 ID 추가 및 제거

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 관리 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 문서의 관리 작업을 수행하려면 계정에 다음과 같은 Azure 역할 기반 액세스 제어가 할당되어야 합니다.

    > [!NOTE]
    > 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

    - [Virtual Machine 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor): 가상 머신 확장 집합을 만들고, 가상 머신 확장 집합에서 시스템 할당 관리 ID 및/또는 사용자 할당 관리 ID를 사용하도록 설정하고 제거합니다.
    - [관리 ID 참가자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할: 사용자 할당 관리 ID를 만듭니다.
    - [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할: 가상 머신 확장 집합에 사용자 할당 관리 ID를 할당하거나 이 집합에서 사용자 할당 관리 ID를 제거합니다.
- 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](/powershell/azure/install-az-ps)을 설치합니다. 

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure PowerShell을 사용하여 시스템 할당 관리 ID를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합을 만드는 동안 시스템 할당 관리 ID를 사용하도록 설정

시스템 할당 관리 ID를 사용하도록 설정된 가상 머신 확장 집합을 만들려면:

1. 참조 *예제 1* 에 [새로 만들기-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) 시스템 할당 된 관리 되는 id를 사용 하 여 가상 머신 확장 집합을 만드는 cmdlet 참조 문서 설정 합니다.  `-IdentityType SystemAssigned` 매개 변수를 `New-AzVmssConfig` cmdlet에 추가:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```
> [!NOTE]
> 필요에 따라 관리 되는 id를 프로 비전 할 Azure 리소스가 가상 머신 확장 집합 확장을 하지만는 곧 사용 되지 않을 수 있습니다. Azure 인스턴스 메타 데이터 id 끝점을 사용 하 여 인증 하는 것이 좋습니다. 자세한 내용은 [VM 확장 사용을 중지 하 고 인증에 대 한 Azure IMDS 엔드포인트를 사용 하 여 시작](howto-migrate-vm-extension.md)합니다.


## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>기존 Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하도록 설정

기존 Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하도록 설정해야 하는 경우:

1. `Connect-AzAccount`를 사용하여 Azure에 로그인합니다. 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 가상 머신 확장 집합에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 기여자”:

   ```powershell
   Connect-AzAccount
   ```

2. 우선 [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) cmdlet을 사용하여 가상 머신 확장 집합 속성을 검색합니다. 그런 다음, 시스템 할당 관리 ID를 사용하도록 설정하려면 [Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet에서 `-IdentityType` 스위치를 사용합니다.

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

> [!NOTE]
> 필요에 따라 관리 되는 id를 프로 비전 할 Azure 리소스가 가상 머신 확장 집합 확장을 하지만는 곧 사용 되지 않을 수 있습니다. Azure 인스턴스 메타 데이터 id 끝점을 사용 하 여 인증 하는 것이 좋습니다. 자세한 내용은 [VM 확장에서 인증에 대 한 Azure IMDS 엔드포인트로 마이그레이션](howto-migrate-vm-extension.md)합니다.

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하지 않도록 설정

시스템 할당 관리 ID는 더 이상 필요하지 않고 사용자 할당 관리 ID는 여전히 필요한 가상 머신 확장 집합이 있는 경우 다음 cmdlet을 사용합니다.

1. `Connect-AzAccount`를 사용하여 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 가상 머신 확장 집합에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 기여자”:

2. 다음 cmdlet을 실행합니다.

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

시스템 할당 관리 ID는 더 이상 필요하지 않고 사용자 할당 관리 ID가 없는 가상 머신 확장 집합이 있는 경우 다음 명령을 사용합니다.

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

이 섹션에서는 Azure PowerShell을 사용하여 가상 머신 확장 집합에서 사용자 할당 관리 ID를 추가하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합을 만드는 동안 사용자 할당 관리 ID를 할당

PowerShell을 통해 사용자 할당 관리 ID가 있는 새 가상 머신 확장 집합 만들기는 현재 지원되지 않습니다. 기존 가상 머신 확장 집합에 사용자 할당 관리 ID를 추가하는 방법은 다음 섹션을 참조하세요. 업데이트를 다시 확인하세요.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>기존 Azure 가상 머신 확장 집합에 사용자 할당 관리 ID 할당

기존 Azure 가상 머신 확장 집합에 사용자 할당 관리 ID를 할당하려면

1. `Connect-AzAccount`를 사용하여 Azure에 로그인합니다. 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 가상 머신 확장 집합에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “Virtual Machine 기여자”:

   ```powershell
   Connect-AzAccount
   ```

2. 우선 `Get-AzVM` cmdlet을 사용하여 가상 머신 확장 집합 속성을 검색합니다. 그런 다음, 가상 머신 확장 집합에 사용자 할당 관리 ID를 할당하려면 [Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet에서 `-IdentityType` 및 `-IdentityID` 스위치를 사용합니다. `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2`를 사용자 고유의 값으로 바꿉니다.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 사용자가 할당한 관리 ID 제거

가상 머신 확장 집합에 여러 사용자 할당 관리 ID가 있는 경우 다음 명령을 사용하여 마지막 항목을 제외한 모든 항목을 제거할 수 있습니다. `<RESOURCE GROUP>` 및 `<VIRTUAL MACHINE SCALE SET NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY NAME>`은 사용자 할당 관리 ID의 이름 속성으로, 가상 머신 확장 집합에 남아 있어야 합니다. 이 정보는 `az vmss show`를 사용하여 가상 머신 확장 집합의 ID 섹션에서 찾을 수 있습니다.

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
가상 머신 확장 집합에 시스템 할당 관리 ID가 없고 모든 사용자 할당 관리 ID를 제거하려는 경우 다음 명령을 사용합니다.

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
가상 머신 확장 집합에 시스템 할당 및 사용자 할당 관리 ID가 모두 있는 경우 시스템 할당 관리 ID만 사용하도록 전환하여 모든 사용자 할당 관리 ID를 제거할 수 있습니다.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID 개요](overview.md)
- 전체 Azure VM 만들기 퀵 스타트는 다음 참조:
  
  - [PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](../../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-powershell.md) 

















