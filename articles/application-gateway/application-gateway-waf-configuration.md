---
title: Azure Application Gateway의 웹 애플리케이션 방화벽 요청 크기 제한 및 제외 목록 - Azure Portal
description: 이 문서에서는 Azure Portal을 사용하는 Application Gateway의 웹 애플리케이션 방화벽 요청 크기 제한 및 제외 목록 구성에 대해 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 1/29/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a814fc6e9a72ba92d915821bd1e1694366844555
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791763"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>웹 애플리케이션 방화벽 요청 크기 제한 및 제외 목록

Azure Application Gateway WAF(웹 애플리케이션 방화벽)는 웹 애플리케이션을 보호합니다. 이 문서에서는 WAF 요청 크기 제한 및 제외 목록 구성에 대해 설명합니다.

## <a name="waf-request-size-limits"></a>WAF 요청 크기 제한

![요청 크기 제한](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

웹 애플리케이션 방화벽을 통해 상한과 하한 사이의 범위에서 요청 크기 제한을 구성할 수 있습니다. 사용 가능한 크기 제한 구성은 다음의 두 가지입니다.

- KB 단위로 지정되는 최대 요청 본문 크기 필드는 파일 업로드를 제외한 전체 요청 크기 제한을 제어합니다. 이 필드에는 1KB~128KB 사이의 값을 지정할 수 있습니다. 요청 본문 크기의 기본값은 128KB입니다.
- MB 단위로 지정되는 파일 업로드 제한 필드는 허용되는 최대 파일 업로드 크기를 제어합니다. 중간 SKU의 최대값이 100MB일 때 이 필드에는 대규모 SKU 인스턴스의 최소값으로 1MB를, 최대값으로 500MB를 지정할 수 있습니다. 파일 업로드 제한의 기본값은 100MB입니다.

WAF는 요청 본문 검사를 설정하거나 해제할 수 있는 구성 가능한 노브도 제공합니다. 요청 본문 검사는 기본적으로 사용됩니다. 요청 본문 검사를 해제하면 WAF는 HTTP 메시지 본문의 내용을 평가하지 않습니다. 이러한 경우에도 헤더, 쿠키 및 URI에는 WAF 규칙이 계속 적용됩니다. 요청 본문 검사를 해제하는 경우에는 최대 요청 본문 크기 필드가 적용되지 않으며 설정할 수 없습니다. 요청 본문 검사를 해제하면 128KB보다 큰 메시지를 WAF로 전송할 수 있지만 메시지 본문에서 취약성을 검사하지는 않습니다.

## <a name="waf-exclusion-lists"></a>WAF 제외 목록

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF 제외 목록을 통해 WAF 평가에서 특정 요청 특성을 생략할 수 있습니다. 일반적인 예로는 인증 또는 암호 필드에 사용되는 Active Directory 삽입 토큰이 있습니다. 이러한 특성은 WAF 규칙에서 가양성을 트리거할 수 있는 특수 문자를 포함하는 경우가 많습니다. WAF 제외 목록에 추가된 특성은 구성된 활성 WAF 규칙에서 고려되지 않습니다. 제외 목록의 범위는 전역입니다.

제외 목록에 다음 특성을 추가할 수 있습니다.

* 요청 헤더
* 요청 쿠키
* 요청 본문

   * 다중 파트 데이터 양식
   * XML
   * JSON

정확히 일치하는 요청 헤더, 본문, 쿠키 또는 쿼리 문자열 특성을 지정할 수 있습니다.  또는 필요한 경우 부분 일치를 지정할 수도 있습니다. 제외는 값이 아닌 헤더 필드에 항상 켜져 있습니다. 제외 규칙은 범위에서 전역적이며 모든 페이지 및 규칙에 적용됩니다.

지원되는 일치 기준 연산자는 다음과 같습니다.

- **Equals**:  이 연산자는 정확한 일치에 사용됩니다. 예를 들어 **bearerToken**이라는 헤더를 선택하는 경우 선택기가 **bearerToken**으로 설정된 equals 연산자를 사용합니다.
- **시작**: 이 연산자는 지정된 선택기 값으로 시작하는 모든 필드와 일치합니다.
- **다음으로 끝**:  이 연산자는 지정된 선택기 값으로 끝나는 모든 요청 필드와 일치합니다.
- **포함**: 이 연산자는 지정된 선택기 값을 포함하는 모든 요청 필드와 일치합니다.
- **모든 equals**: 이 연산자에는 모든 요청 필드와 일치합니다. *는 선택기 값 이어야 합니다.

어떤 경우에서든 일치는 대/소문자를 구분하지 않으며, 정규식은 선택기로 사용할 수 없습니다.

### <a name="examples"></a>예

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 Azure PowerShell 코드 조각은 제외 사용 경우를 보여 줍니다.

```azurepowershell
// exclusion 1: exclude request head start with xyz
// exclusion 2: exclude request args equals a

$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestHeaderNames" -SelectorMatchOperator "StartsWith" -Selector "xyz"

$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestArgNames" -SelectorMatchOperator "Equals" -Selector "a"

// add exclusion lists to the firewall config

$firewallConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention -RuleSetType "OWASP" -RuleSetVersion "2.2.9" -DisabledRuleGroups $disabledRuleGroup1,$disabledRuleGroup2 -RequestBodyCheck $true -MaxRequestBodySizeInKb 80 -FileUploadLimitInMb 70 -Exclusions $exclusion1,$exclusion2
```

다음 json 코드 조각은 제외 사용 경우를 보여 줍니다.

```json
"webApplicationFirewallConfiguration": {
          "enabled": "[parameters('wafEnabled')]",
          "firewallMode": "[parameters('wafMode')]",
          "ruleSetType": "[parameters('wafRuleSetType')]",
          "ruleSetVersion": "[parameters('wafRuleSetVersion')]",
          "disabledRuleGroups": [],
          "exclusions": [
            {
                "matchVariable": "RequestArgNames",
                "selectorMatchOperator": "StartsWith",
                "selector": "a^bc"
            }
```

## <a name="next-steps"></a>다음 단계

WAF 설정을 구성한 후에는 WAF 로그 확인 방법을 살펴볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.
