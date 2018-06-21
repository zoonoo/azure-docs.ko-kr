---
title: Azure AD ID에서 Azure CLI 또는 PowerShell 명령을 실행하여 Azure Storage에 액세스(미리 보기) | Microsoft Docs
description: Azure CLI 및 PowerShell은 Azure AD ID로 로그인하여 Azure Storage 컨테이너와 큐 및 해당 데이터에 대해 명령을 실행하도록 지원합니다. 세션에 액세스 토큰이 제공되고 호출 작업에 권한을 부여하는 데 사용됩니다. 권한은 Azure AD ID에 할당된 역할에 따라 다릅니다.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.openlocfilehash: 98af46707485d1ab49e7d8c6fb1729e6edc6b2ff
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235868"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Azure AD ID를 사용하여 CLI 또는 PowerShell을 통해 Azure Storage에 액세스(미리 보기)

Azure Storage는 로그인한 후 Azure AD(Azure Active Directory) ID에서 스크립트 명령을 실행하도록 하는 Azure CLI 및 PowerShell용 미리 보기 확장을 제공합니다. Azure AD ID는 사용자, 그룹 또는 응용 프로그램 서비스 주체일 수도 있고, [관리되는 서비스 ID](../../active-directory/managed-service-identity/overview.md)일 수도 있습니다. RBAC(역할 기반 액세스 제어)를 통해 저장소 리소스에 액세스하기 위한 권한을 Azure AD ID에 할당할 수 있습니다. Azure Storage의 RBAC 역할에 대한 자세한 내용은 [RBAC를 사용하여 Azure Storage 데이터에 대한 액세스 권한 관리(미리 보기)](storage-auth-aad-rbac.md)를 참조하세요.

Azure AD ID를 사용하여 Azure CLI 또는 PowerShell에 로그인하면 해당 ID에서 Azure Storage에 액세스하기 위한 액세스 토큰이 반환됩니다. 그런 후 CLI 또는 PowerShell에서는 자동으로 해당 토큰을 사용하여 Azure Storage에 대한 작업 권한을 부여합니다. 지원되는 작업의 경우, 더 이상 명령과 함께 계정 키 또는 SAS 토큰을 전달할 필요가 없습니다.

> [!IMPORTANT]
> 이 미리 보기는 프로덕션 이외 용도로 사용해야 합니다. Azure Storage에 대한 Azure AD 통합이 일반 공급 버전으로 선언되어야만 프로덕션 SLA(서비스 수준 계약)를 사용할 수 있습니다. 사용자 시나리오에서 Azure AD 통합이 아직 지원되지 않는 경우, 응용 프로그램에서 공유 키 인증 또는 SAS 토큰을 계속 사용합니다. 자세한 내용은 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증(미리 보기)](storage-auth-aad.md)을 참조하세요.
>
> 미리 보기 동안 RBAC 역할 할당을 전파하는 데 최대 5분이 소요될 수 있습니다.
>
> Azure Storage와 Azure AD를 통합하려면 Azure Storage 작업에 대해 HTTPS를 사용해야 합니다.

## <a name="supported-operations"></a>지원되는 작업

컨테이너 및 큐에 대한 작업에 미리 보기 확장이 지원됩니다. 사용자가 호출할 수 있는 작업은 Azure CLI 또는 PowerShell에 로그인하는 데 사용하는 Azure AD ID에 부여된 권한에 따라 달라집니다. Azure Storage 컨테이너 또는 큐에 대한 권한은 RBAC(역할 기반 액세스 제어)를 통해 할당됩니다. 예를 들어, 데이터 읽기 권한자 역할이 해당 ID에 할당되면 컨테이너 또는 큐에서 데이터를 읽는 스크립팅 명령을 실행할 수 있습니다. 데이터 참가자 역할이 ID에 할당되면 컨테이너 또는 큐나 포함된 데이터를 읽거나, 쓰거나, 삭제하는 스크립팅 명령을 실행할 수 있습니다. 

컨테이너 또는 큐를 대상으로 하는 각 Azure Storage 작업에 필요한 사용 권한에 대한 자세한 내용은 [REST 작업 호출을 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)을 참조하세요.  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Azure AD ID를 사용하여 CLI 명령 호출

Azure CLI에 대한 미리 보기 확장을 설치하려면

1. Azure CLI 버전 2.0.32 이상이 설치되어 있는지 확인합니다. `az --version`을 실행하여 설치된 버전을 확인합니다.
2. 다음 명령을 실행하여 미리 보기 확장을 설치합니다. 

    ```azurecli
    az extension add -n storage-preview
    ```

미리 보기 확장은 지원되는 명령에 새 `--auth-mode` 매개 변수를 추가합니다.

- Azure AD ID를 사용하여 로그인하려면 `--auth-mode` 매개 변수를 `login`으로 설정합니다.
- 계정에 대한 인증 매개 변수가 제공되지 않을 경우 계정 키를 쿼리하려면 `--auth-mode` 매개 변수를 레거시 `key` 값으로 설정합니다. 

예를 들어, Azure AD ID를 사용하여 Azure CLI에서 Blob을 다운로드하려면 먼저 `az login`을 실행한 후 `--auth-mode`를 `login`으로 설정하여 명령을 호출합니다.

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

`--auth-mode` 매개 변수와 연결된 환경 변수는 `AZURE_STORAGE_AUTH_MODE`입니다.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Azure AD ID를 사용하여 PowerShell 명령 호출

Azure PowerShell을 사용하여 Azure AD ID로 로그인하려면

1. 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다. 최신 버전을 설치하려면 다음 명령을 실행합니다.
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. 모든 이전 Azure PowerShell 설치를 제거합니다.
3. AzureRM을 설치합니다.

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. 미리 보기 모듈을 설치합니다.

    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber 
    ```

5. `-UseConnectedAccount` 매개 변수를 포함하고 [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) cmdlet을 호출하여 컨텍스트를 만듭니다. 
6. Azure AD ID를 사용하여 cmdlet을 호출하려면 컨텍스트를 cmdlet으로 전달합니다.

다음 예제에서는 Azure AD ID를 사용하여 Azure PowerShell에서 컨테이너의 Blob을 나열하는 방법을 보여 줍니다. 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>다음 단계

- Azure Storage의 RBAC 역할에 대한 자세한 내용은 [RBAC를 사용하여 저장소 데이터에 대한 액세스 권한 관리(미리 보기)](storage-auth-aad-rbac.md)를 참조하세요.
- Azure Storage에서 관리 서비스 ID를사용 하는 방법에 대한 자세한 내용은 [Azure 관리 서비스 ID에서 Azure AD로 인증(미리 보기)](storage-auth-aad-msi.md)을 참조하세요.
- 저장소 응용 프로그램 내에서 컨테이너와 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [저장소 응용 프로그램에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
- Azure Blob 및 큐의 Azure AD 통합에 대한 자세한 내용은 Azure Storage 팀 블로그 게시물 [Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)(Azure Storage에 대한 Azure AD 인증 미리 보기 발표)를 참조하세요.
