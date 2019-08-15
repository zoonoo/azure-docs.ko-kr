---
title: Azure CLI (미리 보기)를 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS를 만듭니다 Azure Storage
description: Azure CLI를 사용 하 여 Azure Storage에서 Azure Active Directory 자격 증명을 사용 하 여 사용자 위임 SAS를 만드는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c8b17c4a2c58595d12a88ed3aff6c928b56f5e2c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990807"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli-preview"></a>Azure CLI (미리 보기)를 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 Azure AD (Azure Active Directory) 자격 증명을 사용 하 여 Azure CLI (미리 보기)를 통해 컨테이너 또는 blob에 대 한 사용자 위임 SAS를 만드는 방법을 보여 줍니다.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>최신 버전의 Azure CLI 설치

Azure CLI를 사용 하 여 Azure AD 자격 증명으로 SAS를 보호 하려면 먼저 최신 버전의 Azure CLI를 설치 했는지 확인 합니다. Azure CLI를 설치 하는 방법에 대 한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요.

## <a name="sign-in-with-azure-ad-credentials"></a>Azure AD 자격 증명으로 로그인

Azure AD 자격 증명을 사용 하 여 Azure CLI에 로그인 합니다. 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

## <a name="assign-permissions-with-rbac"></a>RBAC를 사용 하 여 권한 할당

Azure PowerShell에서 사용자 위임 SAS를 만들려면 Azure CLI에 로그인 하는 데 사용 되는 Azure AD 계정에 **Microsoft Storage/storageAccounts/blobServices/generateUserDelegationKey** 작업을 포함 하는 역할을 할당 해야 합니다. 이 사용 권한을 통해 Azure AD 계정에서 *사용자 위임 키*를 요청할 수 있습니다. 사용자 위임 키는 사용자 위임 SAS에 서명 하는 데 사용 됩니다. 저장소 계정, 리소스 그룹 또는 구독 수준에서 **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** 작업을 제공 하는 역할을 할당 해야 합니다.

Azure AD 보안 주체에 RBAC 역할을 할당할 수 있는 권한이 없는 경우 계정 소유자 또는 관리자에 게 필요한 권한을 할당 하도록 요청 해야 할 수 있습니다.

다음 예에서는 **저장소 Blob 데이터 참가자** 역할을 할당 합니다. 여기에는 **Microsoft Storage/Storageaccounts/Blobservices/generateUserDelegationKey** action이 포함 됩니다. 역할의 범위는 저장소 계정 수준에서 지정 됩니다.

꺾쇠 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

**Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** action을 포함 하는 기본 제공 역할에 대 한 자세한 내용은 [Azure 리소스에 대 한 기본 제공 역할](/role-based-access-control/built-in-roles)을 참조 하세요.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD 자격 증명을 사용 하 여 SAS 보안

Azure CLI를 사용 하 여 사용자 위임 SAS를 만들 때 SAS에 서명 하는 데 사용 되는 사용자 위임 키는 암시적으로 생성 됩니다. SAS에 대해 지정 하는 시작 시간 및 만료 시간은 사용자 위임 키에 대 한 시작 시간 및 만료 시간으로도 사용 됩니다.

사용자 위임 키가 유효한 최대 간격은 시작 날짜 로부터 7 일 이므로 시작 시간의 7 일 이내에 SAS에 대 한 만료 시간을 지정 해야 합니다. 사용자 위임 키가 만료 된 후에는 SAS가 유효 하지 않으므로 만료 시간이 7 일을 초과 하는 SAS는 7 일 동안만 유효 합니다.

사용자 위임 SAS를 `--auth-mode login` 만들 때 및 `--as-user parameters` 가 필요 합니다. Azure Storage 에 대 한 `--auth-mode` 요청이 Azure AD 자격 증명으로 인증 되도록 매개 변수에 대 한 로그인을 지정 합니다. 반환 된 sas가 사용자 위임 sas 여야 함을 나타내려면 매개변수를지정합니다.`--as-user`

### <a name="create-a-user-delegation-sas-for-a-container"></a>컨테이너에 대 한 사용자 위임 SAS 만들기

Azure CLI를 사용 하 여 컨테이너에 대 한 사용자 위임 SAS를 만들려면 [az storage container generate SAS](/cli/azure/storage/container#az-storage-container-generate-sas) 명령을 호출 합니다.

컨테이너에서 사용자 위임 SAS에 대해 지원 되는 권한에는 Add, Create, Delete, List, Read 및 Write가 있습니다. 권한은 단독으로 지정 하거나 조합 하 여 지정할 수 있습니다. 이러한 사용 권한에 대 한 자세한 내용은 [사용자 위임 SAS 만들기](/rest/api/storageservices/create-a-user-delegation-sas)를 참조 하세요.

다음 예에서는 컨테이너에 대 한 사용자 위임 SAS 토큰을 반환 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

반환 된 사용자 위임 SAS 토큰은 다음과 유사 합니다.

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Blob에 대 한 사용자 위임 SAS 만들기

Azure CLI를 사용 하 여 blob에 대 한 사용자 위임 SAS를 만들려면 [az storage blob generate sas](/cli/azure/storage/blob#az-storage-blob-generate-sas) 명령을 호출 합니다.

Blob에서 사용자 위임 SAS에 대해 지원 되는 권한은 추가, 만들기, 삭제, 읽기 및 쓰기입니다. 권한은 단독으로 지정 하거나 조합 하 여 지정할 수 있습니다. 이러한 사용 권한에 대 한 자세한 내용은 [사용자 위임 SAS 만들기](/rest/api/storageservices/create-a-user-delegation-sas)를 참조 하세요.

다음 구문은 blob에 대 한 사용자 위임 SAS를 반환 합니다. 이 예에서는 SAS `--full-uri` 토큰이 추가 된 blob URI를 반환 하는 매개 변수를 지정 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

반환 된 사용자 위임 SAS URI는 다음과 유사 합니다.

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> 사용자 위임 SAS는 저장 된 액세스 정책을 사용 하 여 사용 권한을 정의 하는 것을 지원 하지 않습니다.

## <a name="see-also"></a>참조

- [사용자 위임 SAS (REST API) 만들기](/rest/api/storageservices/create-a-user-delegation-sas)
- [사용자 위임 키 가져오기 작업](/rest/api/storageservices/get-user-delegation-key)
