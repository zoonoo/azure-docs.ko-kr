---
title: .NET을 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기
titleSuffix: Azure Storage
description: Azure Storage .NET 클라이언트 라이브러리를 사용 하 여 Azure Active Directory 자격 증명으로 사용자 위임 SAS를 만드는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: d5e8f5e3939ade4e5344ef586a62b6a19082e34e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809034"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>.NET을 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 Azure AD (Azure Active Directory) 자격 증명을 사용 하 여 .NET 용 Azure Storage 클라이언트 라이브러리 버전 12를 사용 하는 컨테이너 또는 blob에 대 한 사용자 위임 SAS를 만드는 방법을 보여 줍니다.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>데이터에 액세스 하기 위한 RBAC 역할 할당

Azure AD 보안 주체가 blob 데이터에 액세스 하려고 할 때 해당 보안 주체에 게 리소스에 대 한 권한이 있어야 합니다. 보안 주체가 Azure에서 관리 되는 id이 든, 개발 환경에서 코드를 실행 하는 Azure AD 사용자 계정 인지에 관계 없이 보안 주체는 Azure Storage의 blob 데이터에 대 한 액세스 권한을 부여 하는 RBAC 역할을 할당 받아야 합니다. RBAC를 통해 사용 권한을 할당 하는 방법에 대 한 자세한 내용은 [Azure Active Directory 사용 하 여 Azure blob 및 큐에](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)대 한 액세스 권한 부여에서 **액세스 권한에 대 한 RBAC 역할 할당** 섹션을 참조 하세요.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Azure Storage에서 Azure Id 클라이언트 라이브러리를 사용 하 여 인증 하는 방법에 대 한 자세한 내용은 azure [리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)에서 **azure id 라이브러리를 사용** 하 여 인증 섹션을 참조 하세요.

## <a name="add-using-directives"></a>지시문을 사용하여 추가

`using`Azure id 및 Azure Storage 클라이언트 라이브러리를 사용 하는 다음 지시문을 코드에 추가 합니다.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>인증 된 토큰 자격 증명 가져오기

코드에서 Azure Storage 요청에 권한을 부여 하는 데 사용할 수 있는 토큰 자격 증명을 가져오려면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다.

다음 코드 조각에서는 인증 된 토큰 자격 증명을 가져와서이를 사용 하 여 Blob 저장소에 대 한 서비스 클라이언트를 만드는 방법을 보여 줍니다.

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>사용자 위임 키 가져오기

모든 SAS는 키로 서명 됩니다. 사용자 위임 SAS를 만들려면 먼저 사용자 위임 키를 요청 해야 합니다. 그런 다음 SAS에 서명 하는 데 사용 됩니다. 사용자 위임 키는 Azure AD 자격 증명을 사용 한다는 점을 제외 하 고 서비스 SAS 또는 계정 SAS에 서명 하는 데 사용 되는 계정 키와 유사 합니다. 클라이언트에서 OAuth 2.0 토큰을 사용 하 여 사용자 위임 키를 요청 하면 Azure Storage 사용자를 대신 하 여 사용자 위임 키를 반환 합니다.

사용자 위임 키가 있으면 키의 수명 동안 해당 키를 사용 하 여 사용자 위임 공유 액세스 서명을 원하는 수 만큼 만들 수 있습니다. 사용자 위임 키는 해당 키를 획득 하는 데 사용 되는 OAuth 2.0 토큰과는 독립적 이므로 키가 여전히 유효한 경우에는 토큰을 갱신할 필요가 없습니다. 키가 최대 7 일 동안 유효 하도록 지정할 수 있습니다.

다음 방법 중 하나를 사용 하 여 사용자 위임 키를 요청 합니다.

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

다음 코드 조각은 사용자 위임 키를 가져오고 해당 속성을 작성 합니다.

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>SAS 토큰 만들기

다음 코드 조각에서는 새 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 을 만들고 사용자 위임 SAS에 대 한 매개 변수를 제공 하는 방법을 보여 줍니다. 그런 다음 코드 조각은 [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) 를 호출 하 여 SAS 토큰 문자열을 가져옵니다. 마지막으로, 코드는 리소스 주소 및 SAS 토큰을 포함 하 여 전체 URI를 빌드합니다.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>예: 사용자 위임 SAS 가져오기

다음 예제 메서드는 보안 주체를 인증 하 고 사용자 위임 SAS를 만들기 위한 전체 코드를 보여 줍니다.

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>예: 사용자 위임 SAS를 사용 하 여 blob 읽기

다음 예제에서는 시뮬레이트된 클라이언트 응용 프로그램에서 이전 예제에서 만든 사용자 위임 SAS를 테스트 합니다. SAS가 유효한 경우 클라이언트 응용 프로그램은 blob의 내용을 읽을 수 있습니다. SAS가 유효 하지 않은 경우 (예: 만료 된 경우) Azure Storage 오류 코드 403 (사용할 수 없음)을 반환 합니다.

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid,
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>참조

- [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](../common/storage-sas-overview.md)
- [사용자 위임 키 가져오기 작업](/rest/api/storageservices/get-user-delegation-key)
- [사용자 위임 SAS (REST API) 만들기](/rest/api/storageservices/create-user-delegation-sas)
