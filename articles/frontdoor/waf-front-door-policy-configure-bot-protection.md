---
title: Azure 프런트 도어 (미리 보기) 사용 하 여 웹 응용 프로그램 방화벽에 대 한 봇 보호를 구성 합니다.
description: 웹 응용 프로그램 방화벽 (WAF)에 대해 알아봅니다.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481626"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>웹 응용 프로그램 방화벽 (미리 보기)에 대 한 봇 보호 구성
이 문서에서는 Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 Azure 웹 응용 프로그램 방화벽 (WAF)의 첫 번째 관문에 대 한 봇 보호 규칙을 구성 하는 방법을 보여 줍니다.

알려진된 악성 IP 주소에서 요청에 사용자 지정 작업을 수행 하 여 WAF에 대 한 관리 되는 봇 보호 규칙 집합을 사용할 수 있습니다. IP 주소는 Microsoft 위협 인텔리전스 피드가에서 제공 됩니다. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) Microsoft 위협 인텔리전스를 구동 하 고 Azure Security Center를 비롯 한 여러 서비스에서 사용 됩니다.

> [!IMPORTANT]
> 봇 보호 규칙 집합은 현재 공개 미리 보기로 제공 하 고 미리 보기 서비스 수준 계약을 사용 하 여 제공 됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.  자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

에 설명 된 지침에 따라 첫 번째 관문에 대 한 기본 WAF 정책을 만듭니다 [Azure portal을 사용 하 여 WAF 정책에 대 한 Azure 프런트 도어 만들기](waf-front-door-create-portal.md)합니다.

## <a name="enable-bot-protection-rule-set"></a>봇 보호 규칙 집합을 사용 하도록 설정

1. 기본 정책 페이지의 이전 섹션에서 만든 **설정을**, 클릭 **규칙**합니다.
2. 세부 정보 페이지에서 아래를 **규칙을 관리할** 섹션에서 드롭 다운 메뉴에서 규칙 앞에 있는 확인란을 선택 **BotProtection-미리 보기-0.1**를 선택한 후 **저장**위에 있습니다.
    
   ![봇 보호 규칙](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>다음 단계

- 설명 하는 방법 [WAF 모니터링](waf-front-door-monitor.md)합니다.
