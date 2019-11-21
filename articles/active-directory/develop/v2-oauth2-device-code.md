---
title: Use Microsoft identity platform to sign in users on browser-less devices | Azure
description: Build embedded and browser-less authentication flows using the device authorization grant.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c948c59a90e0db17b4704188221cfc3c3d82310
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207611"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft identity platform and the OAuth 2.0 device authorization grant flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

The Microsoft identity platform supports the [device authorization grant](https://tools.ietf.org/html/rfc8628), which allows users to sign in to input-constrained devices such as a smart TV, IoT device, or printer.  이 흐름을 활성화하기 위해, 해당 장비는 사용자가 로그인할 웹 페이지를 다른 장비의 브라우저에서 방문하도록 합니다.  사용자가 로그인하면 디바이스는 필요에 따라 액세스 토큰 및 새로 고침 토큰을 가져올 수 있습니다.  

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!NOTE]
> The Microsoft identity platform endpoint doesn't support all Azure Active Directory scenarios and features. To determine whether you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>프로토콜 다이어그램

전체 디바이스 코드 흐름은 다음 다이어그램과 유사합니다. 이 문서의 뒷부분에 각 단계에 대한 설명이 나와 있습니다.

![디바이스 코드 흐름](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>디바이스 권한 부여 요청

The client must first check with the authentication server for a device and user code that's used to initiate authentication. 클라이언트는 `/devicecode` 엔드포인트에서 이 요청을 수집합니다. 이 요청에서 클라이언트는 사용자로부터 획득해야 하는 권한을 포함해야 합니다. 이 요청이 전송되는 순간부터, 사용자는 15분 동안 로그인(`expires_in`의 일반적인 값)해야 하므로 사용자가 로그인할 준비가 되었을 때 이 요청을 작성하세요.

> [!TIP]
> Postman에서 이 요청을 실행해 보세요.
> [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 매개 변수를 포함해야 합니다. | 조건 | 설명 |
| --- | --- | --- |
| `tenant` | 필수 | Can be /common, /consumers, or /organizations.  It can also be the directory tenant that you want to request permission from in GUID or friendly name format.  |
| `client_id` | 필수 | The **Application (client) ID** that the [Azure portal – App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience assigned to your app. |
| `scope` | 권장 | 사용자가 동의하게 할 공백으로 구분된 [범위](v2-permissions-and-consent.md) 목록입니다.  |

### <a name="device-authorization-response"></a>디바이스 권한 부여 응답

성공적인 응답은 사용자가 로그인할 수 있는 필수 정보가 포함된 JSON 개체입니다.  

| 매개 변수를 포함해야 합니다. | 형식 | 설명 |
| ---              | --- | --- |
|`device_code`     | string | 클라이언트와 권한 서버 간의 세션을 확인하는 데 사용되는 긴 문자열입니다. The client uses this parameter to request the access token from the authorization server. |
|`user_code`       | string | A short string shown to the user that's used to identify the session on a secondary device.|
|`verification_uri`| URI | 로그인하기 위해 사용자가 `user_code`을(를) 사용하여 이동하는 URI입니다. |
|`expires_in`      | int | `device_code` 및 `user_code`의 만료 전 시간(초)입니다. |
|`interval`        | int | 클라이언트가 폴링 요청 간에 대기해야 하는 시간(초)입니다. |
| `message`        | string | 사용자에 대한 지침이 포함된 사람이 읽을 수 있는 문자열입니다. 이는 `?mkt=xx-XX` 양식의 요청에 **쿼리 매개 변수**를 포함하고 적절한 언어 문화권 코드를 채워서 지역화할 수 있습니다. |

> [!NOTE]
> The `verification_uri_complete` response field is not included or supported at this time.  We mention this because if you read the [standard](https://tools.ietf.org/html/rfc8628) you see that `verification_uri_complete` is listed as an optional part of the device code flow standard.

## <a name="authenticating-the-user"></a>사용자 인증 중

After receiving the `user_code` and `verification_uri`, the client displays these to the user, instructing them to sign in using their mobile phone or PC browser.

If the user authenticates with a personal account (on /common or /consumers), they will be asked to sign in again in order to transfer authentication state to the device.  They will also be asked to provide consent, to ensure they are aware of the permissions being granted.  This does not apply to work or school accounts used to authenticate. 

사용자가 `verification_uri`에서 인증하는 동안 클라이언트는 `device_code`을(를) 사용하여 요청된 토큰에 대한 `/token` 엔드포인트를 폴링해야 합니다.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| 매개 변수를 포함해야 합니다. | 필수 | 설명|
| -------- | -------- | ---------- |
| `tenant`  | 필수 | The same tenant or tenant alias used in the initial request. | 
| `grant_type` | 필수 | `urn:ietf:params:oauth:grant-type:device_code`이어야 합니다.|
| `client_id`  | 필수 | 초기 요청에 사용된 `client_id`과(와) 일치해야 합니다. |
| `device_code`| 필수 | 디바이스 권한 요청에서 반환된 `device_code`입니다.  |

### <a name="expected-errors"></a>예상 오류

The device code flow is a polling protocol so your client must expect to receive errors before the user has finished authenticating.  

| 오류 | 설명 | 클라이언트 작업 |
| ------ | ----------- | -------------|
| `authorization_pending` | The user hasn't finished authenticating, but hasn't canceled the flow. | 최소 `interval`초 후에 요청을 반복하세요. |
| `authorization_declined` | 일반 사용자가 권한 요청을 거부했습니다.| 폴링을 중지하고 인증되지 않은 상태로 되돌립니다.  |
| `bad_verification_code`| The `device_code` sent to the `/token` endpoint wasn't recognized. | 클라이언트가 요청에서 올바른 `device_code`을(를) 보내는지 확인하세요. |
| `expired_token` | `expires_in`초 이상이 경과했으며 이 `device_code`을(를) 사용하여 더 이상 인증할 수 없습니다. | Stop polling and revert to an unauthenticated state. |   

### <a name="successful-authentication-response"></a>성공적인 인증 응답

성공적인 토큰 응답은 다음과 같습니다.

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| 매개 변수를 포함해야 합니다. | 형식 | 설명 |
| --------- | ------ | ----------- |
| `token_type` | string| 항상 “전달자입니다. |
| `scope` | 공백으로 구분된 문자열 | 액세스 토큰이 반환된 경우, 이는 액세스 토큰이 유효한 범위를 나열합니다. |
| `expires_in`| int | 포함된 액세스 토큰이 유효하기 전의 시간(초)입니다. |
| `access_token`| 불투명 문자열 | 요청된 [범위](v2-permissions-and-consent.md)에 대해 발급되었습니다.  |
| `id_token`   | JWT | 원래 `scope` 매개 변수에 `openid` 범위가 포함된 경우에 발급됩니다.  |
| `refresh_token` | 불투명 문자열 | 원래 `scope` 매개 변수에 `offline_access`가 포함된 경우에 발급됩니다.  |

You can use the refresh token to acquire new access tokens and refresh tokens using the same flow documented in the [OAuth Code flow documentation](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
