---
title: Azure Application Gateway를 사용 하 여 HTTP 헤더를 다시 작성 | Microsoft Docs
description: 이 문서에서는 Azure Application Gateway의 HTTP 헤더를 다시 작성 하는 개요를 제공 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: 9160d300270bf1ab5043bee632d27bcc4b7bf332
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476026"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Application Gateway를 사용 하 여 HTTP 헤더를 다시 작성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 헤더에는 클라이언트와 서버 요청 또는 응답을 사용 하 여 추가 정보를 전달할 수 있습니다. 이러한 헤더를 다시 작성 하 여, HSTS 같은 / X XSS 보호 보안 관련 헤더 필드를 추가 하 고, 중요 한 정보를 표시할 수 있는 응답 헤더 필드를 제거 하 고, 포트 정보를 제거와 같은 중요 한 작업을 수행할 수 있습니다. X-전달 기능에 대 한 헤더입니다.

Application Gateway를 사용 하면 추가, 제거 또는 요청 하는 동안 HTTP 요청 및 응답 헤더를 업데이트할 수 있습니다 하 고 응답 패킷의 클라이언트와 백 엔드 풀 간에 이동 합니다. 및 지정 된 헤더는 특정 조건이 충족 될 경우에 다시 작성 하도록 조건을 추가할 수 있습니다.

Application Gateway에 여러 항목을 지원 [서버 변수](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) 요청 및 응답에 대 한 추가 정보를 저장 하는 데 도움이 되도록 합니다. 이렇게 하면 보다 쉽게 강력한 다시 쓰기 규칙을 만들 수 있습니다.

> [!NOTE]
>
> HTTP 헤더 재작성 지원에만 제공 됩니다는 [Standard_V2 및 WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md)합니다.

![헤더를 다시 작성](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>지원 되는 헤더

요청 및 응답, 호스트, 연결 및 업그레이드 헤더를 제외 하 고 모든 헤더를 다시 작성할 수 있습니다. 또한 사용자 지정 헤더를 만들고 요청 및 응답을 통해 라우팅되지에 추가할 응용 프로그램 게이트웨이 사용할 수 있습니다.

## <a name="rewrite-conditions"></a>조건을 다시 작성

HTTP (S) 요청 및 응답의 콘텐츠를 평가 하 고 머리글을 다시 작성 한 경우에 수행을 재작성 조건을 사용할 수 있습니다 또는 이상의 조건이 충족 될 합니다. Application gateway는 HTTP (S) 요청 및 응답의 콘텐츠를 평가 하려면 이러한 유형의 변수를 사용 합니다.

- 요청에서 HTTP 헤더입니다.
- 응답에서 HTTP 헤더입니다.
- 응용 프로그램 게이트웨이 서버 변수입니다.

인지 여부를 지정 된 변수, 지정 된 변수는 특정 값을 일치 하는지 여부를 지정 된 변수는 특정 패턴과 일치 하는지 여부를 평가 하는 조건을 사용할 수 있습니다. 사용 된 [Perl 호환 일반 식 (PCRE) 라이브러리](https://www.pcre.org/) 조건에 일치 하는 정규식 패턴을 설정 하 합니다. 정규식 구문에 대 한 자세한 참조를 [Perl 정규식에 대 한 기본 페이지](https://perldoc.perl.org/perlre.html)합니다.

## <a name="rewrite-actions"></a>작업을 다시 작성

다시 작성 하려는 요청 및 응답 헤더 및 헤더에 대 한 새 값을 지정 하려면 다시 쓰기 작업을 사용 합니다. 새 헤더 만들기, 기존 헤더의 값을 수정 하거나 기존 헤더를 삭제 합니다. 이러한 유형의 값에 새 헤더 또는 기존 헤더 값을 설정할 수 있습니다.

- 텍스트입니다.
- 요청 헤더. 요청 헤더를 지정 하려면 구문을 사용 하 여 {http_req_*headerName*}.
- 응답 헤더입니다. 응답 헤더를 지정 하려면 구문을 사용 하 여 {http_resp_*headerName*}.
- 서버 변수입니다. 서버 변수를 지정 하려면 구문을 사용 하 여 {var_*serverVariable*}.
- 텍스트, 요청 헤더, 응답 헤더 및 서버 변수의 조합입니다.

## <a name="server-variables"></a>서버 변수

Application Gateway는 연결에서 서버에 대 한 유용한 정보, 클라이언트 및 현재 요청을 사용 하 여 연결을 저장할 서버 변수를 사용 합니다. 저장 된 정보의 예로 클라이언트의 IP 주소 및 웹 브라우저 종류를 들 수 있습니다. 서버 변수 동적으로 변경, 예를 들어 새 페이지가 로드 될 때 또는 폼 게시 되 면 합니다. 다시 쓰기 상태를 평가 하 고 헤더를 다시 작성 하려면 이러한 변수를 사용할 수 있습니다.

Application gateway는 이러한 서버 변수를 지원 합니다.

| 변수 이름 | 설명                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | 사용 하 여 X-전달 기능에 대 한 클라이언트 요청 헤더 필드는 `client_ip` 형식 IP1, IP2, IP3, 및 등의 변수를 추가 (이 표의 뒷부분 설명 참조). X-전달 기능에 대 한 필드는 클라이언트 요청 헤더에 없는 경우는 `add_x_forwarded_for_proxy` 변수 값과 같음는 `$client_ip` 변수입니다. 이 변수 헤더에는 포트 정보가 없는 IP 주소만 포함 되도록 응용 프로그램 게이트웨이에서 설정 된 X-전달 기능에 대 한 헤더를 다시 작성 하려는 경우에 특히 유용 합니다. |
| ciphers_supported          | 클라이언트에서 지 원하는 암호화의 목록입니다.          |
| ciphers_used               | 문자열에 SSL 연결이 사용 되는 암호입니다. |
| client_ip                  | Application gateway는 요청을 수신 하는 클라이언트의 IP 주소입니다. Application gateway 및 원래 클라이언트 전에 역방향 프록시가 있는 경우 *client_ip* 역방향 프록시의 IP 주소를 반환 합니다. |
| client_port                | 클라이언트 포트입니다.                                                  |
| client_tcp_rtt             | 클라이언트 TCP 연결에 대 한 정보입니다. TCP_INFO 소켓 옵션을 지 원하는 시스템에서 사용할 수 있습니다. |
| client_user                | HTTP 인증을 사용 하는 경우 인증에 대 한 사용자 이름을 입력 합니다. |
| host                       | 우선 순위 순서로: 요청 줄의 호스트 이름, 호스트 요청 헤더 필드의 호스트 이름 또는 서버 이름 요청을 일치 합니다. |
| cookie_*name*              | 합니다 *이름을* 쿠키입니다.                                            |
| http_method                | URL 요청을 확인 하는 데 사용 된 메서드입니다. 예를 들어 GET 또는 POST입니다. |
| http_status                | 세션 상태입니다. 예를 들어, 200, 400, 또는 403.                       |
| http_version               | 요청 프로토콜입니다. 1.0 일반적으로 HTTP, HTTP/1.1, 또는 HTTP/2.0. |
| query_string               | 다음에 나오는 변수/값 쌍의 목록은 "?" 요청된 된 URL의 합니다. |
| received_bytes             | 요청 (요청 줄, 헤더 및 요청 본문 포함)의 길이입니다. |
| request_query              | 요청 줄에는 인수입니다.                                |
| request_scheme             | 요청 체계: http 또는 https입니다.                            |
| request_uri                | 전체 원래 요청 URI (인수).                   |
| sent_bytes                 | 클라이언트에 보낸 바이트 수입니다.                             |
| server_port                | 요청을 수락 하는 서버의 포트입니다.                 |
| ssl_connection_protocol    | 연결이 SSL의 프로토콜입니다.        |
| ssl_enabled                | "On" 경우 연결이 SSL 모드로 작동합니다. 그렇지 않으면 빈 문자열입니다. |

## <a name="rewrite-configuration"></a>다시 작성 구성

HTTP 헤더 다시 쓰기를 구성 하려면 다음이 단계를 완료 해야 합니다.

1. HTTP 헤더 다시 쓰기에 필요한 개체를 만듭니다.

   - **작업을 다시 작성**: 요청 및 요청 헤더 필드를 다시 작성 하려는 헤더에 대 한 새 값을 지정 하는 데 사용 합니다. 연결할 수 있습니다 또는 다시 작성 작업을 사용 하 여 조건을 다시 작성 더 합니다.

   - **조건을 다시 작성**: 선택적 구성입니다. 다시 쓰기 조건이 HTTP (S) 요청 및 응답의 콘텐츠를 평가 합니다. 다시 작성 작업을 다시 작성 조건과 일치 하는 HTTP (S) 요청 또는 응답 하는 경우 발생 합니다.

     작업을 사용 하 여 하나 이상의 조건에 연결 하는 경우 모든 조건이 충족 되는 경우에 동작이 발생 합니다. 즉, 논리적 AND 연산을 작업이 나타납니다.

   - **재작성 규칙**: 여러 다시 쓰기 작업을 포함 / 조건 조합을 다시 작성 합니다.

   - **규칙 순서**: 재작성 규칙 실행 되는 순서를 확인할 수 있습니다. 이 구성을 다시 쓰기 집합의 다시 쓰기 규칙이 여러 개 있는 경우에 유용 합니다. 낮은 규칙 순서 값이 있는 다시 쓰기 규칙을 먼저 실행 됩니다. 두 다시 쓰기 규칙을 규칙 순서를 할당 하는 경우 실행 순서가 명확 하지 않습니다.

   - **집합을 다시 작성**: 요청 라우팅 규칙을 사용 하 여 관련 된 여러 다시 쓰기 규칙을 포함 합니다.

2. 다시 쓰기 집합 연결 (*rewriteRuleSet*) 라우팅 규칙입니다. 다시 쓰기 구성 라우팅 규칙을 통해 원본 수신기에 연결 됩니다. 기본 라우팅 규칙을 사용 하면 헤더 재작성 구성이 원본 수신기와 연결 된 이며 전체 머리글을 다시 작성 합니다. 경로 기반 라우팅 규칙을 사용 하는 경우 헤더 재작성 구성은 URL 경로 맵에 정의 됩니다. 이 경우 사이트의 특정 경로 영역에만 적용 됩니다.

여러 HTTP 헤더 재작성 집합을 만들고 여러 수신기를 설정 하는 각 다시 쓰기를 적용할 수 있습니다. 그러나 특정 수신기로 다시 작성 하나에 적용할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

헤더 다시 쓰기를 사용 하기 위한 몇 가지 일반적인 시나리오는 다음과 같습니다.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>X-전달 기능에 대 한 헤더에서 포트 정보를 제거 합니다.

Application Gateway 백 엔드에 요청 전달 하기 전에 모든 요청에는 X-전달 기능에 대 한 헤더를 삽입 합니다. 이 헤더에는 IP 포트의 쉼표로 구분 된 목록입니다. 백 엔드 서버의 IP 주소를 포함 하는 헤더만 필요한 시나리오 있을 수 있습니다. X-전달 기능에 대 한 헤더에서 포트 정보를 제거 하려면 헤더 다시 쓰기를 사용할 수 있습니다. 이 작업을 수행 하는 한 가지 방법은 다음과 같습니다. 헤더 add_x_forwarded_for_proxy 서버 변수를 설정 하려면

![포트를 제거 합니다.](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>리디렉션 URL을 수정 합니다.

백 엔드 응용 프로그램에 리디렉션 응답을 보내는 경우에 백 엔드 응용 프로그램에서 지정 된 것을 다른 URL로 클라이언트를 리디렉션하는 것이 좋습니다. 예를 들어, 다음 app service 응용 프로그램 게이트웨이 뒤에 호스팅됩니다 하며 클라이언트가 해당 상대 경로로 리디렉션을 수행 하는 경우이 작업을 수행 하는 것이 좋습니다. (예: contoso.azurewebsites.net/path2 contoso.azurewebsites.net/path1에서 리디렉션에서.)

App Service는 다중 테 넌 트 서비스 이기 때문에 사용 하 여 호스트 헤더는 요청에 올바른 끝점으로 요청을 라우팅합니다. 앱 서비스의 기본 도메인 이름에 *. azurewebsites.net (예: contoso.azurewebsites.net)는 다른 application gateway의 도메인 이름 (예: contoso.com). 클라이언트에서 원래 요청에 호스트 이름으로 application gateway의 도메인 이름 (contoso.com)에 있으므로 응용 프로그램 게이트웨이에 contoso.azurewebsites.net 호스트 이름을 변경 합니다. 이 변경을 수행 하는 app service를 올바른 끝점에 요청을 라우팅할 수 있도록 합니다.

App service가 리디렉션 응답을 보내면 사용은 동일한 호스트 응답의 location 헤더에 있는 application gateway에서 수신한 요청 합니다. 따라서 클라이언트 응용 프로그램 게이트웨이 (contoso.com/path2)를 통하지 않고 contoso.azurewebsites.net/path2에 직접 요청할 됩니다. 적절 하지 않은 응용 프로그램 게이트웨이 무시 합니다.

Application gateway의 도메인 이름에 location 헤더에 호스트 이름을 설정 하 여이 문제를 해결할 수 있습니다.

호스트 이름 바꾸기 단계는 다음과 같습니다.

1. 응답의 location 헤더 azurewebsites.net 있으면 계산 되는 조건을 사용 하 여 다시 쓰기 규칙을 만듭니다. 패턴을 입력 `(https?):\/\/.*azurewebsites\.net(.*)$`합니다.
1. Application gateway의 호스트 이름을 갖도록 location 헤더를 다시 작성 작업을 수행 합니다. 입력 하 여 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 헤더 값으로.

![위치 헤더를 수정 합니다.](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>취약성을 방지 하기 위해 HTTP 보안 헤더를 구현 합니다.

응용 프로그램 응답에 필요한 헤더를 구현 하 여 여러 가지 보안 취약점을 해결할 수 있습니다. 이러한 보안 헤더 X XSS 보호, Strict-전송-보안 및 콘텐츠 보안 정책에 포함 합니다. 모든 응답에 대 한 이러한 헤더를 설정 하려면 응용 프로그램 게이트웨이 사용할 수 있습니다.

![보안 헤더](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>원치 않는 헤더를 삭제 합니다.

HTTP 응답에서 중요 한 정보를 표시 하는 헤더를 제거할 수 있습니다. 예를 들어, 다음 백 엔드 서버 이름, 운영 체제 또는 라이브러리 세부 정보 등의 정보를 제거 하는 것이 좋습니다. 이러한 헤더를 제거 하려면 application gateway를 사용할 수 있습니다.

![헤더를 삭제합니다.](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>헤더의 존재 여부 확인

헤더 또는 서버 변수의 현재 상태에 대 한 HTTP 요청 또는 응답 헤더를 평가할 수 있습니다. 이 평가 특정 헤더가 있는 경우에 헤더 다시 쓰기를 수행 하려는 경우에 유용 합니다.

![헤더의 유무를 확인 하는 중](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>제한 사항

- 응답에 동일한 이름 가진 둘 이상의 머리글이 다른 헤더 응답에서 삭제 한 다음 이러한 헤더 중 하나의 값을 재작성 발생 합니다. 응답에서 Set-cookie 헤더를 여러 개를 가질 수 있으므로 일반적으로 Set-cookie 헤더를 사용 하 여 발생할 수 있습니다. 이러한 시나리오 중 하나는 응용 프로그램 게이트웨이 사용 하 여 app service를 사용 하는 경우 application gateway에서 쿠키 기반 세션 선호도 구성 했습니다. 이 경우 응답 2 Set-cookie 헤더에 포함 됩니다: 즉, app service에서 사용 되는 것 `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` 및 응용 프로그램 게이트웨이 선호도 대 한 다른 즉, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`. 이 시나리오에서는 Set-cookie 헤더 중 하나를 다시 작성은 다른 Set-cookie 헤더 응답에서 제거 될 수 있습니다.

- 연결, 업그레이드 및 호스트 헤더를 다시 작성 현재 지원 되지 않습니다.

- 에 정의 된 대로 헤더 이름은 모든 영숫자 문자 및 특정 기호를 포함할 수 있습니다 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)합니다. 밑줄 현재 지원 되지 않습니다 (\_) 헤더 이름에 특수 문자입니다.

## <a name="next-steps"></a>다음 단계

HTTP 헤더를 다시 작성 하는 방법에 알아보려면 다음을 참조 하세요.

- [Azure portal을 사용 하 여 HTTP 헤더를 다시 작성](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Azure PowerShell을 사용 하 여 HTTP 헤더를 다시 작성](add-http-header-rewrite-rule-powershell.md)
