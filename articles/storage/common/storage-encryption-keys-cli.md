---
title: Azure CLI에서 Azure Storage 암호화를 위해 고객이 관리 하는 키 구성
description: Azure CLI를 사용 하 여 Azure Storage 암호화를 위해 고객이 관리 하는 키를 구성 하는 방법을 알아봅니다. 고객 관리 키를 사용 하 여 액세스 제어를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/24/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ca155763109dbf4292738354244dc397272d06f4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002248"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Azure CLI에서 Azure Storage 암호화를 위해 고객이 관리 하는 키 구성

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

이 문서에서는 Azure CLI를 사용 하 여 고객 관리 키로 key vault를 구성 하는 방법을 보여 줍니다.

> [!IMPORTANT]
> Azure Storage 암호화와 함께 고객 관리 키를 사용 하려면 키 자격 증명 모음에 구성 된 두 가지 필수 속성인 **일시 삭제** 및 **제거 안 함**이 있어야 합니다. 이러한 속성은 Azure Portal에서 새 키 자격 증명 모음을 만들 때 기본적으로 사용 하도록 설정 됩니다. 그러나 기존 키 자격 증명 모음에서 이러한 속성을 사용 하도록 설정 해야 하는 경우에는 PowerShell 또는 Azure CLI를 사용 해야 합니다.
> RSA 키와 키 크기 2048만 지원 됩니다.

## <a name="assign-an-identity-to-the-storage-account"></a>저장소 계정에 id 할당

저장소 계정에 대해 고객이 관리 하는 키를 사용 하도록 설정 하려면 먼저 시스템 할당 관리 id를 저장소 계정에 할당 합니다. 이 관리 되는 id를 사용 하 여 저장소 계정에 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 합니다.

Azure CLI를 사용 하 여 관리 되는 id를 할당 하려면 [az storage account update](/cli/azure/storage/account#az-storage-account-update)를 호출 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Azure CLI를 사용 하 여 시스템 할당 관리 id를 구성 하는 방법에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 AZURE VM에서 azure 리소스에 대 한 관리 Id 구성](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)을 참조 하세요.

## <a name="create-a-new-key-vault"></a>새 key vault 만들기

Azure Storage 암호화를 위해 고객이 관리 하는 키를 저장 하는 데 사용 하는 key vault에는 두 가지 키 보호 설정, **일시 삭제** 및 **제거 안 함**이 있어야 합니다. PowerShell 또는 이러한 설정이 활성화 된 Azure CLI를 사용 하 여 새 key vault를 만들려면 다음 명령을 실행 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 

Azure CLI를 사용 하 여 새 키 자격 증명 모음을 만들려면 [az keyvault create](/cli/azure/keyvault#az-keyvault-create)를 호출 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 구성

그런 다음, 저장소 계정에 액세스할 수 있는 권한이 있도록 키 자격 증명 모음에 대 한 액세스 정책을 구성 합니다. 이 단계에서는 이전에 저장소 계정에 할당 한 관리 되는 id를 사용 합니다.

키 자격 증명 모음에 대 한 액세스 정책을 설정 하려면 [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy)를 호출 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

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
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>새 키 만들기

다음으로 키 자격 증명 모음에 키를 만듭니다. 키를 만들려면 [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)를 호출 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화 구성

기본적으로 Azure Storage 암호화는 Microsoft 관리 키를 사용 합니다. 고객 관리 키에 대 한 Azure Storage 계정을 구성 하 고 저장소 계정과 연결할 키를 지정 합니다.

저장소 계정의 암호화 설정을 업데이트 하려면 [az storage account update](/cli/azure/storage/account#az-storage-account-update)를 호출 합니다. 또한이 예제에서는 키 자격 증명 모음 URI 및 키 버전을 쿼리 합니다 .이 두 값은 모두 키를 저장소 계정에 연결 하는 데 필요 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
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

새 버전의 키를 만드는 경우 새 버전을 사용 하도록 저장소 계정을 업데이트 해야 합니다. 먼저 az [keyvault show](/cli/azure/keyvault#az-keyvault-show)를 호출 하 여 KEY vault URI를 쿼리하고, [az keyvault 키 목록 버전](/cli/azure/keyvault/key#az-keyvault-key-list-versions)을 호출 하 여 키 버전을 쿼리 합니다. 그런 다음 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 를 호출 하 여 이전 섹션에 표시 된 대로 새 버전의 키를 사용 하도록 저장소 계정의 암호화 설정을 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

- [휴지 상태의 데이터에 대 한 암호화 Azure Storage](storage-service-encryption.md) 
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)이란?
