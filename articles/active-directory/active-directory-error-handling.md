<properties
	pageTitle="OAuth 2.0에서의 오류 처리 | Microsoft Azure"
	description="이 문서에서는 Azure Active Directory를 사용할 때 OAuth 2.0의 일반적인 클래스 오류 및 이 오류를 처리하기 위한 모범 사례를 설명합니다."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

# OAuth 2.0에서의 오류 처리

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

이 문서에서는 Azure AD(Active Directory)에서 응용 프로그램을 인증할 때 마주칠 수 있는 일반적인 클래스의 오류를 처리하기 위한 몇 가지 모범 사례에 대해 알아봅니다. 권한 부여 끝점 및 토큰 발급 끝점에 대한 자세한 내용은 [Azure AD에서 응용 프로그램에 대한 인증 흐름](active-directory-protocols-oauth-code.md)을 참조하세요.

## 권한 부여 끝점 오류

권한 부여 프로세스의 첫 단계에서는 클라이언트 응용 프로그램이 요청을 사용자가 필요로 하는 사용 권한 목록과 함께 Azure AD의 `/authorize` 끝점에 보냅니다.

권한 부여 끝점 오류가 `/authorize` 끝점에서 시작됩니다. 이 오류는 웹 페이지에 HTTP 200 오류로, 또는 클라이언트 응용 프로그램을 사용하여 오류를 처리할 수 있는 경우 HTTP 302 리디렉션 코드를 사용하여 반환됩니다.

다음은 요청에 필수 `response_type` 매개 변수가 누락된 경우 Azure AD 권한 부여 끝점에서 오는 샘플 HTTP 302 오류 응답입니다.

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?error=invalid_request&error_description=AADSTS90014%3a+The+request+body+must+contain+the+following+parameter%3a+%27response_type%27.%0d%0aTrace+ID%3a+57f5cb47-2278-4802-a018-d05d9145daad%0d%0aCorrelation+ID%3a+570a9ed3-bf1d-40d1-81ae-63465cc25488%0d%0aTimestamp%3a+2013-12-31+05%3a51%3a35Z&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| 매개 변수 | 설명 |
|-----------|-------------|
| error | [OAuth 2.0 권한 부여 프레임워크](http://tools.ietf.org/html/rfc6749)의 섹션 5.2에 정의된 오류 코드 값입니다. 다음 테이블은 Azure AD가 반환하는 오류 코드를 설명합니다. |
| error\_description | 오류에 대한 자세한 설명입니다. 이 메시지는 최종 사용자에게 친숙한 내용이 아닙니다. |
| state | 상태 값은 교차 사이트 요청 위조(CSRF) 공격을 방지하기 위해 요청에서 전송하고 응답에서 반환하는 무작위로 생성되고 다시 사용되지 않는 값입니다. |

### 권한 부여 끝점 오류에 대한 오류 코드

다음 테이블은 오류 응답의 `error` 매개 변수에 반환될 수 있는 여러 오류 코드를 설명합니다.

| 오류 코드 | 설명 | 클라이언트 작업 |
|------------|-------------|---------------|
| invalid\_request | 프로토콜 오류(예: 필수 매개 변수 누락). | 요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다.|
| unauthorized\_client | 클라이언트 응용 프로그램이 인증 코드를 요청할 수 없습니다. | 이 오류는 일반적으로 클라이언트 응용 프로그램이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 응용 프로그램이 사용자에게 응용 프로그램을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| access\_denied | 리소스 소유자가 동의 거부 | 클라이언트 응용 프로그램이 사용자의 동의를 받지 않으면 계속 진행할 수 없다고 알릴 수 있습니다. |
| unsupported\_response\_type | 권한 부여 서버가 요청에 해당 응답 형식을 지원하지 않습니다. | 요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다.|
|server\_error | 서버에 예기치 않은 오류가 발생했습니다. | 요청을 다시 시도하십시오. 이러한 오류는 일시적인 상태 때문에 발생할 수 있습니다. 클라이언트 응용 프로그램이 일시적 오류 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| temporarily\_unavailable | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. | 요청을 다시 시도하십시오. 클라이언트 응용 프로그램이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| invalid\_resource |대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다.| 리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 응용 프로그램이 사용자에게 응용 프로그램을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |

## 토큰 발급 끝점 오류

응용 프로그램은 `/authorize` 끝점에서 인증 코드를 수신한 후 지정된 리소스에 대한 액세스 토큰을 수신하기 위해 해당 인증 코드를 `/token` 끝점에 전달합니다.

토큰 발급 끝점 오류가 `/token` 끝점에서 시작됩니다. 클라이언트가 토큰 발급 끝점을 직접 호출하기 때문에 발생한 HTTP 오류 코드입니다. Azure AD 발급 끝점은 HTTP 상태 코드 외에 오류를 설명하는 개체가 포함된 JSON 문서도 반환합니다.

예를 들어 요청의 `client_id` 매개 변수가 올바르지 않은 경우의 오류는 다음과 같습니다.

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8

{"error":"invalid_request","error_description":"AADSTS90011: Request is ambiguous, multiple application identifiers found. Application identifiers: '197451ec-ade4-40e4-b403-02105abd9049, 597451ec-ade4-40e4-b403-02105abd9049'.\r\nTrace ID: 4457d068-2a03-42b2-97f2-d55325289d86\r\nCorrelation ID: 6b3474d8-233e-463f-b0a3-86433d8ba889\r\nTimestamp: 2013-12-31 06:31:41Z","error_codes":[90011],"timestamp":"2013-12-31 06:31:41Z","trace_id":"4457d068-2a03-42b2-97f2-d55325289d86","correlation_id":"6b3474d8-233e-463f-b0a3-86433d8ba889"}
```
### HTTP 상태 코드

다음 테이블은 토큰 발급 끝점이 반환하는 HTTP 상태 코드를 나열합니다. 오류 코드가 응답을 설명하기에 충분한 경우도 있지만 오류의 경우 함께 제공되는 JSON 문서를 구문 분석하여 오류 코드를 검사해야 합니다.

| HTTP 코드 | 설명 |
|-----------|-------------|
| 400 | 기본 HTTP 코드입니다. 대부분의 경우에 사용되며 일반적으로 잘못된 형식의 요청으로 인해 발생합니다. 요청을 수정하여 다시 제출하십시오. |
| 401 | 인증에 실패했습니다. 예를 들어 요청에 client\_secret 매개 변수가 누락되었습니다.|
| 403 | 권한 부여에 실패했습니다. 예를 들어 사용자가 리소스에 액세스할 수 있는 사용 권한이 없습니다. |
| 500 | 서비스에서 내부 오류가 발생했습니다. 요청을 다시 시도하십시오. |

### 오류 응답의 JSON 문서 개체

| JSON 개체 | 설명 |
|-------------|-------------|
| error | [OAuth 2.0 권한 부여 프레임워크](http://tools.ietf.org/html/rfc6749)의 섹션 5.2에 정의된 오류 코드 값입니다. 다음 테이블은 Azure AD가 반환하는 오류 코드를 설명합니다. |
| error\_description | 오류에 대한 자세한 설명입니다. 이 메시지는 최종 사용자에게 친숙한 내용이 아닙니다. |
| timestamp | 오류가 발생한 협정 세계시(UTC) 기준 날짜 및 시간입니다. |
| trace\_id | 오류 추적을 식별하는 ID입니다. |
| correlation\_id | 	클라이언트에서 생성하는 값입니다. 이 ID는 토큰 발급 끝점에 대한 요청의 `client-request-id` 헤더에 이 값을 포함한 경우 JSON 오류 문서에 포함됩니다. 이 ID를 동일한 세션의 다른 호출이 사용할 수 있습니다. |
| error\_codes | 서비스의 서로 다른 조건에 매핑되는 오류 코드 목록이 포함되어 있습니다. 응용 프로그램 논리에는 이러한 코드를 사용하지 마십시오. 그러나 이 코드를 사용하여 문제를 진단하고 오류 코드별로 온라인에서 정보를 검색할 수 있습니다. |

### JSON 문서 오류 코드 값

| 오류 코드 | 설명 | 클라이언트 작업 |
|------------|-------------|---------------|
| invalid\_request | 프로토콜 오류(예: 필수 매개 변수 누락). | 요청을 수정하여 다시 제출 |
| invalid\_grant | 권한 부여(또는 해당 매개 변수) 또는 새로 고침 토큰이 잘못되었거나 만료되었거나 해지되었습니다. | 요청을 수정하여 다시 제출하십시오. |
| unauthorized\_client | 인증된 클라이언트가 이 권한 부여 유형을 사용할 권한이 없습니다. | 이 오류는 일반적으로 클라이언트 응용 프로그램이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 응용 프로그램이 사용자에게 응용 프로그램을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| invalid\_client | 클라이언트 인증에 실패했습니다. | 클라이언트 자격 증명이 잘못되었습니다. 해결하려면 응용 프로그램 관리자가 자격 증명을 업데이트합니다. |
| unsupported\_grant\_type | 권한 부여 서버가 해당 권한 부여 유형을 지원하지 않습니다. | 요청에서 권한 부여 유형을 변경하십시오. 이 유형의 오류는 개발 중에만 발생하며 초기 테스트 중에 검색됩니다. |
| invalid\_resource | 대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다. | 리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 응용 프로그램이 사용자에게 응용 프로그램을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| interaction\_required | 요청을 위해 사용자 상호 작용이 필요합니다. 예를 들어 추가 인증 단계가 필요합니다. | 동일한 리소스를 사용하여 요청을 다시 시도하십시오. |
| temporarily\_unavailable | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. | 요청을 다시 시도하십시오. 클라이언트 응용 프로그램이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다.|

## 보안된 리소스의 오류

OAuth 2.0 권한 부여 프레임워크의 [RFC 6750](http://tools.ietf.org/html/rfc6750) 사양을 구현하는 경우 웹 API와 같은 보안된 리소스가 반환할 수 있는 오류입니다.

RFC 6750 문제 HTTP 상태 코드를 구현하는 보안된 리소스입니다. 요청이 인증 자격 증명을 포함하지 않거나 토큰이 누락된 경우 응답에 `WWW-Authenticate` 헤더가 포함됩니다. 요청이 실패한 경우 리소스 서버가 HTTP 상태 코드 및 오류 코드로 응답합니다.

다음은 클라이언트 요청에 전달자 토큰이 포함되지 않은 경우 성공하지 못한 응답의 예입니다.

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

## 오류 매개 변수

| 매개 변수 | 설명 |
|-----------|-------------|
| authorization\_uri | 권한 부여 서버의 URI(실제 끝점)입니다. 이 값은 검색 끝점에서 서버에 관한 자세한 정보를 가져오기 위한 조회 키로도 사용됩니다. <p><p> 클라이언트는 권한 부여 서버가 신뢰할 수 있는지 확인해야 합니다. 리소스가 Azure AD에 의해 보호되는 경우 이 값은 URL이 https://login.windows.net 또는 Azure AD가 지원하는 다른 호스트 이름으로 시작하는지 확인하기에 충분합니다. 테넌트별 리소스는 언제나 테넌트별 권한 부여 URI를 반환해야 합니다. |
| error | [OAuth 2.0 권한 부여 프레임워크](http://tools.ietf.org/html/rfc6749)의 섹션 5.2에 정의된 오류 코드 값입니다.|
| error\_description | 오류에 대한 자세한 설명입니다. 이 메시지는 최종 사용자에게 친숙한 내용이 아닙니다.|
| resource\_id | 리소스의 고유 ID를 반환합니다. 클라이언트 응용 프로그램은 리소스에 대한 토큰을 요청할 때 이 ID를 `resource` 매개 변수의 값으로 사용할 수 있습니다. <p><p> 클라이언트 응용 프로그램이 이 값을 확인하는 것이 매우 중요합니다. 그렇지 않으면 악의적인 서비스가 **elevation-of-privileges** 공격을 유도할 수 있습니다. <p><p> 이 공격을 예방하기 위해 권장되는 전략은 `resource_id`이 액세스 중인 웹 API URL의 기본과 일치하는지 확인하는 것입니다. 예를 들어 https://service.contoso.com/data에 액세스 하는 경우 `resource_id`는 htttps://service.contoso.com/일 수 있습니다. 클라이언트 응용 프로그램은 ID를 확인하는 신뢰할 수 있는 대체 방법이 있지 않은 한 기본 URL로 시작하지 않는 `resource_id`를 거부해야 합니다. |

## 전달자 체계 오류 코드

RFC 6750 사양은 응답에 WWW-authenticate 헤더와 전달자 체계를 사용하는 리소스에 대한 다음과 같은 오류를 정의합니다.

| HTTP 상태 코드 | 오류 코드 | 설명 | 클라이언트 작업 |
|------------------|------------|-------------|---------------|
| 400 | invalid\_request | 요청의 형식이 잘못되었습니다. 예를 들어 매개 변수가 누락되었거나 동일한 매개 변수를 두 번 사용 중일 수 있습니다. | 오류를 해결하고 요청을 다시 시도하십시오. 이 유형의 오류는 개발 중에만 발생하며 초기 테스트에서 검색됩니다. |
| 401 | invalid\_token | 액세스 토큰이 누락되었거나 잘못되었거나 해지되었습니다. error\_description 매개 변수의 값이 추가 세부 정보를 제공합니다. | 권한 부여 서버에서 새 토큰을 요청합니다. 새 토큰이 실패한 경우 예기치 않은 오류가 발생했습니다. 오류 메시지를 사용자에게 보내고 무작위 지연 시간 후에 다시 시도하시시오. |
| 403 | insufficient\_scope | 액세스 토큰에 리소스에 액세스하는 데 필요한 가장 권한이 포함되어 있지 않습니다. | 권한 부여 끝점에 새 권한 부여 요청을 보냅니다. 응답에 범위 매개 변수가 포함된 경우 리소스 요청에 해당 범위 값을 사용합니다. |
| 403 | insufficient\_access | 토큰의 제목에 리소스에 액세스하는 데 필요한 사용 권한이 업습니다. | 사용자에게 다른 계정을 사용하거나 지정된 리소스에 대한 사용 권한을 요청하라는 메시지를 표시합니다. |

<!---HONumber=AcomDC_0601_2016-->