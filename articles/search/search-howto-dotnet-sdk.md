---
title: .NET의 v11 (Azure.Search.Documents) 사용
titleSuffix: Azure Cognitive Search
description: 'C # 및 v11 (Azure.Search.Documents) 클라이언트 라이브러리를 사용 하 여 .NET 응용 프로그램에서 검색 개체를 만들고 관리 하는 방법에 대해 알아봅니다. 코드 조각은 서비스에 연결 하 고 인덱스 및 쿼리를 만드는 과정을 보여 줍니다.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3ceead297ea726e256d806c08c22810b39296793
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917174"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>C # .NET 응용 프로그램에서 Azure.Search.Documents를 사용 하는 방법

이 문서에서는 c # 및 [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) (버전 11) 클라이언트 라이브러리를 사용 하 여 검색 개체를 만들고 관리 하는 방법을 설명 합니다.

## <a name="about-version-11"></a>버전 11 정보

.NET 용 azure SDK는 uments와 동일한 기능을 하는 Azure SDK 팀에서 새 [**Azure.Search.Doc**](/dotnet/api/overview/azure/search) client 라이브러리를 추가 합니다. 클라이언트 라이브러리를 [검색](/dotnet/api/overview/azure/search/client10) 하지만 해당 하는 경우 일반적인 접근 방법 및 규칙을 활용 합니다. 일부 예제 [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) 에는 키 인증과 [System.Text.Js있습니다. ](/dotnet/api/system.text.json.serialization) JSON serialization에 대 한 직렬화입니다.

이전 버전과 마찬가지로이 라이브러리를 사용 하 여 다음 작업을 수행할 수 있습니다.

+ 검색 인덱스, 데이터 원본, 인덱서, 기술력과 및 동의어 맵 만들기 및 관리
+ 인덱스에서 검색 문서 로드 및 관리
+ HTTP 및 JSON의 세부 정보를 처리 하지 않고도 쿼리를 실행 합니다.

라이브러리는 검색 서비스에 대 한 프로그래밍 방식 액세스에 사용 되는 모든 Api를 포함 하는 단일 [Azure.Search.DocUment NuGet 패키지로](https://www.nuget.org/packages/Azure.Search.Documents/)배포 됩니다.

클라이언트 라이브러리는 `SearchIndexClient` 및 `SearchClient` 클래스에서 `SearchIndex`, `SearchField`, `SearchDocument` 등의 클래스와 `SearchIndexClient.CreateIndex` 및 `SearchClient.Search` 등의 작업을 정의합니다. 이러한 클래스는 다음과 같은 네임 스페이스에 구성됩니다.

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (버전 11) [ `2020-06-30` Azure Cognitive Search REST API의](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30)대상 버전입니다. 

클라이언트 라이브러리는 검색 서비스 만들기 및 크기 조정, API 키 관리 등의 [서비스 관리 작업](/rest/api/searchmanagement/)을 제공 하지 않습니다. .NET 응용 프로그램에서 검색 리소스를 관리 해야 하는 경우 .NET 용 Azure SDK의 [Microsoft Azure 관리](/dotnet/api/overview/azure/search/management) 라이브러리를 사용 합니다.

## <a name="upgrade-to-v11"></a>V11로 업그레이드

이전 버전의 .NET SDK를 사용 하 고 있고 현재 일반 공급 버전으로 업그레이드 하려는 경우 [Azure Cognitive Search .NET sdk 버전 11로 업그레이드](search-dotnet-sdk-migration-version-11.md) 를 참조 하세요.

## <a name="sdk-requirements"></a>SDK 요구 사항

+ Visual Studio 2019 이상

+ 사용자 고유의 Azure Cognitive Search 서비스. SDK를 사용하려면 서비스 이름과 하나 이상의 API 키가 필요합니다. [포털이 없으면 포털에서 서비스를 만듭니다](search-create-service-portal.md) .

+ **도구** 를 사용 하 여 [Azure.Search.Documents 패키지](https://www.nuget.org/packages/Azure.Search.Documents) 를 다운로드  >  **NuGet Package Manager**  >  Visual Studio에서 **솔루션에 대 한 nuget 패키지 관리를** 사용 합니다. 패키지 이름을 검색 `Azure.Search.Documents` 합니다.

.NET 용 Azure SDK는 [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support)를 준수 하며,이는 최소 요구 사항으로 4.6.1 및 .net Core 2.0 .NET Framework 의미 합니다.

## <a name="example-application"></a>예제 응용 프로그램

이 문서에서는 GitHub의 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 코드 예제에 의존 하 여 Azure Cognitive Search의 기본 개념을 설명 하는 (예:) 검색 인덱스를 만들고 로드 하 고 쿼리 하는 방법에 대해 설명 합니다.

이 문서의 나머지 부분에서는 몇 개의 문서로 채워진 "호텔" 이라는 새 인덱스를 가정 합니다. 여기에는 결과와 일치 하는 몇 개의 쿼리가 포함 되어 있습니다.

다음은 전체 흐름을 보여 주는 주요 프로그램입니다.

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

다음은 유효한 서비스 이름 및 API 키를 사용 하 여이 응용 프로그램을 실행 한다고 가정 하 고 출력의 부분 스크린샷입니다.

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="샘플 프로그램의 Console. WriteLine 출력":::

### <a name="client-types"></a>클라이언트 유형

클라이언트 라이브러리는 인덱스를 [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) 생성, 업데이트 또는 삭제 하 [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) 고, 인덱스를 로드 또는 쿼리하고, [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) 인덱서 및 기술력과로 작업 하는 등의 다양 한 작업에 대해 세 가지 클라이언트 유형을 사용 합니다. 이 문서에서는 처음 두 가지에 대해 집중적으로 설명 합니다. 

모든 클라이언트에는 최소한 서비스 이름 또는 끝점과 API 키가 필요 합니다. `appsettings.json` [DotNetHowTo 샘플 응용 프로그램](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)의 파일에서 찾을 수 있는 것과 비슷한 방식으로 구성 파일에이 정보를 제공 하는 것이 일반적입니다. 구성 파일에서 읽으려면 프로그램에를 추가 `using Microsoft.Extensions.Configuration;` 합니다.

다음 명령문은 인덱스를 생성, 업데이트 또는 삭제 하는 데 사용 되는 인덱스 클라이언트를 만듭니다. 검색 끝점과 관리 API 키를 사용 합니다.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

다음 문은 문서를 로드 하거나 쿼리를 실행 하는 데 사용 되는 검색 클라이언트를 만듭니다. `SearchClient` 인덱스가 필요 합니다. 문서를 로드 하려면 관리 API 키가 필요 하지만 쿼리 API 키를 사용 하 여 쿼리를 실행할 수 있습니다. 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> 가져오기 작업에 대해 잘못 된 키를 제공 하는 경우 (예: 관리자 키가 필요한 쿼리 키)는에서 `SearchClient` `CloudException` 작업 메서드를 처음 호출할 때 "사용할 수 없음" 오류 메시지와 함께을 throw 합니다. 이 경우 API 키를 두 번 확인 합니다.
>

### <a name="deleting-the-index"></a>인덱스 삭제

개발의 초기 단계에서는 [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) 업데이트 된 정의를 사용 하 여 다시 만들 수 있도록 작업 중인 인덱스를 삭제 하는 문을 포함할 수 있습니다. Azure Cognitive Search에 대 한 샘플 코드는 샘플을 다시 실행할 수 있도록 삭제 단계를 포함 하는 경우가 많습니다.

다음 줄은를 호출 합니다 `DeleteIndexIfExists` .

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

이 메서드는 지정 된를 사용 하 여 인덱스가 있는지 확인 하 고, 있는 `SearchIndexClient` 경우 삭제 합니다.

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> 이 문서의 예제 코드는 간단 하 게 하기 위해 동기 메서드를 사용 하지만 사용자 응용 프로그램에서 비동기 메서드를 사용 하 여 확장 가능 하 고 응답성을 유지 해야 합니다. 예를 들어 위의 메서드에서 대신을 사용할 수 있습니다 [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) .
>

## <a name="create-an-index"></a>인덱스 만들기

를 사용 하 여 인덱스를 만들 수 있습니다 [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) . 

아래 메서드는 [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) 새 인덱스의 스키마를 정의 하는 개체 목록을 사용 하 여 새 개체를 만듭니다. 각 필드에는 이름, 데이터 유형, 그리고 검색 동작을 정의하는 몇 가지 특성이 있습니다. 

를 사용 하 여 모델 클래스에서 필드를 정의할 수 있습니다 [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) . `FieldBuilder` 클래스는 리플렉션을 사용하여 지정된 `Hotel` 모델 클래스의 public 속성 및 특성을 검사하고 인덱스에 대한 `SearchField` 개체 목록을 만듭니다. `Hotel` 클래스에 대해서는 이후에 좀 더 자세히 알아보겠습니다.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

필드 외에도 점수 매기기 프로필, 확인 기 또는 CORS 옵션을 인덱스에 추가할 수 있습니다. 이러한 매개 변수는 간단히 하기 위해 샘플에서 생략 되었습니다. SearchIndex 개체 및 해당 구성 요소에 대 한 자세한 내용은 [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) 속성 목록 및 [REST API 참조](/rest/api/searchservice/)에서 찾을 수 있습니다.

> [!NOTE]
> 필요한 경우 `FieldBuilder`를 사용하는 대신, `Field` 개체 목록을 항상 직접 만들 수 있습니다. 예를 들어 모델 클래스를 사용하지 않으려고 하거나, 특성을 추가하여 수정하지 않으려는 기존 모델 클래스를 사용해야 할 수 있습니다.
>

### <a name="call-createindex-in-main"></a>Main ()에서 CreateIndex 호출

`Main` 위의 메서드를 호출 하 여 새 "호텔" 인덱스를 만듭니다.

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>데이터 표현에 모델 클래스 사용

DotNetHowTo 샘플에서는 [호텔](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs), [주소](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)및 [방](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) 데이터 구조에 대 한 모델 클래스를 사용 합니다. `Hotel` 참조 `Address` , 단일 수준 복합 형식 (여러 부분으로 구성 된 필드) 및 `Room` (여러 부분으로 구성 된 필드의 컬렉션)

이러한 형식을 사용 하 여 인덱스를 만들고 로드 하며 쿼리에서 응답을 구성할 수 있습니다.

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

또 다른 방법은 인덱스에 필드를 직접 추가 하는 것입니다. 다음 예제에서는 몇 개의 필드만 보여 줍니다.

   ```csharp
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };
   ```

### <a name="field-definitions"></a>필드 정의

.NET의 데이터 모델과 해당 인덱스 스키마는 최종 사용자에 게 제공할 검색 환경을 지원 해야 합니다. 검색 인덱스의 검색 문서와 같이 .NET의 각 최상위 개체는 사용자 인터페이스에 표시 되는 검색 결과에 해당 합니다. 예를 들어 호텔 검색 응용 프로그램에서 최종 사용자는 호텔 이름, 호텔의 기능 또는 특정 대화방의 특성을 기준으로 검색할 수 있습니다. 

각 클래스 내에서 필드는 사용 방법을 결정 하는 데이터 형식 및 특성을 사용 하 여 정의 됩니다. 각 클래스에서 각 공용 속성의 이름은 인덱스 정의에서 이름이 같은 필드에 매핑됩니다. 

호텔 클래스에서 여러 필드 정의를 가져오는 다음 코드 조각을 살펴보세요. 주소와 대화방은 고유한 클래스 정의를 포함 하는 c # 형식입니다. 해당 코드를 보려면 샘플 코드를 참조 하세요. 둘 다 복합 유형입니다. 자세한 내용은 [복합 형식을 모델링 하는 방법](search-howto-complex-data-types.md)을 참조 하세요.

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>필드 클래스 선택

필드를 정의할 때 기본 [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) 클래스를 사용 하거나 미리 구성 된 속성을 사용 하 여 "템플릿"으로 제공 되는 파생 도우미 모델을 사용할 수 있습니다.

인덱스에서 정확히 하나의 필드가 문서 키 ()로 사용 되어야 합니다 `IsKey = true` . 문자열 이어야 하며 각 문서를 고유 하 게 식별 해야 합니다. 또한를 사용 해야 `IsHidden = true` 합니다. 즉, 검색 결과에 표시 되지 않습니다.

| 필드 형식 | 설명 및 사용법 |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | 대부분의 속성이 null로 설정 되 고, 필요한 제외한 `Name` 표준 Lucene로 설정 된 기본 클래스 `AnalyzerName` 입니다. |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | 도우미 모델. 모든 데이터 형식일 수 있으며, 항상 검색할 수 없고 (전체 텍스트 검색 쿼리에서는 무시 됨) 검색할 수 있습니다 (숨겨지지 않음). 다른 특성은 기본적으로 꺼져 있지만 사용하도록 설정할 수 있습니다. 필터, 패싯 또는 점수 매기기 프로필에만 사용되는 문서 ID 또는 필드에는 `SimpleField`를 사용할 수 있습니다. 그렇다면 문서 ID에 대한 `IsKey = true`와 같이 시나리오에 필요한 특성을 적용해야 합니다. 자세한 내용은 소스 코드의 [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs)를 참조하세요. |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | 도우미 모델. 는 문자열 이어야 하며 항상 검색 가능 하 고 검색할 수 있습니다. 다른 특성은 기본적으로 꺼져 있지만 사용하도록 설정할 수 있습니다. 이 필드 형식은 검색할 수 있으므로 동의어와 분석기 속성의 전체 보충을 지원합니다. 자세한 내용은 소스 코드의 [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs)를 참조하세요. |

기본 `SearchField` API를 사용하든지 도우미 모델 중 하나를 사용하든지 간에 필터, 패싯 및 정렬 특성을 명시적으로 사용하도록 설정해야 합니다. 예를 들어 [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) 및 [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable)은 위의 샘플처럼 명시적으로 특성을 지정해야 합니다.

#### <a name="adding-field-attributes"></a>필드 특성 추가

각 필드는,, 및와 같은 특성으로 데코 레이트 됩니다 `IsFilterable` `IsSortable` `IsKey` `AnalyzerName` . 이러한 특성은 [Azure Cognitive Search 인덱스의 해당 필드 특성](/rest/api/searchservice/create-index)에 직접 매핑됩니다. `FieldBuilder`클래스는 이러한 속성을 사용 하 여 인덱스에 대 한 필드 정의를 생성 합니다.

#### <a name="field-type-mapping"></a>필드 형식 매핑

속성의 .NET 형식은 인덱스 정의의 해당 필드 형식에 매핑됩니다. 예를 들어, `Category` 문자열 속성은 `Edm.String` 유형인 `category` 필드에 매핑됩니다. ,, 및 사이에는 유사한 형식 매핑이 있습니다 `bool?` `Edm.Boolean` `DateTimeOffset?` `Edm.DateTimeOffset` . 

속성을 확인 하는 데 문제가 `SmokingAllowed` 있나요?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore`이 속성의 특성은를 `FieldBuilder` 인덱스에 필드로 직렬화 하지 않도록에 지시 합니다.  이 방법은 응용 프로그램에서 도우미로 사용할 수 있는 클라이언트 쪽 계산 된 속성을 만드는 좋은 방법입니다.  이 경우 `SmokingAllowed` 속성은 `Room` 컬렉션에 흡연 허용 되는지 여부를 반영 `Rooms` 합니다. 모두 false 이면 전체 호텔에서 흡연을 허용 하지 않음을 나타냅니다.

## <a name="load-an-index"></a>인덱스 로드

의 다음 단계는 `Main` 새로 만든 "호텔" 인덱스를 채웁니다. 이 인덱스 채우기는 다음 메서드에서 수행 됩니다. (일부 코드는 "..."로 대체 되었습니다. 설명을 위한 것입니다. 전체 데이터 채우기 코드는 전체 샘플 솔루션을 참조 하세요.)

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                HotelName = "Secret Point Motel",
                ...
                Address = new Address()
                {
                    StreetAddress = "677 5th Ave",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Budget Room, 1 Queen Bed (Cityside)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (City View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                HotelName = "Twin Dome Motel",
                ...
                {
                    StreetAddress = "140 University Town Center Dr",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Suite, 2 Double Beds (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Standard Room, 1 Queen Bed (City View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Waterfront View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "3",
                HotelName = "Triple Landscape Hotel",
                ...
                Address = new Address()
                {
                    StreetAddress = "3393 Peachtree Rd",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Standard Room, 2 Queen Beds (Amenities)",
                        ...
                    },
                    new Room ()
                    {
                        Description = "Standard Room, 2 Double Beds (Waterfront View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (Cityside)",
                        ...
                    }
                }
            }
        };

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

이 메서드는 네 부분으로 이루어져 있습니다. 첫 번째는 `Hotel` `Room` 인덱스에 업로드할 입력 데이터의 역할을 하는 개체 3 개를 포함 하는 3 개의 개체 배열을 만듭니다. 이 데이터는 간단히 하기 위해 하드 코딩합니다. 실제 응용 프로그램에서 데이터는 SQL database와 같은 외부 데이터 원본에서 가져올 수 있습니다.

두 번째 부분은 [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 문서를 포함 하는을 만듭니다. 이 경우를 호출 하 여 일괄 처리를 만들 때 적용할 작업을 지정 합니다 [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) . 그런 다음 메서드를 통해 일괄 처리를 Azure Cognitive Search 인덱스에 업로드 합니다 [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) .

> [!NOTE]
> 이 예에서는 문서를 업로드하는 것입니다. 변경 사항을 기존 문서에 병합하거나 문서를 삭제하려면 `IndexDocumentsAction.Merge`, `IndexDocumentsAction.MergeOrUpload` 또는 `IndexDocumentsAction.Delete`를 호출하여 배치를 만듭니다. 개체의 컬렉션을 사용 하는 개체의 컬렉션을 사용 하 여 여러 작업을 단일 일괄 처리로 혼합할 수도 있습니다 `IndexBatch.New` `IndexDocumentsAction` . 각 개체는 Azure Cognitive Search 문서에 대해 특정 작업을 수행 하도록 지시 합니다. `IndexDocumentsAction.Merge`, `IndexAction.Upload` 등을 비롯한 해당 메서드를 호출하여 각 `IndexDocumentsAction`과 자체 작업을 만들 수 있습니다.
> 

이 메서드의 세 번째 부분은 인덱싱에 중요한 오류 사례를 처리하는 catch 블록입니다. 검색 서비스에서 일괄 처리의 일부 문서를 인덱싱하지 못한 경우에 `IndexBatchException` 의해이 throw 됩니다 `IndexDocuments` . 이 예외는 서비스의 부하가 높은 상태에서 문서를 인덱싱하는 경우에 발생할 수 있습니다. **이 경우 코드에서 명시적으로 처리하는 것이 좋습니다.** 실패한 문서 인덱싱을 잠시 후 다시 시도하거나, 샘플에서 하던 것처럼 기록하여 계속하거나, 애플리케이션의 데이터 일관성 요구 사항에 따라 다른 작업을 수행할 수 있습니다.

마지막으로, `UploadDocuments` 메서드가 2초 동안 지연됩니다. 인덱싱은 검색 서비스에서 비동기적으로 수행 되므로 샘플 응용 프로그램은 문서를 검색 하는 데 사용할 수 있도록 짧은 시간을 기다려야 합니다. 이와 같이 데모, 테스트, 샘플 애플리케이션에서는 일반적으로 지연만 필요합니다.

### <a name="call-uploaddocuments-in-main"></a>Main ()에서 UploadDocuments를 호출 합니다.

다음 코드 조각에서는 [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) indexClient의 메서드를 사용 하 여 인스턴스를 설정 합니다 [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) . IndexClient는 요청에 대 한 관리 API 키를 사용 하며, 문서를 로드 하거나 새로 고치는 데 필요 합니다.

대체 방법은 `SearchClient` 에서 관리 API 키를 전달 하 여 직접 호출 하는 것입니다 `AzureKeyCredential` .

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>쿼리 실행

먼저 `SearchClient` **appsettings.js** 에서 검색 끝점과 쿼리 API 키를 읽는를 설정 합니다.

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

둘째, 쿼리 요청을 전송 하는 메서드를 정의 합니다. 

메서드가 쿼리를 실행할 때마다 새 개체를 만듭니다 [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) . 이 개체는 정렬, 필터링, 페이징, 패싯 등의 쿼리에 대 한 추가 옵션을 지정 하는 데 사용 됩니다. 이 메서드에서는 `Filter` `Select` 다른 쿼리에 대해, 및 속성을 설정 `OrderBy` 합니다. 검색 쿼리 식 구문에 대 한 자세한 내용은 [단순 쿼리 구문](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)을.

다음 단계를 실제로 검색 쿼리를 실행합니다. 검색을 실행 하는 방법은 메서드를 사용 하 여 수행 `SearchClient.Search` 합니다. 각 쿼리에 대해 문자열 (또는 `"*"` 검색 텍스트가 없는 경우)로 사용할 검색 텍스트와 앞에서 만든 검색 옵션을 전달 합니다. 또한 SDK에 검색 결과 내 문서를 `Hotel` 유형의 개체로 역직렬화하도록 명령하는 `SearchClient.Search`에 대한 유형 매개 변수로 `Hotel`을(를) 지정할 수도 있습니다.

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

셋째, 각 문서를 콘솔에 인쇄 하는 응답을 기록 하는 메서드를 정의 합니다.

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>Main ()에서 RunQueries 호출

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>쿼리 구문 살펴보기

이러한 각 쿼리를 차례대로 조금 더 자세히 살펴보겠습니다. 첫 번째 쿼리를 실행하는 코드는 다음과 같습니다.

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

이 경우 검색 가능한 필드에서 "motel" 이라는 단어에 대 한 전체 인덱스를 검색 하 고, 옵션에 지정 된 대로 호텔 이름만 검색 하려고 합니다 `Select` . 결과는 다음과 같습니다.

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

두 번째 쿼리에서는 필터를 사용 하 여 야간 속도로 $100 미만의 공간을 선택 합니다. 결과에서 호텔 ID 및 설명만 반환 합니다.

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

위의 쿼리에서는 OData 식를 사용 하 여 `$filter` `Rooms/any(r: r/BaseRate lt 100)` 인덱스의 문서를 필터링 합니다. [Any 연산자](./search-query-odata-collection-operators.md) 를 사용 하 여 ' BaseRate l 100 '을 방 컬렉션의 모든 항목에 적용 합니다. 자세한 내용은 [OData 필터 구문](./query-odata-filter-orderby-syntax.md)을 참조 하세요.

세 번째 쿼리에서 가장 최근에 리모델링한 된 맨 위의 두 호텔을 찾고 호텔 이름과 last 보수 date를 표시 합니다. 코드는 다음과 같습니다. 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

마지막 쿼리에서 "호텔" 이라는 단어와 일치 하는 호텔 이름을 모두 찾습니다.

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

이 섹션에서는 .NET SDK 소개를 마무리 하지만 여기서는 중지 하지 않습니다. 다음 섹션에서는 Azure Cognitive Search로 프로그래밍 하는 방법에 대해 자세히 알아볼 추가 리소스를 제안 합니다.

## <a name="next-steps"></a>다음 단계

+ [Azure.Search.Documents](/dotnet/api/azure.search.documents) 및 [REST API](/rest/api/searchservice/) 에 대 한 API 참조 설명서를 찾아봅니다.

+ Uments의 Azure.Search.Doc을 기반으로 하는 다른 코드 샘플 찾아보기- [dotnet-샘플](https://github.com/Azure-Samples/azure-search-dotnet-samples) 및 [검색-시작-dotnet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ [명명 규칙](/rest/api/searchservice/Naming-rules) 을 검토 하 여 다양 한 개체의 이름을 지정 하는 규칙에 대해 알아봅니다.

+ [지원 되는 데이터 형식](/rest/api/searchservice/Supported-data-types) 검토