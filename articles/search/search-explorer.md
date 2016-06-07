<properties
    pageTitle="Azure 포털을 사용하여 Azure 검색 인덱스 쿼리 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
    description="Azure 포털의 검색 탐색기에서 검색 쿼리를 실행합니다."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"
/>
# Azure 포털을 사용하여 Azure 검색 인덱스 쿼리
> [AZURE.SELECTOR]
- [개요](search-query-overview.md)
- [포털](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST (영문)](search-query-rest-api.md)

이 가이드에서는 Azure 포털에서 Azure 검색 인덱스를 쿼리하는 방법을 보여 줍니다.

이 연습을 시작하기 전에 [Azure 검색 인덱스를 만들고](search-what-is-an-index.md) [데이터로 채워야](search-what-is-data-import.md) 합니다.

## I. Azure 검색 블레이드로 이동
1. [Azure 포털](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 왼쪽에 있는 메뉴에서 "모든 리소스"를 클릭합니다.
2. Azure 검색 서비스 선택

## II. 검색하려는 인덱스 선택
1. "인덱스" 타일에서 검색하려는 인덱스를 선택합니다.

![](./media/search-explorer/pick-index.png)

## III. "검색 탐색기" 타일 클릭
![](./media/search-explorer/search-explorer-tile.png)

## III. 검색 시작
1. Azure 검색 인덱스를 검색하려면 "*쿼리 문자열*" 필드에 입력을 시작한 후 "**검색**"을 누릅니다.
 * 검색 탐색기를 사용하는 경우 [쿼리 매개 변수](https://msdn.microsoft.com/library/dn798927.aspx)를 지정할 수 있습니다.

2. "*결과*" 섹션에서 쿼리 결과가 Azure 검색 REST API에 대해 검색 요청을 실행할 때 HTTP 응답 본문에 수신하는 원시 JSON에 표시됩니다.
3. 쿼리 문자열은 적절한 요청 URL로 자동으로 구문 분석되어 Azure 검색 REST API에 대해 HTTP 요청을 제출합니다.

![](./media/search-explorer/search-bar.png)

<!---HONumber=AcomDC_0601_2016-->