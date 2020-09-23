---
title: PowerShell을 사용 하 여 리소스에 관리 id 액세스 할당-Azure AD
description: PowerShell을 사용 하 여 한 리소스에 관리 되는 id를 할당 하 고 다른 리소스에 액세스 하는 방법에 대 한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: b66567275bf2c7454a2d4bb87dcd4c14bb1fb9b4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969291"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>PowerShell을 사용하여 리소스에 관리 ID 액세스 권한 할당

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 ID를 사용하여 Azure 리소스를 구성하면 모든 보안 주체와 마찬가지로 다른 리소스에 관리 ID 액세스 권한을 제공할 수 있습니다. 이 예제에서는 PowerShell을 사용하여 Azure 가상 머신의 관리 ID 액세스 권한을 Azure Storage 계정에 제공하는 방법을 보여 줍니다.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 예제 스크립트를 실행 하기 위해 두 가지 옵션이 있습니다.
    - 코드 블록의 오른쪽 위 모퉁이에 있는 **사용해 보기** 단추를 사용 하 여 열 수 있는 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용 합니다.
    - 최신 버전의 [Azure PowerShell](/powershell/azure/install-az-ps)를 설치한 다음를 사용 하 여 Azure에 로그인 하 여 스크립트를 로컬로 실행 `Connect-AzAccount` 합니다. 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Azure RBAC를 사용 하 여 다른 리소스에 관리 되는 id 액세스 권한 부여

1. Azure [VM과 같은](qs-configure-powershell-windows-vm.md)azure 리소스에서 관리 되는 id를 사용 하도록 설정 합니다.

1. 이 예제에서는 Azure VM에 스토리지 계정 액세스 권한을 제공합니다. 먼저 [Get-AzVM](/powershell/module/az.compute/get-azvm)을 사용하여 관리 ID를 사용하도록 설정할 때 만든 `myVM`이라는 VM의 서비스 주체를 가져옵니다. 그런 다음, [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment)를 사용하여 VM **Reader** 액세스 권한을 `myStorageAcct`라는 스토리지 계정에 제공합니다.

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID 개요](overview.md)
- Azure 가상 머신에서 관리 ID를 사용하려면 [PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-powershell-windows-vm.md)을 참조하세요.
