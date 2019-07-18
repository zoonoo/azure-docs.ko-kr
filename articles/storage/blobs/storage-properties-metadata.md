---
title: Azure Storage에서 개체 속성 및 메타데이터를 설정 및 검색 | Microsoft Docs
description: Azure Storage의 개체에 사용자 지정 메타데이터를 저장하고 시스템 속성을 설정 및 검색합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.openlocfilehash: e8a85319a12f04a11e3914716d9ff84cdb6de8d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787875"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>속성 및 메타데이터 설정 및 검색

Azure Storage의 개체는 시스템 속성 및 사용자 정의 메타데이터와 포함된 데이터를 지원합니다. 이 문서에서는 [.NET용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage/client)를 사용하여 관리 시스템 속성 및 사용자 정의 메타데이터를 관리하는 방법을 설명합니다.

* **시스템 속성**:  각 저장소 리소스에 시스템 속성이 있습니다. 그 중 일부를 읽거나 설정할 수 있지만 나머지는 읽기 전용입니다. 일부 시스템 속성은 내부적으로 특정 표준 HTTP 헤더에 해당합니다. Azure Storage 클라이언트 라이브러리에서 이러한 속성을 유지 관리합니다.

* **사용자 정의 메타 데이터**: 사용자 정의 메타 데이터를 Azure Storage 리소스를 지정 하는 하나 이상의 이름-값 쌍으로 구성 됩니다. 메타데이터를 사용하여 리소스와 함께 추가 값을 저장할 수 있습니다. 메타데이터 값은 고유한 목적으로만 사용되며 리소스의 동작 방식에 영향을 주지 않습니다.

저장소 리소스에 대한 속성 및 메타데이터 값 검색은 두 단계로 이루어집니다. 이러한 값을 읽으려면 먼저 **FetchAttributes** 또는 **FetchAttributesAsync** 메서드를 호출하여 명시적으로 가져와야 합니다. 리소스에서 **Exists** 또는 **ExistsAsync** 메서드를 호출하는 경우, 예외가 발생합니다. 이러한 메서드 중 하나를 호출하면 Azure Storage는 **Exists** 메서드 호출의 일부로 해당하는 **FetchAttributes** 메서드를 내부적으로 호출합니다.

> [!IMPORTANT]
> 저장소 리소스의 속성 또는 메타데이터 값이 채워지지 않은 경우에는 코드가 **FetchAttributes** 또는 **FetchAttributesAsync** 메서드를 호출하는지 확인합니다.
>
> 메타 데이터 이름/값 쌍은 유효한 HTTP 헤더 이므로 HTTP 헤더와 관련 된 모든 제한 사항을 준수 해야 하며 메타 데이터 이름은 유효한 HTTP 헤더 이름 이어야 합니다. 유효 하 고 C# 식별자는 ASCII 문자만 포함 될 수 있습니다 및으로 대/소문자 처리 해야 합니다. 비 ASCII 문자가 포함 된 메타 데이터 값은 Base64로 인코딩된 또는 URL 인코딩 이어야 합니다.

## <a name="setting-and-retrieving-properties"></a>속성 설정 및 검색
속성 값을 검색하려면 Blob 또는 컨테이너에서 **FetchAttributesAsync** 메서드를 호출하여 속성을 채운 다음 값을 읽습니다.

개체에서 속성을 설정하려면 속성 값을 지정한 다음 **SetProperties** 메서드를 호출합니다.

다음 코드 예제는 컨테이너를 만들고 콘솔 창에 속성 값 일부를 씁니다.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>메타데이터 설정 및 검색
Blob 또는 컨테이너 리소스에 하나 이상의 이름-값 쌍으로 메타 데이터를 지정할 수 있습니다. 메타데이터를 설정하려면 이름-값 쌍을 리소스의 **메타데이터** 컬렉션에 추가한 다음, **SetMetadata** 또는 **SetMetadataAsync** 메서드를 호출하여 값을 서비스에 저장합니다.

> [!NOTE]
> 메타데이터의 이름은 C# 식별자에 대한 명명 규칙을 준수해야 합니다.
>
>

다음 코드 예제에서는 컨테이너에서 메타데이터를 설정합니다. 하나의 값은 컬렉션의 **추가** 메서드를 사용하여 설정됩니다. 다른 값은 암시적 키/값 구문을 사용하여 설정됩니다. 둘 다 모두 유효합니다.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

메타데이터를 검색하려면 아래 예제에 나온 것처럼 Blob 또는 컨테이너에서 **FetchAttributes** 또는 **FetchAttributesAsync** 메서드를 호출하여 **Metadata** 컬렉션을 채운 후에 값을 읽습니다.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
{
    // Fetch container attributes in order to populate the container's properties and metadata.
    await container.FetchAttributesAsync();

    // Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>다음 단계
* [.NET 용 azure Storage 클라이언트 라이브러리](/dotnet/api/?term=Microsoft.Azure.Storage)
* [.NET 패키지에 대 한 azure Blob storage 클라이언트 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)
* [.NET 패키지에 대 한 azure 큐 저장소 클라이언트 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [.NET 패키지에 대 한 azure 파일 저장소 클라이언트 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

