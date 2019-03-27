---
title: Blob 및 큐 데이터에 액세스 하려면 Azure AD id 아래에서 Azure CLI 또는 PowerShell 명령을 실행 | Microsoft Docs
description: Azure CLI 및 PowerShell 명령을 실행 하 여 Azure Storage blob 및 큐 데이터를 Azure AD id에 로그인을 지원 합니다. 세션에 액세스 토큰이 제공되고 호출 작업에 권한을 부여하는 데 사용됩니다. 권한은 Azure AD id에 할당 된 RBAC 역할에 따라 다릅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d1fdafaaecd448fd09fc40cf5f6173ce600ac4f9
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483208"
---
# <a name="use-an-azure-ad-identity-to-access-blob-and-queue-data-with-cli-or-powershell"></a>CLI 또는 PowerShell을 사용 하 여 blob 및 큐 데이터에 액세스 하려면 Azure AD id 사용

Azure Storage에 로그인 하 여 Azure Active Directory (Azure AD) id에서 스크립트 명령을 실행할 수 있도록 하는 Azure CLI 및 PowerShell에 대 한 확장을 제공 합니다. Azure AD ID는 사용자, 그룹 또는 애플리케이션 서비스 주체일 수도 있고, [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)일 수도 있습니다. 역할 기반 액세스 제어 (RBAC)를 통해 Azure AD id에 blob 및 큐 데이터에 액세스 하는 권한을 할당할 수 있습니다. Azure Storage에 대 한 RBAC 역할에 대 한 자세한 내용은 참조 하세요. [RBAC 사용 하 여 Azure Storage 데이터에 대 한 관리 액세스 권한을](storage-auth-aad-rbac.md)합니다.

Azure AD id를 사용 하 여 Azure CLI 또는 PowerShell에 로그인 할 때 해당 id에서 Azure Storage에 액세스 하기 위한 액세스 토큰이 반환 됩니다. 그런 후 CLI 또는 PowerShell에서는 자동으로 해당 토큰을 사용하여 Azure Storage에 대한 작업 권한을 부여합니다. 지원되는 작업의 경우, 더 이상 명령과 함께 계정 키 또는 SAS 토큰을 전달할 필요가 없습니다.

## <a name="supported-operations"></a>지원되는 작업

확장은 컨테이너 및 큐 작업에 지원 됩니다. 어떤 작업을 호출할 수 있는 로그인 Azure CLI 또는 PowerShell을 Azure AD id에 부여 된 권한에 따라 달라 집니다. Azure Storage 컨테이너 또는 큐에 대한 권한은 RBAC(역할 기반 액세스 제어)를 통해 할당됩니다. 예를 들어, 데이터 읽기 권한자 역할이 해당 ID에 할당되면 컨테이너 또는 큐에서 데이터를 읽는 스크립팅 명령을 실행할 수 있습니다. 데이터 참가자 역할이 ID에 할당되면 컨테이너 또는 큐나 포함된 데이터를 읽거나, 쓰거나, 삭제하는 스크립팅 명령을 실행할 수 있습니다. 

컨테이너 또는 큐를 대상으로 하는 각 Azure Storage 작업에 필요한 사용 권한에 대한 자세한 내용은 [REST 작업 호출을 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)을 참조하세요.  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Azure AD 자격 증명을 사용 하 여 CLI 명령을 호출 합니다.

Azure CLI를 지원 합니다 `--auth-mode` blob 및 큐 데이터 작업에 대 한 매개 변수:

- 설정 된 `--auth-mode` 매개 변수를 `login` Azure AD 보안 주체를 사용 하 여 로그인 합니다.
- 계정에 대한 인증 매개 변수가 제공되지 않을 경우 계정 키를 쿼리하려면 `--auth-mode` 매개 변수를 레거시 `key` 값으로 설정합니다. 

다음 예제에서는 Azure AD 자격 증명을 사용 하 여 Azure CLI에서 새 저장소 계정에서 컨테이너를 만드는 방법을 보여 줍니다. 꺾쇠 괄호에서 자리 표시자 값을 고유한 값으로 대체 해야 합니다. 

1. 이상 Azure CLI 버전 2.0.46를 설치 했는지 확인 합니다. `az --version`을 실행하여 설치된 버전을 확인합니다.

1. 실행 `az login` 브라우저 창에서 인증 및: 

    ```azurecli
    az login
    ```
    
1. 원하는 구독을 지정 합니다. [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)를 사용하여 리소스 그룹을 만듭니다. 사용 하 여 해당 리소스 그룹 내에서 저장소 계정을 만듭니다 [az storage 계정 만들기](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

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
    
1. 컨테이너를 만들기 전에 할당 된 [Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview) 자신에 게는 역할입니다. 계정 소유자 인 경우에 명시적 권한을 저장소 계정에 대 한 데이터 작업을 수행 해야 합니다. RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure blob 및 큐 데이터에 RBAC 사용 하 여 Azure portal에서 액세스 권한을 부여](storage-auth-aad-rbac.md)합니다.

    > [!IMPORTANT]
    > RBAC 역할 할당에 전파 하는 데는 몇 분 정도 걸릴 수 있습니다.
    
1. 호출을 [az 저장소 컨테이너 만들기](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) 명령과 `--auth-mode` 매개 변수 설정 `login` Azure AD 자격 증명을 사용 하 여 컨테이너를 만들려면:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

`--auth-mode` 매개 변수와 연결된 환경 변수는 `AZURE_STORAGE_AUTH_MODE`입니다. Azure Storage 데이터에 대 한 모든 호출에 포함 되지 않도록 하려면 환경 변수에서 적절 한 값을 지정할 수 있습니다.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Azure AD 자격 증명을 사용 하 여 PowerShell 명령을 호출 합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

로그인 하 고 Azure AD 자격 증명을 사용 하 여 Azure Storage에 대 한 후속 작업을 실행 하려면 Azure PowerShell을 사용 하려면 저장소 계정을 참조 하려면 저장소 컨텍스트를 포함 하는 `-UseConnectedAccount` 매개 변수입니다.

다음 예제에서는 Azure AD 자격 증명을 사용 하 여 Azure PowerShell에서 새 저장소 계정에서 컨테이너를 만드는 방법을 보여 줍니다. 꺾쇠 괄호에서 자리 표시자 값을 고유한 값으로 대체 해야 합니다.

1. 사용 하 여 Azure 구독에 로그인 합니다 `Connect-AzAccount` 명령는 화면의 지침에 Azure AD 자격 증명을 입력: 

    ```powershell
    Connect-AzAccount
    ```
    
1. 호출 하 여 Azure 리소스 그룹을 만듭니다 [새로 만들기-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)합니다. 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 호출 하 여 저장소 계정을 만듭니다 [새로 만들기-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)합니다.

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 호출 하 여 새 저장소 계정을 지정 하는 저장소 계정 컨텍스트를 가져옵니다 [새로 만들기-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)합니다. 저장소 계정에서 역할을 할 때 반복적으로 자격 증명을 전달 하는 대신 컨텍스트를 참조할 수 있습니다. 포함 된 `-UseConnectedAccount` 매개 변수를 Azure AD 자격 증명을 사용 하 여 모든 후속 데이터 작업을 호출 합니다.

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 컨테이너를 만들기 전에 할당 된 [Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview) 자신에 게는 역할입니다. 계정 소유자 인 경우에 명시적 권한을 저장소 계정에 대 한 데이터 작업을 수행 해야 합니다. RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure blob 및 큐 데이터에 RBAC 사용 하 여 Azure portal에서 액세스 권한을 부여](storage-auth-aad-rbac.md)합니다.

    > [!IMPORTANT]
    > RBAC 역할 할당에 전파 하는 데는 몇 분 정도 걸릴 수 있습니다.

1. 호출 하 여 컨테이너를 만듭니다 [새로 만들기-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)합니다. 이전 단계에서 만든 컨텍스트를 사용 하는이 호출을 하기 때문에 컨테이너를 Azure AD 자격 증명을 사용 하 여 생성 됩니다. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>다음 단계

- Azure storage에 대 한 RBAC 역할에 대 한 자세한 내용은 참조 하세요 [RBAC 사용 하 여 저장소 데이터에 대 한 관리 액세스 권한을](storage-auth-aad-rbac.md)합니다.
- 관리 되는 id를 사용 하 여 Azure Storage를 사용 하 여 Azure 리소스에 대 한 자세한 내용은 참조 하세요 [blob 및 Azure 사용 하 여 큐에 대 한 인증 액세스 identities를 관리 되는 Azure 리소스에 대 한](storage-auth-aad-msi.md)합니다.
- 저장소 애플리케이션 내에서 컨테이너와 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [저장소 애플리케이션에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
