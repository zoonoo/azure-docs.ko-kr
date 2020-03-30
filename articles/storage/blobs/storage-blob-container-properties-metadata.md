---
title: .NET을 사용하여 Blob 컨테이너의 속성 및 메타데이터 관리
titleSuffix: Azure Storage
description: .NET 클라이언트 라이브러리를 사용하여 Azure Storage 계정의 Blob 컨테이너에 시스템 속성을 설정 및 검색하고 사용자 지정 메타데이터를 저장하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135924"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>.NET을 통해 컨테이너 속성 및 메타데이터 관리

Blob 컨테이너는 포함된 데이터 외에도 시스템 속성 및 사용자 정의 메타데이터를 지원합니다. 이 문서에서는 .NET 에 [대한 Azure Storage 클라이언트 라이브러리를](/dotnet/api/overview/azure/storage?view=azure-dotnet)사용하여 시스템 속성 및 사용자 정의 메타데이터를 관리하는 방법을 보여 주며 있습니다.

## <a name="about-properties-and-metadata"></a>속성 및 메타데이터 정보

- **시스템 속성**: 시스템 속성은 각 Blob 저장소 리소스에 있습니다. 그 중 일부를 읽거나 설정할 수 있지만 나머지는 읽기 전용입니다. 일부 시스템 속성은 내부적으로 특정 표준 HTTP 헤더에 해당합니다. .NET에 대한 Azure 저장소 클라이언트 라이브러리는 이러한 속성을 유지 관리합니다.

- **사용자 정의 메타데이터**: 사용자 정의 메타데이터는 Blob 저장소 리소스에 대해 지정한 하나 이상의 이름-값 쌍으로 구성됩니다. 메타데이터를 사용하여 리소스와 함께 추가 값을 저장할 수 있습니다. 메타데이터 값은 고유한 목적으로만 사용되며 리소스의 동작 방식에 영향을 주지 않습니다.

Blob 저장소 리소스에 대한 속성 및 메타데이터 값을 검색하는 것은 2단계 프로세스입니다. 이러한 값을 읽으려면 먼저 **FetchAttributes** 또는 **FetchAttributesAsync** 메서드를 호출하여 명시적으로 가져와야 합니다. 이 규칙의 예외는 **Exists** 및 **ExistsAsync** 메서드가 커버 아래에 있는 적절한 **FetchAttributes 메서드를** 호출한다는 것입니다. 이러한 메서드 중 하나를 호출할 때 **FetchAttributes를**호출할 필요 도 없습니다.

> [!IMPORTANT]
> 스토리지 리소스의 속성 또는 메타데이터 값이 채워지지 않은 경우에는 코드가 **FetchAttributes** 또는 **FetchAttributesAsync** 메서드를 호출하는지 확인합니다.

메타데이터 이름/값 쌍은 유효한 HTTP 헤더이므로 HTTP 헤더를 관리하는 모든 제한을 준수해야 합니다. 메타데이터 이름은 유효한 HTTP 헤더 이름과 유효한 C# 식별자여야 하며 ASCII 문자만 포함할 수 있으며 대/소문자를 구분하지 않는 것으로 처리되어야 합니다. ASCII가 아닌 문자를 포함하는 메타데이터 값은 Base64 인코딩 또는 URL 인코딩이어야 합니다.

## <a name="retrieve-container-properties"></a>컨테이너 속성 검색

컨테이너 속성을 검색하려면 다음 방법 중 하나를 호출합니다.

- [속성 가져오기](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [페치트트애싱크](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

다음 코드 예제는 컨테이너의 시스템 속성을 가져오고 콘솔 창에 일부 속성 값을 씁니다.

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

Blob 또는 컨테이너 리소스에 하나 이상의 이름-값 쌍으로 메타 데이터를 지정할 수 있습니다. 메타데이터를 설정하려면 리소스의 **메타데이터** 컬렉션에 이름 값 쌍을 추가한 다음 다음 방법 중 하나를 호출하여 값을 작성합니다.

- [집합 메타데이터](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [세트메타데이터Async](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

메타데이터의 이름은 C# 식별자에 대한 명명 규칙을 준수해야 합니다. 메타데이터 이름은 생성된 대/소문자를 유지하지만 설정하거나 읽을 때 대/소문자를 구분하지 않습니다. 이름이 같은 두 개 이상의 메타데이터 헤더가 리소스에 대해 제출되는 경우 Blob 저장소는 HTTP 오류 코드 400(잘못된 요청)을 반환합니다.

다음 코드 예제에서는 컨테이너에서 메타데이터를 설정합니다. 하나의 값은 컬렉션의 **추가** 메서드를 사용하여 설정됩니다. 다른 값은 암시적 키/값 구문을 사용하여 설정됩니다. 둘 다 모두 유효합니다.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

메타데이터를 검색하려면 아래 예제에 나온 것처럼 Blob 또는 컨테이너에서 **FetchAttributes** 또는 **FetchAttributesAsync** 메서드를 호출하여 **Metadata** 컬렉션을 채운 후에 값을 읽습니다.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
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

- [컨테이너 속성 작업 받기](/rest/api/storageservices/get-container-properties)
- [컨테이너 메타데이터 작업 설정](/rest/api/storageservices/set-container-metadata)
- [컨테이너 메타데이터 작업 받기](/rest/api/storageservices/set-container-metadata)
