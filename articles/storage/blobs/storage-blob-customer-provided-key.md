---
title: .NET-Azure Storage를 사용 하 여 Blob 저장소에 대 한 요청에 고객이 제공한 키를 지정 합니다.
description: .NET을 사용 하 여 Blob 저장소에 대 한 요청에 고객이 제공한 키를 지정 하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1c282b1b9a4693da2aa71831a503d443660b65c1
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666638"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>.NET을 사용 하 여 Blob 저장소에 대 한 요청에 고객이 제공한 키를 지정 합니다.

Azure Blob 저장소에 대 한 요청을 수행 하는 클라이언트에는 개별 요청에 대 한 암호화 키를 제공 하는 옵션이 있습니다. 요청에 암호화 키를 포함 하면 Blob 저장소 작업의 암호화 설정에 대 한 세부적인 제어 기능을 제공 합니다. 고객이 제공한 키 (미리 보기)는 Azure Key Vault 또는 다른 키 저장소에 저장할 수 있습니다.

이 문서에서는 .NET을 사용 하 여 요청 시 고객이 제공한 키를 지정 하는 방법을 보여 줍니다.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>예: 고객이 제공한 키를 사용 하 여 blob 업로드

다음 예에서는 고객이 제공한 키를 만들고 해당 키를 사용 하 여 blob을 업로드 합니다. 이 코드는 블록을 업로드 한 다음 Azure Storage에 대 한 blob을 기록 하는 블록 목록을 커밋합니다.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

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

- [휴지 상태의 데이터에 대 한 암호화 Azure Storage](../common/storage-service-encryption.md)
- [Azure 리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](../common/storage-auth-aad-msi.md)
