---
title: "Azure Application Gateway에서 웹 응용 프로그램 방화벽 규칙 사용자 지정 - PowerShell | Microsoft Docs"
description: "이 페이지에서는 PowerShell을 통해 Application Gateway에서 웹 응용 프로그램 방화벽 규칙을 사용자 지정하는 방법을 설명합니다."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 7ed79af5654939c86ca9449be2a59aa0be1136dc
ms.contentlocale: ko-kr
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-powershell"></a>PowerShell을 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Application Gateway 웹 응용 프로그램 방화벽은 웹 응용 프로그램을 보호합니다. 이러한 보호 기능은 OWASP CRS 규칙 집합을 통해 제공됩니다. 일부 규칙은 거짓 긍정의 원인이 되어 실제 트래픽을 차단할 수도 있습니다.  이러한 이유로 Application Gateway는 Application Gateway를 지원하는 웹 응용 프로그램 방화벽의 규칙 그룹 및 규칙을 사용자 지정하는 기능을 제공합니다. 특정 규칙 그룹 및 규칙에 대한 자세한 내용은 [웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙](application-gateway-crs-rulegroups-rules.md)을 참조하세요.

## <a name="view-rule-groups-and-rules"></a>규칙 그룹 및 규칙 보기

다음은 WAF가 활성화된 응용 프로그램 게이트웨이에서 구성 가능한 규칙 및 규칙 그룹을 보는 방법을 보여 주는 예제입니다.

### <a name="view-rule-groups"></a>규칙 그룹 보기

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

다음은 이전 예제에서 잘린 응답입니다.

```
OWASP (Ver. 3.0):

    REQUEST-910-IP-REPUTATION:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            910011     Rule 910011
            910012     Rule 910012
            ...        ...

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            ...        ...

OWASP (Ver. 2.2.9):

    crs_20_protocol_violations:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            960911     Invalid HTTP Request Line
            981227     Apache Error: Invalid URI in Request.
            960000     Attempted multipart/form-data bypass
            ...        ...
```

## <a name="disable-rules"></a>규칙 사용 안 함

다음 예제는 응용 프로그램 게이트웨이에서 규칙 `910018` 및 `910017`을 비활성화합니다.

```azurecli
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>다음 단계

사용하지 않을 규칙을 구성했으면 [Application Gateway 진단](application-gateway-diagnostics.md#diagnostic-logging)을 방문하여 WAF 로그를 보는 방법에 대해 알아보세요.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
