---
title: Application Gateway의 Azure 웹 애플리케이션 방화벽 - 질문과 대답
description: 이 문서에서는 Application Gateway의 웹 애플리케이션 방화벽에 대한 질문과 대답을 제공합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 3bc71e10167b21b65616dd20f8edb9a1d902527e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83798342"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Application Gateway의 Azure 웹 애플리케이션 방화벽에 대한 질문과 대답

이 문서에서는 Application Gateway의 Azure WAF(웹 애플리케이션 방화벽) 특성 및 기능에 대한 일반적인 질문과 대답을 제공합니다. 

## <a name="what-is-azure-waf"></a>Azure WAF란?

Azure WAF는 SQL 삽입, 사이트 간 스크립팅 및 기타 웹 공격과 같은 일반적인 위협으로부터 웹 애플리케이션을 보호하는 데 유용한 웹 애플리케이션 방화벽입니다. 사용자 지정 및 관리형 규칙의 조합으로 구성된 WAF 정책을 정의하여 웹 애플리케이션에 대한 액세스를 제어할 수 있습니다.

Azure WAF 정책은 Application Gateway 또는 Azure Front Doors에 호스트되는 웹 애플리케이션에 적용할 수 있습니다.

## <a name="what-features-does-the-waf-sku-support"></a>WAF SKU는 어떤 기능을 지원하나요?

WAF SKU는 표준 SKU에서 사용할 수 있는 모든 기능을 지원합니다.

## <a name="how-do-i-monitor-waf"></a>WAF를 모니터링하려면 어떻게 하나요?

진단 로깅을 통해 WAF를 모니터링합니다. 자세한 내용은 [Application Gateway에 대한 진단 로깅 및 메트릭](../../application-gateway/application-gateway-diagnostics.md)을 참조하세요.

## <a name="does-detection-mode-block-traffic"></a>검색 모드에서 트래픽을 차단하나요?

아니요. 검색 모드는 WAF 규칙을 트리거하는 트래픽만 로깅합니다.

## <a name="can-i-customize-waf-rules"></a>WAF 규칙을 사용자 지정할 수 있나요?

예. 자세한 내용은 [WAF 규칙 그룹 및 규칙 사용자 지정](application-gateway-customize-waf-rules-portal.md)을 참조하세요.

## <a name="what-rules-are-currently-available-for-waf"></a>현재 WAF에 사용 가능한 규칙은 무엇인가요?

WAF는 현재 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)및 [3.1](application-gateway-crs-rulegroups-rules.md#owasp31)을 지원합니다. 이러한 규칙은 OWASP(Open Web Application Security Project)에서 식별하는 상위 10가지 취약성 대부분에 대한 기준 보안을 제공합니다. 

* SQL 삽입 공격 보호
* 사이트 간 스크립팅 방지
* 명령 삽입, HTTP 요청 밀반입, HTTP 응답 분할, 원격 파일 포함 공격 등의 일반 웹 공격 방지
* HTTP 프로토콜 위반 보호
* 누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호
* 보트, 크롤러 및 스캐너 방지
* 일반적인 애플리케이션 구성 오류(즉 Apache, IIS 등) 검색

자세한 내용은 [OWASP의 상위 10가지 취약성](https://owasp.org/www-project-top-ten/)을 참조하세요.

## <a name="does-waf-support-ddos-protection"></a>WAF는 DDoS Protection을 지원하나요?

예. Application Gateway가 배포된 가상 네트워크에서 DDoS Protection을 사용하도록 설정할 수 있습니다. 이 설정은 Azure DDoS Protection 서비스가 애플리케이션 게이트웨이 VIP(가상 IP)도 보호하도록 합니다.


## <a name="next-steps"></a>다음 단계

- [Azure 웹 애플리케이션 방화벽](../overview.md)에 대해 알아봅니다.
- [Azure Front Door](../../frontdoor/front-door-overview.md)에 대해 자세히 알아봅니다.
