---
title: Azure Key Vault 관리 스토리지 계정 - PowerShell 버전
description: 관리되는 저장소 계정 기능은 Azure Key Vault와 Azure 저장소 계정 간에 원활한 통합을 제공합니다.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: f8c526148e37ba1b716aafd32dcc3f242358f1eb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427784"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>키 볼트 및 Azure PowerShell을 통해 저장소 계정 키 관리

Azure 저장소 계정은 계정 이름과 키로 구성된 자격 증명을 사용합니다. 키는 자동으로 생성되며 암호가 아닌 암호로 제공됩니다. 키 볼트는 저장소 계정 키를 키 볼트 비밀로 저장하여 관리합니다. 

Key Vault 관리형 저장소 계정 키 기능을 사용하여 Azure 저장소 계정과 키를 나열(동기화)하고 주기적으로 키를 다시 생성(회전)할 수 있습니다. 저장소 계정과 클래식 저장소 계정 모두에 대한 키를 관리할 수 있습니다.

관리 저장소 계정 키 기능을 사용하는 경우 다음 사항을 고려하십시오.

- 키 값은 호출자에 대한 응답으로 반환되지 않습니다.
- 키 볼트만 저장소 계정 키를 관리해야 합니다. 키를 직접 관리하지 말고 Key Vault 프로세스를 방해하지 마십시오.
- 키 Vault 개체는 저장소 계정 키를 관리해야 합니다. 여러 개체에서 키 관리를 허용하지 마십시오.
- Key Vault에서 서비스 주체가 아닌 사용자 주체를 사용하여 저장소 계정을 관리하도록 요청할 수 있습니다.
- 키 볼트만 사용하여 키를 다시 생성합니다. 스토리지 계정 키를 수동으로 다시 생성하지 않습니다.

Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스인 Azure Active Directory(Azure AD)와 Azure 저장소 통합을 사용하는 것이 좋습니다. Azure AD 통합은 [Azure Blob 및 큐에](../../storage/common/storage-auth-aad.md)사용할 수 있으며 Azure 키 볼트와 마찬가지로 Azure 저장소에 대한 OAuth2 토큰 기반 액세스를 제공합니다.

Azure AD를 사용하면 저장소 계정 자격 증명 대신 응용 프로그램 또는 사용자 ID를 사용하여 클라이언트 응용 프로그램을 인증할 수 있습니다. Azure에서 실행할 때 [Azure AD 관리 ID를](/azure/active-directory/managed-identities-azure-resources/) 사용할 수 있습니다. 관리되는 ID는 클라이언트 인증및 응용 프로그램 또는 응용 프로그램과 함께 자격 증명을 저장할 필요가 없습니다.

Azure AD는 역할 기반 액세스 제어(RBAC)를 사용하여 키 볼트에서 지원하는 권한 부여를 관리합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>서비스 주체 응용 프로그램 ID

Azure AD 테넌트는 등록된 각 응용 프로그램에 [서비스 주체를](/azure/active-directory/develop/developer-glossary#service-principal-object)제공합니다. 서비스 주체는 RBAC를 통해 다른 Azure 리소스에 액세스하기 위해 권한 부여 설정 중에 사용되는 응용 프로그램 ID역할을 합니다.

키 볼트는 모든 Azure AD 테넌트의 사전 등록된 Microsoft 응용 프로그램입니다. 키 볼트는 각 Azure 클라우드에서 동일한 응용 프로그램 ID로 등록됩니다.

| 테넌트 | 클라우드 | 애플리케이션 UI |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 공용 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 기타  | 모두 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>사전 요구 사항

이 가이드를 완료하려면 먼저 다음을 수행해야 합니다.

- [Azure PowerShell 모듈을 설치합니다.](/powershell/azure/install-az-ps?view=azps-2.6.0)
- [키 자격 증명 모음 만들기](quick-create-powershell.md)
- [Azure 저장소 계정을 만듭니다.](../../storage/common/storage-account-create.md?tabs=azure-powershell) 저장소 계정 이름은 소문자와 숫자만 사용해야 합니다. 이름의 길이는 3자에서 24자 사이여야 합니다.
      

## <a name="manage-storage-account-keys"></a>저장소 계정 키 관리

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet을 사용하여 PowerShell 세션을 인증합니다. 

```azurepowershell-interactive
Connect-AzAccount
```
여러 Azure 구독이 있는 경우 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) cmdlet을 사용하여 나열하고 [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet에서 사용할 구독을 지정할 수 있습니다. 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>변수 설정

먼저 다음 단계에서 PowerShell cmdlet에서 사용할 변수를 설정합니다. <YourResourceGroupName>에 <YourStorageAccountName>대해 및 자리 <YourKeyVaultName> 표시자를 업데이트하고 $keyVaultSpAppId `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` 설정해야 [합니다(위의 서비스 주체 응용 프로그램 ID에](#service-principal-application-id)지정된 대로).

또한 Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) 및 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) cmdlet을 사용하여 사용자 ID와 Azure 저장소 계정의 컨텍스트를 가져옵니다.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>저장소 계정에 키 볼트 액세스 권한 부여

Key Vault가 스토리지 계정 키에 액세스하여 관리하려면 사용자가 스토리지 계정에 액세스 권한을 부여해야 합니다. Key Vault 애플리케이션은 스토리지 계정의 키를 *나열*하고 *다시 생성*할 수 있는 권한이 필요합니다. 이러한 권한은 기본 제공된 RBAC 역할 [스토리지 계정 키 운영자 서비스 역할](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)을 통해 사용 설정되어 있습니다. 

Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) cmdlet을 사용하여 저장소 계정으로 범위를 제한하는 키 볼트 서비스 주체에 이 역할을 할당합니다.

```azurepowershell-interactive
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

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>관리 스토리지 계정에 사용자 계정 권한 부여

Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) cmdlet을 사용하여 키 볼트 액세스 정책을 업데이트하고 사용자 계정에 저장소 계정 권한을 부여합니다.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

스토리지 계정에 대한 권한은 Azure Portal의 스토리지 계정 “액세스 정책” 페이지에서 제공되지 않습니다.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Key Vault 인스턴스에 관리 스토리지 계정 추가

Azure PowerShell [추가-AzKeyVault관리저장소 계정](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet을 사용하여 키 볼트 인스턴스에서 관리되는 저장소 계정을 만듭니다. `-DisableAutoRegenerateKey` 스위치는 스토리지 계정 키를 다시 생성하지 않도록 지정합니다.

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

키 볼트가 저장소 계정 키를 주기적으로 재생성하도록 하려면 Azure PowerShell [Add-AzKeyVaultStorageStorageStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet을 사용하여 재생성 기간을 설정할 수 있습니다. 이 예제에서는 재생 기간을 3일로 설정합니다. 3일 후 키 볼트는 'key2'를 재생성하고 활성 키를 'key2'에서 'key1'로 바꿉습니다.

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

## <a name="shared-access-signature-tokens"></a>공유 액세스 서명 토큰

Key Vault에 공유 액세스 서명 토큰을 생성하도록 요청할 수도 있습니다. 공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 계정 키를 공유하지 않고 클라이언트에게 저장소 계정의 리소스에 대한 액세스 권한을 부여할 수 있습니다. 공유 액세스 서명은 계정 키를 손상시키지 않고 저장소 리소스를 안전하게 공유할 수 있는 방법을 제공합니다.

이 섹션의 명령은 다음 작업을 완료합니다.

- 계정 공유 액세스 서명 정의를 설정합니다. 
- Blob, 파일, 테이블 및 큐 서비스에 대한 계정 공유 액세스 서명 토큰을 만듭니다. 토큰은 리소스 유형 서비스, 컨테이너 및 개체에 대해 만들어집니다. 토큰은 모든 권한, https 를 초과하며 지정된 시작 및 종료 날짜로 만들어집니다.
- 볼트에서 키 볼트 관리 스토리지 공유 액세스 서명 정의를 설정합니다. 정의에는 생성된 공유 액세스 서명 토큰의 템플릿 URI가 있습니다. 정의에는 공유 액세스 서명 `account` 유형이 있으며 N 일 동안 유효합니다.
- 공유 액세스 서명이 키 자격 증명 모음에 비밀로 저장되었는지 확인합니다.
- 
### <a name="set-variables"></a>변수 설정

먼저 다음 단계에서 PowerShell cmdlet에서 사용할 변수를 설정합니다. <YourStorageAccountName> 및 <YourKeyVaultName> 자리 표시자를 업데이트해야 합니다.

또한 Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) cmdlet을 사용하여 Azure 저장소 계정의 컨텍스트를 가져옵니다.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>공유 액세스 서명 토큰 만들기

Azure PowerShell [New-AzStorageAccountSToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) cmdlet을 사용하여 공유 액세스 서명 정의를 만듭니다.
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
$sasToken 값은 이와 비슷합니다.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>공유 액세스 서명 정의 생성

Azure PowerShell [집합-AzKeyVault관리저장저장사정의](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) cmdlet을 사용하여 공유 액세스 서명 정의를 만듭니다.  선택한 이름을 매개 변수에 `-Name` 제공할 수 있습니다.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>공유 액세스 서명 정의 확인

Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) cmdlet을 사용하여 공유 액세스 서명 정의가 키 자격 증명 모음에 저장되었는지 확인할 수 있습니다.

먼저 키 자격 증명 모음에서 공유 액세스 서명 정의를 찾습니다.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

SAS 정의에 해당하는 비밀에는 다음과 같은 속성이 있습니다.

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

이제 [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) cmdlet 및 비밀 `Name` 속성을 사용하여 해당 비밀의 내용을 볼 수 있습니다.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

이 명령의 출력에는 SAS 정의 문자열이 표시됩니다.


## <a name="next-steps"></a>다음 단계

- [관리 스토리지 계정 키 예제](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell 참조](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
