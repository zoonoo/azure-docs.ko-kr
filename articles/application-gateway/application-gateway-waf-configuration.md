---
title: Azure Application Gateway의 웹 애플리케이션 방화벽 요청 크기 제한 및 제외 목록 - Azure Portal
description: 이 문서에서는 Azure Portal을 사용하는 Application Gateway의 웹 애플리케이션 방화벽 요청 크기 제한 및 제외 목록 구성에 대해 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 272c6d2de23b1e89caef3f9bee20a96c5c196cde
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275186"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>웹 애플리케이션 방화벽 요청 크기 제한 및 제외 목록

Azure Application Gateway WAF(웹 애플리케이션 방화벽)는 웹 애플리케이션을 보호합니다. 이 문서에서는 WAF 요청 크기 제한 및 제외 목록 구성에 대해 설명합니다.

## <a name="waf-request-size-limits"></a>WAF 요청 크기 제한

![요청 크기 제한](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

웹 애플리케이션 방화벽을 통해 상한과 하한 사이의 범위에서 요청 크기 제한을 구성할 수 있습니다. 사용 가능한 크기 제한 구성은 다음의 두 가지입니다.

- 최대 요청 본문 크기 필드는 모든 파일을 제외 하는 전체 요청 크기 제한이 업로드 킬로바이트 및 컨트롤에 지정 됩니다. 이 필드에는 1KB~128KB 사이의 값을 지정할 수 있습니다. 요청 본문 크기의 기본값은 128KB입니다.
- MB 단위로 지정되는 파일 업로드 제한 필드는 허용되는 최대 파일 업로드 크기를 제어합니다. 중간 SKU의 최대값이 100MB일 때 이 필드에는 대규모 SKU 인스턴스의 최소값으로 1MB를, 최대값으로 500MB를 지정할 수 있습니다. 파일 업로드 제한의 기본값은 100MB입니다.

WAF는 요청 본문 검사를 설정하거나 해제할 수 있는 구성 가능한 노브도 제공합니다. 요청 본문 검사는 기본적으로 사용됩니다. 요청 본문 검사 꺼져 있으면 WAF는 HTTP 메시지 본문의 콘텐츠를 계산 하지 않습니다. 이러한 경우에도 헤더, 쿠키 및 URI에는 WAF 규칙이 계속 적용됩니다. 요청 본문 검사를 해제하는 경우에는 최대 요청 본문 크기 필드가 적용되지 않으며 설정할 수 없습니다. 요청 본문 검사를 해제하면 128KB보다 큰 메시지를 WAF로 전송할 수 있지만 메시지 본문에서 취약성을 검사하지는 않습니다.

## <a name="waf-exclusion-lists"></a>WAF 제외 목록

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF 제외 목록을 통해 WAF 평가에서 특정 요청 특성을 생략할 수 있습니다. 일반적인 예로는 인증 또는 암호 필드에 사용되는 Active Directory 삽입 토큰이 있습니다. 이러한 특성은 WAF 규칙에서 가양성을 트리거할 수 있는 특수 문자를 포함하는 경우가 많습니다. WAF 제외 목록에 추가된 특성은 구성된 활성 WAF 규칙에서 고려되지 않습니다. 제외 목록의 범위는 전역입니다.

제외 목록에 다음 특성을 추가할 수 있습니다. 선택한 필드의 값은 WAF 규칙에 대해 평가 되지 않습니다. 제외 제거 검사 필드의 값을 나열합니다.

* 요청 헤더
* 요청 쿠키
* 요청 특성 이름 (args)와 같은 제외 요소로 추가할 수 있습니다.

   * 양식 필드 이름
   * XML 엔터티
   * JSON 엔터티
   * URL 쿼리 문자열 인수

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

다음 예제에서는 제외를 사용 하는 방법을 보여 줍니다.

### <a name="example-1"></a>예 1

이 예제에서는 사용자 에이전트 헤더를 제외 하려고 합니다. 사용자 에이전트 헤더는 응용 프로그램 유형, 운영 체제, 소프트웨어 공급 업체 또는 요청 소프트웨어 사용자 에이전트의 소프트웨어 버전을 식별 하는 네트워크 프로토콜 피어를 허용 하는 특성 문자열을 포함 합니다. 자세한 내용은 [User-agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)합니다.

다양 한 이유로이 헤더를 평가 하지 않으려면 있을 수 있습니다. WAF를 확인 하 고이 악의적 이라고 가정 하는 문자열 있을 수 있습니다. 예를 들어, 클래식 SQL 공격 "x = x"는 문자열에서입니다. 경우에 따라 정당한 트래픽을 수 있습니다. 따라서 WAF 평가에서이 헤더를 제외할 수 해야 합니다.

평가판에서 사용자 에이전트 헤더를 제외 하는 다음 Azure PowerShell cmdlet:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>예 2

이 예제에서는 값을 제외 합니다 *사용자* 요청 URL을 통해 전달 되는 매개 변수입니다. 예를 들어, WAF 구조로 악성 콘텐츠 이므로를 차단 하는 문자열을 포함 하도록 사용자 필드에 대 한 사용자 환경에서 일반적인 것입니다.  WAF를 필드에 아무 것도 계산 하지 않습니다 있도록이 경우 user 매개 변수를 제외할 수 있습니다.

평가에서 user 매개 변수를 제외 하는 다음 Azure PowerShell cmdlet:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
따라서 URL **http://www.contoso.com/?user=fdafdasfda** 전달 되는 WAF를 하려면 문자열을 평가 하지 않습니다 것 **fdafdasfda**합니다.

## <a name="next-steps"></a>다음 단계

WAF 설정을 구성한 후에는 WAF 로그 확인 방법을 살펴볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.
