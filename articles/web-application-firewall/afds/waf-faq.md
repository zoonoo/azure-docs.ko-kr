---
title: Azure 웹 응용 프로그램 방화벽 - 자주 묻는 질문
description: 이 문서에서는 Azure 정문에서 웹 응용 프로그램 방화벽에 대해 자주 묻는 질문에 대한 답변을 제공합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: c40210f3a6f0fb10be1f20deef87b2acfa076a4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460639"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Azure 정문 서비스에서 Azure 웹 응용 프로그램 방화벽에 대해 자주 묻는 질문

이 문서에서는 WAF(Azure 웹 응용 프로그램 방화벽) 기능 및 기능에 대한 일반적인 질문에 대해 답변합니다. 

## <a name="what-is-azure-waf"></a>Azure WAF란 무엇입니까?

Azure WAF는 SQL 주입, 사이트 간 스크립팅 및 기타 웹 악용과 같은 일반적인 위협으로부터 웹 응용 프로그램을 보호하는 데 도움이 되는 웹 응용 프로그램 방화벽입니다. 사용자 지정 및 관리되는 규칙의 조합으로 구성된 WAF 정책을 정의하여 웹 응용 프로그램에 대한 액세스를 제어할 수 있습니다.

Azure WAF 정책은 응용 프로그램 게이트웨이 또는 Azure 정문에서 호스팅되는 웹 응용 프로그램에 적용할 수 있습니다.

## <a name="what-is-waf-on-azure-front-door"></a>Azure 정문에 WAF란 무엇입니까? 

Azure Front Door는 확장성이 뛰어나고 전 세계적으로 분산된 응용 프로그램 및 콘텐츠 전송 네트워크입니다. Front Door와 통합되는 Azure WAF는 가상 네트워크에 들어가기 전에 공격 소스에 가까운 Azure 네트워크 에지에서 서비스 거부 및 대상 응용 프로그램 공격을 중지하여 성능을 저하시키지 않으면서 보호 기능을 제공합니다.

## <a name="does-azure-waf-support-https"></a>Azure WAF가 HTTPS를 지원합니까?

프론트 도어는 SSL 오프로딩을 제공합니다. WAF는 기본적으로 프론트 도어와 통합되어 있으며 해독 된 후 요청을 검사 할 수 있습니다.

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF가 IPv6를 지원합니까?

예. IPv4 및 IPv6에 대한 IP 제한을 구성할 수 있습니다.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>관리되는 규칙 집합은 최신 상태입니까?

우리는 변화하는 위협 환경에 부응하기 위해 최선을 다하고 있습니다. 새 규칙이 업데이트되면 새 버전 번호가 있는 기본 규칙 집합에 추가됩니다.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>WAF 정책을 변경하면 전파 시간은 어떻게 됩니까?

WAF 정책을 전 세계에 배포하는 데는 보통 약 5분이 걸리며 종종 더 빨리 완료됩니다.

## <a name="can-waf-policies-be-different-for-different-regions"></a>WAF 정책은 지역에 따라 다를 수 있습니까?

프론트 도어와 통합될 때 WAF는 글로벌 리소스입니다. 모든 프론트 도어 위치에 동일한 구성이 적용됩니다.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>백 엔드에 대한 액세스를 정문에서만 사용할 수 있도록 제한하려면 어떻게 해야 합니까?

전면 문 아웃바운드 IP 주소 범위만 허용하고 인터넷에서 직접 액세스를 거부하도록 백 엔드에 IP 액세스 제어 목록을 구성할 수 있습니다. 서비스 태그는 가상 네트워크에서 사용할 수 있도록 지원됩니다. 또한 X-전달 호스트 HTTP 헤더 필드가 웹 응용 프로그램에 유효한지 확인할 수 있습니다.

## <a name="which-azure-waf-options-should-i-choose"></a>어떤 Azure WAF 옵션을 선택해야 합니까?

Azure에서 WAF 정책을 적용할 때 두 가지 옵션이 있습니다. Azure 정문이 있는 WAF는 전 세계적으로 분산된 에지 보안 솔루션입니다. 응용 게이트웨이를 갖춘 WAF는 지역별 전용 솔루션입니다. 전반적인 성능 및 보안 요구 사항에 따라 솔루션을 선택하는 것이 좋습니다. 자세한 내용은 [Azure의 응용 프로그램 배달 제품군을 통해 로드 균형 조정을](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)참조하세요.


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>모든 통합 플랫폼에서 동일한 WAF 기능을 지원합니까?

현재 ModSec CRS 2.2.9, CRS 3.0 및 CRS 3.1 규칙은 응용 프로그램 게이트웨이의 WAF에서만 지원됩니다. 속도 제한, 지역 필터링 및 Azure 관리 기본 규칙 집합 규칙은 Azure 정문에서 WAF에서만 지원됩니다.

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS 보호가 프론트 도어와 통합되어 있습니까? 

Azure 네트워크 에지에 전 세계적으로 분산된 Azure Front Door는 대용량 공격을 흡수하고 지리적으로 격리할 수 있습니다. 사용자 지정 WAF 정책을 만들어 알려진 서명이 있는 http(http)의 공격을 자동으로 차단하고 속도 제한할 수 있습니다. 또한 백 엔드가 배포된 VNet에서 DDoS 보호 표준을 활성화할 수 있습니다. Azure DDoS 보호 표준 고객은 비용 보호, SLA 보증 및 공격 중 즉각적인 도움을 받기 위해 DDoS 신속 대응 팀의 전문가에 대한 액세스 등 추가 혜택을 받습니다.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>속도 제한 규칙에 대해 구성된 임계값 을 초과하는 추가 요청이 백 엔드 서버로 전달되는 이유는 무엇입니까?

속도 제한 규칙은 모든 클라이언트 IP 주소에서 비정상적으로 높은 트래픽을 제한할 수 있습니다. 1분 또는 5분 동안 클라이언트 IP 주소에서 허용되는 웹 요청 수에 대한 임계값을 구성할 수 있습니다. 세분화된 속도 제어를 위해 속도 제한을 HTTP(S) 매개 변수 일치와 같은 추가 일치 조건과 결합할 수 있습니다. 

동일한 클라이언트의 요청은 종종 동일한 정문 서버에 도착합니다. 이 경우 임계값을 초과하는 추가 요청이 즉시 차단되는 것을 볼 수 있습니다. 

그러나 동일한 클라이언트의 요청이 아직 속도 제한 카운터를 새로 고치지 않은 다른 Front Door 서버에 도착할 수 있습니다. 예를 들어 클라이언트는 각 요청에 대해 새 연결을 열 수 있으며 임계값이 낮습니다. 이 경우 새 정문 서버에 대한 첫 번째 요청은 속도 제한 검사를 통과합니다. 속도 제한 임계값은 일반적으로 모든 클라이언트 IP 주소의 서비스 거부 공격을 방어하기 위해 높게 설정됩니다. 매우 낮은 임계값의 경우 임계값을 초과하는 추가 요청이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure [웹 응용 프로그램 방화벽에](../overview.md)대해 자세히 알아보기.
- [Azure 정문에](../../frontdoor/front-door-overview.md)대해 자세히 알아보십시오.
