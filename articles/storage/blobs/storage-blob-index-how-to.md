---
title: Blob 인덱스 태그를 사용 하 여 Azure Blob Storage에서 데이터 관리 및 찾기
description: Blob 인덱스 태그를 사용 하 여 blob 개체를 분류, 관리 및 쿼리 하는 방법에 대 한 예를 참조 하세요.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e3e16b71d52edd9ab4eaf55651567b95e334b84
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961790"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Blob 인덱스 태그 (미리 보기)를 사용 하 여 Azure Blob Storage에서 데이터 관리 및 찾기

Blob 인덱스 태그는 키-값 태그 특성을 사용 하 여 저장소 계정의 데이터를 분류 합니다. 이러한 태그는 데이터를 쉽게 찾을 수 있도록 자동으로 인덱싱하고 검색 가능한 다차원 인덱스로 노출 됩니다. 이 문서에서는 Blob 인덱스 태그를 사용하여 데이터를 설정하고 가져오고 검색하는 방법을 보여 줍니다.

> [!NOTE]
> Blob 인덱스는 공개 미리 보기 상태 이며 **캐나다 중부**, **캐나다 동부**, **프랑스 중부** 및 **프랑스 남부** 지역에서 사용할 수 있습니다. 알려진 문제 및 제한과 함께이 기능에 대해 자세히 알아보려면 [Blob 인덱스 태그를 사용 하 여 Azure blob 데이터 관리 및 찾기 (미리 보기)](storage-manage-find-blobs.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

# <a name="portal"></a>[포털](#tab/azure-portal)

- Blob 인덱스 미리 보기에 대 한 액세스를 위해 등록 및 승인 된 Azure 구독
- [Azure Portal](https://portal.azure.com/) 에 대 한 액세스

# <a name="net"></a>[.NET](#tab/net)

Blob 인덱스가 미리 보기 상태 이므로 .NET storage 패키지는 미리 보기 NuGet 피드에 릴리스됩니다. 이 라이브러리는 미리 보기 기간 동안 변경 될 수 있습니다.

1. Visual Studio 프로젝트를 설정 하 여 .NET 용 Azure Blob Storage 클라이언트 라이브러리 v12 시작 합니다. 자세히 알아보려면 [.Net 빠른](storage-quickstart-blobs-dotnet.md) 시작을 참조 하세요.

2. NuGet 패키지 관리자에서 **Azure.** 12.7.0 패키지를 찾고 프로젝트에 버전 **-preview. 1** 이상을 설치 합니다. PowerShell 명령을 실행할 수도 있습니다. `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

   방법에 대한 자세한 내용은 [패키지 찾기 및 설치](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)를 참조하세요.

3. 코드 파일 맨 위에 다음 using 문을 추가합니다.

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>인덱스 태그를 사용하여 새 Blob 업로드

이 작업은 [Storage Blob Data Owner](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 사용자 지정 azure 역할을 통해 [azure 리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) 에 대 한 사용 권한을 부여 받은 저장소 Blob 데이터 소유자 또는 보안 주체에 의해 수행 될 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정을 선택합니다. 

2. **Blob service** 아래 컨테이너 **옵션으로** 이동 하 여 컨테이너를 선택 합니다.

3. **업로드** 단추를 선택 하 고 로컬 파일 시스템을 검색 하 여 블록 blob으로 업로드할 파일을 찾습니다.

4. **고급** 드롭다운을 확장하고 **Blob 인덱스 태그** 섹션으로 이동합니다.

5. 데이터에 적용할 키/값 Blob 인덱스 태그를 입력합니다.

6. **업로드** 단추를 선택하여 Blob을 업로드합니다.

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="인덱스 태그를 사용 하 여 blob을 업로드 하는 방법을 보여 주는 Azure Portal의 스크린샷":::

# <a name="net"></a>[.NET](#tab/net)

다음 예제에서는 태그를 만드는 동안 태그를 설정 하 여 추가 Blob을 만드는 방법을 보여 줍니다.

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Blob 인덱스 태그 가져오기, 설정 및 업데이트

Blob 인덱스 태그 가져오기는 [Storage Blob Data Owner](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 사용자 지정 azure 역할을 통해 [azure 리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) 에 대 한 사용 권한을 부여 받은 저장소 blob 데이터 소유자 또는 보안 주체에서 수행할 수 있습니다.

Blob 인덱스 태그를 설정 하 고 업데이트 하는 작업 [Storage Blob Data Owner](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 은 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 사용자 지정 azure 역할을 통해 [azure 리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) 에 대 한 권한을 부여 받은 저장소 blob 데이터 소유자 또는 보안 주체에 의해 수행 될 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정을 선택합니다. 

2. **Blob Service** 아래의 **컨테이너** 옵션으로 이동하고 컨테이너를 선택합니다.

3. 선택한 컨테이너 내의 blob 목록에서 blob을 선택 합니다.

4. Blob 개요 탭은 **Blob 인덱스 태그** 를 포함하여 Blob의 속성을 표시합니다.

5. Blob에 대한 키/값 인덱스 태그를 가져오거나 설정하거나 수정하거나 삭제할 수 있습니다.

6. **저장** 단추를 선택하여 Blob에 대한 업데이트를 확인합니다.

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Blob에서 인덱스 태그를 가져오고, 설정 하 고, 업데이트 하 고, 삭제 하는 방법을 보여 주는 Azure Portal의 스크린샷":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Blob 인덱스 태그를 사용 하 여 데이터 필터링 및 찾기

이 작업은 [Storage Blob Data Owner](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` 사용자 지정 azure 역할을 통해 [azure 리소스 공급자 작업](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) 에 대 한 사용 권한을 부여 받은 저장소 Blob 데이터 소유자 또는 보안 주체에 의해 수행 될 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal 내에서 blob 인덱스 태그 필터는 매개 변수를 자동으로 적용 `@container` 하 여 선택한 컨테이너의 범위를 적용 합니다. 전체 저장소 계정에서 태그가 지정 된 데이터를 필터링 하 고 찾으려면 REST API, Sdk 또는 도구를 사용 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정을 선택합니다. 

2. **Blob service** 아래 컨테이너 **옵션으로** 이동 하 여 컨테이너를 선택 합니다.

3. **Blob 인덱스 태그 필터** 단추를 선택하여 선택한 컨테이너 내에서 필터링합니다.

4. Blob 인덱스 태그 키 및 태그 값 입력

5. **Blob 인덱스 태그 필터** 단추를 선택하여 추가 태그 필터를 추가합니다(최대 10개).

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="인덱스 태그를 사용 하 여 태그가 지정 된 blob을 필터링 하 고 찾는 방법을 보여 주는 Azure Portal의 스크린샷":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Blob 인덱스 태그 필터를 사용하여 수명 주기 관리

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정을 선택합니다. 

2. **Blob Service** 에서 **수명 주기 관리** 옵션으로 이동합니다.

3. ‘규칙 추가’를 선택한 다음, 작업 세트 양식 필드를 채웁니다.

4. 접두사 일치 및 blob 인덱스 일치에 대 한 선택적 필터를 추가 하려면 **필터** 설정을 선택 합니다.

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="수명 주기 관리에 대 한 인덱스 태그를 추가 하는 방법을 보여 주는 Azure Portal의 스크린샷":::

5. **검토 + 추가** 를 선택 하 여 규칙 설정을 검토 합니다.

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Blob 인덱스 태그를 사용 하 여 수명 주기 관리 규칙을 보여 주는 Azure Portal의 스크린샷 필터 예":::

6. **추가** 를 선택하여 수명 주기 관리 정책에 새 규칙을 적용합니다.

# <a name="net"></a>[.NET](#tab/net)

[수명 주기 관리](storage-lifecycle-management-concepts.md) 정책은 제어 평면 수준의 각 스토리지 계정에 적용됩니다. .NET의 경우 [Microsoft Azure Management Storage 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) 버전 16.0.0 이상을 설치 합니다.

---

## <a name="next-steps"></a>다음 단계

 - Blob 인덱스 태그에 대해 자세히 알아보세요. [blob 인덱스 태그를 사용 하 여 Azure blob 데이터 관리 및 찾기 (미리 보기)](storage-manage-find-blobs.md ) 를 참조 하세요.
 - 수명 주기 관리에 대해 자세히 알아보세요. [Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md) 를 참조 하세요.
