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
ms.openlocfilehash: cbd7bd034c5cbbdf5308ec660a96dc52a9a2b310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78200705"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>코드에서 공유 액세스 서명 토큰 가져오기

키 자격 증명 모음에서 [공유 액세스 서명 토큰으로](../storage/common/storage-dotnet-shared-access-signature-part-1.md) 저장소 계정을 관리할 수 있습니다. 이 문서에서는 SAS 토큰을 가져오고 함께 작업을 수행하는 C# 코드의 예제를 제공합니다.  SAS 토큰을 만들고 저장하는 방법에 대한 자세한 내용은 [키 자격 증명 모음 및 Azure CLI를 사용하여 저장소 계정 키 관리를](key-vault-ovw-storage-keys.md) 참조하거나 키 자격 증명 모음 및 Azure [PowerShell을 사용하여 저장소 계정 키 관리를 참조하십시오.](key-vault-overview-storage-keys-powershell.md)

## <a name="code-samples"></a>코드 샘플

이 예제에서 코드는 키 자격 증명 모음에서 SAS 토큰을 가져오고 이를 사용하여 새 저장소 계정을 만들고 새 Blob 서비스 클라이언트를 만듭니다.  

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

공유 액세스 서명 토큰이 곧 만료되는 경우 키 자격 증명 모음에서 공유 액세스 서명 토큰을 가져오고 코드를 업데이트할 수 있습니다.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>다음 단계
- [키 자격 증명 모음 및 Azure CLI](key-vault-ovw-storage-keys.md) 또는 Azure [PowerShell을](key-vault-overview-storage-keys-powershell.md)사용하여 저장소 계정 키를 관리하는 방법에 대해 알아봅니다.
- [관리형 저장소 계정 주요 샘플](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) 참조
- [키, 비밀 및 인증서 에 대한](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell 참조](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
