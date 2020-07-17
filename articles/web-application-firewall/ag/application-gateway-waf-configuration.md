---
title: Azure Application Gateway의 웹 애플리케이션 방화벽 요청 크기 제한 및 제외 목록 - Azure Portal
description: 이 문서에서는 Azure Portal와 Application Gateway의 웹 응용 프로그램 방화벽 요청 크기 제한 및 제외 목록 구성에 대 한 정보를 제공 합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: ddf631601510e725d77cc391ad41192a47ab0cf1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84752483"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>웹 응용 프로그램 방화벽 요청 크기 제한 및 제외 목록

Azure 애플리케이션 게이트웨이 WAF (웹 응용 프로그램 방화벽)는 웹 응용 프로그램에 대 한 보호를 제공 합니다. 이 문서에서는 WAF 요청 크기 제한 및 제외 목록 구성에 대해 설명합니다. 이러한 설정은 Application Gateway 연결 된 WAF 정책에 있습니다. WAF 정책에 대 한 자세한 내용은 [Azure 애플리케이션 게이트웨이의 Azure 웹 응용 프로그램 방화벽](ag-overview.md) 및 [Application Gateway에 대 한 웹 응용 프로그램 방화벽 정책 만들기](create-waf-policy-ag.md) 를 참조 하세요.

## <a name="waf-exclusion-lists"></a>WAF 제외 목록

![요청 크기 제한](../media/application-gateway-waf-configuration/waf-policy.png)

WAF 제외 목록을 통해 WAF 평가에서 특정 요청 특성을 생략할 수 있습니다. 일반적인 예로는 인증 또는 암호 필드에 사용되는 Active Directory 삽입 토큰이 있습니다. 이러한 특성은 WAF 규칙에서 가양성을 트리거할 수 있는 특수 문자를 포함하는 경우가 많습니다. WAF 제외 목록에 추가된 특성은 구성된 활성 WAF 규칙에서 고려되지 않습니다. 제외 목록의 범위는 전역입니다.

다음 특성은 이름으로 제외 목록에 추가할 수 있습니다. 선택한 필드의 값은 WAF 규칙에 대해 평가 되지 않지만 해당 이름은 여전히입니다 (아래 예 1 참조, 사용자 에이전트 헤더의 값은 WAF 계산에서 제외 됨). 제외 목록은 필드 값 검사를 제거 합니다.

* 요청 헤더
* 요청 쿠키
* 요청 특성 이름 (args)은 다음과 같이 제외 요소로 추가할 수 있습니다.

   * 양식 필드 이름
   * JSON 엔터티
   * URL 쿼리 문자열 인수

정확히 일치하는 요청 헤더, 본문, 쿠키 또는 쿼리 문자열 특성을 지정할 수 있습니다.  또는 필요한 경우 부분 일치를 지정할 수도 있습니다. 제외 규칙은 범위에서 전역적이며 모든 페이지 및 규칙에 적용됩니다.

지원되는 일치 기준 연산자는 다음과 같습니다.

- **equals**: 정확한 일치에 사용됩니다. 예를 들어 **bearerToken**이라는 헤더를 선택하는 경우 선택기가 **bearerToken**으로 설정된 equals 연산자를 사용합니다.
- **starts with**: 이 연산자는 지정된 선택기 값으로 시작하는 모든 필드와 일치합니다.
- **ends with**: 이 연산자는 지정된 선택기 값으로 끝나는 모든 요청 필드와 일치합니다.
- **contains**: 이 연산자는 지정된 선택기 값을 포함하는 모든 요청 필드와 일치합니다.
- **같음 any**:이 연산자는 모든 요청 필드와 일치 합니다. *는 선택기 값이 됩니다.

어떤 경우에서든 일치는 대/소문자를 구분하지 않으며, 정규식은 선택기로 사용할 수 없습니다.

> [!NOTE]
> 자세한 내용 및 문제 해결 도움말은 [Waf 문제 해결](web-application-firewall-troubleshoot.md)을 참조 하세요.

### <a name="examples"></a>예

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

다음 예에서는 제외를 사용 하는 방법을 보여 줍니다.

#### <a name="example-1"></a>예 1

이 예에서는 사용자 에이전트 헤더를 제외 하려고 합니다. 사용자 에이전트 요청 헤더에는 네트워크 프로토콜 피어에서 요청 하는 소프트웨어 사용자 에이전트의 응용 프로그램 종류, 운영 체제, 소프트웨어 공급 업체 또는 소프트웨어 버전을 식별할 수 있도록 하는 특성 문자열이 포함 되어 있습니다. 자세한 내용은 [사용자 에이전트](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)를 참조 하세요.

이 헤더의 평가를 사용 하지 않도록 설정 하는 데는 여러 가지 이유가 있을 수 있습니다. WAF가 확인 하 고 악성 이라고 가정 하는 문자열이 있을 수 있습니다. 예를 들어, 문자열에서 클래식 SQL 공격 "x = x"가 있습니다. 경우에 따라 합법적인 트래픽이 될 수 있습니다. 따라서 WAF evaluation에서이 헤더를 제외 해야 할 수도 있습니다.

다음 Azure PowerShell cmdlet은 evaluation에서 사용자 에이전트 헤더를 제외 합니다.

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>예제 2

이 예에서는 URL을 통해 요청에 전달 되는 *user* 매개 변수에서 값을 제외 합니다. 예를 들어 사용자의 사용자 필드에는 WAF가 악의적인 콘텐츠로 보고 하는 문자열을 포함 하는 것이 일반적 이라고 가정 합니다.  이 경우 WAF가 필드에서 아무것도 계산 하지 않도록 사용자 매개 변수를 제외할 수 있습니다.

다음 Azure PowerShell cmdlet은 evaluation에서 user 매개 변수를 제외 합니다.

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
따라서 URL `http://www.contoso.com/?user%281%29=fdafdasfda` 이 WAF에 전달 되는 경우에는 **fdafdasfda**문자열을 평가 하지 않지만 매개 변수 이름 **사용자 %281 %29**은 (는) 계속 계산 합니다. 

## <a name="waf-request-size-limits"></a>WAF 요청 크기 제한



웹 애플리케이션 방화벽을 통해 상한과 하한 사이의 범위에서 요청 크기 제한을 구성할 수 있습니다. 사용 가능한 크기 제한 구성은 다음의 두 가지입니다.

- 최대 요청 본문 크기 필드는 킬로바이트 단위로 지정 되며 파일 업로드를 제외한 전체 요청 크기 제한을 제어 합니다. 이 필드에는 1KB~128KB 사이의 값을 지정할 수 있습니다. 요청 본문 크기의 기본값은 128KB입니다.
- MB 단위로 지정되는 파일 업로드 제한 필드는 허용되는 최대 파일 업로드 크기를 제어합니다. 이 필드의 최소값은 1mb이 고 최대값은 다음과 같습니다.

   - v1 Medium WAF 게이트웨이의 100 MB
   - v1 Large WAF 게이트웨이의 경우 500 MB
   - v2 WAF 게이트웨이의 750 MB 

 파일 업로드 제한의 기본값은 100MB입니다.

WAF는 요청 본문 검사를 설정하거나 해제할 수 있는 구성 가능한 노브도 제공합니다. 요청 본문 검사는 기본적으로 사용됩니다. 요청 본문 검사가 꺼져 있으면 WAF는 HTTP 메시지 본문의 내용을 평가 하지 않습니다. 이러한 경우에도 헤더, 쿠키 및 URI에는 WAF 규칙이 계속 적용됩니다. 요청 본문 검사를 해제하는 경우에는 최대 요청 본문 크기 필드가 적용되지 않으며 설정할 수 없습니다. 요청 본문 검사를 해제하면 128KB보다 큰 메시지를 WAF로 전송할 수 있지만 메시지 본문에서 취약성을 검사하지는 않습니다.

## <a name="next-steps"></a>다음 단계

WAF 설정을 구성한 후에는 WAF 로그 확인 방법을 살펴볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.
