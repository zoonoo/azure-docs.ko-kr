---
title: Azure 애플리케이션 Gateway를 사용 하 여 HTTP 헤더 및 URL 다시 작성 | Microsoft Docs
description: 이 문서에서는 Azure 애플리케이션 게이트웨이에서 HTTP 헤더 및 URL을 다시 작성 하는 방법에 대 한 개요를 제공 합니다.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: surmb
ms.openlocfilehash: 46cb4d0d099cd21db3ce51c337d3b059206bb425
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099298"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Application Gateway를 사용 하 여 HTTP 헤더 및 URL 다시 작성

 Application Gateway 선택 된 요청 및 응답 내용을 다시 작성할 수 있습니다. 이 기능을 사용 하면 Url, 쿼리 문자열 매개 변수를 변환 하 고 요청 및 응답 헤더를 수정할 수 있습니다. 또한 특정 조건이 충족 될 때만 URL 또는 지정 된 헤더를 다시 쓸지 확인 하는 조건을 추가할 수 있습니다. 이러한 조건은 요청 및 응답 정보를 기반으로 합니다.

>[!NOTE]
>HTTP 헤더 및 URL 재작성 기능은 [Application Gateway V2 SKU](application-gateway-autoscaling-zone-redundant.md) 에서만 사용할 수 있습니다.

## <a name="rewrite-types-supported"></a>지원 되는 형식 다시 작성

### <a name="request-and-response-headers"></a>요청 및 응답 헤더

HTTP 헤더를 사용 하면 클라이언트와 서버에서 요청 또는 응답과 함께 추가 정보를 전달할 수 있습니다. 이러한 헤더를 다시 작성 하 여 HSTS/X-XSS 보호와 같은 보안 관련 헤더 필드 추가, 중요 한 정보를 표시할 수 있는 응답 헤더 필드 제거, X 전달 된 헤더에서 포트 정보 제거와 같은 중요 한 작업을 수행할 수 있습니다.

Application Gateway는 요청 및 응답 패킷이 클라이언트와 백 엔드 풀 사이를 이동하는 동안 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트할 수 있습니다.

Azure Portal를 사용 하 여 요청 및 응답 헤더를 Application Gateway 다시 작성 하는 방법을 알아보려면 [여기](rewrite-url-portal.md)를 참조 하세요.

![img](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**지원 되는 헤더**

연결 및 업그레이드 헤더를 제외 하 고 요청 및 응답의 모든 헤더를 다시 작성할 수 있습니다. 응용 프로그램 게이트웨이를 사용 하 여 사용자 지정 헤더를 만들어이 헤더를 통해 라우팅되는 요청 및 응답에 추가할 수도 있습니다.

### <a name="url-path-and-query-string-preview"></a>URL 경로 및 쿼리 문자열 (미리 보기)

Application Gateway에서 URL 재작성 기능을 사용 하면 다음을 수행할 수 있습니다.

* 요청 URL의 호스트 이름, 경로 및 쿼리 문자열을 다시 작성 합니다. 

* 수신기의 모든 요청 또는 설정 된 하나 이상의 조건과 일치 하는 요청에 대 한 URL을 다시 작성 하도록 선택 합니다. 이러한 조건은 요청 및 응답 속성 (요청, 헤더, 응답 헤더 및 서버 변수)을 기반으로 합니다.

* 원래 URL 또는 재작성 된 URL을 기반으로 요청을 라우팅하는 방법 (백 엔드 풀 선택)을 선택 합니다.

Azure Portal를 사용 하 여 URL을 다시 작성 하는 방법에 대 한 자세한 내용은 [여기](rewrite-url-portal.md)를 참조 하십시오. Application Gateway

![img](./media/rewrite-http-headers-url/url-rewrite-overview.png)

>[!NOTE]
> URL 다시 쓰기 기능은 미리 보기 상태 이며 Application Gateway의 Standard_v2 및 WAF_v2 SKU에 대해서만 사용할 수 있습니다. 프로덕션 환경에서는 사용 하지 않는 것이 좋습니다. 미리 보기에 대 한 자세한 내용은 [여기의 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="rewrite-actions"></a>작업 다시 작성

다시 쓰기 작업을 사용 하 여 다시 작성할 URL, 요청 헤더 또는 응답 헤더와 다시 작성 하려는 새 값을 지정 합니다. URL 또는 새 또는 기존 헤더의 값은 다음과 같은 형식 값으로 설정할 수 있습니다.

* 텍스트
* 요청 헤더. 요청 헤더를 지정 하려면 {http_req_*헤드 ername*} 구문을 사용 해야 합니다.
* 응답 헤더입니다. 응답 헤더를 지정 하려면 {http_resp_*헤드 ername*} 구문을 사용 해야 합니다.
* 서버 변수입니다. 서버 변수를 지정 하려면 {var_*Servervariable*} 구문을 사용 해야 합니다. 지원 되는 서버 변수 목록 보기
* 텍스트, 요청 헤더, 응답 헤더 및 서버 변수의 조합입니다. 

## <a name="rewrite-conditions"></a>다시 쓰기 조건

다시 쓰기 조건 (옵션 구성)을 사용 하 여 HTTP 요청 및 응답의 내용을 평가 하 고 하나 이상의 조건이 충족 될 때만 다시 쓰기를 수행할 수 있습니다. 응용 프로그램 게이트웨이는 이러한 형식의 변수를 사용 하 여 요청 및 응답의 콘텐츠를 평가 합니다.

* 요청의 HTTP 헤더
* 응답의 HTTP 헤더
* Application Gateway 서버 변수

조건을 사용 하 여 지정 된 변수가 있는지, 지정 된 변수가 특정 값과 일치 하는지 여부 또는 지정 된 변수가 특정 패턴과 일치 하는지 여부를 평가할 수 있습니다. 


### <a name="pattern-matching"></a>패턴 일치 

Application Gateway는 조건에서 패턴 일치에 정규식을 사용 합니다. [Perl 호환 정규식 (PCRE) 라이브러리](https://www.pcre.org/) 를 사용 하 여 조건에서 정규식 패턴 일치를 설정할 수 있습니다. 정규식 구문에 대 한 자세한 내용은 [Perl 정규식 기본 페이지](https://perldoc.perl.org/perlre.html)를 참조 하세요.

### <a name="capturing"></a>캡처 중

나중에 사용할 부분 문자열을 캡처하려면 조건 regex 정의에서 일치 하는 하위 패턴을 괄호로 묶습니다. 괄호의 첫 번째 쌍은 하위 문자열을 1에, 두 번째 쌍은 2로 저장 합니다. 원하는 만큼 괄호를 사용할 수 있습니다. Perl은 이러한 캡처된 문자열을 나타내기 위해 보다 번호가 매겨진 변수를 계속 해 서 정의 합니다. [Ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm)의 몇 가지 예제는 다음과 같습니다. 

*  /(\d) (\d)/# 두 자리를 일치 시키고 그룹 1과 2로 캡처합니다. 

* /(\d +)/# 하나 이상의 숫자를 일치 시키고 모두 그룹 1로 캡처합니다. 

* /(\d) +/# 숫자를 한 번 이상 일치 시키고 그룹 1에 마지막 캡처

캡처한 후에는 다음 형식을 사용 하 여 작업 집합에서 참조할 수 있습니다.

* 요청 헤더 캡처의 경우 {http_req_headerName_groupNumber}을 (를) 사용 해야 합니다. 예: {http_req_User-Agent_1} 또는 {http_req_User Agent_2}
* 응답 헤더 캡처의 경우 {http_resp_headerName_groupNumber}을 (를) 사용 해야 합니다. 예: {http_resp_Location_1} 또는 {http_resp_Location_2}
* 서버 변수의 경우 {var_serverVariableName_groupNumber}을 (를) 사용 해야 합니다. 예: {var_uri_path_1} 또는 {var_uri_path_2}

전체 값을 사용 하려면 숫자를 언급 하지 않아야 합니다. GroupNumber 없이 {http_req_headerName} 등의 형식을 사용 하면 됩니다.

## <a name="server-variables"></a>서버 변수

Application Gateway 서버 변수를 사용 하 여 서버에 대 한 유용한 정보, 클라이언트에 대 한 연결 및 현재 연결 요청을 저장 합니다. 저장 되는 정보의 예로는 클라이언트의 IP 주소와 웹 브라우저 유형이 있습니다. 서버 변수는 새 페이지가 로드 될 때 또는 양식이 게시 될 때와 같이 동적으로 변경 됩니다. 이러한 변수를 사용 하 여 재작성 조건을 평가 하 고 헤더를 다시 작성할 수 있습니다. 서버 변수 값을 사용 하 여 헤더를 다시 작성 하려면 {var_*serverVariableName*} 구문에서 이러한 변수를 지정 해야 합니다.

Application gateway는 다음 서버 변수를 지원 합니다.

|   변수 이름    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | `client_ip`I p 1, IP2, p 3 등의 형식으로 추가 된 (이 표의 뒷부분에 나오는 설명 참조) 변수와 함께 X로 전달 된 클라이언트 요청 헤더 필드입니다. X 전달-For 필드가 클라이언트 요청 헤더에 없으면 `add_x_forwarded_for_proxy` 변수는 `$client_ip` 변수와 같습니다.   이 변수는 헤더에 포트 정보가 없는 IP 주소만 포함 되도록 Application Gateway에 의해 설정 된 X 전달 된 헤더를 다시 작성 하려는 경우에 특히 유용 합니다. |
| ciphers_supported         | 클라이언트에서 지 원하는 암호화 목록입니다.               |
| ciphers_used              | 설정 된 TLS 연결에 사용 되는 암호화 문자열입니다. |
| client_ip                 | 응용 프로그램 게이트웨이에서 요청을 받은 클라이언트의 IP 주소입니다. 응용 프로그램 게이트웨이와 원래 클라이언트 앞에 역방향 프록시가 있는 경우 *client_ip* 는 역방향 프록시의 ip 주소를 반환 합니다. |
| client_port               | 클라이언트 포트입니다.                                             |
| client_tcp_rtt            | 클라이언트 TCP 연결에 대 한 정보입니다. TCP_INFO 소켓 옵션을 지 원하는 시스템에서 사용할 수 있습니다. |
| client_user               | HTTP 인증을 사용 하는 경우 인증을 위해 제공 되는 사용자 이름입니다. |
| 호스트                      | 이 우선 순위 순서 대로: 요청 줄의 호스트 이름, 호스트 요청 헤더 필드의 호스트 이름 또는 요청과 일치 하는 서버 이름입니다. 예: 요청에서 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* 호스트 값은 *contoso.com* 입니다. |
| cookie_*이름*             | *이름* 쿠키입니다.                                           |
| http_method               | URL 요청을 만드는 데 사용 되는 메서드입니다. 예를 들어 GET 또는 POST입니다. |
| http_status               | 세션 상태입니다. 예: 200, 400 또는 403.           |
| http_version              | 요청 프로토콜입니다. 일반적으로 HTTP/1.0, HTTP/1.1 또는 HTTP/2.0입니다. |
| query_string              | 요청 된 URL에서 "?" 뒤에 오는 변수/값 쌍의 목록입니다. 예: 요청에서 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* query_string 값은 *id = 123&title = fabrikam* 입니다. |
| received_bytes            | 요청 (요청 줄, 헤더 및 요청 본문 포함)의 길이입니다. |
| request_query             | 요청 줄의 인수입니다.                           |
| request_scheme            | 요청 체계: http 또는 https.                           |
| request_uri               | 전체 원래 요청 URI (인수 포함)입니다. 예: 요청에서 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* request_uri 값은 */article.aspx? id = 123&title = fabrikam* 입니다. |
| sent_bytes                | 클라이언트에 전송 된 바이트 수입니다.                        |
| server_port               | 요청을 수락한 서버 포트입니다.              |
| ssl_connection_protocol   | 설정 된 TLS 연결의 프로토콜입니다.               |
| ssl_enabled               | TLS 모드에서 연결이 작동 하는 경우 "On"입니다. 그렇지 않으면 빈 문자열입니다. |
| uri_path                  | 웹 클라이언트에서 액세스 하려는 호스트의 특정 리소스를 식별 합니다. 이는 인수가 없는 요청 URI의 일부입니다. 예: 요청에서 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* uri_path 값은 */article.aspx* 입니다. |

 

## <a name="rewrite-configuration"></a>구성 재작성

재작성 규칙을 구성 하려면 재작성 규칙 집합을 만들고 다시 쓰기 규칙 구성을 추가 해야 합니다.

재작성 규칙 집합에는 다음이 포함 됩니다.

* **요청 라우팅 규칙 연결:** 재작성 구성은 라우팅 규칙을 통해 원본 수신기에 연결 됩니다. 기본 라우팅 규칙을 사용 하는 경우 재작성 구성은 원본 수신기와 연결 되며 전역 헤더 재작성입니다. 경로 기반 라우팅 규칙을 사용 하는 경우 재작성 구성은 URL 경로 맵에 정의 됩니다. 이 경우 사이트의 특정 경로 영역에만 적용 됩니다. 여러 재작성 집합을 만들고 각 재작성 집합을 여러 수신기에 적용할 수 있습니다. 그러나 특정 수신기에 재작성 집합을 하나만 적용할 수 있습니다.

* **다시 작성 조건**: 선택적 구성입니다. 다시 쓰기 조건은 HTTP 요청 및 응답의 콘텐츠를 평가 합니다. HTTP (S) 요청 또는 응답이 재작성 조건과 일치 하면 재작성 작업이 수행 됩니다. 작업에 둘 이상의 조건을 연결 하는 경우 모든 조건이 충족 되는 경우에만 작업이 수행 됩니다. 즉, 작업은 논리적 AND 연산입니다.

* **다시 쓰기 유형**: 3 가지 유형의 다시 쓰기를 사용할 수 있습니다.
   * 요청 헤더 재작성 
   * 응답 헤더를 다시 작성 합니다.
   * URL 재작성: URL 재작성 유형에 3 개의 구성 요소가 있습니다.
      * **URL 경로**: 경로를 다시 쓸 값입니다. 
      * **URL 쿼리 문자열**: 쿼리 문자열을 다시 쓸 값입니다. 
      * **경로 맵 다시 평가**: URL 경로 맵을 다시 확인할 지 여부를 결정 하는 데 사용 됩니다. 선택 하지 않은 상태로 유지 되는 경우 원본 URL 경로는 URL 경로 맵의 경로 패턴과 일치 하는 데 사용 됩니다. True로 설정 되 면 다시 작성 된 경로와 일치 하는 항목을 확인 하기 위해 URL 경로 맵이 다시 계산 됩니다. 이 스위치를 사용 하도록 설정 하면 다른 백 엔드 풀 사후 재작성으로 요청을 라우팅하는 데 도움이 됩니다.

### <a name="common-scenarios-for-header-rewrite"></a>헤더 재작성에 대 한 일반적인 시나리오

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>X 전달 된 헤더에서 포트 정보를 제거 합니다.

Application Gateway는 백 엔드에 요청을 전달 하기 전에 모든 요청에 X 전달 된 헤더를 삽입 합니다. 이 헤더는 IP 포트를 쉼표로 구분한 목록입니다. 백 엔드 서버에는 IP 주소를 포함 하는 머리글만 필요 합니다. 헤더 재작성을 사용 하 여 X 전달 된 헤더에서 포트 정보를 제거할 수 있습니다. 이 작업을 수행 하는 한 가지 방법은 헤더를 add_x_forwarded_for_proxy 서버 변수로 설정 하는 것입니다. 또는 변수 client_ip를 사용할 수도 있습니다.

![포트 제거](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>리디렉션 URL 수정

백 엔드 응용 프로그램에서 리디렉션 응답을 보내는 경우 백 엔드 응용 프로그램에서 지정한 URL과 다른 URL로 클라이언트를 리디렉션할 수 있습니다. 예를 들어 app service가 응용 프로그램 게이트웨이 뒤에서 호스트 되는 경우이 작업을 수행 하 고 클라이언트가 상대 경로에 대 한 리디렉션을 수행 해야 할 수 있습니다. 예를 들어 contoso.azurewebsites.net/path1에서 contoso.azurewebsites.net/path2로의 리디렉션이 있습니다.

App Service은 다중 테 넌 트 서비스 이므로 요청에서 호스트 헤더를 사용 하 여 요청을 올바른 끝점으로 라우팅합니다. App services의 기본 도메인 이름은 application gateway의 도메인 이름과 다른 *. azurewebsites.net (contoso.azurewebsites.net)입니다 (예를 들어 contoso.com). 클라이언트의 원래 요청에는 호스트 이름으로 응용 프로그램 게이트웨이의 도메인 이름 (contoso.com)이 있으므로 application gateway는 호스트 이름을 contoso.azurewebsites.net로 변경 합니다. App service가 요청을 올바른 끝점으로 라우팅할 수 있도록이 변경을 수행 합니다.

App service는 리디렉션 응답을 보낼 때 응용 프로그램 게이트웨이에서 수신 하는 요청의 위치 헤더에 있는 것과 동일한 호스트 이름을 사용 합니다. 따라서 클라이언트는 application gateway (contoso.com/path2)를 통하지 않고 contoso.azurewebsites.net/path2에 직접 요청을 만듭니다. Application gateway를 무시 하는 것은 바람직하지 않습니다.

Location 헤더의 호스트 이름을 application gateway의 도메인 이름으로 설정 하 여이 문제를 해결할 수 있습니다.

호스트 이름을 교체 하는 단계는 다음과 같습니다.

1. 응답의 location 헤더에 azurewebsites.net가 포함 되어 있는지 여부를 평가 하는 다시 쓰기 규칙을 만듭니다. 패턴을 입력 `(https?):\/\/.*azurewebsites\.net(.*)$` 합니다.
2. 응용 프로그램 게이트웨이의 호스트 이름을 포함 하도록 위치 헤더를 다시 작성 하는 작업을 수행 합니다. 헤더 값으로를 입력 하 여이 작업을 수행 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 합니다. 또는 서버 변수를 사용 하 여 `host` 원래 요청과 일치 하도록 호스트 이름을 설정할 수도 있습니다.

![위치 헤더 수정](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>취약성을 방지 하기 위해 보안 HTTP 헤더 구현

응용 프로그램 응답에 필요한 헤더를 구현 하 여 여러 보안 취약점을 해결할 수 있습니다. 이러한 보안 헤더에는 X-XSS 보호, 엄격한 전송 보안 및 콘텐츠 보안 정책이 포함 됩니다. Application Gateway를 사용 하 여 모든 응답에 대해 이러한 헤더를 설정할 수 있습니다.

![보안 헤더](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>원치 않는 머리글 삭제

HTTP 응답에서 중요 한 정보를 표시 하는 헤더를 제거 하는 것이 좋습니다. 예를 들어 백 엔드 서버 이름, 운영 체제 또는 라이브러리 세부 정보와 같은 정보를 제거 하는 것이 좋습니다. Application gateway를 사용 하 여 이러한 헤더를 제거할 수 있습니다.

![헤더 삭제](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>헤더가 있는지 확인 합니다.

헤더 또는 서버 변수가 있는지에 대 한 HTTP 요청 또는 응답 헤더를 평가할 수 있습니다. 이 계산은 특정 헤더가 있는 경우에만 헤더 재작성을 수행 하려는 경우에 유용 합니다.

![헤더가 있는지 확인 하는 중](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>URL 다시 쓰기에 대 한 일반적인 시나리오

#### <a name="parameter-based-path-selection"></a>매개 변수 기반 경로 선택

헤더의 값, URL의 일부 또는 요청에 포함 된 쿼리 문자열을 기반으로 백 엔드 풀을 선택 하려는 시나리오를 수행 하려면 URL 재작성 기능과 경로 기반 라우팅의 조합을 사용할 수 있습니다.  예를 들어 쇼핑 웹 사이트가 있고 product 범주가 URL에 쿼리 문자열로 전달 되 고 쿼리 문자열에 따라 요청을 백 엔드로 라우팅하는 경우 다음을 수행 합니다.

**1 단계:**  아래 이미지와 같이 경로 맵을 만듭니다.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL 재작성 시나리오 1-1.":::

**2 단계 (a):** 재작성 규칙 3 개를 포함 하는 재작성 집합을 만듭니다. 

* 첫 번째 규칙에는 *category =* listing1에 대 한 *query_string* 변수를 확인 하 고, URL 경로를/*listing1* 로 다시 작성 하 고, **경로 맵을 다시 평가** 하는 작업이 있는 조건이 있습니다.

* 두 번째 규칙에는 *category =* 모음에 대 한 *query_string* 변수를 확인 하 고, URL 경로를/*listing2* 로 다시 작성 하 고, **경로 맵을 다시 평가** 하는 작업이 포함 되어 있습니다.

* 세 번째 규칙에는 *category = 액세서리* 의 *query_string* 변수를 확인 하 고, URL 경로를/*listing3* 로 다시 작성 하 고 **경로 맵을 다시 평가** 하는 작업이 포함 되어 있습니다.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL 재작성 시나리오 1-2.":::

 

**2 단계 (b):** 위의 경로 기반 규칙의 기본 경로에이 재작성 집합을 연결 합니다.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL 재작성 시나리오 1-3.":::

이제 사용자가 *contoso.com/listing?category=any*를 요청 하면 경로 맵 (/listing1,/listing1,/listing1)의 경로 패턴이 모두 일치 하지 않으므로 기본 경로와 일치 하 게 됩니다. 위의 재작성 집합을이 경로와 연결 했으므로이 재작성 집합이 평가 됩니다. 쿼리 문자열은이 재작성 집합에서 3 다시 쓰기 규칙의 조건과 일치 하지 않으므로 다시 쓰기 작업이 수행 되지 않으므로 요청은 기본 경로 ( *Genericlist*)와 연결 된 백 엔드로 변경 되지 않습니다.

 사용자가 *contoso.com/listing?category=shoes를* 요청 하면 기본 경로가 일치 하 게 됩니다. 그러나이 경우에는 첫 번째 규칙의 조건이 일치 하므로 해당 조건과 연결 된 작업이 실행 되며,이는 URL 경로를/*listing1* 로 다시 작성 하 고 경로 맵을 다시 평가 하는 데 사용 됩니다. 경로 맵이 재평가 되 면 이제 요청은 패턴 */listing1* 과 연결 된 경로와 일치 하 고 요청은이 패턴과 연결 된 백 엔드 (ShoesListBackendPool)로 라우팅됩니다.

>[!NOTE]
>이 시나리오는 정의 된 조건에 따라 모든 헤더 또는 쿠키 값, URL 경로, 쿼리 문자열 또는 서버 변수로 확장 될 수 있으며, 기본적으로 이러한 조건에 따라 요청을 라우팅할 수 있습니다.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>URL을 기반으로 쿼리 문자열 매개 변수 다시 작성

사용자가 볼 수 있는 링크가 간단 하 고 이해 하기 쉬운 쇼핑 웹 사이트의 시나리오를 고려해 야 하지만 백 엔드 서버에는 올바른 콘텐츠를 표시 하는 쿼리 문자열 매개 변수가 필요 합니다.

이 경우 URL에서 매개 변수를 캡처하여 URL의 매개 변수에서 쿼리 문자열 키-값 쌍을 추가할 수 Application Gateway. 예를 들어, 사용자가 https://www.contoso.com/fashion/shirts https://www.contoso.com/buy.aspx?category=fashion&product=shirts 다음 URL 재작성 구성을 통해 달성할 수 있도록 다시 작성 하려는 경우를 가정해 보겠습니다.

**조건** -서버 변수가 `uri_path` 패턴과 같은 경우`/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL 재작성 시나리오 2-1.":::

**작업** -URL 경로를로 설정 하 `buy.aspx` 고 문자열을 쿼리 합니다.`category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL 재작성 시나리오 2-2.":::

위에서 설명한 시나리오를 수행 하는 단계별 가이드는를 [사용 하 여 URL 다시 쓰기 Application Gateway](rewrite-url-portal.md) 를 참조 하세요 Azure Portal

### <a name="url-rewrite-vs-url-redirect"></a>URL 재작성 vs URL 리디렉션

URL 재작성의 경우 요청이 백 엔드로 전송 되기 전에 URL을 다시 작성 Application Gateway 합니다. 변경 내용이 사용자에 게 숨겨져 있기 때문에 브라우저에서 사용자에 게 표시 되는 항목은 변경 되지 않습니다.

URL 리디렉션 시 Application Gateway는 새 URL을 사용 하 여 클라이언트에 리디렉션 응답을 보냅니다. 그러면 클라이언트가 해당 요청을 리디렉션에 제공 된 새 URL로 다시 전송 해야 합니다. 브라우저에서 사용자에 게 표시 되는 URL이 새 URL로 업데이트 됩니다.

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Vs 리디렉션을 다시 작성 합니다.":::

## <a name="limitations"></a>제한 사항

- 응답에 이름이 같은 헤더가 두 개 이상 있는 경우 해당 헤더 중 하나의 값을 다시 작성 하면 응답의 다른 헤더가 삭제 됩니다. 이는 일반적으로 응답에 두 개 이상의 쿠키 헤더를 사용할 수 있으므로 설정-쿠키 헤더에서 발생할 수 있습니다. 이러한 시나리오 중 하나는 응용 프로그램 게이트웨이와 함께 app service를 사용 하 고 응용 프로그램 게이트웨이에서 쿠키 기반 세션 선호도를 구성 하는 경우입니다. 이 경우 응답에는 app service에서 사용 하는 두 개의 설정-쿠키 헤더 (예:) `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` 와 application gateway 선호도 (예:)가 포함 `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` 됩니다. 이 시나리오에서 Set 쿠키 헤더 중 하나를 다시 작성 하면 응답에서 다른 Set 쿠키 헤더가 제거 될 수 있습니다.
- 응용 프로그램 게이트웨이가 요청을 리디렉션하거나 사용자 지정 오류 페이지를 표시 하도록 구성 된 경우에는 다시 작성이 지원 되지 않습니다.
- 헤더 이름에는 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)에 정의 된 대로 영숫자 문자와 특정 기호가 포함 될 수 있습니다. 현재 헤더 이름에 밑줄 (_) 특수 문자를 지원 하지 않습니다.
- 연결 및 업그레이드 헤더를 다시 작성할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [을 사용 하 여 HTTP 헤더를 Application Gateway 다시 작성 하는 방법을 알아봅니다 Azure Portal](rewrite-http-headers-portal.md)
- [Application Gateway를 사용 하 여 URL을 다시 작성 하는 방법을 알아봅니다 Azure Portal](rewrite-url-portal.md)
