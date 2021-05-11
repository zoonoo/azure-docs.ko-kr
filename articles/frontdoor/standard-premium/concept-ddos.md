---
title: 'Azure Front Door: DDoS 보호'
description: 이 페이지에서는 Azure Front Door 표준/프리미엄을 통해 DDoS 공격으로부터 보호하는 방법에 대한 정보를 제공합니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100149"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄(미리 보기)에 대한 DDoS 보호

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure Front Door에는 DDoS(배포된 서비스 거부) 공격을 방지하는 데 도움이 될 수 있는 몇 가지 기능과 특징이 있습니다. 해당 기능은 공격자가 애플리케이션에 도달해서 애플리케이션의 가용성 및 성능에 영향을 주는 것을 방지할 수 있습니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="integration-with-azure-ddos-protection-basic"></a>Azure DDoS Protection Basic과 통합

Front Door은 Azure DDoS Protection Basic으로 보호됩니다. 이 기능은 기본적으로 추가 비용 없이 Azure Front Door 플랫폼에 통합됩니다. Front Door 전역 배포 네트워크의 전체 크기 및 용량은 항상 작동하는 트래픽 모니터링 및 실시간 위험 완화를 통해 일반적인 네트워크 계층 공격을 방어합니다. 또한 기본 DDoS 보호는 퍼블릭 엔드포인트를 대상으로 하는 가장 일반적이고 자주 발생하는 계층 7 DNS 쿼리 플러드(flood)와 계층 3 및 4 대규모 공격에 대해 방어합니다. 또한 이 서비스는 대규모 공격으로부터 Microsoft의 엔터프라이즈 및 소비자 서비스를 보호하는 것으로 입증된 추적 기록을 포함합니다. 자세한 내용은 [Azure DDoS Protection](../../security/fundamentals/ddos-best-practices.md)를 참조하세요.

## <a name="protocol-blocking"></a>프로토콜 차단

Front Door는 HTTP 및 HTTPS 프로토콜에 대한 트래픽만 허용하고 알려진 헤더를 사용하는 유효한 요청만 처리합니다 `Host` . 이 동작을 통해 다양한 프로토콜 및 포트, DNS 증폭 공격 및 TCP 감염 공격에 걸쳐 분산되는 대규모 공격을 비롯한 몇 가지 일반적인 DDoS 공격 유형을 완화할 수 있습니다.

## <a name="capacity-absorption"></a>용량 흡수

Front Door는 전역적으로 분산된 대규모 확장 서비스입니다. Microsoft의 대규모 클라우드 제품을 비롯한 많은 고객이 초당 수백, 수천 개의 요청을 받을 수 있습니다. Front Door는 Azure 네트워크의 가장자리에 있으며, 흡수 및 지리적으로 대규모 볼륨 공격을 분리합니다. 이로 인해 악의적인 트래픽이 Azure 네트워크의 가장자리보다 더 이상 이동하지 못하게 할 수 있습니다.

## <a name="caching"></a>캐싱

[Front Door의 캐싱 기능](concept-caching.md)을 사용하여 공격에 의해 생성된 대량 트래픽 볼륨의 백 엔드를 보호할 수 있습니다. 캐시된 리소스는 Front Door 에지 노드에서 반환되므로 백 엔드로 전달되지 않습니다. 동적 응답에 대한 짧은 캐시 만료 시간(초 또는 분)도 백 엔드 서비스의 부하를 크게 줄일 수 있습니다. 캐싱 개념 및 패턴에 대한 자세한 내용은 [캐싱 고려 사항](/azure/architecture/best-practices/caching) 및 [캐시 배제 패턴](/azure/architecture/patterns/cache-aside)을 참조하세요.

## <a name="web-application-firewall-waf"></a>WAF(웹 애플리케이션 방화벽)

[Front Door의 WAF](../../web-application-firewall/afds/afds-overview.md)(웹 애플리케이션 방화벽)를 사용하여 다양한 유형의 공격을 완화할 수 있습니다.

* 관리형 규칙 집합을 사용하면 여러 일반적인 공격으로부터 보호합니다.
* 정의된 지리적 지역 외부 또는 정의된 하위 지역 내에서 트래픽을 차단하거나 정적 웹 페이지로 리디렉션할 수 있습니다. 자세한 내용은 [지역 필터링](../../web-application-firewall/afds/waf-front-door-geo-filtering.md)을 참조하세요.
* 악성으로 식별하는 IP 주소 및 범위는 차단할 수 있습니다.
* 요금 제한을 적용하여 IP 주소가 서비스를 너무 자주 호출하지 않도록 방지할 수 있습니다.
* [사용자 지정 WAF 규칙](../../web-application-firewall/afds/waf-front-door-custom-rules.md)을 만들어 알려진 서명이 있는 HTTP 또는 HTTPS 공격을 자동으로 차단 및 속도 제한을 할 수 있습니다.

## <a name="for-further-protection"></a>추가 보호

추가 보호를 요구하는 경우 백 엔드가 배포된 VNet에서 [Azure DDoS Protection Standard](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard)를 사용하도록 설정할 수 있습니다. DDoS Protection 표준 고객은 다음을 비롯한 더 많은 혜택을 받습니다.

* 비용 보호
* SLA 보장
* 공격 중에 즉각적인 도움을 받을 수 있도록 DDoS 빠른 응답 팀의 전문가에게 액세스하세요.

## <a name="next-steps"></a>다음 단계

[Front Door 표준/프리미엄 만드는](create-front-door-portal.md) 방법 알아보기.
