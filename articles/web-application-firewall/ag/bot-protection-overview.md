---
title: Azure 애플리케이션 Gateway bot 보호 개요의 WAF
titleSuffix: Azure Web Application Firewall
description: 이 문서에서는 Application Gateway bot protection의 WAF (웹 응용 프로그램 방화벽) 개요를 제공 합니다.
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77027096"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure 애플리케이션 Gateway bot protection의 Azure 웹 응용 프로그램 방화벽 개요

모든 인터넷 트래픽의 약 20%가 잘못 된 봇에서 제공 됩니다. 웹 응용 프로그램에서 취약점을 검색 하 고 스크랩 하는 등의 작업을 수행 합니다. 이러한 봇이 WAF (웹 응용 프로그램 방화벽)에서 중지 되 면 사용자를 공격할 수 없습니다. 또한 백 엔드 및 기타 기본 인프라와 같은 리소스와 서비스를 사용할 수 없습니다.

WAF에 대해 관리 되는 봇 보호 규칙 집합을 사용 하도록 설정 하 여 알려진 악성 IP 주소의 요청을 차단 하거나 기록할 수 있습니다. IP 주소는 Microsoft 위협 인텔리전스 피드에서 제공됩니다. Intelligent Security Graph는 Microsoft 위협 인텔리전스를 구동하며 Azure Security Center를 비롯한 여러 서비스에서 사용됩니다.

> [!IMPORTANT]
> Bot protection 규칙 집합은 현재 공개 미리 보기 상태 이며 미리 보기 서비스 수준 계약과 함께 제공 됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대 한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="use-with-owasp-rulesets"></a>OWASP 규칙 집합 사용

OWASP 규칙 집합 (2.2.9, 3.0 및 3.1)과 함께 봇 보호 규칙 집합을 사용할 수 있습니다. 지정 된 시간에 OWASP 규칙 집합을 하나만 사용할 수 있습니다. Bot 보호 규칙 집합에는 자체 규칙 집합에 표시 되는 추가 규칙이 포함 되어 있습니다. **Microsoft_BotManagerRuleSet_0**. n s s 라는 제목의 제목을 사용 하거나 다른 OWASP 규칙 처럼 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

![Bot 규칙 집합](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>규칙 집합 업데이트

Microsoft 위협 인텔리전스 피드에서 봇과 동기화를 유지 하기 위해 알려진 잘못 된 IP 주소의 봇 완화 규칙 집합 목록은 매일 여러 번 업데이트 됩니다. 웹 응용 프로그램은 봇 공격 벡터가 변경 되는 경우에도 지속적으로 보호 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 Gateway에서 웹 응용 프로그램 방화벽에 대 한 bot 보호 구성 (미리 보기)](bot-protection.md)
