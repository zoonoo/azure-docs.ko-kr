---
title: 컨테이너 또는 blob에 대 한 서비스 SAS 만들기
titleSuffix: Azure Storage
description: Blob Storage에 대 한 Azure Storage 라이브러리를 사용 하 여 컨테이너 또는 blob에 대 한 서비스 SAS (공유 액세스 서명)를 만드는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b2d18165bf2c5a4f70f1cbc555db79020ce988f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95250620"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>컨테이너 또는 blob에 대 한 서비스 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 저장소 계정 키를 사용 하 여 Blob Storage에 대 한 Azure Storage 클라이언트 라이브러리를 통해 컨테이너 또는 blob에 대 한 서비스 SAS를 만드는 방법을 보여 줍니다.

## <a name="create-a-service-sas-for-a-blob-container"></a>Blob 컨테이너에 대 한 서비스 SAS 만들기

다음 코드 예제에서는 컨테이너에 대 한 SAS를 만듭니다. 기존에 저장된 액세스 정책의 이름을 제공하는 경우 해당 정책은 SAS와 연결됩니다. 저장 된 액세스 정책이 제공 되지 않는 경우 코드는 컨테이너에 임시 SAS를 만듭니다.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

서비스 SAS는 계정 액세스 키로 서명 됩니다. [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) 클래스를 사용 하 여 SAS에 서명 하는 데 사용 되는 자격 증명을 만듭니다. 그런 다음 새 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 개체를 만들고 [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) 를 호출 하 여 SAS 토큰 문자열을 가져옵니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetServiceSasUriForContainer":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

컨테이너에 대 한 서비스 SAS를 만들려면 [CloudBlobContainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) 메서드를 호출 합니다.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, 
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

서비스 SAS는 계정 액세스 키로 서명 됩니다. [StorageSharedKeyCredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential) 클래스를 사용 하 여 SAS에 서명 하는 데 사용 되는 자격 증명을 만듭니다. 그런 다음 필요한 옵션을 제공 하는 [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) 함수를 호출 하 여 SAS 토큰 문자열을 가져옵니다.

```javascript
function getContainerSasUri(containerClient, sharedKeyCredential, storedPolicyName) {
    const sasOptions = {
        containerName: containerClient.containerName,
        permissions: ContainerSASPermissions.parse("c")
    };

    if (storedPolicyName == null) {
        sasOptions.startsOn = new Date();
        sasOptions.expiresOn = new Date(new Date().valueOf() + 3600 * 1000);
    } else {
        sasOptions.identifier = storedPolicyName;
    }

    const sasToken = generateBlobSASQueryParameters(sasOptions, sharedKeyCredential).toString();
    console.log(`SAS token for blob container is: ${sasToken}`);

    return `${containerClient.url}?${sasToken}`;
}
```

---

## <a name="create-a-service-sas-for-a-blob"></a>Blob에 대 한 서비스 SAS 만들기

다음 코드 예제에서는 blob에 SAS를 만듭니다. 기존에 저장된 액세스 정책의 이름을 제공하는 경우 해당 정책은 SAS와 연결됩니다. 저장 된 액세스 정책이 제공 되지 않는 경우 코드는 blob에 임시 SAS를 만듭니다.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

서비스 SAS는 계정 액세스 키로 서명 됩니다. [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) 클래스를 사용 하 여 SAS에 서명 하는 데 사용 되는 자격 증명을 만듭니다. 그런 다음 새 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 개체를 만들고 [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) 를 호출 하 여 SAS 토큰 문자열을 가져옵니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetServiceSasUriForBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Blob에 대 한 서비스 SAS를 만들려면 [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) 메서드를 호출 합니다.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Blob에 대 한 서비스 SAS를 만들려면 [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) 메서드를 호출 합니다.

Blob에 대 한 서비스 SAS를 만들려면 필요한 옵션을 제공 하는 [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) 함수를 호출 합니다.

```javascript
function getBlobSasUri(containerClient, blobName, sharedKeyCredential, storedPolicyName) {
    const sasOptions = {
        containerName: containerClient.containerName,
        blobName: blobName
    };

    if (storedPolicyName == null) {
        sasOptions.startsOn = new Date();
        sasOptions.expiresOn = new Date(new Date().valueOf() + 3600 * 1000);
        sasOptions.permissions = BlobSASPermissions.parse("r");
    } else {
        sasOptions.identifier = storedPolicyName;
    }

    const sasToken = generateBlobSASQueryParameters(sasOptions, sharedKeyCredential).toString();
    console.log(`SAS token for blob is: ${sasToken}`);

    return `${containerClient.getBlockBlobClient(blobName).url}?${sasToken}`;
}
```

---

## <a name="create-a-service-sas-for-a-directory"></a>디렉터리에 대 한 서비스 SAS 만들기

계층적 네임 스페이스를 사용 하도록 설정 된 저장소 계정에서 디렉터리에 대 한 서비스 SAS를 만들 수 있습니다. Service SAS를 만들려면 12.5.0 [. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) 패키지의 버전을 설치 했는지 확인 합니다.

다음 예제에서는 .NET 용 v12 클라이언트 라이브러리를 사용 하 여 디렉터리에 대 한 서비스 SAS를 만드는 방법을 보여 줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetServiceSasUriForDirectory":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>다음 단계

- [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](../common/storage-sas-overview.md)
- [서비스 SAS 만들기](/rest/api/storageservices/create-service-sas)
