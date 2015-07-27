<properties
	pageTitle="앱 서비스 환경에서 웹앱을 만드는 방법"
	description="앱 서비스 환경에 대해 검사된 웹앱 및 앱 서비스 계획 만들기 흐름"
	services="app-service\web"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="04/27/2015"
	ms.author="ccompy"/>

# 앱 서비스 환경에서 웹앱을 만드는 방법 #

웹앱 만들기는 일반적으로 ASE(앱 서비스 환경)에서 거의 동일합니다. 앱 서비스 환경 기능을 잘 모르는 경우 [앱 서비스 환경 정의](app-service-app-service-environment-intro.md) 문서를 참조하세요.

ASE에서 웹앱을 만들려면 먼저 ASE를 만들어야 합니다. ASE를 만드는 방법에 대한 자세한 내용은 [앱 서비스 환경을 만드는 방법](app-service-web-how-to-create-an-app-service-environment.md) 문서를 참조하세요.

웹앱을 만드는 첫 번째 단계는 ASP(앱 서비스 계획)를 만들거나 선택하는 것입니다. ASE에서 ASP를 만드는 것은 일반적인 만들기 작업과 동일하며, 새로 만들기 -> 웹 + 모바일 -> 웹앱으로 시작하는 웹앱 만들기 흐름을 통해 이루어집니다.

![][1]


앱 서비스 환경에서 이미 만든 앱 서비스 계획을 사용하려면 해당 계획을 선택하고 웹앱의 이름을 입력한 후 만들기를 선택합니다. 일반적으로 웹앱을 만들 때와 동일한 흐름입니다. 주요 차이점은 웹앱은 다음 위치에서 연결된다는 점입니다.

[*sitename*].[*앱 서비스 환경의 이름*].p.azurewebsites.net

다음 위치 대신

[*sitename*].azurewebsites.net

현재 웹앱 이름은 전체 Azure 앱 서비스에서 고유해야 합니다. 즉, "thisismywebapp"이라는 웹앱을 만든 경우 현재 Azure 앱 서비스에 "thisismywebapp"이라는 다른 웹앱이 있을 수 없습니다.

### 앱 서비스 계획 ###

앱 서비스 계획은 관리되는 웹앱 집합입니다. 가격을 선택하면 개별 앱이 아니라 앱 서비스 계획에 청구되는 가격이 적용됩니다. 웹앱의 인스턴스 수를 늘리려면 ASP 인스턴스를 늘립니다. 그러면 해당 계획의 모든 웹앱에 적용됩니다. 사이트 슬롯 또는 VNET 통합과 같은 일부 기능에는 계획 내 수량 제한도 있습니다. 앱 서비스 계획에 대한 자세한 내용은 [Azure 앱 서비스 계획 세부 정보](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요.

새 앱 서비스 계획을 만드는 경우 앱 서비스 환경에서 ASP를 만들 때와 몇 가지 차이점이 있습니다. 특히 앱 서비스 환경에는 공유 작업자가 없기 때문에 작업자 선택이 다릅니다. 사용해야 하는 작업자는 관리자에 의해 앱 서비스 환경에 할당된 작업자입니다. 즉, 새 ASP를 만들려면 앱 서비스 환경에 할당된 작업자 수가 앱 서비스 환경 내 모든 ASP의 전체 인스턴스 수보다 많아야 합니다. 앱 서비스 환경의 작업자 수가 부족하여 ASP를 만들 수 없는 경우 앱 서비스 환경 관리자와 함께 추가해야 합니다.

앱 서비스 환경 호스팅 ASP와의 또 다른 차이점은 가격을 선택할 수 없다는 점입니다. 앱 서비스 환경이 있는 경우 시스템에서 사용되는 계산 리소스에 대한 비용을 지불하며 해당 환경의 ASP에 대한 추가 요금은 없습니다. 일반적으로 ASP를 만들 때 청구를 결정하는 가격 계획을 선택합니다. 앱 서비스 환경은 기본적으로 콘텐츠를 만들 수 있는 개인 위치입니다. 따라서 콘텐츠 호스트 비용이 아니라 환경에 대한 비용을 지불합니다.

### 앱 서비스 환경 선택 ###

앱 서비스 환경은 기본적으로 개인 배포 위치이므로 먼저 위치 선택기에서 사용할 ASE를 선택해야 합니다.

![][2]

선택을 완료하면 UI가 업데이트되고 가격 계획 선택기가 작업자 풀 선택기로 바뀝니다. 위치에는 ASE 시스템의 이름과 해당 시스템이 있는 지역이 표시됩니다. URL 아래의 ASE 도메인 이름에서는 일반적으로 present .azurewebsites.net이 앱 서비스 환경 이름으로 대체됩니다.

![][3]

### 작업자 풀 선택 ###

일반적으로 Azure 앱 서비스 내부와 앱 서비스 환경 외부에는 전용 가격 계획을 선택할 때 사용할 수 있는 세 가지 크기가 있습니다. 마찬가지로 ASE를 소유한 고객은 최대 3개의 작업자 풀을 정의하고 해당 작업자 풀에 사용되는 VM의 크기를 지정할 수 있습니다. ASP에 대한 가격 계획을 선택하는 대신 작업자 풀을 선택합니다.

작업자 풀 선택 UI에는 작업자 풀 이름 아래에 해당 작업자 풀에 사용되는 VM의 크기가 표시됩니다. 사용 가능한 양은 해당 풀에서 사용할 수 있는 VM 수를 나타냅니다. 실제로 전체 풀에는 이보다 많은 VM이 있을 수 있지만 이 값은 현재 사용 중이지 않은 개수를 나타냅니다. 앱 서비스 환경을 조정하여 계산 리소스를 추가해야 하는 경우 [앱 서비스 환경 구성](app-service-web-configure-an-app-service-environment.md) 문서를 참조하세요.

![][4]

이 예제에서는 사용 가능한 작업자 풀이 두 개뿐인 것을 볼 수 있습니다. 이는 ASE 관리자가 2개의 작업자 풀에만 VM을 할당했기 때문입니다. 세 번째 풀은 할당된 VM이 있으면 표시됩니다.

### 웹앱을 만든 후 ###

ASE에서 웹앱을 실행하고 ASP를 관리하기 위해 고려해야 하는 몇 가지 사항이 있습니다.

앞서 설명한 것처럼 ASE 소유자는 시스템의 크기를 관리하므로 원하는 ASP를 호스트할 충분한 용량이 있는지 확인해야 합니다. 사용 가능한 작업자가 없으면 ASP를 만들 수 없습니다. 또한 웹앱을 확장할 수도 없습니다. 추가 인스턴스가 필요한 경우 앱 서비스 관리자에게 작업자를 추가하도록 요청해야 합니다.

웹앱과 ASP를 만든 후 이를 확장하는 것이 좋습니다. ASE에는 앱에 대한 내결함성을 제공하기 위해 항상 2개 이상의 ASP 인스턴스가 있어야 합니다. ASE에서 ASP를 확장하는 작업은 일반적으로 ASP UI를 통해 이루어집니다. 확장에 대한 자세한 내용은 [앱 서비스 환경에서 웹앱을 확장하는 방법](app-service-web-scale-a-web-app-in-an-app-service-environment.md) 문서를 참조하세요.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
 

<!---HONumber=July15_HO3-->