---
title: Azure DDoS Protection 기능
description: Azure DDoS Protection 기능 알아보기
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 133a27d8aef6c9df16ffcabfb4fac6c118665890
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905386"
---
# <a name="azure-ddos-protection-standard-features"></a>Azure DDoS Protection 표준 기능

다음 섹션에서는 Azure DDoS Protection 표준 서비스의 주요 기능을 간략하게 설명합니다.

## <a name="always-on-traffic-monitoring"></a>상시 트래픽 모니터링

DDoS Protection 표준은 실제 트래픽 사용률을 모니터링하고 이를 DDoS 정책에 정의된 임계값과 지속적으로 비교합니다. 트래픽 임계값이 초과되면 DDoS 완화가 자동으로 시작됩니다. 트래픽이 임계값 아래로 돌아오면 완화가 제거됩니다.

![Azure DDoS Protection 표준 완화](./media/ddos-protection-overview/mitigation.png)

완화 중에 보호되는 리소스로 보낸 트래픽은 DDoS Protection 서비스를 통해 리디렉션되며 다음과 같은 여러 검사가 수행됩니다.

- 패킷이 인터넷 사양을 준수하고 잘못된 형식이 아닌지 확인합니다.
- 클라이언트와 상호 작용하여 트래픽이 스푸핑된 패킷일 가능성이 있는지 확인합니다(예: SYN Auth 또는 SYN 쿠키 또는 원본에서 다시 전송하도록 패킷 삭제).
- 다른 적용 방법을 수행할 수 없는 경우 패킷 속도를 제한합니다.

DDoS 보호는 공격 트래픽을 차단하고, 나머지 트래픽을 의도하는 대상으로 전달합니다. 공격이 감지되면 몇 분 안에 Azure Monitor 메트릭을 통해 알림이 전송됩니다. DDoS Protection 표준 원격 분석에 대한 로깅을 구성하면 추후 분석 시 사용할 수 있는 옵션에 로그를 작성할 수 있습니다. DDoS Protection 표준을 위한 Azure Monitor의 메트릭 데이터는 30일 동안 보존됩니다.

## <a name="adaptive-real-time-tuning"></a>적응 실시간 튜닝

Azure DDoS Protection Basic 서비스를 통해 고객을 보호하고 다른 고객에 영향을 주지 않도록 방지합니다. 예를 들어 인프라 수준 DDoS Protection 정책의 *트리거 비율* 보다 작고 합법적인 수신 트래픽의 일반 볼륨에 대해 서비스가 프로비전되면 해당 고객의 리소스에 대한 DDoS 공격을 알아차리지 못할 수 있습니다. 뿐만 아니라 최근 공격(예: 다중 벡터 DDoS) 및 테넌트의 애플리케이션별 동작이 더욱 복잡해지면서 고객별로 사용자 지정된 보호 정책의 필요성이 대두되었습니다. 서비스에서는 두 개의 정보를 사용하여 이 사용자 지정을 수행합니다.

- 레이어 3 및 4의 고객별(IP별) 트래픽 패턴 자동 학습

- Azure의 크기 조정을 통해 트래픽의 상당 부분을 흡수할 수 있음을 고려하여 거짓 긍정 최소화

!["정책 생성"을 원으로 표시한 DDoS Protection 표준 작동 방법 다이어그램](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection 원격 분석, 모니터링 및 경고

DDoS Protection 표준은 DDoS 공격이 진행되는 동안 [Azure Monitor](/azure/azure-monitor/overview)를 통해 풍부한 원격 분석을 노출합니다. DDoS Protection에서 사용하는 Azure Monitor 메트릭 중 하나에 대한 경고를 구성할 수 있습니다. Azure Monitor 진단 인터페이스를 통해 고급 분석을 위해 Splunk (Azure Event Hubs), Azure Monitor 로그 및 Azure Storage와 로깅을 통합할 수 있습니다.

### <a name="ddos-mitigation-policies"></a>DDoS 완화 정책

Azure Portal에서 **모니터링**  >  **메트릭** 을 선택 합니다. **메트릭** 창에서 리소스 그룹, **공용 IP 주소** 의 리소스 유형, Azure 공용 IP 주소를 차례로 선택합니다. **사용 가능한 메트릭** 창에 DDoS 메트릭이 표시됩니다.

DDoS Protection 표준은 DDoS를 사용하도록 설정된 가상 네트워크에서 보호되는 리소스의 각 공용 IP에 대해 자동 조정된 세 가지 완화 정책(TCP SYN, TCP 및 UDP)을 적용합니다. **DDoS 완화를 트리거할 인바운드 패킷** 메트릭을 선택하여 정책 임계값을 볼 수 있습니다.

![사용 가능한 메트릭 및 메트릭 차트](./media/ddos-best-practices/image-7.png)

정책 임계값은 기계 학습 기반의 네트워크 트래픽 프로파일링을 통해 자동으로 구성됩니다. 정책 임계값을 초과하면 공격을 받고 있는 IP 주소에 대해 DDoS 완화가 발생합니다.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>DDoS 공격을 받는 IP 주소에 대한 메트릭

공용 IP 주소가 공격을 받으면 DDoS Protection이 공격 트래픽에 대한 완화를 수행하므로 메트릭 **DDoS 공격 진행 여부** 값이 1로 변경됩니다.

!["DDoS 공격 진행 여부" 메트릭 및 차트](./media/ddos-best-practices/image-8.png)

이 메트릭에서 경고를 구성하는 것이 좋습니다. 공용 IP 주소에서 활성 DDoS 완화가 수행되면 알림을 받게 됩니다.

자세한 내용은 [Azure Portal을 사용하여 Azure DDoS Protection Standard 관리](manage-ddos-protection.md)를 참조하세요.

## <a name="web-application-firewall-for-resource-attacks"></a>리소스 공격에 대한 웹 애플리케이션 방화벽

애플리케이션 계층에서 이루어지는 리소스 공격과 관련하여 웹 애플리케이션을 보호할 수 있도록 WAF(웹 애플리케이션 방화벽)를 구성해야 합니다. WAF는 인바운드 웹 트래픽을 검사하여 SQL 주입, 사이트 간 스크립팅, DDoS 및 기타 레이어 7 공격을 차단합니다. Azure는 [WAF(웹 애플리케이션 방화벽)](/azure/application-gateway/application-gateway-web-application-firewall-overview)는 일반적인 악용 및 취약점으로부터 웹 애플리케이션을 중앙 집중식으로 보호하는 Application Gateway 기능으로 WAF를 제공합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)에는 Azure 파트너가 제공하는 기타 WAF가 있으며, 그 중에는 고객의 요구 사항에 보다 적합한 서비스가 있을 수도 있습니다.

웹 애플리케이션 방화벽조차 대규모 공격 및 상태 고갈 공격에 취약합니다. 대규모 공격 및 프로토콜 공격으로부터 보호할 수 있도록 WAF 가상 네트워크에서 DDoS Protection 표준을 사용하는 것이 좋습니다. 자세한 내용은 [DDoS Protection 참조 아키텍처](ddos-protection-reference-architectures.md) 섹션을 참조하세요.

## <a name="protection-planning"></a>보호 계획

계획 및 준비는 DDoS 공격을 받는 동안 시스템이 어떻게 작동하는지 이해하는 데 매우 중요합니다. 인시던트 관리 응답 계획을 디자인하는 작업이 이 활동의 일부입니다.

DDoS Protection 표준이 설치된 경우 인터넷 연결 엔드포인트의 가상 네트워크에서 사용하도록 설정해야 합니다. DDoS 경고를 구성하면 인프라에 대한 잠재적 공격을 지속적으로 감시할 수 있습니다. 

응용 프로그램을 독립적으로 모니터링 합니다. 애플리케이션의 정상 동작을 이해해야 합니다. DDoS 공격을 받는 동안 애플리케이션이 예상대로 작동하지 않을 경우를 준비합니다. 

[시뮬레이션을 통해 테스트](test-through-simulations.md)하 여 서비스에서 공격에 대응 하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

- [DDoS 보호 계획을 만드는](manage-ddos-protection.md)방법에 대해 알아봅니다.