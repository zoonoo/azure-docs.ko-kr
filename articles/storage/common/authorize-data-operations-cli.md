---
title: Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure CLI를 사용하여 Blob 또는 큐 데이터에 대해 데이터 작업을 승인하는 방법을 지정합니다. Azure AD 자격 증명, 계정 액세스 키 또는 SAS(공유 액세스 서명) 토큰을 사용하여 데이터 작업에 권한을 부여할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207694"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여

Azure Storage는 Blob 또는 큐 데이터에 대한 작업을 승인하는 방법을 지정할 수 있는 Azure CLI에 대한 확장을 제공합니다. 다음과 같은 방법으로 데이터 작업에 권한을 부여할 수 있습니다.

- Azure Active Directory(Azure AD) 보안 보안 주체를 사용합니다. 뛰어난 보안과 사용 편의성을 위해 Azure AD 자격 증명을 사용하는 것이 좋습니다.
- 계정 액세스 키 또는 공유 액세스 서명(SAS) 토큰을 사용합니다.

## <a name="specify-how-data-operations-are-authorized"></a>데이터 작업 승인 방법 지정

Blob 및 큐 데이터를 읽고 쓰기 위한 Azure `--auth-mode` CLI 명령에는 선택적 매개 변수가 포함됩니다. 이 매개 변수를 지정하여 데이터 작업을 인증하는 방법을 나타냅니다.

- Azure `--auth-mode` AD `login` 보안 보안 주체(권장)를 사용하여 로그인할 매개 변수를 설정합니다.
- 매개 `--auth-mode` 변수를 레거시 `key` 값으로 설정하여 권한 부여에 사용할 계정 액세스 키를 검색하려고 시도합니다. 매개 변수를 `--auth-mode` 생략하면 Azure CLI도 액세스 키를 검색하려고 시도합니다.

매개 변수를 `--auth-mode` 사용하려면 Azure CLI 버전 2.0.46 이상에 설치했는지 확인합니다. `az --version`을 실행하여 설치된 버전을 확인합니다.

> [!IMPORTANT]
> 매개 변수를 `--auth-mode` 생략하거나 `key`로 설정하면 Azure CLI는 권한 부여를 위해 계정 액세스 키를 사용하려고 시도합니다. 이 경우 명령또는 **AZURE_STORAGE_KEY** 환경 변수에서 액세스 키를 제공하는 것이 좋습니다. 환경 변수에 대한 자세한 내용은 [권한 부여 매개 변수에 대한 환경 변수 설정](#set-environment-variables-for-authorization-parameters)섹션을 참조하십시오.
>
> 액세스 키를 제공하지 않으면 Azure CLI가 Azure Storage 리소스 공급자에게 호출하여 각 작업에 대해 검색하려고 시도합니다. 리소스 공급자에 대한 호출이 필요한 많은 데이터 작업을 수행하면 제한이 발생할 수 있습니다. 리소스 공급자 제한에 대한 자세한 내용은 [Azure Storage 리소스 공급자의 확장성 및 성능 목표를](scalability-targets-resource-provider.md)참조하십시오.

## <a name="authorize-with-azure-ad-credentials"></a>Azure AD 자격 증명으로 권한 부여

Azure AD 자격 증명을 사용하여 Azure CLI에 로그인하면 OAuth 2.0 액세스 토큰이 반환됩니다. 이 토큰은 Azure CLI에서 Blob 또는 Queue 저장소에 대한 후속 데이터 작업에 권한을 부여하기 위해 자동으로 사용됩니다. 지원되는 작업의 경우, 더 이상 명령과 함께 계정 키 또는 SAS 토큰을 전달할 필요가 없습니다.

역할 기반 액세스 제어(RBAC)를 통해 Azure AD 보안 주체에 Blob 및 큐 데이터에 대한 권한을 할당할 수 있습니다. Azure 저장소의 RBAC 역할에 대한 자세한 내용은 [RBAC를 사용하여 Azure 저장소 데이터에 대한 액세스 권한 관리를](storage-auth-aad-rbac.md)참조하십시오.

### <a name="permissions-for-calling-data-operations"></a>데이터 작업 호출에 대한 권한

Azure 저장소 확장은 Blob 및 큐 데이터에 대한 작업에 대해 지원됩니다. 호출할 수 있는 작업은 Azure CLI에 로그인하는 Azure AD 보안 보안 주체에 부여된 권한에 따라 다릅니다. Azure 저장소 컨테이너 또는 큐에 대한 권한은 RBAC를 통해 할당됩니다. 예를 들어 **Blob Data Reader** 역할이 할당된 경우 컨테이너 또는 큐에서 데이터를 읽는 스크립팅 명령을 실행할 수 있습니다. **Blob 데이터 기여자** 역할이 할당된 경우 컨테이너 또는 큐 또는 해당 역할에 포함된 데이터를 읽거나 쓰거나 삭제하는 스크립팅 명령을 실행할 수 있습니다.

컨테이너 또는 큐의 각 Azure Storage 작업에 필요한 권한에 대한 자세한 내용은 [OAuth 토큰을 사용 하 여 저장소 호출 작업을](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)참조 합니다.  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>예: Azure AD 자격 증명을 사용하여 컨테이너를 만드는 작업에 권한을 부여합니다.

다음 예제에서는 Azure AD 자격 증명을 사용하여 Azure CLI에서 컨테이너를 만드는 방법을 보여 줍니다. 컨테이너를 만들려면 Azure CLI에 로그인해야 하며 리소스 그룹과 저장소 계정이 필요합니다. 이러한 리소스를 만드는 방법에 대해 알아보려면 [빠른 시작: Azure CLI를 사용하여 Blob 만들기, 다운로드 및 목록 작성을](../blobs/storage-quickstart-blobs-cli.md)참조하십시오.

1. 컨테이너를 만들기 전에 저장소 [Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 자신에게 할당합니다. 계정 소유자인 경우에도 저장소 계정에 대해 데이터 작업을 수행하려면 명시적인 권한이 필요합니다. RBAC 역할 할당에 대한 자세한 내용은 [Azure 포털에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 권한 부여 액세스를](storage-auth-aad-rbac.md)참조하세요.

    > [!IMPORTANT]
    > RBAC 역할 할당이 전파되는 데 몇 분 정도 걸릴 수 있습니다.

1. Azure AD 자격 증명을 사용하여 `login` 컨테이너를 만들려면 매개 변수를 설정한 [az 저장소 컨테이너 create](/cli/azure/storage/container#az-storage-container-create) 명령을 호출합니다. `--auth-mode` 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>계정 액세스 키로 권한 부여

계정 키가 있는 경우 Azure Storage 데이터 작업을 호출할 수 있습니다. 일반적으로 계정 키를 사용하는 것은 안전하지 않습니다. 계정 키가 손상된 경우 계정의 모든 데이터가 손상될 수 있습니다.

다음 예제에서는 계정 액세스 키를 사용하여 컨테이너를 만드는 방법을 보여 주며 있습니다. 계정 키를 지정하고 `--auth-mode` 매개 변수에 `key` 값을 제공합니다.

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>SAS 토큰으로 권한 부여

SAS 토큰을 보유한 경우 SAS에서 허용하는 데이터 작업을 호출할 수 있습니다. 다음 예제에서는 SAS 토큰을 사용하여 컨테이너를 만드는 방법을 보여 주며 있습니다.

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>권한 부여 매개 변수에 대한 환경 변수 설정

환경 변수에 권한 부여 매개 변수를 지정하여 Azure Storage 데이터 작업에 대한 모든 호출에 이러한 매개 변수를 포함하지 않도록 할 수 있습니다. 다음 표는 사용 가능한 환경 변수에 대해 설명합니다.

| 환경 변수                  | 설명                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    스토리지 계정 이름입니다. 이 변수는 저장소 계정 키 또는 SAS 토큰과 함께 사용해야 합니다. 둘 다 없는 경우 Azure CLI는 인증된 Azure AD 계정을 사용하여 저장소 계정 액세스 키를 검색하려고 시도합니다. 한 번에 많은 수의 명령이 실행되는 경우 Azure Storage 리소스 공급자 제한 제한에 도달할 수 있습니다. 리소스 공급자 제한에 대한 자세한 내용은 [Azure Storage 리소스 공급자의 확장성 및 성능 목표를](scalability-targets-resource-provider.md)참조하십시오.             |
|    AZURE_STORAGE_KEY                  |    스토리지 계정 키입니다. 이 변수는 저장소 계정 이름과 함께 사용해야 합니다.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    저장소 계정 키 또는 SAS 토큰을 포함하는 연결 문자열입니다. 이 변수는 저장소 계정 이름과 함께 사용해야 합니다.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    공유 액세스 서명(SAS) 토큰입니다. 이 변수는 저장소 계정 이름과 함께 사용해야 합니다.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    명령을 실행할 권한 부여 모드입니다. 허용된 값은 `login` (권장) 또는 `key`. 지정하면 `login`Azure CLI는 Azure AD 자격 증명을 사용하여 데이터 작업에 권한을 부여합니다. 레거시 `key` 모드를 지정하면 Azure CLI는 계정 액세스 키를 쿼리하고 키로 명령을 승인하려고 시도합니다.    |

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 Blob 및 큐 데이터에 대한 액세스를 위해 RBAC 역할을 할당합니다.](storage-auth-aad-rbac-cli.md)
- [Azure 리소스에 대한 관리 ID로 Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-msi.md)
