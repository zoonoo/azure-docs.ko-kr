---
title: "Azure App Service Environment 추가 정보"
description: "Azure App Service Environment을 설명하는 설명서 나열"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 77452413-5193-4762-8b3d-5fa8e4edf1ca
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8b401036d4fa8e6c4e6430433b1641f5cb4ae010
ms.contentlocale: ko-kr
ms.lasthandoff: 06/26/2017

---

# <a name="app-service-environment-documentation"></a>앱 서비스 환경 설명서
App Service Environment는 Azure App Service 앱을 매우 높은 확장성으로 안전하게 실행하기 위해 완전히 격리된 전용 환경을 제공하는 Azure App Service 기능입니다. 이 기능은 [Web Apps][webapps], [Mobile Apps][mobileapps], [API Apps][APIApps] 및 [함수][Functions]를 호스트할 수 있습니다.

App Service Environment는 다음을 필요로 하는 응용 프로그램 워크로드에 적합합니다.

* 매우 높은 확장성
* 격리 및 보안된 네트워크 액세스

고객은 단일 Azure 지역 내뿐만 아니라 여러 Azure 지역에 걸쳐서 여러 앱 서비스 환경을 만들 수 있습니다. 따라서 앱 서비스 환경은 높은 RPS 작업을 지원하여 상태가 없는 응용 프로그램 계층을 수평적으로 확장하는 데 적합합니다.

앱 서비스 환경은 단일 고객의 응용 프로그램만을 실행하도록 격리되며 항상 가상 네트워크에 배포됩니다. 고객은 [네트워크 보안 그룹][NSGs]을 사용하여 인바운드 및 아웃바운드 응용 프로그램 네트워크 트래픽을 세부적으로 제어할 수 있습니다. 응용 프로그램은 온-프레미스 회사 리소스에 가상 네트워크를 통한 고속 보안 연결을 설정할 수도 있습니다.

앱에서는 내부 데이터베이스 및 웹 서비스와 같은 회사 리소스에 자주 액세스해야 합니다. App Service 환경에서 실행 중인 앱은 [사이트 간][SiteToSite] VPN 및 [Azure ExpressRoute][ExpressRoute] 연결을 통해 접근 가능한 리소스에 액세스할 수 있습니다.

* [App Service Environment란?][Intro]
* [App Service Environment 만들기][MakeExternalASE]
* [내부 부하 분산 장치 App Service Environment 사용][MakeILBASE]
* [App Service Environment 사용][UsingASE]
* [네트워킹 고려 사항과 App Service Environment][ASENetwork]
* [템플릿에서 App Service Environment 만들기][MakeASEfromTemplate]


## <a name="videos"></a>비디오
Azure App Service를 사용하는 Enterprise에 대한 최신 PaaS 마스터
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

확장성이 매우 높으며 안전한 앱 배포
>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

Azure App Service에서 Enterprise 웹 및 Mobile Apps 실행
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]

## <a name="asev1"></a>ASEv1 ##
App Service Environment에는 두 가지 버전(ASEv1 및 ASEv2)이 있습니다. ASEv1에 대한 자세한 내용은 [App Service Environment v1 소개][ASEv1README]를 참조하세요.


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[ASEv1README]: ../app-service-app-service-environments-readme.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-site-to-site-create.md
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

