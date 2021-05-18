---
title: DDoS 대응 전략의 구성 요소
description: Azure DDoS Protection Standard를 사용하여 DDoS 공격에 대응하는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: c028185a0753bc4da966ebc56bb792e57cac1f77
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103048"
---
# <a name="components-of-a-ddos-response-strategy"></a>DDoS 대응 전략의 구성 요소

일반적으로 Azure 리소스를 대상으로 하는 DDoS 공격에는 사용자 관점에서 최소한의 개입이 필요합니다. 그래도 DDoS 완화를 인시던트 대응 전략의 일환으로 통합하면 비즈니스 연속성에 미치는 영향을 최소화할 수 있습니다.

## <a name="microsoft-threat-intelligence"></a>Microsoft 위협 인텔리전스

Microsoft는 광범위한 위협 인텔리전스 네트워크를 보유하고 있습니다. 이 네트워크는 Microsoft의 온라인 서비스, Microsoft 파트너 및 인터넷 보안 커뮤니티와의 관계를 지원하는 확장된 보안 커뮤니티의 집단 지식을 사용합니다. 

Microsoft는 중요한 인프라 공급자로써 위협에 대한 조기 경고를 수신합니다. Microsoft는 온라인 서비스 및 글로벌 고객 수준에서 위협 인텔리전스를 수집합니다. Microsoft는 이 위협 인텔리전스를 다시 Azure DDoS Protection 제품에 모두 통합합니다.

또한 Microsoft DCU(Digital Crimes Unit)은 봇네트에 대한 방어 전략을 수행합니다. 봇네트는 일반적인 소스 명령 및 DDoS 공격에 대한 제어입니다.

## <a name="risk-evaluation-of-your-azure-resources"></a>Azure 리소스의 위험 평가

반드시 DDoS 공격의 위험 범위를 지속적으로 파악해야 합니다. 주기적으로 확인:

- 보호가 필요한 새로 공개된 Azure 리소스는 무엇인가요?

- 서비스에 단일 실패 지점이 있나요? 

- 서비스를 격리하여 공격의 영향을 제한하면서도 유효한 고객에게 서비스를 계속 제공하려면 어떻게 해야 할까요?

- DDoS Protection Standard를 사용하도록 설정해야 하는데 설정하지 않은 가상 네트워크가 있나요? 

- 내 서비스가 여러 지역에서 활성화/장애 조치(failover)와 함께 활성화됩니까?

애플리케이션의 정상적인 동작을 이해하고 애플리케이션이 DDoS 공격 중에 예상대로 작동하지 않는 경우 조치를 취할 준비를 하는 것이 중요합니다. 클라이언트 동작을 모방하는 업무상 중요한 애플리케이션에 대해 모니터를 구성하고 관련된 변칙이 검색되면 사용자에게 알립니다. 애플리케이션의 상태에 대한 인사이트를 얻으려면 [모니터링 및 진단 모범 사례](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios)를 참조하세요.

[Azure Application Insights](../azure-monitor/app/app-insights-overview.md)는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. Application Insights를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. 성능 이상을 자동으로 검색합니다. 또한 문제를 진단하고 앱을 사용하여 수행하는 작업을 파악할 수 있는 분석 도구도 포함되어 있습니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

## <a name="customer-ddos-response-team"></a>고객 DDoS 대응 팀

공격에 효과적이고 신속하게 대응하기 위한 핵심 단계는 DDoS 대응 팀을 만드는 것입니다. 조직 내에서 계획 및 실행을 감독할 연락 담당자를 식별합니다. DDoS 대응 팀은 Azure DDoS Protection 표준 서비스를 완전히 이해해야 합니다. 팀이 Microsoft 지원 팀을 포함한 내부 및 외부 고객을 조정하여 공격을 식별하고 줄일 수 있는지 확인합니다. 

서비스 가용성 및 연속성 계획의 일반적인 부분으로 시뮬레이션 연습을 사용하는 것이 좋으며 이러한 연습에는 크기 조정 테스트가 포함되어야 합니다. Azure 퍼블릭 엔드포인트에 대해 DDoS 테스트 트래픽을 시뮬레이션하는 방법에 대해 알아보려면 [시뮬레이션 테스트](test-through-simulations.md)를 참조하세요.

## <a name="alerts-during-an-attack"></a>공격 진행 중 경고

Azure DDoS Protection 표준은 사용자 개입 없이 DDoS 공격을 식별하여 완화합니다. 보호되는 공용 IP에 대한 활성 완화가 발생할 때 알림을 받으려면 [경고를 구성](alerts.md)합니다.

### <a name="when-to-contact-microsoft-support"></a>Microsoft 지원에 문의해야 하는 경우

Azure DDoS Protection Standard 고객은 공격 및 공격 후 분석을 통해 공격 조사에 도움을 주는 DRR(DDoS 신속한 응답) 팀에 액세스할 수 있습니다. DRR 팀의 참여가 필요한 경우를 포함하여 자세한 내용은 [DDoS 신속한 응답](ddos-rapid-response.md)을 참조하세요.

## <a name="post-attack-steps"></a>사후 공격 단계

공격 후에는 항상 사후 조치를 수행하고 필요에 따라 DDoS 대응 전략을 조정하는 것이 좋습니다. 고려 사항은 다음과 같습니다.

- 확장 가능한 아키텍처가 부족하여 서비스 또는 사용자 환경이 중단되었습니까?

- 가장 피해가 컸던 애플리케이션 또는 서비스는 무엇입니까?

- DDoS 대응 전략이 얼마나 효과적이었으며 어떻게 개선할 수 있나요?

DDoS 공격을 받고 있다고 의심 되는 경우 일반 Azure 지원 채널을 통해 에스컬레이션하세요.

## <a name="next-steps"></a>다음 단계

- [DDoS 보호 계획을 생성](manage-ddos-protection.md)하는 방법을 알아봅니다.