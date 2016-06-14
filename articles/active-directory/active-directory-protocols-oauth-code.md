<properties
	pageTitle="Azure AD .NET 프로토콜 개요 | Microsoft Azure"
	description="이 문서에서는 Azure Active Directory 및 OAuth 2.0을 사용하여 테넌트에서 웹 응용 프로그램 및 Web API에 대한 액세스 권한을 부여하기 위해 HTTP 메시지를 사용하는 방법을 설명합니다."
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


# OAuth 2.0 및 Azure Active Directory를 사용하여 웹 응용 프로그램에 대한 액세스 권한 부여

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Azure AD(Azure Active Directory)는 OAuth 2.0을 사용하여 사용자는 Azure AD 테넌트에서 웹 응용 프로그램 및 웹 API에 대한 액세스 권한을 부여할 수 있습니다. 이 가이드는 언어 독립적이며 공개 소스 라이브러리를 사용하지 않고 HTTP 메시지를 수신하는 방법을 설명합니다.

OAuth 2.0 인증 코드 흐름은 [OAuth 2.0 사양의 섹션 4.1](https://tools.ietf.org/html/rfc6749#section-4.1)에서 설명합니다. 웹앱 및 기본적으로 설치된 앱을 포함하여 대부분의 앱 형식에서 인증 및 권한 부여를 수행하는 데 사용됩니다.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## OAuth 2.0 권한 부여 흐름

높은 수준에서 응용 프로그램에 대한 전체 권한 부여 흐름은 다음과 같습니다.

![OAuth 인증 코드 흐름](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## 인증 코드 요청

인증 코드 흐름은 클라이언트가 사용자를 `/authorize` 끝점으로 보내는 것으로 시작됩니다. 이 요청에서 클라이언트는 사용자로부터 얻어야 하는 사용 권한을 나타냅니다. Azure 클래식 포털의 응용 프로그램 페이지에서 아래쪽 서랍의 **끝점 보기** 단추로 OAuth 2.0 끝점을 가져올 수 있습니다.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 필수 | 요청의 경로에 있는 `{tenant}` 값을 사용하여 응용 프로그램에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 테넌트 독립 토큰에 대한 테넌트 식별자(예: `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com`, `common`)입니다. |
| client\_id | 필수 | Azure AD에 등록할 때 앱에 할당된 응용 프로그램 ID입니다. Azure 관리 포털에서 이를 확인할 수 있습니다. **Active Directory**, 해당 디렉터리를 클릭하고 응용 프로그램을 클릭한 후 **구성**을 클릭합니다. |
| response\_type | 필수 | 인증 코드 흐름에 대한 `code`를 포함해야 합니다. |
| redirect\_uri | 권장 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect\_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. 네이티브 및 모바일 앱의 경우 `urn:ietf:wg:oauth:2.0:oob`의 기본값을 사용해야 합니다. |
| scope | 필수 | 사용자가 동의하게 할 공백으로 구분된 [범위](active-directory-v2-scopes.md) 목록입니다. |
| response\_mode | 권장 | 결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. `query` 또는 `form_post`일 수 있습니다. |
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](http://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| prompt | 선택 사항 | 필요한 사용자 상호 작용 유형을 나타냅니다. 이 경우 유효한 값은 'login', 'none', 'consent'뿐입니다. `prompt=login`은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다. `prompt=none`은 그 반대로 사용자에게 어떠한 대화형 프롬프트도 표시되지 않도록 합니다. Single-Sign On을 통해 요청이 자동으로 완료될 수 없는 경우에 v2.0 끝점은 오류를 반환합니다. `prompt=consent`는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다. |
| login\_hint | 선택 사항 | 사용자 이름을 미리 알고 있는 경우 사용자를 위해 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용될 수 있습니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름이 이미 추출된 경우 앱이 재인증 과정에서 이 매개 변수를 종종 사용합니다. |
| domain\_hint | 선택 사항 | `consumers` 또는 `organizations` 중 하나일 수 있습니다. 포함된 경우, v2.0 로그인 페이지를 거친 사용자가 좀 더 효율적인 사용자 경험을 가질 수 있도록 전자 메일 기반 검색 프로세스를 건너뜁니다. 앱이 이전 로그인 작업에서 `tid`를 추출하여 재인증 과정에서 이 매개 변수를 종종 사용합니다. `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`인 경우 `domain_hint=consumers`를 사용해야 합니다. 그렇지 않으면 `domain_hint=organizations`를 사용합니다. |

> [AZURE.NOTE] 사용자가 조직에 소속된 경우 조직 관리자는 사용자 대신 동의 또는 거부하거나 사용자가 동의하도록 허용할 수 있습니다. 사용자에게는 관리자가 허용하는 경우에만 동의하는 옵션이 제공됩니다.

이때 사용자에게 자격 증명을 입력하고 `scope` 쿼리 매개 변수에 표시된 사용 권한에 동의하는지 묻는 메시지가 표시됩니다. 사용자가 인증하고 동의하면 Azure AD는 요청의 `redirect_uri` 주소에서 사용자의 앱에 응답을 보냅니다.

### 성공적인 응답

성공적인 응답은 다음과 같습니다.

```
http://localhost:12345/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&state=12345
&session_state=733ad279-b681-49c3-9215-951abf94d2c5
```

| 매개 변수 | 설명 |
| -----------------------| --------------- |
| admin\_consent | 관리자가 동의 요청 프롬프트에 동의한 경우 값은 True입니다.|
| 코드 | 응용 프로그램에서 요청한 인증 코드입니다. 응용 프로그램은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. |
| session\_state | 현재 사용자 세션을 식별하는 고유 값입니다. 이 값은 GUID이지만 검사 없이 전달되는 불투명 값으로 처리되어야 합니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 응용 프로그램은 요청의 상태 값을 확인해야 합니다.
요청에 상태 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타납니다. 응용 프로그램에서 요청 및 응답의 상태 값이 동일한지 확인하는 것이 좋습니다.

### 오류 응답

응용 프로그램이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

## 인증 코드를 사용하여 액세스 토큰 요청

인증 코드를 획득하고 사용자가 사용 권한을 부여했으므로 이제 POST 요청을 `/token` 끝점으로 보내 액세스 토큰에 대한 코드를 원하는 리소스에 대한 코드로 교환할 수 있습니다.

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: client_secret only required for web apps
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | 필수 | 요청의 경로에 있는 `{tenant}` 값을 사용하여 응용 프로그램에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 테넌트 독립 토큰에 대한 테넌트 식별자(예: `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com`, `common`)입니다. |
| client\_id | 필수 | Azure AD에 등록할 때 앱에 할당된 응용 프로그램 ID입니다. Azure 클래식 포털에서 이를 확인할 수 있습니다. **Active Directory**, 해당 디렉터리를 클릭하고 응용 프로그램을 클릭한 후 **구성**을 클릭합니다. |
| grant\_type | 필수 | 인증 코드 흐름에 대한 `authorization_code`여야 합니다. |
| 코드 | 필수 | 이전 섹션에서 획득한 `authorization_code`입니다. |
| redirect\_uri | 필수 | `authorization_code`를 획득하는 데 사용된 동일한 `redirect_uri` 값입니다. |
| client\_secret | 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 응용 프로그램 암호입니다. 장치에 client\_secret을 안정적으로 저장할 수 없으므로 네이티브 앱에서는 사용하면 안 됩니다. 서버 쪽에서 `client_secret`을 안전하게 저장할 수 있는 웹앱과 Web API에 필요합니다. |


### 성공적인 응답

성공적인 응답은 다음과 같습니다.

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| access\_token | 요청된 액세스 토큰입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스에 인증할 수 있습니다. |
| token\_type | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)을 참조하세요. |
| expires\_in | 액세스 토큰이 유효한 기간(초)입니다. |
| scope | 클라이언트 응용 프로그램에 부여된 권한을 가장합니다. 기본 권한은 `user_impersonation`입니다. 보안 리소스의 소유자는 Azure AD에서 추가 값을 등록할 수 있습니다.|
| refresh\_token | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 액세스 토큰이 만료된 후 이 토큰을 사용하여 추가 액세스 토큰을 획득할 수 있습니다. 새로 고침 토큰은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. |
| id\_token | 서명되지 않은 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 base64Url로 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있지만 권한 부여 또는 보안 경계에 대해 의존해서는 안 됩니다. |

### JWT 토큰 클레임
`id_token` 매개 변수 값의 JWT 토큰은 다음 클레임으로 디코딩될 수 있습니다.

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

`id_token` 매개 변수는 다음 클레임 유형을 포함합니다. JSON 웹 토큰에 대한 자세한 내용은 [JWT IETF 초안 사양](http://go.microsoft.com/fwlink/?LinkId=392344)을 참조하세요. 토큰 유형 및 클레임에 대한 자세한 내용은 [지원되는 토큰 및 클레임 유형](active-directory-token-and-claims.md)을 참조하세요.

| 클레임 유형 | 설명 |
|------------|-------------|
| aud | 토큰의 대상입니다. 토큰이 클라이언트 응용 프로그램에 발급된 경우 대상 그룹은 클라이언트의 `client_id`입니다.
| exp | 만료 시간입니다. 토큰이 만료되는 시간입니다. 토큰이 유효하려면 현재 날짜/시간이 `exp` 값보다 작거나 같아야 합니다. 시간은 1970년 1월 1일(1970-01-01T0:0:0Z) UTC부터 토큰이 발급된 시간까지 기간(초)으로 표시됩니다. |
| family\_name | 사용자의 성입니다. 응용 프로그램이 이 값을 표시할 수 있습니다. |
| given\_name | 사용자의 이름입니다. 응용 프로그램이 이 값을 표시할 수 있습니다. |
| iat | 발급 시간입니다. JWT가 발급된 시간입니다. 시간은 1970년 1월 1일(1970-01-01T0:0:0Z) UTC부터 토큰이 발급된 시간까지 기간(초)으로 표시됩니다. |
| iss | 토큰 발급자를 식별합니다. |
| nbf | 이전 시간이 아닙니다. 토큰이 유효해지는 시간입니다. 토큰이 유효하려면 현재 날짜/시간이 Nbf 값보다 크거나 같아야 합니다. 시간은 1970년 1월 1일(1970-01-01T0:0:0Z) UTC부터 토큰이 발급된 시간까지 기간(초)으로 표시됩니다. |
| oid | Azure AD에서 사용자 개체의 개체 식별자(ID)입니다. |
| sub | 토큰 주체 식별자입니다. 토큰이 설명하는 사용자에 대한 영구적이고 변경할 수 없는 식별자입니다. 캐싱 논리에 이 값을 사용합니다. |
| tid | 토큰을 발급한 Azure AD 테넌트의 테넌트 식별자(ID)입니다. |
| unique\_name | 사용자에게 표시할 수 있는 고유한 식별자입니다. 일반적으로 사용자 계정 이름(UPN)입니다. |
| upn | 사용자의 사용자 계정 이름입니다. |
| ver | 버전. JWT 토큰의 버전으로 일반적으로 1.0입니다. |

### 오류 응답

샘플 오류 응답은 다음과 같습니다.

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| error\_codes | 진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다. |
| timestamp | 오류가 발생한 시간입니다. |
| trace\_id | 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| correlation\_id | 여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다.|

## 리소스에 액세스하는 데 액세스 토큰 사용

`access_token`을 성공적으로 획득했으므로 이제 `Authorization` 헤더에 포함하여 Web API에 대한 요청에 토큰을 사용할 수 있습니다. [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) 사양에서는 HTTP 요청에 전달자 토큰을 사용하여 보호된 리소스에 액세스하는 방법을 설명합니다.

### 샘플 요청

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

## 액세스 토큰 새로 고침

액세스 토큰은 수명이 짧으며, 만료되면 새로 고쳐야 리소스에 계속 액세스할 수 있습니다. 다른 `POST` 요청을 `/token` 끝점에 제출하여 `access_token`을 새로 고칠 수 있지만 이번에는 `code` 대신 `refresh_token`을 제공해야 합니다.

새로 고침 토큰의 수명은 제공되지 않고 정책 설정에 따라 달라지며 인증 코드가 부여된 시간은 Azure AD에 의해 해지됩니다. 응용 프로그램은 새 액세스 토큰에 대한 요청이 실패하는 경우를 예상하고 처리해야 합니다. 이 경우 새 액세스 토큰을 요청하는 코드로 돌아가야 합니다.

새로 고침 토큰을 사용하여 새 액세스 토큰을 얻는 **테넌트별** 끝점(**common** 끝점도 사용 가능)에 대한 샘플 요청은 다음과 같습니다.

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| 매개 변수 | 설명 |
|-----------|-------------|
| access\_token | 요청된 새 액세스 토큰입니다.|
| expires\_in | 토큰의 남은 수명(초)입니다. 일반적인 값은 3600(한 시간)입니다. |
| expires\_on | 토큰이 만료되는 날짜 및 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 만료 시간까지 기간(초)으로 표시됩니다. |
| refresh\_token | 이 응답에 있는 토큰이 만료될 때 새 액세스 토큰을 요청하는 데 사용할 수 있는 새 OAuth 2.0 refresh\_token입니다. |
| resource | 액세스하는 데 액세스 토큰을 사용할 수 있는 보안 리소스를 식별합니다. |
| scope | 네이티브 클라이언트 응용 프로그램에 부여된 권한을 가장합니다. 기본 권한은 **user\_impersonation**입니다. 대상 리소스의 소유자는 Azure AD에서 대체 값을 등록할 수 있습니다. |
| token\_type | 토큰 형식입니다. 현재 지원되는 유일한 값은 **bearer**입니다. |

### 성공적인 응답

성공적인 토큰 응답은 다음과 같습니다.

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### 오류 응답

샘플 오류 응답은 다음과 같습니다.

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

<!---HONumber=AcomDC_0601_2016-->