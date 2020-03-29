---
title: Azure CLI를 사용하여 컨테이너 또는 Blob에 대한 사용자 위임 SAS를 만듭니다.
titleSuffix: Azure Storage
description: Azure CLI를 사용하여 Azure Active Directory 자격 증명을 사용하여 사용자 위임 SAS를 만드는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371992"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Azure CLI를 사용하여 컨테이너 또는 Blob에 대한 사용자 위임 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 Azure Active Directory(Azure AD) 자격 증명을 사용하여 Azure CLI를 사용하여 컨테이너 또는 Blob에 대한 사용자 위임 SAS를 만드는 방법을 보여 주며 있습니다.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Azure CLI 최신 버전 설치

Azure CLI를 사용하여 Azure AD 자격 증명을 사용하여 SAS를 보호하려면 먼저 Azure CLI의 최신 버전을 설치했는지 확인합니다. Azure CLI 설치에 대한 자세한 내용은 [Azure CLI 설치를](/cli/azure/install-azure-cli)참조하십시오.

Azure CLI를 사용하여 사용자 위임 SAS를 만들려면 버전 2.0.78 이상에 버전을 설치했는지 확인합니다. 설치된 버전을 확인하려면 `az --version` 명령을 사용합니다.

## <a name="sign-in-with-azure-ad-credentials"></a>Azure AD 자격 증명으로 로그인

Azure AD 자격 증명을 사용하여 Azure CLI에 로그인합니다. 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

## <a name="assign-permissions-with-rbac"></a>RBAC에 사용 권한 할당

Azure PowerShell에서 사용자 위임 SAS를 만들려면 Azure CLI에 로그인하는 데 사용되는 Azure AD 계정에 **Microsoft.Storage/StorageAccounts/blobServices/generateUserDelegationKey** 작업을 포함하는 역할이 할당되어야 합니다. 이 권한을 사용하면 Azure AD 계정이 *사용자 위임 키를*요청할 수 있습니다. 사용자 위임 키는 사용자 위임 SAS에 서명하는 데 사용됩니다. **Microsoft.Storage/StorageAccounts/blobServices/generateUserDelegationKey** 작업을 제공하는 역할은 저장소 계정, 리소스 그룹 또는 구독 수준에서 할당되어야 합니다.

Azure AD 보안 주체에 RBAC 역할을 할당할 수 있는 충분한 권한이 없는 경우 계정 소유자 또는 관리자에게 필요한 권한을 할당하도록 요청해야 할 수 있습니다.

다음 예제에서는 **Microsoft.Storage/StorageAccounts/blobServices/generateUserDelegationKey** 작업을 포함 하는 **저장소 Blob 데이터 기여자** 역할을 할당 합니다. 역할은 저장소 계정 수준에서 범위가 조정됩니다.

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

**Microsoft.Storage/StorageAccounts/blobServices/generateUserDelegationKey** 작업을 포함하는 기본 제공 역할에 대한 자세한 내용은 [Azure 리소스에 대한 기본 제공 역할을](../../role-based-access-control/built-in-roles.md)참조하십시오.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD 자격 증명을 사용하여 SAS 보안

Azure CLI를 사용하여 사용자 위임 SAS를 만들 때 SAS에 서명하는 데 사용되는 사용자 위임 키가 암시적으로 만들어집니다. SAS에 대해 지정한 시작 시간 및 만료 시간은 사용자 위임 키의 시작 시간 및 만료 시간으로도 사용됩니다.

사용자 위임 키가 유효한 최대 간격은 시작 날짜로부터 7일이므로 시작 시간으로부터 7일 이내에 SAS의 만료 시간을 지정해야 합니다. 사용자 위임 키가 만료된 후에도 SAS가 유효하지 않으므로 만료 시간이 7일을 초과하는 SAS는 7일 동안만 유효합니다.

사용자 위임 SAS를 만들 `--auth-mode login` `--as-user parameters` 때, 및 필수입니다. Azure Storage에 `--auth-mode` 대한 요청이 Azure AD 자격 증명으로 승인되도록 매개 변수에 대한 *로그인을* 지정합니다. 반환된 `--as-user` SAS가 사용자 위임 SAS여야 함을 나타내는 매개 변수를 지정합니다.

### <a name="create-a-user-delegation-sas-for-a-container"></a>컨테이너에 대한 사용자 위임 SAS 만들기

Azure CLI가 있는 컨테이너에 대한 사용자 위임 SAS를 만들려면 [az 저장소 컨테이너 생성 sas](/cli/azure/storage/container#az-storage-container-generate-sas) 명령을 호출합니다.

컨테이너에서 사용자 위임 SAS에 대한 지원되는 사용 권한에는 추가, 만들기, 삭제, 목록, 읽기 및 쓰기가 포함됩니다. 사용 권한을 개별적으로 지정하거나 결합할 수 있습니다. 이러한 사용 권한에 대한 자세한 내용은 [사용자 위임 SAS 만들기를](/rest/api/storageservices/create-user-delegation-sas)참조하십시오.

다음 예제는 컨테이너에 대한 사용자 위임 SAS 토큰을 반환합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

반환되는 사용자 위임 SAS 토큰은 다음과 유사합니다.

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Blob에 대한 사용자 위임 SAS 만들기

Azure CLI를 사용하여 Blob에 대한 사용자 위임 SAS를 만들려면 [az 저장소 Blob 생성 sas](/cli/azure/storage/blob#az-storage-blob-generate-sas) 명령을 호출합니다.

Blob에서 사용자 위임 SAS에 대한 지원되는 사용 권한에는 추가, 만들기, 삭제, 읽기 및 쓰기가 포함됩니다. 사용 권한을 개별적으로 지정하거나 결합할 수 있습니다. 이러한 사용 권한에 대한 자세한 내용은 [사용자 위임 SAS 만들기를](/rest/api/storageservices/create-user-delegation-sas)참조하십시오.

다음 구문은 Blob에 대한 사용자 위임 SAS를 반환합니다. 이 예제에서는 SAS 토큰이 `--full-uri` 추가된 Blob URI를 반환하는 매개 변수를 지정합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

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

반환된 사용자 위임 SAS URI는 다음과 유사합니다.

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> 사용자 위임 SAS는 저장된 액세스 정책으로 사용 권한 정의를 지원하지 않습니다.

## <a name="revoke-a-user-delegation-sas"></a>사용자 위임 SAS 해지

Azure CLI에서 사용자 위임 SAS를 취소하려면 [az 저장소 계정 해지 위임-키](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) 명령을 호출합니다. 이 명령은 지정된 저장소 계정과 연결된 모든 사용자 위임 키를 해제합니다. 이러한 키와 연결된 모든 공유 액세스 서명은 무효화됩니다.

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> 사용자 위임 키와 RBAC 역할 할당은 모두 Azure Storage에 의해 캐시되므로 해지 프로세스를 시작할 때와 기존 사용자 위임 SAS가 유효하지 않게 되는 시점 사이에 지연이 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 위임 SAS(REST API) 만들기](/rest/api/storageservices/create-user-delegation-sas)
- [사용자 위임 키 작업 받기](/rest/api/storageservices/get-user-delegation-key)
