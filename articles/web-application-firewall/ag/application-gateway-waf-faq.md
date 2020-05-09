---
title: Application Gateway의 Azure 웹 응용 프로그램 방화벽-질문과 대답
description: 이 문서에서는 Application Gateway의 웹 응용 프로그램 방화벽에 대 한 질문과 대답을 제공 합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 57081cd948bcee1261415eae31f91b3c61f08c9f
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842852"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Application Gateway에서 Azure 웹 응용 프로그램 방화벽에 대 한 질문과 대답

이 문서에서는 Application Gateway 기능 및 기능에 대 한 Azure WAF (웹 응용 프로그램 방화벽)에 대 한 일반적인 질문에 답변 합니다. 

## <a name="what-is-azure-waf"></a>Azure WAF 란?

Azure WAF는 SQL 삽입, 사이트 간 스크립팅 및 기타 웹 익스플로잇과 같은 일반적인 위협 으로부터 웹 응용 프로그램을 보호 하는 데 도움이 되는 웹 응용 프로그램 방화벽입니다. 사용자 지정 및 관리 규칙의 조합으로 구성 된 WAF 정책을 정의 하 여 웹 응용 프로그램에 대 한 액세스를 제어할 수 있습니다.

Azure WAF 정책은 Application Gateway 또는 Azure Front 문에 호스트 되는 웹 응용 프로그램에 적용할 수 있습니다.

## <a name="what-features-does-the-waf-sku-support"></a>WAF SKU는 어떤 기능을 지원 하나요?

WAF SKU는 표준 SKU에서 사용할 수 있는 모든 기능을 지원 합니다.

## <a name="how-do-i-monitor-waf"></a>WAF를 모니터링하려면 어떻게 하나요?

진단 로깅을 통해 WAF를 모니터링 합니다. 자세한 내용은 [Application Gateway에 대 한 진단 로깅 및 메트릭](../../application-gateway/application-gateway-diagnostics.md)을 참조 하세요.

## <a name="does-detection-mode-block-traffic"></a>검색 모드에서 트래픽을 차단하나요?

아니요. 검색 모드는 WAF 규칙을 트리거하는 트래픽만 로깅합니다.

## <a name="can-i-customize-waf-rules"></a>WAF 규칙을 사용자 지정할 수 있나요?

예. 자세한 내용은 [WAF 규칙 그룹 및 규칙 사용자 지정](application-gateway-customize-waf-rules-portal.md)을 참조 하세요.

## <a name="what-rules-are-currently-available-for-waf"></a>WAF에 대해 현재 사용할 수 있는 규칙은 무엇 인가요?

WAF는 현재 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)및 [3.1](application-gateway-crs-rulegroups-rules.md#owasp31)을 지원 합니다. 이러한 규칙은 OWASP (Web Application Security Project)에서 식별 하는 대부분의 상위 10 개 취약점에 대 한 기준선 보안을 제공 합니다. 

* SQL 삽입 공격 보호
* 사이트 간 스크립팅 보호
* 명령 삽입, HTTP 요청 밀 반입, HTTP 응답 분할 및 원격 파일 포함 공격과 같은 일반적인 웹 공격 으로부터 보호
* HTTP 프로토콜 위반 보호
* 누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호
* 보트, 크롤러 및 스캐너 방지
* 일반적인 응용 프로그램 잘못 된 기능 (즉, Apache, IIS 등) 검색

자세한 내용은 [OWASP top-10 취약성](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)을 참조 하세요.

## <a name="does-waf-support-ddos-protection"></a>WAF는 DDoS protection을 지원 하나요?

예. Application gateway가 배포 된 가상 네트워크에서 DDoS 보호를 사용 하도록 설정할 수 있습니다. 이 설정은 Azure DDoS Protection 서비스가 응용 프로그램 게이트웨이 VIP (가상 IP)도 보호 하도록 합니다.


## <a name="next-steps"></a>다음 단계

- [Azure 웹 응용 프로그램 방화벽](../overview.md)에 대해 알아봅니다.
- [Azure Front 도어](../../frontdoor/front-door-overview.md)에 대해 자세히 알아보세요.
