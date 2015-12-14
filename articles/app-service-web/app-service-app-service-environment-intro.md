<properties 
	pageTitle="앱 서비스 환경 소개" 
	description="모든 앱을 실행하기 위한 VNet 가입 보안 전용 확장 단위를 제공하는 앱 서비스 환경 기능에 대해 알아봅니다." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/06/2015"
	ms.author="stefsch"/>

# 앱 서비스 환경 소개

## 개요 ##
앱 서비스 환경은 Azure 앱 서비스의 [프리미엄][PremiumTier] 서비스 계획 옵션으로, [웹앱][WebApps], [모바일 앱][MobileApps] 및 [API 앱][APIApps]을 포함하여 높은 확장성에서 Azure 앱 서비스 앱을 안전하게 실행하기 위해 완전히 격리된 전용 환경을 제공합니다.

앱 서비스 환경은 다음을 필요로 하는 응용 프로그램 작업에 적합합니다.

- 매우 높은 확장성
- 격리 및 보안된 네트워크 액세스

고객은 단일 Azure 지역 내뿐만 아니라 여러 Azure 지역에 걸쳐서 여러 앱 서비스 환경을 만들 수 있습니다. 따라서 앱 서비스 환경은 높은 RPS 작업을 지원하여 상태가 없는 응용 프로그램 계층을 수평적으로 확장하는 데 적합합니다.

앱 서비스 환경은 단일 고객의 응용 프로그램만을 실행하도록 격리되며 항상 가상 네트워크에 배포됩니다. 고객은 인바운드 및 아웃바운드 응용 프로그램 네트워크 트래픽 둘 다에 대해 세밀하게 제어할 수 있고 응용 프로그램은 가상 네트워크를 통해 온-프레미스 회사 리소스에 고속 보안 연결을 설정할 수 있습니다.

앱 서비스 환경이 어떻게 높은 확장성을 사용하고 네트워크 액세스를 보호할 수 있는지에 대한 개요는 앱 서비스 환경에서 [AzureCon 심층 분석][AzureConDeepDive]을 참조하세요.

여러 앱 서비스 환경을 사용하는 수평 확장에 대한 심층 분석은 [지역 분산 앱 메모리 공간][GeodistributedAppFootprint]을 설정하는 방법에 대한 문서를 참조하세요.

AzureCon 심층 분석에 표시된 보안 아키텍처를 구성하는 방법을 보려면 앱 서비스 환경을 사용하여 [계층화된 보안 아키텍처](app-service-app-service-environment-layered-security.md)를 구현하는데 대한 문서를 참조하세요.

앱 서비스 환경에서 실행 중인 앱은 웹 응용 프로그램 방화벽 (WAF) 등의 업스트림 장치에서 제어된 액세스를 가질 수 있습니다. [앱 서비스 환경에 대한 WAF 구성](app-service-app-service-environment-web-application-firewall.md)의 문서는 이 시나리오에 대해 설명합니다.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 전용 계산 리소스 ##
앱 서비스 환경의 모든 컴퓨터 리소스는 전적으로 단일 구독 전용이며 앱 서비스 환경은 단일 응용 프로그램 전용으로 최대 50개 계산 리소스로 구성될 수 있습니다.

앱 서비스 환경은 1~3개 작업자 계산 리소스 풀뿐만 아니라 프런트 엔드 계산 리소스 풀로 구성됩니다.

프런트 엔드 풀에는 앱 서비스 환경 내의 앱 요청에 대한 자동 부하 분산뿐 아니라 SSL 종료를 담당하는 계산 리소스가 포함되어 있습니다.

각 작업자 풀에는 하나 이상의 Azure 앱 서비스 앱을 포함하는 [앱 서비스 계획][AppServicePlan]에 할당된 계산 리소스가 있습니다. 앱 서비스 환경에는 최대 세 가지 작업자 풀을 둘 수 있으므로 작업자 풀마다 다른 계산 리소스를 유연하게 선택할 수 있습니다.

예를 들어 앱 개발 또는 테스트용 앱 서비스 계획에 사용하기 위해 성능이 낮은 계산 리소스가 포함된 하나의 작업자 풀을 만들 수 있습니다. 두 번째(또는 세 번째) 작업자 풀은 프로덕션 앱을 실행하는 앱 서비스 계획에 성능이 보다 뛰어난 계산 리소스를 사용할 수 있습니다.

프런트 엔드 및 작업자 풀에 사용할 수 있는 계산 리소스의 양에 대한 자세한 내용은 [앱 서비스 환경을 구성하는 방법][HowToConfigureanAppServiceEnvironment]을 참조하세요.

앱 서비스 환경에서 지원되는 사용 가능한 계산 리소스 크기에 대한 자세한 내용은 [앱 서비스 가격][AppServicePricing] 페이지에서 프리미엄 가격 계층의 앱 서비스 환경에 사용 가능한 옵션을 참조하세요.

## 가상 네트워크 지원 ##
기존 지역 클래식 "v1" 가상 네트워크 또는 새 지역 클래식 "v1" 가상 네트워크에서 앱 서비스 환경을 만들 수 있습니다.([가상 네트워크에 대한 자세한 정보][MoreInfoOnVirtualNetworks]) 앱 서비스 환경은 항상 지역 가상 네트워크, 보다 정확히 말하자면 지역 가상 네트워크의 서브넷 내에 존재하므로 가상 네트워크의 보안 기능을 활용하여 인바운드 및 아웃바운드 네트워크 통신을 모두 제어할 수 있습니다.

[네트워크 보안 그룹][NetworkSecurityGroups]을 사용하여 앱 서비스 환경이 있는 서브넷에 대한 인바운드 네트워크 통신을 제한할 수 있습니다. 이 옵션을 통해 웹 응용 프로그램 방화벽 및 SaaS 공급자와 같은 업스트림 장치 및 서비스 뒤에서 앱을 실행할 수 있습니다.

또한 앱에서는 내부 데이터베이스 및 웹 서비스와 같은 회사 리소스에 자주 액세스해야 합니다. 일반적인 접근 방법은 Azure 가상 네트워크 내에서 이동하는 내부 네트워크 트래픽에만 이러한 끝점을 사용할 수 있도록 하는 것입니다. 앱 서비스 환경이 내부 서비스와 동일한 가상 네트워크에 가입되면 해당 환경에서 실행되는 앱은 [사이트 간][SiteToSite] 및 [Azure ExpressRoute][ExpressRoute] 연결을 통해 도달할 수 있는 끝점을 비롯하여 내부 서비스에 액세스할 수 있게 됩니다.

앱 서비스 환경이 가상 네트워크 및 온-프레미스 네트워크와 함께 작동하는 방법에 대한 자세한 내용은 [네트워크 구조][NetworkArchitectureOverview], [인바운드 트래픽 제어][ControllingInboundTraffic] 및 [백 엔드에 안전하게 연결][SecurelyConnectingToBackends]에 대한 문서를 참조하세요.

**참고:** "v2" 가상 네트워크에는 앱 서비스 환경을 만들 수 없습니다.

## 시작

앱 서비스 환경을 시작하려면 [앱 서비스 환경을 만드는 방법][HowToCreateAnAppServiceEnvironment]을 참조하세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스][AzureAppService]를 참조하세요.

앱 서비스 환경 네트워크 구조의 개요는 [네트워크 구조 개요][NetworkArchitectureOverview] 문서를 참조하세요.

Express 경로로 앱 서비스 환경을 사용하는 방법에 대한 자세한 내용은 [Express 경로 및 앱 서비스 환경][NetworkConfigDetailsForExpressRoute]에 대한 문서를 참조하세요.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]: https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=AcomDC_1203_2015-->