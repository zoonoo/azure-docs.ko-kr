---
title: Azure 웹 응용 프로그램 방화벽-질문과 대답
description: 이 페이지에서는 Azure Front 도어 서비스에 대해 자주 묻는 질문에 대 한 답변을 제공 합니다.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: f7d205920704ce8aedf3b2e3c07bd429b3c64964
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375338"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Azure 웹 응용 프로그램 방화벽에 대 한 질문과 대답

이 문서에서는 Azure WAF (웹 응용 프로그램 방화벽) 기능 및 기능에 대 한 일반적인 질문에 답변 합니다. 

## <a name="what-is-azure-waf"></a>Azure WAF 란?

Azure WAF는 SQL 삽입, 사이트 간 스크립팅 및 기타 웹 익스플로잇과 같은 일반적인 위협 으로부터 웹 응용 프로그램을 보호 하는 데 도움이 되는 웹 응용 프로그램 방화벽입니다. 사용자 지정 및 관리 규칙의 조합으로 구성 된 WAF 정책을 정의 하 여 웹 응용 프로그램에 대 한 액세스를 제어할 수 있습니다.

Azure WAF 정책은 Application Gateway 또는 Azure Front 도어 서비스에서 호스트 되는 웹 응용 프로그램에 적용할 수 있습니다.

## <a name="what-is-waf-for-azure-front-door-service"></a>Azure Front 도어 서비스의 WAF 란? 

Azure 전면 도어는 확장성이 높고 전 세계적으로 분산 된 응용 프로그램 및 콘텐츠 배달 네트워크입니다. Azure WAF는 프런트 도어와 통합 된 경우 Azure 네트워크에 지에서 서비스 거부 및 대상 응용 프로그램 공격을 중지 하 고, 가상 네트워크에 들어가기 전에 공격 원본에서 공격을 중지 하 고, 성능을 저하 시 키 지 않고 보호를 제공 합니다.

## <a name="does-azure-waf-support-https"></a>Azure WAF는 HTTPS를 지원 하나요?

전면 도어 서비스는 SSL 오프 로딩을 제공 합니다. WAF는 기본적으로 전면 도어와 통합 되며 암호 해독 후 요청을 검사할 수 있습니다.

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF는 i p v 6을 지원 하나요?

예. IPv4 및 IPv6에 대 한 IP 제한을 구성할 수 있습니다.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>관리 되는 규칙 집합은 최신 상태 인가요?

위협 환경을 변경 하는 것이 가장 좋습니다. 새 규칙을 업데이트 한 후에는 새 버전 번호를 사용 하 여 기본 규칙 집합에 추가 됩니다.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>WAF 정책을 변경 하는 경우 전파 시간은 어떻게 되나요?

WAF 정책을 전역적으로 배포 하는 작업은 일반적으로 약 5 분 정도 걸리며 자주 완료 됩니다.

## <a name="can-waf-policies-be-different-for-different-regions"></a>다른 지역에 대해 WAF 정책이 다를 수 있나요?

프런트 도어 서비스와 통합 된 경우 WAF는 전역 리소스입니다. 모든 전면 도어 위치에서 동일한 구성이 적용 됩니다.
 

## <a name="which-azure-waf-options-should-i-choose"></a>어떤 Azure WAF 옵션을 선택 해야 하나요?

Azure에서 WAF 정책을 적용할 때 두 가지 옵션이 있습니다. Azure 전면 도어를 사용 하는 WAF는 전역적으로 분산 된에 지 보안 솔루션입니다. Application Gateway 포함 된 WAF는 지역 전용 솔루션입니다. 전반적인 성능 및 보안 요구 사항에 따라 솔루션을 선택 하는 것이 좋습니다. 자세한 내용은 [Azure의 응용 프로그램 배달 도구 모음을 사용한 부하 분산](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)을 참조 하세요.


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>모든 통합 플랫폼에서 동일한 WAF 기능을 지원 하나요?

현재 ModSec CRS 2.2.9 및 CRS 3.0 규칙은 Application Gateway WAF 에서만 지원 됩니다. 속도 제한, 지역 필터링 및 Azure 관리 되는 기본 규칙 집합 규칙은 Azure Front 도어의 WAF 에서만 지원 됩니다.

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS 보호가 전면 도어와 통합 되나요? 

Azure 네트워크에 지에서 전 세계적으로 배포 되는 Azure Front 도어가 대규모 볼륨 공격을 흡수 하 고 지리적으로 격리할 수 있습니다. 알려진 서명이 있는 http (s) 공격을 자동으로 차단 및 평가 하는 사용자 지정 WAF 정책을 만들 수 있습니다. 또한 백 엔드가 배포 되는 VNet에서 DDoS Protection Standard를 사용 하도록 설정할 수 있습니다. Azure DDoS Protection 표준 고객은 비용 보호, SLA 보증, 공격 중에 즉각적인 도움을 받을 수 있는 DDoS 빠른 응답 팀의 전문가에 게 액세스 등의 추가 혜택을 받습니다. 

DDoS 공격 노출 영역을 줄이기 위해 프로덕션 환경에서 백 엔드 잠금을 해제 하는 것이 좋습니다. [내 백 엔드에 대 한 액세스를 Azure Front 도어에만 잠금 어떻게 할까요?를 참조 하세요](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door) .


## <a name="next-steps"></a>다음 단계

- [Azure 웹 애플리케이션 방화벽](waf-overview.md)에 대해 알아봅니다.
- [Azure Front 도어](front-door-overview.md)에 대해 자세히 알아보세요.
