---
title: .NET을 사용하여 계정 SAS 만들기
titleSuffix: Azure Storage
description: .NET 클라이언트 라이브러리를 사용하여 계정 SAS(공유 액세스 서명)를 만드는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 3e08962c0cff2e9aa04601f89e677334878e2f53
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110461865"
---
# <a name="create-an-account-sas-with-net"></a>.NET을 사용하여 계정 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 스토리지 계정 키를 사용하여 [.NET용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage)로 계정 SAS를 만드는 방법을 보여 줍니다.

## <a name="create-an-account-sas"></a>계정 SAS 만들기

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

계정 SAS는 계정 액세스 키로 서명됩니다. [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) 클래스를 사용하여 SAS에 서명하는 데 사용되는 자격 증명을 만듭니다. 그런 다음 새 [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) 개체를 만들고 [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters)를 호출하여 SAS 토큰 문자열을 가져옵니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

컨테이너에 대한 계정 SAS를 만들려면 [CloudStorageAccount.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) 메서드를 호출합니다.

다음 코드 예제는 Blob 및 파일 공유에 유효한 계정 SAS를 만들며 클라이언트가 읽기, 쓰기, 목록 권한을 사용하여 서비스 수준 API에 액세스할 수 있는 권한을 부여합니다. 계정 SAS는 프로토콜을 HTTPS로 제한하므로 반드시 HTTPS로 요청해야 합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>클라이언트에서 계정 SAS 사용

계정 SAS를 사용하여 Blob service에 대한 서비스 수준 API에 액세스하려면 SAS를 사용하는 Blob 클라이언트 개체와 스토리지 계정의 Blob Storage 엔드포인트를 구성합니다.

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

이 코드 조각에서 `<storage-account>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>다음 단계

- [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)
- [계정 SAS 만들기](/rest/api/storageservices/create-account-sas)
