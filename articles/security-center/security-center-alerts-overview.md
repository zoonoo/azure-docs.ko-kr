---
title: Azure Security Center의 보안 경고 | Microsoft Docs
description: 이 토픽에서는 보안 경고가 무엇인지와, Azure Security Center에서 사용할 수 있는 다양한 유형의 보안 경고에 대해 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: memildin
ms.openlocfilehash: bd52cb9ba10cbbc7823cf631cea146cdccdad4e1
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89278142"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure Security Center의 보안 경고

Azure Security Center에는 다양한 리소스 유형에 대한 광범위한 경고가 있습니다. Security Center는 Azure에 배포된 리소스와, 온-프레미스 및 하이브리드 클라우드 환경에 배포된 리소스에 대해 경고를 생성합니다.

보안 경고는 고급 검색에 의해 트리거되고 Azure Security Center 표준 계층 에서만 사용할 수 있습니다. 평가판을 사용할 수 있습니다. **가격 책정 & 설정** 페이지에서 업그레이드할 수 있습니다. [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)에 대해 자세히 알아보세요.

## <a name="respond-to-todays-threats"></a>오늘 위협 <a name="respond-threats"> </a> 에 대응

지난 20년 동안 위협 환경에 중요한 변경 내용이 있었습니다. 과거에 회사는 일반적으로 "자신의 능력"을 확인하는 데 주로 관심이 있던 개별 공격자에 의한 웹 사이트 파손에 대해서만 걱정했습니다. 오늘날의 공격자는 훨씬 더 정교하며 조직적입니다. 특정 금융 및 전략적 목표가 있는 경우가 많습니다. 또한 국가 또는 조직 범죄로 자금 지원을 받으므로 더 많은 사용 가능한 리소스가 있습니다.

이렇게 변화하는 현실로 인해 공격자가 전에 없이 전문적이 수준이 되었습니다. 공격자는 더 이상 웹 손상에 관심이 없습니다. 이제는 오픈 마켓에서 현금을 생성하거나 특정 비즈니스, 정치적 또는 군사 위치를 활용하는 데 사용할 수 있는 정보, 재무 계정 및 프라이빗 데이터를 도용하는 데 관심이 있습니다. 재무 목표를 가진 공격자보다 더욱 걱정되는 것은 인프라 및 사용자에게 해를 입히기 위해 네트워크를 위반하는 공격자입니다.

이에 대한 응답으로 조직은 종종 알려진 공격 서명을 검색하여 엔터프라이즈 경계 또는 엔드포인트 방어에 집중하는 다양한 지점 솔루션을 배포합니다. 이러한 솔루션은 보안 분석가에게 심사 및 조사를 요구하는 대용량의 낮은 신뢰 경고를 생성하는 경향이 있습니다. 대부분의 조직은 이러한 경고에 응답하는 데 필요한 시간 및 전문 지식이 부족하므로 많은 경고가 해결되지 않습니다.  

또한 공격자는 많은 서명 기반 방어를 무너뜨리고 [클라우드 환경에 적응](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/)하기 위해 메서드를 발전시켜 왔습니다. 새로운 위협 요소를 보다 신속하게 식별하고 감지 및 대응을 신속하게 처리하기 위해 새로운 접근 방식이 필요합니다.

## <a name="what-are-security-alerts-and-security-incidents"></a>보안 경고 및 보안 인시던트란 무엇인가요? 

**경고**는 리소스에 대한 위협을 검색할 때 Security Center가 생성하는 알림입니다. Security Center는 사용자가 신속하게 문제를 조사하기 위해 필요한 정보와 함께 경고에 우선 순위를 정하여 나열합니다. Security Center는 공격을 해결하는 방법에 대한 권장 사항도 제공합니다.

**보안 인시던트**는 각 경고를 개별적으로 나열하는 것이 아닌 관련 경고의 컬렉션입니다. Security Center는 [클라우드 스마트 경고 상관 관계](security-center-alerts-cloud-smart.md)를 사용하여 다양한 경고와 불특정한 신호에서 보안 인시던트와의 상관 관계를 확인합니다.

Security Center는 인시던트를 통해 사용자에게 공격 행위와 모든 관련 경고의 단일 보기를 제공합니다. 이 보기에서 공격자의 행위와 영향을 받는 리소스를 신속히 이해할 수 있습니다. 자세한 내용은 [클라우드 스마트 경고상관 관계](security-center-alerts-cloud-smart.md)를 참조하세요.



## <a name="how-does-security-center-detect-threats"></a>Security Center는 위협을 어떻게 검색하나요? <a name="detect-threats"> </a>

Microsoft 보안 연구원은 지속적으로 위협을 지켜보고 있습니다. 클라우드 및 온-프레미스를 망라하는 Microsoft의 글로벌 입지 덕분에 포괄적인 원격 분석 집합에 액세스할 수 있습니다. 이 광범위하고 다양한 데이터 세트의 컬렉션을 통해 온라인 서비스뿐 아니라 해당 온-프레미스 소비자 및 엔터프라이즈 제품에서도 새로운 공격 패턴 및 추세를 검색할 수 있습니다. 결과적으로 보안 센터는 공격자가 새롭고 더욱 정교한 악용을 릴리스하는 동안 신속하게 해당 감지 알고리즘을 업데이트할 수 있습니다. 이 방법을 통해 빠르게 움직이는 위협 환경을 지속적으로 관리할 수 있습니다.

실제 위혐을 검색하고 가양성을 줄이기 위해 Security Center는 Azure 리소스 및 네트워크에서 로그 데이터를 수집, 분석 및 취합합니다. 또한 방화벽 및 엔드포인트 보호 솔루션처럼 연결된 파트너 솔루션과도 연동합니다. Security Center는 위협 식별을 위해 종종 여러 원본의 정보를 상호 연결하여 이 정보를 분석합니다.

![보안 센터 데이터 수집 및 프레젠테이션](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

보안 센터는 서명 기반 방식을 뛰어 넘는 고급 보안 분석을 사용합니다. 수동 접근 방법을 사용하고 공격의 발전을 예측하여 식별할 수 없는 위협을 감지하도록 전체 클라우드 패브릭에 대한 이벤트를 평가하는 데 빅 데이터 및 [기계 학습](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) 기술의 돌파구를 활용합니다. 이러한 보안 분석은 다음과 같습니다.

* **통합된 위협 인텔리전스**: Microsoft는 방대한 글로벌 위협 인텔리전스가 있습니다. 원격 분석은 Azure, Microsoft 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft DCU (Digital Crimes Unit) 및 MSRC (Microsoft 보안 대응 센터)와 같은 여러 원본에서 흐릅니다. 연구소에서는 주요 클라우드 서비스 공급자 간에 공유하는 인텔리전스 정보와 타사의 피드도 받습니다. Azure Security Center는 이 정보를 사용하여 알려진 부정 행위자의 위협을 경고할 수 있습니다.

* **동작 분석**: 동작 분석은 알려진 패턴의 컬렉션에 대해 데이터를 분석하고 비교하는 기술입니다. 그러나 이러한 패턴은 단순한 서명이 아닙니다. 대량 데이터 세트에 적용되는 복잡한 기계 학습 알고리즘을 통해 결정됩니다. 또한 전문 분석가가 악의적인 행동을 신중하게 분석하여 결정합니다. Azure Security Center는 동작 분석을 사용하여 가상 머신 로그, 가상 네트워크 디바이스 로그, 패브릭 로그 및 기타 원본에 대한 분석에 따라 손상된 리소스를 식별할 수 있습니다.

* **변칙 탐지**: Azure Security Center는 이상 감지를 사용하여 위협을 식별합니다. 행위 분석(큰 데이터 세트에서 파생된 알려진 패턴에 따라 결정)과 달리 변칙 검색은 더욱 "막춤형"으로 되고 배포에만 적용되는 기준에 중점을 둡니다. 배포에 대한 정상적인 작동을 확인하기 위해 기계 학습이 적용되고 보안 이벤트를 표시할 수 있는 이상값 조건을 정의하는 규칙이 생성됩니다.

## <a name="how-are-alerts-classified"></a>경고는 어떻게 분류되나요?

Security Center는 사용자가 각각의 경고에 주목하는 우선 순위를 정하는 데 도움이 되도록 경고에 심각도를 할당하므로, 리소스가 손상되면 즉시 해당 내용을 파악할 수 있습니다. 심각도는 경고를 실행하는 데 사용된 Security Center의 검색 또는 분석의 신뢰도와 악의적인 의도로 경고를 발생시킨 활동의 신뢰도 수준을 기준으로 합니다.

> [!NOTE]
> 경고 심각도는 포털과, 01-01-2019 이전 REST API 버전에서 다르게 표시됩니다. 이전 버전의 API를 사용할 경우 아래에서 설명한 대로 일관된 환경을 위해 업그레이드하세요.

- **높음:** 리소스가 손상될 가능성이 높습니다. 지금 즉시 리소스를 살펴보아야 합니다. Security Center는 경고 실행에 사용되는 악의적 의도와 결과 둘 다에서 신뢰성이 높습니다. 자격 증명 훔치기에 많이 사용되는 Mimikatz처럼 알려진 악의적 도구 실행을 검색하는 경고를 예로 들 수 있습니다.
- **보통:** 리소스의 손상 가능성을 나타내는 의심스러운 활동입니다.
Security Center의 분석 또는 결과 신뢰도는 보통이며 악의적 의도의 신뢰도는 보통부터 높음 사이입니다. 일반적으로 기계 학습 또는 변칙 기반 검색입니다. 비정상적인 위치에서의 로그인 시도를 예로 들 수 있습니다.
- **낮음:** 무해한 양성 또는 차단된 공격일 수 있습니다.
   * Security Center는 신뢰도가 높지 않기 때문에 의도가 악의적이고 작업은 악의적이지 않을 수 있습니다. 예를 들어 로그 지우기는 공격자가 흔적을 숨기려 시도할 때 발생할 수도 있지만, 대부분은 관리자가 수행하는 일상적인 작업입니다.
   * Security Center는 일반적으로 공격을 차단하더라도 살펴볼 가치가 있는 흥미로운 사례가 아닌 경우에는 사용자에게 알리지 않습니다. 
- **정보 제공:** 보안 인시던트로 드릴다운하거나 특정 경고 ID가 있는 REST API를 사용하는 경우 정보 경고만 표시됩니다. 일반적으로 한 인시던트는 여러 경고로 구성되며, 그 중 일부는 오직 정보 제공만을 위해 단독으로 표시될 수 있지만 다른 경고의 컨텍스트에서는 자세히 살펴볼 필요가 있습니다. 

## <a name="continuous-monitoring-and-assessments"></a>연속 모니터링 및 평가

Azure Security Center는 위협 환경의 변화를 지속적으로 모니터링하는 보안 연구 및 데이터 과학 팀을 활용하고 있습니다. 다음 이니셔티브가 포함됩니다.

* **위협 인텔리전스 모니터링**: 위협 인텔리전스에는 기존 또는 새로운 위협에 대한 메커니즘, 표시기, 영향 및 조치 가능한 조언이 포함됩니다. 이 정보는 보안 커뮤니티에서 공유되고 Microsoft는 내부 및 외부 소스에서 위협 인텔리전스 피드를 지속적으로 모니터링합니다.
* **신호 공유**: Microsoft의 클라우드 및 온-프레미스 서비스, 서버 및 클라이언트 엔드포인트 디바이스의 광범위한 포트폴리오에 대한 보안 팀의 인사이트를 공유하고 분석합니다.
* **Microsoft 보안 전문가**: 법정 분석 및 웹 공격 탐지와 같은 전문 보안 분야에서 Microsoft 팀과 지속적인 관계를 유지하며 활동합니다.
* **탐지 튜닝**: 알고리즘은 실제 고객 데이터 세트에 대해 실행되며, 보안 연구원은 고객과 협력하여 결과의 유효성을 검사합니다. 기계 학습 알고리즘을 구체화하기 위해 참 및 거짓 긍정이 사용됩니다.

이러한 결합된 노력은 즉시 활용할 수 있는 새롭고 향상된 감지에 누적됩니다. 수행해야 할 작업이 없습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center에서 제공하는 다양한 보안 경고 유형에 대해 알아보았습니다. 자세한 내용은 다음을 참조하세요.

* [Azure Security Center의 위협 방지](threat-protection.md) - Azure Security Center에 표시되는 보안 경고의 원본에 대한 간단한 설명입니다. 
* **Azure 활동 로그의 보안 경고** - 보안 경고와 인시던트는 Azure Portal에서 또는 프로그래밍 방식으로 사용할 수 있을 뿐만 아니라 [Azure 활동 로그](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)에서 이벤트로 감사됩니다. 이벤트 스키마에 대한 자세한 내용은 [Azure 활동 로그의 보안 경고](https://go.microsoft.com/fwlink/?linkid=2114113)를 참조하세요.

