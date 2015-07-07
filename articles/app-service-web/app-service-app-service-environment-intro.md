<properties 
	pageTitle="앱 서비스 환경 소개" 
	description="모든 앱을 실행하기 위한 VNet 가입 보안 전용 확장 단위를 제공하는 앱 서비스 환경 기능에 대해 알아봅니다." 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="stefsh"/>

# 앱 서비스 환경 소개

## 개요 ##
앱 서비스 환경은 Azure 앱 서비스의 [프리미엄][PremiumTier] 서비스 계획 옵션으로, 모든 앱의 안전한 실행을 위해 완전히 격리된 전용 환경을 제공합니다. 여기에는 확장된 크기 조정 옵션과 함께 [웹 앱][WebApps], [모바일 앱][MobileApps], [API 앱][APIApps] 및 [논리 앱][LogicApps]이 포함됩니다.

앱 서비스 환경의 계산 리소스는 사용자 앱을 실행하는 데에만 사용됩니다. 앱 서비스 환경은 항상 지역 가상 네트워크에 만들어지므로 앱에 네트워크 격리를 위한 새 옵션을 제공합니다. 또한 앱 서비스 환경은 앱을 실행하는 데 최대 50개의 계산 리소스를 사용할 수 있는 추가 크기 조정 옵션을 지원합니다. 앱 서비스 환경 외부에서는 앱을 호스트하는 데 사용할 수 있는 계산 리소스가 20개로 제한됩니다.

## 가상 네트워크 지원 ##
기존 지역 가상 네트워크 또는 새 지역 가상 네트워크에서 앱 서비스 환경을 만들 수 있습니다([가상 네트워크에 대 한 자세한 정보][MoreInfoOnVirtualNetworks]). 앱 서비스 환경은 항상 지역 가상 네트워크, 보다 정확히 말하자면 지역 가상 네트워크의 서브넷 내에 존재하므로 가상 네트워크의 보안 기능을 활용하여 인바운드 및 아웃바운드 네트워크 통신을 모두 제어할 수 있습니다.

[네트워크 보안 그룹][NetworkSecurityGroups]을 사용하여 앱 서비스 환경이 있는 서브넷에 대한 인바운드 네트워크 통신을 제한할 수 있습니다. 이 옵션을 통해 웹 응용 프로그램 방화벽 및 SaaS 공급자와 같은 업스트림 장치 및 서비스 뒤에서 앱을 실행할 수 있습니다.

또한 앱에서는 내부 데이터베이스 및 웹 서비스와 같은 회사 리소스에 자주 액세스해야 합니다. 일반적인 접근 방법은 Azure 가상 네트워크 내에서 이동하는 내부 네트워크 트래픽에만 이러한 끝점을 사용할 수 있도록 하는 것입니다. 앱 서비스 환경이 내부 서비스와 동일한 가상 네트워크에 가입되면 해당 환경에서 실행되는 앱은 [사이트 간][SiteToSite] 및 [Azure ExpressRoute][ExpressRoute] 연결을 통해 도달할 수 있는 끝점을 비롯하여 내부 서비스에 액세스할 수 있게 됩니다.

## 전용 계산 리소스 ##
앱 서비스 환경의 모든 계산 리소스는 단일 구독에만 사용됩니다. 앱 서비스 환경은 단일 프런트 엔드 계산 리소스 풀 및 1~3개의 작업자 계산 리소스 풀로 구성됩니다.

프런트 엔드 풀에는 앱 서비스 환경 내의 앱 요청에 대한 자동 부하 분산뿐 아니라 SSL 종료를 담당하는 계산 리소스가 포함되어 있습니다.

각 작업자 풀에는 하나 이상의 Azure 앱 서비스 앱을 포함하는 [앱 서비스 계획][AppServicePlan]에 할당된 계산 리소스가 있습니다. 앱 서비스 환경에는 최대 세 가지 작업자 풀을 둘 수 있으므로 작업자 풀마다 다른 계산 리소스를 유연하게 선택할 수 있습니다.

예를 들어 앱 개발 또는 테스트용 앱 서비스 계획에 사용하기 위해 성능이 낮은 계산 리소스가 포함된 하나의 작업자 풀을 만들 수 있습니다. 두 번째(또는 세 번째) 작업자 풀은 프로덕션 앱을 실행하는 앱 서비스 계획에 성능이 보다 뛰어난 계산 리소스를 사용할 수 있습니다.

단일 작업자 풀에 있는 최대 50개의 계산 리소스로 앱 서비스 환경을 구성할 수 있습니다. 프런트 엔드 및 작업자 풀에 사용할 수 있는 계산 리소스의 양에 대한 자세한 내용은 [앱 서비스 환경을 구성하는 방법][HowToConfigureanAppServiceEnvironment]을 참조하세요.

앱 서비스 환경에서 지원되는 사용 가능한 계산 리소스 크기에 대한 자세한 내용은 [앱 서비스 가격][AppServicePricing] 페이지에서 프리미엄 가격 계층의 앱 서비스 환경에 사용 가능한 옵션을 참조하세요.


## 시작

앱 서비스 환경을 시작하려면 [앱 서비스 환경을 만드는 방법][HowToCreateAnAppServiceEnvironment]을 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스][AzureAppService]를 참조하세요.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[Azure preview portal]: http://portal.azure.com
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[SiteToSite]: https://msdn.microsoft.com/library/azure/dn133795.aspx
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=62-->