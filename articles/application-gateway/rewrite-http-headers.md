---
title: Azure Application Gateway의 HTTP 헤더 다시 쓰기 | Microsoft Docs
description: 이 문서에서는 Azure Application Gateway의 HTTP 헤더 다시 쓰기 기능 개요를 제공합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 3e40dbb96b27df4b228c52e7a8f70d047a556c31
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198528"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Azure Application Gateway의 HTTP 헤더 다시 쓰기(공개 미리 보기)

HTTP 헤더를 통해 클라이언트와 서버는 요청 또는 응답을 사용하여 추가 정보를 전달할 수 있습니다. 이러한 HTTP 헤더를 다시 쓰면 HSTS/ X-XSS-보호와 같은 보안 관련 헤더 필드 추가 또는 백 엔드 서버 이름과 같은 중요한 정보를 표시할 수 있는 응답 헤더 필드 제거와 같은 여러 중요한 시나리오를 수행할 수 있습니다.

Application Gateway는 이제 들어오는 HTTP 요청뿐 아니라 나가는 HTTP 응답의 헤더를 다시 쓰는 기능도 지원합니다. 요청/응답 패킷이 클라이언트와 백 엔드 풀 사이를 이동하는 동안 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트할 수 있습니다. 표준 및 비표준 헤더 필드를 모두 다시 쓸 수 있습니다.

> [!NOTE] 
>
> HTTP 헤더 다시 쓰기 지원은 [새 SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)에만 제공됩니다.

Application Gateway 헤더 다시 쓰기 지원은 다음 기능을 제공합니다.

- **글로벌 헤더 다시 쓰기**: 사이트와 관련된 모든 요청 및 응답에 대한 특정 헤더를 다시 쓸 수 있습니다.
- **경로 기반 헤더 다시 쓰기**: 이 유형의 다시 쓰기는 특정 사이트 영역에만 관련된 요청 및 응답(예: /cart/*로 표시된 쇼핑 카트 영역)의 헤더 다시 쓰기를 지원합니다.

이렇게 변경하려면 다음 작업을 수행해야 합니다.

1. http 헤더를 다시 쓰는 데 필요한 새 개체를 만듭니다. 
   - **RequestHeaderConfiguration**:이 개체는 다시 쓰려는 요청 헤더 필드와 원본 헤더를 다시 써야 하는 새 값을 지정하는 데 사용합니다.
   - **ResponseHeaderConfiguration**:이 개체는 다시 쓰려는 응답 헤더 필드와 원본 헤더를 다시 써야 하는 새 값을 지정하는 데 사용합니다.
   - **ActionSet**: 이 개체는 위에 지정된 요청 및 응답 헤더의 구성을 포함합니다. 
   - **RewriteRule**: 이 개체는 위에 지정된 모든 *actionSets*를 포함합니다. 
   - **RewriteRuleSet**: 이 개체는 모든 *rewriteRules*를 포함하며 요청 회람 규칙인 기본 또는 경로 기반에 연결해야 합니다.
2. 그 다음에는 다시 쓰기 규칙 세트를 회람 규칙에 연결해야 합니다. 규칙을 만들면 이 다시 쓰기 구성이 회람 규칙을 통해 원본 수신기에 첨부됩니다. 기본 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 원본 수신기와 연결되어 글로벌 헤더 다시 쓰기가 됩니다. 경로 기반 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 URL 경로 맵에 정의됩니다. 따라서 사이트의 특정 경로 영역에만 적용됩니다.

여러 http 헤더 다시 쓰기 규칙 세트를 만들고 각 다시 쓰기 규칙 세트를 여러 수신자에게 적용할 수 있습니다. 그러나 특정 수신자에게는 오직 한 http 다시 쓰기 규칙 세트만 적용할 수 있습니다.

헤더의 값을 다음 항목으로 다시 쓸 수 있습니다.

- 텍스트 값. 

  *예제:* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- 다른 헤더의 값. 

  *예제 1:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > 요청 헤더를 지정하려면 {http_req_headerName} 구문을 사용해야 합니다.

  *예제 2*:

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > 요청 헤더를 지정하려면 {http_resp_headerName} 구문을 사용해야 합니다.

- 지원되는 서버 변수의 값.

  *예제:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > 서버 변수를 지정하려면 {var_serverVariable} 구문을 사용해야 합니다.

- 위의 조합.

## <a name="server-variables"></a>서버 변수

서버 변수는 웹 서버에 대한 유용한 정보를 저장합니다. 이러한 변수는 연결에서의 현재 요청, 클라이언트와의 연결, 서버에 대한 정보(IP 주소 또는 웹 브라우저 종류 등)를 제공합니다. 이러한 변수는 새 페이지가 로드될 때나 양식이 게시될 때 등에 동적으로 변경됩니다.  사용자는 이러한 변수를 사용하여 요청 헤더뿐 아니라 응답 헤더도 설정할 수 있습니다. 

이 기능은 헤더를 다음 서버 변수로 다시 쓰는 것을 지원합니다.

| 지원되는 서버 변수 | 설명                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | 클라이언트에서 지원되는 암호 목록을 반환합니다.          |
| ciphers_used               | 설정된 SSL 연결에 사용되는 암호를 반환합니다. |
| client_latitude            | 클라이언트 IP 주소에 따라 국가, 지역 및 도시를 확인합니다. |
| client_longitude           | 클라이언트 IP 주소에 따라 국가, 지역 및 도시를 확인합니다. |
| client_port                | 클라이언트 포트.                                                  |
| client_tcp_rtt             | 클라이언트 TCP 연결에 대한 정보이며, TCP_INFO 소켓 옵션을 지원하는 시스템에서 사용할 수 있습니다. |
| client_user                | HTTP 인증을 사용하는 경우 인증을 위해 제공되는 사용자 이름. |
| host                       | 우선 순위 순서대로: 요청 줄의 호스트 이름 또는 "호스트" 요청 헤더 필드의 호스트 이름 또는 요청과 일치하는 서버 이름 |
| http_method                | URL 요청을 만드는 데 사용되는 메서드. 예: GET, POST 등. |
| http_status                | 세션 상태. 예: 200, 400, 403 등                       |
| http_version               | 요청 프로토콜. 일반적으로 "HTTP/1.0", "HTTP/1.1" 또는 "HTTP/2.0" |
| query_string               | 요청된 URL에서 "?" 뒤에 오는 변수-값 쌍 목록. |
| received_bytes             | 요청 길이(요청 줄, 헤더 및 요청 본문 포함) |
| request_query              | 요청 줄의 인수                                |
| request_scheme             | 요청 스키마. "http" 또는 "https"                            |
| request_uri                | 원래 요청 전체 URI(인수 포함)                   |
| sent_bytes                 | 클라이언트로 전송된 바이트 수                             |
| server_port                | 요청을 수락한 서버 포트                 |
| ssl_connection_protocol    | 설정된 SSL 연결의 프로토콜을 반환합니다.        |
| ssl_enabled                | 연결이 SSL 모드에서 작동하면 “on”이고, 그렇지 않으면 빈 문자열입니다. |

## <a name="limitations"></a>제한 사항

- HTTP 헤더 다시 쓰기 기능은 현재 Azure PowerShell, Azure API 및 Azure SDK를 통해서만 사용할 수 있습니다. 포털 및 Azure CLI를 통한 지원은 곧 제공될 예정입니다.

- HTTP 헤더 다시 쓰기 지원은 새 SKU [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)에만 제공됩니다. 이전 SKU에서는 이 기능이 지원되지 않습니다.

- 연결, 업그레이드 및 호스트 헤더 다시 쓰기는 아직 지원되지 않습니다.

- 두 가지 중요한 서버 변수인 client_ip(요청하는 클라이언트의 IP 주소) 및 cookie_*name*(*이름* 쿠키)은 아직 지원되지 않습니다. client_ip 서버 변수는 헤더에 포트 정보를 제외한 클라이언트의 IP 주소만 포함되도록 Application Gateway에서 설정된 x-forwarded-for 헤더를 고객이 다시 쓰려는 시나리오에서 특히 유용합니다.

  두 서버 변수 모두 곧 지원될 예정입니다.

- 조건부로 http 헤더를 다시 쓰는 기능도 곧 제공될 예정입니다.

- 헤더 이름은 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)에 정의된 대로 영숫자 문자 및 특정 기호를 포함할 수 있습니다. 그러나 현재 "밑줄"(\_) 특수 문자는 헤더 이름에서 지원되지 않습니다. 

## <a name="need-help"></a>도움 필요 시

이 기능과 관련하여 도움이 필요한 경우 [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com)으로 문의하세요.

## <a name="next-steps"></a>다음 단계

HTTP 헤더를 다시 쓰는 기능을 배운 후에는 [HTTP 헤더를 다시 쓰는 자동 크기 조정 및 영역 중복 애플리케이션 게이트웨이 만들기](tutorial-http-header-rewrite-powershell.md) 또는 [기존 자동 크기 조정 및 영역 중복 애플리케이션 게이트웨이의 HTTP 헤더 다시 쓰기](add-http-header-rewrite-rule-powershell.md)로 이동하세요.
