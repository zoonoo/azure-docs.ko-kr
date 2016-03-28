<properties
	pageTitle="Azure 포털을 사용하여 Azure 검색 인덱스 만들기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 포털을 사용하여 인덱스를 만듭니다."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="03/10/2016"
	ms.author="ashmaka"/>

# Azure 포털을 사용하여 Azure 검색 인덱스 만들기
> [AZURE.SELECTOR]
- [개요](search-what-is-an-index.md)
- [포털](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST (영문)](search-create-index-rest-api.md)

이 문서는 Azure 포털을 사용하여 Azure 검색 [인덱스](search-what-is-an-index.md)를 만드는 프로세스를 안내합니다.

이 가이드를 수행하고 인덱스를 만들기 전에 이미 [Azure 검색 서비스를 만들어야](search-create-service-portal.md) 합니다.


## I. Azure 검색 블레이드로 이동
1. [Azure 포털](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 왼쪽에 있는 메뉴에서 "모든 리소스"를 클릭합니다.
2. Azure 검색 서비스 선택

## II. 인덱스 추가 및 명명
1. "인덱스 추가" 단추를 클릭합니다.
2. Azure 검색 인덱스를 명명합니다. 이 가이드에서 호텔을 검색하는 인덱스를 만들고 있으므로 인덱스를 "호텔"로 명명합니다.
  * 인덱스 이름은 문자로 시작하고 소문자, 숫자 또는 대시("-")만 포함해야 합니다.
  * 또한 서비스 이름과 마찬가지로 선택한 인덱스 이름은 Azure 검색 API에 대한 HTTP 요청을 보내는 끝점 URL의 일부입니다.
3. "필드" 항목을 클릭하여 새 블레이드를 엽니다.

![](./media/search-create-index-portal/add-index.png)


## III. 인덱스의 필드 만들기 및 정의
1. "필드" 항목을 선택하여 인덱스 정의를 입력하라는 양식과 함께 새 블레이드가 열립니다.
2. 양식을 사용하여 인덱스에 필드를 추가합니다.

  * Edm.String 형식의 *키* 필드는 모든 Azure 검색 인덱스에 필수입니다. 이 키 필드는 기본적으로 필드 이름 "ID"로 만들어집니다. 인덱스에서 "hotelId"로 변경했습니다.
  * 인덱스 스키마의 특정 속성을 한 번만 설정할 수 있고 나중에 업데이트할 수 없습니다. 이 때문에 필드 형식을 변경하는 등 인덱싱을 다시 수행해야 하는 모든 스키마 업데이트는 초기 구성 후인 현재에도 가능하지 않습니다.
  * 응용 프로그램에서 사용되는 방법에 따라 각 필드에 대한 속성 값을 신중하게 선택했습니다. 인덱스를 각 필드로 디자인하는 경우 검색 사용자 환경 및 비즈니스 요구를 [적합한 속성](https://msdn.microsoft.com/library/azure/dn798941.aspx)으로 할당해야 한다는 점을 염두합니다. 이러한 속성은 어떤 검색 기능(전체 텍스트 검색의 필터링, 패싯, 정렬 등)이 어떤 필드에 적용될지를 제어합니다. 예를 들어 호텔을 검색 중인 사용자에게는 "설명" 필드에서 키워드 일치 항목이 유용할 수 있으므로 "검색 가능" 속성을 설정하여 해당 필드에 대한 전체 텍스트 검색을 사용하도록 설정합니다.
	* 또한 블레이드 맨 위에 있는 "분석기" 탭을 클릭하여 각 필드에 [언어 분석기](https://msdn.microsoft.com/ko-KR/library/azure/dn879793.aspx)를 설정할 수 있습니다. 프랑스어 텍스트를 위한 인덱스의 필드에 프랑스어 분석기를 선택하였음을 아래에서 확인할 수 있습니다.

3. "필드" 블레이드에서 **확인**을 클릭하여 필드 정의 확인합니다.
4. "인덱스 추가" 블레이드에서 **확인**을 클릭하여 방금 정의한 인덱스를 저장하고 만듭니다.

아래 스크린샷에서 이름을 지정하고 "호텔" 인덱스에 대한 필드를 정의하는 방법을 볼 수 있습니다.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## 다음
Azure 검색 인덱스를 만든 후에 데이터를 검색하기 시작할 수 있도록 [콘텐츠를 인덱스에 업로드](search-what-is-data-import.md)할 준비가 되었습니다.

<!---HONumber=AcomDC_0316_2016-->