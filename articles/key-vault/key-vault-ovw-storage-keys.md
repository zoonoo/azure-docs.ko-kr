---
title: Azure Key Vault 및 Azure CLI를 사용 하 여 저장소 계정 키 관리
description: Storage 계정 키는 Azure storage 계정에 대 한 Azure Key Vault 및 키 기반 액세스 간의 원활한 통합을 제공 합니다.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 251a7c21b671052a23f6ee18cb4278737464b25c
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744866"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Azure Key Vault 및 Azure CLI를 사용 하 여 저장소 계정 키 관리 

Azure Key Vault는 Azure storage 계정 및 클래식 저장소 계정에 대 한 키를 관리 합니다. Key Vault 관리 저장소 계정 기능을 사용 하 여 여러 키 관리 기능을 완성할 수 있습니다.

[Azure Storage 계정](/azure/storage/storage-create-storage-account)은 계정 이름과 키로 구성된 자격 증명을 사용합니다. 키가 자동으로 생성 되 고 암호화 키가 아닌 암호로 사용 됩니다. Key Vault 저장소 계정 키를 [Key Vault 비밀](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)으로 저장 하 여 관리 합니다. 키가 Azure storage 계정에 나열 (동기화) 되 고 주기적으로 다시 생성 되거나 _회전_됩니다. 

관리 되는 저장소 계정 키 기능을 사용 하는 경우 다음 사항을 고려 하세요.

- 호출자에 대 한 응답으로 키 값이 반환 되지 않습니다.
- Key Vault만 저장소 계정 키를 관리 해야 합니다. 키를 직접 관리 하지 않고 Key Vault 프로세스를 방해 하지 않습니다.
- 단일 Key Vault 개체만 저장소 계정 키를 관리 해야 합니다. 여러 개체의 키 관리를 허용 하지 않습니다.
- 사용자 계정을 사용 하 여 저장소 계정을 관리 하는 Key Vault를 요청 하 고 서비스 사용자는 사용할 수 없습니다.
- Key Vault만 사용 하 여 키를 다시 생성 합니다. 저장소 계정 키를 수동으로 다시 생성 하지 마세요. 

> [!NOTE]
> Azure Active Directory (Azure AD)와 Azure Storage 통합은 Microsoft의 클라우드 기반 id 및 액세스 관리 서비스입니다.
> Azure AD 통합은 [azure blob 및 큐](../storage/common/storage-auth-aad.md)에서 사용할 수 있습니다.
> 인증 및 권한 부여에 Azure AD를 사용 합니다.
> Azure AD는 Azure Key Vault와 마찬가지로 Azure Storage에 OAuth2 토큰 기반 액세스를 제공 합니다.
>
> Azure AD를 사용 하면 저장소 계정 자격 증명 대신 응용 프로그램 또는 사용자 id를 사용 하 여 클라이언트 응용 프로그램을 인증할 수 있습니다.
> Azure에서 실행할 때 [AZURE AD 관리 id](/azure/active-directory/managed-identities-azure-resources/) 를 사용할 수 있습니다. 관리 되는 id는 클라이언트 인증에 대 한 필요성을 제거 하 고 또는 응용 프로그램에 자격 증명을 저장 합니다.
> Azure AD는 RBAC (역할 기반 액세스 제어)를 사용 하 여 권한 부여를 관리 합니다 .이는 Key Vault 에서도 지원 됩니다.

### <a name="service-principal-application-id"></a>서비스 사용자 응용 프로그램 ID

Azure AD 테 넌 트는 등록 된 각 응용 프로그램을 [서비스 사용자](/azure/active-directory/develop/developer-glossary#service-principal-object)에 게 제공 합니다. 서비스 주체는 응용 프로그램 id (ID)로 사용 됩니다. 응용 프로그램 ID는 RBAC를 통해 다른 Azure 리소스에 액세스 하기 위해 권한 부여를 설정 하는 동안 사용 됩니다.

Key Vault은 모든 Azure AD 테 넌 트에서 미리 등록 된 Microsoft 응용 프로그램입니다. Key Vault는 동일한 응용 프로그램 ID 및 각 Azure 클라우드 내에 등록 됩니다.

| 테 넌 트 | 클라우드 | 애플리케이션 ID |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 공용 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 기타  | 임의의 값 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>전제 조건

Key Vault를 사용 하 여 저장소 계정 키를 관리 하기 전에 필수 구성 요소를 검토 합니다.

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 설치합니다.
- [Azure storage 계정을](https://azure.microsoft.com/services/storage/)만듭니다. [다음 단계](../storage/index.yml)를 수행 합니다.
- 저장소 계정 이름에는 소문자와 숫자만 사용 해야 합니다. 이름의 길이는 3 자에서 24 자 사이 여야 합니다.        
      
## <a name="manage-storage-account-keys"></a>저장소 계정 키 관리

Key Vault를 사용 하 여 저장소 계정 키를 관리 하는 네 가지 기본 단계가 있습니다.

1. 기존 저장소 계정을 가져옵니다.
1. 기존 key vault를 인출 합니다.
1. 자격 증명 모음에 Key Vault 관리 저장소 계정을 추가 합니다. 다시 `key1` 생성 기간이 90 일인 활성 키로 설정 합니다.
1. 지정 `key1` 된 저장소 계정에 대 한 저장소 컨텍스트를 설정 하는 데 사용 합니다.

> [!NOTE]
> Key Vault 서비스에는 저장소 계정에 대 한 운영자 권한이 할당 됩니다.
> 
> Azure Key Vault 관리 저장소 계정 키를 설정한 후 Key Vault를 사용 하 여 키만 변경 합니다.
> 관리 되는 저장소 계정 키의 경우 Key Vault는 저장소 계정 키의 회전을 관리 합니다.

1. 저장소 계정을 만든 후 다음 명령을 실행 하 여 관리할 저장소 계정의 리소스 ID를 가져옵니다.
    ```
    az storage account show -n storageaccountname
    ```

    명령 출력에서 리소스 ID 값을 복사 합니다.
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    예제 출력:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Key Vault에 "저장소 계정 키 운영자 서비스 역할" RBAC 역할을 할당 합니다. 이 역할은 저장소 계정에 대 한 액세스 범위를 제한 합니다. 클래식 저장소 계정의 경우 "클래식 저장소 계정 키 운영자 서비스 역할" 역할을 사용 합니다.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`는 Azure 공용 클라우드에서 Key Vault에 대 한 개체 ID입니다. Azure Government 클라우드에서 Key Vault에 대 한 개체 ID를 가져오려면 [서비스 사용자 응용 프로그램 id](#service-principal-application-id)를 참조 하세요.
    
1. Key Vault 관리 저장소 계정 만들기:

    90 일의 다시 생성 기간을 설정 합니다. 90 일 후 Key Vault가 다시 `key1` 생성 되 고 활성 키를 `key2` 에서 `key1`로 바꿉니다. `key1`가 활성 키로 표시 됩니다. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>토큰 만들기 및 생성

Key Vault를 요청 하 여 공유 액세스 서명 토큰을 생성할 수도 있습니다. 공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 계정 키를 공유 하지 않고 저장소 계정의 리소스에 대 한 액세스 권한을 클라이언트에 부여할 수 있습니다. 공유 액세스 서명은 계정 키를 손상 시 키 지 않고 저장소 리소스를 공유 하는 안전한 방법을 제공 합니다.

이 섹션의 명령은 다음 작업을 완료 합니다.

- 계정 공유 액세스 서명 정의 `<YourSASDefinitionName>`를 설정 합니다. 이 정의는 키 자격 증명 모음 `<YourStorageAccountName>` `<VaultName>`에서 관리 되는 Key Vault 저장소 계정에 설정 됩니다.
- Blob, 파일, 테이블 및 큐 서비스에 대 한 계정 공유 액세스 서명 토큰을 만듭니다. 토큰은 리소스 유형 서비스, 컨테이너 및 개체에 대해 생성 됩니다. 토큰은 지정 된 시작 및 종료 날짜를 사용 하 여 https를 통해 모든 사용 권한으로 만들어집니다.
- 자격 증명 모음에서 관리 되는 저장소 공유 액세스 서명 정의를 Key Vault 설정 합니다. 정의에는 만들어진 공유 액세스 서명 토큰의 템플릿 URI가 있습니다. 정의는 공유 액세스 서명 유형이 `account` 며 N 일 동안 유효 합니다.
- 공유 액세스 서명 정의에 해당 하는 Key Vault 암호에서 실제 액세스 토큰을 검색 합니다.

이전 섹션의 단계를 완료 한 후 다음 명령을 실행 하 Key Vault 하 여 공유 액세스 서명 토큰을 생성 하도록 요청 합니다. 

1. 공유 액세스 서명 정의를 만듭니다. 공유 액세스 서명 정의를 만든 후에는 Key Vault를 요청 하 여 더 많은 공유 액세스 서명 토큰을 생성 합니다. 이 작업 `storage` 을 수행 하려면 `setsas` 및 권한이 필요 합니다.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    작업에 대 한 도움말은 [az storage account generate sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) reference 설명서를 참조 하세요.

    작업이 성공적으로 실행 되 면 출력을 복사 합니다.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. 이전 명령 `$sasToken` 에 의해 생성 된를 사용 하 고 공유 액세스 서명 정의를 만듭니다. 명령 매개 변수에 대 한 자세한 내용은 [az keyvault 저장소 sas-정의 참조 만들기](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) 설명서를 참조 하세요.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    사용자에 게 저장소 계정에 대 한 권한이 없으면 먼저 사용자의 개체 ID를 가져옵니다.
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>코드에서 토큰 페치

Key Vault에서 [공유 액세스 서명 토큰](../storage/common/storage-dotnet-shared-access-signature-part-1.md) 을 가져와 저장소 계정에 대 한 작업을 실행 합니다.

Key Vault에 인증 하는 방법에는 다음 세 가지가 있습니다.

- 관리 서비스 id를 사용 합니다. 이 방법은 매우 권장 됩니다.
- 서비스 주체 및 인증서를 사용 합니다. 
- 서비스 사용자 및 암호를 사용 합니다. 이 방법은 사용 하지 않는 것이 좋습니다.

자세한 내용은 Azure Key Vault를 참조 [하세요. 기본 개념](key-vault-whatis.md#basic-concepts).

다음 예제에서는 공유 액세스 서명 토큰을 가져오는 방법을 보여 줍니다. 공유 액세스 서명 정의를 만든 후에 토큰을 페치합니다. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

공유 액세스 서명 토큰이 만료 될 예정 이면 Key Vault에서 공유 액세스 서명 토큰을 다시 가져오고 코드를 업데이트 합니다.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure CLI 명령

관리 되는 저장소 계정과 관련 된 Azure CLI 명령에 대 한 자세한 내용은 [az keyvault 저장소](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) 참조 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [키, 암호 및 인증서](https://docs.microsoft.com/rest/api/keyvault/)에 대해 자세히 알아보세요.
- [Azure Key Vault 팀 블로그에서](https://blogs.technet.microsoft.com/kv/)문서를 검토 합니다.
