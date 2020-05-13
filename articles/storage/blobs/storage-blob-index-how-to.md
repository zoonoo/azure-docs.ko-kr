---
title: Blob 인덱스를 활용 하 여 Azure Blob storage에서 데이터 관리 및 찾기
description: Blob 인덱스 태그를 사용 하 여 blob 개체를 범주화 하 고, 관리 하 고, 쿼리 하는 방법에 대 한 예를 참조 하세요.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 9ba151aa1ddc7f4b14d5f4ec7f1990e2fd760602
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121238"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Blob 인덱스 태그 (미리 보기)를 활용 하 여 Azure Blob storage에서 데이터 관리 및 찾기

Blob 인덱스 태그는 키-값 태그 특성을 활용 하 여 저장소 계정에서 데이터를 분류 합니다. 이러한 태그는 데이터를 쉽게 찾을 수 있도록 쿼리 가능한 다차원 인덱스로 자동으로 인덱싱됩니다. 이 문서에서는 blob 인덱스 태그를 사용 하 여 데이터를 설정 하 고 가져오고 검색 하는 방법을 보여 줍니다.

Blob 인덱스에 대해 자세히 알아보려면 [Blob 인덱스를 사용 하 여 Azure Blob Storage에서 데이터 관리 및 찾기 (미리 보기)](storage-manage-find-blobs.md)를 참조 하세요.

> [!NOTE]
> Blob 인덱스는 공개 미리 보기 상태 이며 **프랑스 중부** 및 **프랑스 남부** 지역에서 사용할 수 있습니다. 알려진 문제 및 제한과 함께이 기능에 대해 자세히 알아보려면 [Blob 인덱스를 사용 하 여 Azure Blob Storage에서 데이터 관리 및 찾기 (미리 보기)](storage-manage-find-blobs.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항
# <a name="portal"></a>[포털](#tab/azure-portal)
- 구독을 등록 하 고 Blob 인덱스 미리 보기에 대 한 액세스 승인
- [Azure Portal](https://portal.azure.com/) 에 대 한 액세스

# <a name="net"></a>[.NET](#tab/net)
Blob 인덱스가 공개 미리 보기 상태 이므로 .NET storage 패키지는 미리 보기 NuGet 피드에 릴리스됩니다. 이 라이브러리는 현재 사이에서 변경 될 수 있으며 공식이 될 때 변경 될 수 있습니다. 

1. Visual Studio에서 `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` NuGet 패키지 소스에 URL을 추가 합니다. 

   방법을 알아보려면 [패키지 원본](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)을 참조 하세요.

2. NuGet 패키지 관리자에서 **Azure.** 12.5.0 패키지를 찾고 **20200422.2** 버전을 프로젝트에 설치 합니다. 명령을 실행할 수도 있습니다.```Install-Package Azure.Storage.Blobs -Version12.5.0-dev.20200422.2```

   방법에 대 한 자세한 내용은 [패키지 찾기 및 설치](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)를 참조 하세요.

3. 다음 using 문을 코드 파일의 맨 위에 추가 합니다.
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

## <a name="upload-a-new-blob-with-index-tags"></a>인덱스 태그를 사용 하 여 새 blob 업로드
# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에서 저장소 계정을 선택 합니다. 

2. **Blob Service**아래의 **컨테이너** 옵션으로 이동 하 여 컨테이너를 선택 합니다.

3. **업로드** 단추를 선택하여 업로드 블레이드를 열고 로컬 파일 시스템을 탐색하여 블록 Blob으로 업로드할 파일을 찾습니다.

4. **고급** 드롭다운을 확장 하 고 **Blob 인덱스 태그** 섹션으로 이동 합니다.

5. 데이터에 적용할 키/값 blob 인덱스 태그를 입력 합니다.

6. **업로드** 단추를 선택 하 여 blob을 업로드 합니다.

![Blob 인덱스 태그를 사용 하 여 데이터 업로드](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
다음 예제에서는 태그를 만드는 동안 태그를 설정 하 여 추가 blob을 만드는 방법을 보여 줍니다.
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
         
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
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
# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에서 저장소 계정을 선택 합니다. 

2. **Blob Service**아래의 **컨테이너** 옵션으로 이동 하 여 컨테이너를 선택 합니다.

3. 선택한 컨테이너 내의 blob 목록에서 원하는 blob을 선택 합니다.

4. Blob 개요 탭은 blob **인덱스 태그** 를 포함 하 여 blob의 속성을 표시 합니다.

5. Blob에 대 한 키/값 인덱스 태그를 가져오거나 설정 하거나 수정 하거나 삭제할 수 있습니다.

6. **저장** 단추를 선택 하 여 blob에 대 한 업데이트를 확인 합니다.

![개체에서 blob 인덱스 태그를 가져오고, 설정 하 고, 업데이트 하 고, 삭제 합니다.](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

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

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
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

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal 내에서 Blob 인덱스 태그 필터는 매개 변수를 자동으로 적용 `@container` 하 여 선택한 컨테이너의 범위를 적용 합니다. 전체 저장소 계정에서 태그가 지정 된 데이터를 필터링 하 고 찾으려면 REST API, Sdk 또는 도구를 사용 하세요.

1. [Azure Portal](https://portal.azure.com/)에서 저장소 계정을 선택 합니다. 

2. **Blob Service**아래의 **컨테이너** 옵션으로 이동 하 여 컨테이너를 선택 합니다.

3. 선택한 컨테이너 내에서 필터링 하려면 **Blob 인덱스 태그 필터** 단추를 선택 합니다.

4. Blob 인덱스 태그 키 및 태그 값 입력

5. **Blob 인덱스 태그 필터** 단추를 선택 하 여 추가 태그 필터 추가 (최대 10 개)

![Blob 인덱스 태그를 사용 하 여 태그가 지정 된 개체 필터링 및 찾기](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob Index queries and selection
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
           
          // Blob Index tags to upload
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

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
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

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Blob 인덱스 태그 필터를 사용 하 여 수명 주기 관리

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에서 저장소 계정을 선택 합니다. 

2. **Blob Service** 에서 **수명 주기 관리** 옵션으로 이동 합니다.

3. *규칙 추가* 를 선택한 다음 작업 집합 양식 필드를 채웁니다.

4. 필터 집합을 선택 하 여 접두사 일치 및 Blob 인덱스 일치에 대 한 선택적 필터 추가- ![ 수명 주기 관리에 대 한 blob 인덱스 태그 추가 필터](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. **검토 + 추가** 를 선택 하 여 ![ blob 인덱스 태그를 사용 하 여 규칙 설정 수명 주기 관리 규칙 필터 예제를 검토 합니다.](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. **추가** 를 선택 하 여 새 규칙을 수명 주기 관리 정책에 적용 합니다.

# <a name="net"></a>[.NET](#tab/net)
[수명 주기 관리](storage-lifecycle-management-concepts.md) 정책은 제어 평면 수준의 각 저장소 계정에 적용 됩니다. .NET의 경우 수명 주기 관리 규칙 내에서 Blob 인덱스 일치 필터를 활용 하려면 [Microsoft Azure Management Storage Library 버전 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) 이상을 설치 합니다.

---

## <a name="next-steps"></a>다음 단계

Blob 인덱스에 대해 자세히 알아보세요. [Blob 인덱스를 사용 하 여 Azure Blob Storage에서 데이터 관리 및 찾기 (미리 보기)](storage-manage-find-blobs.md ) 를 참조 하세요.

수명 주기 관리에 대해 자세히 알아보세요. [Azure Blob storage 수명 주기 관리를](storage-lifecycle-management-concepts.md) 참조 하세요.
