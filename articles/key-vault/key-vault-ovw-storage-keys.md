---
ms.assetid: 
title: "Azure Key Vault Storage 계정 키"
description: "저장소 계정 키는 Azure Key Vault 간의 원활한 통합과 Azure Storage 계정에 대한 키 액세스를 제공합니다."
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 07/10/2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b30f9601725cdf568f0f2e18bebdc4ae86a616f6
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault Storage 계정 키

Azure Key Vault Storage 계정 키 이전에는 개발자가 고유한 ASA(Azure Storage 계정) 키를 관리하고 수동으로 또는 외부 자동화를 통해 회전해야 했습니다. 이제 Key Vault Storage 계정 키가 Azure Storage 계정에 인증하기 위한 [Key Vault 암호](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets)로 구현됩니다. 

ASA 키 기능은 자동으로 암호 회전을 관리하고 SAS(공유 액세스 서명)를 방법으로 제공하여 사용자가 ASA 키로 직접 연결할 필요가 없도록 합니다. 

Azure Storage 계정에 대한 일반적인 내용은 [Azure 저장소 계정 정보](https://docs.microsoft.com/azure/storage/storage-create-storage-account)를 참조하세요.

## <a name="supporting-interfaces"></a>인터페이스 지원

Azure Storage 계정 키 기능은 처음에 REST, .NET/C# 및 PowerShell 인터페이스를 통해 제공됩니다. 자세한 내용은 [Key Vault 참조](https://docs.microsoft.com/azure/key-vault/)를 참조하세요.

## <a name="storage-account-keys-behavior"></a>저장소 계정 키 동작

### <a name="what-key-vault-manages"></a>Key Vault에서 관리하는 사항

저장소 계정 키를 사용할 경우 Key Vault에서 사용자 대신 여러 가지 내부 관리 기능을 수행합니다.

1. Azure Key Vault는 SAS(Azure Storage 계정)의 키를 관리합니다. 
    - 내부적으로 Azure Key Vault는 키와 Azure Storage 계정을 나열(동기화)할 수 있습니다.  
    - Azure Key Vault는 정기적으로 키를 다시 생성(회전)합니다. 
    - 키 값은 호출자에게 응답으로 반환되지 않습니다. 
    - Azure Key Vault는 저장소 계정과 클래식 저장소 계정 둘 다의 키를 관리합니다. 
2. Azure Key Vault를 사용하면 자격 증명 모음/개체 소유자가 SAS(계정 또는 서비스 SAS) 정의를 만들 수 있습니다. 
    - SAS 정의를 사용하여 만든 SAS 값은 REST URI 경로를 통해 암호로 반환됩니다. 자세한 내용은 [Azure Key Vault 저장소 계정 작업](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)을 참조하세요.

### <a name="naming-guidance"></a>명명 지침

저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.  
 
SAS 정의 이름은 1-102자로, 0-9, a-z, A-Z만 포함해야 합니다.

## <a name="developer-experience"></a>개발자 환경 

### <a name="before-azure-key-vault-storage-keys"></a>Azure Key Vault Storage 키 이전 

개발자는 Azure 저장소에 액세스하기 위해 저장소 계정 키로 다음 작업을 수행해야 했습니다. 
 
 ```
//create storage account using connection string containing account name 
// and the storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Azure Key Vault Storage 키 이후 

```
//Use PowerShell command to get Secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  
-AccountName msak01 -Name blobsas1 -Protocol HttpsOrHttp -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If SAS token is about to expire then Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);

  ```
 
 ### <a name="developer-best-practices"></a>개발자 모범 사례 

- Key Vault에서만 ASA 키를 관리하도록 허용합니다. 직접 관리할 경우 Key Vault 프로세스를 방해할 수 있으므로 직접 관리하려고 하지 마세요. 
- 둘 이상의 Key Vault 개체가 ASA 키를 관리하도록 허용하지 않습니다. 
- ASA 키를 수동으로 다시 생성해야 하는 경우 Key Vault를 통해 다시 생성하는 것이 좋습니다. 

## <a name="getting-started"></a>시작

### <a name="setup-for-role-based-access-control-permissions"></a>역할 기반 액세스 제어 권한 설정

Key Vault에 저장소 계정의 키를 나열하고 다시 생성할 수 있는 권한이 필요합니다. 다음 단계를 따라 이러한 권한을 설정하세요.

- Key Vault의 ObjectId를 가져옵니다. 

    `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`

- Azure Key Vault ID에 저장소 키 운영자 역할을 할당합니다. 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > 클래식 계정 유형의 경우 역할 매개 변수를 *"클래식 저장소 계정 키 운영자 서비스 역할"*로 설정합니다.

### <a name="storage-account-onboarding"></a>저장소 계정 등록 

#### <a name="example"></a>예제

Key Vault 개체 소유자가 저장소 계정을 등록하기 위해 AzKV에서 저장소 계정 개체를 추가합니다.

등록하는 동안 Key Vault는 계정을 등록하는 ID에 저장소 키를 *나열* 및 *다시 생성*할 수 있는 권한이 있는지 확인해야 합니다. Key Vault는 대상 그룹을 Azure Resource Manager로 지정하여 EvoSTS에서 OBO 토큰을 가져오고 저장소 RP에 대해 키 나열 호출을 수행합니다. 나열 호출이 실패할 경우 Key Vault 개체 생성도 *사용할 수 없음* http 상태 코드로 실패합니다. 이러한 방식으로 나열된 키는 Key Vault 엔터티 저장소로 캐시됩니다. 

Key Vault에서 ID에 *다시 생성* 권한이 있는지 확인해야 키 다시 생성에 대한 소유권을 가질 수 있습니다. OBO 토큰을 통한 ID 및 Key Vault의 첫 번째 파티 ID에 이러한 사용 권한이 있는지 확인하기 위해

- Key Vault는 저장소 계정 리소스에 대한 RBAC 권한을 나열합니다.
- Key Vault는 작업 및 비작업의 정규식 일치를 통해 응답의 유효성을 검사합니다. 

몇 가지 지원 예제: 

- [GitHub 샘플](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167) 예 

OBO 토큰을 통한 ID에 *다시 생성* 권한이 없거나 Key Vault의 첫 번째 파티 ID에 *나열* 또는 *다시 생성* 권한이 없는 경우 등록 요청이 실패하고 적절한 오류 코드 및 메시지가 반환됩니다. 

OBO 토큰은 PowerShell 또는 CLI의 네이티브 클라이언트 응용 프로그램인 첫 번째 파티를 사용하는 경우에만 작동합니다.

## <a name="other-applications"></a>다른 응용 프로그램

- Key Vault 저장소 계정 키를 사용하여 생성된 SAS 토큰은 Azure 저장소 계정에 훨씬 더 제어된 액세스 권한을 부여합니다. 자세한 내용은 [공유 액세스 서명 사용](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [키, 비밀 및 인증서에 대한 정보](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault 팀 블로그](https://blogs.technet.microsoft.com/kv/)

