---
title: App Service Environment | Microsoft Docs
description: "Azure App 서비스 환경이란? 앱 서비스 환경 소개"
keywords: "Azure App 서비스 환경, 가상 네트워크, 보안 네트워킹"
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 1db5c057-3c56-4537-b580-cdd21fe3f3a7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: df2fa1d0790f8942a0350f9734232cc4b25cadc1
ms.openlocfilehash: a54f559801e1a4f4b752bc2268ea8d6cb20b1267
ms.lasthandoff: 12/01/2016


---
# <a name="app-service-environment-documentation"></a>앱 서비스 환경 설명서
App Service 환경은 Azure App Service의 [프리미엄][PremiumTier] 서비스 계획 옵션으로, [웹앱][WebApps], [Mobile Apps][MobileApps] 및 [API 앱][APIApps]을 포함하여 높은 확장성에서 Azure App Service 앱을 안전하게 실행하기 위해 완전히 격리된 전용 환경을 제공합니다.  

앱 서비스 환경은 다음을 필요로 하는 응용 프로그램 작업에 적합합니다.

* 매우 높은 확장성
* 격리 및 보안된 네트워크 액세스

고객은 단일 Azure 지역 내뿐만 아니라 여러 Azure 지역에 걸쳐서 여러 앱 서비스 환경을 만들 수 있습니다.  따라서 앱 서비스 환경은 높은 RPS 작업을 지원하여 상태가 없는 응용 프로그램 계층을 수평적으로 확장하는 데 적합합니다.

앱 서비스 환경은 단일 고객의 응용 프로그램만을 실행하도록 격리되며 항상 가상 네트워크에 배포됩니다.  고객은 [네트워크 보안 그룹][NetworkSecurityGroups]을 사용하여 인바운드 및 아웃바운드 응용 프로그램 네트워크 트래픽을 세부적으로 제어할 수 있습니다.  응용 프로그램은 온-프레미스 회사 리소스에 가상 네트워크를 통한 고속 보안 연결을 설정할 수도 있습니다.

앱에서는 내부 데이터베이스 및 웹 서비스와 같은 회사 리소스에 자주 액세스해야 합니다.  App Service 환경에서 실행 중인 앱은 [사이트 간][SiteToSite] VPN 및 [Azure ExpressRoute][ExpressRoute] 연결을 통해 접근 가능한 리소스에 액세스할 수 있습니다.

* [앱 서비스 환경이란?](../app-service-web/app-service-app-service-environment-intro.md)
* [앱 서비스 환경 만들기](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [앱 서비스 환경에서 앱 만들기](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [앱 서비스 환경에서 내부 부하 분산 장치 생성 및 사용](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [앱 서비스 환경 구성](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [앱 서비스 환경에서 앱 확장](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [네트워크 보안 및 아키텍처](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>방법
[!INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

## <a name="videos"></a>동영상
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]



<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

