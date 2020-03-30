---
title: Azure 앱 서비스 웹 앱 및 API 보호
description: 이 문서에서는 Azure 보안 센터에서 Azure 앱 서비스 웹 앱 및 API 보호를 시작하는 데 도움이 됩니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 2d81e1a1218add504e1e35015276b6924da0e3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616477"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Azure 앱 서비스 웹 앱 및 API 보호

Azure App Service는 인프라를 관리할 필요 없이 웹 앱 및 API를 빌드하고 호스팅하기 위한 완전히 관리되는 플랫폼입니다. 엔터프라이즈급 성능, 보안 및 규정 준수 요구 사항을 충족하기 위해 관리, 모니터링 및 운영 통찰력을 제공합니다. 자세한 내용은 [Azure 앱 서비스](https://azure.microsoft.com/services/app-service/)를 참조하십시오.

Azure App 서비스 계획에 대한 고급 위협 보호를 사용하려면 다음을 수행해야 합니다.

* Azure 보안 센터의 표준 가격 책정 계층 구독
* 아래와 같이 앱 서비스 계획을 활성화합니다. 보안 센터는 기본적으로 앱 서비스와 통합되어 배포 및 온보딩의 필요성을 제거하므로 통합이 투명합니다.
* 전용 컴퓨터와 연결된 앱 서비스 계획을 수립합니다. 지원되는 요금제는 기본, 표준, 프리미엄, 격리 또는 Linux입니다. 보안 센터는 무료, 공유 또는 소비 계획을 지원하지 않습니다. 자세한 내용은 [앱 서비스 계획을](https://azure.microsoft.com/pricing/details/app-service/plans/)참조하십시오.

앱 서비스 계획을 사용하도록 설정하면 보안 센터는 앱 서비스 계획에서 다루는 리소스를 평가하고 결과에 따라 보안 권장 사항을 생성합니다. 보안 센터는 앱 서비스가 실행되는 VM 인스턴스와 관리 인터페이스를 보호합니다. 또한 App Service에서 실행 중인 앱에서 송수신한 요청 및 응답을 모니터링합니다.

보안 센터는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹 앱 공격을 모니터링합니다. 보안 센터는 공격자가 정찰 단계에 있는 동안에도 응용 프로그램에 대한 공격을 검색하고 새로운 공격을 식별할 수 있으며, 여러 Azure 호스팅 응용 프로그램에서 취약점을 식별하기 위해 검색할 수 있습니다. Azure 네이티브 서비스인 보안 센터는 이 PaaS의 기본 계산 노드를 포함하는 호스트 기반 보안 분석을 제공하여 보안 센터가 이미 악용된 웹 응용 프로그램에 대한 공격을 탐지할 수 있도록 하는 고유한 위치에 있습니다. 자세한 내용은 [Azure 앱 서비스에 대한 위협 보호를](threat-protection.md#app-services)참조하십시오.


## <a name="enabling-monitoring-and-protection-of-app-service"></a>App Service 모니터링 및 보호 사용

1. Azure 포털에서 보안 센터를 선택합니다.
2. 가격 **& 설정으로** 이동하여 구독을 선택합니다.
3. **가격 책정 계층** 아래에서 **App service** 행을 선택하고 플랜을 **사용**으로 토글합니다.

    [![표준 계층 구독에서 앱 서비스 사용](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> **리소스 수량에** 대해 나열된 인스턴스 수는 가격 책정 계층 블레이드를 연 순간에 실행되는 이 구독의 모든 앱 서비스 계획에서 총 계산 인스턴스 수를 나타냅니다.
>
> Azure 앱 서비스는 다양한 계획을 제공합니다. 앱 서비스 계획은 실행할 웹 앱의 계산 리소스 집합을 정의합니다. 이는 기존 웹 호스팅의 서버 팜과 동일합니다. 하나 이상의 앱은 동일한 컴퓨팅 리소스(또는 동일한 App Service 계획)에서 실행하도록 구성될 수 있습니다.
>
>개수의 유효성을 검사하려면 Azure Portal의 '앱 서비스 계획'으로 이동하여 각 요금제에서 사용되는 계산 인스턴스 수를 확인할 수 있습니다. 






App Service에 대한 모니터링과 권장 사항을 사용하지 않도록 설정하려면 이 프로세스를 반복하고 **App Service** 플랜을 **사용 안 함**으로 토글합니다.



## <a name="see-also"></a>참조
이 문서에서는 Azure Security Center의 모니터링 기능을 사용하는 방법을 살펴보았습니다. Azure Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) - Azure Security Center에서 보안 설정을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [앱 서비스](security-center-virtual-machine-protection.md#app-services): 상태 요약이 있는 앱 서비스 환경 목록을 봅니다.
* [Azure Security Center에서 파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
