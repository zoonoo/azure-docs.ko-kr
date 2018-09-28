---
title: Azure AD를 사용하여 인증받고 OAuth 2.0을 사용하여 JWT 토큰 가져오기
description: OAuth 2.0을 사용하여 Azure Active Directory에서 인증을 받고 조직의 보안 웹 응용 프로그램 및 Web API에 액세스하는 방법을 보여 주는 예제 코드입니다.
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: d77af898d5baef4fa7970132b0eb8deddb8f68cb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981800"
---
# <a name="request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-active-directory"></a>액세스 토큰을 요청하고 OAuth 2.0을 사용하여 Azure Active Directory로 보호되는 Web API 및 응용 프로그램에 액세스

이 문서에서는 JWT(JSON 웹 토큰)를 가져와 Azure AD로 보호되는 리소스에 액세스하는 방법을 보여 줍니다. 여기서는 사용자에게 사용자가 부여한 권한 또는 [서비스 주체](/azure/active-directory/develop/active-directory-application-objects)를 통해 제공된 [권한 부여 토큰](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)이 있다고 가정합니다.

## <a name="build-the-request"></a>요청 빌드

다음 `POST` HTTP 요청을 통해 JWT를 가져와 Azure AD로 보호되는 특정 응용 프로그램 또는 API에 액세스합니다.

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>헤더 요청

다음과 같은 헤더가 필요합니다.

|요청 헤더|설명|  
|--------------------|-----------------|  
| *Host:* | https://login.microsoftonline.com |
| *Content-Type:*| application/x-www-form-urlencoded |
 

### <a name="uri-parameters"></a>URI 매개 변수

| 매개 변수     |                       | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant        | 필수              | 요청의 경로에 있는 `{tenant}` 값을 사용하여 응용 프로그램에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요.                                                                                                                                                   |
| client_id     | 필수              | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList))에서 앱에 할당한 응용 프로그램 ID입니다.                                                                                                                                                                                                                             |
| grant_type    | 필수              | 인증 코드 흐름에 대한 `authorization_code` 여야 합니다.                                                                                                                                                                                                                                                                                                                                                                            |
| scope         | 필수              | 공백으로 구분된 범위 목록입니다. 이 레그에서 요청된 범위가 `/authorize` 호출의 범위와 동일하거나 이 범위에 속해야 합니다. 이 요청에 지정된 범위가 여러 리소스 서버에 걸쳐 있는 경우 v2.0 엔드포인트는 첫 번째 범위에 지정된 리소스에 대한 토큰을 반환합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](v2-permissions-and-consent.md)를 참조하세요. |
| 코드          | 필수              | `/authorize` 호출에서 얻은 authorization_code입니다.                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | 필수              | authorization_code를 획득하는 데 사용된 값과 동일한 redirect_uri 값입니다.                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 응용 프로그램 암호입니다. 장치에 client_secret을 안정적으로 저장할 수 없으므로 네이티브 앱에서는 사용하지 마세요. 서버 쪽에서 client_secret을 안전하게 저장할 수 있는 웹앱과 Web API에 필요합니다.  클라이언트 암호는 전송되기 전에 URL로 인코딩되어야 합니다.                                                                                 |
| code_verifier | 선택 사항              | authorization_code를 얻는 데 사용된 동일한 code_verifier입니다. 인증 코드 부여 요청에 PKCE가 사용된 경우에는 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요.                                                                                                                                                                                                                                                                                             |
## <a name="handle-the-response"></a>응답 처리

성공적인 토큰 응답에는 JWT 토큰이 포함되고 다음과 같습니다.

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 매개 변수     | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | 요청된 [액세스 토큰](access-tokens.md)입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스를 인증할 수 있습니다.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다.                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | 액세스 토큰이 유효한 기간(초)입니다.                                                                                                                                                                                                                                                                                                                                                                                                       |
| scope         | access_token이 유효한 범위입니다.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 액세스 토큰이 만료된 후 이 토큰을 사용하여 추가 액세스 토큰을 획득할 수 있습니다. refresh_token은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [v2.0 코드 부여 참조](v2-oauth2-auth-code-flow.md#refresh-the-access-token)를 참조하세요. <br> **참고:** `offline_access` 범위가 요청된 경우에만 제공됩니다.                                               |
| id_token      | 서명되지 않은 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있지만 권한 부여 또는 보안 경계에 대해 의존해서는 안 됩니다. id_tokens에 대한 자세한 내용은 [`id_token reference`](id-tokens.md)를 참조하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |



