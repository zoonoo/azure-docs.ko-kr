---
title: Azure의 azure 웹 응용 프로그램 방화벽 Front 도어 서비스-질문과 대답
description: 이 문서에서는 Azure Front 도어의 웹 응용 프로그램 방화벽에 대 한 질문과 대답을 제공 합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5b60082db53b458adc53ac23d98731ad1c97b52b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563650"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Azure Front 도어 서비스의 Azure 웹 응용 프로그램 방화벽에 대 한 질문과 대답

이 문서에서는 Azure Front 도어 서비스의 기능 및 기능에 대 한 Azure WAF (웹 응용 프로그램 방화벽)에 대 한 일반적인 질문에 답변 합니다. 

## <a name="what-is-azure-waf"></a>Azure WAF란?

Azure WAF는 SQL 삽입, 사이트 간 스크립팅 및 기타 웹 공격과 같은 일반적인 위협으로부터 웹 애플리케이션을 보호하는 데 유용한 웹 애플리케이션 방화벽입니다. 사용자 지정 및 관리형 규칙의 조합으로 구성된 WAF 정책을 정의하여 웹 애플리케이션에 대한 액세스를 제어할 수 있습니다.

Azure WAF 정책은 Application Gateway 또는 Azure Front Doors에 호스트되는 웹 애플리케이션에 적용할 수 있습니다.

## <a name="what-is-waf-on-azure-front-door"></a>Azure 전면 도어의 WAF 란? 

Azure 전면 도어는 확장성이 높고 전 세계적으로 분산 된 응용 프로그램 및 콘텐츠 배달 네트워크입니다. Azure WAF는 프런트 도어와 통합 된 경우 Azure 네트워크에 지에서 서비스 거부 및 대상 응용 프로그램 공격을 중지 하 고, 가상 네트워크에 들어가기 전에 공격 원본에서 공격을 중지 하 고, 성능을 저하 시 키 지 않고 보호를 제공 합니다.

## <a name="does-azure-waf-support-https"></a>Azure WAF는 HTTPS를 지원 하나요?

전면 도어는 TLS 오프 로딩을 제공 합니다. WAF는 기본적으로 전면 도어와 통합 되며 암호 해독 후 요청을 검사할 수 있습니다.

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF는 i p v 6을 지원 하나요?

예. IPv4 및 IPv6에 대 한 IP 제한을 구성할 수 있습니다.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>관리 되는 규칙 집합은 최신 상태 인가요?

위협 환경을 변경 하는 것이 가장 좋습니다. 새 규칙을 업데이트 한 후에는 새 버전 번호를 사용 하 여 기본 규칙 집합에 추가 됩니다.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>WAF 정책을 변경 하는 경우 전파 시간은 어떻게 되나요?

WAF 정책을 전역적으로 배포 하는 작업은 일반적으로 약 5 분 정도 걸리며 자주 완료 됩니다.

## <a name="can-waf-policies-be-different-for-different-regions"></a>다른 지역에 대해 WAF 정책이 다를 수 있나요?

전면 도어와 통합 된 경우 WAF는 전역 리소스입니다. 모든 전면 도어 위치에서 동일한 구성이 적용 됩니다.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>프런트 도어 에서만 내 백 엔드에 대 한 액세스를 제한 어떻게 할까요??

프런트 도어 아웃 바운드 IP 주소 범위만 허용 하 고 인터넷에서 직접 액세스를 거부 하도록 백 엔드에서 IP Access Control 목록을 구성할 수 있습니다. 서비스 태그는 가상 네트워크에서 사용할 수 있도록 지원 됩니다. 또한 웹 응용 프로그램에 대해 X 전달-호스트 HTTP 헤더 필드가 유효한 지 확인할 수 있습니다.

## <a name="which-azure-waf-options-should-i-choose"></a>어떤 Azure WAF 옵션을 선택 해야 하나요?

Azure에서 WAF 정책을 적용할 때 두 가지 옵션이 있습니다. Azure 전면 도어를 사용 하는 WAF는 전역적으로 분산 된에 지 보안 솔루션입니다. Application Gateway 포함 된 WAF는 지역 전용 솔루션입니다. 전반적인 성능 및 보안 요구 사항에 따라 솔루션을 선택 하는 것이 좋습니다. 자세한 내용은 [Azure의 응용 프로그램 배달 도구 모음을 사용한 부하 분산](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md)을 참조 하세요.


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>모든 통합 플랫폼에서 동일한 WAF 기능을 지원 하나요?

현재 ModSec CRS 2.2.9, CRS 3.0 및 CRS 3.1 규칙은 Application Gateway의 WAF 에서만 지원 됩니다. 요금 제한, 지역 필터링 및 Azure 관리 되는 기본 규칙 집합 규칙은 Azure Front 도어의 WAF 에서만 지원 됩니다.

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS 보호가 전면 도어와 통합 되나요? 

Azure 네트워크에 지에서 전 세계적으로 배포 되는 Azure Front 도어가 대규모 볼륨 공격을 흡수 하 고 지리적으로 격리할 수 있습니다. 알려진 서명이 있는 http (s) 공격을 자동으로 차단 및 평가 하는 사용자 지정 WAF 정책을 만들 수 있습니다. 또한 백 엔드가 배포 되는 VNet에서 DDoS Protection Standard를 사용 하도록 설정할 수 있습니다. Azure DDoS Protection 표준 고객은 비용 보호, SLA 보증, 공격 중에 즉각적인 도움을 받을 수 있는 DDoS 빠른 응답 팀의 전문가에 게 액세스 등의 추가 혜택을 받습니다. 자세한 내용은 [DDoS protection On Front 도어가](../../frontdoor/front-door-ddos.md)항목을 참조 하세요.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>내 요금 제한 규칙에 대해 구성 된 임계값을 초과 하는 추가 요청이 내 백 엔드 서버에 전달 되는 이유는 무엇 인가요?

Rate limit 규칙은 모든 클라이언트 IP 주소에서 비정상적으로 높은 트래픽을 제한할 수 있습니다. 1 분 또는 5 분 동안 클라이언트 IP 주소에서 허용 되는 웹 요청 수에 대 한 임계값을 구성할 수 있습니다. 세부적인 요금 제어를 위해 HTTP (S) 매개 변수 일치와 같은 추가 일치 조건과 함께 요율 제한을 결합할 수 있습니다. 

동일한 클라이언트의 요청은 종종 동일한 Front 도어 서버에 도착 합니다. 이 경우 임계값을 초과 하는 추가 요청이 즉시 차단 됩니다. 

그러나 동일한 클라이언트의 요청이 속도 제한 카운터를 새로 고치지 않은 다른 프런트 도어 서버에 도착할 수 있습니다. 예를 들어 클라이언트는 각 요청에 대해 새 연결을 열 수 있으며 임계값은 낮습니다. 이 경우 새 Front 도어 서버에 대 한 첫 번째 요청은 요율 제한 검사를 통과 합니다. 일반적으로는 클라이언트 IP 주소에서 서비스 거부 공격 으로부터 보호 하기 위해 높은 속도로 설정 됩니다. 매우 낮은 임계값에 대 한 추가 요청을 임계값 가져오기 보다 크게 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 웹 애플리케이션 방화벽](../overview.md)에 대해 알아봅니다.
- [Azure Front Door](../../frontdoor/front-door-overview.md)에 대해 자세히 알아봅니다.