---
title: v2 사용자 지정 규칙 만들기 및 사용
titleSuffix: Azure Web Application Firewall
description: 이 문서에서는 Azure 응용 프로그램 게이트웨이에서 WAF(웹 응용 프로그램 방화벽) v2 사용자 지정 규칙을 만드는 방법에 대한 정보를 제공합니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: bfa6690c636e15fa933f50698cd81359600b5c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368312"
---
# <a name="create-and-use-web-application-firewall-v2-custom-rules-on-application-gateway"></a>응용 프로그램 게이트웨이에서 웹 응용 프로그램 방화벽 v2 사용자 지정 규칙 생성 및 사용

Azure 응용 프로그램 게이트웨이의 WAF(웹 응용 프로그램 방화벽) v2는 웹 응용 프로그램에 대한 보호를 제공합니다. 이 보호는 개방형 웹 응용 프로그램 보안 프로젝트(OWASP) 핵심 규칙 집합(CRS)에서 제공됩니다. 경우에 따라 특정 요구 사항에 맞게 사용자 지정 규칙을 직접 만들어야 할 수도 있습니다. WAF 사용자 지정 규칙에 대한 자세한 내용은 [사용자 지정 웹 응용 프로그램 방화벽 규칙 개요를](custom-waf-rules-overview.md)참조하십시오.

이 문서에서는 v2 WAF에서 만들고 사용할 수 있는 몇 가지 사용자 지정 규칙을 보여 줍니다. Azure PowerShell을 사용하여 사용자 지정 규칙으로 WAF를 배포하는 방법을 알아보려면 [Azure PowerShell을 사용하여 웹 응용 프로그램 방화벽 사용자 지정 규칙 구성을](configure-waf-custom-rules.md)참조하세요.

>[!NOTE]
> Application Gateway에서 WAF 계층을 사용하지 않는 경우 Application Gateway를 WAF 계층으로 업그레이드하는 옵션이 오른쪽 창에 표시됩니다.

![WAF 사용][fig1]

## <a name="example-1"></a>예 1

당신은 당신이 당신의 웹 사이트를 크롤링에서 차단하려는 *evilbot라는* 이름의 봇이 있다는 것을 알고있다. 이 경우 요청 헤더에서 사용자 에이전트 *evilbot을* 차단합니다.

논리: p

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

그리고 여기에 해당 JSON입니다 :

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

이 사용자 지정 규칙을 사용하여 배포된 WAF를 보려면 [Azure PowerShell을 사용하여 웹 응용 프로그램 방화벽 사용자 지정 규칙 구성을](configure-waf-custom-rules.md)참조하십시오.

### <a name="example-1a"></a>실시예 1a

정규식을 사용하여 동일한 작업을 수행할 수 있습니다.

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Regex `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

그리고 해당 JSON :

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-2"></a>예제 2

GeoMatch 연산자를 사용하여 미국에서 트래픽을 허용하려고 합니다.

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr `

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator GeoMatch `
   -MatchValue "US" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name "allowUS" `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow
```

그리고 해당 JSON :

```json
  {
    "customRules": [
      {
        "name": "allowUS",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Allow",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "GeoMatch",
            "matchValues": [
              "US"
            ]
          }
        ]
      }
    ]
  }
```



## <a name="example-3"></a>예제 3

범위 198.168.5.0/24의 IP 주소에서 모든 요청을 차단하려고 합니다.

이 예제에서는 IP 주소 범위에서 오는 모든 트래픽을 차단합니다. 규칙의 이름은 *myrule1이고* 우선 순위는 10으로 설정됩니다.

논리: p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

해당 JSON은 다음과 같습니다.

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      }
    ]
  }
```

해당 CRS 규칙:`SecRule REMOTE_ADDR "@ipMatch 192.168.5.0/24" "id:7001,deny"`

## <a name="example-4"></a>예제 4

이 예제에서는 사용자 에이전트 *evilbot*및 범위 192.168.5.0/24의 트래픽을 차단하려고 합니다. 이렇게 하려면 두 개의 별도 일치 조건을 만들고 둘 다 동일한 규칙에 둘 다 넣을 수 있습니다. 이렇게 하면 사용자 에이전트 헤더의 *evilbot과* 범위 192.168.5.0/24의 IP 주소가 모두 일치하는 경우 요청이 차단됩니다. **and**

논리 : **p와** q

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

해당 JSON은 다음과 같습니다.

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 10, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": false, 
              "matchValues": [ 
                "192.168.5.0/24" 
              ] 
            }, 
            { 
              "matchVariable": "RequestHeaders", 
              "selector": "User-Agent", 
              "operator": "Contains", 
              "transforms": [ 
                "Lowercase" 
              ], 
              "matchValues": [ 
                "evilbot" 
              ] 
            } 
        ] 
      } 
    ] 
  } 
```

## <a name="example-5"></a>예제 5

이 예제에서는 요청이 IP 주소 범위 *192.168.5.0/24*외부에 있거나 사용자 에이전트 문자열이 *크롬이* 아닌 경우 차단하려고 합니다(사용자가 Chrome 브라우저를 사용하지 않는 경우). 이 논리는 다음 예제에서 볼 수 있듯이 두 조건이 별도의 규칙에 **사용하거나 사용하므로**두 가지 조건이 서로 다른 규칙에 있습니다. *myrule1과* *myrule2* 모두 트래픽을 차단하기 위해 일치해야합니다.

논리: **아니** (p **및** q) = **아니** p **또는 q.**

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

그리고 해당 JSON :

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "selector": "User-Agent",
            "operator": "Contains",
            "negateCondition": true,
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "chrome"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-6"></a>예제 6

사용자 지정 SQLI를 차단하려고 합니다. 여기에 사용된 논리가 **또는**,에 모든 값이 *RequestUri에*있기 때문에 모든 *MatchValues는* 쉼표로 구분된 목록에 있을 수 있습니다.

논리 : p **또는** Q **또는** r

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri 
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1", "drop tables", "'—" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule4 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

해당 JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 10
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1",
              "drop tables",
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

대체 Azure PowerShell:

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
-Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "drop tables" `
   -NegationCondition $False

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block

$variable3 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition3 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable3 `
   -Operator Contains `
   -MatchValue "’—" `
   -NegationCondition $False

$rule3 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule3 `
   -Priority 30 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

해당 JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "drop tables"
            ]
          }
        ]
      },
      {
        "name": "myrule3",
        "ruleType": "MatchRule",
        "priority": 30,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙을 만든 후 WAF 로그를 보는 방법을 알아볼 수 있습니다. 자세한 내용은 [응용 프로그램 게이트웨이 진단을](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)참조하십시오.

[fig1]: ../media/create-custom-waf-rules/1.png
