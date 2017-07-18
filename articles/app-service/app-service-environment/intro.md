---
title: "Azure App Service Environment 소개"
description: "Azure App Service Environment에 대한 간략한 개요"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0d078aefbf73a45298f397d02ab24b2c8232ecef
ms.contentlocale: ko-kr
ms.lasthandoff: 06/26/2017

---
# <a name="introduction-to-the-app-service-environment"></a>App Service Environment 소개 #
 
## <a name="overview"></a>개요 ##

App Service Environment는 Azure App Service 앱을 매우 높은 확장성으로 안전하게 실행하기 위해 완전히 격리된 전용 환경을 제공하는 Azure App Service 기능입니다. 이 기능은 [Web Apps][webapps], [Mobile Apps][mobileapps], [API Apps][APIapps] 및 [함수][Functions]를 호스트할 수 있습니다.

App Service Environment는 다음을 필요로 하는 응용 프로그램 워크로드에 적합합니다.

- 매우 높은 확장성
- 격리 및 보안된 네트워크 액세스
- 높은 메모리 사용률

고객은 단일 Azure 지역 내뿐만 아니라 여러 Azure 지역에 걸쳐서 여러 App Service Environment를 만들 수 있습니다. 따라서 앱 서비스 환경은 높은 RPS 작업을 지원하여 상태가 없는 응용 프로그램 계층을 수평적으로 확장하는 데 적합합니다.

앱 서비스 환경은 단일 고객의 응용 프로그램만을 실행하도록 격리되며 항상 가상 네트워크에 배포됩니다. 고객은 인바운드 및 아웃바운드 응용 프로그램 네트워크 트래픽 모두를 세밀하게 제어할 수 있고 응용 프로그램은 VPN을 통해 온-프레미스 회사 리소스에 고속 보안 연결을 설정할 수 있습니다.

App Service Environment에 대한 모든 문서와 지침은 [응용 프로그램 서비스 환경의 추가 정보][ASEReadme]에 있습니다.

App Service Environment가 어떻게 높은 확장성 및 안전한 네트워크 액세스를 사용하는지에 대한 개요는 App Service Environment에서 [AzureCon 심층 분석](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/)을 참조하세요.

여러 App Service Environment를 사용하는 수평 크기 조정에 대한 심층 분석은 [지역 분산 앱 메모리 공간](https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/)을 설정하는 방법에 대한 문서를 참조하세요.

AzureCon 심층 분석에 표시된 보안 아키텍처를 구성하는 방법을 보려면 앱 서비스 환경을 사용하여 [계층화된 보안 아키텍처](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-layered-security)를 구현하는데 대한 문서를 참조하세요.

앱 서비스 환경에서 실행 중인 앱은 웹 응용 프로그램 방화벽 (WAF) 등의 업스트림 장치에서 제어된 액세스를 가질 수 있습니다. [앱 서비스 환경에 대한 WAF 구성](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-web-application-firewall) 의 문서는 이 시나리오에 대해 설명합니다.

## <a name="dedicated-environment"></a>전용 환경 ##

App Service Environment은 단일 구독에 단독으로 전용되어 100개 인스턴스를 호스팅할 수 있습니다. 단일 App Service 계획의 100의 인스턴스부터 100개의 단일 인스턴스 App Service 계획까지 가능하며 모든 항목은 이 두 계획 사이에 있습니다.

App Service Environment는 프런트 엔드와 작업자로 구성됩니다. 프런트 엔드는 App Service Environment 내의 앱 요청에 대한 자동 부하 분산뿐 아니라 HTTP/HTTPS 종료를 담당합니다. 프런트 엔드는 App Service Environment의 App Service 계획이 스케일 아웃됨에 따라 자동으로 추가됩니다.

작업자는 고객 앱을 호스트하는 역할입니다. 작업자는 3가지 고정된 크기로 사용할 수 있습니다.
* 1 코어/3.5GB RAM
* 2 코어/7GB RAM
* 4 코어/14GB RAM.

고객은 프런트 엔드와 작업자를 관리할 필요가 없습니다. 모든 인프라는 고객이 App Service 계획을 스케일 아웃함에 따라 자동으로 추가됩니다. App Service Environment에서 App Service 계획을 생성하거나 규모를 변경함에 따라 필요한 인프라가 적절하게 추가되거나 제거됩니다.

인프라에 대해 대금을 지급하고 App Service Environment 크기에 따라 변경되지 않는 App Service Environment의 월정액이 있습니다. 거기에 App Service 계획 코어당 비용이 발생합니다. App Service Environment에 호스트되는 모든 앱은 격리 요금제 SKU에 해당합니다. App Service Environment에 대한 자세한 가격 책정 내용은 [App Service 가격 책정][Pricing] 페이지에서 App Service Environment에 대해 사용 가능한 옵션을 검토하세요.

## <a name="virtual-network-support"></a>가상 네트워크 지원 ##

App Service Environment는 Azure Resource Manager 가상 네트워크에서만 만들 수 있습니다. Azure Virtual Networks에 대한 자세한 내용은 [Azure Virtual Networks FAQ](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/)를 참조하세요. App Service Environment은 항상 가상 네트워크에 존재합니다. 더 엄밀히 말해, 가상 네트워크의 서브넷 내에 존재하므로 가상 네트워크의 보안 기능을 활용하여 사용자 앱에 대한 인바운드 및 아웃바운드 네트워크 통신을 모두 제어할 수 있습니다.

App Service Environment는 공개 IP 주소가 있는 인터넷 연결이거나 Azure ILB(내부 부하 분산 장치) 주소만 있는 내부 연결일 수 있습니다.

[네트워크 보안 그룹][NSGs]을 사용하여 App Service 환경이 있는 서브넷에 대한 인바운드 네트워크 통신을 제한할 수 있습니다. 이 옵션을 통해 웹 응용 프로그램 방화벽 및 SaaS 공급자와 같은 업스트림 장치 및 서비스 뒤에서 앱을 실행할 수 있습니다.

또한 앱에서는 내부 데이터베이스 및 웹 서비스와 같은 회사 리소스에 자주 액세스해야 합니다. App Service Environment를 온-프레미스 네트워크에 VPN 연결이 있는 Azure Virtual Network에 배포한 경우 App Service Environment에 있는 앱은 온-프레미스 리소스에 액세스할 수 있게 됩니다. 이는 VPN이 [사이트 간](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/)이든 [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/) VPN이든 마찬가지입니다.

App Service Environment가 가상 네트워크 및 온-프레미스 네트워크와 함께 어떻게 작동하는지에 대한 자세한 내용은 [App Service Environment 네트워크 고려 사항][ASENetwork]을 참조하세요.

## <a name="asev1"></a>ASEv1 ##

App Service Environment에는 ASEv1 및 ASEv2와 같은 두 가지 버전이 있습니다. 위의 정보는 ASEv2를 중심으로 합니다. 이 섹션은 ASEv1과 ASEv2의 차이를 보여줍니다. 

ASEv1에서는 모든 리소스를 수동으로 관리해야 합니다. 여기에는 IP 기반 SSL에 사용된 프런트 엔드, 작업자 및 IP 주소가 포함됩니다. App Service 계획을 스케일 아웃하기 전에 호스트할 첫 번째 작업자 풀을 먼저 스케일 아웃해야 합니다.

ASEv1은 ASEv2와는 다른 가격 책정 모델을 사용합니다. ASEv1에서는 할당된 각 코어에 대한 비용을 지불해야 합니다. 여기에는 워크로드를 호스트하지 않는 프런트 엔드 또는 작업자에 사용된 코어가 포함됩니다. ASEv1에서 App Service Environment의 기본 최대 규모 크기는 총 55개의 호스트입니다. 여기에는 작업자 및 프런트 엔드가 포함됩니다. ASEv1의 장점은 리소스 관리자 가상 네트워크뿐만 아니라 클래식 가상 네트워크에 배포할 수 있다는 것입니다. [App Service Environment v1 소개][ASEv1Intro]에서 ASEv1에 대해 자세히 알아볼 수 있습니다.

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

