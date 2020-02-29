---
title: 'C#자습서: 여러 데이터 원본 인덱싱'
titleSuffix: Azure Cognitive Search
description: 인덱서를 사용 하 여 여러 데이터 원본에서 단일 Azure Cognitive Search 인덱스로 데이터를 가져오는 방법에 대해 알아봅니다. 이 자습서 및 샘플 코드는에 C#있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 6408689deec7de365ede86665a0eaeb0bd0de64b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196572"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>자습서:에서 여러 데이터 소스의 데이터 인덱싱C#

Azure Cognitive Search는 데이터를 여러 데이터 원본에서 단일 결합형 검색 인덱스로 가져오고, 분석하고, 인덱싱할 수 있습니다. 이렇게 하면 정형 데이터가 텍스트, HTML 또는 JSON 문서와 같은 다른 원본의 비정형 데이터, 심지어 일반 텍스트 데이터를 사용하여 집계되는 상황을 지원합니다.

이 자습서에서는 Azure Cosmos DB 데이터 원본의 호텔 데이터를 인덱싱하고, 이를 Azure Blob Storage 문서에서 가져온 호텔 객실 세부 정보와 병합하는 방법에 대해 설명합니다. 결과는 복잡한 데이터 형식이 포함된 결합형 호텔 검색 인덱스가 됩니다.

이 자습서에서는 C# 및 [.net SDK](https://aka.ms/search-sdk) 를 사용 하 여 다음 작업을 수행 합니다.

> [!div class="checklist"]
> * 샘플 데이터 업로드 및 데이터 원본 만들기
> * 문서 키 식별
> * 인덱스 정의 및 만들기
> * Azure Cosmos DB의 호텔 데이터 인덱싱
> * Blob 스토리지의 호텔 객실 데이터 병합

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [기존 검색 서비스](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [만들기](search-create-service-portal.md) 또는 찾기 

> [!Note]
> 이 자습서에서는 무료 서비스를 사용할 수 있습니다. 무료 검색 서비스는 세 개의 인덱스, 세 개의 인덱서 및 세 개의 데이터 원본으로 제한 합니다. 이 자습서에서는 각각을 하나씩 만듭니다. 시작 하기 전에 새 리소스를 수락 하는 서비스에 대 한 공간이 있는지 확인 합니다.

## <a name="download-files"></a>파일 다운로드

1. GitHub에서 [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) 샘플 리포지토리를 찾습니다.
1. **복제 또는 다운로드**를 선택하고, 리포지토리의 프라이빗 로컬 복사본을 만듭니다.
1. Visual Studio 2019을 열고 Microsoft Azure Cognitive Search NuGet 패키지를 설치 합니다 (아직 설치 하지 않은 경우). **도구** 메뉴에서 **nuget 패키지 관리자** 를 선택 하 고 **솔루션에 대 한 nuget 패키지 관리**...를 선택 합니다. **찾아보기** 탭에서 **9.0.1 (버전** 이상)를 찾아서 설치 합니다. 설치를 완료하려면 추가 대화 상자를 클릭해야 합니다.

    ![NuGet을 사용하여 Azure 라이브러리 추가](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Visual Studio를 사용하여 로컬 리포지토리로 이동한 다음, **AzureSearchMultipleDataSources.sln** 솔루션 파일을 엽니다.

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

Azure Cognitive Search 서비스와 상호 작용하려면 서비스 URL과 액세스 키가 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인 하 고 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키는 요청을 보내는 애플리케이션과 이 요청을 처리하는 서비스 간에 요청별로 신뢰를 설정합니다.

## <a name="prepare-sample-azure-cosmos-db-data"></a>샘플 Azure Cosmos DB 데이터 준비

이 샘플에서는 7개의 가상 호텔을 설명하는 두 개의 작은 데이터 세트를 사용합니다. 한 세트는 호텔 자체를 설명하고 Azure Cosmos DB 데이터베이스에 로드됩니다. 다른 세트는 호텔 객실 세부 정보를 포함하고 있으며, Azure Blob Storage에 업로드할 7개의 개별 JSON 파일로 제공됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 한 다음 Azure Cosmos DB 계정 개요 페이지를 탐색 합니다.

1. **데이터 탐색기** 선택한 다음 **새 데이터베이스**를 선택 합니다.

   ![새 데이터베이스 만들기](media/tutorial-multiple-data-sources/cosmos-newdb.png "새 데이터베이스 만들기")

1. **호텔-대화방-db**이름을 입력 합니다. 나머지 설정에 대해 기본값을 적용 합니다.

   ![데이터베이스 구성](media/tutorial-multiple-data-sources/cosmos-dbname.png "데이터베이스 구성")

1. 새 컨테이너를 만듭니다. 방금 만든 기존 데이터베이스를 사용 합니다. 컨테이너 이름에 대해 **호텔** 을 입력 하 고 파티션 키에 **/HotelId** 를 사용 합니다.

   ![컨테이너 추가](media/tutorial-multiple-data-sources/cosmos-add-container.png "컨테이너 추가")

1. **호텔**에서 **항목** 을 선택 하 고 명령 모음에서 **항목 업로드** 를 클릭 합니다. 로 이동한 다음 프로젝트 폴더에서 **cosmosdb/HotelsDataSubset_CosmosDb** 파일을 선택 합니다.

   ![Azure Cosmos DB 컬렉션에 업로드](media/tutorial-multiple-data-sources/cosmos-upload.png "Cosmos DB 컬렉션에 업로드")

1. [새로 고침] 단추를 사용하여 호텔 컬렉션의 항목 보기를 새로 고칩니다. 7개의 새 데이터베이스 문서가 나열됩니다.

## <a name="prepare-sample-blob-data"></a>샘플 Blob 데이터 준비

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고, Azure storage 계정으로 이동 하 고, **blob**을 클릭 한 후 **+ 컨테이너**를 클릭 합니다.

1. 샘플 호텔 객실 JSON 파일을 저장할 [hotel-rooms](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)라는 **Blob 컨테이너를 만듭니다**. 유효한 값에 대한 공용 액세스 수준을 설정할 수 있습니다.

   ![Blob 컨테이너 만들기](media/tutorial-multiple-data-sources/blob-add-container.png "Blob 컨테이너 만들기")

1. 컨테이너를 만들었으면 연 다음, 명령 모음에서 **업로드**를 선택합니다. 샘플 파일이 포함된 폴더로 이동합니다. 모두 선택한 다음, **업로드**를 클릭합니다.

   ![파일 업로드](media/tutorial-multiple-data-sources/blob-upload.png "파일 업로드")

업로드가 완료되면 파일이 데이터 컨테이너의 목록에 표시됩니다.

## <a name="set-up-connections"></a>연결 설정

검색 서비스 및 데이터 원본에 대한 연결 정보는 솔루션의 **appsettings.json** 파일에 지정됩니다. 

1. Visual Studio에서 **AzureSearchMultipleDataSources.sln** 파일을 엽니다.

1. 솔루션 탐색기에서 **appsettings.json** 파일을 편집합니다.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "BlobStorageAccountName": "Put your Azure Storage account name here",
  "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
  "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
  "CosmosDBDatabaseName": "hotel-rooms-db"
}
```

처음 두 항목은 Azure Cognitive Search 서비스에 대한 URL 및 관리자 키를 사용합니다. 예를 들어 엔드포인트가 `https://mydemo.search.windows.net`으로 지정되면 제공할 서비스 이름은 `mydemo`입니다.

다음 항목에서는 Azure Blob Storage 및 Azure Cosmos DB 데이터 원본에 대한 계정 이름과 연결 문자열 정보를 지정합니다.

### <a name="identify-the-document-key"></a>문서 키 식별

Azure Cognitive Search에서 키 필드는 인덱스의 각 문서를 고유하게 식별합니다. 모든 검색 인덱스는 `Edm.String`형식의 키 필드를 정확히 하나만 포함해야 합니다. 이 키 필드는 인덱스에 추가되는 데이터 원본의 각 문서에 대해 있어야 합니다. (실제로 유일한 필수 필드임)

여러 데이터 원본의 데이터를 인덱싱하는 경우 공통 문서 키를 사용 하 여 물리적으로 고유한 두 원본 문서의 데이터를 결합 된 인덱스의 새 검색 문서로 병합 합니다. 인덱스에 대 한 의미 있는 문서 키를 식별 하 고 두 데이터 원본 모두에 존재 하는지 확인 하기 위한 사전 계획이 필요 합니다. 이 데모에서는 Cosmos DB의 각 호텔에 대 한 HotelId 키가 Blob storage의 대화방 JSON blob에도 있습니다.

Azure Cognitive Search 인덱서는 인덱싱 프로세스 중에 필드 매핑을 사용하여 데이터 필드의 이름을 변경하고 심지어 형식도 다시 지정할 수 있으므로 원본 데이터를 올바른 인덱스 필드로 보낼 수 있습니다.

예를 들어 샘플 Azure Cosmos DB 데이터에서 호텔 식별자를 **`HotelId`** 이라고 합니다. 그러나 호텔 방에 대 한 JSON blob 파일에서 호텔 식별자의 이름은 **`Id`** 입니다. 프로그램은 blob의 **`Id`** 필드를 인덱스의 **`HotelId`** 키 필드에 매핑하여이를 처리 합니다.

> [!NOTE]
> 대부분의 경우 일부 인덱서에서 기본적으로 만드는 문서 키와 같이 자동 생성된 문서 키는 결합된 인덱스에 적합한 문서 키를 만들지 않습니다. 일반적으로 데이터 원본에 이미 있거나 쉽게 추가할 수 있는 의미 있고 고유한 키 값을 사용하려고 합니다.

## <a name="understand-the-code"></a>코드 이해

데이터와 구성 설정이 제대로 갖추어지면 **AzureSearchMultipleDataSources.sln**의 샘플 프로그램을 빌드하고 실행할 준비가 됩니다.

이 간단한 C#/.NET 콘솔 앱에서 수행하는 작업은 다음과 같습니다.
* C# 호텔 클래스의 데이터 구조를 기반으로 새 인덱스를 만듭니다 .이는 Address 및 Room 클래스도 참조 합니다.
* 인덱스 필드에 Azure Cosmos DB 데이터를 매핑하는 새 데이터 원본 및 인덱서를 만듭니다. 둘 다 Azure Cognitive Search의 개체입니다.
* Cosmos DB에서 호텔 데이터를 로드 하는 인덱서를 실행 합니다.
* JSON blob 데이터를 인덱스 필드에 매핑하는 두 번째 데이터 원본 및 인덱서를 만듭니다.
* 두 번째 인덱서를 실행 하 여 Blob 저장소에서 대화방 데이터를 로드 합니다.

 프로그램을 실행하기 전에 잠시 시간을 내어 이 샘플에 대한 코드, 인덱스 및 인덱서 정의를 살펴봅니다. 관련된 코드가 다음과 같은 두 개의 파일에 있습니다.

  + **Hotel.cs**에는 인덱스를 정의하는 스키마가 포함되어 있습니다.
  + **Program.cs**에는 Azure Cognitive Search 인덱스, 데이터 원본 및 인덱서를 만들고 결합된 결과를 인덱스에 로드하는 함수가 포함되어 있습니다.

### <a name="define-the-index"></a>인덱스 정의

이 샘플 프로그램에서는 .NET SDK를 사용하여 Azure Cognitive Search 인덱스를 정의하고 만듭니다. [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) 클래스를 활용하여 C# 데이터 모델 클래스에서 인덱스 구조를 생성합니다.

데이터 모델은 Address 및 Room 클래스에 대한 참조를 포함한 Hotel 클래스에서 정의됩니다. FieldBuilder는 여러 클래스 정의를 드릴다운하여 인덱스에 대한 복합 데이터 구조를 생성합니다. 메타데이터 태그는 검색 가능 여부 또는 정렬 가능 여부와 같이 각 필드의 특성을 정의하는 데 사용됩니다.

**Hotel.cs** 파일의 다음 코드 조각에서는 단일 필드 및 다른 데이터 모델 클래스에 대한 참조를 지정하는 방법을 보여 줍니다.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

**Program.cs** 파일에서 인덱스는 `FieldBuilder.BuildForType<Hotel>()` 메서드에서 생성된 이름과 필드 컬렉션으로 정의되어 다음과 같이 만들어집니다.

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Azure Cosmos DB 데이터 원본 및 인덱서 만들기

다음으로, 주 프로그램에는 호텔 데이터에 대한 Azure Cosmos DB 데이터 원본을 만드는 논리가 포함됩니다.

먼저 Azure Cosmos DB 데이터베이스 이름을 연결 문자열에 연결합니다. 그런 다음, [useChangeDetection] 속성과 같은 Azure Cosmos DB 원본에 특정한 설정을 포함하여 데이터 원본 개체를 정의합니다.

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

데이터 원본이 만들어지면 프로그램에서 **hotel-rooms-cosmos-indexer**라는 Azure Cosmos DB 인덱서를 설정합니다.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
이 예제를 두 번 이상 실행하려는 경우 프로그램에서 먼저 동일한 이름의 기존 인덱서를 삭제한 후에 새 인덱서를 만듭니다.

이 예제에서는 하루에 한 번 실행되는 인덱서의 일정을 정의합니다. 나중에 인덱서를 자동으로 다시 실행하지 않도록 하려면 이 호출에서 schedule 속성을 제거하면 됩니다.

### <a name="index-azure-cosmos-db-data"></a>Azure Cosmos DB 데이터 인덱싱

데이터 원본과 인덱서가 만들어지면 인덱서를 실행하는 코드는 다음과 같이 간단합니다.

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

이 예제에는 실행 중에 발생할 수 있는 오류를 보고하는 간단한 try-catch 블록이 포함되어 있습니다.

Azure Cosmos DB 인덱서가 실행되면 검색 인덱스에 샘플 호텔 문서의 전체 세트가 포함됩니다. 그러나 Azure Cosmos DB 데이터 원본에는 객실 세부 정보가 없으므로 각 호텔의 rooms 필드는 빈 배열이 됩니다. 다음으로, 프로그램에서 Blob 스토리지로부터 객실 데이터를 가져와서 로드하고 병합합니다.

### <a name="create-blob-storage-data-source-and-indexer"></a>Blob 스토리지 데이터 원본 및 인덱서 만들기

객실 세부 정보를 가져오기 위해 프로그램에서는 먼저 개별 JSON Blob 파일의 세트를 참조하도록 Blob 스토리지 데이터 원본을 설정합니다.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

데이터 원본이 만들어지면 프로그램에서 **hotel-rooms-blob-indexer**라는 Blob 인덱서를 설정합니다.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

JSON blob에는 **`HotelId`** 대신 **`Id`** 라는 키 필드가 포함 됩니다. 이 코드는 `FieldMapping` 클래스를 사용 하 여 **`Id`** 필드 값을 인덱스의 **`HotelId`** 문서 키로 보내도록 인덱서에 지시 합니다.

Blob 스토리지 인덱서는 사용할 구문 분석 모드를 식별하는 매개 변수를 사용할 수 있습니다. 구문 분석 모드는 단일 문서 또는 동일한 Blob 내의 여러 문서를 나타내는 Blob에 따라 달라집니다. 이 예제에서는 각 Blob이 단일 인덱스 문서를 나타내므로 코드에서 `IndexingParameters.ParseJson()` 매개 변수를 사용합니다.

JSON Blob에 대한 인덱서 구문 분석 매개 변수에 대한 자세한 내용은 [JSON Blob 인덱싱](search-howto-index-json-blobs.md)을 참조하세요. .NET SDK를 사용하여 이러한 매개 변수를 지정하는 방법에 대한 자세한 내용은 [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) 클래스를 참조하세요.

이 예제를 두 번 이상 실행하려는 경우 프로그램에서 먼저 동일한 이름의 기존 인덱서를 삭제한 후에 새 인덱서를 만듭니다.

이 예제에서는 하루에 한 번 실행되는 인덱서의 일정을 정의합니다. 나중에 인덱서를 자동으로 다시 실행하지 않도록 하려면 이 호출에서 schedule 속성을 제거하면 됩니다.

### <a name="index-blob-data"></a>Blob 데이터 인덱싱

Blob 스토리지 데이터 원본과 인덱서가 만들어지면 인덱서를 실행하는 코드는 다음과 같이 간단합니다.

```csharp
    try
    {
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

## <a name="search-your-json-files"></a>JSON 파일 검색

포털의 [**검색 탐색기**](search-explorer.md)를 사용하여 프로그램이 실행된 후 채워진 검색 인덱스를 검색할 수 있습니다.

Azure Portal에서 검색 서비스 **개요** 페이지를 열고, **인덱스** 목록에서 **hotel-rooms-sample** 인덱스를 찾습니다.

  ![Azure Cognitive Search 인덱스 목록](media/tutorial-multiple-data-sources/index-list.png "Azure Cognitive Search 인덱스 목록")

목록에서 hotel-rooms-sample 인덱스를 클릭합니다. 인덱스에 대한 검색 탐색기 인터페이스가 표시됩니다. "Luxury"(특실)와 같은 용어에 대한 쿼리를 입력합니다. 결과에서 하나 이상의 문서가 표시되며, 이 문서에는 객실 배열에 객실 개체 목록이 표시되어 있습니다.

## <a name="reset-and-rerun"></a>다시 설정하고 다시 실행

개발의 초기 실험 단계에서 디자인 반복의 가장 실용적인 방법은 Azure Cognitive Search에서 개체를 삭제 하 고 코드에서 해당 개체를 다시 작성할 수 있도록 하는 것입니다. 리소스 이름은 고유합니다. 개체를 삭제하면 동일한 이름을 사용하여 개체를 다시 만들 수 있습니다.

이 자습서의 샘플 코드는 기존 개체를 확인 하 고 삭제 하 여 코드를 다시 실행할 수 있도록 합니다.

또한 포털을 사용 하 여 인덱스, 인덱서 및 데이터 원본을 삭제할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

사용자 고유의 구독에서 작업하는 경우 프로젝트의 끝에서 더 이상 필요하지 않은 리소스를 제거하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 모든 리소스 또는 리소스 그룹 링크를 사용 하 여 포털에서 리소스를 찾고 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 여러 소스의 데이터를 수집 개념을 잘 알고 있으므로 Cosmos DB부터 시작 하는 인덱서 구성을 자세히 살펴보겠습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB 인덱서 구성](search-howto-index-cosmosdb.md)