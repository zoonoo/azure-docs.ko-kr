---
title: Azure WAF (웹 응용 프로그램 방화벽) v2 사용자 지정 규칙 만들기 및 사용
description: 이 문서에서는 Azure 애플리케이션 게이트웨이에서 WAF (웹 응용 프로그램 방화벽) v2 사용자 지정 규칙을 만드는 방법을 설명 합니다.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 8cf82ce9ed4a9dc701c016f15224d6adfa299736
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263593"
---
# <a name="create-and-use-web-application-firewall-v2-custom-rules"></a>웹 응용 프로그램 방화벽 v2 사용자 지정 규칙 만들기 및 사용

Azure 애플리케이션 게이트웨이 WAF (웹 응용 프로그램 방화벽) v2는 웹 응용 프로그램에 대 한 보호를 제공 합니다. 이 보호는 OWASP (Open Web Application Security Project) 핵심 규칙 집합에서 제공 됩니다. 경우에 따라 특정 요구 사항을 충족 하기 위해 사용자 지정 규칙을 만들어야 할 수도 있습니다. WAF 사용자 지정 규칙에 대 한 자세한 내용은 [Overview 개요를 참조 하세요. 사용자 지정 웹 응용 프로그램 방화벽 규칙 @ no__t-0.

이 문서에서는 WAF v2에서 만들고 사용할 수 있는 몇 가지 예제 사용자 지정 규칙을 보여 줍니다. Azure PowerShell를 사용 하 여 사용자 지정 규칙으로 WAF를 배포 하는 방법을 알아보려면 [Azure PowerShell를 사용 하 여 웹 응용 프로그램 방화벽 사용자 지정 규칙 구성](configure-waf-custom-rules.md)을 참조 하세요.

> [!NOTE]
> Application gateway에서 WAF 계층을 사용 하지 않는 경우 application gateway를 WAF 계층으로 업그레이드 하는 옵션이 오른쪽 창에 표시 됩니다.

![WAF 사용][fig1]

## <a name="example-1"></a>예제 1

웹 사이트 탐색에서 차단 하려는 *evilbot* 이라는 봇이 있음을 알고 있습니다. 이 예제에서는 요청 헤더의 사용자 에이전트 *evilbot* 를 차단 합니다.

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

해당 하는 JSON 코드는 다음과 같습니다.

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

이 사용자 지정 규칙을 사용 하 여 배포 된 WAF를 보려면 [Azure PowerShell를 사용 하 여 웹 응용 프로그램 방화벽 사용자 지정 규칙 구성](configure-waf-custom-rules.md)을 참조 하세요.

### <a name="example-1a"></a>예 1a

정규식을 사용 하 여 동일한 작업을 수행할 수 있습니다.

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

해당 하는 JSON 코드는 다음과 같습니다.

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

198.168.5.0/24 범위의 IP 주소에 대 한 모든 요청을 차단 하려고 합니다.

이 예에서는 IP 주소 범위에서 들어오는 모든 트래픽을 차단 합니다. 규칙 이름은 *myrule1*이 고 우선 순위는 100로 설정 됩니다.

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

해당 하는 JSON 코드는 다음과 같습니다.

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

해당 하는 핵심 규칙 집합 규칙은 다음과 같습니다.

  `SecRule REMOTE_ADDR "@ipMatch 192.168.5.0/24" "id:7001,deny"`

## <a name="example-3"></a>예 3

이 예에서는 사용자 에이전트 *evilbot*및 192.168.5.0/24 범위의 트래픽을 차단 하려고 합니다. 이 결과를 얻기 위해 별도의 두 일치 조건을 만든 다음 둘 다 동일한 규칙에 둘 수 있습니다. 이 방법을 사용 하면 사용자 에이전트 헤더의 두 *evilbot* 192.168.5.0/24 범위의 IP 주소 *와* 일치 하는 경우 요청이 차단 됩니다.

논리: p *및* q

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

해당 하는 JSON 코드는 다음과 같습니다.

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

## <a name="example-4"></a>예제 4

이 예에서는 요청이 IP 주소 범위 *192.168.5.0/24*외부에 있거나 사용자 에이전트 문자열이 *chrome* 이 아닌 경우 (즉, 사용자가 chrome 브라우저를 사용 하지 않는 경우) 차단 하려고 합니다. 이 논리는 *또는*를 사용 하기 때문에 다음 예제와 같이 두 조건은 별도의 규칙에 있습니다. 트래픽을 차단 하려면 *myrule1* 와 *myrule2* 가 모두 일치 해야 합니다.

논리: *not* (p *및* q *) = p가 아니거나 no* *q입니다* .

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

해당 하는 JSON 코드는 다음과 같습니다.

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

## <a name="example-5"></a>예제 5

사용자 지정 SQLI를 차단 하려고 합니다. 여기서 사용 되는 논리는 *또는* 이 고 모든 값이 *RequestUri*에 있으므로 *matchvalues* 는 모두 쉼표로 구분 된 목록에 있을 수 있습니다.

논리: p *또는* q *또는* r

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
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

해당 하는 JSON 코드는 다음과 같습니다.

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 100
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

대체 Azure PowerShell 코드는 다음과 같습니다.

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

해당 하는 JSON 코드는 다음과 같습니다.

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

사용자 지정 규칙을 만든 후 WAF 로그를 보는 방법에 대해 알아볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
