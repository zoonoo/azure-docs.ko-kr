---
title: .NET - Azure 저장소를 사용하여 Blob 컨테이너 만들기 또는 삭제
description: .NET 클라이언트 라이브러리를 사용하여 Azure Storage 계정에서 Blob 컨테이너를 만들거나 삭제하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135941"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>.NET을 사용하여 Azure 저장소에서 컨테이너 만들기 또는 삭제

Azure 저장소의 Blob은 컨테이너로 구성됩니다. Blob을 업로드하려면 먼저 컨테이너를 만들어야 합니다. 이 문서에서는 .NET 에 [대한 Azure Storage 클라이언트 라이브러리를](/dotnet/api/overview/azure/storage?view=azure-dotnet)사용하여 컨테이너를 만들고 삭제하는 방법을 보여 주며

## <a name="name-a-container"></a>컨테이너 이름 지정

컨테이너 이름은 컨테이너 또는 해당 Blob을 처리하는 데 사용되는 고유한 URI의 일부를 구성하므로 유효한 DNS 이름이어야 합니다. 컨테이너 이름을 지정할 때 다음 규칙을 따릅니다.

- 컨테이너 이름은 3자에서 63자 사이일 수 있습니다.
- 컨테이너 이름은 문자 또는 숫자로 시작해야 하며 소문자, 숫자 및 대시(-) 문자만 포함할 수 있습니다.
- 컨테이너 이름에는 두 개 이상의 연속 대시 문자가 허용되지 않습니다.

컨테이너의 URI는 다음과 같은 형식입니다.

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너를 만들려면 다음 방법 중 하나를 호출합니다.

- [만들기](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [만들기Async](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [만들기IfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

**Create** 및 **CreateAsync** 메서드는 이름이 같은 컨테이너가 이미 있는 경우 예외를 throw합니다.

**CreateIfNotExists** 및 **CreateIfNotExistsAsync** 메서드는 컨테이너가 만들어졌는지 여부를 나타내는 부울 값을 반환합니다. 이름이 같은 컨테이너가 이미 있는 경우 이러한 메서드는 **False를** 반환하여 새 컨테이너가 만들어지지 되었음을 나타냅니다.

컨테이너는 저장소 계정 아래에 즉시 생성됩니다. 컨테이너를 서로 중첩할 수는 없습니다.

다음 예제는 비동기적으로 컨테이너를 만듭니다.

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

루트 컨테이너는 저장소 계정에 대한 기본 컨테이너 역할을 합니다. 각 저장소 계정에는 $root 이름을 지정해야 하는 하나의 루트 컨테이너가 있을 수 *있습니다.* 루트 컨테이너를 명시적으로 만들거나 삭제해야 합니다.

루트 컨테이너 이름을 포함하지 않고 루트 컨테이너에 저장된 Blob을 참조할 수 있습니다. 루트 컨테이너를 사용하면 저장소 계정 계층 구조의 최상위 수준에서 Blob을 참조할 수 있습니다. 예를 들어 루트 컨테이너에 있는 Blob을 참조할 수 있는 방법은 다음과 같습니다.

`https://myaccount.blob.core.windows.net/default.html`

다음 예제는 루트 컨테이너를 동기적으로 만듭니다.

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

.NET에서 컨테이너를 삭제하려면 다음 방법 중 하나를 사용합니다.

- [삭제](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [삭제IfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [삭제IfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

**삭제** 및 **DeleteAsync** 메서드는 컨테이너가 없는 경우 예외를 throw합니다.

**삭제IfExists** 및 **DeleteIfExistsAsync** 메서드는 컨테이너가 삭제되었는지 여부를 나타내는 부울 값을 반환합니다. 지정된 컨테이너가 없는 경우 이러한 메서드는 **False를** 반환하여 컨테이너가 삭제되지 않음을 나타냅니다.

컨테이너를 삭제한 후에는 이름이 같은 컨테이너를 30초 이상 만들 수 없습니다. 컨테이너가 삭제되는 동안 HTTP 오류 코드 409(충돌)와 같은 이름의 컨테이너를 만들려는 시도가 실패합니다. 컨테이너 또는 컨테이너에 포함 된 Blob에 다른 작업은 컨테이너가 삭제 되는 동안 HTTP 오류 코드 404 (찾을 수 없습니다)와 함께 실패 합니다.

다음 예제에서는 지정된 컨테이너를 삭제 하 고 컨테이너가 없는 경우 예외를 처리 합니다.

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

다음 예제에서는 지정된 접두사로 시작하는 모든 컨테이너를 삭제하는 방법을 보여 주십습니다. 이 예제에서는 컨테이너에 기존 임대가 있는 경우 임대를 중단합니다.

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

## <a name="see-also"></a>참조

- [컨테이너 작업 만들기](/rest/api/storageservices/create-container)
- [컨테이너 삭제 작업](/rest/api/storageservices/delete-container)
