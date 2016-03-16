<properties
	pageTitle="포털에서 검색 탐색기를 사용하여 Azure 검색 인덱스 쿼리 | Microsoft Azure | 호스팅되는 클라우드 검색 서비스"
	description="탐색기 검색은 Azure 검색 인덱스를 쿼리하는 코드 없는 방식입니다."
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
	ms.date="01/23/2016"
	ms.author="heidist"/>

# Azure 포털에서 검색 탐색기를 사용하여 Azure 검색 인덱스 쿼리
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

**검색 탐색기**는 Azure 검색 인덱스에 대한 코드 없는 쿼리를 위한 Azure 포털의 기본 제공 쿼리 도구입니다. 이 도구는 서비스에서 모든 인덱스에 연결하며 검색 문자열과 수식을 입력하는 검색 상자를 제공합니다. 유효한 쿼리 구문은 사용자 입력을 기초로 생성됩니다. 결과는 포털 페이지에 표시됩니다.

검색 탐색기는 쿼리 구문을 학습하고, 경우에 따라 임시 쿼리를 실행하거나 코드에 삽입하기 전에 쿼리 수식을 조정하는 데 적합합니다. 이 도구를 사용하려면 기존에 Azure 검색 서비스와 인덱스가 있어야 합니다. 이 작업에 대한 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md) 및 [포털을 사용하여 Azure 검색에 데이터 가져오기](search-import-data-portal.md)를 참조하세요.

## 검색 탐색기 열기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. Azure 검색 서비스의 서비스 대시보드를 엽니다. 대시보드를 찾을 수 있는 몇 가지 방법이 있습니다.
	- 표시줄에서 **홈**을 클릭합니다. 홈 페이지에는 구독에 포함된 모든 서비스에 대한 타일이 있습니다. 타일을 클릭하면 서비스 대시보드가 열립니다.
	- 표시줄에서 **모두 찾아보기** > **필터 기준** > **서비스 검색**을 클릭하여 목록에서 검색 서비스를 찾습니다.

3. 서비스 대시보드의 위쪽에 **검색 탐색기**에 대한 한 명령이 포함된 명령 모음이 표시됩니다.

  	![][1]

4. **검색 탐색기**를 클릭하여 검색 탐색기 블레이드를 밀어 엽니다.
5. 인덱스 및 API 버전을 설정합니다. 검색 탐색기는 인덱스 목록의 최초 인덱스에 자동으로 연결하지만 **인덱스 변경**을 클릭하여 다른 인덱스로 전환할 수 있습니다. **API 버전 설정**에서는 일반적으로 사용 가능한 버전 또는 미리 보기 버전을 지정할 수 있습니다. 일부 쿼리 구문은 미리 보기 전용입니다.
6. [Azure 검색 시작하기](search-get-started-portal.md)에 따라 로드 아일랜드에 대한 미국 지리정보국(USGS) 데이터를 기초로 인덱스를 만들어 입력했다면 이 검색 조건을 사용하여 동일한 3개 결과를 검색 탐색기로 불러 확인할 수 있습니다: `roger williams +school -building`.

검색 조건 입력에 따라 자동으로 생성된 쿼리 구문을 확인합니다.

![][2]

## 다음 단계

쿼리 구문에 대한 자세한 내용과 예제는 MSDN의 [문서 검색](https://msdn.microsoft.com/library/azure/dn798927.aspx)에서 제공합니다.

이 링크에서 검색 서비스나 인덱스를 만들거나 관리하기 위한 코드 없는 다른 방법을 확인하세요.

- [포털에서 Azure 검색서비스 만들기](search-create-service-portal.md)
- [포털을 사용하여 Azure 검색으로 데이터 가져오기](search-import-data-portal.md)
- [Azure에서 검색 서비스 관리](search-manage.md)

<!--Image References-->
[1]: ./media/search-explorer/AzSearch-SearchExplorer-Btn.png
[2]: ./media/search-explorer/AzSearch-SearchExplorer-Example.png

<!---HONumber=AcomDC_0128_2016-->