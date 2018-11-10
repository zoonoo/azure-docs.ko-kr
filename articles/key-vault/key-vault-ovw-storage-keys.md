---
ms.assetid: ''
title: Azure Key Vault Storage 계정 키
description: 저장소 계정 키는 Azure Key Vault 간의 원활한 통합과 Azure Storage 계정에 대한 키 기반 액세스를 제공합니다.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 02fffe7c4a3acff6ce6d68046eee4286003b1766
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232225"
---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault Storage 계정 키

> [!NOTE]
> [Azure Storage는 이제 AAD 권한 부여를 지원합니다](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). 사용자가 해당 저장소 계정 키 회전에 대해 걱정할 필요가 없으므로 저장소에 대한 인증 및 권한 부여에 Azure Active Directory를 사용하는 것이 좋습니다.

- Azure Key Vault는 ASA(Azure Storage Account)의 키를 관리합니다.
    - 내부적으로 Azure Key Vault는 키와 Azure Storage 계정을 나열(동기화)할 수 있습니다.    
    - Azure Key Vault는 정기적으로 키를 다시 생성(회전)합니다.
    - 키 값은 호출자에게 응답으로 반환되지 않습니다.
    - Azure Key Vault는 Storage 계정과 클래식 Storage 계정 둘 다의 키를 관리합니다.

<a name="prerequisites"></a>필수 조건
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Azure CLI 설치   
2. [저장소 계정 만들기](https://azure.microsoft.com/services/storage/)
    - 이 [문서](https://docs.microsoft.com/azure/storage/)의 단계에 따라 저장소 계정을 만듭니다.  
    - **명명 지침:** Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.        
      
<a name="step-by-step-instructions"></a>단계별 지침
-------------------------
아래 지침에서는 저장소 계정에 대한 운영자 권한이 있는 서비스로 Key Vault를 할당합니다.

1. 저장소 계정을 만든 후 다음 명령을 실행하여 관리하려는 저장소 계정의 리소스 ID를 가져옵니다.

    ```
    az storage account show -n storageaccountname (Copy ID out of the result of this command)
    ```
    
2. Azure Key Vault의 서비스 주체에 대한 응용 프로그램 ID를 가져옵니다. 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Azure Key Vault ID에 저장소 키 운영자 역할 할당

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id hhjkh --scope idofthestorageaccount
    ```
    
4. Key Vault 관리 저장소 계정을 만듭니다.     <br /><br />
   아래에서 다시 생성 기간을 90일로 설정합니다. 90일 후에 Key Vault는 ‘key1’을 다시 생성하고 활성 키를 ‘key1’에서 ‘key2’로 교체합니다.
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    사용자가 저장소 계정을 만들지 않고 저장소 계정에 대한 사용 권한이 없을 경우 아래 단계는 Key Vault에서 모든 저장소 사용 권한을 관리할 수 있는지 확인하기 위해 사용자 계정에 대한 사용 권한을 설정합니다.
 > [!NOTE] 
    사용자에게 저장소 계정에 대한 권한이 없는 경우 먼저 사용자의 개체 ID를 가져옵니다.

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

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
