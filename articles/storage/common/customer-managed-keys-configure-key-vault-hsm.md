---
title: Azure Key Vault 관리되는 HSM에 저장된 고객 관리형 키를 사용하여 암호화 구성(미리 보기)
titleSuffix: Azure Storage
description: Azure CLI를 사용하여 Azure Key Vault 관리되는 HSM(미리 보기)에 저장된 고객 관리형 키로 Azure Storage 암호화를 구성하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: ea51c1f7fcfce5b795965eab2f9c03a820a6ab03
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059363"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Azure Key Vault 관리되는 HSM에 저장된 고객 관리형 키를 사용하여 암호화 구성(미리 보기)

Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다. 암호화 키에 대한 추가 제어를 위해 사용자 고유의 키를 관리할 수 있습니다. 고객 관리형 키는 Azure Key Vault 또는 Key Vault 관리되는 HSM(하드웨어 보안 모델)(미리 보기)에 저장되어야 합니다. Azure Key Vault 관리되는 HSM은 FIPS 140-2 수준 3 유효성이 검증된 HSM입니다.

이 문서에서는 Azure CLI를 사용하여 관리되는 HSM에 저장된 고객 관리형 키로 암호화를 구성하는 방법을 보여 줍니다. Key Vault에 저장된 고객 관리형 키로 암호화를 구성하는 방법에 대해 알아보려면 [Azure Key Vault에 저장된 고객 관리형 키로 암호화 구성](customer-managed-keys-configure-key-vault.md)을 참조하세요.

> [!IMPORTANT]
>
> Azure Key Vault 관리되는 HSM에 저장된 고객 관리형 키로 암호화 구성은 현재 **PREVIEW** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> Azure Key Vault 및 Azure Key Vault 관리되는 HSM은 구성에 대해 동일한 API 및 관리 인터페이스를 지원합니다.

## <a name="assign-an-identity-to-the-storage-account"></a>스토리지 계정에 ID 할당

먼저 시스템이 할당한 관리 ID를 스토리지 계정에 할당합니다. 이 관리 ID를 사용하여 스토리지 계정에서 관리되는 HSM에 액세스할 수 있는 권한을 부여합니다. 시스템이 할당한 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

Azure CLI를 사용하여 관리 ID를 할당하려면 [az storage account update](/cli/azure/storage/account#az-storage-account-update)를 호출합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>관리되는 HSM에 액세스하기 위해 스토리지 계정에 역할 할당

그런 다음 스토리지 계정이 관리되는 HSM에 대한 사용 권한을 가지도록 **관리되는 HSM 암호화 서비스 암호화** 역할을 스토리지 계정의 관리 ID에 할당합니다. 관리 ID에 가능한 최소 권한을 부여하려면 역할 할당 범위를 개별 키 수준으로 지정하는 것이 좋습니다.

스토리지 계정에 대한 역할 할당을 만들려면 [az key vault role assignment create](/cli/azure/role/assignment#az_role_assignment_create)를 호출합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.
  
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

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>관리되는 HSM에서 키를 사용하여 암호화 구성

마지막으로 관리되는 HSM에서 저장된 키를 사용하도록 고객 관리형 키로 Azure Storage 암호화를 구성합니다. 지원되는 키 형식에는 2048, 3072, 4096 크기의 RSA-HSM 키가 포함됩니다. 관리되는 HSM에서 키를 만드는 방법을 알아보려면 [HSM 키 만들기](../../key-vault/managed-hsm/key-management.md#create-an-hsm-key)를 참조하세요.

Azure CLI 2.12.0 이상을 설치하여 관리되는 HSM에서 고객 관리형 키를 사용하도록 암호화를 구성합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

고객 관리형 키의 키 버전을 자동으로 업데이트하려면 스토리지 계정에 대해 고객 관리형 키로 암호화를 구성할 때 키 버전을 생략하세요. 다음 예제와 같이 [az storage account update](/cli/azure/storage/account#az_storage_account_update)를 호출하여 스토리지 계정의 암호화 설정을 업데이트합니다. `--encryption-key-source parameter`를 포함하고 이를 `Microsoft.Keyvault`에 설정하여 계정에 대해 고객 관리형 키를 사용하도록 설정합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

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

고객 관리형 키 버전을 수동으로 업데이트하려면 스토리지 계정에 대한 암호화를 구성할 때 키 버전을 포함해야 합니다.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

키 버전을 수동으로 업데이트할 때 새 버전을 사용하려면 스토리지 계정의 암호화 설정을 업데이트해야 합니다. 먼저 [az keyvault show](/cli/azure/keyvault#az-keyvault-show)를 호출하여 KEY Vault URI를 쿼리하고 [az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions)를 호출하여 키 버전을 쿼리합니다. 그런 다음 이전 예제와 같이 [az storage account update](/cli/azure/storage/account#az-storage-account-update)를 호출하여 새 버전의 키를 사용하도록 스토리지 계정의 암호화 설정을 업데이트합니다.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Storage 암호화를 위한 고객 관리형 키](customer-managed-keys-overview.md)
