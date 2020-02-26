---
title: Azure App Service 웹 앱 및 Api 보호
description: 이 문서는 Azure Security Center에서 Azure App Service 웹 앱 및 Api를 보호 하기 시작 하는 데 도움이 됩니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: c286efcfad78a8f7acf9974605d53ed3cc06a09e
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604539"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Azure App Service 웹 앱 및 Api 보호

Azure App Service는 인프라를 관리할 필요 없이 웹 앱 및 Api를 빌드하고 호스팅하기 위한 완전히 관리 되는 플랫폼입니다. 엔터프라이즈 수준의 성능, 보안 및 규정 준수 요구 사항을 충족 하는 관리, 모니터링 및 operational insights를 제공 합니다. 자세한 내용은 [Azure App Service](https://azure.microsoft.com/services/app-service/)를 참조 하세요.

Azure App Service 계획에 대 한 고급 위협 방지를 사용 하도록 설정 하려면 다음을 수행 해야 합니다.

* Azure Security Center의 표준 가격 책정 계층을 구독 합니다.
* 아래와 같이 App Service 계획을 사용 하도록 설정 합니다. Security Center은 기본적으로 App Service와 통합 되어 배포 및 등록에 대 한 필요성을 제거 합니다. 통합은 투명 합니다.
* 전용 컴퓨터와 연결 된 App Service 요금제가 있어야 합니다. 지원 되는 요금제는 Basic, Standard, Premium, Isolated 또는 Linux입니다. Security Center은 무료, 공유 또는 소비 계획을 지원 하지 않습니다. 자세한 내용은 [App Service 플랜](https://azure.microsoft.com/pricing/details/app-service/plans/)을 참조하세요.

App Service 계획을 사용 하도록 설정 하면 Security Center App Service 계획에 포함 된 리소스를 평가 하 고 해당 결과에 따라 보안 권장 사항을 생성 합니다. Security Center App Service를 실행 하는 VM 인스턴스와 관리 인터페이스를 보호 합니다. 또한 App Service에서 실행 중인 앱에서 송수신한 요청 및 응답을 모니터링합니다.

Security Center는 클라우드의 규모와 Azure에서 클라우드 공급자로의 표시 유형을 활용 하 여 일반적인 웹 앱 공격을 모니터링 합니다. 공격자가 정찰 단계에 있는 동안에도 응용 프로그램에 대 한 공격을 검색 하 고 새로운 공격을 식별할 수 Security Center. Azure native Security Center service로 서는이 PaaS의 기본 계산 노드를 포함 하는 호스트 기반 보안 분석을 제공 하는 고유한 위치에도 있으므로 이미 악용 된 웹 응용 프로그램에 대 한 공격을 검색할 Security Center 있습니다. Azure App Service의 위협 검색 경고 Security Center에 대 한 자세한 내용은 [클라우드 네이티브 컴퓨팅에 대 한 위협 감지](security-center-alerts-compute.md#azure-app-service-)를 참조 하세요.


## <a name="enabling-monitoring-and-protection-of-app-service"></a>App Service 모니터링 및 보호 사용

1. Azure Portal에서 Security Center를 선택 합니다.
2. **가격 책정 & 설정** 으로 이동 하 여 구독을 선택 합니다.
3. **가격 책정 계층** 아래에서 **App service** 행을 선택하고 플랜을 **사용**으로 토글합니다.

    [표준 계층 구독에서 app services를 사용 하도록 설정 ![](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> **리소스 수량** 에 대해 나열 되는 인스턴스 수는 가격 책정 계층 블레이드를 열 때 실행 되는 현재 구독에 대 한 모든 App Service 계획의 총 계산 인스턴스 수를 나타냅니다.
>
> Azure App Service는 다양 한 계획을 제공 합니다. App Service 계획은 실행할 웹 앱에 대 한 계산 리소스 집합을 정의 합니다. 이는 기존 웹 호스팅의 서버 팜과 동일 합니다. 하나 이상의 앱은 동일한 컴퓨팅 리소스(또는 동일한 App Service 계획)에서 실행하도록 구성될 수 있습니다.
>
>카운트의 유효성을 검사 하려면 Azure Portal에서 ' App Service 요금제 '로 이동 합니다. 여기서 각 계획에 사용 되는 계산 인스턴스 수를 확인할 수 있습니다. 






App Service에 대한 모니터링과 권장 사항을 사용하지 않도록 설정하려면 이 프로세스를 반복하고 **App Service** 플랜을 **사용 안 함**으로 토글합니다.



## <a name="see-also"></a>참고 항목
이 문서에서는 Azure Security Center의 모니터링 기능을 사용하는 방법을 살펴보았습니다. Azure Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) - Azure Security Center에서 보안 설정을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [앱 서비스](security-center-virtual-machine-protection.md#app-services): 상태 요약이 포함 된 app service environment의 목록을 봅니다.
* [Azure Security Center에서 파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
