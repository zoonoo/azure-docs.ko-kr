---
title: Azure Key Vault 관리 스토리지 계정 - PowerShell 버전
description: 관리 스토리지 계정 기능은 Azure Key Vault와 Azure Storage 계정 간에 완벽한 통합을 제공합니다.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 9b6089aa828b5667f100c1a8cbff3e69345e4512
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405100"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Azure Key Vault 관리 스토리지 계정 - PowerShell

> [!NOTE]
> [Azure Active Directory(Azure AD)와 Azure Storage 통합은 현재 미리 보기 중입니다](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). 인증 및 권한 부여에 Azure Key Vault뿐만 아니라, Azure Storage에 대한 OAuth2 토큰 기반 액세스를 제공하는 Azure AD를 사용하는 것이 좋습니다. 이를 통해 다음을 수행할 수 있습니다.
> - 스토리지 계정 자격 증명 대신, 애플리케이션 또는 사용자 ID를 사용하여 클라이언트 애플리케이션을 인증합니다. 
> - Azure 기반 실행 시 [Azure AD 관리 ID](/azure/active-directory/managed-identities-azure-resources/)를 사용합니다. 관리 ID를 통해 클라이언트 인증은 물론, 애플리케이션에 또는 애플리케이션을 통해 자격 증명을 저장할 필요성이 없어집니다.
> - 권한 부여 관리에 Key Vault에서도 지원되는 역할 기반 제어(RBAC)를 사용합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Storage 계정](/azure/storage/storage-create-storage-account)은 계정 이름과 키로 구성된 자격 증명을 사용합니다. 키는 자동 생성되며 암호화 키와는 더 반대되는 "암호" 역할을 합니다. Key Vault는 이러한 키를 [Key Vault 비밀](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)로 저장하여 해당 스토리지 계정 키를 관리할 수 있습니다. 

## <a name="overview"></a>개요

Key Vault 관리 스토리지 계정 기능은 사용자를 대신하여 다양한 관리 기능을 수행합니다.

- Azure Storage 계정과 함께 키를 나열(동기화)합니다.
- 키를 정기적으로 다시 생성(회전)합니다.
- 스토리지 계정과 클래식 스토리지 계정 모두의 키를 관리합니다.
- 키 값은 호출자에게 응답으로 반환되지 않습니다.

관리 스토리지 계정 키 기능을 사용할 경우:

- **Key Vault에서만 스토리지 계정 키를 관리하도록 허용합니다.** 직접 관리할 경우 Key Vault 프로세스를 방해할 수 있으므로 직접 관리하려고 하지 마세요.
- **둘 이상의 Key Vault 개체가 스토리지 계정 키를 관리하도록 허용하지 않습니다**.
- **스토리지 계정 키를 수동으로 다시 생성하지 않습니다**. Key Vault를 통해 다시 생성하는 것이 좋습니다.

다음 예에서는 Key Vault에서 스토리지 계정 키를 관리하도록 허용하는 방법을 보여줍니다.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Key Vault가 스토리지 계정에 액세스하도록 권한 부여

> [!IMPORTANT]
> Azure AD 테넌트는 등록된 각 애플리케이션에 애플리케이션의 ID 역할을 하는 **[서비스 주체](/azure/active-directory/develop/developer-glossary#service-principal-object)** 를 제공합니다. 서비스 주체의 애플리케이션 ID는 RBAC(역할 기반 액세스 제어)를 통해 다른 Azure 리소스에 액세스할 수 있는 권한을 부여할 때 사용됩니다. Key Vault는 Microsoft 애플리케이션이므로 각 Azure 클라우드 내에서 동일한 애플리케이션 ID에 속한 모든 Azure AD 테넌트에 사전 등록되어 있습니다.
> - Azure Government 클라우드의 Azure AD 테넌트는 애플리케이션 ID `7e7c393b-45d0-48b1-a35e-2905ddf8183c`를 사용합니다.
> - Azure 공용 클라우드 및 그 외 모든 클라우드의 Azure AD 테넌트는 애플리케이션 ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`을 사용합니다.

Key Vault가 스토리지 계정 키에 액세스하여 관리하려면 사용자가 스토리지 계정에 액세스 권한을 부여해야 합니다. Key Vault 애플리케이션은 스토리지 계정의 키를 *나열*하고 *다시 생성*할 수 있는 권한이 필요합니다. 이러한 권한은 기본 제공된 RBAC 역할 [스토리지 계정 키 운영자 서비스 역할](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)을 통해 사용 설정되어 있습니다. 

다음 단계를 통해 스토리지 계정으로 범위를 한정하여 이 역할을 Key Vault 서비스 주체에 할당합니다. 스크립트를 실행하기 전에 `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey` 및 `$keyVaultName` 변수를 반드시 업데이트해야 합니다.

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

역할 할당에 성공하면 다음 예제와 비슷한 결과가 나타납니다.

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Key Vault가 이미 스토리지 계정의 역할에 추가되었다면 *"역할 할당이 이미 있습니다."* 오류가 표시됩니다. 또한 Azure Portal에서 스토리지 계정 “액세스 제어(IAM)” 페이지를 사용하여 역할 할당을 확인할 수도 있습니다.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>관리 스토리지 계정에 사용자 계정 권한 부여

>[!TIP] 
> Azure AD가 애플리케이션 ID의 **서비스 주체**에 제공한 것과 마찬가지로, 사용자 ID에는 **사용자 주체**가 제공됩니다. 그런 다음, Key Vault 액세스 정책 권한을 통해 사용자 주체에 Key Vault에 액세스할 수 있는 권한을 부여할 수 있습니다.

동일한 PowerShell 세션을 사용하여 관리 스토리지 계정에 대한 Key Vault 액세스 정책을 업데이트합니다. 이 단계는 스토리지 계정 권한을 사용자 계정에 적용하여 다음과 같은 관리 스토리지 계정 기능에 액세스할 수 있습니다. 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

스토리지 계정에 대한 권한은 Azure Portal의 스토리지 계정 “액세스 정책” 페이지에서 제공되지 않습니다.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Key Vault 인스턴스에 관리 스토리지 계정 추가

동일한 PowerShell 세션을 사용하여 Key Vault 인스턴스에서 관리 스토리지 계정을 만듭니다. `-DisableAutoRegenerateKey` 스위치는 스토리지 계정 키를 다시 생성하지 않도록 지정합니다.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

키가 다시 생성되지 않고 스토리지 계정 추가에 성공하면 다음 예제와 유사한 출력이 표시됩니다.

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>키 다시 생성 사용

Key Vault로 스토리지 계정 키를 주기적으로 다시 생성하려는 경우 다시 생성 기간을 설정할 수 있습니다. 다음 예제에서는 다시 생성 기간을 3일로 설정합니다. 3일 후에 Key Vault는 ‘key1’을 다시 생성하고 활성 키를 ‘key2’에서 ‘key1’으로 교체합니다.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

키가 다시 생성되고 스토리지 계정 추가에 성공하면 다음 예제와 유사한 출력이 표시됩니다.

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>다음 단계

- [관리 스토리지 계정 키 예제](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell 참조](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
