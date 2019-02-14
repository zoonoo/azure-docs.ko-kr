---
ms.assetid: ''
title: Azure Key Vault 관리 스토리지 계정 - CLI
description: 스토리지 계정 키는 Azure Key Vault 간의 원활한 통합과 Azure Storage 계정에 대한 키 기반 액세스를 제공합니다.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 152e1e5892e3a72286205c2f5bf4e18b2a2bcbf7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814846"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Azure Key Vault 관리 스토리지 계정 - CLI

> [!NOTE]
> [Azure Active Directory(Azure AD)와 Azure Storage 통합은 현재 미리 보기 중입니다](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). 인증 및 권한 부여에 Azure Key Vault뿐만 아니라, Azure Storage에 대한 OAuth2 토큰 기반 액세스를 제공하는 Azure AD를 사용하는 것이 좋습니다. 이를 통해 다음을 수행할 수 있습니다.
> - 스토리지 계정 자격 증명 대신, 애플리케이션 또는 사용자 ID를 사용하여 클라이언트 애플리케이션을 인증합니다. 
> - Azure 기반 실행 시 [Azure AD 관리 ID](/azure/active-directory/managed-identities-azure-resources/)를 사용합니다. 관리 ID를 통해 클라이언트 인증은 물론, 애플리케이션에 또는 애플리케이션을 통해 자격 증명을 저장할 필요성이 없어집니다.
> - 권한 부여 관리에 Key Vault에서도 지원되는 역할 기반 제어(RBAC)를 사용합니다.

- Azure Key Vault는 ASA(Azure Storage Account)의 키를 관리합니다.
    - 내부적으로 Azure Key Vault는 키와 Azure Storage 계정을 나열(동기화)할 수 있습니다.    
    - Azure Key Vault는 정기적으로 키를 다시 생성(회전)합니다.
    - 키 값은 호출자에게 응답으로 반환되지 않습니다.
    - Azure Key Vault는 Storage 계정과 클래식 Storage 계정 둘 다의 키를 관리합니다.
    
> [!IMPORTANT]
> Azure AD 테넌트는 등록된 각 애플리케이션에 애플리케이션의 ID 역할을 하는 **[서비스 주체](/azure/active-directory/develop/developer-glossary#service-principal-object)** 를 제공합니다. 서비스 주체의 애플리케이션 ID는 RBAC(역할 기반 액세스 제어)를 통해 다른 Azure 리소스에 액세스할 수 있는 권한을 부여할 때 사용됩니다. Key Vault는 Microsoft 애플리케이션이므로 각 Azure 클라우드 내에서 동일한 애플리케이션 ID에 속한 모든 Azure AD 테넌트에 사전 등록되어 있습니다.
> - Azure Government 클라우드의 Azure AD 테넌트는 애플리케이션 ID `7e7c393b-45d0-48b1-a35e-2905ddf8183c`를 사용합니다.
> - Azure 공용 클라우드 및 그 외 모든 클라우드의 Azure AD 테넌트는 애플리케이션 ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`을 사용합니다.

<a name="prerequisites"></a>필수 조건
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Azure CLI 설치   
2. [저장소 계정 만들기](https://azure.microsoft.com/services/storage/)
    - 이 [문서](https://docs.microsoft.com/azure/storage/)의 단계에 따라 저장소 계정을 만듭니다.  
    - **명명 지침:** Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Key Vault를 사용하여 스토리지 계정 키를 관리하는 방법에 대한 단계별 지침
--------------------------------------------------------------------------------
아래 지침에서는 저장소 계정에 대한 운영자 권한이 있는 서비스로 Key Vault를 할당합니다.

> [!NOTE]
> Azure Key Vault 관리 스토리지 계정 키를 설정했으면 Key Vault를 통하는 경우를 제외하고 더 이상 변경하지 **않아야 합니다**. 관리 스토리지 계정 키는 Key Vault가 스토리지 계정 키 회전을 관리한다는 것을 의미합니다.

1. 저장소 계정을 만든 후 다음 명령을 실행하여 관리하려는 저장소 계정의 리소스 ID를 가져옵니다.

    ```
    az storage account show -n storageaccountname 
    ```
    위의 명령의 결과에서 ID 필드 복사
    
2. 아래 명령을 실행하여 Azure Key Vault 서비스 주체의 개체 ID 가져오기

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
    이 명령이 완료되면 결과에서 개체 ID 찾기
    ```console
        {
            ...
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
            ...
        }
    ```
    
3. Azure Key Vault ID에 저장소 키 운영자 역할 할당

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Key Vault 관리 저장소 계정을 만듭니다.     <br /><br />
   아래에서 다시 생성 기간을 90일로 설정합니다. 90일 후에 Key Vault는 ‘key1’을 다시 생성하고 활성 키를 ‘key1’에서 ‘key2’로 교체합니다. Key Vault는 이제 Key1을 활성 키로 표시합니다. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```
    사용자가 저장소 계정을 만들지 않고 저장소 계정에 대한 사용 권한이 없을 경우 아래 단계는 Key Vault에서 모든 저장소 사용 권한을 관리할 수 있는지 확인하기 위해 사용자 계정에 대한 사용 권한을 설정합니다.
    
 > [!NOTE] 
 > 사용자에게 저장소 계정에 대한 권한이 없는 경우 먼저 사용자의 개체 ID를 가져옵니다.


    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    
    ```
    
## <a name="how-to-access-your-storage-account-with-sas-tokens"></a>SAS 토큰을 사용하여 스토리지 계정에 액세스하는 방법

이 섹션에서는 Key Vault에서 [SAS 토큰](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)을 가져와서 스토리지 계정에 대한 작업을 수행하는 방법을 설명합니다.

아래 섹션에서는 Key Vault에 저장된 스토리지 계정 키를 가져와서 스토리지 계정의 SAS(공유 액세스 서명) 정의를 만드는 데 사용하는 방법을 보여줍니다.

> [!NOTE] 
  [기본 개념](key-vault-whatis.md#basic-concepts)에 설명된 것처럼 3가지 방법으로 Key Vault에 인증할 수 있습니다.
> - 관리 서비스 ID 사용(강력 권장)
> - 서비스 주체 및 인증서 사용 
> - 서비스 주체 및 암호 사용(권장하지 않음)

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

SAS 토큰이 곧 만료될 예정이면 Key Vault에서 SAS 토큰을 다시 가져와서 코드를 업데이트합니다.

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


### <a name="relavant-azure-cli-cmdlets"></a>관련 Azure CLI cmdlet
[Azure CLI 스토리지 Cmdlet](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>관련 PowerShell cmdlet

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>참고 항목

- [키, 비밀 및 인증서에 대한 정보](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault 팀 블로그](https://blogs.technet.microsoft.com/kv/)
