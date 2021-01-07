---
title: Blob Storage에서 동시성 관리
titleSuffix: Azure Storage
description: 응용 프로그램에서 낙관적 또는 비관적 동시성을 구현 하 여 blob에 대 한 여러 작성기를 관리 하는 방법에 대해 알아봅니다. 낙관적 동시성은 blob에 대 한 ETag 값을 확인 하 고 제공 된 ETag와 비교 합니다. 비관적 동시성은 배타적 임대를 사용 하 여 blob을 다른 작성기로 잠급니다.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523519"
---
# <a name="managing-concurrency-in-blob-storage"></a>Blob Storage에서 동시성 관리

최신 응용 프로그램에는 여러 사용자가 동시에 데이터를 보고 업데이트 하는 경우가 많습니다. 응용 프로그램 개발자는 특히 여러 사용자가 동일한 데이터를 업데이트할 수 있는 시나리오를 위해 최종 사용자에 게 예측 가능한 환경을 제공 하는 방법을 신중 하 게 고려해 야 합니다. 개발자가 일반적으로 고려하는 주요 데이터 동시성 전략에는 다음의 세 가지가 있습니다.  

- **낙관적 동시성**: 업데이트의 일부로 업데이트를 수행 하는 응용 프로그램은 응용 프로그램이 마지막으로 데이터를 읽은 후 데이터가 변경 되었는지 여부를 확인 합니다. 예를 들어 wiki 페이지를 보는 두 사용자가 해당 페이지에 대 한 업데이트를 수행 하는 경우 wiki 플랫폼은 두 번째 업데이트가 첫 번째 업데이트를 덮어쓰지 않도록 해야 합니다. 또한 사용자가 업데이트의 성공 여부를 사용자에 게 파악 해야 합니다. 이 전략은 웹 애플리케이션에서 가장 흔히 사용됩니다.

- **비관적 동시성**: 업데이트를 수행 하려는 응용 프로그램은 잠금이 해제 될 때까지 다른 사용자가 데이터를 업데이트 하지 못하게 하는 개체에 대 한 잠금을 사용 합니다. 예를 들어 주 복제본만 업데이트를 수행 하는 기본/보조 데이터 복제 시나리오에서 주 복제본은 일반적으로 오랜 시간 동안 데이터에 대 한 배타적 잠금을 보유 하 여 다른 사용자가 업데이트할 수 없도록 합니다.

- **마지막 기록기** 적용: 다른 응용 프로그램이 데이터를 읽은 후 업데이트 했는지 먼저 확인 하지 않고 업데이트 작업을 진행할 수 있도록 하는 방법입니다. 이 방법은 일반적으로 여러 사용자가 동시에 동일한 데이터에 액세스 하지 않는 방식으로 데이터를 분할할 때 사용 됩니다. 일시적인 데이터 스트림을 처리하는 경우에도 이 전략이 유용할 수 있습니다.

Azure Storage는 낙관적 및 비관적 동시성을 완벽 하 게 지원 하기 위한 다양 한 기능을 제공 하지만 세 가지 전략을 모두 지원 합니다. Azure Storage은 서비스가 삽입 또는 업데이트 작업을 수행 하 고 나면 후속 읽기 작업에서 최신 업데이트를 반환 하도록 보장 하는 강력한 일관성 모델을 수용 하도록 디자인 되었습니다.

개발자는 적절 한 동시성 전략을 선택 하는 것 외에도 저장소 플랫폼의 변경 내용, 특히 여러 트랜잭션에서 동일한 개체의 변경 내용을 격리 하는 방법을 알고 있어야 합니다. Azure Storage는 스냅숏 격리를 사용 하 여 단일 파티션 내에서 쓰기 작업과 동시에 읽기 작업을 허용 합니다. 스냅숏 격리를 통해 모든 읽기 작업은 업데이트가 발생 하는 동안에도 데이터의 일관 된 스냅숏을 반환 합니다.

낙관적 또는 비관적 동시성 모델을 사용 하 여 blob 및 컨테이너에 대 한 액세스를 관리 하도록 선택할 수 있습니다. 전략을 명시적으로 지정 하지 않으면 기본적으로 마지막 기록기가 적용 됩니다.  

## <a name="optimistic-concurrency"></a>낙관적 동시성

Azure Storage는 저장 된 모든 개체에 식별자를 할당 합니다. 이 식별자는 개체에 대해 쓰기 작업이 수행 될 때마다 업데이트 됩니다. 식별자는 HTTP 프로토콜에 의해 정의 된 ETag 헤더의 HTTP GET 응답의 일부로 클라이언트에 반환 됩니다.

업데이트를 수행 하는 클라이언트는 조건부 헤더와 함께 원래 ETag를 전송 하 여 특정 조건이 충족 될 경우에만 업데이트가 발생 하도록 할 수 있습니다. 예를 들어, **Match** 헤더를 지정 하는 경우 Azure Storage 업데이트 요청에 지정 된 etag의 값이 업데이트 되는 개체의 etag와 동일한 지 확인 합니다. 조건부 헤더에 대 한 자세한 내용은 [Blob service 작업에 대 한 조건부 헤더 지정](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)을 참조 하세요.

이 프로세스는 대략적으로 다음과 같이 수행됩니다.  

1. Azure Storage에서 blob을 검색 합니다. 응답에는 개체의 현재 버전을 식별 하는 HTTP ETag 헤더 값이 포함 되어 있습니다.
1. Blob을 업데이트할 때 1 단계에서 받은 ETag 값을 쓰기 요청의 **If-Match** 조건부 헤더에 포함 합니다. Azure Storage 요청의 ETag 값을 blob의 현재 ETag 값과 비교 합니다.
1. Blob의 현재 ETag 값이 요청에 제공 된 **일치** 조건 헤더에 지정 된 것과 다른 경우 AZURE STORAGE는 HTTP 상태 코드 412 (전제 조건 실패)를 반환 합니다. 이 오류는 클라이언트가 첫 번째 프로세스를 검색 한 후 다른 프로세스에서 blob를 업데이트 했음을 나타냅니다.
1. Blob의 현재 ETag 값이 요청에서 **일치** 하는 조건 헤더의 etag와 동일한 버전인 경우 Azure Storage는 요청한 작업을 수행 하 고 blob의 현재 etag 값을 업데이트 합니다.  

다음 코드 예제에서는 blob에 대 한 ETag 값을 확인 하는 쓰기 요청에서 **If-Match** 조건을 생성 하는 방법을 보여 줍니다. Azure Storage는 blob의 현재 ETag가 요청에 제공 된 ETag와 동일한 지 여부를 평가 하 고 두 ETag 값이 일치 하는 경우에만 쓰기 작업을 수행 합니다. 다른 프로세스가 일시적으로 blob을 업데이트 한 경우 Azure Storage는 HTTP 412 (전제 조건 실패) 상태 메시지를 반환 합니다.  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

또한 Azure Storage는 다른 조건부 헤더를 지원 합니다. 예를 들어, 이후-수정 되지 않은 **경우** - **수정** 된 경우이 고, 그렇지 않으면- **없음-일치** 하는 경우입니다. 자세한 내용은 [Blob 서비스 작업의 조건부 헤더 지정](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)을 참조하세요.  

## <a name="pessimistic-concurrency-for-blobs"></a>Blob에 대한 비관적 동시성

단독 사용을 위해 Blob를 잠그려는 경우 Blob에 대한 임대를 획득할 수 있습니다. 임대를 획득 하는 경우 임대 기간을 지정 합니다. 유한 임대는 15 ~ 60 초 범위에서 유효 합니다. 임대는 무제한으로 제한 될 수 있으며,이는 배타적 잠금에도 적용 됩니다. 유한 임대를 갱신 하 여 확장 하 고 완료 되 면 임대를 해제할 수 있습니다. Azure Storage는 만료 되는 경우 자동으로 유한 임대를 해제 합니다.  

임대를 사용 하면 배타적 쓰기/공유 읽기 작업, 배타적 쓰기/배타적 읽기 작업, 공유 쓰기/배타적 읽기 작업을 포함 하 여 다양 한 동기화 전략을 지원할 수 있습니다. 임대가 있으면 Azure Storage는 임대 소유자에 대 한 쓰기 작업에 대 한 배타적 액세스를 적용 합니다. 그러나 독점 성을 읽기 작업을 보장 하려면 개발자가 모든 클라이언트 응용 프로그램에서 임대 ID를 사용 하 고 한 번에 한 클라이언트만 유효한 임대 ID를 사용 하도록 해야 합니다. 임대 ID를 포함하지 않는 읽기 작업에서는 공유 읽기가 수행됩니다.  

다음 코드 예제에서는 blob에 대 한 배타적 임대를 획득 하 고 임대 ID를 제공 하 여 blob의 내용을 업데이트 한 다음 임대를 해제 하는 방법을 보여 줍니다. 임대가 활성 상태이 고 임대 ID가 쓰기 요청에서 제공 되지 않으면 쓰기 작업이 실패 하 고 오류 코드 412 (전제 조건 실패)가 발생 합니다.  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>컨테이너에 대한 비관적 동시성

컨테이너에 대 한 임대를 사용 하면 배타적 쓰기/공유 읽기, 배타적 쓰기/배타적 읽기, 공유 쓰기/배타적 읽기 등 blob에 대해 지원 되는 것과 동일한 동기화 전략이 가능 합니다. 그러나 컨테이너의 경우 배타적 잠금은 삭제 작업에만 적용 됩니다. 임대가 활성 상태인 컨테이너를 삭제하려면 클라이언트가 삭제 요청에 활성 임대 ID를 포함해야 합니다. 다른 모든 컨테이너 작업은 임대 ID 없이 임대 된 컨테이너에서 성공 합니다.  

## <a name="next-steps"></a>다음 단계

* [Blob service 작업에 대 한 조건부 헤더 지정](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [컨테이너 임대](/rest/api/storageservices/lease-container)
* [Blob 임대](/rest/api/storageservices/lease-blob)
