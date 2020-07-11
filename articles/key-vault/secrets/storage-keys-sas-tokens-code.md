---
title: 코드에서 공유 액세스 서명 토큰 가져오기 | Azure Key Vault
description: 관리 되는 storage 계정 기능은 Azure Key Vault와 Azure storage 계정 간에 원활한 통합을 제공 합니다.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: e429115ce2624685c413ae252229964feee70137
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232596"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>코드에서 공유 액세스 서명 토큰 가져오기

키 자격 증명 모음에 저장 된 SAS (공유 액세스 서명) 토큰을 사용 하 여 저장소 계정을 관리할 수 있습니다. 자세한 내용은 [SAS를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](../../storage/common/storage-sas-overview.md)를 참조 하세요.

이 문서에서는 SAS 토큰을 가져오고이를 사용 하 여 작업을 수행 하는 .NET 코드의 예제를 제공 합니다. SAS 토큰을 만들고 저장 하는 방법에 대 한 자세한 내용은 [Key Vault를 사용 하 여 저장소 계정 키 관리 및 Azure CLI](overview-storage-keys.md) 또는 [Key Vault 및 Azure PowerShell를 사용 하 여 저장소 계정 키 관리](overview-storage-keys-powershell.md)를 참조 하세요.

## <a name="code-samples"></a>코드 샘플

이 예제에서 코드는 키 자격 증명 모음에서 SAS 토큰을 인출 하 고,이 토큰을 사용 하 여 새 저장소 계정을 만들고, 새 Blob service 클라이언트를 만듭니다.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

공유 액세스 서명 토큰이 만료 될 예정 이면 키 자격 증명 모음에서 공유 액세스 서명 토큰을 인출 하 고 코드를 업데이트할 수 있습니다.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>다음 단계
- [SAS를 사용 하 여 Azure Storage 리소스에 대해 제한 된 액세스 권한을 부여](../../storage/common/storage-sas-overview.md)하는 방법을 알아봅니다.
- [Key Vault 및 Azure CLI 또는 Azure PowerShell를 사용 하 여 저장소 계정 키](overview-storage-keys.md) 를 [Azure PowerShell](overview-storage-keys-powershell.md)관리 하는 방법에 대해 알아봅니다.
- [관리 되는 저장소 계정 키 샘플](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) 을 참조 하세요.
