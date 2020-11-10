---
title: Azure 전면 도어-DDoS 보호
description: 이 페이지는 Azure Front 도어가 DDoS 공격 으로부터 보호 하는 방법에 대 한 정보를 제공 합니다.
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
ms.openlocfilehash: 58efeaebcc9f643c725cde54fcbda6f65c4bd700
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94415167"
---
# <a name="ddos-protection-on-front-door"></a>DDoS protection on Front 도어

Azure Front 문에는 DDoS (분산 서비스 거부) 공격을 방지 하는 데 도움이 될 수 있는 몇 가지 기능과 특징이 있습니다. 이러한 기능을 통해 공격자는 응용 프로그램에 도달 하지 못하게 하 고 응용 프로그램의 가용성 및 성능에 영향을 줄 수 있습니다.

## <a name="integration-with-azure-ddos-protection-basic"></a>Azure DDoS Protection Basic과 통합

전면 도어는 Azure DDoS Protection Basic에 의해 보호 됩니다. 이는 기본적으로 추가 비용 없이 전면 도어 플랫폼에 통합 됩니다. 프런트 도어의 전 세계적으로 배포 된 네트워크의 전체 크기 및 용량은 상시 트래픽 모니터링과 실시간 완화를 통해 일반적인 네트워크 계층 공격에 대 한 방어를 제공 합니다. 또한 기본 DDoS 보호는 공용 끝점을 대상으로 하는 가장 일반적인, 자주 발생 하는 계층 7 DNS 쿼리 및 계층 3 및 4 대규모 공격에 대해 방어 합니다. 또한이 서비스는 대규모 공격 으로부터 Microsoft의 엔터프라이즈 및 소비자 서비스를 보호 하는 것으로 입증 된 추적 레코드를 포함 합니다. 자세한 내용은 [Azure DDoS Protection](../security/fundamentals/ddos-best-practices.md)를 참조 하세요.

## <a name="protocol-blocking"></a>프로토콜 차단

전면 도어는 HTTP 및 HTTPS 프로토콜에 대 한 트래픽만 허용 하 고 알려진 헤더를 사용 하는 유효한 요청만 처리 합니다 `Host` . 이 동작을 통해 다양 한 프로토콜 및 포트, DNS 증폭 공격 및 TCP 감염 공격에 걸쳐 분산 된 대규모 공격을 비롯 한 몇 가지 일반적인 DDoS 공격 유형을 완화할 수 있습니다.

## <a name="capacity-absorption"></a>용량 absorption

전면 도어는 전역적으로 분산 된 대규모 확장 서비스입니다. Microsoft의 대규모 클라우드 제품을 비롯 한 많은 고객이 초당 수백 개의 요청을 받을 수 있습니다. 전면 도어는 Azure 네트워크의 가장자리에 있으며, 흡수 하 고 지리적으로 대규모 볼륨 공격을 분리 합니다. 이로 인해 악의적인 트래픽이 Azure 네트워크의 가장자리 보다 더 이상 이동 하지 못할 수 있습니다.

## <a name="caching"></a>캐싱

[전면 도어의 캐싱 기능](./front-door-caching.md) 을 사용 하 여 공격에 의해 생성 된 대량 트래픽 볼륨의 백 엔드를 보호할 수 있습니다. 캐시 된 리소스는 프런트 도어에 지 노드에서 반환 되므로 백 엔드로 전달 되지 않습니다. 동적 응답에 대 한 짧은 캐시 만료 시간 (초 또는 분)도 백 엔드 서비스의 부하를 크게 줄일 수 있습니다. 캐싱 개념 및 패턴에 대 한 자세한 내용은 [캐싱 고려 사항](https://docs.microsoft.com/azure/architecture/best-practices/caching) 및 [캐시 배제 패턴](https://docs.microsoft.com/azure/architecture/patterns/cache-aside)을 참조 하세요.

## <a name="web-application-firewall-waf"></a>WAF(웹 애플리케이션 방화벽)

[프런트 도어의 WAF (웹 응용 프로그램 방화벽)](../web-application-firewall/afds/afds-overview.md) 를 사용 하 여 다양 한 유형의 공격을 완화할 수 있습니다.

* 관리 되는 규칙 집합을 사용 하면 여러 가지 일반적인 공격 으로부터 보호를 제공 합니다.
* 정의 된 지역 외부 또는 정의 된 지역 내에서 트래픽을 차단 하거나 정적 웹 페이지로 리디렉션할 수 있습니다. 자세한 내용은 [지역 필터링](../web-application-firewall/afds/waf-front-door-geo-filtering.md)을 참조 하세요.
* 악성으로 식별 하는 IP 주소 및 범위는 차단할 수 있습니다.
* 요금 제한을 적용 하 여 IP 주소가 서비스를 너무 자주 호출 하지 않도록 방지할 수 있습니다.
* [사용자 지정 WAF 규칙](../web-application-firewall/afds/waf-front-door-custom-rules.md) 을 만들어 알려진 서명이 있는 HTTP 또는 HTTPS 공격을 자동으로 차단 및 비율로 제한할 수 있습니다.

## <a name="for-further-protection"></a>추가 보호

추가 보호를 요구 하는 경우 백 엔드가 배포 된 VNet에서 [Azure DDoS Protection Standard](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) 를 사용 하도록 설정할 수 있습니다. DDoS Protection 표준 고객은 비용 보호, SLA 보증, 공격 중에 즉각적인 도움을 받을 수 있는 DDoS 신속한 대응 팀의 전문가에 게 액세스 등의 추가 혜택을 받습니다.

## <a name="next-steps"></a>다음 단계

- [전면 도어에서 Waf 프로필](front-door-waf.md)을 구성 하는 방법에 대해 알아봅니다. 
- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
