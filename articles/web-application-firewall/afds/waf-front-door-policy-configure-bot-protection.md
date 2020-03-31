---
title: Azure 정문(미리 보기)을 사용 하 고 웹 응용 프로그램 방화벽에 대 한 봇 보호 구성
description: WAF(웹 응용 프로그램 방화벽)에 대해 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934667"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>웹 응용 프로그램 방화벽에 대한 봇 보호 구성(미리 보기)
이 문서에서는 Azure 포털을 사용하여 정문에 대한 WAF(Azure 웹 응용 프로그램 방화벽)에서 봇 보호 규칙을 구성하는 방법을 보여 주십니다. CLI, Azure PowerShell 또는 Azure 리소스 관리자 템플릿을 사용하여 봇 보호 규칙을 구성할 수도 있습니다.

> [!IMPORTANT]
> 봇 보호 규칙 세트는 현재 공개 미리 보기이며 미리 보기 서비스 수준 계약과 함께 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.  자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure 포털을 [사용하여 Azure 정문에 대한 WAF 정책 만들기에](waf-front-door-create-portal.md)설명된 지침에 따라 정문에 대한 기본 WAF 정책을 만듭니다.

## <a name="enable-bot-protection-rule-set"></a>봇 보호 규칙 설정 사용

웹 응용 프로그램 방화벽 정책을 만들 때 **관리되는 규칙** 페이지에서 먼저 **관리되는 규칙 집합** 섹션을 찾고 드롭다운 메뉴에서 규칙 **Microsoft_BotManager_1.0** 앞에 있는 확인란을 선택한 다음 **검토 + 만들기를**선택합니다.

   ![봇 보호 규칙](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>다음 단계

- [WAF를 모니터링하는](waf-front-door-monitor.md)방법에 대해 알아봅니다.
