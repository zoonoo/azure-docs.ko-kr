---
title: Azure WAF (웹 응용 프로그램 방화벽)에 대해 봇 보호 구성
description: Azure 애플리케이션 게이트웨이에서 WAF (웹 응용 프로그램 방화벽)에 대해 봇 보호를 구성 하는 방법에 대해 알아봅니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "73516865"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Azure 애플리케이션 Gateway에서 웹 응용 프로그램 방화벽에 대 한 bot 보호 구성 (미리 보기)

이 문서에서는 Azure Portal를 사용 하 여 Application Gateway에 대해 Azure WAF (웹 응용 프로그램 방화벽)에서 봇 보호 규칙을 구성 하는 방법을 보여 줍니다. 

WAF에 대해 관리 되는 봇 보호 규칙 집합을 사용 하도록 설정 하 여 알려진 악성 IP 주소의 요청을 차단 하거나 기록할 수 있습니다. IP 주소는 Microsoft 위협 인텔리전스 피드에서 제공됩니다. Intelligent Security Graph는 Microsoft 위협 인텔리전스를 구동하며 Azure Security Center를 비롯한 여러 서비스에서 사용됩니다.

> [!NOTE]
> Bot protection 규칙 집합은 현재 공개 미리 보기 상태 이며 미리 보기 서비스 수준 계약과 함께 제공 됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대 한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

 [Application Gateway에 대 한 웹 응용 프로그램 방화벽 정책 만들기](create-waf-policy-ag.md)에 설명 된 지침에 따라 Application Gateway에 대 한 기본 waf 정책을 만듭니다.

## <a name="enable-bot-protection-rule-set"></a>Bot 보호 규칙 집합 사용

1. 이전에 만든 **기본** 정책 페이지의 **설정**에서 **규칙**을 선택 합니다.  

2. 세부 정보 페이지의 **규칙** 관리 섹션 아래에 있는 드롭다운 메뉴에서 봇 보호 규칙의 확인란을 선택 하 고 **저장**을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![봇 보호](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙에 대 한 자세한 내용은 [Azure 애플리케이션 게이트웨이에서 웹 응용 프로그램 방화벽 v2의 사용자 지정 규칙](custom-waf-rules-overview.md)을 참조 하세요.