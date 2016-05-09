<properties
   pageTitle=".NET 응용 프로그램에서 Azure 검색을 사용하는 방법 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
   description=".NET 응용 프로그램에서 Azure 검색을 사용하는 방법"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="03/08/2016"
   ms.author="brjohnst"/>

# .NET 응용 프로그램에서 Azure 검색을 사용하는 방법

이 문서는 [Azure 검색.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)를 준비하여 실행하기 위한 연습입니다. Azure 검색을 사용하여 응용 프로그램에서 풍부한 검색 환경을 구현하는 .NET SDK를 사용할 수 있습니다.

## Azure 검색 SDK의 주요 기능

SDK는 클라이언트 라이브러리 `Microsoft.Azure.Search`로 구성되어 있습니다. 인덱스, 데이터 원본 및 인덱서를 관리할 뿐만 아니라 문서를 업로드 및 관리, 쿼리를 실행할 수 있으며, 이 모두를 HTTP와 JSON의 세부 정보를 처리하지 않고 수행할 수 있습니다.

클라이언트 라이브러리는 `SearchServiceClient` 및 `SearchIndexClient` 클래스에서 `Index`, `Field`, `Document` 등의 클래스와 `Indexes.Create` 및 `Documents.Search` 등의 작업을 정의합니다. 이러한 클래스는 다음과 같은 네임 스페이스에 구성됩니다.

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

Azure 검색 .NET SDK의 현재 버전이 이제 일반 공급됩니다. 다음 버전에 반영하기 위한 피드백을 제공하려는 경우 [피드백 페이지](https://feedback.azure.com/forums/263029-azure-search/)를 방문하세요.

.NET SDK는 [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 문서화된 Azure 검색 REST API의 `2015-02-28` 버전을 지원합니다. 이 버전은 이제 Lucene 쿼리 구문 및 Microsoft 언어 분석기에 대한 지원을 포함하고 있습니다. `moreLikeThis` 매개 변수 검색 등 이 버전의 일부가 *아닌* 새로운 기능은 [미리 보기](search-api-2015-02-28-preview.md)에 있으나 SDK에서는 아직 사용할 수 없습니다. 기능에 대한 업데이트 여부는 [검색 서비스 버전 관리](https://msdn.microsoft.com/library/azure/dn864560.aspx) 또는 [Azure 검색의 최신 업데이트](search-latest-updates.md)에서 다시 확인할 수 있습니다

이 SDK에서 지원되지 않는 기타 기능에는

  - [관리 작업](https://msdn.microsoft.com/library/azure/dn832684.aspx)이 있습니다. 관리 작업에는 Azure 검색 서비스 프로비전과 API 키 관리가 있습니다. 이들 기능은 향후 별도의 Azure 검색.NET 관리 SDK에서 지원됩니다.

## 최신 버전의 SDK로 업그레이드

Azure 검색 .NET SDK 이전 버전을 사용하는 경우 새로운 일반 공급 버전으로 업그레이드하려면 [이 문서](search-dotnet-sdk-migration.md)에서 방법을 참조하세요.

## SDK의 요구 사항

1. Visual Studio 2013 또는 Visual Studio 2015

2. Azure 검색 서비스 SDK를 사용하려면 서비스 이름과 하나 이상의 API 키가 필요합니다. [포털에서 서비스 만들기](search-create-service-portal.md)는 이들 단계를 통해 도움을 받을 수 있습니다.

3. Visual Studio에서 "NuGet 패키지 관리"를 사용하여 Azure 검색.NET SDK [NuGet 패키지](http://www.nuget.org/packages/Microsoft.Azure.Search)를 다운로드하십시오. NuGet.org에서 패키지 이름 `Microsoft.Azure.Search`을(를) 검색하십시오.

Azure 검색.NET SDK는.NET Framework 4.5를 대상으로 한 응용 프로그램과 Windows 8.1 및 Windows Phone 8.1을 대상으로 하는 Windows 스토어 앱을 지원합니다. Silverlight는 지원되지 않습니다.

## 핵심 시나리오

검색 응용 프로그램에서 수행해야 할 몇 가지 작업이 있습니다. 이 자습서에서는 이러한 핵심 시나리오를 다룹니다.

- 인덱스 만들기
- 문서를 사용하여 인덱스 채우기
- 전체 텍스트 검색 및 필터를 사용하여 문서 검색하기

이들 각각을 설명하는 샘플 코드 사용자 응용 프로그램에서 코드 조각을 자유롭게 사용하십시오.

### 개요

"호텔"이라는 이름의 새로운 인덱스를 탐색하려는 샘플 응용 프로그램은 몇몇 문서로 채운 다음, 일부 검색 쿼리를 실행합니다. 전체 흐름을 보여주는 주 프로그램은 다음과 같습니다.

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

단계별로 연습해보겠습니다. 먼저 새로운 `SearchServiceClient`을(를) 만들어야 합니다. 이 개체를 통해 인덱스를 관리할 수 있습니다. 하나를 생성하기 위해 Azure 검색 서비스 이름과 관리 API 키를 제공해야 합니다.

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE] 잘못된 키(예, 관리 키가 필요한 쿼리 키)를 제공하는 경우, `SearchServiceClient`은(는) `Indexes.Create`와(과) 같은 작업 메서드를 처음 호출하면 "사용할 수 없음"이라는 오류 메시지와 함께 `CloudException`을(를) 발생시킵니다. 이 경우 API 키를 다시 확인하십시오.

다음 몇 줄은 "호텔"이라는 인덱스를 만드는 메서드를 호출하며, 이미 있는 경우 이를 먼저 삭제합니다. 잠시 후 이들 메서드를 연습해보겠습니다.

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

그런 다음 인덱스를 채워야 합니다. 이를 위해서는 `SearchIndexClient`이(가) 필요합니다. 두 가지 방법 즉, 키를 생성하거나 `SearchServiceClient`에서 `Indexes.GetClient`을(를) 호출하여 키를 얻을 수 있습니다. 편의를 위해 후자를 사용합니다.

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE] 일반적인 검색 응용 프로그램에서 인덱스 관리 및 채우기는 검색 쿼리와는 별도의 구성 요소에 의해 처리됩니다. `Indexes.GetClient`은(는) 다른 `SearchCredentials`을(를) 제공하는데 문제가 생기지 않도록 하기 때문에 인덱스 채우기에 편리합니다. 이는 새 `SearchIndexClient`에 `SearchServiceClient`을(를) 만드는 데 사용하는 관리 키를 눌러 수행됩니다. 그러나 쿼리를 실행하는 일부 응용 프로그램의 경우, `SearchIndexClient`을(를) 직접 만들어 관리 키 대신에 쿼리 키에서 전달하는 것이 더 낫습니다. 이는 최소 권한의 원칙와 일치하고 응용 프로그램을 더욱 안전하게 하는데 도움이 됩니다. 관리 키와 쿼리 키에 대한 자세한 내용은 [여기](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 확인할 수 있습니다.

이제 `SearchIndexClient`이 생겼고 인덱스를 채울 수 있습니다. 이것은 나중에 연습할 또 다른 메서드에 의해 수행됩니다.

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

마지막으로 몇몇 검색 쿼리를 실행하고 `SearchIndexClient`을(를) 다시 사용하여 결과를 표시하겠습니다.

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

유효한 서비스 이름 및 API 키로 이 응용 프로그램을 실행하는 경우, 출력은 다음과 같이 보입니다.

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury	Tags: []
    Complete.  Press any key to end application...

응용 프로그램의 전체 소스 코드는이 문서의 마지막 부분에 제공됩니다.

다음으로, `Main`에 의해 호출된 각 메서드를 좀더 자세히 살펴볼 것입니다.

### 인덱스 만들기

`SearchServiceClient`을(를) 만든 후, `Main`이(가) 하는 다음 일은 이미 있는 "호텔" 인덱스를 삭제하는 것입니다. 작업은 다음과 같은 메서드로 수행 됩니다.

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

이 메서드는 주어진 `SearchServiceClient`을(를) 사용하여 인덱스가 존재하는지 확인하고 존재하면 이를 삭제합니다.

> [AZURE.NOTE] 이 문서의 예제 코드는 간단히 하기 위해 Azure 검색.NET SDK의 동기 메서드를 사용합니다. 확장성과 응답성이 유지하기 위해 사용 중인 응용 프로그램에서 비동기 메서드를 사용하는 것이 좋습니다. 예를 들어, 위의 메서드의 경우 `Exists` 및 `Delete` 대신`ExistsAsync` 및 `DeleteAsync`을(를) 사용할 수 있습니다.

그 다음 `Main`이(가) 이 메서드를 호출하여 새 "호텔" 인덱스를 만듭니다.

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

이 메서드는 새 인덱스의 스키마를 정의하는 `Field` 개체 목록과 함께 새 `Index` 개체를 만듭니다. 각 필드에는 이름, 데이터 유형, 그리고 검색 동작을 정의하는 몇 가지 특성이 있습니다. 필드 외에도, 점수 매기기 프로필, 서제스터 또는 CORS 옵션을 인덱스에 추가할 수도 있습니다(이들 필드는 간단하게 나타내기 위해 샘플에서 생략됩니다). [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx)의 SDK 참조뿐만 아니라 [Azure 검색 REST API 참조](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인덱스 개체와 그 구성 요소에 대한 자세한 정보를 찾을 수 있습니다.

### 인덱스 채우기

`Main`의 다음 단계는 새로 만든 인덱스를 채우는 것입니다. 이것은 다음 메서드로 수행됩니다.

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                {
                    HotelId = "1058-441",
                    HotelName = "Fancy Stay",
                    BaseRate = 199.0,
                    Category = "Luxury",
                    Tags = new[] { "pool", "view", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                    Rating = 5,
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "666-437",
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "970-501",
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "956-532",
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "566-518",
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(documents);
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

이 메서드는 네 부분으로 이루어져 있습니다. 첫 번째 부분은 입력 데이터를 인덱스에 업로드할 때 사용되는 다양한 `Hotel` 개체를 만듭니다. 이 데이터는 간단히 하기 위해 하드 코딩합니다. 사용 중인 응용 프로그램의 경우, 데이터는 SQL 데이터베이스와 같은 외부 데이터 원본에서 나올 가능성이 높습니다.

두 번째 부분은 문서를 포함하는 `IndexBatch`를 만듭니다. 배치를 만들 때 배치에 적용할 작업을 지정합니다. 이 경우에는 `IndexBatch.Upload`를 호출하여 지정합니다. 그런 다음 `Documents.Index` 메서드를 통해 Azure 검색 인덱스에 일괄적으로 업로드합니다.

> [AZURE.NOTE] 이 예에서는 문서를 업로드하는 것입니다. 변경 사항을 기존 문서에 병합하거나 문서를 삭제하려면 `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, 또는 `IndexBatch.Delete`를 호출하여 배치를 만듭니다. `IndexBatch.New`를 호출하여 단일 배치 내에 다른 작업들을 혼합할 수 있으며, 이것은 `IndexAction` 개체 컬렉션을 사용하고, 이들 각각은 Azure 검색이 문서의 특정 작업을 수행하도록 지시합니다. `IndexAction.Merge`, `IndexAction.Upload` 등을 비롯한 해당 메서드를 호출하여 각 `IndexAction`과 자체 작업을 만들 수 있습니다.

이 메서드의 세 번째 부분은 인덱싱에 중요한 오류 사례를 처리하는 catch 블록입니다. Azure 검색 서비스가 일괄 처리에서 문서 일부를 인덱싱하는데 실패하는 경우 `Documents.Index`에 의해 `IndexBatchException`이(가) 발생합니다. 이는 부하가 높은 상태에서 서비스 되는 동안에 문서를 인덱싱는 경우 발생할 수 있습니다. **이 경우 코드에서 명시적으로 처리하는 것이 좋습니다.** 실패한 문서 인덱싱을 잠시 후 다시 시도하거나, 샘플에서 하던 것처럼 기록하여 계속하거나, 응용 프로그램의 데이터 일관성 요구 사항에 따라 다른 작업을 수행할 수 있습니다.

마지막으로, 메서드가 2초 동안 지연됩니다. Azure 검색 서비스에서 인덱싱이 비동기적으로 발생하기 때문에, 샘플 응용 프로그램은 문서 검색을 위해 잠시 기다려야 합니다. 이와 같이 데모, 테스트, 샘플 응용 프로그램에서는 일반적으로 지연만 필요합니다.

#### .NET SDK가 문서를 처리하는 방법

Azure 검색.NET SDK가 어떻게 `Hotel`와(과) 같은 사용자 정의 클래스의 인스턴스를 업로드할 수 있는지 궁금할 수 있습니다. 이 질문에 대답하기 위해 `Hotel` 클래스를 살펴보겠습니다.

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

먼저 주목할 것은 `Hotel`의 각 공용 속성이 인덱스 정의의 필드와 일치하지만 한 가지 중요한 차이가 있습니다. 각 필드의 이름은 소문자("카멜식 대/소문자")로 시작하지만, `Hotel`의 각 공용 속성 이름은 대문자 문자("파스칼식 대/소문자")로 시작합니다. 이것은 대상 스키마가 응용 프로그램 개발자의 제어 범위를 벗어난 데이터 바인딩을 수행하는 .NET 응용 프로그램의 일반적인 시나리오입니다. 카멜식 대/소문자 속성으로 이름을 지정하면 .NET 이름 지정 지침을 위반하지 않고 속성 이름을 자동으로 `[SerializePropertyNamesAsCamelCase]` 특성을 지닌 카멜식 대/소문자에 매핑하도록 SDK에 명령할 수 있습니다.

> [AZURE.NOTE] Azure 검색 .NET SDK는 [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) 라이브러리를 사용하여 사용자 지정 모델을 JSON과 직렬화 및 역질렬화합니다. 필요한 경우 직렬화를 사용자 지정할 수 있습니다. 자세한 내용은 [여기](search-dotnet-sdk-migration.md#WhatsNew)에서 볼 수 있습니다.

`Hotel` 클래스에 대해 두 번째로 중요한 부분은 공용 속성의 데이터 유형입니다. 이러한 속성의 .NET 유형은 인덱스 정의의 동등한 필드 유형에 매핑합니다. 예를 들어, `Category` 문자열 속성은 `Edm.String` 유형인 `category` 필드에 매핑됩니다. `bool?` 및 `Edm.Boolean`, `DateTimeOffset?` 및 `Edm.DateTimeOffset` 사이에는 유사한 유형 매핑이 있습니다. 유형 매핑에 대한 특정 규칙은 [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx)에 `Documents.Get` 메서드로 문서화됩니다.

사용자의 클래스를 문서로서 사용하는 이 능력은 양방향으로 사용 가능합니다. 또한 다음 섹션에서 확인할 수 있듯이 검색 결과를 검색하고 이 검색 결과를 SDK가 자동으로 사용자가 선택한 유형으로 역직렬화하도록 할 수도 있습니다.

> [AZURE.NOTE] Azure 검색.NET SDK는 필드 이름을 필드 값에 매핑하는 키/값인 `Document` 클래스를 사용하여 동적 유형의 문서도 지원합니다. 디자인 타임에서 인덱스 스키마를 알 수 없거나 특정 모델 클래스에 바인딩하기 불편한 시나리오에서 유용합니다. 문서를 처리하는 SDK의 모든 메서드에는 `Document` 클래스와 연동하는 오버로드와 제네릭 유형 매개 변수를 취하는 강력한 유형의 오버로드가 있습니다. 이 자습서의 샘플 코드에서는 후자만 사용됩니다. `Document` 클래스에 대한 자세한 내용은 [여기](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx)에서 확인할 수 있습니다.

**데이터 유형에 대한 중요한 정보**

사용자 고유의 모델 클래스를 Azure 검색 인덱스에 매핑하도록 설계하는 경우 `bool` 및 `int` 등과 같은 값 유형의 속성을 null이 허용되도록 선언하는 것이 좋습니다(예: `bool?` 대신 `bool`). Null이 허용되지 않는 속성을 사용하는 경우 인덱스의 문서가 해당 필드에 대해 Null 값을 포함하지 않음을 **보장**해야 합니다. SDK와 Azure 검색 서비스 모두 이를 적용하는 데 활용할 수 없습니다.

이것은 가상의 문제가 아닙니다. `Edm.Int32` 형식인 기존 인덱스에 새 필드를 추가하는 시나리오를 상상해 보세요. 인덱스 정의를 업데이트한 후 모든 문서는 해당하는 새 필드에 대해 Null 값을 포함하게 됩니다(Azure 검색에서 모든 형식은 Null을 허용하기 때문). 그런 다음 해당 필드에 대해 Null이 허용되지 않는 `int` 속성으로 모델 클래스를 사용하는 경우 문서를 검색하려고 시도할 때 다음과 같은 `JsonSerializationException`이 발생합니다.

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

이러한 이유로 모델 클래스에는 Null을 허용하는 형식을 사용하는 것이 가장 좋습니다.

### 인덱스에서 문서 검색

샘플 응용 프로그램의 마지막 단계는 인덱스의 일부 문서를 검색하는 것입니다. 다음 메서드가 이를 수행합니다.

    private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response.Results)
        {
            Console.WriteLine(result.Document);
        }
    }

첫째, 이 메서드는 새로운 `SearchParameters` 개체를 만듭니다 이는 정렬, 필터링, 페이징, 패시팅 같은 쿼리에 대한 추가 옵션을 지정하는 데 사용됩니다. 이 예에서는 `Filter` 속성만 설정합니다.

다음 단계를 실제로 검색 쿼리를 실행합니다. 이는 `Documents.Search` 메서드를 사용하여 실행됩니다. 이 경우, 문자열로 사용할 검색 텍스트와 앞서 만든 검색 매개 변수를 전달합니다. 또한 SDK에 검색 결과 내 문서를 `Hotel` 유형의 개체로 역직렬화하도록 명령하는 `Documents.Search`에 대한 유형 매개 변수로 `Hotel`을(를) 지정할 수도 있습니다.

마지막으로, 이 메서드는 검색 결과에서 일치하는 모든 항목을 반복하여 각 문서를 콘솔에 인쇄합니다.

이 메서드를 호출하는 방법을 자세히 살펴보겠습니다.

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

첫 번째 호출에서는 "멋진" 또는 "wifi"라는 쿼리 용어를 포함하는 모든 문서를 찾습니다. 두번째 호출에서는 검색 텍스트를 "모두 찾기"를 의미하는 "*"로 설정합니다. 검색 쿼리 식 구문에 대한 자세한 내용은 [여기](https://msdn.microsoft.com/library/azure/dn798920.aspx)에서 찾을 수 있습니다.

두 번째 호출은 OData `$filter` 식인 `category eq 'Luxury'`을(를) 사용합니다. 이는 `category` 필드가 "사치"라는 문자열과 정확히 일치하는 반환 문서로만 검색을 제한합니다. Azure 검색이 지원하는 OData 구문에 대한 자세한 내용은 [여기](https://msdn.microsoft.com/library/azure/dn798921.aspx)에서 확인할 수 있습니다.

이제 이러한 두 호출이 수행하는 작업을 파악했으므로 해당 출력이 왜 이와 같이 보이는지 보다 쉽게 알 수 있습니다.

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury	Tags: []

첫 번째 검색은 두 문서를 반환합니다. 첫 번째 문서에는 이름에 "멋진"이라는 문자열이 있고 두 번째 문서에 는`tags` 필드에 "wifi"가 있습니다. 두 번째 검색은 두 문서를 반환하고 "사치"로 설정된 `category` 필드가 있는 문서만 인덱스에 나타납니다.

이 단계에서 자습서를 완료하지만 여기서 멈추지 마십시오. **다음 단계**에서는 Azure 검색에 대해 자세히 학습하기 위한 추가 리소스가 제공됩니다.

## 다음 단계

- MSDN에서 [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) 및 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에 대한 참고 자료를 찾아봅니다.
- [비디오와 기타 샘플 및 자습서](search-video-demo-tutorial-list.md)를 통해 지식을 심화시키십시오.
- 이 버전의 Azure 검색 SDK의 특징과 기능에 관한 내용인 [Azure 검색 개요](https://msdn.microsoft.com/library/azure/dn798933.aspx)를 읽어 보십시오.
- [명명 규칙](https://msdn.microsoft.com/library/azure/dn857353.aspx)을 검토하여 다양한 개체 명명에 대한 규칙에 알아보십시오.
- Azure 검색에서 [지원되는 데이터 유형](https://msdn.microsoft.com/library/azure/dn798938.aspx)을 검토하십시오.


## 샘플 응용 프로그램 소스 코드

여기에는 이 연습에서 사용된 샘플 응용 프로그램의 전체 소스 코드가 나와 있습니다. 샘플을 빌드하고 실행하려는 경우 Program.cs의 서비스 이름과 API 키 자리 표시자를 사용자의 값으로 대체해야 합니다.

Program.cs:

    using System;
    using System.Configuration;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.SDKHowTo
    {
        class Program
        {
            // This sample shows how to delete, create, upload documents and query an index
            static void Main(string[] args)
            {
                // Put your search service name here. This is the hostname portion of your service URL.
                // For example, if your service URL is https://myservice.search.windows.net, then your
                // service name is myservice.
                string searchServiceName = "myservice";

                string apiKey = "Put your API admin key here."

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteHotelsIndexIfExists(serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateHotelsIndex(serviceClient);

                SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

                Console.WriteLine("{0}", "Uploading documents...\n");
                UploadDocuments(indexClient);

                Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
                SearchDocuments(indexClient, searchText: "fancy wifi");

                Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
                SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists("hotels"))
                {
                    serviceClient.Indexes.Delete("hotels");
                }
            }

            private static void CreateHotelsIndex(SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = "hotels",
                    Fields = new[]
                    {
                        new Field("hotelId", DataType.String)                       { IsKey = true },
                        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                    }
                };

                serviceClient.Indexes.Create(definition);
            }

            private static void UploadDocuments(SearchIndexClient indexClient)
            {
                var documents =
                    new Hotel[]
                    {
                        new Hotel()
                        {
                            HotelId = "1058-441",
                            HotelName = "Fancy Stay",
                            BaseRate = 199.0,
                            Category = "Luxury",
                            Tags = new[] { "pool", "view", "concierge" },
                            ParkingIncluded = false,
                            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                            Rating = 5,
                            Location = GeographyPoint.Create(47.678581, -122.131577)
                        },
                        new Hotel()
                        {
                            HotelId = "666-437",
                            HotelName = "Roach Motel",
                            BaseRate = 79.99,
                            Category = "Budget",
                            Tags = new[] { "motel", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                            Rating = 1,
                            Location = GeographyPoint.Create(49.678581, -122.131577)
                        },
                        new Hotel()
                        {
                            HotelId = "970-501",
                            HotelName = "Econo-Stay",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "pool", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(46.678581, -122.131577)
                        },
                        new Hotel()
                        {
                            HotelId = "956-532",
                            HotelName = "Express Rooms",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "wifi", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(48.678581, -122.131577)
                        },
                        new Hotel()
                        {
                            HotelId = "566-518",
                            HotelName = "Surprisingly Expensive Suites",
                            BaseRate = 279.99,
                            Category = "Luxury",
                            ParkingIncluded = false
                        }
                    };

                try
                {
                    var batch = IndexBatch.Upload(documents);
                    indexClient.Documents.Index(batch);
                }
                catch (IndexBatchException e)
                {
                    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                    // the batch. Depending on your application, you can take compensating actions like delaying and
                    // retrying. For this simple demo, we just log the failed document keys and continue.
                    Console.WriteLine(
                        "Failed to index some of the documents: {0}",
                        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
                }

                // Wait a while for indexing to complete.
                Thread.Sleep(2000);
            }

            private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
            {
                // Execute search based on search text and optional filter
                var sp = new SearchParameters();

                if (!String.IsNullOrEmpty(filter))
                {
                    sp.Filter = filter;
                }

                DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
                foreach (SearchResult<Hotel> result in response.Results)
                {
                    Console.WriteLine(result.Document);
                }
            }
        }
    }

Hotel.cs:

    using System;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.SDKHowTo
    {
        [SerializePropertyNamesAsCamelCase]
        public class Hotel
        {
            public string HotelId { get; set; }

            public string HotelName { get; set; }

            public double? BaseRate { get; set; }

            public string Category { get; set; }

            public string[] Tags { get; set; }

            public bool? ParkingIncluded { get; set; }

            public DateTimeOffset? LastRenovationDate { get; set; }

            public int? Rating { get; set; }

            public GeographyPoint Location { get; set; }

            public override string ToString()
            {
                return String.Format(
                    "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                    HotelId,
                    HotelName,
                    Category,
                    (Tags != null) ? String.Join(", ", Tags) : String.Empty);
            }
        }
    }

<!---HONumber=AcomDC_0427_2016-->