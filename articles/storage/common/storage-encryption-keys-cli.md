---
title: Azure CLI를 사용 하 여 고객 관리 키 구성
titleSuffix: Azure Storage
description: Azure CLI를 사용 하 여 Azure Storage 암호화를 위해 고객이 관리 하는 Azure Key Vault 키를 구성 하는 방법을 알아봅니다. 고객 관리 키를 사용 하 여 액세스 제어를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bf21cd27fa290b9b9b863803aef043eccc815573
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912706"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

이 문서에서는 Azure CLI를 사용 하 여 고객 관리 키를 사용 하 여 Azure Key Vault를 구성 하는 방법을 보여 줍니다. Azure CLI를 사용 하 여 key vault를 만드는 방법을 알아보려면 [빠른 시작: Azure CLI를 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](../../key-vault/quick-create-cli.md)을 참조 하세요.

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

Azure CLI를 사용 하 여 기존 주요 자격 증명 모음에서 **일시 삭제** 를 사용 하도록 설정 하 고 **제거 하지 않도록** 설정 하는 방법에 대 한 자세한 내용은 CLI를 사용 하 여 [소프트 삭제를 사용 하는 방법](../../key-vault/key-vault-soft-delete-cli.md)에서 **일시 삭제 사용** 및 **제거 방지 기능** 사용 섹션을

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

저장소 계정의 암호화 설정을 업데이트 하려면 다음 예제와 같이 [az storage account update](/cli/azure/storage/account#az-storage-account-update)를 호출 합니다. `--encryption-key-source` 매개 변수를 포함 하 고 `Microsoft.Keyvault`으로 설정 하 여 저장소 계정에 대해 고객이 관리 하는 키를 사용 하도록 설정 합니다. 또한이 예제에서는 키 자격 증명 모음 URI와 최신 키 버전을 쿼리 합니다 .이 두 값은 모두 저장소 계정에 키를 연결 하는 데 필요 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

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

새 버전의 키를 만드는 경우 새 버전을 사용 하도록 저장소 계정을 업데이트 해야 합니다. 먼저 az [keyvault show](/cli/azure/keyvault#az-keyvault-show)를 호출 하 여 KEY vault URI를 쿼리하고, [az keyvault 키 목록 버전](/cli/azure/keyvault/key#az-keyvault-key-list-versions)을 호출 하 여 키 버전을 쿼리 합니다. 그런 다음 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 를 호출 하 여 이전 섹션에 표시 된 대로 새 버전의 키를 사용 하도록 저장소 계정의 암호화 설정을 업데이트 합니다.

## <a name="use-a-different-key"></a>다른 키 사용

Azure Storage 암호화에 사용 되는 키를 변경 하려면 [고객이 관리 하는 키를 사용 하 여 암호화 구성](#configure-encryption-with-customer-managed-keys) 에 표시 된 것 처럼 [az Storage account update](/cli/azure/storage/account#az-storage-account-update) 를 호출 하 고 새 키 이름 및 버전을 제공 합니다. 새 키가 다른 키 자격 증명 모음에 있으면 키 자격 증명 모음 URI도 업데이트 합니다.

## <a name="disable-customer-managed-keys"></a>고객 관리 키 사용 안 함

고객 관리 키를 사용 하지 않도록 설정 하면 저장소 계정이 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 고객 관리 키를 사용 하지 않도록 설정 하려면 다음 예제와 같이 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 를 호출 하 고 `--encryption-key-source parameter`를 `Microsoft.Storage`로 설정 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>다음 단계

- [휴지 상태의 데이터에 대 한 암호화 Azure Storage](storage-service-encryption.md) 
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)이란?
