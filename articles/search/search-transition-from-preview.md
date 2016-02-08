<properties 
	pageTitle="미리 보기 api-version=2014*에서 api-version=2015*로 전환 | Microsoft Azure | 호스트된 클라우드 검색 서비스" 
	description="Azure 검색, api-버전=2015-02-28에 대한 2014-07-31-미리 보기 또는 2014-10-20-미리 보기에 대해 작성된 주요 변경 내용 및 코드 마이그레이션 방법을 알아보세요." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="11/04/2015" 
	ms.author="heidist"/>

#미리보기 api-버전=2014*에서 api-버전=2015*로 전환#

Azure 검색은 Microsoft Azure에서 호스팅되는 클라우드 검색 서비스입니다. 다음 지침은 Azure 검색의 미리 보기 버전에서 사용자 지정 응용 프로그램을 빌드하고 이제 일반적으로 사용 가능한 릴리스 2015-02-28로 마이그레이션하는 고객을 위한 것입니다.

미리 보기 고객은 다음의 이전 미리 보기 버전 중 하나를 사용할 수 있을 것입니다:

- 2014-07-31-미리 보기
- 2014-10-20-미리 보기

이제 Azure 검색은 일반적으로 사용 가능하며 최신 릴리스로 전환하는 것이 좋습니다: 2015-02-28은 Azure 검색을 일반적으로 사용 가능한 릴리스의 공식 API 버전입니다. 해당 버전은 [MSDN](https://msdn.microsoft.com/library/azure/dn798933.aspx)에서 확인할 수 있습니다.

본사는 아직 개발 중인 기능을 도입하여 다음 미리 보기 버전인 [2015-02-28-Preview](search-api-2015-02-28-preview.md)을 출시합니다. [Azure 검색 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch) 또는 당사 [사용자 의견 페이지](https://feedback.azure.com/forums/263029-azure-search /)를 통해 미리 보기 API에 관한 사용자 의견을 제공할 수 있습니다.

###마이그레이션의 검사 목록###

- 솔루션이 영향을 받는지 여부를 확인하기 위한 주요 변경 내용을 검토합니다.
- 잠긴 버전에 대해 API 버전을 `2015-02-28`으로 바꿔 주세요. 해당 버전은 SLA에 따릅니다. 문제와 충돌하는 경우 더 신속하게 해결할 수 있습니다.
- 빌드, 배포, 테스트. 주요 변경 내용을 제외하고 검색 동작을 기준으로 100% 패리티가 있어야 합니다.
- 프로덕션을 롤아웃하세요.
- 이후 기능 도입을 위해 새로운 기능을 평가합니다. 드라이브 Microsoft 자연어 프로세서 또는 `morelikethis`를 테스트 하려는 경우 다시 2015-02-28-미리 보기로 바꿔 주세요.

##api-버전=2015*의 주요 변경 내용##

API의 초기 릴리스는 자동 완성 또는 자동 완성 제안 기능을 포함합니다. 접두사 일치는 필드의 다른 위치에서 일치 여부를 지원하지 않고 검색어의 첫번째 문자를 검색하여 유용하지만 제한적입니다. 접두사 일치를 특정 필드에서 사용하고자 하는 경우 구현은 사용자가 `true`를 설정하는 `suggestions`이라는 부울 속성이었습니다.

접사 및 유사 항목 일치를 제공하는 [인덱스](https://msdn.microsoft.com/library/azure/dn798941.aspx) 기능에서 정의된 새로운 `Suggesters` 구성을 기준으로 이런 원래의 구현은 이제 사용되지 않습니다. 이름에서 알 수 있듯이 접사 및 유사 항목 일치는 훨씬 더 넓은 범위의 일치 기능을 제공합니다. 접사 일치는 접두사를 포함하며 이 경우에 여전히 시작 문자만 일치하지만, 문자열의 나머지 부분도 포함하는 일치로 확장됩니다.

새로운 솔루션을 빌드하는 고객이 부주의하게 채택하는 경우를 막기 위해 본사는 이전 구현(부울 속성)을 중단하기로 결정했으며 이것은 하위 호환성이 없는 2015 버전을 전체적으로 사용할 수 없다는 의미입니다. `2015-02-28` 또는 `2015-02-28-Preview` 중 하나를 사용하는 경우 사용자는 자동 완성 쿼리를 사용하기 위해 새로운 `Suggesters` 구성을 사용해야 할 것입니다.

##기존 코드를 이식합니다.##

제안 속성은 유일한 주요 변경 내용입니다. 해당 속성을 사용하지 않는 경우 사용자는 `2015-02-28`의 `2014-07-31-Preview` 또는 `2014-10-20-Preview` 중 하나에서 `api-version`을 바꿀 수 있으며, 다시 빌드하고 배포할 수 있습니다. 응용 프로그램은 이전처럼 작동합니다.

제안 사항을 구현하는 사용자 지정 응용 프로그램은 다음을 수행해야 합니다:

1. 모든 NuGet 패키지를 업데이트합니다.
1. Api-버전을 `2015-02-28`로 바꿉니다. 다음 코드 예제를 사용하는 경우 Api-버전은 **AzureSearchHelper** 클래스입니다.
1. 사용자 인덱스를 정의하는 JSON 스키마에서 `Suggestions={true | false}` 특성을 삭제합니다.
1. `Suggesters`의 인덱스 맨 아래에 구조를 추가합니다.([이후](#after) 섹션과 같이).
1. 사용자 서비스를 게시할 수 있는지 확인합니다.(명명 충돌을 방지하려면 인덱스 이름을 바꾸어야 함)
1. 솔루션을 다시 빌드하고 테스트 환경에 배포합니다.
1. 솔루션이 예상한 대로 작동되는지 보장하기 위해 모든 테스트 사례를 실행합니다.
1. 프로덕션을 롤아웃하세요.

[codeplex의 Adventure Works 샘플](https://azuresearchadventureworksdemo.codeplex.com/)에서 코드 예제는 원래대로 `Suggestions`구현됩니다. 해당 샘플을 샘플 코드의 코드 마이그레이션을 연습하기 위해 사용하려고 할 수 있습니다.

다음 섹션에서는 제안 사항이 구현되기 [이전](#before) 및 [이후](#after)를 보여줍니다. **CreateCatalogIndex()** 메서드를 [이후](#after) 섹션 버전으로 대체하고, 새로운 기능을 시도하는 솔루션을 빌드 및 배포할 수 있습니다.

<a name="before"></a>
###이전###

표시된 것처럼 `Suggestions`은 각 필드에 설정된 부울 속성입니다. 모든 `Suggestions` 특성을 삭제합니다.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false }
                }
            };

<a name="after"></a>
###명령이###

마이그레이션된 스키마 정의는 아래 쪽에서 `Suggestions` 속성을 생략하고 `Suggesters` 구성을 추가합니다.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true }
                },
                suggesters = new[]
                {
                new {
                    name = "sg",
                    searchMode = "analyzingInfixMatching",
                    sourceFields = new []{"name", "productNumber", "categoryName", "modelName", "description"}
                    }
                 }
            };

##새로운 기능 및 접근 방법 평가##

솔루션을 이식하고 예상 대로 실행되는지 확인한 후에 새로운 기능에 대해 읽을 수 있는 이러한 링크를 사용할 수 있습니다.

- [Azure 검색은 일반적으로 사용할 수 있습니다 (블로그 게시물)](http://go.microsoft.com/fwlink/p/?LinkId=528211)
- [Azure 검색에 최신 업데이트된 새로운 기능](search-latest-updates.md)
- [Azure 검색이란?](search-what-is-azure-search.md)

##도움말 보기##

API 버전`2015-02-28`은 SLA에 따릅니다. 지원 티켓을 만들기 위해 지원 옵션 및 [해당 페이지](../support/options/)의 링크를 사용합니다.

 

<!---HONumber=AcomDC_0128_2016-->