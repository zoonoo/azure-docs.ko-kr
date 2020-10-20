---
title: 여러 Azure 데이터 원본을 인덱싱하는 C# 자습서
titleSuffix: Azure Cognitive Search
description: 인덱서를 사용하여 데이터를 여러 데이터 원본에서 단일 Azure Cognitive Search 인덱스로 가져오는 방법을 알아봅니다. 이 자습서 및 샘플 코드는 C#에 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c964e3c02148c461c601eab4bc5bfb0abb4ac052
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013307"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>자습서: .NET SDK를 사용하여 여러 데이터 원본에서 인덱스

Azure Cognitive Search는 데이터를 여러 데이터 원본에서 단일 통합 검색 인덱스로 가져오고, 분석하고, 인덱싱할 수 있습니다. 

이 자습서에서는 C# 및 .NET용 Azure SDK의 [Azure.Search.Documents](/dotnet/api/overview/azure/search) 클라이언트 라이브러리를 사용하여 Azure Cosmos DB의 호텔 데이터 샘플을 인덱싱하고 이를 Azure Blob Storage 문서에서 가져온 호텔 객실 세부 정보와 병합합니다. 결과는 복잡한 데이터 형식의 객실과 호텔 문서가 결합된 호텔 검색 인덱스가 됩니다.

이 자습서에서 수행하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 샘플 데이터 업로드 및 데이터 원본 만들기
> * 문서 키 식별
> * 인덱스 정의 및 만들기
> * Azure Cosmos DB의 호텔 데이터 인덱싱
> * Blob 스토리지의 호텔 객실 데이터 병합

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="overview"></a>개요

이 자습서에서는 새 클라이언트 라이브러리인 [Azure.Search.Documents](/dotnet/api/overview/azure/search) 버전 11.x를 사용하여 여러 인덱서를 만들고 실행합니다. 여기서는 둘 다에서 가져와서 단일 검색 인덱스를 채우는 인덱서를 구성할 수 있도록 두 개의 Azure 데이터 원본을 설정합니다. 병합을 지원하기 위해 두 데이터 세트에 공통된 값이 있어야 합니다. 이 샘플에서 해당 필드는 ID입니다. 매핑을 지원하기 위한 공통 필드가 있으면 인덱서에서 서로 다른 리소스의 데이터(Azure SQL의 정형 데이터, Blob 스토리지의 비정형 데이터 또는 Azure에서 [지원되는 데이터 원본](search-indexer-overview.md#supported-data-sources)의 조합)를 병합할 수 있습니다.

이 자습서의 완성된 최종 코드 버전은 다음 프로젝트에서 찾을 수 있습니다.

* [multiple-data-sources/v11(GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v11)

이 자습서는 Azure.Search.Documents(버전 11) 패키지를 사용하도록 업데이트되었습니다. 이전 버전의 .NET SDK는 GitHub의 [Microsoft.Azure.Search(버전 10) 코드 샘플](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v10)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Visual Studio](https://visualstudio.microsoft.com/)
+ [Azure Cognitive Search(버전 11.x) NuGet 패키지](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [만들기](search-create-service-portal.md) 또는 [기존 검색 서비스 찾기](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 이 자습서에서는 체험 서비스를 사용할 수 있습니다. 체험 검색 서비스에서는 인덱스, 인덱서 및 데이터 원본이 각각 3개로 제한됩니다. 이 자습서에서는 각각을 하나씩 만듭니다. 시작하기 전에 새 리소스를 수용할 수 있는 공간이 서비스에 있는지 확인하세요.

## <a name="1---create-services"></a>1 - 서비스 만들기

이 자습서에서 인덱싱 및 쿼리에는 Azure Cognitive Search, 하나의 데이터 세트에는 Azure Cosmos DB, 두 번째 데이터 세트에는 Azure Blob 스토리지를 사용합니다. 

가능한 경우 근접 연결과 관리 효율성을 위해 모든 서비스를 동일한 지역과 리소스 그룹에 만듭니다. 실제로 서비스는 어느 지역에나 있을 수 있습니다.

이 샘플에서는 7개의 가상 호텔을 설명하는 두 개의 작은 데이터 세트를 사용합니다. 한 세트는 호텔 자체를 설명하고 Azure Cosmos DB 데이터베이스에 로드됩니다. 다른 세트는 호텔 객실 세부 정보를 포함하고 있으며, Azure Blob Storage에 업로드할 7개의 개별 JSON 파일로 제공됩니다.

### <a name="start-with-cosmos-db"></a>Cosmos DB 시작

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음, Azure Cosmos DB 계정 개요 페이지를 탐색합니다.

1. **데이터 탐색기**, **새 데이터베이스**를 차례로 선택합니다.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="새 데이터베이스 만들기" border="false":::

1. **hotel-rooms-db**라는 이름을 입력합니다. 나머지 설정에 대해 기본값을 그대로 적용합니다.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="새 데이터베이스 만들기" border="false":::

1. 새 컨테이너를 만듭니다. 방금 만든 기존 데이터베이스를 사용합니다. 컨테이너 이름에 대해 **hotels**를 입력하고, 파티션 키에 대해 **/HotelId**를 사용합니다.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="새 데이터베이스 만들기" border="false":::

1. **hotels** 아래에서 **항목**을 선택한 다음, 명령 모음에서 **항목 업로드**를 클릭합니다. 프로젝트 폴더에서 **cosmosdb/HotelsDataSubset_CosmosDb.json** 파일을 찾아서 선택합니다.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="새 데이터베이스 만들기" border="false":::

1. [새로 고침] 단추를 사용하여 호텔 컬렉션의 항목 보기를 새로 고칩니다. 7개의 새 데이터베이스 문서가 나열됩니다.

1. 연결 문자열을 **키** 페이지에서 메모장으로 복사합니다. 이는 이후 단계에서 **appsettings.json**에 필요합니다. 제안된 "hotel-rooms-db"라는 데이터베이스 이름을 사용하지 않은 경우 데이터베이스 이름도 복사합니다.

### <a name="azure-blob-storage"></a>Azure Blob 스토리지

1. [Azure Portal](https://portal.azure.com)에 로그인하고, Azure 스토리지 계정으로 이동하고, **Blob**을 클릭한 다음, **+ 컨테이너**를 클릭합니다.

1. 샘플 호텔 객실 JSON 파일을 저장할 **hotel-rooms**라는 [Blob 컨테이너를 만듭니다](../storage/blobs/storage-quickstart-blobs-portal.md). 유효한 값에 대한 공용 액세스 수준을 설정할 수 있습니다.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="새 데이터베이스 만들기" border="false":::

1. 컨테이너를 만들었으면 연 다음, 명령 모음에서 **업로드**를 선택합니다. 샘플 파일이 포함된 폴더로 이동합니다. 모두 선택한 다음, **업로드**를 클릭합니다.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="새 데이터베이스 만들기" border="false":::

1. 스토리지 계정 이름과 연결 문자열을 **액세스 키** 페이지에서 메모장으로 복사합니다. 두 값은 모두 이후 단계에서 **appsettings.json**에 필요합니다.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

세 번째 구성 요소는 [포털에서 만들](search-create-service-portal.md) 수 있는 Azure Cognitive Search입니다. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search용 관리 API 키와 URL 복사

검색 서비스를 인증하려면 서비스 URL과 액세스 키가 필요합니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   :::image type="content" source="media/search-get-started-nodejs/service-name-and-keys.png" alt-text="새 데이터베이스 만들기" border="false":::

유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="2---set-up-your-environment"></a>2 - 환경 설정

1. Visual Studio를 시작하고, **도구** 메뉴에서 **NuGet 패키지 관리자**, **솔루션용 NuGet 패키지 관리...** 를 차례로 선택합니다. 

1. **찾아보기** 탭에서 **Azure.Search.Documents**(버전 11.0 이상)를 찾아서 설치합니다. 설치를 완료하려면 추가 대화 상자를 클릭해야 합니다.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="새 데이터베이스 만들기" border="false":::

1. **Microsoft.Extensions.Configuration** 및 **Microsoft.Extensions.Configuration.Json** NuGet 패키지도 검색하여 설치합니다.

1. **/v11/AzureSearchMultipleDataSources.sln** 솔루션 파일을 엽니다.

1. 솔루션 탐색기에서 연결 정보를 추가하도록 **appsettings.json** 파일을 편집합니다.  

    ```json
    {
      "SearchServiceUri": "<YourSearchServiceURL>",
      "SearchServiceAdminApiKey": "<YourSearchServiceAdminApiKey>",
      "BlobStorageAccountName": "<YourBlobStorageAccountName>",
      "BlobStorageConnectionString": "<YourBlobStorageConnectionString>",
      "CosmosDBConnectionString": "<YourCosmosDBConnectionString>",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

처음 두 항목은 검색 서비스의 URL 및 관리자 키입니다. 전체 엔드포인트(예: `https://mydemo.search.windows.net`)를 사용합니다.

다음 항목에서는 Azure Blob Storage 및 Azure Cosmos DB 데이터 원본에 대한 계정 이름과 연결 문자열 정보를 지정합니다.

## <a name="3---map-key-fields"></a>3 - 키 필드 매핑

콘텐츠를 병합하려면 두 데이터 스트림이 모두 검색 인덱스에 있는 동일한 문서를 대상으로 해야 합니다. 

Azure Cognitive Search에서 키 필드는 각 문서를 고유하게 식별합니다. 모든 검색 인덱스는 `Edm.String`형식의 키 필드를 정확히 하나만 포함해야 합니다. 이 키 필드는 인덱스에 추가되는 데이터 원본의 각 문서에 대해 있어야 합니다. (실제로 유일한 필수 필드임)

여러 데이터 원본에서 데이터를 인덱싱하는 경우 두 개의 물리적으로 고유한 원본 문서의 데이터를 결합된 인덱스의 새 검색 문서로 병합하는 공통 문서 키가 들어오는 각 행 또는 문서에 포함되어 있는지 확인합니다. 

인덱스에 대한 의미 있는 문서 키를 식별하고 두 데이터 원본에 모두 있는지 확인하기 위한 몇 가지 사전 계획이 필요한 경우가 많습니다. 이 데모에서는 Cosmos DB의 각 호텔에 대한 `HotelId` 키가 Blob 스토리지의 rooms JSON Blob에도 있습니다.

Azure Cognitive Search 인덱서는 인덱싱 프로세스 중에 필드 매핑을 사용하여 데이터 필드의 이름을 변경하고 심지어 형식도 다시 지정할 수 있으므로 원본 데이터를 올바른 인덱스 필드로 보낼 수 있습니다. 예를 들어 Cosmos DB에서 호텔 식별자는 **`HotelId`** 라고 합니다. 그러나 호텔 객실에 대한 JSON Blob 파일에서 호텔 식별자의 이름은 **`Id`** 입니다. 이는 프로그램에서 **`Id`** 필드를 Blob에서 인덱서의 **`HotelId`** 키 필드에 매핑하여 처리합니다.

> [!NOTE]
> 대부분의 경우 일부 인덱서에서 기본적으로 만든 것과 같이 자동으로 생성된 문서 키는 결합된 인덱스에 적합한 문서 키를 만들지 않습니다. 일반적으로 데이터 원본에 이미 있거나 쉽게 추가할 수 있는 의미 있고 고유한 키 값을 사용하려고 합니다.

## <a name="4---explore-the-code"></a>4 - 코드 살펴보기

데이터와 구성 설정이 준비되면 **/v11/AzureSearchMultipleDataSources.sln**의 샘플 프로그램을 빌드하고 실행할 준비가 됩니다.

이 간단한 C#/.NET 콘솔 앱에서 수행하는 작업은 다음과 같습니다.

* C# Hotel 클래스(Address 및 Room 클래스도 참조)의 데이터 구조를 기반으로 하여 새 인덱스를 만듭니다.
* 새 데이터 원본과 Azure Cosmos DB 데이터를 인덱스 필드에 매핑하는 인덱서를 만듭니다. 이 두 항목은 모두 Azure Cognitive Search의 개체입니다.
* 인덱서를 실행하여 Cosmos DB에서 Hotel 데이터를 로드합니다.
* 두 번째 데이터 원본 및 JSON Blob 데이터를 인덱스 필드에 매핑하는 인덱서를 만듭니다.
* 두 번째 인덱서를 실행하여 Blob 스토리지에서 Rooms 데이터를 로드합니다.

 프로그램을 실행하기 전에 잠시 시간을 내어 이 샘플에 대한 코드, 인덱스 및 인덱서 정의를 살펴봅니다. 관련된 코드가 다음과 같은 두 개의 파일에 있습니다.

  + **Hotel.cs**에는 인덱스를 정의하는 스키마가 포함되어 있습니다.
  + **Program.cs**에는 Azure Cognitive Search 인덱스, 데이터 원본 및 인덱서를 만들고 결합된 결과를 인덱스에 로드하는 함수가 포함되어 있습니다.

### <a name="create-an-index"></a>인덱스 만들기

이 샘플 프로그램에서는 [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync)를 사용하여 Azure Cognitive Search 인덱스를 정의하고 만듭니다. [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) 클래스를 활용하여 C# 데이터 모델 클래스에서 인덱스 구조를 생성합니다.

데이터 모델은 Address 및 Room 클래스에 대한 참조를 포함한 Hotel 클래스에서 정의됩니다. FieldBuilder는 여러 클래스 정의를 드릴다운하여 인덱스에 대한 복합 데이터 구조를 생성합니다. 메타데이터 태그는 검색 가능 여부 또는 정렬 가능 여부와 같이 각 필드의 특성을 정의하는 데 사용됩니다.

이 예제를 두 번 이상 실행하려는 경우 프로그램에서 먼저 동일한 이름의 기존 인덱스를 삭제한 후에 새 인덱스를 만듭니다.

**Hotel.cs** 파일의 다음 코드 조각에서는 단일 필드 및 그 뒤에 다른 데이터 모델 클래스인 Room[]에 대한 참조를 보여 줍니다. 이는 차례로 **Room.cs** 파일(보여 주지 않음)에 정의됩니다.

```csharp
. . .
[SimpleField(IsFilterable = true, IsKey = true)]
public string HotelId { get; set; }

[SearchableField(IsFilterable = true, IsSortable = true)]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

**Program.cs** 파일에서 [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex)는 `FieldBuilder.Build` 메서드에서 생성된 이름과 필드 컬렉션으로 정의되어 다음과 같이 만들어집니다.

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Azure Cosmos DB 데이터 원본 및 인덱서 만들기

다음으로, 주 프로그램에는 호텔 데이터에 대한 Azure Cosmos DB 데이터 원본을 만드는 논리가 포함됩니다.

먼저 Azure Cosmos DB 데이터베이스 이름을 연결 문자열에 연결합니다. 그런 다음, [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) 개체를 정의합니다.

```csharp
private static async Task CreateAndRunCosmosDbIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    // Append the database name to the connection string
    string cosmosConnectString =
        configuration["CosmosDBConnectionString"]
        + ";Database="
        + configuration["CosmosDBDatabaseName"];

    SearchIndexerDataSourceConnection cosmosDbDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["CosmosDBDatabaseName"],
        type: SearchIndexerDataSourceType.CosmosDb,
        connectionString: cosmosConnectString,
        container: new SearchIndexerDataContainer("hotels"));

    // The Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(cosmosDbDataSource);
```

데이터 원본이 만들어지면 프로그램에서 **hotel-rooms-cosmos-indexer**라는 Azure Cosmos DB 인덱서를 설정합니다.

프로그램에서 기존 인덱서를 동일한 이름으로 업데이트하여 기존 인덱서를 위 코드의 내용으로 덮어씁니다. 이 예제를 두 번 이상 실행하려는 경우에도 재설정 및 실행 작업이 포함됩니다.

다음 예제에서는 하루에 한 번 실행되도록 인덱서에 대한 일정을 정의합니다. 나중에 인덱서를 자동으로 다시 실행하지 않도록 하려면 이 호출에서 schedule 속성을 제거하면 됩니다.

```csharp
SearchIndexer cosmosDbIndexer = new SearchIndexer(
    name: "hotel-rooms-cosmos-indexer",
    dataSourceName: cosmosDbDataSource.Name,
    targetIndexName: indexName)
{
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

// Indexers keep metadata about how much they have already indexed.
// If we already ran the indexer, it "remembers" and does not run again.
// To avoid this, reset the indexer if it exists.
try
{
    await indexerClient.GetIndexerAsync(cosmosDbIndexer.Name);
    // Reset the indexer if it exists.
    await indexerClient.ResetIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404)
{
    // If the indexer does not exist, 404 will be thrown.
}

await indexerClient.CreateOrUpdateIndexerAsync(cosmosDbIndexer);

Console.WriteLine("Running Cosmos DB indexer...\n");

try
{
    // Run the indexer.
    await indexerClient.RunIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 429)
{
    Console.WriteLine("Failed to run indexer: {0}", ex.Message);
}
```

이 예제에는 실행 중에 발생할 수 있는 오류를 보고하는 간단한 try-catch 블록이 포함되어 있습니다.

Azure Cosmos DB 인덱서가 실행되면 검색 인덱스에 샘플 호텔 문서의 전체 세트가 포함됩니다. 그러나 Azure Cosmos DB 데이터 원본에는 객실 세부 정보가 생략되어 있으므로 각 호텔의 객실 필드는 빈 배열이 됩니다. 다음으로, 프로그램에서 Blob 스토리지로부터 객실 데이터를 가져와서 로드하고 병합합니다.

### <a name="create-blob-storage-data-source-and-indexer"></a>Blob 스토리지 데이터 원본 및 인덱서 만들기

객실 세부 정보를 가져오기 위해 프로그램에서 먼저 개별 JSON Blob 파일 세트를 참조하도록 Blob 스토리지 데이터 원본을 설정합니다.

```csharp
private static async Task CreateAndRunBlobIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    SearchIndexerDataSourceConnection blobDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["BlobStorageAccountName"],
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["BlobStorageConnectionString"],
        container: new SearchIndexerDataContainer("hotel-rooms"));

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(blobDataSource);
```

데이터 원본이 만들어지면 프로그램에서 아래와 같이 **hotel-rooms-blob-indexer**라는 Blob 인덱서를 설정합니다.

JSON Blob에는 **`HotelId`** 대신 **`Id`** 라는 키 필드가 있습니다. 이 코드는 `FieldMapping` 클래스를 사용하여 **`Id`** 필드 값을 인덱스의 **`HotelId`** 문서 키로 보내도록 인덱서에 지시합니다.

Blob 스토리지 인덱서는 [IndexingParameters](/dotnet/api/azure.search.documents.indexes.models.indexingparameters)를 사용하여 구문 분석 모드를 지정할 수 있습니다. Blob이 단일 문서를 나타내는지, 아니면 동일한 Blob 내의 여러 문서를 나타내는지에 따라 다른 구문 분석 모드를 설정해야 합니다. 이 예제에서는 각 Blob이 단일 JSON 문서를 나타내므로 코드에서 `json` 구문 분석 모드를 사용합니다. JSON Blob에 대한 인덱서 구문 분석 매개 변수에 대한 자세한 내용은 [JSON Blob 인덱싱](search-howto-index-json-blobs.md)을 참조하세요.

이 예제에서는 하루에 한 번 실행되는 인덱서의 일정을 정의합니다. 나중에 인덱서를 자동으로 다시 실행하지 않도록 하려면 이 호출에서 schedule 속성을 제거하면 됩니다.

```csharp
// Map the Id field in the Room documents to the HotelId key field in the index
List<FieldMapping> map = new List<FieldMapping> {
    new FieldMapping("Id")
    {
        TargetFieldName =  "HotelId"
    }
};

IndexingParameters parameters = new IndexingParameters();
parameters.Configuration.Add("parsingMode", "json");

SearchIndexer blobIndexer = new SearchIndexer(
    name: "hotel-rooms-blob-indexer",
    dataSourceName: blobDataSource.Name,
    targetIndexName: indexName)
{
    Parameters = parameters,
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

blobIndexer.FieldMappings.Add(new FieldMapping("Id") { TargetFieldName = "HotelId" });

// Reset the indexer if it already exists
try
{
    await indexerClient.GetIndexerAsync(blobIndexer.Name);
    await indexerClient.ResetIndexerAsync(blobIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404) { }

await indexerClient.CreateOrUpdateIndexerAsync(blobIndexer);

try
{
    // Run the indexer.
    await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
    Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

인덱스가 이미 Azure Cosmos DB 데이터베이스의 호텔 데이터로 채워졌으므로, Blob 인덱서는 인덱스의 기존 문서를 업데이트하고 객실 세부 정보를 추가합니다.

> [!NOTE]
> 두 데이터 원본에 키가 아닌 동일한 필드가 있고 해당 필드 내의 데이터가 일치하지 않으면 가장 최근에 실행된 인덱서의 값이 인덱스에 포함됩니다. 이 예제에서는 두 데이터 원본에서 모두 **HotelName** 필드를 포함하고 있습니다. 키 값이 동일한 문서에 대해 어떤 이유로 이 필드의 데이터가 다른 경우 가장 최근에 인덱싱된 데이터 원본의 **HotelName** 데이터가 인덱스에 저장되는 값이 됩니다.

## <a name="5---search"></a>5 - 검색

포털의 [**검색 탐색기**](search-explorer.md)를 사용하여 프로그램이 실행된 후 채워진 검색 인덱스를 검색할 수 있습니다.

Azure Portal에서 검색 서비스 **개요** 페이지를 열고, **인덱스** 목록에서 **hotel-rooms-sample** 인덱스를 찾습니다.

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="새 데이터베이스 만들기" border="false":::

목록에서 hotel-rooms-sample 인덱스를 클릭합니다. 인덱스에 대한 검색 탐색기 인터페이스가 표시됩니다. "Luxury"(특실)와 같은 용어에 대한 쿼리를 입력합니다. 결과에서 하나 이상의 문서가 표시되며, 이 문서에는 객실 배열에 객실 개체 목록이 표시되어 있습니다.

## <a name="reset-and-rerun"></a>다시 설정하고 다시 실행

개발의 초기 실험 단계에서 설계 반복에 대한 가장 실용적인 방법은 Azure Cognitive Search에서 개체를 삭제하고 코드에서 이를 다시 작성하도록 허용하는 것입니다. 리소스 이름은 고유합니다. 개체를 삭제하면 동일한 이름을 사용하여 개체를 다시 만들 수 있습니다.

샘플 코드에서는 기존 개체를 확인하고, 프로그램을 다시 실행할 수 있도록 해당 개체를 삭제하거나 업데이트합니다.

또한 포털을 사용하여 인덱스, 인덱서 및 데이터 원본을 삭제할 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

사용자 고유의 구독에서 작업하는 경우 프로젝트의 끝에서 더 이상 필요하지 않은 리소스를 제거하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 [모든 리소스] 또는 [리소스 그룹] 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 여러 원본에서 데이터를 수집하는 개념을 잘 알고 있으므로 Cosmos DB부터 시작하여 인덱서 구성을 자세히 살펴보겠습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB 인덱서 구성](search-howto-index-cosmosdb.md)
