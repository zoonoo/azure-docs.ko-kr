---
title: Azure CLI를 사용하여 컨테이너 또는 Blob에 대한 사용자 위임 SAS 만들기
titleSuffix: Azure Storage
description: Azure CLI를 사용하여 Azure Active Directory 자격 증명으로 사용자 위임 SAS를 만드는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ec434b9b6da3b3b80a3afddbb432ddeece2b389
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788550"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Azure CLI를 사용하여 컨테이너 또는 Blob에 대한 사용자 위임 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 Azure AD(Azure Active Directory) 자격 증명을 사용하여 Azure CLI를 통해 컨테이너 또는 Blob에 대한 사용자 위임 SAS를 만드는 방법을 보여 줍니다.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Azure CLI 최신 버전 설치

Azure CLI를 사용하여 Azure AD 자격 증명으로 SAS를 보호하려면 먼저 최신 버전의 Azure CLI를 설치했는지 확인합니다. Azure CLI를 설치하는 방법에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

Azure CLI를 사용하여 사용자 위임 SAS를 만들려면 버전 2.0.78 이상을 설치했는지 확인합니다. 설치된 버전을 확인하려면 `az --version` 명령을 사용합니다.

## <a name="sign-in-with-azure-ad-credentials"></a>Azure AD 자격 증명을 사용하여 로그인

Azure AD 자격 증명을 사용하여 Azure CLI에 로그인합니다. 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

## <a name="assign-permissions-with-azure-rbac"></a>Azure RBAC를 사용하여 권한 할당

Azure PowerShell에서 사용자 위임 SAS를 만들려면 Azure CLI에 로그인하는 데 사용되는 Azure AD 계정에 **Microsoft.Storage/storageAccounts/BlobServices/generateUserDelegationKey** 작업을 포함하는 역할이 할당되어야 합니다. 이 권한을 통해 해당 Azure AD 계정은 *사용자 위임 키* 를 요청할 수 있습니다. 사용자 위임 키는 사용자 위임 SAS에 서명하는 데 사용됩니다. **Microsoft.Storage/storageAccounts/BlobServices/generateUserDelegationKey** 작업을 제공하는 역할은 스토리지 계정, 리소스 그룹 또는 구독 수준에서 할당되어야 합니다.

Azure AD 보안 주체에 Azure 역할을 할당할 권한이 없는 경우 계정 소유자 또는 관리자에게 필요한 권한을 할당하도록 요청해야 할 수 있습니다.

다음 예에서는 **Microsoft.Storage/storageAccounts/BlobServices/generateUserDelegationKey** 작업을 포함하는  **Storage Blob 데이터 기여자** 역할을 할당합니다. 역할의 범위는 스토리지 계정 수준에서 지정됩니다.

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

**Microsoft.Storage/storageAccounts/BlobServices/generateUserDelegationKey** 작업을 포함하는 기본 제공 역할에 대한 자세한 내용은 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD 자격 증명을 사용하여 SAS 보안

Azure CLI를 사용하여 사용자 위임 SAS를 만들면 SAS에 서명하는 데 사용되는 사용자 위임 키가 암시적으로 만들어집니다. SAS에 대해 지정하는 시작 시간 및 만료 시간은 사용자 위임 키의 시작 시간 및 만료 시간으로도 사용됩니다.

사용자 위임 키가 유효한 최대 간격은 시작 날짜로부터 7일이므로 시작 시간으로부터 7일 이내에 SAS 만료 시간을 지정해야 합니다. 사용자 위임 키가 만료된 후에는 SAS가 유효하지 않으므로 만료 시간이 7일 이상인 SAS도 7일 동안만 유효합니다.

사용자 위임 SAS를 만들 때 `--auth-mode login` 및 `--as-user parameters`가 필요합니다. Azure Storage에 대한 요청이 Azure AD 자격 증명으로 승인되도록 `--auth-mode` 매개 변수에 *login* 을 지정합니다. 반환된 SAS가 사용자 위임 SAS여야 함을 표시하려면 `--as-user` 매개 변수를 지정합니다.

### <a name="create-a-user-delegation-sas-for-a-container"></a>컨테이너에 대한 사용자 위임 SAS 만들기

Azure CLI를 사용하여 컨테이너에 대한 사용자 위임 SAS를 만들려면 [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas) 명령을 호출합니다.

컨테이너에서 사용자 위임 SAS에 지원되는 권한에는 추가, 만들기, 삭제, 나열, 읽기 및 쓰기가 포함됩니다. 권한은 단독으로 또는 결합하여 지정할 수 있습니다. 이러한 권한에 대한 자세한 내용은 [사용자 위임 SAS 만들기](/rest/api/storageservices/create-user-delegation-sas)를 참조하세요.

다음 예는 컨테이너에 대한 사용자 위임 SAS 토큰을 반환합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

반환된 사용자 위임 SAS 토큰은 다음과 유사합니다.

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Blob에 대한 사용자 위임 SAS 만들기

Azure CLI를 사용하여 Blob에 대한 사용자 위임 SAS를 만들려면 [az storage Blob generate-sas](/cli/azure/storage/blob#az_storage_blob_generate_sas) 명령을 호출합니다.

Blob에서 사용자 위임 SAS에 지원되는 권한에는 추가, 만들기, 삭제, 읽기 및 쓰기가 포함됩니다. 권한은 단독으로 또는 결합하여 지정할 수 있습니다. 이러한 권한에 대한 자세한 내용은 [사용자 위임 SAS 만들기](/rest/api/storageservices/create-user-delegation-sas)를 참조하세요.

다음 구문은 Blob에 대한 사용자 위임 SAS를 반환합니다. 이 예에서는 SAS 토큰이 추가된 Blob URI를 반환하는 `--full-uri` 매개 변수를 지정합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user \
    --full-uri
```

반환된 사용자 위임 SAS URI는 다음과 유사합니다.

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> 사용자 위임 SAS는 저장된 액세스 정책을 사용한 권한 정의를 지원하지 않습니다.

## <a name="revoke-a-user-delegation-sas"></a>사용자 위임 SAS 철회

Azure CLI에서 사용자 위임 SAS를 철회하려면 [az storage account revoke-delegation-keys](/cli/azure/storage/account#az_storage_account_revoke_delegation_keys) 명령을 호출합니다. 이 명령은 지정된 스토리지 계정과 연결된 모든 사용자 위임 키를 철회합니다. 이러한 키와 관련된 모든 공유 액세스 서명은 무효화됩니다.

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> 사용자 위임 키와 Azure 역할 할당은 모두 Azure Storage에서 캐시되므로 철회 프로세스를 시작하는 시점과 기존 사용자 위임 SAS가 무효화되는 시점 사이에 지연이 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 위임 SAS(REST API) 만들기](/rest/api/storageservices/create-user-delegation-sas)
- [사용자 위임 키 가져오기 작업](/rest/api/storageservices/get-user-delegation-key)
