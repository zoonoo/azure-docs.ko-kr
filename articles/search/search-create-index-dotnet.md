---
title: .NET API를 사용하여 코드에서 인덱스 만들기 - Azure Search
description: Azure Search .NET SDK 및 C# 샘플 코드를 사용하여 전체 텍스트 검색 가능 인덱스를 만드는 방법을 알아봅니다.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: dbaac1478fdbf1b42fc6b597c3a5c541e007e413
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287148"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>빠른 시작: 1 - C#에서 Azure Search 인덱스 만들기

이 문서에서는 C# 및 [.NET SDK](https://aka.ms/search-sdk)를 사용하여 [Azure Search 인덱스](search-what-is-an-index.md)를 만드는 프로세스를 안내합니다. 인덱스를 만들고, 로드하고, 쿼리하는 3부 연습의 첫 번째 과정입니다. 인덱스를 만들려면 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 검색 서비스에 연결할 [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) 개체를 만듭니다.
> * `Indexes.Create`에서 매개 변수로 전달할 [`Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 개체를 만듭니다.
> * `SearchServiceClient`에서 `Indexes.Create` 메서드를 호출하여 `Index`를 서비스에 보냅니다.

## <a name="prerequisites"></a>필수 조건

[Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 모든 버전. 샘플 코드와 지침은 Community 평가판 버전에서 테스트되었습니다.

검색 서비스의 URL 엔드포인트 및 관리자 API 키입니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

  1. Azure Portal의 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

  2. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

  ![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-fiddler/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="1---open-the-project"></a>1 - 프로젝트 열기

GitHub에서 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 샘플 코드를 다운로드합니다. 

appsettings.json에서 기본 콘텐츠를 아래 예제로 바꾼 다음, 서비스에 대한 서비스 이름과 관리자 API 키를 제공합니다. 서비스 이름으로 이름 자체만 필요합니다. 예를 들어 URL이 https://mydemo.search.windows.net이면 `mydemo`를 JSON 파일에 추가합니다.


```json
{
    "SearchServiceName": "Put your search service name here",
    "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
}
```

이러한 값이 설정되면 F5를 사용하여 콘솔 앱을 실행할 솔루션을 빌드할 수 있습니다. 이 연습의 나머지 단계와 이어지는 단계는 이 코드의 작동 방식을 살펴보는 것입니다. 

또는 [.NET 애플리케이션에서 Azure Search를 사용하는 방법](search-howto-dotnet-sdk.md)에서 SDK 동작에 대한 자세한 내용을 참조할 수 있습니다. 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 - 클라이언트 만들기

Azure Search .NET SDK 사용을 시작하려면 `SearchServiceClient` 클래스의 인스턴스를 만듭니다. 이 클래스에는 몇 가지 생성자가 있습니다. 검색 서비스 이름과 `SearchCredentials` 개체를 매개 변수로 사용할 생성자입니다. `SearchCredentials` 는 API 키를 래핑합니다.

다음 코드는 Program.cs 파일에서 찾을 수 있습니다. 애플리케이션의 구성 파일(appsettings.json)에 저장된 검색 서비스 이름과 API 키에 대한 값을 사용하여 `SearchServiceClient`를 새로 만듭니다.

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient`에는 `Indexes` 속성이 있습니다. 이 속성은 Azure Search 인덱스를 생성, 나열, 업데이트 또는 삭제하는 데 필요한 모든 메서드를 제공합니다.

> [!NOTE]
> `SearchServiceClient` 클래스는 검색 서비스에 대한 연결을 관리합니다. 너무 많은 연결이 열리는 것을 방지하기 위해 되도록 애플리케이션에서 단일 `SearchServiceClient` 인스턴스를 공유합니다. 해당 메서드는 스레드로부터 안전하므로 이러한 공유를 사용할 수 있습니다.
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 - 인덱스 생성
`Indexes.Create` 메서드를 한 번 호출하면 인덱스가 만들어집니다. 이 메서드는 Azure Search 인덱스를 정의하는 `Index` 개체를 매개 변수로 사용합니다. 다음과 같이 `Index` 개체를 만들고 초기화합니다.

1. `Index` 개체의 `Name` 속성을 인덱스의 이름으로 설정합니다.

2. `Index` 개체의 `Fields` 속성을 `Field` 개체의 배열로 설정합니다. `Field` 개체를 만드는 가장 쉬운 방법은 `FieldBuilder.BuildForType` 메서드를 호출하여 형식 매개 변수에 대한 모델 클래스를 전달합니다. 모델 클래스에는 인덱스 필드에 매핑되는 속성이 있습니다. 이를 통해 모델 클래스의 인스턴스에 대한 검색 인덱스에서 문서를 바인딩할 수 있습니다.

> [!NOTE]
> 모델 클래스를 사용할 계획이 아니라면 `Field` 개체를 직접.만들어 여전히 인덱스를 정의할 수 있습니다. 필드 이름을 데이터 형식(또는 문자열 필드의 분석기)과 함께 생성자에 제공할 수 있습니다. 또한 `IsSearchable`, `IsFilterable`과 같은 다른 속성은 몇 가지 이름으로 설정할 수도 있습니다.
>
>

인덱스를 설계할 때 검색 사용자 환경과 비즈니스 요구 사항을 고려해야 합니다. 각 필드에는 적용할 검색 기능(필터링, 패싯, 정렬 등)과 적용되는 필드를 제어하는 [특성](https://docs.microsoft.com/rest/api/searchservice/Create-Index)이 할당되어야 합니다. 명시적으로 설정하지 않은 속성의 경우 사용자가 특별히 사용하도록 설정하지 않는 한 `Field` 클래스가 기본적으로 해당 검색 기능을 사용 안 함으로 설정합니다.

다음 예제에서는 인덱스 이름이 "hotels"이며, 필드는 모델 클래스를 사용하여 정의됩니다. 모델 클래스의 각 속성에는 해당 인덱스 필드의 검색 관련 동작을 결정하는 특성이 있습니다. 모델 클래스는 다음과 같이 정의됩니다.

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

애플리케이션에서 사용되는 방법에 따라 각 속성에 대한 특성을 신중하게 선택했습니다. 예를 들어 호텔을 검색 중인 사용자에게는 `description` 필드에서의 키워드 일치 항목이 유용할 수 있으므로 `IsSearchable` 특성을 `Description` 속성에 추가하여 해당 필드에 대한 전체 텍스트 검색을 사용하도록 설정합니다.

`Key` 특성을 추가하여 형식 `string`의 인덱스에 정확히 하나의 필드가 *키* 필드로 지정되어야 합니다(위 예제의 `HotelId` 참조).

위의 인덱스 정의는 프랑스어 텍스트를 저장하기 위해서 `description_fr` 필드에 언어 분석기를 사용합니다. 자세한 내용은 [Azure Search 인덱스에 언어 분석기 추가](index-add-language-analyzers.md)를 참조하세요.

> [!NOTE]
> 기본적으로 모델 클래스의 각 속성 이름은 인덱스의 필드 이름에 해당합니다. 모든 속성 이름을 카멜식 대/소문자 필드 이름에 매핑하려는 경우 `SerializePropertyNamesAsCamelCase` 특성을 지닌 클래스를 표시합니다. 다른 이름에 매핑하려는 경우 `JsonProperty` 특성을 위의 `DescriptionFr` 속성처럼 사용할 수 있습니다. `JsonProperty` 특성은 `SerializePropertyNamesAsCamelCase` 특성보다 우선합니다.
> 
> 

모델 클래스를 정의했으므로 인덱스 정의를 아주 쉽게 만들 수 있습니다.

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 - Indexes.Create 호출
이제 초기화된 `Index` 개체가 있으므로 `SearchServiceClient` 개체에서 `Indexes.Create`를 호출하여 인덱스를 만듭니다.

```csharp
serviceClient.Indexes.Create(definition);
```

요청이 성공하면 메서드가 정상적으로 반환됩니다. 잘못된 매개 변수 등 요청에 문제가 발생하면 메서드가 `CloudException`을 발생시킵니다.

인덱스 작업이 완료되고 해당 인덱스를 삭제하려면 `SearchServiceClient`에서 `Indexes.Delete` 메서드를 호출합니다. 예: 

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> 이 문서의 예제 코드는 간단히 하기 위해 Azure Search.NET SDK의 동기 메서드를 사용합니다. 확장성과 응답성이 유지하기 위해 사용 중인 애플리케이션에서 비동기 메서드를 사용하는 것이 좋습니다. 예를 들어 위의 예제에서 `Create` 및 `Delete` 대신 `CreateAsync` 및 `DeleteAsync`를 사용할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 필드 데이터 형식과 동작을 정의하는 스키마를 기반으로 하여 빈 Azure Search 인덱스를 만들었습니다. 이 시리즈의 다음 빠른 시작에서는 검색 가능한 콘텐츠를 사용하여 인덱스를 로드하는 방법에 대해 설명합니다.

> [!div class="nextstepaction"]
> [C#을 사용하여 Azure Search 인덱스에 데이터 로드](search-import-data-dotnet.md)