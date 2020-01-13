---
title: 테이블 및 큐에 대해 고객이 관리 하는 키를 지 원하는 계정 만들기
titleSuffix: Azure Storage
description: 테이블 및 큐에 대 한 고객 관리 키 구성을 지 원하는 저장소 계정을 만드는 방법에 대해 알아봅니다. Azure CLI 또는 Azure Resource Manager 템플릿을 사용 하 여 Azure Storage 암호화를 위한 계정 암호화 키에 의존 하는 저장소 계정을 만듭니다. 그런 다음 계정에 대 한 고객 관리 키를 구성할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c501dda60b65f78bb82649bed6bca4b031ad8a6b
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913161"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>테이블 및 큐에 대해 고객이 관리 하는 키를 지 원하는 계정 만들기

Azure Storage는 미사용 저장소 계정의 모든 데이터를 암호화 합니다. 기본적으로 Queue storage 및 Table storage는 서비스로 범위가 지정 되 고 Microsoft에서 관리 하는 키를 사용 합니다. 또한 고객이 관리 하는 키를 사용 하 여 큐 또는 테이블 데이터를 암호화할 수 있습니다. 큐 및 테이블에서 고객 관리 키를 사용 하려면 먼저 서비스 대신 계정으로 범위가 한정 된 암호화 키를 사용 하는 저장소 계정을 만들어야 합니다. 큐 및 테이블 데이터에 대 한 계정 암호화 키를 사용 하는 계정을 만든 후에는 해당 저장소 계정에 대 한 Azure Key Vault를 사용 하 여 고객 관리 키를 구성할 수 있습니다.

이 문서에서는 계정으로 범위가 지정 된 키에 의존 하는 저장소 계정을 만드는 방법을 설명 합니다. 계정을 처음 만들 때 Microsoft는 계정 키를 사용 하 여 계정의 데이터를 암호화 하 고 Microsoft는 키를 관리 합니다. 그런 다음 사용자 고유의 키를 제공 하 고, 키 버전을 업데이트 하 고, 키를 회전 하 고, 액세스 제어를 취소할 수 있는 기능을 포함 하 여, 계정에 대 한 고객 관리 키를 구성 하 여 이러한 혜택을 활용할 수 있습니다.

## <a name="about-the-feature"></a>기능 정보

큐 및 테이블 저장소의 계정 암호화 키에 의존 하는 저장소 계정을 만들려면 먼저를 등록 하 여 Azure에서이 기능을 사용 해야 합니다. 제한 된 용량으로 인해 액세스 요청이 승인 되기까지 몇 개월이 걸릴 수 있습니다.

다음 지역에서 큐 및 테이블 저장소의 계정 암호화 키에 의존 하는 저장소 계정을 만들 수 있습니다.

- 미국 동부
- 미국 중남부
- 미국 서부 2  

### <a name="register-to-use-the-account-encryption-key"></a>계정 암호화 키를 사용 하도록 등록

Azure CLI 등록 하려면 [az feature register](/cli/azure/feature#az-feature-register) 명령을 호출 합니다.

큐 저장소에 계정 암호화 키를 사용 하도록 등록 하려면:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

테이블 저장소에 계정 암호화 키를 사용 하도록 등록 하려면:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="check-the-status-of-your-registration"></a>등록 상태를 확인 합니다.

큐 저장소에 대 한 등록 상태를 확인 하려면 다음을 수행 합니다.

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

테이블 저장소에 대 한 등록 상태를 확인 하려면:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="re-register-the-azure-storage-resource-provider"></a>Azure Storage 리소스 공급자를 다시 등록 합니다.

등록이 승인 된 후 Azure Storage 리소스 공급자를 다시 등록 해야 합니다. [Az provider register](/cli/azure/provider#az-provider-register) 명령을 호출 합니다.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>계정 암호화 키를 사용 하는 계정 만들기

저장소 계정을 만들 때 큐 및 테이블에 대 한 계정 암호화 키를 사용 하도록 새 저장소 계정을 구성 해야 합니다. 계정이 생성 된 후에는 암호화 키의 범위를 변경할 수 없습니다.

저장소 계정은 범용 v2 형식 이어야 하며 LRS (로컬 중복 저장소)에 대해 구성 되어야 합니다. 저장소 계정을 만들고 Azure CLI 또는 Azure Resource Manager 템플릿을 사용 하 여 계정 암호화 키를 사용 하도록 구성할 수 있습니다.

> [!NOTE]
> 저장소 계정을 만들 때 계정 암호화 키를 사용 하 여 데이터를 암호화 하도록 큐 및 테이블 저장소만 선택적으로 구성할 수 있습니다. Blob storage 및 Azure Files 항상 계정 암호화 키를 사용 하 여 데이터를 암호화 합니다.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 계정 암호화 키에 의존 하는 저장소 계정을 만들려면 Azure CLI 버전 2.0.80 이상을 설치 했는지 확인 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음으로, 다음과 같이 적절 한 매개 변수를 사용 하 여 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 명령을 호출 하 여 범용 v2 저장소 계정을 만듭니다.

- `--encryption-key-type-for-queue` 옵션을 포함 하 고 해당 값을 `Account`으로 설정 하 여 계정 암호화 키를 사용 하 여 Queue storage의 데이터를 암호화 합니다.
- `--encryption-key-type-for-table` 옵션을 포함 하 고 해당 값을 `Account`으로 설정 하 여 계정 암호화 키를 사용 하 여 테이블 저장소의 데이터를 암호화 합니다.

다음 예제에서는 LRS에 대해 구성 되 고, 계정 암호화 키를 사용 하 여 큐 및 테이블 저장소에 대 한 데이터를 암호화 하는 범용 v2 저장소 계정을 만드는 방법을 보여 줍니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

### <a name="templatetabtemplate"></a>[템플릿](#tab/template)

다음 JSON 예제에서는 LRS에 대해 구성 되 고, 계정 암호화 키를 사용 하 여 큐 및 테이블 저장소에 대 한 데이터를 암호화 하는 범용 v2 저장소 계정을 만듭니다. 꺾쇠 괄호 안의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_LRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

계정 암호화 키를 사용 하는 계정을 만든 후에는 다음 문서 중 하나를 참조 하 여 Azure Key Vault에서 고객 관리 키를 구성 합니다.

- [Azure Portal를 사용 하 여 Azure Key Vault으로 고객 관리 키 구성](storage-encryption-keys-portal.md)
- [PowerShell을 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI를 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>계정 암호화 키 확인

새 저장소 계정이 계정 암호화 키를 사용 하 고 있는지 확인 하려면 Azure CLI [az storage account](/cli/azure/storage/account#az-storage-account-show) 명령을 호출 합니다. 이 명령은 저장소 계정 속성 및 해당 값의 목록을 반환 합니다. `keyType` 속성을 찾아 `Account`으로 설정 되어 있는지 확인 합니다.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

## <a name="next-steps"></a>다음 단계

- [휴지 상태의 데이터에 대 한 암호화 Azure Storage](storage-service-encryption.md) 
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)이란?
