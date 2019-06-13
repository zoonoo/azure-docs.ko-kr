---
title: Azure 웹 응용 프로그램 방화벽 (WAF) 사용자 지정 규칙 만들기 및 사용
description: 이 문서에서는 Azure Application Gateway에서 웹 응용 프로그램 방화벽 (WAF) 사용자 지정 규칙을 만드는 방법을 설명 합니다.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: bb8c50664261814c7d994c9b879972b1e8b846e4
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66689033"
---
# <a name="create-and-use-web-application-firewall-custom-rules"></a>사용자 지정 웹 응용 프로그램 방화벽 규칙 만들기 및 사용

Azure Application Gateway WAF(웹 애플리케이션 방화벽)는 웹 애플리케이션을 보호합니다. 이 보호 하 여는 OWASP Open Web Application Security Project () 코어 규칙 집합 (CR) 제공 됩니다. 경우에 따라 특정 요구 사항에 맞게 사용자 고유의 사용자 지정 규칙을 만드는 해야 합니다. WAF 사용자 지정 규칙에 대 한 자세한 내용은 참조 하세요. [사용자 지정 웹 응용 프로그램 방화벽 규칙 개요](custom-waf-rules-overview.md)합니다.

이 문서는 만들고 WAF를 사용 하 여 사용할 수 있는 몇 가지 예제에서는 사용자 지정 규칙을 보여줍니다. Azure PowerShell을 사용 하 여 사용자 지정 규칙을 사용 하 여 WAF를 배포 하는 방법에 알아보려면 참조 [Azure PowerShell을 사용 하 여 웹 응용 프로그램 방화벽 구성 사용자 지정 규칙](configure-waf-custom-rules.md)합니다.

>[!NOTE]
> Application Gateway에서 WAF 계층을 사용하지 않는 경우 Application Gateway를 WAF 계층으로 업그레이드하는 옵션이 오른쪽 창에 표시됩니다.

![WAF 사용][fig1]

## <a name="example-1"></a>예 1

봇 라는 것을 알고 있는 *evilbot* 웹 사이트를 크롤링할에서 차단 되도록 합니다. 이 경우 사용자-에이전트에서 차단 됩니다 *evilbot* 요청 헤더에 있습니다.

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

및 해당 JSON 다음과 같습니다.

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

이 사용자 지정 규칙을 사용 하 여 배포 하는 WAF를 참조 하세요 [Azure PowerShell을 사용 하 여 사용자 지정 웹 응용 프로그램 방화벽 규칙 구성](configure-waf-custom-rules.md)합니다.

### <a name="example-1a"></a>예제 1a

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

및 해당 JSON을 추가 합니다.

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

## <a name="example-2"></a>예 2

IP 주소 범위 198.168.5.4/24에서에서 모든 요청을 차단 해야 합니다.

이 예제에서는 IP 주소 범위에서 들어오는 모든 트래픽을 차단할 수 있습니다. 규칙의 이름은 *myrule1* 우선 순위를 100으로 설정 됩니다.

논리: p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

해당 JSON는 다음과 같습니다.

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.4/24"
            ]
          }
        ]
      }
    ]
  }
```

해당 CRS 규칙: `SecRule REMOTE_ADDR "@ipMatch 192.168.5.4/24" "id:7001,deny"`

## <a name="example-3"></a>예 3

예를 들어 사용자 에이전트를 차단 하려는 *evilbot*, 및 범위 192.168.5.4/24의 트래픽에 합니다. 이렇게 하려면 두 개의 별도 일치 조건을 만들 수 있으며 동일한 규칙에 둘 다 배치 키를 누릅니다. 이렇게 하면 둘 다 *evilbot* User-agent 헤더의 **및** IP 주소 범위 192.168.5.4/24에서 차단 됩니다.

논리: p **고** 질문 및 답변

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

해당 JSON는 다음과 같습니다.

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 100, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": true, 
              "matchValues": [ 
                "192.168.5.4/24" 
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

예를 들어 요청은 IP 주소 범위를 벗어난 경우 차단 하려는 *192.168.5.4/24*, 또는 사용자 에이전트 문자열 있지 *chrome* (Chrome 브라우저 사용 하지 않는 사용자를 의미). 이 논리 사용 때문 **또는**, 다음 예제와 같이 별도 규칙에는 두 가지 조건입니다. *myrule1* 하 고 *myrule2* 둘 다는 트래픽을 차단 하도록 일치 해야 합니다.

논리: **되지** (p **하 고** q) = **하지** p **여부** q.

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 200 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

및 해당 JSON을 추가 합니다.

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.4/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 200,
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

사용자 지정 SQLI 차단 하려고 합니다. 사용 되는 논리 이후 같습니다 **또는**, 및 모든 값에는 *RequestUri*모든의 *MatchValues* 쉼표로 구분 된 목록에 있을 수 있습니다.

논리: p **나** q **또는** r

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

해당 JSON입니다.

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

다른 Azure PowerShell:

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
   -Priority 100 `
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
   -Priority 200 `
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
   -Priority 300 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

해당 JSON입니다.

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
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
        "priority": 100,
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
        "priority": 100,
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

해당 ModSecurity 규칙:

`SecRule REQUEST_URI "@contains 1-1" "id:7001,deny"`

`SecRule REQUEST_URI "@contains --" "id:7001,deny"`

`SecRule REQUEST_URI "@contains drop tables" "id:7001,deny"`

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙을 만든 후 WAF 로그를 확인 하는 방법을 알아보십시오. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
