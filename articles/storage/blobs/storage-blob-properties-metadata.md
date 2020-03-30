---
title: .NET - Azure 저장소를 통해 Blob에 대한 속성 및 메타데이터 관리
description: .NET 클라이언트 라이브러리를 사용하여 Azure Storage 계정의 Blob에 시스템 속성을 설정 및 검색하고 사용자 지정 메타데이터를 저장하는 방법을 알아봅니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137665"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>.NET을 통해 Blob 속성 및 메타데이터 관리

Blob은 포함된 데이터 외에도 시스템 속성 및 사용자 정의 메타데이터를 지원합니다. 이 문서에서는 .NET 에 [대한 Azure Storage 클라이언트 라이브러리를](/dotnet/api/overview/azure/storage?view=azure-dotnet)사용하여 시스템 속성 및 사용자 정의 메타데이터를 관리하는 방법을 보여 주며 있습니다.

## <a name="about-properties-and-metadata"></a>속성 및 메타데이터 정보

- **시스템 속성**: 시스템 속성은 각 Blob 저장소 리소스에 있습니다. 그 중 일부를 읽거나 설정할 수 있지만 나머지는 읽기 전용입니다. 일부 시스템 속성은 내부적으로 특정 표준 HTTP 헤더에 해당합니다. .NET에 대한 Azure 저장소 클라이언트 라이브러리는 이러한 속성을 유지 관리합니다.

- **사용자 정의 메타데이터**: 사용자 정의 메타데이터는 Blob 저장소 리소스에 대해 지정한 하나 이상의 이름-값 쌍으로 구성됩니다. 메타데이터를 사용하여 리소스와 함께 추가 값을 저장할 수 있습니다. 메타데이터 값은 사용자 고유의 용도로만 사용되며 리소스의 행동 방식에영향을 주지 않습니다.

Blob 저장소 리소스에 대한 메타데이터 및 속성 값을 검색하는 것은 2단계 프로세스입니다. 이러한 값을 읽으려면 먼저 또는 `FetchAttributes` `FetchAttributesAsync` 메서드를 호출하여 명시적으로 가져와야 합니다. 이 규칙의 예외는 `Exists` 및 `ExistsAsync` 메서드가 `FetchAttributes` 커버 아래에 적절한 메서드를 호출한다는 것입니다. 이러한 메서드 중 하나를 호출할 때 을 호출할 `FetchAttributes`필요 도 없습니다.

> [!IMPORTANT]
> 저장소 리소스에 대한 속성 또는 메타데이터 값이 채워지지 않은 경우 코드가 `FetchAttributes` `FetchAttributesAsync` 또는 메서드를 호출하는지 확인합니다.

## <a name="set-and-retrieve-properties"></a>속성 설정 및 검색

다음 코드 예제는 `ContentType` `ContentLanguage` Blob에서 및 시스템 속성을 설정합니다.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Blob 속성을 검색하려면 `FetchAttributes` Blob의 또는 `FetchAttributesAsync` 메서드를 호출하여 `Properties` 속성을 채웁니다. 다음 코드 예제는 Blob의 시스템 속성을 얻고 일부 값을 표시합니다.

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>메타데이터 설정 및 검색

Blob 또는 컨테이너 리소스에 하나 이상의 이름-값 쌍으로 메타 데이터를 지정할 수 있습니다. 메타데이터를 설정하려면 리소스의 `Metadata` 컬렉션에 이름 값 쌍을 추가합니다. 그런 다음 다음 방법 중 하나를 호출하여 값을 작성합니다.

- [집합 메타데이터](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [세트메타데이터Async](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

메타데이터 이름/값 쌍은 유효한 HTTP 헤더이며 HTTP 헤더를 관리하는 모든 제한을 준수해야 합니다. 메타데이터 이름은 유효한 HTTP 헤더 이름과 유효한 C# 식별자여야 하며 ASCII 문자만 포함할 수 있으며 대/소문자를 구분하지 않는 것으로 처리되어야 합니다. [BASE64-ASCII](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) 문자가 포함된 메타데이터 값을 참조하거나 [URL을 인코딩합니다.](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode)

메타데이터의 이름은 C# 식별자에 대한 명명 규칙을 준수해야 합니다. 메타데이터 이름은 만들 때 사용되는 대/소문자를 유지 관리하지만 설정하거나 읽을 때 대/소문자를 구분하지 않습니다. 동일한 이름을 사용하는 두 개 이상의 메타데이터 헤더가 리소스에 대해 제출되는 경우 Azure Blob 저장소는 HTTP 오류 코드 400(잘못된 요청)을 반환합니다.

다음 코드 예제는 Blob에서 메타데이터를 설정합니다. 컬렉션의 `Add` 메서드를 사용하여 하나의 값이 설정됩니다. 다른 값은 암시적 키/값 구문을 사용하여 설정됩니다.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

메타데이터를 검색하려면 `FetchAttributes` Blob 또는 컨테이너의 또는 `FetchAttributesAsync` 메서드를 `Metadata` 호출하여 컬렉션을 채운 다음 아래 예제와 같이 값을 읽으십시오.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>참조

- [Blob 속성 설정 작업](/rest/api/storageservices/set-blob-properties)
- [Blob 속성 작업 받기](/rest/api/storageservices/get-blob-properties)
- [Blob 메타데이터 작업 설정](/rest/api/storageservices/set-blob-metadata)
- [Blob 메타데이터 작업 받기](/rest/api/storageservices/get-blob-metadata)
