---
title: 'C# 자습서: 여러 데이터 원본 인덱싱 - Azure Search'
description: 데이터를 여러 데이터 원본에서 단일 Azure Search 인덱스로 가져오는 방법을 알아봅니다.
author: RobDixon22
manager: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: heidist
ms.openlocfilehash: 2776bcd095c570be500fa6ddb2675820b2259716
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840666"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C# 자습서: 여러 데이터 원본의 데이터를 하나의 Azure Search 인덱스에 결합

Azure Search는 데이터를 여러 데이터 원본에서 단일 결합형 검색 인덱스로 가져오고, 분석하고, 인덱싱할 수 있습니다. 이렇게 하면 정형 데이터가 텍스트, HTML 또는 JSON 문서와 같은 다른 원본의 비정형 데이터, 심지어 일반 텍스트 데이터를 사용하여 집계되는 상황을 지원합니다.

이 자습서에서는 Azure Cosmos DB 데이터 원본의 호텔 데이터를 인덱싱하고, 이를 Azure Blob Storage 문서에서 가져온 호텔 객실 세부 정보와 병합하는 방법에 대해 설명합니다. 결과는 복잡한 데이터 형식이 포함된 결합형 호텔 검색 인덱스가 됩니다.

이 자습서에서는 C#, Azure Search용 .NET SDK 및 Azure Portal을 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 샘플 데이터 업로드 및 데이터 원본 만들기
> * 문서 키 식별
> * 인덱스 정의 및 만들기
> * Azure Cosmos DB의 호텔 데이터 인덱싱
> * Blob 스토리지의 호텔 객실 데이터 병합

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 사용되는 서비스, 도구 및 데이터는 다음과 같습니다. 

- [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 자습서에서는 체험 서비스를 사용할 수 있습니다.

- 샘플 호텔 데이터를 저장하기 위한 [Azure Cosmos DB 계정을 만듭니다](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

- 샘플 JSON Blob 데이터를 저장하기 위한 [Azure 스토리지 계정을 만듭니다](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

- IDE로 사용할 [Visual Studio를 설치](https://visualstudio.microsoft.com/)합니다.

### <a name="install-the-project-from-github"></a>GitHub에서 프로젝트 설치

1. GitHub에서 [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) 샘플 리포지토리를 찾습니다.
1. **복제 또는 다운로드**를 선택하고, 리포지토리의 프라이빗 로컬 복사본을 만듭니다.
1. Visual Studio를 열고, 아직 설치되지 않은 경우 Microsoft Azure Search NuGet 패키지를 설치합니다. **도구** 메뉴에서 **NuGet 패키지 관리자**, **솔루션용 NuGet 패키지 관리...** 를 차례로 선택합니다. **찾아보기** 탭을 선택한 다음, 검색 상자에서 "Azure Search"를 입력합니다. 목록에 **Microsoft.Azure.Search**(버전 9.0.1 이상)가 나타나면 설치합니다. 설치를 완료하려면 추가 대화 상자를 클릭해야 합니다.

    ![NuGet을 사용하여 Azure 라이브러리 추가](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Visual Studio를 사용하여 로컬 리포지토리로 이동한 다음, **AzureSearchMultipleDataSources.sln** 솔루션 파일을 엽니다.

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

Azure Search 서비스와 상호 작용하려면 서비스 URL과 액세스 키가 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키는 요청을 보내는 애플리케이션과 이 요청을 처리하는 서비스 간에 요청별로 신뢰를 설정합니다.

## <a name="prepare-sample-azure-cosmos-db-data"></a>샘플 Azure Cosmos DB 데이터 준비

이 샘플에서는 7개의 가상 호텔을 설명하는 두 개의 작은 데이터 세트를 사용합니다. 한 세트는 호텔 자체를 설명하고 Azure Cosmos DB 데이터베이스에 로드됩니다. 다른 세트는 호텔 객실 세부 정보를 포함하고 있으며, Azure Blob Storage에 업로드할 7개의 개별 JSON 파일로 제공됩니다.

1. [Azure Portal에 로그인](https://portal.azure.com)한 다음, Azure Cosmos DB 계정 개요 페이지를 탐색합니다.

1. 메뉴 모음에서 [컨테이너 추가]를 클릭합니다. "새 데이터베이스 만들기"를 지정하고, **hotel-rooms-db** 이름을 사용합니다. 컬렉션 이름에 대해 **hotel-rooms**를 입력하고, 파티션 키에 대해 **/HotelId**를 입력합니다. **확인**을 클릭하여 데이터베이스와 컨테이너를 만듭니다.

   ![Azure Cosmos DB 컨테이너 추가](media/tutorial-multiple-data-sources/cosmos-add-container.png "Azure Cosmos DB 컨테이너 추가")

1. Cosmos DB 데이터 탐색기로 이동하고, **hotel-rooms-db** 데이터베이스 내의 **hotels** 컨테이너 아래에서 **items** 요소를 선택합니다. 그런 다음, 명령 모음에서 **항목 업로드**를 클릭합니다.

   ![Azure Cosmos DB 컬렉션에 업로드](media/tutorial-multiple-data-sources/cosmos-upload.png "Cosmos DB 컬렉션에 업로드")

1. 업로드 패널에서 폴더 단추를 클릭한 다음, 프로젝트 폴더의 **cosmosdb/HotelsDataSubset_CosmosDb.json** 파일로 이동합니다. **확인**을 클릭하여 업로드를 시작합니다.

   ![업로드할 파일 선택](media/tutorial-multiple-data-sources/cosmos-upload2.png "업로드할 파일 선택")

1. [새로 고침] 단추를 사용하여 호텔 컬렉션의 항목 보기를 새로 고칩니다. 7개의 새 데이터베이스 문서가 나열됩니다.

## <a name="prepare-sample-blob-data"></a>샘플 Blob 데이터 준비

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Storage 계정으로 이동한 후 **Blobs**를 클릭하고 **+ 컨테이너**를 클릭합니다.

1. 샘플 호텔 객실 JSON 파일을 저장할 **hotel-rooms**라는 [Blob 컨테이너를 만듭니다](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 유효한 값에 대한 공용 액세스 수준을 설정할 수 있습니다.

   ![Blob 컨테이너 만들기](media/tutorial-multiple-data-sources/blob-add-container.png "Blob 컨테이너 만들기")

1. 컨테이너를 만들었으면 연 다음, 명령 모음에서 **업로드**를 선택합니다.

   ![명령 모음에서 업로드](media/search-semi-structured-data/upload-command-bar.png "명령 모음에서 업로드")

1. 샘플 파일이 포함된 폴더로 이동합니다. 모두 선택한 다음, **업로드**를 클릭합니다.

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

처음 두 항목은 Azure Search 서비스에 대한 URL 및 관리자 키를 사용합니다. 예를 들어 엔드포인트가 `https://mydemo.search.windows.net`으로 지정되면 제공할 서비스 이름은 `mydemo`입니다.

다음 항목에서는 Azure Blob Storage 및 Azure Cosmos DB 데이터 원본에 대한 계정 이름과 연결 문자열 정보를 지정합니다.

### <a name="identify-the-document-key"></a>문서 키 식별

Azure Search에서 키 필드는 인덱스의 각 문서를 고유하게 식별합니다. 모든 검색 인덱스는 `Edm.String`형식의 키 필드를 정확히 하나만 포함해야 합니다. 이 키 필드는 인덱스에 추가되는 데이터 원본의 각 문서에 대해 있어야 합니다. (실제로 유일한 필수 필드임)

여러 데이터 원본에서 데이터를 인덱싱할 때 각 데이터 원본 키 값은 결합된 인덱스의 동일한 키 필드에 매핑되어야 합니다. 인덱스에 의미 있는 문서 키를 식별하고 모든 데이터 원본에 있는지 확인하는 몇 가지 사전 계획이 필요합니다.

Azure Search 인덱서는 인덱싱 프로세스 중에 필드 매핑을 사용하여 데이터 필드의 이름을 변경하고 심지어 형식도 다시 지정할 수 있으므로 원본 데이터를 올바른 인덱스 필드로 보낼 수 있습니다.

예를 들어 샘플 Azure Cosmos DB 데이터에서 호텔 식별자는 **HotelId**라고 합니다. 그러나 호텔 객실에 대한 JSON Blob 파일에서 호텔 식별자의 이름은 **Id**입니다. 프로그램에서 이 문제는 Blob의 **Id** 필드를 인덱스의 **HotelId** 키 필드에 매핑하여 처리합니다.

> [!NOTE]
> 대부분의 경우 일부 인덱서에서 기본적으로 만드는 문서 키와 같이 자동 생성된 문서 키는 결합된 인덱스에 적합한 문서 키를 만들지 않습니다. 일반적으로 데이터 원본에 이미 있거나 쉽게 추가할 수 있는 의미 있고 고유한 키 값을 사용하려고 합니다.

## <a name="understand-the-code"></a>코드 이해

데이터와 구성 설정이 제대로 갖추어지면 **AzureSearchMultipleDataSources.sln**의 샘플 프로그램을 빌드하고 실행할 준비가 됩니다.

이 간단한 C#/.NET 콘솔 앱에서 수행하는 작업은 다음과 같습니다.
* C# Hotel 클래스(Address 및 Room 클래스도 참조)의 데이터 구조를 기반으로 하여 새 Azure Search 인덱스를 만듭니다.
* Azure Cosmos DB 데이터 원본 및 Azure Cosmos DB 데이터를 인덱스 필드에 매핑하는 인덱서를 만듭니다.
* Azure Cosmos DB 인덱서를 실행하여 Hotel 데이터를 로드합니다.
* Azure Blob Storage 데이터 원본 및 JSON Blob 데이터를 인덱스 필드에 매핑하는 인덱서를 만듭니다.
* Azure Blob Storage 인덱서를 실행하여 Rooms 데이터를 로드합니다.

 프로그램을 실행하기 전에 잠시 시간을 내어 이 샘플에 대한 코드, 인덱스 및 인덱서 정의를 살펴봅니다. 관련된 코드가 다음과 같은 두 개의 파일에 있습니다.

  + **Hotel.cs**에는 인덱스를 정의하는 스키마가 포함되어 있습니다.
  + **Program.cs**에는 Azure Search 인덱스, 데이터 원본 및 인덱서를 만들고 결합된 결과를 인덱스에 로드하는 함수가 포함되어 있습니다.

### <a name="define-the-index"></a>인덱스 정의

이 샘플 프로그램에서는 .NET SDK를 사용하여 Azure Search 인덱스를 정의하고 만듭니다. [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) 클래스를 활용하여 C# 데이터 모델 클래스에서 인덱스 구조를 생성합니다.

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

JSON Blob에는 **HotelId** 대신 **Id**라는 키 필드가 있습니다. 이 코드는 `FieldMapping` 클래스를 사용하여 **Id** 필드 값을 인덱스의 **HotelId** 문서 키로 지정하도록 인덱서에 지시합니다.

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

  ![Azure Search 인덱스 목록](media/tutorial-multiple-data-sources/index-list.png "Azure Search 인덱스 목록")

목록에서 hotel-rooms-sample 인덱스를 클릭합니다. 인덱스에 대한 검색 탐색기 인터페이스가 표시됩니다. "Luxury"(특실)와 같은 용어에 대한 쿼리를 입력합니다. 결과에서 하나 이상의 문서가 표시되며, 이 문서에는 객실 배열에 객실 개체 목록이 표시되어 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 마친 후 정리하는 가장 빠른 방법은 Azure Search 서비스를 포함하고 있는 리소스 그룹을 삭제하는 것입니다. 리소스 그룹을 삭제하여 이제 리소스 그룹 내의 모든 항목을 영구 삭제할 수 있습니다. 포털에서 리소스 그룹 이름은 Azure Search 서비스의 개요 페이지에 있습니다.

## <a name="next-steps"></a>다음 단계

JSON Blob을 인덱싱하는 데 사용할 수 있는 방법 및 옵션에는 여러 가지가 있습니다. 원본 데이터에 JSON 콘텐츠가 포함되어 있으면 이러한 옵션을 검토하여 시나리오에 가장 적합한 항목을 확인할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Search Blob 인덱서를 사용하여 JSON Blob을 인덱싱하는 방법](search-howto-index-json-blobs.md)

비정형 Blob 또는 전체 텍스트 콘텐츠에서 인지적으로 강화된 데이터를 사용하여 하나의 데이터 원본에 있는 정형 인덱스 데이터를 보강할 수도 있습니다. 다음 자습서에서는 .NET SDK를 사용하여 Cognitive Services를 Azure Search와 함께 사용하는 방법을 보여 줍니다.

> [!div class="nextstepaction"]
> [Azure Search 인덱싱 파이프라인에서 Cognitive Services API 호출](cognitive-search-tutorial-blob-dotnet.md)
