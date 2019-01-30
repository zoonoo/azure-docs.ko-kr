---
title: PowerShell을 사용하여 Azure 리소스에 관리 ID 액세스 권한을 할당하는 방법
description: PowerShell을 사용하여 한 리소스에 관리 ID를 할당하고 다른 리소스에 액세스 권한을 할당하기 위한 단계별 지침을 제공합니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: daveba
ms.openlocfilehash: 72e05af92e88dc04f470d8be9a65347672777556
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427664"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>PowerShell을 사용하여 리소스에 관리 ID 액세스 권한 할당

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 ID를 사용하여 Azure 리소스를 구성하면 모든 보안 주체와 마찬가지로 다른 리소스에 관리 ID 액세스 권한을 제공할 수 있습니다. 이 예제에서는 PowerShell을 사용하여 Azure 가상 머신의 관리 ID 액세스 권한을 Azure Storage 계정에 제공하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](https://www.powershellgallery.com/packages/AzureRM)을 설치합니다.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC를 사용하여 다른 리소스에 관리 ID 액세스 권한 할당

[Azure VM과 같은](qs-configure-powershell-windows-vm.md) Azure 리소스에서 관리 ID를 사용하도록 설정한 후 다음을 수행합니다.

1. `Connect-AzureRmAccount` cmdlet을 사용하여 Azure에 로그인합니다. 관리 ID를 구성한 Azure 구독과 연결된 계정을 사용하세요.

   ```powershell
   Connect-AzureRmAccount
   ```
2. 이 예제에서는 Azure VM에 저장소 계정 액세스 권한을 제공합니다. 먼저 [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm)을 사용하여 관리 ID를 사용하도록 설정할 때 만든 `myVM`이라는 VM의 서비스 주체를 가져옵니다. 그런 다음, [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)를 사용하여 VM **Reader** 액세스 권한을 `myStorageAcct`라는 스토리지 계정에 제공합니다.

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID 개요](overview.md)
- Azure 가상 머신에서 관리 ID를 사용하려면 [PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-powershell-windows-vm.md)을 참조하세요.
