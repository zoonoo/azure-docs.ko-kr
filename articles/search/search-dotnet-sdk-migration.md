<properties
   pageTitle="Azure 검색 .NET SDK 버전 1.0.1-Preview | Microsoft Azure | 호스트된 클라우드 검색 서비스"
   description="Azure 검색 .NET SDK 버전 1.0.1-preview로 업그레이드"
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
   ms.date="01/22/2016"
   ms.author="brjohnst"/>

# Azure 검색 .NET SDK 버전 1.0.1-preview로 업그레이드

버전 0.13.0-preview 또는 이전 버전의 [Azure 검색.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)를 사용하는 경우 이 문서를 통해 최신 버전인 1.0.1-preview로 응용 프로그램을 업그레이드할 수 있습니다.

예제를 비롯하여 SDK에 대한 보다 일반적인 연습은 [.NET 응용 프로그램에서 Azure 검색을 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

Azure 검색.NET SDK의 버전 1.0.1-preview에는 이전 버전(0.13.0-preview)에서 몇 가지 주요 변경된 내용이 포함됩니다. 대부분 소소한 변경이므로 코드를 변경하는 데 최소한의 작업만 필요합니다. 새 SDK 버전을 사용하는 코드를 변경하는 방법에 대한 지침은 [업그레이드 단계](#UpgradeSteps)를 참조하세요.

<a name="WhatsNew"></a>
## 1\.0.1-preview의 새로운 기능

버전 1.0.1-preview는 이전 버전의 Azure 검색 .NET SDK(2015-02-28)와 동일한 REST API를 대상으로 하므로 이 릴리스에는 새로운 서비스 기능이 없습니다. 하지만 새로운 클라이언트쪽 serialization 기능이 있습니다.

이 SDK는 문서를 직렬화 및 역직렬화하는 데 JSON.NET을 사용합니다. 새 버전의 SDK에서는 `JsonConverter` 및 `IContractResolver`를 통해 사용자 지정 serialization을 지원합니다(자세한 내용은 [JSON.NET 설명서](http://www.newtonsoft.com/json/help/html/Introduction.htm) 참조). 이 기능은 Azure 검색에 사용할 응용 프로그램에서 기존 모델 클래스를 적용하려는 경우와 기타 고급 시나리오에서 유용할 수 있습니다. 예를 들어 사용자 지정 serialization으로 다음을 수행할 수 있습니다.
 
 - 모델 클래스의 특정 속성을 문서 필드로 저장하는 데 포함 또는 제외할 수 있습니다.
 - 코드의 속성 이름과 인덱스의 필드 이름을 매핑할 수 있습니다.
 - 속성을 문서 필드로 매핑하고 해당 인덱스 정의를 만드는 데 사용할 수 있는 사용자 지정 특성을 만들 수 있습니다.

GitHub에서 Azure 검색 .NET SDK에 대한 단위 테스트에서 사용자 지정 serialization을 구현하는 예제를 찾을 수 있습니다. 적절한 시작 지점은 [이 폴더](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models)입니다. 여기에는 사용자 지정 serialization 테스트에 사용되는 클래스가 있습니다.

사용자 지정 serialization 외에, 새 SDK 역시 `SearchContinuationToken` 개체의 serialization을 지원합니다. 웹 응용 프로그램에서 Azure 검색을 호출하고 검색 결과를 통해 페이징을 하는 동안 브라우저 또는 모바일 클라이언트와 연속 토큰을 교환해야 하는 경우에 유용합니다.

<a name="UpgradeSteps"></a>
## 업그레이드 단계

먼저, NuGet 패키지 관리자 콘솔을 사용하거나 Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리..."를 선택하여 `Microsoft.Azure.Search`에 대한 NuGet 참조를 업데이트합니다.

> [AZURE.NOTE] Visual Studio를 사용하는 경우 "시험판 포함"을 선택하고 패키지 관리자 콘솔을 사용하는 경우 `-IncludePrerelease` 스위치를 사용하여 시험판 패키지를 볼 수 있는지 확인합니다.

NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 다음과 유사한 빌드 오류가 표시됩니다.

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

다음은 빌드 오류를 하나씩 수정하는 과정입니다. 대부분은 SDK에서 이름이 변경된 일부 클래스와 메서드 이름을 변경해야 합니다. [1\.0.1-preview의 주요 변경 내용 목록](#ListOfChanges)에는 이러한 이름 변경 목록이 있습니다.

사용자 지정 클래스를 사용하여 문서를 모델링하고 해당 클래스에 Null이 허용되지 않는 기본 형식(예를 들어 C#의 `int` 또는 `bool`)이 있는 경우 알고 있어야 할 SDK 1.0.1-preview 버전의 버그 수정이 있습니다. 자세한 내용은 [1\.0.1-preview의 버그 수정](#BugFixes)을 참조하세요.

마지막으로, 모든 빌드 오류를 수정했다면 원하는 새 기능을 활용하도록 응용 프로그램을 변경할 수 있습니다. 새 SDK의 사용자 지정 serialization 기능은 [1\.0.1-preview의 새로운 기능](#WhatsNew)에 자세히 설명되어 있습니다.

<a name="ListOfChanges"></a>
## 1\.0.1-preview의 주요 변경 내용 목록

다음 목록은 변경이 응용 프로그램 코드에 영향을 줄 가능성 순서로 정렬되어 있습니다.

### IndexBatch 및 IndexAction 변경

`IndexBatch.Create`는 `IndexBatch.New`로 이름이 변경되었고 더 이상 `params` 인수를 포함하지 않습니다. 다양한 유형의 작업(병합, 삭제 등)이 혼합된 배치에는 `IndexBatch.New`를 사용할 수 있습니다. 또한 모든 작업이 동일한 배치를 만드는 새로운 정적 메서드가 제공됩니다(`Delete`, `Merge`, `MergeOrUpload` 및 `Upload`).

`IndexAction`은 더 이상 public 생성자를 포함하지 않으며 해당 속성은 변경할 수 없습니다. `Delete`, `Merge`, `MergeOrUpload` 및 `Upload`와 같은 다양한 용도의 작업을 만들기 위해서는 새로운 정적 메서드를 사용해야 합니다. `IndexAction.Create`는 제거되었습니다. 한 개의 문서만 받는 오버로드를 사용한 경우 대신 `Upload`를 사용해야 합니다.

#### 예

코드는 다음과 같습니다.

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

빌드 오류를 수정하기 위해 다음으로 변경할 수 있습니다.

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

원하는 경우 이를 다음과 같이 보다 단순화할 수 있습니다.

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### IndexBatchException 변경

`IndexBatchException.IndexResponse` 속성이 `IndexingResults`로 이름이 변경되었고 형식은 이제 `IList<IndexingResult>`입니다.

#### 예

코드는 다음과 같습니다.

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

빌드 오류를 수정하기 위해 다음으로 변경할 수 있습니다.

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
### 작업 메서드 변경

Azure 검색 .NET SDK의 각 작업은 동기 및 비동기 호출자에 대한 메서드 오버로드 집합으로 노출됩니다. 이러한 메서드 오버로드의 서명 및 팩터링이 1.0.1-preview에서 변경되었습니다.

예를 들어 이전 버전의 SDK에서 "인덱스 통계 가져오기" 작업은 다음과 같은 서명을 노출했습니다.

`IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

`IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

버전 1.0.1-preview에서 동일한 작업에 대한 메서드 서명은 다음과 같습니다.

`IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

`IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations, 
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

버전 1.0.1-preview부터 Azure 검색 .NET SDK가 작업 메서드를 다르게 구성합니다.
 
 - 이제 선택적 매개 변수는 추가 메서드 오버로드가 아닌 기본 매개 변수로 모델링됩니다. 따라서 메서드 오버로드 수가 경우에 따라서는 상당히 줄어듭니다.
 - 이제 확장 메서드는 호출자에게 HTTP의 불필요한 많은 세부 정보를 숨깁니다. 예를 들어 이전 버전의 SDK에서는 HTTP 상태 코드와 함께 응답 개체를 반환했지만 작업 메서드가 오류를 나타내는 상태 코드에 대해 `CloudException`을 throw하므로 보통은 이를 확인할 필요가 없었습니다. 새로운 확장 메서드는 모델 개체만 반환하여 코드에서 래핑 해제해야 하는 문제가 없어집니다.
 - 반대로, 코어 인터페이스가 HTTP 수준에서 필요한 경우 보다 세밀한 제어를 제공하는 메서드를 노출합니다. 이제 요청에 포함될 사용자 지정 HTTP 헤더를 전달할 수 있으며 새로운 `AzureOperationResponse<T>` 반환 형식으로 작업할 `HttpRequestMessage` 및 `HttpResponseMessage`에 직접 액세스할 수 있습니다. `AzureOperationResponse`는 `Microsoft.Rest.Azure` 네임스페이스에 정의되며 `Hyak.Common.OperationResponse`를 대체합니다.

### 모델 클래스 변경

[작업 메서드 변경](#OperationMethodChanges)에 설명된 서명 변경으로 인해 `Microsoft.Azure.Search.Models` 네임스페이스의 많은 클래스가 이름이 변경되거나 제거되었습니다. 예:

 - `IndexDefinitionResponse`는 `AzureOperationResponse<Index>`로 대체되었습니다.
 - `DocumentSearchResponse`는 `DocumentSearchResult`로 이름이 변경되었습니다.
 - `IndexResult`는 `IndexingResult`로 이름이 변경되었습니다.
 - `Documents.Count()`는 `DocumentCountResponse` 대신 문서 수와 함께 `long`을 반환합니다.
 - `IndexGetStatisticsResponse`는 `IndexGetStatisticsResult`로 이름이 변경되었습니다.
 - `IndexListResponse`는 `IndexListResult`로 이름이 변경되었습니다.

요약하자면 모델 개체를 래핑하기 위해서만 존재했던 `OperationResponse` 파생 클래스가 제거되었습니다. 나머지 클래스는 접미사가 `Response`에서 `Result`로 변경되었습니다.

#### 예

코드는 다음과 같습니다.

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

빌드 오류를 수정하기 위해 다음으로 변경할 수 있습니다.

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

#### 응답 클래스 및 IEnumerable

코드에 영향을 줄 수 있는 추가 변경은 더 이상 `IEnumerable<T>`을 구현하지 않는 컬렉션을 보유하는 응답 클래스입니다. 대신, 컬렉션 속성에 직접 액세스할 수 있습니다. 예를 들어 코드는 다음과 같습니다.

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

빌드 오류를 수정하기 위해 다음으로 변경할 수 있습니다.

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### 웹 응용 프로그램에 대한 중요 참고 사항

`DocumentSearchResponse`를 직접 serialize하여 검색 결과를 브라우저로 보내는 웹 응용 프로그램이 있는 경우 코드를 변경해야 하며 그렇지 않은 경우 결과가 제대로 serialize되지 않습니다. 예를 들어 코드는 다음과 같습니다.

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

검색 결과 렌더링을 수정하기 위해 검색 응답의 `.Results` 속성을 가져와 이를 변경할 수 있습니다.

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

코드에서 직접 이러한 경우를 찾아야 합니다. `JsonResult.Data`가 `object` 형식이므로 **컴파일러가 경고해주지 않습니다**.

### CloudException 변경

`CloudException` 클래스가 `Hyak.Common` 네임스페이스에서 `Microsoft.Rest.Azure` 네임스페이스로 이동되었습니다. 또한 해당 `Error` 속성의 이름이 `Body`로 변경되었습니다.

### SearchServiceClient 및 SearchIndexClient 변경

`Credentials` 속성의 형식이 `SearchCredentials`에서 기본 클래스 `ServiceClientCredentials`로 변경되었습니다. `SearchIndexClient` 또는 `SearchServiceClient`의 `SearchCredentials`에 액세스해야 하는 경우 새 `SearchCredentials` 속성을 사용하세요.

이전 버전의 SDK에서 `SearchServiceClient` 및 `SearchIndexClient`는 `HttpClient` 매개 변수를 사용하는 생성자를 포함했습니다. 이는 `HttpClientHandler` 및 `DelegatingHandler` 개체 배열을 사용하는 생성자로 대체되었습니다. 이렇게 하면 필요한 경우 전처리 HTTP 요청에 사용자 지정 처리기를 쉽게 설치할 수 있습니다.

마지막으로 `Uri` 및 `SearchCredentials`를 사용하는 생성자가 변경되었습니다. 예를 들어, 다음과 같은 코드가 있는 경우

    var client = 
        new SearchServiceClient(
            new SearchCredentials("abc123"), 
            new Uri("http://myservice.search.windows.net"));

빌드 오류를 수정하기 위해 다음으로 변경할 수 있습니다.

    var client = 
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123")); 

또한 자격 증명 매개 변수의 형식이 `ServiceClientCredentials`로 변경되었습니다. `SearchCredentials`는 `ServiceClientCredentials`에서 파생되므로 코드에 영향을 줄 가능성은 없습니다.

### 요청 ID 전달

이전 버전의 SDK에서는 요청 ID를 `SearchServiceClient` 또는 `SearchIndexClient`에서 설정하고 REST API에 대한 모든 요청에 포함했습니다. 지원에 문의해야 하는 경우 검색 서비스로 문제를 해결하는 데 유용합니다. 그러나 모든 작업에 대해 동일한 ID를 사용하기보다는 모든 작업에 고유한 요청 ID를 설정하는 것이 더 유용합니다. 이러한 이유로 `SearchServiceClient` 및 `SearchIndexClient`의 `SetClientRequestId` 메서드는 제거되었습니다. 대신, `SearchRequestOptions` 매개 변수(선택 사항)를 통해 요청 ID를 각 작업 메서드에 전달할 수 있습니다.

> [AZURE.NOTE] SDK의 이후 릴리스에서는 다른 Azure SDK에서 사용하는 방법과 일치하는 클라이언트 개체에서 요청 ID를 전역적으로 설정하는 새 메커니즘을 추가할 예정입니다.

#### 예

다음과 같은 코드가 있는 경우

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

빌드 오류를 수정하기 위해 다음으로 변경할 수 있습니다.

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### 인터페이스 이름 변경

작업 그룹 인터페이스 이름이 해당 속성 이름과 일치하도록 모두 변경되었습니다.
 
 - `ISearchServiceClient.Indexes` 형식이 `IIndexOperations`에서 `IIndexesOperations`로 이름 변경되었습니다.
 - `ISearchServiceClient.Indexers` 형식이 `IIndexerOperations`에서 `IIndexersOperations`로 이름 변경되었습니다.
 - `ISearchServiceClient.DataSources` 형식이 `IDataSourceOperations`에서 `IDataSourcesOperations`로 이름 변경되었습니다.
 - `ISearchIndexClient.Documents` 형식이 `IDocumentOperations`에서 `IDocumentsOperations`로 이름 변경되었습니다.

이 변경은 테스트 용도로 모의 인터페이스를 만들지 않은 경우 코드에 영향을 줄 가능성은 없습니다.

<a name="BugFixes"></a>
## 1\.0.1-preview의 버그 수정

이전 버전의 Azure 검색 .NET SDK에는 사용자 지정 모델 클래스의 serialization과 관련된 버그가 있었습니다. Null이 허용되지 않는 값 형식의 속성으로 사용자 지정 모델 클래스를 만든 경우 버그가 발생할 수 있습니다.

### 재현 단계

Null이 허용되지 않는 값 형식의 속성으로 사용자 지정 모델 클래스를 만듭니다. 예를 들어 `int?` 대신 `int` 형식의 공용 `UnitCount` 속성을 추가합니다.

해당 형식의 기본 값을 사용하여 문서를 인덱싱할 경우(예: `int`에 대해 0) 필드는 Azure 검색에서 Null이 됩니다. 이후에 해당 문서를 검색하면 `Search` 호출은 `null`을 `int`로 변환할 수 없다는 `JsonSerializationException`을 throw합니다.

또한 Null이 의도한 값 대신 인덱스에 기록되었으므로 필터가 예상대로 작동하지 않을 수 있습니다.

### 수정 세부 정보

SDK 버전 1.0.1-preview에서 이 문제를 해결했습니다. 이제 다음과 같이 모델 클래스가 있고

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

`IntValue`를 0으로 설정하면 네트워크에서 해당 값이 0으로 올바르게 serialize되고 인덱스에 0으로 저장됩니다. 또한 왕복이 예상대로 작동합니다.

이 접근 방식에서 알고 있어야 하는 한 가지 잠재적인 문제가 있습니다. Null이 허용되지 않는 속성의 모델 형식을 사용하는 경우 인덱스의 문서가 해당 필드에 대해 Null 값을 포함하지 않음을 **보장**해야 합니다. SDK와 Azure 검색 REST API 모두 이를 적용하는 데 활용할 수 없습니다.

이것은 가상의 문제가 아닙니다. `Edm.Int32` 형식인 기존 인덱스에 새 필드를 추가하는 시나리오를 상상해 보세요. 인덱스 정의를 업데이트한 후 모든 문서는 해당하는 새 필드에 대해 Null 값을 포함하게 됩니다(Azure 검색에서 모든 형식은 Null을 허용하기 때문). 그런 다음 해당 필드에 대해 Null이 허용되지 않는 `int` 속성으로 모델 클래스를 사용하는 경우 문서를 검색하려고 시도할 때 다음과 같은 `JsonSerializationException`이 발생합니다.

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

이러한 이유로 모델 클래스에는 Null을 허용하는 형식을 사용하는 것이 가장 좋습니다.

이 버그 및 수정에 대한 자세한 내용은 [GitHub에서 해당 문제](https://github.com/Azure/azure-sdk-for-net/issues/1063)를 참조하세요.

## 결론
Azure 검색 .NET SDK를 사용하는 방법에 대한 자세한 정보가 필요한 경우 최근 업데이트된 [방법](search-howto-dotnet-sdk.md) 및 [시작](search-get-started-dotnet.md) 문서를 참조하세요.

SDK에 대한 귀하의 피드백을 환영합니다! 문제가 발생하면 [Azure 검색 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-KR/home?forum=azuresearch)을 통해 자유롭게 도움을 요청하세요. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 문제 제목에 "검색 SDK:"라는 접두사를 지정해야 합니다.

Azure 검색을 이용해 주셔서 감사합니다!

<!---HONumber=AcomDC_0128_2016-->