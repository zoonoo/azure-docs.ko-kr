---
title: Azure 웹 응용 프로그램 방화벽(WAF)에 대한 봇 보호 구성
description: Azure 응용 프로그램 게이트웨이에서 WAF(웹 응용 프로그램 방화벽)에 대한 봇 보호를 구성하는 방법을 알아봅니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516865"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Azure 응용 프로그램 게이트웨이에서 웹 응용 프로그램 방화벽에 대한 봇 보호 구성(미리 보기)

이 문서에서는 Azure 포털을 사용하여 응용 프로그램 게이트웨이에 대한 WAF(Azure 웹 응용 프로그램 방화벽)에서 봇 보호 규칙을 구성하는 방법을 보여 주십니다. 

WAF가 알려진 악성 IP 주소의 요청을 차단하거나 로그온하도록 관리형 봇 보호 규칙 세트를 활성화할 수 있습니다. IP 주소는 Microsoft 위협 인텔리전스 피드에서 제공됩니다. Intelligent Security Graph는 Microsoft 위협 인텔리전스를 구동하며 Azure Security Center를 비롯한 여러 서비스에서 사용됩니다.

> [!NOTE]
> 봇 보호 규칙 집합은 현재 공개 미리 보기중이며 미리 보기 서비스 수준 계약이 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

응용 프로그램 게이트웨이에 대한 웹 응용 프로그램 방화벽 만들기 정책에 설명된 지침에 따라 [응용 프로그램 게이트웨이에 대한](create-waf-policy-ag.md)기본 WAF 정책을 만듭니다.

## <a name="enable-bot-protection-rule-set"></a>봇 보호 규칙 설정 사용

1. 이전에 만든 **기본** 정책 페이지에서 **설정에서** **규칙을**선택합니다.  

2. 세부 정보 페이지에서 **규칙** 관리 섹션에서 드롭다운 메뉴에서 봇 보호 규칙에 대한 확인란을 선택한 다음 **저장을**선택합니다.

> [!div class="mx-imgBorder"]
> ![봇 보호](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙에 대한 자세한 내용은 [Azure 응용 프로그램 게이트웨이에서 웹 응용 프로그램 방화벽 v2에 대한 사용자 지정 규칙을](custom-waf-rules-overview.md)참조하십시오.