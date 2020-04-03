---
title: Azure CLI를 사용하여 고객 관리 키 구성
titleSuffix: Azure Storage
description: Azure CLI를 사용하여 Azure 저장소 암호화에 대한 Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 구성하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea944d4cfa3006c33f1dee3dd8e6ee6088681aa7
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618638"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Azure CLI를 사용하여 Azure 키 자격 증명 모음을 사용하여 고객 관리 키 구성

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

이 문서에서는 Azure CLI를 사용하여 고객 관리 키로 Azure 키 자격 증명 모음을 구성하는 방법을 보여 주며 있습니다. Azure CLI를 사용하여 키 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure CLI를 사용하여 Azure 키 자격 증명 모음에서 비밀을 설정하고 검색합니다.](../../key-vault/quick-create-cli.md)

## <a name="assign-an-identity-to-the-storage-account"></a>저장소 계정에 ID 할당

저장소 계정에 대한 고객 관리 키를 사용하려면 먼저 시스템 할당된 관리되는 ID를 저장소 계정에 할당합니다. 이 관리되는 ID를 사용하여 저장소 계정에서 키 자격 증명 모음에 액세스할 수 있는 권한을 부여합니다.

Azure CLI를 사용하여 관리되는 ID를 할당하려면 [az 저장소 계정 업데이트를](/cli/azure/storage/account#az-storage-account-update)호출합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Azure CLI를 사용하여 시스템 할당관리 ID를 구성하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리되는 ID 구성을](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)참조하십시오.

## <a name="create-a-new-key-vault"></a>새 키 자격 증명 모음 만들기

Azure Storage 암호화에 대한 고객 관리 키를 저장하는 데 사용하는 키 자격 증명 모음에는 두 가지 키 보호 설정인 **소프트 삭제** 및 제거 **안 함**설정이 활성화되어 있어야 합니다. 이러한 설정을 사용하도록 설정하여 PowerShell 또는 Azure CLI를 사용하여 새 키 자격 증명 모음을 만들려면 다음 명령을 실행합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

Azure CLI를 사용하여 새 키 자격 증명 모음을 만들려면 [az 키 볼트 만들기를](/cli/azure/keyvault#az-keyvault-create)호출합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Azure CLI를 사용하여 기존 키 자격 증명 모음에서 **소프트 삭제** 및 제거 안 함 사용(권한 **제거)을** 활성화하는 방법을 알아보려면 [CLI에서 소프트 삭제를 사용하는 방법에서](../../key-vault/key-vault-soft-delete-cli.md) **소프트 삭제 사용** 및 **지우기 보호 활성화** 라는 섹션을 참조하십시오.

## <a name="configure-the-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 구성

그런 다음 저장소 계정에 액세스할 수 있는 권한이 있도록 키 자격 증명 모음에 대한 액세스 정책을 구성합니다. 이 단계에서는 저장소 계정에 이전에 할당한 관리ID를 사용합니다.

키 자격 증명 모음에 대한 액세스 정책을 설정하려면 [az 키볼트 설정 정책을](/cli/azure/keyvault#az-keyvault-set-policy)호출합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>새 키 만들기

다음으로 키 자격 증명 모음에 키를 만듭니다. 키를 만들려면 [az 키볼트 키 만들기를](/cli/azure/keyvault/key#az-keyvault-key-create)호출합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

Azure 저장소 암호화를 통해 2048비트 RSA 및 RSA-HSM 키만 지원됩니다. 키에 대한 자세한 내용은 Azure [키 볼트 정보 키, 비밀 및 인증서의](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)키 자격 증명 모음 **키를** 참조하십시오.

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리 키로 암호화 구성

기본적으로 Azure 저장소 암호화는 Microsoft에서 관리하는 키를 사용합니다. Azure Storage 계정을 고객 관리 키에 대해 구성하고 저장소 계정과 연결할 키를 지정합니다.

저장소 계정의 암호화 설정을 업데이트하려면 다음 예제와 같이 [az 저장소 계정 업데이트를](/cli/azure/storage/account#az-storage-account-update)호출합니다. 매개 `--encryption-key-source` 변수를 포함하고 `Microsoft.Keyvault` 저장소 계정에 대한 고객 관리 키를 사용하도록 설정합니다. 또한 이 예제에서는 키 를 저장소 계정과 연결하는 데 필요한 값인 키 자격 증명 모음 URI 및 최신 키 버전에 대한 쿼리도 합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>키 버전 업데이트

키의 새 버전을 만들 때 새 버전을 사용 하려면 저장소 계정을 업데이트 해야 합니다. 먼저 [az keyvault 표시](/cli/azure/keyvault#az-keyvault-show)표시를 호출하여 키 볼트 URI를 쿼리하고 az keyvault 키 목록 버전을 호출하여 키 [버전에 대해 쿼리합니다.](/cli/azure/keyvault/key#az-keyvault-key-list-versions) 그런 다음 [az 저장소 계정 업데이트를](/cli/azure/storage/account#az-storage-account-update) 호출하여 이전 섹션과 같이 저장소 계정의 암호화 설정을 업데이트하여 키의 새 버전을 사용합니다.

## <a name="use-a-different-key"></a>다른 키 사용

Azure Storage 암호화에 사용되는 키를 변경하려면 [고객 관리 키로 암호화 구성에](#configure-encryption-with-customer-managed-keys) 표시된 대로 [az 저장소 계정 업데이트를](/cli/azure/storage/account#az-storage-account-update) 호출하고 새 키 이름과 버전을 제공합니다. 새 키가 다른 키 자격 증명 모음에 있는 경우 키 자격 증명 모음 URI도 업데이트합니다.

## <a name="revoke-customer-managed-keys"></a>고객 관리 키 해지

키가 손상되었다고 생각되면 키 자격 증명 모음 액세스 정책을 제거하여 고객 관리 키를 해지할 수 있습니다. 고객 관리 키를 해지하려면 다음 예제와 같이 [az keyvault 삭제 정책](/cli/azure/keyvault#az-keyvault-delete-policy) 명령을 호출합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꾸고 이전 예제에서 정의된 변수를 사용해야 합니다.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>고객 관리 키 비활성화

고객 관리 키를 사용하지 않도록 설정하면 저장소 계정이 다시 한 번 Microsoft 관리 키로 암호화됩니다. 고객 관리 키를 사용하지 않도록 설정하려면 az `--encryption-key-source parameter` `Microsoft.Storage`저장소 [계정 업데이트를](/cli/azure/storage/account#az-storage-account-update) 호출하고 다음 예제와 같이 를 로 설정합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꾸고 이전 예제에서 정의된 변수를 사용해야 합니다.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure 저장소 암호화](storage-service-encryption.md) 
- [Azure 키 볼트란?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
