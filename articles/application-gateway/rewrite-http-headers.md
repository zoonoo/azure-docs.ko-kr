---
title: Azure 응용 프로그램 게이트웨이로 HTTP 헤더 다시 작성 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 응용 프로그램 게이트웨이에서 HTTP 헤더 다시 작성에 대한 개요를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: d0b28770940f0e1adeec16aa89cd087299bd4abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132987"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>응용 프로그램 게이트웨이로 HTTP 헤더 다시 작성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 헤더를 사용하면 클라이언트와 서버가 요청 또는 응답으로 추가 정보를 전달할 수 있습니다. 이러한 헤더를 다시 작성하면 HSTS/X-XSS-Protection과 같은 보안 관련 헤더 필드 추가, 중요한 정보를 표시할 수 있는 응답 헤더 필드 제거, X-Forwarded-For 헤더에서 포트 정보 제거 와 같은 중요한 작업을 수행할 수 있습니다.

Application Gateway는 요청 및 응답 패킷이 클라이언트와 백 엔드 풀 사이를 이동하는 동안 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트할 수 있습니다. 뿐만 아니라 특정 조건이 충족되는 경우에만 지정된 헤더를 다시 쓸 수 있도록 조건을 추가할 수 있습니다.

또한 응용 프로그램 게이트웨이는 요청 및 응답에 대한 추가 정보를 저장하는 데 도움이 되는 여러 [서버 변수를](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) 지원합니다. 이렇게 하면 강력한 다시 쓰기 규칙을 쉽게 만들 수 있습니다.

> [!NOTE]
>
> HTTP 헤더 재작성 지원은 [Standard_V2 및 WAF_v2 SKU에서만](application-gateway-autoscaling-zone-redundant.md)사용할 수 있습니다.

![헤더 다시 쓰기](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>지원되는 헤더

호스트, 연결 및 업그레이드 헤더를 제외한 요청 및 응답의 모든 헤더를 다시 작성할 수 있습니다. 또한 응용 프로그램 게이트웨이를 사용하여 사용자 지정 헤더를 만들고 이를 통해 라우팅되는 요청 및 응답에 추가할 수 있습니다.

## <a name="rewrite-conditions"></a>조건 다시 작성

재작성 조건을 사용하여 HTTP(S) 요청 및 응답의 내용을 평가하고 하나 이상의 조건이 충족되는 경우에만 헤더 다시 쓰기를 수행할 수 있습니다. 응용 프로그램 게이트웨이는 이러한 유형의 변수를 사용하여 HTTP(S) 요청 및 응답의 내용을 평가합니다.

- 요청의 HTTP 헤더입니다.
- 응답의 HTTP 헤더입니다.
- 응용 프로그램 게이트웨이 서버 변수입니다.

조건을 사용하여 지정된 변수가 있는지, 지정된 변수가 특정 값과 일치하는지 또는 지정된 변수가 특정 패턴과 일치하는지 여부를 평가할 수 있습니다. [Perl 호환 정규식(PCRE) 라이브러리를](https://www.pcre.org/) 사용하여 조건에서 정규식 패턴 일치를 설정합니다. 정규식 구문에 대해 자세히 알아보려면 [Perl 정규식 기본 페이지를](https://perldoc.perl.org/perlre.html)참조하십시오.

## <a name="rewrite-actions"></a>작업 다시 작성

다시 작성 작업을 사용하여 다시 작성할 요청 및 응답 헤더와 헤더에 대한 새 값을 지정합니다. 새 헤더를 만들거나, 기존 헤더의 값을 수정하거나, 기존 헤더를 삭제할 수 있습니다. 새 헤더 또는 기존 헤더의 값을 다음과 같은 유형의 값으로 설정할 수 있습니다.

- Text.
- 요청 헤더. 요청 헤더를 지정하려면 {http_req_*headerName*}구문을 사용해야 합니다.
- 응답 헤더입니다. 응답 헤더를 지정하려면 {http_resp_*헤더Name*}구문을 사용해야 합니다.
- 서버 변수입니다. 서버 변수를 지정하려면 {var_*serverVariable*}구문을 사용해야 합니다.
- 텍스트, 요청 헤더, 응답 헤더 및 서버 변수의 조합입니다.

## <a name="server-variables"></a>서버 변수

응용 프로그램 게이트웨이는 서버 변수를 사용하여 서버, 클라이언트와의 연결 및 연결의 현재 요청에 대한 유용한 정보를 저장합니다. 저장된 정보의 예로는 클라이언트의 IP 주소와 웹 브라우저 유형이 있습니다. 예를 들어 새 페이지가 로드되거나 폼이 게시될 때 서버 변수가 동적으로 변경됩니다. 이러한 변수를 사용하여 다시 쓰기 조건을 평가하고 헤더를 다시 작성할 수 있습니다.

응용 프로그램 게이트웨이는 다음 서버 변수를 지원합니다.

| 변수 이름 | 설명                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | `client_ip` 변수가 있는 X-Forwarded-For 클라이언트 요청 헤더 필드(이 표의 설명 참조)는 IP1, IP2, IP3 등의 형식으로 추가됩니다. X-전달-For 필드가 클라이언트 요청 헤더에 없는 경우 `add_x_forwarded_for_proxy` 변수는 `$client_ip` 변수와 같습니다. 이 변수는 헤더에 포트 정보가 없는 IP 주소만 포함되도록 응용 프로그램 게이트웨이에서 설정한 X-Forwarded-For 헤더를 다시 작성하려는 경우에 특히 유용합니다. |
| ciphers_supported          | 클라이언트에서 지원하는 암호 목록입니다.          |
| ciphers_used               | 설정된 TLS 연결에 사용되는 암호 문자열입니다. |
| client_ip                  | 응용 프로그램 게이트웨이가 요청을 받은 클라이언트의 IP 주소입니다. 응용 프로그램 게이트웨이 와 원래 클라이언트 앞에 역방향 프록시가 있는 경우 *client_ip* 역방향 프록시의 IP 주소를 반환합니다. |
| client_port                | 클라이언트 포트입니다.                                                  |
| client_tcp_rtt             | 클라이언트 TCP 연결에 대한 정보입니다. TCP_INFO 소켓 옵션을 지원하는 시스템에서 사용할 수 있습니다. |
| client_user                | HTTP 인증을 사용하는 경우 인증을 위해 제공된 사용자 이름입니다. |
| host                       | 이 우선 순위: 요청 줄의 호스트 이름, 호스트 요청 헤더 필드의 호스트 이름 또는 요청과 일치하는 서버 이름입니다. |
| cookie_*이름*              | *이름* 쿠키입니다.                                            |
| http_method                | URL 요청을 하는 데 사용되는 방법입니다. 예를 들어 GET 또는 POST를 예로 들 수 있습니다. |
| http_status                | 세션 상태입니다. 예를 들어 200, 400 또는 403입니다.                       |
| http_version               | 요청 프로토콜입니다. 일반적으로 HTTP/1.0, HTTP/1.1 또는 HTTP/2.0. |
| query_string               | 요청된 URL에서 "?"를 따르는 변수/값 쌍 목록입니다. |
| received_bytes             | 요청 의 길이(요청 줄, 헤더 및 요청 본문 포함)입니다. |
| request_query              | 요청 줄의 인수입니다.                                |
| request_scheme             | 요청 체계: http 또는 https.                            |
| request_uri                | 전체 원래 요청 URI(인수 있음)입니다.                   |
| sent_bytes                 | 클라이언트에 전송된 바이트 수입니다.                             |
| server_port                | 요청을 수락한 서버의 포트입니다.                 |
| ssl_connection_protocol    | 설정된 TLS 연결의 프로토콜입니다.        |
| ssl_enabled                | 연결이 TLS 모드에서 작동하는 경우 "켜기". 그렇지 않으면 빈 문자열이 됩니다. |

## <a name="rewrite-configuration"></a>구성 다시 작성

HTTP 헤더 재작성을 구성하려면 다음 단계를 완료해야 합니다.

1. HTTP 헤더 다시 작성하는 데 필요한 개체를 만듭니다.

   - **작업 다시 쓰기**: 다시 작성할 요청 및 요청 헤더 필드와 헤더에 대한 새 값을 지정하는 데 사용됩니다. 하나 이상의 재쓰기 조건을 다시 쓰기 작업과 연결할 수 있습니다.

   - **다시 쓰기 조건**: 선택적 구성입니다. 다시 작성 조건은 HTTP(S) 요청 및 응답의 내용을 평가합니다. HTTP(S) 요청 또는 응답이 재작성 조건과 일치하는 경우 다시 쓰기 작업이 수행됩니다.

     둘 이상의 조건을 작업과 연결하는 경우 모든 조건이 충족될 때만 작업이 수행됩니다. 즉, 작업은 논리적 AND 작업입니다.

   - **다시 쓰기 규칙**: 여러 재쓰기 작업 / 다시 쓰기 조건 조합을 포함합니다.

   - **규칙 시퀀스**: 다시 쓰기 규칙이 실행되는 순서를 결정하는 데 도움이 됩니다. 이 구성은 다시 쓰기 집합에 여러 번 다시 쓰기 규칙이 있는 경우에 유용합니다. 규칙 시퀀스 값이 낮은 다시 쓰기 규칙이 먼저 실행됩니다. 동일한 규칙 시퀀스를 두 개의 다시 쓰기 규칙에 할당하는 경우 실행 순서는 결정적이지 않습니다.

   - **다시 쓰기 집합**: 요청 라우팅 규칙과 연결될 여러 재작성 규칙을 포함합니다.

2. 리쓰기*집합(다시 쓰기규칙집합)을*라우팅 규칙에 연결합니다. 재작성 구성은 라우팅 규칙을 통해 소스 수신기에 연결됩니다. 기본 라우팅 규칙을 사용하는 경우 헤더 재작성 구성은 원본 수신기와 연결되며 전역 헤더 다시 작성입니다. 경로 기반 라우팅 규칙을 사용하는 경우 헤더 재작성 구성이 URL 경로 맵에 정의됩니다. 이 경우 사이트의 특정 경로 영역에만 적용됩니다.
   > [!NOTE]
   > URL 다시 쓰기 헤더를 변경합니다. 경로의 URL은 변경되지 않습니다.

여러 HTTP 헤더 다시 쓰기 집합을 만들고 각 재쓰기 집합을 여러 수신기에 적용할 수 있습니다. 그러나 특정 수신기에 하나의 재쓰기 집합만 적용할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

다음은 헤더 다시 쓰기를 사용하기 위한 몇 가지 일반적인 시나리오입니다.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>X-전달-For 헤더에서 포트 정보 제거

응용 프로그램 게이트웨이는 요청을 백 엔드로 전달하기 전에 모든 요청에 X-전달-For 헤더를 삽입합니다. 이 헤더는 IP 포트의 쉼표로 구분된 목록입니다. 백 엔드 서버가 IP 주소를 포함하기 위해 헤더만 필요로하는 시나리오가있을 수 있습니다. 헤더 다시 쓰기를 사용하여 X-Forwarded-For 헤더에서 포트 정보를 제거할 수 있습니다. 이 작업을 수행하는 한 가지 방법은 헤더를 add_x_forwarded_for_proxy 서버 변수로 설정하는 것입니다.

![포트 제거](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>리디렉션 URL 수정

백 엔드 응용 프로그램이 리디렉션 응답을 보내는 경우 클라이언트를 백 엔드 응용 프로그램에서 지정한 URL과 다른 URL로 리디렉션할 수 있습니다. 예를 들어 앱 서비스가 응용 프로그램 게이트웨이 뒤에서 호스팅되고 클라이언트가 상대 경로로 리디렉션을 수행해야 하는 경우 이 작업을 수행할 수 있습니다. 예를 들어 contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2 리디렉션됩니다.

App Service는 다중 테넌트 서비스이므로 요청의 호스트 헤더를 사용하여 요청을 올바른 끝점으로 라우팅합니다. 앱 서비스에는 응용 프로그램 게이트웨이의 도메인 이름(예: contoso.com)과 다른 *.azurewebsites.net(예: contoso.azurewebsites.net)의 기본 도메인 이름이 있습니다. 클라이언트의 원래 요청은 응용 프로그램 게이트웨이의 도메인 이름(contoso.com)을 호스트 이름으로 사용하므로 응용 프로그램 게이트웨이는 호스트 이름을 contoso.azurewebsites.net 변경합니다. 앱 서비스가 요청을 올바른 끝점으로 라우팅할 수 있도록 이 변경이 적용됩니다.

앱 서비스가 리디렉션 응답을 보내면 해당 응답의 위치 헤더에서 응용 프로그램 게이트웨이에서 수신하는 요청의 호스트 이름과 동일한 호스트 이름을 사용합니다. 따라서 클라이언트는 응용 프로그램 게이트웨이(contoso.com/path2)를 거치지 않고 contoso.azurewebsites.net/path2 직접 요청을 합니다. 응용 프로그램 게이트웨이를 우회하는 것은 바람직하지 않습니다.

위치 헤더의 호스트 이름을 응용 프로그램 게이트웨이의 도메인 이름으로 설정하여 이 문제를 해결할 수 있습니다.

호스트 이름을 대체하는 단계는 다음과 같습니다.

1. 응답의 위치 헤더에 azurewebsites.net 포함되어 있는지 평가하는 조건으로 다시 작성 규칙을 만듭니다. 패턴을 `(https?):\/\/.*azurewebsites\.net(.*)$`입력합니다.
1. 응용 프로그램 게이트웨이의 호스트 이름을 지을 수 있도록 위치 헤더를 다시 작성하는 작업을 수행합니다. 헤더 값으로 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 입력하여 이 작업을 수행합니다.

![위치 헤더 수정](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>보안 HTTP 헤더를 구현하여 취약점을 방지합니다.

응용 프로그램 응답에 필요한 헤더를 구현하여 여러 보안 취약점을 해결할 수 있습니다. 이러한 보안 헤더에는 X-XSS 보호, 엄격한 전송 보안 및 콘텐츠 보안 정책이 포함됩니다. 응용 프로그램 게이트웨이를 사용하여 모든 응답에 대해 이러한 헤더를 설정할 수 있습니다.

![보안 헤더](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>원치 않는 헤더 삭제

HTTP 응답에서 중요한 정보를 표시하는 헤더를 제거할 수 있습니다. 예를 들어 백 엔드 서버 이름, 운영 체제 또는 라이브러리 세부 정보와 같은 정보를 제거할 수 있습니다. 응용 프로그램 게이트웨이를 사용하여 다음 헤더를 제거할 수 있습니다.

![헤더 삭제](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>헤더의 존재 여부를 확인합니다.

HTTP 요청 또는 응답 헤더를 평가하여 헤더 또는 서버 변수가 있는지 확인할 수 있습니다. 이 평가는 특정 헤더가 있는 경우에만 헤더 다시 작성을 수행하려는 경우에 유용합니다.

![헤더의 존재 확인](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>제한 사항

- 응답에 이름이 같은 헤더가 두 개 이상 있는 경우 해당 헤더 중 하나의 값을 다시 작성하면 응답에서 다른 헤더가 삭제됩니다. 이 문제는 일반적으로 응답에 두 개 이상의 Set-Cookie 헤더를 가질 수 있으므로 Set-Cookie 헤더에서 발생할 수 있습니다. 이러한 시나리오 중 하나는 응용 프로그램 게이트웨이가 있는 앱 서비스를 사용하고 응용 프로그램 게이트웨이에서 쿠키 기반 세션 선호도를 구성한 경우입니다. 이 경우 응답에는 두 개의 Set-Cookie 헤더(예: 앱 서비스에서 `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` 사용하는 헤더)와 응용 프로그램 게이트웨이 `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`선호도에 대한 헤더(예: )가 포함됩니다. 이 시나리오에서 Set-Cookie 헤더 중 하나를 다시 작성하면 응답에서 다른 Set-Cookie 헤더가 제거될 수 있습니다.

- 연결, 업그레이드 및 호스트 헤더 다시 작성은 현재 지원되지 않습니다.

- 헤더 이름에는 [RFC 7230에](https://tools.ietf.org/html/rfc7230#page-27)정의된 모든 참숫자 문자와 특정 기호가 포함될 수 있습니다. 현재 헤더 이름의 밑줄 ()\_특수 문자를 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

HTTP 헤더를 다시 작성하는 방법에 대해 알아보려면 다음을 참조하십시오.

- [Azure 포털을 사용하여 HTTP 헤더 다시 작성](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Azure PowerShell을 사용하여 HTTP 헤더 다시 작성](add-http-header-rewrite-rule-powershell.md)
