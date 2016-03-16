<properties
	pageTitle="Azure 검색 쿼리 작성 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색에서 검색 쿼리를 작성하고 검색 매개 변수를 사용하여 검색 결과를 필터링하고 정렬하고 패싯합니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/23/2016"
	ms.author="heidist"/>

# Azure 검색의 쿼리
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

내장된 검색 환경에 대해 Azure 검색을 통합하는 사용자 지정 솔루션은 검색 쿼리를 생성하여 이를 처리하도록 검색 엔진에 전송하는 코드를 포함해야 합니다.

쿼리 실행을 위한 메서드를 작성하기 위해 .NET SDK 또는 REST API를 사용할 수 있습니다. 예비 테스트 또는 탐색을 위해 Azure 포털의 기본 제공 검색 탐색기 등의 도구를 사용하여 인덱스를 쿼리하거나, Fiddler를 사용하여 검색 서비스에 유효한 요청을 실행할 수 있습니다.

코드로 쿼리를 구성하는 단계별 데모와 샘플 목록은 [비디오, 자습서, 데모 및 코드 샘플](search-video-demo-tutorial-list.md)에서 제공합니다. 거의 모든 샘플에서 검색 서비스를 쿼리하는 코드를 제공하고 있습니다.

<!---HONumber=AcomDC_0128_2016-->