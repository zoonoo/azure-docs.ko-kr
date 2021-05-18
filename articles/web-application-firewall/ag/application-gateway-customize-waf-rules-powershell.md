---
title: PowerShell을 사용하여 규칙 사용자 지정
titleSuffix: Azure Web Application Firewall
description: 이 문서에서는 PowerShell을 통해 Application Gateway에서 Web Application Firewall 규칙을 사용자 지정하는 방법을 설명합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048517"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>PowerShell을 사용하여 Web Application Firewall 규칙 사용자 지정

Azure Application Gateway WAF(Web Application Firewall)는 웹 애플리케이션을 보호합니다. 이러한 보호 기능은 OWASP(Open Web Application Security Project) CRS(코어 규칙 세트)을 통해 제공됩니다. 일부 규칙은 거짓 긍정의 원인이 되어 실제 트래픽을 차단할 수도 있습니다. 이러한 이유로 Application Gateway는 규칙 그룹 및 규칙을 사용자 지정하는 기능을 제공합니다. 특정 규칙 그룹 및 규칙에 대한 자세한 내용은 [Web Application Firewall CRS 규칙 그룹 및 규칙 목록](application-gateway-crs-rulegroups-rules.md)을 참조하세요.

## <a name="view-rule-groups-and-rules"></a>규칙 그룹 및 규칙 보기

다음은 WAF가 활성화된 애플리케이션 게이트웨이에서 구성 가능한 규칙 및 규칙 그룹을 보는 방법을 보여주는 코드 예제입니다.

### <a name="view-rule-groups"></a>규칙 그룹 보기

다음 예제에서는 규칙 그룹을 보는 방법을 보여 줍니다.

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

다음 출력은 이전 예제에서 잘린 응답입니다.

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>규칙 사용 안 함

다음 예제는 애플리케이션 게이트웨이에서 규칙 `911011` 및 `911012`을 비활성화합니다.

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>필수 규칙

다음 목록에는 WAF가 방지 모드에 있는 동안 요청을 차단하도록 하는 조건이 포함되어 있습니다(검색 모드에서 예외로 기록됨). 구성하거나 사용하지 않도록 설정할 수 없습니다.

* 본문 검사가 꺼져 있지 않은 경우 요청 본문을 구문 분석하지 못하면 요청이 차단됩니다(XML, JSON, 양식 데이터).
* 파일이 없는 요청 본문 데이터 길이가 구성된 제한보다 큽니다.
* 파일을 포함한 요청 본문이 제한보다 큽니다.
* WAF 엔진

CR 3.x 특정:

* 인바운드 변칙 점수가 임계값을 초과했습니다.

## <a name="next-steps"></a>다음 단계

사용하지 않는 규칙을 구성한 후에 WAF 로그를 보는 방법을 알아볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
