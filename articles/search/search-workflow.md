<properties
	pageTitle="Azure 검색 개발을 위한 일반적인 워크플로 | Microsoft Azure | 호스트 된 클라우드 검색 서비스"
	description="호스트된 클라우드 검색 서비스인 Azure 검색과 통합된 프로덕션 응용 프로그램이나 프로토타입을 구축하기 위한 워크플로 또는 로드맵입니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Azure 검색 개발을 위한 일반적인 워크플로

이 문서는 사용자 지정 응용 프로그램에서 검색 환경을 제공하는 구성 요소로 호스트된 클라우드 검색 서비스인 Azure 검색을 포함시키기 위한 로드맵입니다. 테스트 중인지 또는 곧바로 시작할 준비가 되었는지에 따라 Azure 검색을 사용자 지정 개발 프로젝트에 통합하는 방법에 대한 예비 지침이 필요하게 됩니다.

다음 섹션에서는 Azure 검색이 응용 프로그램의 검색 요구 사항을 얼마나 잘 충족시키는지 평가하는 데 도움이 되는 초기 프로토타입에 대한 일반적인 워크플로를 자세히 살펴봅니다. 이 문서는 두 부분으로 구성되어 있으며 보다 중대한 응용 프로그램 개발 작업에 영향을 주는 중요한 설계 결정에 대해 다룹니다.

프로토타입 작성을 시작하기 전에 시작 자습서 또는 이 [1시간짜리 심층 분석 프레젠테이션 비디오](http://azure.microsoft.com/documentation/videos/tech-ed-europe-2014-azure-search-deep-dive/) 중 하나를 살펴보는 것이 좋습니다. 시작 자습서는 [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md), [Node.JS](search-get-started-nodejs.md) 언어로 제공됩니다.

## 프로토타입 개발

성공적으로 프로토타입을 개발하는 가장 빠른 경로에는 일반적으로 이 섹션의 단계가 포함됩니다. 여기에는 서비스를 프로비전하는 단계, 인덱스의 스키마를 정의하는 단계, 문서를 사용하여 인덱스를 로드하는 단계 및 인덱스를 쿼리하는 단계가 포함됩니다.

휘발성 데이터를 사용하는 응용 프로그램의 경우(예: 일반적으로 인벤토리 또는 콘텐츠에 대한 신속한 변경이 포함되는 경우) 프로토타입에는 문서 업데이트를 위한 구성 요소도 포함해야 합니다.

   ![][1]

### 1단계: 서비스 프로비전

Azure 검색은 완벽하게 관리되는 온라인 서비스로, Azure 구독을 통해 사용할 수 있습니다. [Azure에 등록한 후](http://azure.microsoft.com/pricing/free-trial/)에는 검색 서비스를 빠르게 추가할 수 있습니다. 검색 서비스를 구독에 추가하는 방법에 대한 지침은 [포털에서 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

두 가지 가격 책정 계층 중에서 선택할 수 있습니다. 소규모 데이터 하위 집합으로 작업해야 하는 caveat를 사용하여 프로토타입을 작성하려면 공유(무료) 서비스를 사용하는 것이 좋습니다. 공유 서비스는 기존 구독자에게 무료(평가판 또는 정식 멤버 자격을 통해)로 빠르게 설치되지만 사용할 수 있는 인덱스 및 문서 수가 3개의 인덱스, 인덱스당 최대 10,000개의 문서 또는 저장소당 총 50MB 중 먼저 도달하는 것으로 제한됩니다.

### 2단계: 인덱스 만들기

서비스를 만든 후에는 스키마 정의부터 시작하여 인덱스를 만들 준비가 된 것입니다.

인덱스를 만드는 가장 쉽고 빠른 방법은 Azure 포털을 통해 만드는 것입니다. 최소한 각 문서에는 고유 키 및 검색 가능한 데이터가 포함된 최소 하나 이상의 필드가 있어야 합니다. 시작하려면 [포털에서 인덱스 만들기](search-create-index-portal.md)를 참조하세요.

> [AZURE.NOTE]Azure 검색 인덱스 살펴보기
>
> 인덱스는 모든 후속 검색 작업에서 검색 모음으로 사용되는 체계적이고 지속적인 데이터입니다. 검색 모음은 클라우드에 검색 서비스 구독의 일부분으로 저장되며, 이를 통해 검색 작업을 신속하고 일관성 있게 실행할 수 있습니다. 검색 용어에서는 검색 모음에 있는 하나의 항목을 문서라고 부르며 모든 문서의 총계는 문서 컬렉션입니다.
>
>인덱스 스키마는 문서 내의 모든 필드를 이름, 데이터 형식 및 해당 필드의 검색 가능 여부, 필터링 가능 여부, 패싯 가능 여부 등을 지정하는 속성별로 정의합니다.
>
> 인덱스 스키마는 문서 구조 외에도 검색 점수를 향상시키기 위한 조건을 제공하는 점수 매기기 프로필, 자동 완성 쿼리(확인기) 및 도메인 간 요청에 대한 CORS를 지원하는 구성 설정을 지정합니다. 프로토타입의 경우 문서의 필드를 지정해서 간단하게 시작한 다음, 증분 방식으로 다른 기능을 추가하는 것이 좋습니다(나중에 추가할 기능 목록은 5단계 참조).
>
> 실제 적용 예는 전자 상거래 응용 프로그램을 생각해 보세요. 검색 인덱스에는 응용 프로그램에서 검색 가능한 모든 제품 또는 서비스(검색 결과에 반환되는 모든 것)가 포함됩니다. 각 SKU에 대해 하나의 문서가 있습니다. 각 문서에는 제품 이름, 브랜드, 크기, 가격, 색상 및 이미지 또는 검색 결과 내에서 반환되기를 원하는 기타 리소스 파일에 대한 참조도 포함됩니다.

### 3단계: 문서 로드

Azure 검색에서 인덱스를 저장한 후 다음 단계는 문서로 인덱스를 채우는 것입니다. 이 단계에서는 데이터가 업로드, 분석, 토큰화되어 검색 작업을 위해 설계된 데이터 구조(예: 반전된 인덱스)에 저장됩니다.

인덱스에 업로드하는 데이터는 이전 단계에서 정의한 스키마를 준수해야 합니다. 문서 데이터는 JSON 형식으로 각 필드에 대한 키/값 쌍의 집합으로 표시됩니다. 스키마가 ID(키) 필드, 이름 필드, 번호 필드 및 URL 필드(외부 이미지가 검색 결과의 일부인 경우 해당될 수 있음)를 지정하는 경우 인덱스에 공급되는 모든 문서에는 각 필드에 대한 값(또는 null)이 있어야 합니다.

문서를 로드하는 여러 가지 방법이 있지만 현재는 모두 API가 필요합니다. 대부분의 프로토타입에서 이 단계는 코딩이 요구되므로 가장 많은 시간이 소요될 수 있습니다. 옵션은 이 문서의 뒷부분에서 설명합니다.

> [AZURE.NOTE]공유 서비스는 인덱스당 10,000개의 문서로 제한됩니다. 한도 아래를 유지하도록 데이터 집합을 줄이도록 합니다. 자세한 내용은 [한도 및 제약 조건](search-limits-quotas-capacity.md)을 참조하세요.

#### 인덱스에 데이터를 로드하는 방법

한 가지 방법은 인덱서를 사용하는 것입니다. Azure DocumentDB 또는 Azure의 SQL Server 관계형 데이터 원본(특히, Azure SQL 데이터베이스 또는 Azure VM의 SQL Server)의 경우는 [인덱서](https://msdn.microsoft.com/library/dn946891.aspx)를 사용하여 지원되는 데이터 원본에서 문서를 검색할 수 있습니다. 인덱서를 사용하여 문서를 로드하는 코드 샘플은 [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md), [Node.JS](search-get-started-nodejs.md)의 시작하기 자습서에서 찾을 수 있습니다.

두 번째 옵션은 REST API 또는 .NET 라이브러리 중 하나를 사용하여 문서를 로드하는 간단한 프로그램을 작성하는 것입니다.

- [문서 추가, 업데이트 또는 삭제(REST API)](https://msdn.microsoft.com/library/dn798930.aspx)
- [DocumentOperationsExtensions 클래스](https://msdn.microsoft.com/library/microsoft.azure.search.documentoperationsextensions.aspx)

매우 작은 데이터 집합에서 작업하기 위한 세 번째 옵션은 [Fiddler](search-fiddler.md) 또는 [Chrome Postman](search-chrome-postman.md)을 사용하여 문서를 업로드하는 것입니다.

가장 쉬운 네 번째 옵션은 솔루션에 포함된 데이터베이스(.mdf)에서 문서를 로드하는 [Adventure Works C# REST API 예제](https://azuresearchadventureworksdemo.codeplex.com/) 또는 솔루션에 포함된 JSON 데이터 파일에서 데이터를 로드하는 [점수 매기기 프로필 C# REST API 예제](https://azuresearchscoringprofiles.codeplex.com/) 중 하나에서 코드를 가져오는 것입니다.

> [AZURE.TIP]데이터 JSON 파일 및 schema.json 파일을 응용 프로그램에 유효한 데이터로 바꾸는 [점수 매기기 프로필 샘플](https://azuresearchscoringprofiles.codeplex.com/)을 수정하여 실행할 수 있습니다.

### 4단계: 문서 쿼리

문서가 인덱스에 로드되면 첫 번째 쿼리를 작성할 수 있습니다.

검색 서비스에서 초기 검색 결과를 가져오는 가장 빠른 방법은 [Fiddler](search-fiddler.md) 또는 [Chrome Postman](search-chrome-postman.md)을 사용하여 응답을 보는 것입니다. 하지만 현실적으로, 읽을 수 있는 형식으로 결과를 보기 위해 간단한 UI 코드를 작성 하려고 할 수 있습니다.

검색 작업용 API에는 다음이 포함됩니다.

- [문서 검색 작업](https://msdn.microsoft.com/library/dn798927.aspx)
- [SearchIndexClient 클래스](https://msdn.microsoft.com/library/microsoft.azure.search.searchindexclient.aspx)

Azure 검색에서 쿼리는 매우 간단할 수 있습니다. URI에 `search=*`를 포함시키면 검색 모음에서 처음 50개 항목을 반환합니다. `search=<some phrase>`를 지정하면 문구에 대한 전체 텍스트 검색을 수행하여 입력 용어와 일치하는 항목이 포함된 최소한 50개 이상의 문서가 있다고 가정하고 최대 50개의 문서를 반환합니다.

기본 문서 수는 50개입니다. `$Count` 쿼리 매개 변수를 사용하여 반환되는 항목 수를 변경할 수 있습니다. 이 매개 변수는 [문서 검색](https://msdn.microsoft.com/library/dn798927.aspx)에서 설명합니다.

> [AZURE.TIP]가장 포괄적인 쿼리 예제 목록이 [문서 검색](https://msdn.microsoft.com/library/dn798927.aspx)에 나와 있지만 [구문 참조](https://msdn.microsoft.com/library/dn798920.aspx)에서 지원되는 연산자 목록을 검토할 수도 있습니다.

### 5단계: 추가 기능 탐색

이제 서비스와 인덱스가 있으므로 검색 환경을 더욱 개선시킬 수 있는 기능을 시험해볼 수 있습니다. 검토해볼 만한 간단한 기능 목록이 다음에 나와 있습니다.

**검색 페이지**에는 종종 결과 집합의 문서 개수가 포함되며 페이지 매김 기능을 사용하여 보다 관리가 용이한 숫자로 결과를 세분화할 수 있습니다. 자세한 내용은 [페이지 매김](search-pagination-page-layout.md)을 참조하세요.

**searchMode=all**은 Azure 검색에서 NOT 연산자를 평가하는 방법을 변경하는 쿼리 매개 변수입니다. 기본적으로 NOT(-)이 포함된 쿼리는 결과를 좁히기 보다는 확장합니다. 이 매개 변수를 설정하여 연산자 평가 방법을 변경할 수 있습니다. 이 내용은 [문서 검색](https://msdn.microsoft.com/library/dn798927.aspx) 또는 [SearchMode 열거](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchmode.aspx)에 설명되어 있습니다.

**점수 매기기 프로필**은 미리 정의된 조건을 충족하는 항목이 검색 결과에서 더 높은 수준에 표시되도록 검색 점수를 향상시키는 데 사용됩니다. 이 기능을 단계별로 보려면 [점수 매기기 프로필 시작](search-get-started-scoring-profiles.md)을 참조하세요.

**필터**는 선택에 대한 추가적인 조건을 제공하여 검색 결과를 좁히는 데 사용됩니다. 필터 식은 쿼리 내에 배치됩니다. 자세한 내용은 [문서 검색](https://msdn.microsoft.com/library/dn798927.aspx)을 참조하세요.

**패싯 탐색**은 자기 주도형 필터링에 사용됩니다. Azure 검색은 구조를 빌드하여 반환하며 검색 결과 페이지에서 패싯 탐색 구조를 렌더링하는 것은 코드입니다. 자세한 내용은 [패싯 탐색](search-faceted-navigation.md)을 참조하세요.

**확인기**는 제안된 검색어를 검색 구문에서 첫 번째 문자의 사용자 입력으로 반환하는 자동 완성 쿼리 또는 사전 입력 제안을 참조합니다. 자세한 내용은 [제안 작업](https://msdn.microsoft.com/library/dn798936.aspx) 또는 [확인기 클래스](https://msdn.microsoft.com/library/microsoft.azure.search.models.suggester.aspx)를 참조하세요.

**언어 분석기**는 텍스트 분석 중에 사용되는 언어 규칙을 제공합니다. Azure 검색용 기본 언어 분석기는 Lucene 영어이지만 다른 분석기 또는 여러 분석기를 인덱스에 지정하여 사용할 수도 있습니다. Lucene 분석기는 모든 API에서 사용할 수 있습니다. Microsoft 자연어 프로세서는 [2015-02-28-미리 보기 REST API](search-api-2015-02-28-preview.md)에서만 사용할 수 있습니다. 자세한 내용은 [언어 지원](https://msdn.microsoft.com/library/dn879793.aspx)을 참조하세요.

### 6단계: 인덱스 및 문서 업데이트

평가하려는 기능 중 일부는 인덱스에 대한 업데이트가 필요할 수 있으며 이는 종종 문서에 대한 업데이트가 필요한 후속 효과가 발생합니다.

인덱스 또는 문서를 업데이트해야 하는 경우, 예를 들어 확인기를 추가하거나 해당 목적으로 추가한 필드에 대해 언어 분석기를 지정해야 하는 경우, 자세한 지침은 다음 링크를 참조하세요.

- [인덱스 업데이트 작업(REST API)](https://msdn.microsoft.com/library/dn800964.aspx)
- [인덱서 업데이트 작업(REST API)](https://msdn.microsoft.com/library/dn946892.aspx)
- [문서 추가, 업데이트 또는 삭제 작업(REST API)](https://msdn.microsoft.com/library/dn798930.aspx)
- [인덱스 클래스(.NET 라이브러리)](https://msdn.microsoft.com/library/microsoft.azure.search.models.index.aspx)
- [문서 클래스(.NET 라이브러리)](https://msdn.microsoft.com/library/microsoft.azure.search.models.document.aspx)

개념 증명을 설정하는 프로토타입을 빌드했으면 프로덕션 작업을 지원할 수 있는 개발 프로젝트를 설계하여 배운 내용을 다음 수준으로 확장할 수 있습니다.

## 응용 프로그램 개발

이제 다음 단계로 이동하기 위해서는 사용할 API, 문서 및 업로드 빈도 관리 방법 및 검색 결과에 외부 리소스를 포함할지 여부를 결정해야 합니다.

솔루션 설계에는 프로토타입에 대해 설명한 모든 단계가 포함되어야 하지만 가장 편리한 경로에 우선 순위를 두는 대신 전체 솔루션과 가장 잘 호환되는 방식을 고려해야 합니다.

### API 선택

Azure 검색에서는 두 가지 프로그래밍 모델을 제공하는데, 하나는 관리 코드를 위한 .NET 라이브러리이고 다른 하나는 Java, JavaScript 같은 프로그래밍 언어 또는 Microsoft .NET Framework를 대상으로 하지 않는 기타 언어를 위한 REST API입니다.

현재 기능의 작은 하위 집합이 아직 .NET 라이브러리에 없으므로 관리 코드를 작성하려면 REST API를 사용하여 원하는 기능을 가져와야 할 수 있습니다. REST API에서만 사용할 수 있는 기능은 다음과 같습니다.

- [Microsoft 자연어 프로세서 - 미리 보기 전용](../search-api-2015-02-28-preview/)
- [moreLikeThis 기능 - 미리 보기 전용](../search-api-2015-02-28-preview/)
- [관리 API](https://msdn.microsoft.com/library/dn832684.aspx)

변경된 기능 상태를 모니터링하려면 [새로운 기능](search-latest-updates.md) 문서를 정기적으로 확인합니다.

### 데이터 동기화 방법 결정: 푸시 또는 풀(Pull)

푸시 및 풀(Pull) 모델은 인덱스에서 문서를 업데이트하는 방법을 참조합니다. 종종 시나리오를 통해 가장 적합한 모델을 쉽게 파악할 수 있습니다.

온라인 소매업을 하는 경우는 재고 변경 내용을 OLTP 데이터베이스와 Azure 검색 인덱스에 푸시하거나 양쪽에 쓸 수 있으므로 푸시 모델이 가장 필요할 수 있습니다. 특정 SKU가 품절되거나 특정 크기 또는 색상을 구입할 수 없게 되면 고객 불만을 방지할 수 있도록 가능한 한 신속하게 인덱스를 업데이트해야 합니다. 푸시 모델만이 거의 실시간으로 검색 인덱스 업데이트를 제공할 수 있습니다.

Azure 검색에는 푸시 모델을 구현하기 위한 특정 메커니즘이 없습니다. 컬렉션에 있는 문서를 업데이트하려면 데이터 계층의 응용 프로그램 코드에서 [REST API](https://msdn.microsoft.com/library/dn798935.aspx) 또는 [.NET 라이브러리](https://msdn.microsoft.com/library/dn951165.aspx) 중 하나를 사용하여 문서 업데이트 작업을 처리해야 합니다. 구현 세부 정보로, 문서 키에 대한 제품 SKU를 사용하여 이 작업을 지원할 수 있습니다.

풀(Pull) 모델은 일반적으로 외부 데이터 원본에서 데이터를 가져오는 예약 작업입니다. Azure 검색에서 풀(Pull) 모델은 [인덱서](https://msdn.microsoft.com/library/azure/dn946891.aspx)를 통해 사용할 수 있으며, Azure DocumentDB 또는 Azure SQL 데이터베이스(및 Azure VM의 SQL Server) 등의 특정 데이터 원본에 대해서도 사용할 수 있습니다.

### 문서 일괄 로드

처리량을 향상시키기 위해 문서를 일괄 처리로 추가하는 것이 좋습니다. 평균 문서 크기를 약 1-2KB로 가정할 경우 최대 1,000개까지 문서를 일괄 처리할 수 있습니다.

POST 요청에 대한 전체 상태 코드가 있습니다. 상태 코드는 HTTP 200(성공) 또는 성공한 문서와 실패한 문서가 함께 있을 경우 HTTP 207(다중 상태)입니다. POST 요청에 대한 상태 코드 외에도 Azure 검색은 각 문서의 상태 필드를 유지 관리합니다. 일괄 업로드를 감안할 경우 각 문서에 대한 삽입이 성공 또는 실패했는지를 나타내는 문서별 상태를 가져올 방법이 필요합니다. 상태 필드에서 해당 정보를 제공합니다. 문서를 로드하지 못한 경우 false로 설정됩니다.

부하가 크면 일부 업로드 실패가 발생하는 것이 일반적입니다. 이 경우 전체 상태 코드는 부분 성공을 나타내는 207이며, 인덱싱에 실패한 문서의 'status' 속성이 false로 설정됩니다.

> [AZURE.NOTE]서비스가 문서를 받으면 인덱싱을 위해 대기되며, 검색 결과에 즉시 포함되지 않을 수도 있습니다. 부하가 크지 않은 경우 일반적으로 몇 초 내에 문서가 인덱싱됩니다.

인덱스를 업데이트할 때 여러 작업(삽입, 병합, 삭제)을 동일한 일괄 처리로 결합하여 여러 왕복 요구를 제거할 수 있습니다. 현재 Azure 검색은 부분 업데이트(HTTP PATCH)를 지원하지 않으므로 인덱스를 업데이트해야 하는 경우 인덱스 정의를 다시 전송해야 합니다.

### 검색 결과에 외부 데이터 통합

Azure 검색은 검색 작업에 사용되는 인덱스 및 문서에 대해 내부 저장소를 사용합니다. 텍스트 분석 및 인덱스 구문 분석은 모든 검색 가능 필드 및 관련 특성을 사용할 수 있는지에 따라 달라집니다.

하지만 문서의 모든 필드가 검색 가능한 것은 아닙니다. 예를 들어 응용 프로그램이 음악 또는 동영상의 온라인 카탈로그인 경우 Azure Blob 서비스 또는 다른 저장소 형식에 이진 파일을 저장하는 것이 좋습니다. 이진 파일 자체는 검색 가능하지 않으므로 Azure 검색 저장소에 저장할 필요가 없습니다. 이미지, 비디오 및 오디오 파일을 다른 서비스 또는 위치에 저장해야 하더라도 파일 위치에 대한 URL을 참조하는 필드를 포함해야 합니다. 이러한 방식으로 검색 결과의 일부로 외부 데이터를 반환할 수 있습니다.

외부 데이터를 사용하려면 외부 데이터 파일에 대한 URL 포인터를 저장하는 필드를 인덱스에 정의해야 합니다. [문서 조회](https://msdn.microsoft.com/library/dn798929.aspx) 요청을 발급하거나 검색 결과에 필드를 포함하는 경우 문서 컨텍스트에 이진 파일이 표시됩니다.

### 용량 계획

Azure 검색에서 주목할 만한 기능 중 하나는 요구에 따라 쉽게 리소스의 규모를 확장하거나 축소할 수 있는 기능입니다. 이 기능으로 용량 계획에 대한 필요성이 없어지지는 않지만 대부분의 위험이 최소화됩니다. 검색 작업을 실행하기 위한 추가 하드웨어나 잘못된 하드웨어가 필요하지 않습니다.

마지막 단계로, 복제본과 파티션 모두에 대해 기존 리소스 수준을 검토하고 조정이 필요한지 여부를 결정합니다. 용량을 가장 쉽게 조정하는 방법은 [Azure 포털](https://ms.portal.azure.com/)입니다.

표준 가격 책정 계층만 확장 또는 축소할 수 있습니다. 또한 조정 정도에 따라 서비스를 위한 추가 클러스터를 배포하는 데 몇 분에서 몇 시간까지 걸릴 수 있습니다.

> [AZURE.NOTE]관리 REST API를 사용하여 프로그래밍 방식으로 용량을 조정할 수 있습니다. 자세한 내용은 [관리 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)를 참조하세요.


<!--Image references-->
[1]: ./media/search-workflow/AzSearch-Workflow.png

<!----HONumber=Nov15_HO3-->