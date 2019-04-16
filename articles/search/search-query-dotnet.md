---
title: C#으로 Azure Search 인덱스 데이터 쿼리(.NET SDK) - Azure Search
description: Azure Search에서 검색 쿼리를 작성하기 위한 C# 코드 예제입니다. 검색 매개 변수를 추가하여 검색 결과를 필터링하고 정렬합니다.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 1194407122123797c2564c96ac452b9582b017a4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264950"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>빠른 시작: 3 - C#으로 Azure Search 인덱스 쿼리

이 문서에서는 C# 및 [.NET SDK](https://aka.ms/search-sdk)를 사용하여 [Azure Search 인덱스](search-what-is-an-index.md)를 쿼리하는 방법을 보여줍니다. 인덱스의 문서 검색은 다음 작업을 통해 수행됩니다.

> [!div class="checklist"]
> * 읽기 전용 권한으로 검색 인덱스에 연결하는 [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) 개체를 만듭니다.
> * 검색 또는 필터 정의를 포함하는 [`SearchParameters`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) 개체를 만듭니다.
> * `SearchIndexClient`에서 `Documents.Search` 메서드를 호출하여 인덱스에 쿼리를 보냅니다.

## <a name="prerequisites"></a>필수 조건

호텔 샘플 데이터가 포함된 [Azure Search 인덱스를 로드](search-import-data-dotnet.md)합니다.

문서에 대한 읽기 전용 액세스에 사용되는 쿼리 키를 획득합니다. 지금까지는 검색 서비스에 개체와 콘텐츠를 만들 수 있도록 관리 API 키를 사용했습니다. 하지만 앱의 쿼리 지원에는 쿼리 키를 사용할 것을 강력하게 권장합니다. 지침은 [쿼리 키 만들기](search-security-api-keys.md#create-query-keys)를 참조하세요.

## <a name="create-a-client"></a>클라이언트 만들기
읽기 전용 액세스에 대한 쿼리 키를 제공할 수 있도록 `SearchIndexClient` 클래스의 인스턴스를 만듭니다(이전 단원에서 사용한 `SearchServiceClient`에 부여된 쓰기 액세스 권한과는 달리).

이 클래스에는 몇 가지 생성자가 있습니다. 그 중 하나는 검색 서비스 이름, 인덱스 이름 및 [`SearchCredentials`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) 개체를 매개 변수로 사용합니다. `SearchCredentials` API 키를 래핑합니다.

아래 코드는 애플리케이션의 구성 파일([샘플 애플리케이션](https://aka.ms/search-dotnet-howto)의 경우 `appsettings.json`)에 저장된 api 키와 검색 서비스 이름의 값을 사용하여 "호텔" 인덱스의 새 `SearchIndexClient`를 만듭니다.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` [`Documents`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet) 속성이 있습니다. 이 속성은 Azure Search 인덱스를 쿼리하는 데 필요한 모든 메서드를 제공합니다.

## <a name="construct-searchparameters"></a>SearchParameters 생성
.NET SDK로 검색하는 작업은 `SearchIndexClient`에서 `Documents.Search` 메서드를 호출하는 것 만큼 간단합니다. 이 메서드는 쿼리를 구체화하는 데 사용할 수 있는 `SearchParameters` 개체와 함께 검색 텍스트를 비롯한 몇 가지 매개 변수를 사용합니다.

### <a name="types-of-queries"></a>쿼리 유형
사용할 두 가지 기본 [쿼리 유형](search-query-overview.md#types-of-queries)은 `search` 및 `filter`입니다. `search` 쿼리는 인덱스에서 모든 *검색 가능한* 필드에 대해 하나 이상의 단어를 검색합니다. `filter` 쿼리는 인덱스의 *필터링 가능한* 모든 필드에 걸쳐 부울 식을 계산합니다. 검색 및 필터를 함께 또는 별도로 사용할 수 있습니다.

검색 및 필터는 모두 `Documents.Search` 메서드를 사용하여 수행됩니다. 검색 쿼리는 `searchText` 매개 변수에 전달될 수 있는 반면 필터 식은 `SearchParameters` 클래스의 `Filter` 속성에 전달될 수 있습니다. 검색하지 않고 필터링하려면 `searchText` 매개 변수에 `"*"`를 전달합니다. 필터링하지 않고 검색하려면 `Filter` 속성을 설정하지 않고 그대로 두거나 `SearchParameters` 인스턴스에 전달하지 않아야 합니다.

### <a name="example-queries"></a>예제 쿼리
다음 샘플 코드는 [C#으로 Azure Search 인덱스 만들기](search-create-index-dotnet.md#DefineIndex)에 정의된 "호텔" 인덱스를 쿼리하는 몇 가지 방법을 보여줍니다. 검색 결과와 함께 반환된 문서는 `Hotel` 클래스의 인스턴스이며, [C#으로 Azure Search 인덱스로 데이터 가져오기](search-import-data-dotnet.md#construct-indexbatch)에서 정의되었습니다. 샘플 코드는 `WriteDocuments` 메서드를 사용하여 콘솔에 검색 결과를 출력합니다. 이 메서드는 다음 섹션에 설명되어 있습니다.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>검색 결과 처리
`Documents.Search` 메서드는 쿼리의 결과를 포함하는 `DocumentSearchResult` 개체를 반환합니다. 이전 섹션의 예제에서는 콘솔에 검색 결과 출력하기 위해 `WriteDocuments` 이라는 메서드를 사용했습니다.

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

다음은 이전 섹션의 쿼리가 보여주는 결과이며, "호텔" 인덱스가 다음 샘플 데이터로 채워진다고 가정합니다.

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

위의 샘플 코드는 콘솔을 사용하여 검색 결과를 출력합니다. 마찬가지로 고유한 애플리케이션에 검색 결과를 표시해야 합니다. ASP.NET MVC 기반 웹 애플리케이션에서 검색 결과를 렌더링하는 방법의 예는 GitHub의 [DotNetSample 프로젝트](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample)를 참조하세요.

## <a name="next-steps"></a>다음 단계

GitHub의 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)에 제공되는 샘플 코드, 그리고 샘플 코드에 대한 자세한 설명을 제공하는 [.NET 애플리케이션에서 Azure Search를 사용하는 방법](search-howto-dotnet-sdk.md)을 아직 검토하지 않은 분들은 지금 검토하세요. 