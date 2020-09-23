---
title: Azure Key Vault 관리 HSM에 저장 된 고객 관리 키를 사용 하 여 암호화 구성 (미리 보기)
titleSuffix: Azure Storage
description: Azure CLI를 사용 하 여 Azure Key Vault 관리 HSM (미리 보기)에 저장 된 고객 관리 키를 사용 하 여 Azure Storage 암호화를 구성 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9c742ca2fd9779589a3c8aea7f030460c5db8b5d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995978"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Azure Key Vault 관리 HSM에 저장 된 고객 관리 키를 사용 하 여 암호화 구성 (미리 보기)

Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다. 암호화 키에 대 한 추가 제어를 위해 사용자 고유의 키를 관리할 수 있습니다. 고객 관리 키는 Azure Key Vault 또는 Key Vault 관리 되는 HSM (하드웨어 보안 모델) (미리 보기)에 저장 되어야 합니다. Azure Key Vault 관리 되는 HSM은 FIPS 140-2 수준 3 유효성 검사 된 HSM입니다.

이 문서에서는 Azure CLI를 사용 하 여 관리 되는 HSM에 저장 된 고객 관리 키를 사용 하 여 암호화를 구성 하는 방법을 보여 줍니다. 키 자격 증명 모음에 저장 된 고객 관리 키를 사용 하 여 암호화를 구성 하는 방법에 대 한 자세한 내용은 [Azure Key Vault에 저장 된 고객이 관리 하는 키로 암호화 구성](customer-managed-keys-configure-key-vault.md)을 참조 하세요.

> [!NOTE]
> Azure Key Vault 및 Azure Key Vault 관리 HSM은 구성에 대해 동일한 Api 및 관리 인터페이스를 지원 합니다.

## <a name="assign-an-identity-to-the-storage-account"></a>저장소 계정에 id 할당

먼저 시스템 할당 관리 id를 저장소 계정에 할당 합니다. 관리 되는 id를 사용 하 여 저장소 계정에 관리 되는 HSM에 액세스할 수 있는 권한을 부여 합니다. 시스템 할당 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.

Azure CLI를 사용 하 여 관리 되는 id를 할당 하려면 [az storage account update](/cli/azure/storage/account#az-storage-account-update)를 호출 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>관리 되는 HSM에 액세스 하기 위해 저장소 계정에 역할 할당

그런 다음 저장소 계정에 관리 되는 HSM에 대 한 권한이 있도록 **관리 되는 Hsm 암호화 서비스 암호화** 역할을 저장소 계정의 관리 되는 id에 할당 합니다. 관리 id에 가능한 최소 권한을 부여 하기 위해 역할 할당을 개별 키 수준으로 범위를 지정 하는 것이 좋습니다.

저장소 계정에 대 한 역할 할당을 만들려면 [az key vault role 할당 create](/cli/azure/role/assignment#az_role_assignment_create)를 호출 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>관리 되는 HSM에서 키를 사용 하 여 암호화 구성

마지막으로, 관리 되는 HSM에 저장 된 키를 사용 하도록 고객이 관리 하는 키를 사용 하 여 Azure Storage 암호화를 구성 합니다. 지원 되는 키 유형에는 2048, 3072 및 4096의 RSA HSM 키가 포함 됩니다. Azure CLI 2.12.0 이상을 설치 하 여 관리 되는 HSM에서 고객이 관리 하는 키를 사용 하도록 암호화를 구성 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

고객 관리 키에 대 한 키 버전을 자동으로 업데이트 하려면 저장소 계정에 대 한 고객 관리 키를 사용 하 여 암호화를 구성할 때 키 버전을 생략 합니다. 다음 예제와 같이 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 를 호출 하 여 저장소 계정의 암호화 설정을 업데이트 합니다. 를 포함 하 `--encryption-key-source parameter` 고 해당 `Microsoft.Keyvault` 계정에 대해 고객이 관리 하는 키를 사용 하도록 설정 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

고객 관리 키에 대 한 버전을 수동으로 업데이트 하려면 저장소 계정에 대 한 암호화를 구성할 때 키 버전을 포함 합니다.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

키 버전을 수동으로 업데이트 하는 경우 새 버전을 사용 하도록 저장소 계정의 암호화 설정을 업데이트 해야 합니다. 먼저 az [keyvault show](/cli/azure/keyvault#az-keyvault-show)를 호출 하 여 KEY vault URI를 쿼리하고, [az keyvault 키 목록 버전](/cli/azure/keyvault/key#az-keyvault-key-list-versions)을 호출 하 여 키 버전을 쿼리 합니다. 그런 다음 이전 예제와 같이 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 를 호출 하 여 새 버전의 키를 사용 하도록 저장소 계정의 암호화 설정을 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Storage 암호화를 위한 고객 관리 키](customer-managed-keys-overview.md)