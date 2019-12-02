---
title: PowerShell을 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성-Azure Storage
description: PowerShell을 사용 하 여 Azure Storage 암호화를 위해 고객이 관리 하는 키를 구성 하는 방법을 알아봅니다. 고객 관리 키를 사용 하 여 액세스 제어를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bd723787d9cea2d3b9d81ae9db63c70a21190854
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666225"
---
# <a name="configure-customer-managed-keys-for-azure-storage-by-using-powershell"></a>PowerShell을 사용 하 여 Azure Storage에 대 한 고객 관리 키 구성

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

이 문서에서는 PowerShell을 사용 하 여 고객 관리 키를 사용 하 여 Azure Key Vault를 구성 하는 방법을 보여 줍니다. Azure CLI를 사용 하 여 주요 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: PowerShell을 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](../../key-vault/quick-create-powershell.md)을 참조 하세요.

> [!IMPORTANT]
> Azure Storage 암호화와 함께 고객 관리 키를 사용 하려면 키 자격 증명 모음에 대해 두 개의 속성을 설정 해야 합니다. **일시 삭제** 하 고 **제거 하지**않아야 합니다. 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않습니다. 이러한 속성을 사용 하도록 설정 하려면 PowerShell 또는 Azure CLI 중 하나를 사용 합니다.
> RSA 키와 키 크기 2048만 지원 됩니다.

## <a name="assign-an-identity-to-the-storage-account"></a>저장소 계정에 id 할당

저장소 계정에 대해 고객이 관리 하는 키를 사용 하도록 설정 하려면 먼저 시스템 할당 관리 id를 저장소 계정에 할당 합니다. 이 관리 되는 id를 사용 하 여 저장소 계정에 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 합니다.

PowerShell을 사용 하 여 관리 되는 id를 할당 하려면 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)를 호출 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

PowerShell을 사용 하 여 시스템 할당 관리 id를 구성 하는 방법에 대 한 자세한 내용은 [powershell을 사용 하 여 AZURE VM에서 azure 리소스에 대 한 관리 Id 구성](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)을 참조 하세요.

## <a name="create-a-new-key-vault"></a>새 key vault 만들기

PowerShell을 사용 하 여 새 키 자격 증명 모음을 만들려면 [AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)를 호출 합니다. Azure Storage 암호화를 위해 고객이 관리 하는 키를 저장 하는 데 사용 하는 key vault에는 두 가지 키 보호 설정, **일시 삭제** 및 **제거 안 함**이 있어야 합니다. 

대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 구성

그런 다음, 저장소 계정에 액세스할 수 있는 권한이 있도록 키 자격 증명 모음에 대 한 액세스 정책을 구성 합니다. 이 단계에서는 이전에 저장소 계정에 할당 한 관리 되는 id를 사용 합니다.

키 자격 증명 모음에 대 한 액세스 정책을 설정 하려면 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)를 호출 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>새 키 만들기

다음으로 키 자격 증명 모음에 새 키를 만듭니다. 새 키를 만들려면 [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)를 호출 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화 구성

기본적으로 Azure Storage 암호화는 Microsoft 관리 키를 사용 합니다. 이 단계에서는 고객이 관리 하는 키를 사용 하도록 Azure Storage 계정을 구성 하 고 저장소 계정과 연결할 키를 지정 합니다.

[AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 하 여 저장소 계정의 암호화 설정을 업데이트 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>키 버전 업데이트

새 버전의 키를 만드는 경우 새 버전을 사용 하도록 저장소 계정을 업데이트 해야 합니다. 먼저 [AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) 를 호출 하 여 최신 버전의 키를 가져옵니다. 그런 다음 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 하 여 이전 섹션에 표시 된 대로 새 버전의 키를 사용 하도록 저장소 계정의 암호화 설정을 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

- [휴지 상태의 데이터에 대 한 암호화 Azure Storage](storage-service-encryption.md)
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)이란?
