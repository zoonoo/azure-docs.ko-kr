---
title: .NET-Azure Storage를 사용 하 여 blob 컨테이너 만들기 또는 삭제
description: .NET 클라이언트 라이브러리를 사용 하 여 Azure Storage 계정에서 blob 컨테이너를 만들거나 삭제 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135941"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>.NET을 사용 하 여 Azure Storage에서 컨테이너 만들기 또는 삭제

Azure Storage blob은 컨테이너로 구성 됩니다. Blob을 업로드 하려면 먼저 컨테이너를 만들어야 합니다. 이 문서에서는 [.net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 사용 하 여 컨테이너를 만들고 삭제 하는 방법을 보여 줍니다.

## <a name="name-a-container"></a>컨테이너 이름

컨테이너 이름은 컨테이너 또는 해당 blob의 주소를 지정할 때 사용 되는 고유 URI의 일부를 형성 하므로 올바른 DNS 이름 이어야 합니다. 컨테이너의 이름을 지정할 때 다음 규칙을 따릅니다.

- 컨테이너 이름은 3 ~ 007e; 63 자까지 가능 합니다.
- 컨테이너 이름은 문자 또는 숫자로 시작 해야 하며 소문자, 숫자 및 대시 (-) 문자만 포함할 수 있습니다.
- 컨테이너 이름에는 연속 하는 대시 문자를 두 개 이상 사용할 수 없습니다.

컨테이너의 URI는 다음 형식으로 되어 있습니다.

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너를 만들려면 다음 메서드 중 하나를 호출 합니다.

- [만들기](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

같은 이름을 가진 컨테이너가 이미 있는 경우 **Create** 및 **createasync** 메서드는 예외를 throw 합니다.

**Createifnotexists** 및 **CreateIfNotExistsAsync** 메서드는 컨테이너를 만들었는지 여부를 나타내는 부울 값을 반환 합니다. 이름이 같은 컨테이너가 이미 있으면이 메서드는 **False** 를 반환 하 여 새 컨테이너가 만들어지지 않았음을 표시 합니다.

컨테이너는 저장소 계정 아래에 즉시 생성됩니다. 컨테이너를 서로 중첩할 수는 없습니다.

다음 예제에서는 컨테이너를 비동기식으로 만듭니다.

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>루트 컨테이너 만들기

루트 컨테이너는 저장소 계정에 대한 기본 컨테이너 역할을 합니다. 각 저장소 계정에는 하나의 루트 컨테이너가 있을 수 있습니다. 여기에는 *$root*이름으로 이름을 지정 해야 합니다. 루트 컨테이너를 명시적으로 만들거나 삭제 해야 합니다.

루트 컨테이너 이름을 포함 하지 않고 루트 컨테이너에 저장 된 blob을 참조할 수 있습니다. 루트 컨테이너를 사용 하면 저장소 계정 계층의 최상위 수준에서 blob을 참조할 수 있습니다. 예를 들어 루트 컨테이너에 있는 Blob을 참조할 수 있는 방법은 다음과 같습니다.

`https://myaccount.blob.core.windows.net/default.html`

다음 예제에서는 루트 컨테이너를 동기적으로 만듭니다.

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>컨테이너 삭제

.NET에서 컨테이너를 삭제 하려면 다음 방법 중 하나를 사용 합니다.

- [Delete](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

**Delete** 및 **DeleteAsync** 메서드는 컨테이너가 없는 경우 예외를 throw 합니다.

**Deleteifexists** 및 **DeleteIfExistsAsync** 메서드는 컨테이너가 삭제 되었는지 여부를 나타내는 부울 값을 반환 합니다. 지정 된 컨테이너가 없으면이 메서드는 **False** 를 반환 하 여 컨테이너가 삭제 되지 않았음을 표시 합니다.

컨테이너를 삭제 한 후에는 최소 30 초 동안 동일한 이름의 컨테이너를 만들 수 없으며, 더 길어질 수도 있습니다. 컨테이너를 삭제 하는 동안 동일한 이름의 컨테이너를 만들려고 하면 HTTP 오류 코드 409 (충돌)이 발생 합니다. 컨테이너 또는 컨테이너에 포함 된 blob에 대 한 다른 모든 작업은 컨테이너를 삭제 하는 동안 HTTP 오류 코드 404 (찾을 수 없음)와 함께 실패 합니다.

다음 예제에서는 지정 된 컨테이너를 삭제 하 고 컨테이너가 없는 경우 예외를 처리 합니다.

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
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

다음 예제에서는 지정 된 접두사로 시작 하는 모든 컨테이너를 삭제 하는 방법을 보여 줍니다. 이 예에서는 컨테이너에 기존 임대가 있는 경우 임대를 중단 합니다.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>참고 항목

- [컨테이너 작업 만들기](/rest/api/storageservices/create-container)
- [컨테이너 삭제 작업](/rest/api/storageservices/delete-container)
