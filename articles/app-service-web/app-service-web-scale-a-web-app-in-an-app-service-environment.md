<properties 
	pageTitle="앱 서비스 환경에서 웹앱을 확장하는 방법" 
	description="앱 서비스 환경에서 웹앱 확장" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="ccompy"/>

# 앱 서비스 환경에서 웹앱 확장 #

크게 보면 앱 서비스 환경은 기본적으로 Azure 앱 서비스를 VNET에 개인적으로 배포한 것이며, 구독을 통해서만 관리할 수 있습니다. 앱 서비스 환경은 VNET에 있기 때문에 새로운 네트워킹 기능을 제공하며, 일반적으로 Azure 앱 서비스 환경에서 제공되는 것보다 규모를 확장할 수 있습니다. ASE(앱 서비스 환경)에 대한 자세한 내용은 [앱 서비스 환경 정의][WhatisASE]를 참조하세요. 앱 서비스 환경을 만드는 방법 또는 앱 서비스 환경에서 웹앱을 만드는 방법에 대한 자세한 내용은 [앱 서비스 환경을 만드는 방법][HowtoCreateASE] 및 [앱 서비스 환경에서 웹앱을 만드는 방법][CreateWebappinASE]을 참조하세요.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

일반적으로 웹앱의 확장 특성을 변경할 때는 앱 서비스 계획 수준에서 변경합니다. 앱 서비스 계획을 확장하는 방법 또는 앱 서비스 환경 외부의 앱 서비스 계획에 대한 자세한 내용은 [Azure 앱 서비스에서 웹앱 확장][ScaleWebapp] 및 [앱 서비스 계획 세부 개요][Appserviceplans]를 참조하세요.

앱 서비스 환경에서 웹앱을 확장하는 작업은 일반적으로 웹앱을 확장하는 작업과 매우 유사합니다. Azure 앱 서비스에서는 일반적으로 다음 세 가지를 확장할 수 있습니다.

- 가격 계획
- 작업자 크기(전용 인스턴스)
- 인스턴스 수

ASE에서는 가격 계획을 선택하거나 변경할 필요가 없습니다. 기능적인 측면에서 이미 프리미엄 가격 기능 수준이기 때문입니다. 또한 앱 서비스 환경에는 공유 작업자가 없습니다. 모두 전용 작업자입니다. ASE 관리자는 고정 크기 대신 각 작업자 풀에 사용할 계산 리소스의 크기를 할당할 수 있습니다. 따라서 필요한 경우 P4 계산 리소스가 있는 작업자 풀 1과 P1 계산 리소스가 있는 작업자 풀 2를 둘 수 있습니다. 크기 순이 아니어도 됩니다. 크기 및 해당 가격에 대한 자세한 내용은 [Azure 앱 서비스 가격][AppServicePricing]을 참조하세요. 따라서 앱 서비스 환경에서는 웹앱 및 앱 서비스 계획의 확장 옵션에 다음 항목만 남게 됩니다.

- 작업자 풀 선택
- 인스턴스 수

두 항목 중 하나의 변경은 앱 서비스 계획과 함께 표시되는 UI를 통해 수행됩니다.

![][1]

### 인스턴스 수 확장 ###

앱 서비스 환경에서 웹앱을 처음 만들 때는 내결함성을 제공하기 위해 2개 이상의 인스턴스로 확장해야 합니다.

ASE의 용량이 충분한 경우에는 매우 간단합니다. 확장할 사이트를 유지하는 앱 서비스 계획으로 이동하여 크기 조정을 선택합니다. 그러면 인스턴스 표시기를 원하는 값으로 올리고 저장할 수 있는 UI가 열립니다.

![][2]

앱 서비스 계획이 있는 작업자 풀의 사용 가능한 계산 리소스 수를 초과하여 앱 서비스 계획을 확장할 수 없습니다. 더 많은 계획이 필요한 경우 ASE 관리자에게 작업자 풀에 계산 리소스를 추가하도록 요청해야 합니다. ASE를 다시 구성하는 방법에 대한 자세한 내용은 [앱 서비스 환경을 구성하는 방법][HowtoConfigureASE]을 참조하세요.
 

### 작업자 풀 선택 ###

앱 서비스 계획 UI에서 작업자 풀 선택에 액세스할 수 있습니다. 확장할 앱 서비스 계획을 열고 작업자 풀을 선택합니다. 앱 서비스 환경에서 구성한 모든 작업자 풀이 표시됩니다. 작업자 풀이 하나뿐인 경우에는 하나의 풀만 나열됩니다. 앱 서비스 계획이 있는 작업자 풀을 변경하려면 앱 서비스 계획을 이동할 작업자 풀을 선택하기만 하면 됩니다.

![][3]

그러나 그 전에 앱 서비스 계획에 적절한 용량이 있는지 확인해야 합니다. 작업자 풀 목록에는 작업자 풀 이름이 나열될 뿐만 아니라 해당 작업자 풀에서 사용 가능한 작업자 수도 표시됩니다. 앱 서비스 계획을 포함할 수 있는 충분한 인스턴스가 있는지 확인합니다. 이동할 작업자 풀에 추가 계산 리소스가 필요한 경우 ASE 관리자에게 추가하도록 요청합니다.

하나의 작업자 풀에서 웹앱을 이동하면 웹앱이 다시 시작됩니다. 이로 인해 다시 시작하는 데 걸리는 시간에 따라 앱의 작동이 잠시 중단될 수 있습니다.

## 시작

앱 서비스 환경을 시작하려면 [앱 서비스 환경을 만드는 방법][HowtoCreateASE]을 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스][AzureAppService]를 참조하세요.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleasp.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleinstances.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scalepool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=Oct15_HO3-->