---
title: Azure Front Door에서 웹 애플리케이션 방화벽에 대한 봇 보호 구성(미리 보기)
description: Azure Portal을 사용하여 Front Door용 Azure WAF(Web Application Firewall)에서 봇 보호 규칙을 구성하는 방법에 대해 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 2357c51f47bcb9bd8bbc6c408cb6d8edbab4d10e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91267009"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Web Application Firewall에 대한 봇 보호 구성(미리 보기)
이 문서에서는 Azure Portal을 사용하여 Front Door용 Azure WAF(Web Application Firewall)에서 봇 보호 규칙을 구성하는 방법을 보여줍니다. 봇 보호 규칙은 CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용하여 구성할 수도 있습니다.

> [!IMPORTANT]
> 봇 보호 규칙 세트는 현재 공개 미리 보기이며 미리 보기 서비스 수준 계약과 함께 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.  자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[Azure Portal을 사용하여 Azure Front Door용 WAF 정책 만들기](waf-front-door-create-portal.md)에 설명된 지침을 수행하여 Front Door용 기본 WAF 정책을 만듭니다.

## <a name="enable-bot-protection-rule-set"></a>봇 보호 규칙 집합 사용 설정

**관리 규칙** 페이지에서 웹 애플리케이션 방화벽 정책을 만들 때 먼저 **관리형 규칙 집합** 섹션을 찾아 드롭다운 메뉴에서 규칙 **Microsoft_BotManager_1.0** 앞에 있는 확인란을 선택한 다음 **검토 + 만들기** 를 선택합니다.

   ![봇 보호 규칙](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>다음 단계

- [WAF 모니터링](waf-front-door-monitor.md) 방법을 알아봅니다.
