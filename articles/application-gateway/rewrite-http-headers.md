---
title: Azure Application Gateway의 HTTP 헤더 다시 쓰기 | Microsoft Docs
description: 이 문서에서는 Azure Application Gateway의 HTTP 헤더 다시 쓰기 기능 개요를 제공합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: efb7b46919066beb1382d70b676a2115ea0fb8ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544155"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Azure Application Gateway의 HTTP 헤더 다시 쓰기(공개 미리 보기)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 헤더를 통해 클라이언트와 서버는 요청 또는 응답을 사용하여 추가 정보를 전달할 수 있습니다. HSTS 같은 보안 관련 헤더 필드를 추가 하는 등 몇 가지 중요 한 시나리오를 수행 하면 이러한 HTTP 헤더를 다시 작성/X-XSS-보호에서 포트 정보를 제거 하는 중요 한 정보를 통해 확인할 수 있습니다 하는 필드 응답 헤더를 제거 합니다. X-전달 기능에 대 한 헤더 등 Application gateway는 추가, 제거 또는 HTTP 요청 및 응답 헤더를 요청 하는 동안 업데이트 하는 기능을 지원 하 고 응답 패킷의 클라이언트와 백 엔드 풀 간에 이동 합니다. 또한, 특정 조건이 충족 될 경우에 지정 된 헤더는 다시 작성할 수 있도록 조건을 추가 하는 기능을 제공 합니다.
> [!NOTE]
>
> HTTP 헤더 다시 쓰기 지원은 [새 SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)에만 제공됩니다.

## <a name="headers-supported-for-rewrite"></a>지원 되는 헤더를 다시 작성

기능을 사용 하면 요청 및 응답 헤더 호스트, 연결 및 업그레이드를 차단에서 모든 헤더를 다시 작성할 수 있습니다. 또한 사용자 지정 헤더를 만들고 요청 및 응답을 통해 라우팅되지에 추가할 응용 프로그램 게이트웨이 사용할 수 있습니다. 

## <a name="rewrite-conditions"></a>조건을 다시 작성

하나 이상의 조건이 충족 될 경우에 다시 작성 하는 HTTP (S) 요청 및 응답의 콘텐츠를 평가 하 고 헤더를 수행할 수 있습니다 조건을 다시 쓰기를 사용 합니다. 다음 3 가지 유형의 변수는 HTTP (S) 요청 및 응답의 콘텐츠를 평가 하는 application gateway에서 사용 됩니다.

- 요청의 HTTP 헤더
- 응답의 HTTP 헤더
- 응용 프로그램 게이트웨이 서버 변수

인지 여부 지정된 된 변수가 있는 지정된 된 변수가 특정 값을 정확 하 게 일치 하는지 여부 지정된 된 변수는 특정 패턴을 정확 하 게 일치 하는지 여부를 평가 하는 조건을 사용할 수 있습니다. [Perl 호환 일반 식 (PCRE) 라이브러리](https://www.pcre.org/) 조건에 일치 하는 정규식 패턴을 구현 하는 데 사용 됩니다. 정규식 구문에 대 한 자세한 내용은 참조는 [Perl 정규식 man 페이지](http://perldoc.perl.org/perlre.html)합니다.

## <a name="rewrite-actions"></a>작업을 다시 작성

다시 작성 작업은 다시 작성 하려는 요청 및 응답 헤더 및 원본 헤더를 다시 작성 해야 하는 새 값을 지정 하는 데 사용 됩니다. 새 헤더 만들기, 기존 헤더의 값을 수정 하거나 기존 헤더를 삭제 합니다. 값의 형식에 새 헤더 또는 기존 헤더 값을 설정할 수 있습니다.

- 텍스트 
- 요청 헤더: 요청 헤더를 지정 하려면 구문을 사용 해야 {http_req_*headerName*}
- 응답 헤더: 응답 헤더를 지정 하려면 구문을 사용 해야 {http_resp_*headerName*}
- 서버 변수: 서버 변수를 지정 하려면 구문을 사용 해야 {var_*serverVariable*}
- 텍스트, 요청 헤더, 응답 헤더 및 서버 변수의 조합입니다.

## <a name="server-variables"></a>서버 변수

Application gateway는 클라이언트의 IP 주소 또는 웹 브라우저 종류와 같은 연결에서 서버, 클라이언트 및 현재 요청을 사용 하 여 연결 하는 방법에 대 한 유용한 정보를 저장할 서버 변수를 사용 합니다. 이러한 변수는 폼이 게시는 새 페이지가 로드 될 때 또는 같은 동적으로 변경 합니다. 다시 쓰기 상태를 평가 하 고 헤더를 다시 작성 하려면 이러한 서버 변수를 사용할 수 있습니다. 

Application gateway는 다음 서버 변수를 지원 합니다.

| 지원되는 서버 변수 | 설명                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | "X-전달 기능에 대 한" 클라이언트 요청 헤더 필드를 포함 합니다 `client_ip` (이 표 아래에서 설명) 변수 형식 (IP1, IP2, IP3,...)를 추가 합니다. "X-전달 기능에 대 한" 필드는 클라이언트 요청 헤더에 없는 경우는 `add_x_forwarded_for_proxy` 변수 값과 같음는 `$client_ip` 변수입니다. 이 변수는 고객에 게는 포트 정보가 없는 IP 주소만 포함 하는 헤더는 Application Gateway에서 설정 된 X-전달 기능에 대 한 헤더를 다시 작성 하려는 시나리오에서 특히 유용 합니다. |
| ciphers_supported          | 클라이언트에서 지원되는 암호 목록을 반환합니다.          |
| ciphers_used               | 설정된 SSL 연결에 사용되는 암호를 반환합니다. |
| client_ip                  | Application gateway는 요청을 수신 하는 클라이언트의 IP 주소입니다. Application gateway 및 원래 클라이언트 전에 역방향 프록시를 다음 없는 경우 *client_ip* 역방향 프록시의 IP 주소를 반환 합니다. |
| client_port                | 클라이언트 포트.                                                  |
| client_tcp_rtt             | 클라이언트 TCP 연결에 대한 정보이며, TCP_INFO 소켓 옵션을 지원하는 시스템에서 사용할 수 있습니다. |
| client_user                | HTTP 인증을 사용하는 경우 인증을 위해 제공되는 사용자 이름. |
| host                       | 우선 순위 순서대로: 요청 줄의 호스트 이름 또는 "호스트" 요청 헤더 필드의 호스트 이름 또는 요청과 일치하는 서버 이름 |
| cookie_*name*              | 합니다 *이름을* 쿠키                                            |
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

## <a name="rewrite-configuration"></a>다시 작성 구성

HTTP 헤더 다시 쓰기를 구성 해야 합니다.

1. http 헤더를 다시 쓰는 데 필요한 새 개체를 만듭니다.

   - **작업을 다시 작성**: 요청 및 요청 헤더 필드를 다시 작성 하려는 원본 헤더를 다시 작성 해야 하는 새 값을 지정 하는 데 사용 합니다. 연결할 하나 더 많은 다시 쓰기 조건을 다시 작성 작업을 선택할 수 있습니다.

   - **조건을 다시 작성**: 선택적 구성 이며 재작성 조건에 추가 하는 경우 HTTP (S) 요청 및 응답의 콘텐츠는 계산 합니다. 재작성 조건과 사용 하 여 연결을 다시 작성 작업을 실행 하는 결정 재작성 조건을 사용 하 여 HTTP (S) 요청 또는 응답와 일치 하는지 여부에 따라 달라 집니다. 

     둘 이상의 조건을 사용 하 여 연결 된 경우 작업을 다음 작업을 실행할지 즉, 모든 조건이 충족 되는 경우에, 논리적 AND 연산을 수행 됩니다.

   - **재작성 규칙**: 여러 다시 쓰기 작업을 포함 하는 다시 쓰기 규칙-조건 조합을 다시 작성 합니다.

   - **규칙 순서**: 실행 되는 다양 한 다시 쓰기 규칙 순서를 확인할 수 있습니다. 다시 쓰기 집합의 다시 쓰기 규칙이 여러 개 있을 때 유용 합니다. 낮은 규칙 순서 값을 사용 하 여 다시 쓰기 규칙을 먼저 실행 됩니다. 두 다시 쓰기 규칙을 규칙 순서를 제공 하는 경우 실행 순서 비결 수 됩니다.

   - **집합을 다시 작성**: 요청 라우팅 규칙에 연결 될 여러 다시 쓰기 규칙을 포함 합니다.

2. 다시 쓰기 집합을 연결 해야 합니다 (*rewriteRuleSet*) 라우팅 규칙을 사용 하 여 합니다. 즉, 재작성 구성이 라우팅 규칙을 통해 원본 수신기에 연결 되었습니다. 기본 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 원본 수신기와 연결되어 글로벌 헤더 다시 쓰기가 됩니다. 경로 기반 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 URL 경로 맵에 정의됩니다. 따라서 사이트의 특정 경로 영역에만 적용됩니다.

여러 http 헤더 재작성 집합을 만들 수 있습니다 하 고 각 다시 쓰기 집합 리스너가 여러 개 적용할 수 있습니다. 그러나 특정 수신기로 다시 작성 하나에 적용할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

헤더 다시 작성 해야 하는 일반적인 시나리오 중 일부는 아래 설명 되어 있습니다.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>X-전달 기능에 대 한 헤더에서 포트 정보를 제거 합니다.

응용 프로그램 게이트웨이 백 엔드에 요청 전달 하기 전에 모든 요청에 X-전달 기능에 대 한 헤더를 삽입 합니다. 이 헤더의 형식은 쉼표로 구분 된 목록을 ip: port입니다. 그러나 백 엔드 서버 IP 주소 포함 하도록 헤더를 요구 하는 위치 하는 시나리오가 있을 수 있습니다. 이러한 시나리오를 수행 하는 것에 대 한 헤더 재작성 X-전달 기능에 대 한 헤더에서 포트 정보를 제거 하려면 사용할 수 있습니다. 이 작업을 수행 하는 한 가지 방법은 add_x_forwarded_for_proxy server 변수에 헤더를 설정 하는 것입니다. 

![포트를 제거 합니다.](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>리디렉션 URL을 수정

백 엔드 응용 프로그램에 리디렉션 응답을 보내는 경우에 백 엔드 응용 프로그램에서 지정 된 것을 다른 URL로 클라이언트를 리디렉션하는 것이 좋습니다. 이러한 시나리오 중 하나 경우 app service 응용 프로그램 게이트웨이 뒤에 호스팅됩니다 리디렉션을 상대 경로 (contoso.azurewebsites.net/path1에서 contoso.azurewebsites.net/path2 리디렉션) 하기 위해 클라이언트가 필요 합니다. 

앱 서비스는 다중 테 넌 트 서비스 이므로 사용 하 여 호스트 헤더는 요청에 올바른 끝점으로 라우팅합니다. 앱 서비스의 기본 도메인 이름에 *. azurewebsites.net (예: contoso.azurewebsites.net) application gateway의 도메인 이름 (예: contoso.com) 다릅니다. 클라이언트에서 원래 요청에 호스트 이름으로 application gateway의 도메인 이름이 contoso.com, application gateway 변경 호스트 이름을 contoso.azurewebsites.net, app service를 올바른 끝점으로 라우팅할 수 있도록 합니다. App service가 리디렉션 응답을 보내면 사용은 동일한 호스트 응답의 location 헤더에 있는 application gateway에서 수신한 요청 합니다. 따라서 클라이언트 응용 프로그램 게이트웨이 (contoso.com/path2)를 통하지 않고 contoso.azurewebsites.net/path2에 직접 요청할 됩니다. Application gateway를 우회 바람직하지 않습니다. 

Application gateway의 도메인 이름에 location 헤더에 호스트 이름을 설정 하 여이 문제를 해결할 수 있습니다. 이 위해 응답의 location 헤더를 입력 하 여 azurewebsites.net를 포함 하는 경우 계산 되는 조건을 사용 하 여 다시 쓰기 규칙을 만들 수 있습니다 `(https?):\/\/.*azurewebsites\.net(.*)$` 패턴으로 application gateway의 한 위치 헤더를 다시 작성 작업을 수행 호스트 이름을 입력 하 여 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 헤더 값으로.

![위치 헤더를 수정 합니다.](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>취약성을 방지 하기 위해 HTTP 보안 헤더를 구현 합니다.

응용 프로그램 응답에 필요한 헤더를 구현 하 여 여러 가지 보안 취약점을 해결할 수 있습니다. 이러한 보안 헤더의 일부는 X XSS 보호, 보안-전송-Strict, 콘텐츠-보안 정책, 등입니다. 모든 응답에 대 한 이러한 헤더를 설정 하려면 응용 프로그램 게이트웨이 사용할 수 있습니다.

![보안 헤더](media/rewrite-http-headers/security-header.png)

## <a name="limitations"></a>제한 사항

- 연결, 업그레이드 및 호스트 헤더를 다시 작성 아직 지원 되지 않습니다.

- 헤더 이름은 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)에 정의된 대로 영숫자 문자 및 특정 기호를 포함할 수 있습니다. 그러나 현재 "밑줄"(\_) 특수 문자는 헤더 이름에서 지원되지 않습니다. 

## <a name="need-help"></a>도움 필요 시

이 기능과 관련하여 도움이 필요한 경우 [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com)으로 문의하세요.

## <a name="next-steps"></a>다음 단계

HTTP 헤더를 다시 작성 하는 방법에 알아보려면 다음을 참조 하세요.

-  [Azure portal을 사용 하 여 HTTP 헤더를 다시 작성](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [Azure PowerShell을 사용 하 여 HTTP 헤더를 다시 작성](add-http-header-rewrite-rule-powershell.md)