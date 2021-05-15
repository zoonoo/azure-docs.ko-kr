---
title: WAF(웹 애플리케이션 방화벽)에 대한 봇 보호 구성
description: Azure Application Gateway에서 WAF(웹 애플리케이션 방화벽)에 대한 봇 보호를 구성하는 방법을 알아봅니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "73516865"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Azure Application Gateway에서 웹 애플리케이션 방화벽에 대한 봇 보호 구성(미리 보기)

이 문서에서는 Azure Portal을 사용하여 Application Gateway에 대해 Azure WAF(웹 애플리케이션 방화벽)에서 봇 보호 규칙을 구성하는 방법을 보여 줍니다. 

WAF에 대해 관리형 봇 보호 규칙 집합을 사용하도록 설정하여 알려진 악성 IP 주소의 요청을 차단하거나 기록할 수 있습니다. IP 주소는 Microsoft 위협 인텔리전스 피드에서 제공됩니다. Intelligent Security Graph는 Microsoft 위협 인텔리전스를 구동하며 Azure Security Center를 비롯한 여러 서비스에서 사용됩니다.

> [!NOTE]
> 봇 보호 규칙 세트는 현재 공개 미리 보기이며 미리 보기 서비스 수준 계약과 함께 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은  [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

 [Application Gateway에 대한 웹 애플리케이션 방화벽 정책 만들기](create-waf-policy-ag.md)에 설명된 지침에 따라 Application Gateway에 대한 기본 WAF 정책을 만듭니다.

## <a name="enable-bot-protection-rule-set"></a>봇 보호 규칙 세트 사용 설정

1. 이전에 만든 **기본** 정책 페이지의 **설정** 아래에서 **규칙** 을 선택합니다.  

2. 세부 정보 페이지의  **규칙 관리**  섹션 아래에 있는 드롭다운 메뉴에서 봇 보호 규칙의 확인란을 선택한 다음 **저장** 을 선택합니다.

> [!div class="mx-imgBorder"]
> ![봇 보호](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙에 대한 자세한 내용은 [Azure Application Gateway의 웹 애플리케이션 방화벽 v2에 대한 사용자 지정 규칙](custom-waf-rules-overview.md)을 참조하세요.