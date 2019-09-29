---
title: Azure AD 자격 증명을 사용 하 여 Azure CLI 또는 PowerShell 명령을 실행 하 여 blob 또는 큐 데이터에 액세스 | Microsoft Docs
description: Azure CLI 및 PowerShell은 Azure Storage blob 및 큐 데이터에서 명령을 실행 하기 위해 Azure AD 자격 증명으로 로그인을 지원 합니다. 세션에 액세스 토큰이 제공되고 호출 작업에 권한을 부여하는 데 사용됩니다. 사용 권한은 Azure AD 보안 주체에 할당 된 RBAC 역할에 따라 달라 집니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e1c7f4531dee8673cc5b6dfe675e4c793144931e
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671096"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Azure AD 자격 증명을 사용 하 여 Azure CLI 또는 PowerShell 명령을 실행 하 여 blob 또는 큐 데이터에 액세스

Azure Storage Azure Active Directory (Azure AD) 자격 증명을 사용 하 여 로그인 하 고 스크립팅 명령을 실행할 수 있는 Azure CLI 및 PowerShell에 대 한 확장을 제공 합니다. Azure AD 자격 증명을 사용 하 여 Azure CLI 또는 PowerShell에 로그인 하면 OAuth 2.0 액세스 토큰이 반환 됩니다. 이 토큰은 Blob 또는 큐 저장소에 대 한 후속 데이터 작업에 권한을 부여 하기 위해 CLI 또는 PowerShell에서 자동으로 사용 됩니다. 지원되는 작업의 경우, 더 이상 명령과 함께 계정 키 또는 SAS 토큰을 전달할 필요가 없습니다.

RBAC (역할 기반 액세스 제어)를 통해 blob에 사용 권한을 할당 하 고 Azure AD 보안 주체에 데이터를 큐에 할당할 수 있습니다. Azure Storage의 RBAC 역할에 대 한 자세한 내용은 [rbac를 사용 하 여 데이터 Azure Storage에 대 한 액세스 권한 관리](storage-auth-aad-rbac.md)를 참조 하세요.

## <a name="supported-operations"></a>지원되는 작업

확장은 컨테이너 및 큐에 대 한 작업에 대해 지원 됩니다. 호출할 수 있는 작업은 Azure CLI 또는 PowerShell에 로그인 하는 데 사용 하는 Azure AD 보안 주체에 부여 된 권한에 따라 달라 집니다. Azure Storage 컨테이너 또는 큐에 대한 권한은 RBAC(역할 기반 액세스 제어)를 통해 할당됩니다. 예를 들어 **Blob 데이터 판독기** 역할이 할당 된 경우 컨테이너 또는 큐에서 데이터를 읽는 스크립팅 명령을 실행할 수 있습니다. **Blob 데이터 참가자** 역할이 할당 된 경우 컨테이너 또는 큐 또는 포함 된 데이터를 읽거나, 쓰고, 삭제 하는 스크립팅 명령을 실행할 수 있습니다. 

컨테이너 또는 큐에서 각 Azure Storage 작업에 필요한 사용 권한에 대 한 자세한 내용은 [OAuth 토큰을 사용 하 여 저장소 작업 호출](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)을 참조 하세요.  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Azure AD 자격 증명을 사용 하 여 CLI 명령 호출

Azure CLI는 blob `--auth-mode` 및 큐 데이터 작업에 대 한 매개 변수를 지원 합니다.

- Azure AD 보안 주체 `login` 를 사용 하 여 로그인 하려면 매개변수를로설정합니다.`--auth-mode`
- 계정에 대한 인증 매개 변수가 제공되지 않을 경우 계정 키를 쿼리하려면 `--auth-mode` 매개 변수를 레거시 `key` 값으로 설정합니다. 

다음 예제에서는 Azure AD 자격 증명을 사용 하 여 Azure CLI에서 새 저장소 계정으로 컨테이너를 만드는 방법을 보여 줍니다. 꺾쇠 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 

1. Azure CLI 버전 2.0.46 이상을 설치 했는지 확인 합니다. `az --version`을 실행하여 설치된 버전을 확인합니다.

1. 을 `az login` 실행 하 고 브라우저 창에서 인증 합니다. 

    ```azurecli
    az login
    ```

1. 원하는 구독을 지정 합니다. [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)를 사용하여 리소스 그룹을 만듭니다. [Az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)를 사용 하 여 해당 리소스 그룹 내에 저장소 계정을 만듭니다.

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. 컨테이너를 만들기 전에 [저장소 Blob 데이터 참가자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 자신에 게 할당 합니다. 계정 소유자 인 경우에도 저장소 계정에 대해 데이터 작업을 수행 하려면 명시적 권한이 필요 합니다. RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure Portal에서 Azure blob에 대 한 액세스 권한 부여 및 RBAC를 사용 하 여 큐 데이터](storage-auth-aad-rbac.md)를 참조 하세요.

    > [!IMPORTANT]
    > RBAC 역할 할당을 전파 하는 데 몇 분 정도 걸릴 수 있습니다.

1. 매개 변수가 로`login` 설정 된 [az storage container create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) 명령을 호출 하 여 Azure AD 자격 증명을 사용 하 여 컨테이너를 만듭니다. `--auth-mode`

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

`--auth-mode` 매개 변수와 연결된 환경 변수는 `AZURE_STORAGE_AUTH_MODE`입니다. 환경 변수에 적절 한 값을 지정 하 여 Azure Storage 데이터 작업의 모든 호출에 대 한 포함을 방지할 수 있습니다.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Azure AD 자격 증명을 사용 하 여 PowerShell 명령 호출

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell를 사용 하 여 로그인 하 고 Azure AD 자격 증명을 사용 하 여 Azure Storage에 대해 후속 작업을 실행 하려면 저장소 계정을 참조 하는 저장소 컨텍스트를 만들고 `-UseConnectedAccount` 매개 변수를 포함 합니다.

다음 예제에서는 Azure AD 자격 증명을 사용 하 여 Azure PowerShell에서 새 저장소 계정으로 컨테이너를 만드는 방법을 보여 줍니다. 꺾쇠 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

1. [AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용 하 여 Azure 계정에 로그인 합니다.

    ```powershell
    Connect-AzAccount
    ```

    PowerShell을 사용 하 여 Azure에 로그인 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 사용 하 여 로그인](/powershell/azure/authenticate-azureps)을 참조 하세요.

1. [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 호출 하 여 Azure 리소스 그룹을 만듭니다. 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. [AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)를 호출 하 여 저장소 계정을 만듭니다.

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. [AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)를 호출 하 여 새 저장소 계정을 지정 하는 저장소 계정 컨텍스트를 가져옵니다. 저장소 계정에서 동작 하는 경우 자격 증명을 반복 해 서 전달 하는 대신 컨텍스트를 참조할 수 있습니다. Azure AD `-UseConnectedAccount` 자격 증명을 사용 하 여 후속 데이터 작업을 호출 하는 매개 변수를 포함 합니다.

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 컨테이너를 만들기 전에 [저장소 Blob 데이터 참가자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 자신에 게 할당 합니다. 계정 소유자 인 경우에도 저장소 계정에 대해 데이터 작업을 수행 하려면 명시적 권한이 필요 합니다. RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure Portal에서 Azure blob에 대 한 액세스 권한 부여 및 RBAC를 사용 하 여 큐 데이터](storage-auth-aad-rbac.md)를 참조 하세요.

    > [!IMPORTANT]
    > RBAC 역할 할당을 전파 하는 데 몇 분 정도 걸릴 수 있습니다.

1. [AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)를 호출 하 여 컨테이너를 만듭니다. 이 호출에서 이전 단계에서 만든 컨텍스트를 사용 하므로 컨테이너는 Azure AD 자격 증명을 사용 하 여 만들어집니다. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>다음 단계

- Azure storage에 대 한 RBAC 역할에 대해 자세히 알아보려면 [rbac를 사용 하 여 저장소 데이터에 대 한 액세스 권한 관리](storage-auth-aad-rbac.md)를 참조 하세요.
- Azure Storage에서 Azure 리소스에 관리 id를 사용 하는 방법에 대 한 자세한 내용은 [Azure 리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 blob 및 큐에 대 한 액세스 인증](storage-auth-aad-msi.md)
- 스토리지 애플리케이션 내에서 컨테이너와 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [스토리지 애플리케이션에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
