---
title: .NET-Azure Storage를 사용 하 여 Blob 저장소에 대 한 요청에 고객이 제공한 키를 지정 합니다.
description: .NET을 사용 하 여 Blob 저장소에 대 한 요청에 고객이 제공한 키를 지정 하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693511"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>.NET을 사용 하 여 Blob 저장소에 대 한 요청에 고객이 제공한 키를 지정 합니다.

Azure Blob 저장소에 대 한 요청을 수행 하는 클라이언트에는 개별 요청에 대 한 AES-256 암호화 키를 제공 하는 옵션이 있습니다. 요청에 암호화 키를 포함 하면 Blob 저장소 작업의 암호화 설정에 대 한 세부적인 제어 기능을 제공 합니다. 고객이 제공한 키는 Azure Key Vault 또는 다른 키 저장소에 저장할 수 있습니다.

이 문서에서는 .NET을 사용 하 여 요청 시 고객이 제공한 키를 지정 하는 방법을 보여 줍니다.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Azure Id 클라이언트 라이브러리를 사용 하 여 인증 하는 방법에 대 한 자세한 내용은 [.net 용 Azure id 클라이언트 라이브러리](/dotnet/api/overview/azure/identity-readme)를 참조 하세요.

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>고객이 제공한 키를 사용 하 여 blob에 쓰기

다음 예제에서는 Blob 저장소 용 v12 클라이언트 라이브러리를 사용 하 여 blob을 업로드할 때 AES-256 키를 제공 합니다. 이 예제에서는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 개체를 사용 하 여 Azure AD에서 쓰기 요청에 권한을 부여 하지만 공유 키 자격 증명을 사용 하 여 요청에 권한을 부여할 수도 있습니다.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>다음 단계

- [Blob 저장소에 대 한 요청에 암호화 키 제공](encryption-customer-provided-keys.md)
- [미사용 데이터에 대한 Azure Storage 암호화](../common/storage-service-encryption.md)
