---
title: .NET (미리 보기)을 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기-Azure Storage
description: .NET 클라이언트 라이브러리를 사용 하 여 Azure Storage에서 Azure Active Directory 자격 증명을 사용 하 여 사용자 위임 SAS를 만드는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 59de768e75a88d7cfa5b68fa306d0e83f1aa0ba3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671336"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>.NET (미리 보기)을 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 Azure AD (Azure Active Directory) 자격 증명을 사용 하 여 [.net 용 Azure Storage 클라이언트 라이브러리](https://www.nuget.org/packages/Azure.Storage.Blobs)에서 컨테이너 또는 blob에 대 한 사용자 위임 SAS를 만드는 방법을 보여 줍니다.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>미리 보기 패키지 설치

이 문서의 예제에서는 Blob 저장소에 대 한 Azure Storage 클라이언트 라이브러리의 최신 미리 보기 버전을 사용 합니다. 미리 보기 패키지를 설치 하려면 NuGet 패키지 관리자 콘솔에서 다음 명령을 실행 합니다.

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

이 문서의 예제에서는 Azure AD 자격 증명을 사용 하 여 인증 하기 위해 [.net 용 Azure id 클라이언트 라이브러리](https://www.nuget.org/packages/Azure.Identity/) 의 최신 미리 보기 버전도 사용 합니다. Azure Id 클라이언트 라이브러리는 보안 주체를 인증 합니다. 인증 된 보안 주체는 사용자 위임 SAS를 만들 수 있습니다. Azure Id 클라이언트 라이브러리에 대 한 자세한 내용은 [.net 용 Azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)를 참조 하세요.

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>서비스 주체 만들기

Azure Id 클라이언트 라이브러리를 통해 Azure AD 자격 증명으로 인증 하려면 코드가 실행 되는 위치에 따라 서비스 주체 또는 관리 되는 id를 보안 주체로 사용 합니다. 코드를 개발 환경에서 실행 하는 경우 테스트 목적으로 서비스 주체를 사용 합니다. 코드가 Azure에서 실행 되는 경우 관리 id를 사용 합니다. 이 문서에서는 사용자가 개발 환경에서 코드를 실행 하 고 있다고 가정 하 고 서비스 주체를 사용 하 여 사용자 위임 SAS를 만드는 방법을 보여 줍니다.

Azure CLI를 사용 하 여 서비스 주체를 만들고 RBAC 역할을 할당 하려면 [az ad sp create-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 명령을 호출 합니다. 새 서비스 사용자에 게 할당할 Azure Storage 데이터 액세스 역할을 제공 합니다. 역할은 **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** 작업을 포함 해야 합니다. Azure Storage에 대해 제공 되는 기본 제공 역할에 대 한 자세한 내용은 [Azure 리소스에 대 한 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조 하세요.

또한 역할 할당에 대 한 범위를 제공 합니다. 서비스 주체는 저장소 계정 수준에서 수행 된 작업 인 사용자 위임 키를 만듭니다. 따라서 역할 할당의 범위는 저장소 계정, 리소스 그룹 또는 구독 수준에서 지정 되어야 합니다. 사용자 위임 SAS를 만드는 RBAC 권한에 대 한 자세한 내용은 [사용자 위임 Sas 만들기 (REST API)](/rest/api/storageservices/create-user-delegation-sas)에서 **rbac를 사용 하 여 권한 할당** 섹션을 참조 하세요.

서비스 주체에 역할을 할당할 수 있는 권한이 없는 경우 계정 소유자 또는 관리자에 게 역할 할당을 수행 하도록 요청 해야 할 수 있습니다.

다음 예에서는 Azure CLI를 사용 하 여 새 서비스 주체를 만들고 계정 범위를 사용 하 여 **저장소 Blob 데이터 읽기 권한자** 역할을 할당 합니다.

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

@No__t-0 명령은 JSON 형식의 서비스 사용자 속성 목록을 반환 합니다. 이러한 값을 복사 하 여 다음 단계에서 필요한 환경 변수를 만들 수 있습니다.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> RBAC 역할 할당을 전파 하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="set-environment-variables"></a>환경 변수 설정

Azure Id 클라이언트 라이브러리는 런타임에 세 가지 환경 변수에서 값을 읽어서 서비스 사용자를 인증 합니다. 다음 표에서는 각 환경 변수에 대해 설정할 값을 설명 합니다.

|환경 변수|값
|-|-
|`AZURE_CLIENT_ID`|서비스 사용자의 앱 ID
|`AZURE_TENANT_ID`|서비스 주체의 Azure AD 테 넌 트 ID
|`AZURE_CLIENT_SECRET`|서비스 사용자에 대해 생성 된 암호입니다.

> [!IMPORTANT]
> 환경 변수를 설정한 후 콘솔 창을 닫았다가 다시 엽니다. Visual Studio 또는 다른 개발 환경을 사용 하는 경우 새 환경 변수를 등록 하기 위해 개발 환경을 다시 시작 해야 할 수 있습니다.

## <a name="add-using-directives"></a>지시문을 사용하여 추가

Azure Id 및 Azure Storage 클라이언트 라이브러리의 미리 보기 버전을 사용 하려면 코드에 다음 `using` 지시문을 추가 합니다.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="authenticate-the-service-principal"></a>서비스 주체 인증

서비스 사용자를 인증 하려면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다. @No__t-0 생성자는 이전에 만든 환경 변수를 읽습니다.

다음 코드 조각에서는 인증 된 자격 증명을 가져오고이를 사용 하 여 Blob 저장소에 대 한 서비스 클라이언트를 만드는 방법을 보여 줍니다.

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

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
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>SAS 토큰 만들기

다음 코드 조각에서는 새 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 을 만들고 사용자 위임 SAS에 대 한 매개 변수를 제공 하는 방법을 보여 줍니다. 그런 다음 코드 조각은 [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) 를 호출 하 여 SAS 토큰 문자열을 가져옵니다. 마지막으로, 코드는 리소스 주소 및 SAS 토큰을 포함 하 여 전체 URI를 빌드합니다.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

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
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

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
    catch (StorageRequestFailedException e)
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

- [사용자 위임 키 가져오기 작업](/rest/api/storageservices/get-user-delegation-key)
- [사용자 위임 SAS (REST API) 만들기](/rest/api/storageservices/create-user-delegation-sas)
