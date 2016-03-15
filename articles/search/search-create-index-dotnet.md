<properties
    pageTitle=".NET SDK를 사용하여 Azure 검색 인덱스 만들기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
    description="Azure 검색 .NET SDK를 사용하여 코드에 인덱스를 만듭니다."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="brjohnst"/>

# .NET SDK를 사용하여 Azure 검색 인덱스 만들기
> [AZURE.SELECTOR]
- [개요](search-what-is-an-index.md)
- [포털](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST (영문)](search-create-index-rest-api.md)


이 문서는 [Azure 검색 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)를 사용하여 Azure 검색 [인덱스](https://msdn.microsoft.com/library/azure/dn798941.aspx)를 만드는 프로세스를 안내합니다.

이 가이드를 수행하고 인덱스를 만들기 전에 이미 [Azure 검색 서비스를 만들어야](search-create-service-portal.md) 합니다.

이 문서의 모든 샘플 코드는 C#으로 작성되었습니다. 전체 소스 코드는 [GitHub](http://aka.ms/search-dotnet-howto)를 참조하세요.

## I. Azure 검색 서비스의 관리 API 키 식별
Azure 검색 서비스를 프로비전했다면 .NET SDK를 사용하여 서비스 끝점에 대한 요청을 실행할 준비가 거의 된 것입니다. 먼저 프로비전한 검색 서비스에 대해 생성된 관리 API 키 중 하나가 있어야 합니다. .NET SDK는 서비스에 대한 모든 요청에 대해 이 API 키를 전송합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 응용 프로그램과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

1. 서비스의 API 키를 찾으려면 [Azure 포털](https://portal.azure.com/)에 로그인해야 합니다.
2. Azure 검색 서비스의 블레이드로 이동합니다.
3. "키" 아이콘을 클릭합니다.

서비스에는 *관리 키* 및 *쿼리 키*가 있습니다.

  - 기본 및 보조 *관리 키*는 서비스를 관리하며 인덱스, 인덱서 및 데이터 원본을 만들고 삭제하는 기능을 비롯한 모든 작업에 전체 권한을 부여합니다. 두 개의 키가 있으므로 기본 키를 다시 생성하려는 경우 보조 키를 사용하여 계속할 수 있고 반대도 가능합니다.
  - *쿼리 키*는 인덱스 및 문서에 대한 읽기 전용 액세스를 부여하며 일반적으로 검색 요청을 실행하는 클라이언트 응용 프로그램에 배포됩니다.

인덱스를 만들기 위해 기본 또는 보조 관리 키를 사용할 수 있습니다.

<a name="CreateSearchServiceClient"></a>
## II. SearchServiceClient 클래스의 인스턴스 만들기
Azure 검색 .NET SDK 사용을 시작하려면 `SearchServiceClient` 클래스의 인스턴스를 만들어야 합니다. 이 클래스에는 몇 가지 생성자가 있습니다. 검색 서비스 이름과 `SearchCredentials` 개체를 매개 변수로 사용할 생성자입니다. `SearchCredentials`는 API 키를 래핑합니다.

아래 코드에서는 응용 프로그램의 구성 파일(`app.config` 또는 `web.config`)에 저장된 API 키와 검색 서비스 이름을 사용하여 새 `SearchServiceClient`를 만듭니다.

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient`에는 `Indexes` 속성이 있습니다. 이 속성은 Azure 검색 인덱스를 생성, 나열, 업데이트 또는 삭제하는 데 필요한 모든 메서드를 제공합니다.

> [AZURE.NOTE] `SearchServiceClient` 클래스는 검색 서비스에 대한 연결을 관리합니다. 너무 많은 연결이 열리는 것을 방지하기 위해 되도록 응용 프로그램에서 단일 `SearchServiceClient` 인스턴스를 공유합니다. 해당 메서드는 스레드로부터 안전하므로 이러한 공유를 사용할 수 있습니다.

<a name="DefineIndex"></a>
## III. `Index` 클래스를 사용하여 Azure 검색 인덱스 정의
`Indexes.Create` 메서드에 대한 단일 호출이 인덱스를 만듭니다. 이 메서드는 Azure 검색 인덱스를 정의하는 `Index` 개체를 매개 변수로 사용합니다. 다음과 같이 `Index` 개체를 만들고 초기화해야 합니다.

1. `Index` 개체의 `Name` 속성을 인덱스의 이름으로 설정합니다.
2. `Index` 개체의 `Fields` 속성을 `Field` 개체의 배열로 설정합니다. 각각의 `Field` 개체는 인덱스 필드의 동작을 정의합니다. 필드 이름을 데이터 형식(또는 문자열 필드의 분석기)과 함께 생성자에 제공할 수 있습니다. `IsSearchable`, `IsFilterable` 등의 기타 속성을 설정할 수도 있습니다.

인덱스를 각 `Field`로 디자인하는 경우 검색 사용자 환경 및 비즈니스 요구를 [적합한 속성](https://msdn.microsoft.com/library/azure/dn798941.aspx)으로 할당해야 한다는 점을 염두하는 것이 중요합니다. 이러한 속성은 어떤 검색 기능(전체 텍스트 검색의 필터링, 패싯, 정렬 등)이 어떤 필드에 적용될지를 제어합니다. 명시적으로 설정하지 않은 속성의 경우 사용자가 특별히 사용하도록 설정하지 않는 한 `Field` 클래스가 기본적으로 해당 검색 기능을 사용 안 함으로 설정합니다.

예를 들어 인덱스 "호텔"의 이름을 지정하고 필드를 다음과 같이 정의했습니다.

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[] 
    { 
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

응용 프로그램에서 사용되는 방법에 따라 각 `Field`에 대한 속성 값을 신중하게 선택했습니다. 예를 들어 호텔을 검색 중인 사용자에게는 `description` 필드에서 키워드 일치 항목이 유용할 수 있으므로 `IsSearchable`을 `true`로 설정하여 해당 필드에 대한 전체 텍스트 검색을 사용하도록 설정합니다.

`IsKey`를 `true`로 설정하여 형식 `DataType.String`의 인덱스에 정확히 하나의 필드가 키 필드로 지정되어야 합니다(위 예제의 `hotelId` 참조).

위의 인덱스 정의는 프랑스어 텍스트를 저장하기 위해서 `description_fr` 필드에 사용자 지정 언어 분석기를 사용합니다. 언어 분석기에 대한 자세한 내용은 [MSDN의 언어 지원 항목](https://msdn.microsoft.com/library/azure/dn879793.aspx)뿐만 아니라 해당하는 [블로그 게시물](https://azure.microsoft.com/blog/language-support-in-azure-search/)을 참조하세요.

> [AZURE.NOTE]  생성자에 `AnalyzerName.FrLucene`를 전달하면 `Field`가 자동으로 `DataType.String` 형식이 되고 `IsSearchable`이 `true`로 설정됩니다.

## IV. 인덱스 만들기
`Index` 개체를 초기화한 후에는 `SearchServiceClient` 개체에서 `Indexes.Create`를 호출하여 간단히 인덱스를 만들 수 있습니다.

```csharp
serviceClient.Indexes.Create(definition);
```

요청이 성공하면 메서드가 정상적으로 반환됩니다. 잘못된 매개 변수 등 요청에 문제가 발생하면 메서드가 `CloudException`을 발생시킵니다.

인덱스 관련 작업을 완료하고 삭제하려는 경우 `SearchServiceClient`에서 `Indexes.Delete` 메서드를 호출합니다. 예를 들어 "호텔" 인덱스를 삭제하는 방법입니다.

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] 이 문서의 예제 코드는 간단히 하기 위해 Azure 검색.NET SDK의 동기 메서드를 사용합니다. 확장성과 응답성이 유지하기 위해 사용 중인 응용 프로그램에서 비동기 메서드를 사용하는 것이 좋습니다. 예를 들어 위의 예제에서 `Create` 및 `Delete` 대신 `CreateAsync` 및 `DeleteAsync`를 사용할 수 있습니다.

## 다음
Azure 검색 인덱스를 만든 후에 데이터를 검색하기 시작할 수 있도록 콘텐츠를 인덱스에 업로드할 준비가 되었습니다. 자세한 내용은 [.NET SDK를 사용하여 Azure 검색으로 데이터 가져오기](search-import-data-dotnet.md)를 참조하세요.

<!---HONumber=AcomDC_0309_2016-->