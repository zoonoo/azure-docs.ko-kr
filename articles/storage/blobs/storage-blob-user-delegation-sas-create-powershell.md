---
title: PowerShell (미리 보기)을 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기-Azure Storage
description: PowerShell을 사용 하 여 Azure Storage에서 Azure Active Directory 자격 증명을 사용 하 여 SAS (공유 액세스 서명)를 만드는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/29/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 8a455fdb8ef81b0e06d1f77f7a9cdd5bec351b2b
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164278"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell-preview"></a>PowerShell (미리 보기)을 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 Azure Active Directory (Azure AD) 자격 증명을 사용 하 여 Azure PowerShell (미리 보기)가 있는 컨테이너 또는 blob에 대 한 사용자 위임 SAS를 만드는 방법을 보여 줍니다.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-module"></a>Preview 모듈 설치

PowerShell을 사용 하 여 사용자 위임 SAS를 만들려면 먼저 Az. Storage 1.3.1-preview 모듈을 설치 해야 합니다. 다음 단계에 따라 모듈을 설치합니다.

1. 모든 이전 Azure PowerShell 설치를 제거합니다.

    - **설정**에 있는 **앱 및 기능** 설정을 사용하여 Windows에서 이전에 설치한 Azure PowerShell을 제거합니다.
    - 에서 모든 **Azure** 모듈을 `%Program Files%\WindowsPowerShell\Modules`제거 합니다.

1. 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다. Windows PowerShell 창을 열고 다음 명령을 실행하여 최신 버전을 설치합니다.

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet을 설치한 후 PowerShell 창을 닫았다가 다시 엽니다.

1. Azure PowerShell의 최신 버전을 설치합니다.

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 사용자 위임 SAS를 지 원하는 Azure Storage 미리 보기 모듈을 설치 합니다.

    ```powershell
    Install-Module Az.Storage `
        –Repository PSGallery `
        -RequiredVersion 1.3.1-preview `
        –AllowPrerelease `
        –AllowClobber `
        –Force
    ```

1. PowerShell 창을 닫았다가 다시 엽니다.

PowerShell은 최신 Az. Storage 모듈을 기본적으로 로드 하므로 콘솔을 시작할 때 1.3.1-preview 모듈을 명시적으로 로드 해야 할 수도 있습니다. 미리 보기 모듈을 명시적으로 로드 하려면 [import-module](/powershell/module/microsoft.powershell.core/import-module) 명령을 실행 합니다.

```powershell
Import-Module Az.Storage -RequiredVersion 1.3.1
```

Azure PowerShell를 설치 하는 방법에 대 한 자세한 내용은 [PowerShellGet을 사용 하 여 Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조 하세요.

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Azure AD를 사용 하 여 Azure PowerShell에 로그인

[AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 호출 하 여 Azure AD 계정으로 로그인 합니다.

```powershell
Connect-AzAccount
```

PowerShell을 사용 하 여 로그인 하는 방법에 대 한 자세한 내용은 [Azure PowerShell를 사용 하 여 로그인](/powershell/azure/authenticate-azureps)을 참조 하세요.

## <a name="assign-permissions-with-rbac"></a>RBAC를 사용 하 여 권한 할당

Azure PowerShell에서 사용자 위임 SAS를 만들려면 PowerShell에 로그인 하는 데 사용 되는 Azure AD 계정에 **Microsoft Storage/storageAccounts/blobServices/generateUserDelegationKey** 작업을 포함 하는 역할을 할당 해야 합니다. 이 사용 권한을 통해 Azure AD 계정에서 *사용자 위임 키*를 요청할 수 있습니다. 사용자 위임 키는 사용자 위임 SAS에 서명 하는 데 사용 됩니다. 저장소 계정, 리소스 그룹 또는 구독 수준에서 **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** 작업을 제공 하는 역할을 할당 해야 합니다. 사용자 위임 SAS를 만드는 RBAC 권한에 대 한 자세한 내용은 [사용자 위임 Sas 만들기](/rest/api/storageservices/create-user-delegation-sas)의 **rbac를 사용 하 여 권한 할당** 섹션을 참조 하세요.

Azure AD 보안 주체에 RBAC 역할을 할당할 수 있는 권한이 없는 경우 계정 소유자 또는 관리자에 게 필요한 권한을 할당 하도록 요청 해야 할 수 있습니다.

다음 예에서는 **저장소 Blob 데이터 참가자** 역할을 할당 합니다. 여기에는 **Microsoft Storage/Storageaccounts/Blobservices/generateUserDelegationKey** action이 포함 됩니다. 역할의 범위는 저장소 계정 수준에서 지정 됩니다.

꺾쇠 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

**Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** action을 포함 하는 기본 제공 역할에 대 한 자세한 내용은 [Azure 리소스에 대 한 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조 하세요.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD 자격 증명을 사용 하 여 SAS 보안

Azure PowerShell를 사용 하 여 사용자 위임 SAS를 만들 때 SAS에 서명 하는 데 사용 되는 사용자 위임 키는 암시적으로 생성 됩니다. SAS에 대해 지정 하는 시작 시간 및 만료 시간은 사용자 위임 키에 대 한 시작 시간 및 만료 시간으로도 사용 됩니다. 

사용자 위임 키가 유효한 최대 간격은 시작 날짜 로부터 7 일 이므로 시작 시간의 7 일 이내에 SAS에 대 한 만료 시간을 지정 해야 합니다. 사용자 위임 키가 만료 된 후에는 SAS가 유효 하지 않으므로 만료 시간이 7 일을 초과 하는 SAS는 7 일 동안만 유효 합니다.

Azure PowerShell 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS를 만들려면 먼저 `-UseConnectedAccount` 매개 변수를 지정 하 여 새 Azure Storage 컨텍스트 개체를 만듭니다. `-UseConnectedAccount` 매개 변수는 명령에서 로그인 한 Azure AD 계정으로 컨텍스트 개체를 만드는 것을 지정 합니다.

꺾쇠 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>컨테이너에 대 한 사용자 위임 SAS 만들기

컨테이너에 대 한 사용자 위임 SAS 토큰을 반환 하려면 이전에 만든 Azure Storage 컨텍스트 개체를 전달 하 여 [AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) 명령을 호출 합니다.

다음 예에서는 컨테이너에 대 한 사용자 위임 SAS 토큰을 반환 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

반환 된 사용자 위임 SAS 토큰은 다음과 유사 합니다.

```
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Blob에 대 한 사용자 위임 SAS 만들기

Blob에 대 한 사용자 위임 SAS 토큰을 반환 하려면 이전에 만든 Azure Storage 컨텍스트 개체를 전달 하 여 [AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) 명령을 호출 합니다.

다음 구문은 blob에 대 한 사용자 위임 SAS를 반환 합니다. 이 예에서는 SAS `-FullUri` 토큰이 추가 된 blob URI를 반환 하는 매개 변수를 지정 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

반환 된 사용자 위임 SAS URI는 다음과 유사 합니다.

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> 사용자 위임 SAS는 저장 된 액세스 정책을 사용 하 여 사용 권한을 정의 하는 것을 지원 하지 않습니다.

## <a name="revoke-a-user-delegation-sas"></a>사용자 위임 SAS 해지

Azure PowerShell에서 사용자 위임 SAS를 해지 하려면 **AzStorageAccountUserDelegationKeys** 명령을 호출 합니다. 이 명령은 지정 된 저장소 계정과 연결 된 모든 사용자 위임 키를 해지 합니다. 이러한 키와 연결 된 공유 액세스 서명은 무효화 됩니다.

꺾쇠 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> 사용자 위임 키와 RBAC 역할 할당은 모두 Azure Storage에 의해 캐시 되므로, 해지 프로세스를 시작 하 고 기존 사용자 위임 SAS가 무효화 될 때 사이에 지연이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 위임 SAS (REST API) 만들기](/rest/api/storageservices/create-user-delegation-sas)
- [사용자 위임 키 가져오기 작업](/rest/api/storageservices/get-user-delegation-key)
