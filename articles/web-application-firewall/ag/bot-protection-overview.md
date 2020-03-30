---
title: Azure 응용 프로그램 게이트웨이 봇 보호 개요에 대한 WAF
titleSuffix: Azure Web Application Firewall
description: 이 문서에서는 응용 프로그램 게이트웨이 봇 보호에 대한 WAF(웹 응용 프로그램 방화벽)에 대한 개요를 제공합니다.
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027096"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure 응용 프로그램 게이트웨이 봇 보호 개요에 Azure 웹 응용 프로그램 방화벽

모든 인터넷 트래픽의 약 20%는 나쁜 봇에서 비롯됩니다. 웹 응용 프로그램에서 스크래핑, 검색 및 취약점 찾기와 같은 작업을 수행합니다. 이러한 봇이 WAF(웹 응용 프로그램 방화벽)에서 중지되면 공격할 수 없습니다. 또한 백엔드 및 기타 기본 인프라와 같은 리소스와 서비스를 사용할 수 없습니다.

WAF가 알려진 악성 IP 주소의 요청을 차단하거나 로그온하도록 관리형 봇 보호 규칙 세트를 활성화할 수 있습니다. IP 주소는 Microsoft 위협 인텔리전스 피드에서 제공됩니다. Intelligent Security Graph는 Microsoft 위협 인텔리전스를 구동하며 Azure Security Center를 비롯한 여러 서비스에서 사용됩니다.

> [!IMPORTANT]
> 봇 보호 규칙 집합은 현재 공개 미리 보기중이며 미리 보기 서비스 수준 계약이 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 참조하십시오.

## <a name="use-with-owasp-rulesets"></a>OWASP 규칙 집합과 함께 사용

모든 OWASP 규칙 집합(2.2.9, 3.0 및 3.1)과 함께 봇 보호 규칙 집합을 사용할 수 있습니다. 지정된 시간에 하나의 OWASP 규칙 집합만 사용할 수 있습니다. 봇 보호 규칙 집합에는 자체 규칙 집합에 나타나는 추가 규칙이 포함되어 있습니다. **Microsoft_BotManagerRuleSet_0.1이라는**제목이 붙으며 다른 OWASP 규칙처럼 활성화하거나 비활성화할 수 있습니다.

![봇 규칙 집합](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>규칙 집합 업데이트

알려진 불량 IP 주소의 봇 완화 규칙 집합 목록은 Microsoft 위협 인텔리전스 피드에서 하루에 여러 번 업데이트되어 봇과 동기화상태를 유지합니다. 봇 공격 벡터가 변경되더라도 웹 응용 프로그램은 지속적으로 보호됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 응용 프로그램 게이트웨이에서 웹 응용 프로그램 방화벽에 대한 봇 보호 구성(미리 보기)](bot-protection.md)
