<properties
	pageTitle="포털에서 Azure 검색 서비스 만들기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 클래식 포털을 사용하여 무료 또는 표준 Azure 검색을 기존 구독에 추가합니다. Azure 검색은 사용자 지정 앱에 대한 클라우드 호스트된 검색 서비스입니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Azure 클래식 포털에서 Azure 검색서비스 만들기

Microsoft Azure 검색은 호스팅되는 클라우드 검색 서비스로, 서비스 기능을 사용자 지정 응용 프로그램에 포함할 수 있습니다. Azure 클래식 포털, .NET SDK 또는 REST API를 통해 액세스 및 관리할 수 있는 검색 데이터에 대한 검색 엔진 및 저장소를 제공합니다. 주요 기능에는 자동 완성 쿼리, 유사 항목 일치, 적중 항목 강조 표시, 패싯 탐색, 평가 프로필, 다국어 지원 등이 있습니다. Azure 검색에서 수행하는 작업에 대한 자세한 내용은 [Azure 검색이란?](seach-what-is-search.md)을 참조하세요.

## 무료 구독에 Azure 검색 추가

관리자는 공유 서비스를 선택할 때 무료로 또는 전용 리소스를 옵트인(opt in)할 때 표준 요금으로 Azure 검색을 기존 Azure 구독에 추가할 수 있습니다.

1. [Azure 클래식 포털](https://portal.azure.com)에 로그인합니다.

2. 이동 표시줄에서 **새로 만들기** > **데이터 + 저장소** > **검색**을 클릭합니다.

     ![][1]

3. 서비스 이름, 가격 책정 계층, 리소스 그룹, 구독 및 위치를 구성합니다. 이러한 설정은 필수 사항이며, 서비스가 프로비전된 후에는 변경할 수 없습니다.

     ![][2]

	- **서비스 이름**은 고유하고 소문자여야 하며, 공백 없이 15자 미만이어야 합니다. 이 이름은 Azure 검색 서비스 끝점의 일부가 됩니다. 이름 지정 규칙에 대한 자세한 내용은 [이름 지정 규칙](https://msdn.microsoft.com/library/azure/dn857353.aspx)을 참조하세요.

	- **가격 책정 계층**은 용량 및 대금 청구를 결정합니다. 두 계층 모두 동일한 기능을 제공하지만 리소스 수준이 다릅니다.

		- **무료**는 다른 구독자와 공유되는 클러스터에서 실행되며, 자습서를 체험하고 개념 증명 코드를 작성하기에 충분한 용량을 제공하지만 프로덕션 응용 프로그램에 사용할 수 없습니다. 일반적으로 무료 서비스를 배포하는 데 몇 분 정도 걸립니다.
		- **표준**은 전용 리소스에서 실행되고 확장성이 높습니다. 처음에는 하나의 복제본과 하나의 파티션으로 프로비전되지만 서비스를 만든 후 용량을 조정할 수 있습니다. 표준 서비스를 배포하는 데 더 오래 걸립니다(일반적으로 약 15분).

	- **리소스 그룹**은 일반적인 목적에 사용되는 서비스 및 리소스의 컨테이너입니다. 예를 들어 Azure 검색, Azure 앱 서비스의 웹앱 기능 및 Azure Blob 저장소를 기반으로 하여 사용자 지정 검색 응용 프로그램을 작성하는 경우 포털 관리 페이지에서 해당 서비스를 결합하는 리소스 그룹을 만들 수 있습니다.

	- **구독**에서는 둘 이상의 구독이 있는 경우 여러 구독 중에서 선택할 수 있습니다.

	- **위치**는 데이터 센터 지역입니다. 현재 모든 리소스는 동일한 데이터 센터에서 실행되어야 합니다. 여러 데이터 센터에 리소스를 분산하는 것은 지원되지 않습니다.

4. **만들기**를 클릭하여 서비스를 프로비전합니다.

표시줄의 알림을 확인합니다. 서비스를 사용할 준비가 되면 알림이 표시됩니다.

<a id="sub-3"></a>
## 전용 리소스를 가져오려면 표준 계층 검색 서비스를 추가합니다.

많은 고객이 무료 서비스를 시작한 다음 더 큰 작업 부하를 수용하기 위해 표준 계층으로 전환합니다. 표준 계층은 Azure 데이터 센터에서 사용자만 사용할 수 있는 전용 리소스를 제공합니다.

Azure 검색 작업에는 저장소 및 서비스 복제본이 둘 다 필요합니다. 리소스를 추가하기 위한 옵션이 없는 무료 서비스와 달리, 표준 계층은 시나리오에 보다 중요한 리소스를 증가시켜 더 많은 저장소 또는 쿼리 지원을 추가하기 위해 확장할 수 있습니다.

표준 계층을 사용하려면 해당 가격 수준에서 새 검색 서비스를 만들어야 할 수 있습니다. 이 문서의 이전 단계를 반복하여 새 Azure 검색 서비스를 만들 수 있습니다. 전용 리소스를 설정하는 데 시간이 걸릴 수 있습니다(최대 15분 이상).

무료 버전의 현재 위치 업그레이드는 없습니다. 표준으로 전환하면 크기가 확장될 수 있으므로 새 서비스가 필요합니다. 검색 응용 프로그램에서 사용하는 인덱스와 문서를 다시 로드해야 합니다.

표준 가격 책정 계층의 Azure 검색 서비스는 각각 하나의 복제본과 파티션으로 만들어지지만 상위 리소스 수준에서 쉽게 크기를 조정할 수 있습니다.

1.	서비스가 만들어진 후 서비스 대시보드로 돌아갑니다.

2.	**크기 조정** 타일을 클릭합니다.

3.	슬라이더를 사용하여 복제본, 파티션 또는 둘 다를 추가합니다.

추가 복제본과 파티션은 검색 단위로 청구됩니다. 리소스를 추가할 때 특정 리소스 구성을 지원하는 데 필요한 총 검색 단위가 페이지에 표시됩니다.

[가격 정보](http://go.microsoft.com/fwlink/p/?LinkID=509792)에서 단위당 청구 정보를 확인할 수 있습니다. 파티션 및 복제본 조합을 구성하는 방법 결정에 대한 도움말은 [제한 및 제약 조건](search-limits-quotas-capacity.md)을 참조하세요.

<a id="sub-2"></a>
## Azure 검색 서비스의 서비스 이름 및 api-key 찾기

서비스를 만든 후 Azure 클래식 포털로 돌아가 URL 또는 `api-key`를 가져올 수 있습니다. Azure 검색 서비스에 연결하려면 URL과 호출을 인증할 `api-key`가 둘 다 있어야 합니다.

1. 이동 표시줄에서 **홈**을 클릭한 다음 Azure 검색 서비스를 클릭하여 서비스 대시보드를 엽니다.

2. 서비스 대시보드에 기본 정보에 대한 타일 및 관리 키에 액세스할 수 있는 키 아이콘이 표시됩니다.

  	![][3]

3. 서비스 URL 및 관리 키를 복사합니다. 다음 작업인 [서비스 작업 테스트](#sub-4)에 필요합니다.


<a id="sub-4"></a>
## 서비스 작동 테스트

서비스가 작동하고 클라이언트 응용 프로그램에서 액세스 가능한지 확인하는 것이 Azure 검색 구성의 최종 단계입니다. 다음의 링크 중 하나를 사용하면 코드를 작성하지 않고 서비스 가용성을 확인할 수 있습니다.

- [Azure 검색에서 Chrome Postman을 사용하는 방법](search-chrome-postman.md)
- [Azure 검색에서 Telerik Fiddler를 사용하는 방법](search-fiddler.md)

<!--Next steps and links -->
<a id="next-steps"></a>
## 다음 단계

다음 내용에서는 Azure 검색을 사용하는 검색 응용 프로그램을 작성 및 관리하는 방법을 보여 줍니다.

- [.NET에서 Azure 검색을 사용하는 방법](search-howto-dotnet-sdk.md)

- [Microsoft Azure에서 검색 솔루션 관리](search-manage.md)

- [MSDN의 Azure 검색](http://msdn.microsoft.com/library/dn798933.aspx)

- [채널 9 비디오: Azure 검색 소개](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)


<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to the standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=AcomDC_1203_2015-->