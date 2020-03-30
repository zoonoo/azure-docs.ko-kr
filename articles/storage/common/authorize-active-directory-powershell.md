---
title: Azure AD 자격 증명을 사용하여 PowerShell 명령을 실행하여 Blob 또는 큐 데이터에 액세스합니다.
titleSuffix: Azure Storage
description: PowerShell은 Azure AD 자격 증명으로 로그인하여 Azure Storage Blob 및 큐 데이터에 대한 명령을 실행할 수 있도록 지원합니다. 세션에 액세스 토큰이 제공되고 호출 작업에 권한을 부여하는 데 사용됩니다. 사용 권한은 Azure AD 보안 주체에 할당된 RBAC 역할에 따라 다릅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553450"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Azure AD 자격 증명을 사용하여 PowerShell 명령을 실행하여 Blob 또는 큐 데이터에 액세스합니다.

Azure Storage는 Azure Active Directory(Azure AD) 자격 증명을 사용하여 스크립팅 명령을 로그인하고 실행할 수 있는 PowerShell에 대한 확장을 제공합니다. Azure AD 자격 증명을 사용하여 PowerShell에 로그인하면 OAuth 2.0 액세스 토큰이 반환됩니다. 이 토큰은 PowerShell에서 Blob 또는 Queue 저장소에 대한 후속 데이터 작업에 권한을 부여하기 위해 자동으로 사용됩니다. 지원되는 작업의 경우, 더 이상 명령과 함께 계정 키 또는 SAS 토큰을 전달할 필요가 없습니다.

역할 기반 액세스 제어(RBAC)를 통해 Azure AD 보안 주체에 Blob 및 큐 데이터에 대한 권한을 할당할 수 있습니다. Azure 저장소의 RBAC 역할에 대한 자세한 내용은 [RBAC를 사용하여 Azure 저장소 데이터에 대한 액세스 권한 관리를](storage-auth-aad-rbac.md)참조하십시오.

## <a name="supported-operations"></a>지원되는 작업

Azure 저장소 확장은 Blob 및 큐 데이터에 대한 작업에 대해 지원됩니다. 호출할 수 있는 작업은 PowerShell에 로그인하는 Azure AD 보안 주체에 부여된 권한에 따라 다릅니다. Azure 저장소 컨테이너 또는 큐에 대한 권한은 RBAC를 통해 할당됩니다. 예를 들어 **Blob Data Reader** 역할이 할당된 경우 컨테이너 또는 큐에서 데이터를 읽는 스크립팅 명령을 실행할 수 있습니다. **Blob 데이터 기여자** 역할이 할당된 경우 컨테이너 또는 큐 또는 해당 역할에 포함된 데이터를 읽거나 쓰거나 삭제하는 스크립팅 명령을 실행할 수 있습니다.

컨테이너 또는 큐의 각 Azure Storage 작업에 필요한 권한에 대한 자세한 내용은 [OAuth 토큰을 사용 하 여 저장소 호출 작업을](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)참조 합니다.  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Azure AD 자격 증명을 사용하여 PowerShell 명령 호출

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell을 사용하여 Azure AD 자격 증명을 사용하여 Azure 저장소에 대한 후속 작업을 로그인하고 실행하려면 저장소 계정을 참조하는 저장소 컨텍스트를 만들고 매개 변수를 포함합니다. `-UseConnectedAccount`

다음 예제에서는 Azure AD 자격 증명을 사용하여 Azure PowerShell의 새 저장소 계정에 컨테이너를 만드는 방법을 보여 줍니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 계정에 로그인합니다.

    ```powershell
    Connect-AzAccount
    ```

    PowerShell을 통해 Azure에 로그인하는 자세한 내용은 [Azure PowerShell을 통해 로그인을](/powershell/azure/authenticate-azureps)참조하세요.

1. [New-AzResourceGroup을](/powershell/module/az.resources/new-azresourcegroup)호출하여 Azure 리소스 그룹을 만듭니다. 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. [New-AzStorageAccount를](/powershell/module/az.storage/new-azstorageaccount)호출하여 저장소 계정을 만듭니다.

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)를 호출하여 새 저장소 계정을 지정하는 저장소 계정 컨텍스트를 가져옵니다. 저장소 계정에서 작동할 때 자격 증명을 반복적으로 전달하는 대신 컨텍스트를 참조할 수 있습니다. Azure `-UseConnectedAccount` AD 자격 증명을 사용하여 후속 데이터 작업을 호출하는 매개 변수를 포함합니다.

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 컨테이너를 만들기 전에 저장소 [Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 자신에게 할당합니다. 계정 소유자인 경우에도 저장소 계정에 대해 데이터 작업을 수행하려면 명시적인 권한이 필요합니다. RBAC 역할 할당에 대한 자세한 내용은 [Azure 포털에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 권한 부여 액세스를](storage-auth-aad-rbac.md)참조하세요.

    > [!IMPORTANT]
    > RBAC 역할 할당이 전파되는 데 몇 분 정도 걸릴 수 있습니다.

1. [New-AzStorageContainer를](/powershell/module/az.storage/new-azstoragecontainer)호출하여 컨테이너를 만듭니다. 이 호출은 이전 단계에서 만든 컨텍스트를 사용하므로 Azure AD 자격 증명을 사용하여 컨테이너가 만들어집니다.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용하여 Blob 및 큐 데이터에 대한 액세스를 위해 RBAC 역할을 할당합니다.](storage-auth-aad-rbac-powershell.md)
- [Azure 리소스에 대한 관리 ID로 Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-msi.md)
