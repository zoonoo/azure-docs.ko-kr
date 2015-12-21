<properties
	pageTitle="여러 Azure 지역에 Azure API 관리 서비스 인스턴스를 배포하는 방법"
	description="여러 Azure 지역에 Azure API 관리 서비스 인스턴스를 배포하는 방법에 대해 알아봅니다." 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="sdanie"/>

# 여러 Azure 지역에 Azure API 관리 서비스 인스턴스를 배포하는 방법

API 관리는 원하는 Azure 지역의 수에 상관 없이 단일 API 관리 서비스를 배포하기 위해 API 게시자를 사용할 수 있는 여러 지역 배포를 지원합니다. 이를 통해 지역적으로 배포된 API 소비자가 느끼는 요청 대기 시간을 줄일 수 있으며 한 지역이 오프라인인 경우 가능한 서비스를 개선할 수도 있습니다.

API 관리 서비스가 처음 만들어질 때 한 [단위][]만 포함하며 단일 Azure 지역에 있어 기본 지역으로 지정됩니다. Azure 클래식 포털을 통해 추가 지역을 쉽게 추가할 수 있습니다. API 관리 게이트웨이 서버는 각 지역에 배포되고 호출 트래픽이 가장 가까운 게이트웨이에 라우팅됩니다. 지역이 오프라인 상태가 되면 다음으로 가장 가까운 게이트웨이에 트래픽이 자동으로 리디렉션됩니다.

> [AZURE.IMPORTANT]다중 지역 배포는 **[프리미엄][]** 계층에서만 사용할 수 있습니다.

## <a name="add-region"> </a>새 지역에 API 관리 서비스 인스턴스 배포

시작하려면 API 관리 서비스에 대해 Azure 클래식 포털에서 **관리**를 클릭합니다. API 관리 게시자 포털로 이동됩니다.

![게시자 포털][api-management-management-console]

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][] 자습서의 [API 관리 서비스 인스턴스 만들기][]를 참조하세요.

API 관리 서비스에 인스턴스에 대해 Azure 클래식 포털의 **크기 조정** 탭으로 이동합니다.

![크기 조정 탭][api-management-scale-service]

새 지역에 배포하려면 기본 지역 아래 드롭다운 목록을 클릭하고 목록에서 지역을 선택합니다.

![지역 추가][api-management-add-region]

지역이 선택되면 단위 드롭다운 목록에서 새 지역에 대한 단위 수를 선택합니다.

![단위 지정][api-management-select-units]

원하는 지역 및 단위가 구성되면 **저장**을 클릭합니다.

## <a name="remove-region"> </a>지역에서 API 관리 서비스 인스턴스 삭제

지역에서 API 관리 서비스 인스턴스를 제거하려면 API 관리 서비스 인스턴스에 대해 Azure 클래식 포털의 **크기 조정** 탭으로 이동합니다.

![크기 조정 탭][api-management-scale-service]

제거하려는 지역 오른쪽에 있는 **X**를 클릭합니다.

![지역 제거][api-management-remove-region]

원하는 지역이 제거되면 **저장**을 클릭합니다.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Azure API 관리 시작]: api-management-get-started.md#create-service-instance
[API 관리 서비스 인스턴스 만들기]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[단위]: http://azure.microsoft.com/pricing/details/api-management/
[프리미엄]: http://azure.microsoft.com/pricing/details/api-management/

<!---HONumber=AcomDC_1210_2015-->