---
title: Azure Front Door - DDoS 보호
description: 이 페이지는 Azure Front Door가 DDoS 공격으로부터 보호하는 방법에 대한 정보를 제공합니다
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96350786"
---
# <a name="ddos-protection-on-front-door"></a>Front Door의 DDoS 보호

Azure Front Door에는 DDoS(배포된 서비스 거부) 공격을 방지하는 데 도움이 될 수 있는 몇 가지 기능과 특징이 있습니다. 해당 기능은 공격자가 애플리케이션에 도달해서 애플리케이션의 가용성 및 성능에 영향을 주는 것을 방지할 수 있습니다.

## <a name="integration-with-azure-ddos-protection-basic"></a>Azure DDoS Protection Basic과 통합

Front Door은 Azure DDoS Protection Basic으로 보호됩니다. 이 기능은 기본값으로 추가 비용 없이 Azure Front Door 플랫폼에 통합됩니다. Front Door 전역 배포 네트워크의 전체 크기 및 용량은 항상 작동하는 트래픽 모니터링 및 실시간 위험 완화를 통해 일반적인 네트워크 계층 공격을 방어합니다. 또한 기본 DDoS 보호는 퍼블릭 엔드포인트를 대상으로 하는 가장 일반적이고 자주 발생하는 계층 7 DNS 쿼리 플러드(flood)와 계층 3 및 4 대규모 공격에 대해 방어합니다. 또한 이 서비스는 대규모 공격으로부터 Microsoft의 엔터프라이즈 및 소비자 서비스를 보호하는 것으로 입증된 추적 기록을 포함합니다. 자세한 내용은 [Azure DDoS Protection](../security/fundamentals/ddos-best-practices.md)를 참조하세요.

## <a name="protocol-blocking"></a>프로토콜 차단

Front Door는 HTTP 및 HTTPS 프로토콜에 대한 트래픽만 허용하고 알려진 `Host` 헤더를 사용하는 유효한 요청만 처리합니다. 이 동작은 다양한 프로토콜 및 포트에 걸쳐 확산되는 볼륨 공격, DNS 증폭 공격 및 TCP 감염 공격을 비롯한 몇 가지 일반적인 DDoS 공격 유형을 완화하는 데 유용합니다.

## <a name="capacity-absorption"></a>용량 흡수

Front Door는 전역적으로 분산된 대규모 확장 서비스입니다. Microsoft의 대규모 클라우드 제품을 비롯한 많은 고객이 초당 수백, 수천 개의 요청을 수신할 수 있습니다. Front Door는 Azure 네트워크의 가장자리에 있어, 대규모 볼륨 공격을 흡수하고 지리적으로 분리합니다. 이로 인해 악의적인 트래픽이 Azure 네트워크의 가장자리에서 더 이상 이동하지 못하게 할 수 있습니다.

## <a name="caching"></a>캐싱

[Front Door의 캐싱 기능](./front-door-caching.md)을 사용하여 공격에 의해 생성된 대량 트래픽 볼륨의 백 엔드를 보호할 수 있습니다. 캐시된 리소스는 Front Door 에지 노드에서 반환되므로 백 엔드로 전달되지 않습니다. 동적 응답에 대한 짧은 캐시 만료 시간(초 또는 분)도 백 엔드 서비스의 부하를 크게 줄일 수 있습니다. 캐싱 개념 및 패턴에 대한 자세한 내용은 [캐싱 고려 사항](/azure/architecture/best-practices/caching) 및 [캐시 배제 패턴](/azure/architecture/patterns/cache-aside)을 참조하세요.

## <a name="web-application-firewall-waf"></a>WAF(웹 애플리케이션 방화벽)

[WAF(Front Door Web Application Firewall)](../web-application-firewall/afds/afds-overview.md) 를 사용하여 다양한 유형의 공격을 완화할 수 있습니다.

* 관리되는 규칙 집합을 사용하면 다양한 일반적인 공격으로부터 보호를 제공합니다.
* 정의된 지리적 영역 외부 또는 지정 지역 내에서 오는 트래픽은 차단되거나 고정적인 웹 페이지로 리디렉션될 수 있습니다. 자세한 내용은 [지역 필터링](../web-application-firewall/afds/waf-front-door-geo-filtering.md)을 참조하세요.
* 악성으로 식별하는 IP 주소 및 범위는 차단할 수 있습니다.
* 요금 제한을 적용하여 IP 주소가 서비스를 너무 자주 호출하지 않도록 방지할 수 있습니다.
* [사용자 지정 WAF 규칙](../web-application-firewall/afds/waf-front-door-custom-rules.md)을 만들어 알려진 서명이 있는 HTTP 또는 HTTPS 공격을 자동으로 차단 및 속도 제한을 할 수 있습니다.

## <a name="for-further-protection"></a>추가 보호

추가 보호를 요구하는 경우 백 엔드가 배포된 VNet에서 [Azure DDoS Protection Standard](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard)를 사용하도록 설정할 수 있습니다. DDoS Protection Standard 고객은 비용 보호, SLA 보증, 공격 중 즉각적인 도움을 받을 수 있는 DDoS 신속 대응 팀의 전문가 액세스 등의 추가 혜택을 받습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door WAF 프로필](front-door-waf.md)을 구성하는 방법을 알아봅니다. 
- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.