---
title: Azure Front 도어 (미리 보기)를 사용 하 여 WAF 용 bot 보호 구성
description: WAF (웹 응용 프로그램 방화벽)에 대해 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 95660f764d28172ecb55a4952b785fea5f2aa4bb
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186706"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>웹 응용 프로그램 방화벽에 대 한 bot 보호 구성 (미리 보기)
이 문서에서는 Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 Front 문에 대해 Azure WAF (웹 응용 프로그램 방화벽)에서 봇 보호 규칙을 구성 하는 방법을 보여 줍니다.

WAF에 대해 관리형 봇 보호 규칙 세트를 사용하도록 설정하여 알려진 악성 IP 주소의 요청에 대해 사용자 지정 작업을 수행할 수 있습니다. IP 주소는 Microsoft 위협 인텔리전스 피드에서 제공됩니다. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)는 Microsoft 위협 인텔리전스를 구동하며 Azure Security Center를 비롯한 여러 서비스에서 사용됩니다.

> [!IMPORTANT]
> 봇 보호 규칙 세트는 현재 공개 미리 보기이며 미리 보기 서비스 수준 계약과 함께 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.  자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>선행 조건

[Azure Portal를 사용 하 여 Azure 전면 도어에 대 한 waf 정책 만들기](waf-front-door-create-portal.md)에 설명 된 지침에 따라 front 문에 대 한 기본 waf 정책을 만듭니다.

## <a name="enable-bot-protection-rule-set"></a>Bot 보호 규칙 집합 사용

1. 이전 섹션에서 만든 기본 정책 페이지의 **설정**에서 **규칙**을 클릭 합니다.
2. 세부 정보 페이지의 **규칙 관리** 섹션 아래에 있는 드롭다운 메뉴에서 **BotProtection-preview-0.1**규칙 앞에 있는 확인란을 선택 하 고 위에서 **저장** 을 선택 합니다.
    
   ![Bot 보호 규칙](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>다음 단계

- [WAF를 모니터링](waf-front-door-monitor.md)하는 방법을 알아봅니다.
