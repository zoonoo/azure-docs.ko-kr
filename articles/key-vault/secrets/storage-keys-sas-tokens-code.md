---
title: Azure Key Vault 관리 스토리지 계정 - PowerShell 버전
description: 관리 되는 storage 계정 기능은 Azure Key Vault와 Azure storage 계정 간에 원활한 통합을 제공 합니다.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 7307741e56c7fc912f60d0496979243eb4be77a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431268"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>코드에서 공유 액세스 서명 토큰 가져오기

키 자격 증명 모음에서 [공유 액세스 서명 토큰](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) 을 사용 하 여 저장소 계정을 관리할 수 있습니다. 이 문서에서는 SAS 토큰을 가져오고이를 사용 하 여 작업을 수행 하는 c # 코드의 예제를 제공 합니다.  SAS 토큰을 만들고 저장 하는 방법에 대 한 자세한 내용은 [Key Vault를 사용 하 여 저장소 계정 키 관리 및 Azure CLI](overview-storage-keys.md) 또는 [Key Vault 및 Azure PowerShell를 사용 하 여 저장소 계정 키 관리](overview-storage-keys-powershell.md)를 참조 하세요.

## <a name="code-samples"></a>코드 샘플

이 예제에서 코드는 키 자격 증명 모음에서 SAS 토큰을 인출 하 고,이 토큰을 사용 하 여 새 저장소 계정을 만들고, 새 Blob service 클라이언트를 만듭니다.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

공유 액세스 서명 토큰이 만료 될 예정 이면 키 자격 증명 모음에서 공유 액세스 서명 토큰을 인출 하 고 코드를 업데이트할 수 있습니다.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>다음 단계
- [Key Vault 및 Azure CLI 또는 Azure PowerShell를 사용 하 여 저장소 계정 키](overview-storage-keys.md) 를 [Azure PowerShell](overview-storage-keys-powershell.md)관리 하는 방법에 대해 알아봅니다.
- [관리 되는 저장소 계정 키 샘플](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) 을 참조 하세요.
- [Key Vault PowerShell 참조](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
