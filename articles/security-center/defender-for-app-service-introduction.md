---
title: Azure Defender for App Service - 이점 및 특징
description: Azure Defender for App Service의 이점 및 특징에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2ac4f41a36a2a18a65816be5ea74e88d0b364294
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789066"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Azure Defender for App Service 소개

Azure App Service는 웹앱 및 API를 빌드하고 호스팅하기 위한 완전 관리형 플랫폼으로, 사용자가 인프라 관리에 대해 걱정할 필요가 없습니다. 엔터프라이즈급 성능, 보안 및 규정 준수 요구 사항을 충족하는 관리, 모니터링 및 운영 인사이트를 제공합니다. 자세한 내용은 [Azure App Service](https://azure.microsoft.com/services/app-service/)를 참조하세요.

**Azure Defender for App Service** 는 클라우드 규모를 사용하여 App Service를 통해 실행되는 애플리케이션을 대상으로 하는 공격을 식별합니다. 공격자는 웹 애플리케이션을 검색하여 약점을 찾아 악용합니다. 특정 환경으로 라우팅되기 전에 Azure에서 실행되는 애플리케이션에 대한 요청은 여러 게이트웨이를 통과하면서 검사 및 기록됩니다. 그런 다음, 이 데이터는 악용 및 공격자를 식별하고, 나중에 사용할 새 패턴을 학습하는 데 사용됩니다.

Azure에서 클라우드 공급자로 제공하는 표시 유형을 사용하면 Security Center에서 App Service 내부 로그를 분석하여 여러 대상에 대한 공격 방법론을 식별합니다. 예를 들어 방법론에는 광범위한 검사 및 분산 공격이 포함됩니다. 이러한 유형의 공격은 일반적으로 작은 IP 하위 집합에서 발생하며, 여러 호스트에서 비슷한 엔드포인트로 크롤링하는 패턴을 보여 줍니다. 공격은 취약한 페이지 또는 플러그 인을 검색하며, 단일 호스트의 관점에서 식별할 수 없습니다.


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[Azure Defender for App Service](azure-defender.md)는 [가격 책정 페이지](security-center-pricing.md)에 표시된 대로 요금이 청구됩니다.|
|지원되는 App Service 요금제:|![예](./media/icons/yes-icon.png) Basic, Standard, Premium, Isolated 또는 Linux<br>![아니요](./media/icons/no-icon.png) 무료, 공유 또는 소비<br>[App Service 요금제에 대한 자세한 정보](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![아니요](./media/icons/no-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Azure Defender for App Service는 무엇을 보호하나요?

App Service 요금제를 사용하도록 설정하면 Security Center에서 App Service 요금제가 적용되는 리소스를 평가하고 해당 결과에 따라 보안 권장 사항을 생성합니다. Security Center는 App Service가 실행 중인 VM 인스턴스와 관리 인터페이스를 보호합니다. 또한 App Service에서 실행 중인 앱에서 송수신한 요청 및 응답을 모니터링합니다.

Windows 기반 App Service 계획을 실행하는 경우 Security Center에서도 기본 샌드박스 및 VM에 액세스할 수 있습니다. 위에서 언급한 로그 데이터와 함께 인프라는 악순환되는 새 공격에서 고객 컴퓨터의 손상에 이르기까지 모든 것을 알려줄 수 있습니다. 따라서 웹앱이 악용된 후 Security Center를 배포하더라도 지속적인 공격을 탐지할 수 있습니다.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Azure App Service 웹앱 및 API 보호
Azure Defender for App Service를 사용하여 Azure App Service 요금제를 보호하려면 다음을 수행합니다.

- 전용 머신과 연결된 지원되는 App Service 요금제가 있는지 확인합니다. 지원되는 요금제는 위의 [가용성](#availability)에 나열되어 있습니다.

- 구독에서 **Azure Defender** 를 사용하도록 설정합니다. 필요에 따라 [Azure Security Center 가격 책정](security-center-pricing.md)에 설명된 대로 **Azure Defender for App Service** 요금제만 사용하도록 설정할 수 있습니다.

Security Center는 기본적으로 App Service와 통합되므로 배포 및 탑재 필요 없이 자동 통합이 이루어집니다.

>[!NOTE]
> 가격 책정 및 설정 페이지에서 **리소스 수량** 에 대한 여러 인스턴스가 나열됩니다. 이는 가격 책정 계층 페이지를 열 때 실행 중인 현재 구독에 대한 모든 App Service 요금제의 총 컴퓨팅 인스턴스 수를 나타냅니다.
>
> Azure App Service는 다양한 요금제를 제공합니다. App Service 요금제는 실행할 웹앱에 대한 컴퓨팅 리소스 세트를 정의합니다. 이는 기존 웹 호스팅의 서버 팜과 동일합니다. 하나 이상의 앱은 동일한 컴퓨팅 리소스(또는 동일한 App Service 계획)에서 실행하도록 구성될 수 있습니다.
>
>카운트의 유효성을 검사하려면 Azure Portal에서 'App Service 요금제'로 이동합니다. 여기서 각 요금제에 사용되는 컴퓨팅 인스턴스 수를 확인할 수 있습니다. 



## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender for App Service에 대해 알아보았습니다. 

관련 자료는 다음 문서를 참조하세요. 

- Security Center에서 경고를 생성하든 Security Center에서 다른 보안 제품으로부터 경고를 받든 이를 내보낼 수 있습니다. 경고를 Azure Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM, SOAR 또는 IT 서비스 관리 솔루션에 대한 경고 스트리밍](export-to-siem.md)의 지침을 따릅니다.
- Azure App Service 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-azureappserv)를 참조하세요.
- App Service 계획에 대한 자세한 내용은 [App Service 계획](https://azure.microsoft.com/pricing/details/app-service/plans/)을 참조하세요.
- > [!div class="nextstepaction"]
    > [Azure Defender 사용](security-center-pricing.md)