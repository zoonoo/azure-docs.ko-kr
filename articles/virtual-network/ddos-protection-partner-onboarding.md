---
title: Azure DDoS 보호 표준과의 파트너십
description: Azure DDoS 보호 표준에서 사용할 수 있는 파트너 지정 기회를 이해합니다.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849683"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Azure DDoS 보호 표준과의 파트너십
이 문서에서는 Azure DDoS 보호 표준에서 사용할 수 있는 파트너 지정 기회에 대해 설명합니다. 이 문서는 제품 관리자와 비즈니스 개발 역할이 투자 경로를 이해하고 파트너 가치 제안에 대한 통찰력을 제공할 수 있도록 설계되었습니다.

## <a name="background"></a>배경
분산 서비스 거부(DDoS) 공격은 애플리케이션을 클라우드로 이동하는 고객이 가장 많이 사용하는 가용성 및 보안 문제 중 하나입니다. 강탈과 핵티비즘이 DDoS 공격의 일반적인 동기가 됨에 따라 비교적 쉽고 저렴하게 출시할 수 있기 때문에 유형, 규모 및 발생 빈도가 지속적으로 증가하고 있습니다.

Azure DDoS 보호는 Azure 네트워킹의 글로벌 규모를 활용하여 가장 정교한 DDoS 위협에 대한 대책을 제공합니다. 이 서비스는 가상 네트워크에 배포된 응용 프로그램 및 리소스에 대한 향상된 DDoS 완화 기능을 제공합니다.

기술 파트너는 Azure DDoS 보호 표준을 사용하여 기본적으로 고객의 리소스를 보호하여 DDoS 공격으로 인한 가용성 및 안정성 문제를 해결할 수 있습니다.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS 보호 표준 소개
Azure DDoS 보호 표준은 계층 3 및 계층 4 DDoS 공격에 대해 향상된 DDoS 완화 기능을 제공합니다. 다음은 DDoS 보호 표준 서비스의 주요 기능입니다.

### <a name="adaptive-real-time-tuning"></a>적응형 실시간 조정
보호된 모든 응용 프로그램에 대해 Azure DDoS 보호 표준은 응용 프로그램의 트래픽 프로필 패턴에 따라 DDoS 완화 정책 임계값을 자동으로 조정합니다. 서비스에서는 두 개의 정보를 사용하여 이 사용자 지정을 수행합니다.

- 레이어 3 및 4의 고객별(IP별) 트래픽 패턴 자동 학습
- Azure의 크기 조정을 통해 트래픽의 상당 부분을 흡수할 수 있음을 고려하여 거짓 긍정 최소화

![적응형 실시간 튜닝](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>공격 분석, 원격 분석, 모니터링 및 경고
Azure DDoS 보호는 사용자 개입 없이 DDoS 공격을 식별하고 완화합니다.

- 보호된 리소스가 Azure 보안 센터에서 적용되는 구독에 있는 경우 DDoS 보호 표준은 보호된 응용 프로그램에 대해 DDoS 공격이 감지되고 완화될 때마다 보안 센터에 경고를 자동으로 보냅니다.
- 또는 보호된 공용 IP에 대한 활성 완화가 있을 때 알림을 받으려면 DDoS 공격 중 메트릭에 대한 [경고를 구성할](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) 수 있습니다.
- 또한 다른 DDoS 메트릭에 대한 경고를 만들고 [공격 규모,](manage-ddos-protection.md#configure-ddos-attack-analytics) 삭제되는 트래픽, 공격 벡터, 상위 참여자 및 기타 세부 정보를 이해하기 위해 공격 분석을 구성할 수 있습니다.

![DDoS 메트릭](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS 신속 응답(DRR)
DDoS 보호 표준 고객은 적극적인 공격 중에 [신속한 대응 팀에](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) 액세스할 수 있습니다. DRR은 공격 조사, 공격 중 사용자 지정 완화 및 공격 후 분석에 도움이 될 수 있습니다.

### <a name="sla-guarantee-and-cost-protection"></a>SLA 보증 및 비용 보호
DDoS 보호 표준 서비스는 99.99%의 SLA를 적용하며, 비용 보호는 문서화된 공격 중에 확장할 수 있는 리소스 크레딧을 제공합니다. 자세한 내용은 [Azure DDoS 보호에 대한 SLA를](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)참조하십시오.

## <a name="featured-partner-scenarios"></a>주요 파트너 시나리오
다음은 Azure DDoS 보호 표준과 통합하여 얻을 수 있는 주요 이점입니다.

- 고객에게 제공되는 파트너의 서비스(로드 밸런서, 웹 응용 프로그램 방화벽, 방화벽 등)는 백 엔드의 Azure DDoS 보호 표준에 의해 자동으로 보호됩니다(화이트 라벨).
- 파트너는 Azure DDoS 보호 표준 공격 분석 및 원격 분석에 액세스하여 자체 제품과 통합하여 통합된 고객 환경을 제공합니다.  
- 파트너는 DDoS 관련 문제에 대해 Azure 신속한 응답이 없는 경우에도 DDoS 신속한 응답 지원에 액세스할 수 있습니다.
- 파트너의 보호된 애플리케이션은 DDoS 공격 시 DDoS SLA 보증 및 비용 보호를 통해 지원됩니다.

## <a name="technical-integration-overview"></a>기술 통합 개요
Azure DDoS 보호 표준 파트너 지정 기회는 Azure 포털, API 및 CLI/PS를 통해 사용할 수 있습니다.

### <a name="integrate-with-ddos-protection-standard"></a>DDoS 보호 표준과 통합
파트너가 Azure DDoS 보호 표준과의 통합을 구성하려면 다음 단계가 필요합니다.
1. 원하는 파트너 구독에서 DDoS 보호 계획을 만듭니다. 단계별 지침은 [DDoS 표준 보호 계획 만들기를](manage-ddos-protection.md#create-a-ddos-protection-plan)참조하십시오.
   > [!NOTE]
   > 지정된 테넌트에 대해 1개의 DDoS 보호 계획만 만들어야 합니다. 
2. 로드 밸런서, 방화벽 및 웹 응용 프로그램 방화벽과 같은 (파트너) 구독에 공용 끝점이 있는 서비스를 배포합니다. 
3. 첫 번째 단계에서 만든 DDoS 보호 계획을 사용하여 공용 끝점이 있는 서비스의 가상 네트워크에서 Azure DDoS 보호 표준을 활성화합니다. 단계별 지침은 [DDoS 표준 보호 지원 계획을](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network) 참조하십시오.
   > [!IMPORTANT] 
   > Azure DDoS 보호 표준이 가상 네트워크에서 활성화되면 해당 가상 네트워크 내의 모든 공용 IP가 자동으로 보호됩니다. 이러한 공용 IP의 출처는 Azure(클라이언트 구독) 내에서 또는 Azure 외부에 있을 수 있습니다. 
4. 선택적으로 Azure DDoS 보호 표준 원격 분석 및 공격 분석을 응용 프로그램별 고객 대면 대시보드에 통합합니다. 원격 분석 사용에 대한 자세한 내용은 [DDoS 보호 원격 분석 사용을](manage-ddos-protection.md#use-ddos-protection-telemetry)참조하십시오. 공격 분석 구성에 대한 자세한 내용은 [DDoS 공격 분석 구성을](manage-ddos-protection.md#configure-ddos-attack-analytics) 참조하십시오.

### <a name="onboarding-guides-and-technical-documentation"></a>온보딩 가이드 및 기술 문서

- [Azure DDoS Protection 제품 페이지](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS Protection 설명서](ddos-protection-overview.md)
- [Azure DDoS 보호 API 참조](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure 가상 네트워크 API 참조](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>도움말 보기

- Azure DDoS 보호 표준과의 응용 프로그램, 서비스 또는 제품 통합에 대한 질문이 있는 경우 [Azure 보안 커뮤니티에](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)문의하십시오.
- [스택 오버플로에](https://stackoverflow.com/tags/azure-ddos/)대한 토론을 따르십시오.

### <a name="get-to-market"></a>시장 출시

- 마이크로소프트와 파트너십을 위한 기본 프로그램은 [마이크로소프트 파트너 네트워크.](https://partner.microsoft.com/) – 마이크로 소프트 그래프 보안 통합은 [MPN 독립 소프트웨어 공급 업체 (ISV)](https://partner.microsoft.com/saas-solution-guide) 트랙에 속한다.
- [Microsoft 지능형 보안 협회는](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) Microsoft 보안 파트너를 위한 프로그램으로 Microsoft 보안 제품을 보강하고 Microsoft 보안 제품에 대한 통합에 대한 고객 검색 가능성을 개선하는 데 도움을 줍니다.

## <a name="next-steps"></a>다음 단계
기존 파트너 통합 보기:

- [바라쿠다 WAF-서비스](https://www.barracuda.com/waf-as-a-service)
- [래드웨어의 Azure 클라우드 WAF](https://www.radware.com/resources/microsoft-azure/)
