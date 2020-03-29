---
title: .NET - Azure 저장소를 사용하여 Blob 저장소에 대한 요청에 대해 고객이 제공한 키 지정
description: .NET을 사용하여 Blob 저장소에 대한 요청에 대해 고객이 제공한 키를 지정하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807003"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>.NET을 사용하여 Blob 저장소에 대한 요청에 고객이 제공한 키 지정

Azure Blob 저장소에 대해 요청하는 클라이언트는 개별 요청에 대해 암호화 키를 제공할 수 있습니다. 요청에 암호화 키를 포함하면 Blob 저장소 작업에 대한 암호화 설정을 세부적으로 제어할 수 있습니다. 고객이 제공한 키(미리 보기)는 Azure 키 볼트 또는 다른 키 저장소에 저장할 수 있습니다.

이 문서에서는 .NET을 사용하여 요청에 대해 고객이 제공한 키를 지정하는 방법을 보여 주며 있습니다.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Azure Storage에서 Azure Id 클라이언트 라이브러리를 사용하여 인증하는 방법에 대해 자세히 알아보려면 Azure Active [Directory 및 Azure 리소스에 대한 관리되는 ID를 사용하여 Blob 및 큐에 대한 액세스 권한 부여에서](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library) **Azure ID 라이브러리로 인증이라는** 섹션을 참조하세요.

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>예: 고객이 제공한 키를 사용하여 Blob 업로드

다음 예제에서는 고객이 제공한 키를 만들고 해당 키를 사용하여 Blob을 업로드합니다. 코드는 블록을 업로드한 다음 블록 목록을 커밋하여 Blob을 Azure Storage에 작성합니다.

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

- [미사용 데이터에 대한 Azure 저장소 암호화](../common/storage-service-encryption.md)
- [Azure Active Directory 및 Azure 리소스에 대한 관리ID를 사용하여 Blob 및 큐에 대한 액세스 권한을 부여합니다.](../common/storage-auth-aad-msi.md)
