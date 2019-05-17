---
title: 브라우저를 사용 하지 않는 장치에서 사용자 로그인에 사용 하 여 Microsoft id 플랫폼 | Azure
description: 디바이스 코드 부여를 사용하여 브라우저 없는 포함된 인증 흐름을 빌드하세요.
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
ms.date: 04/20/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86e875108e0349c0ab08a7217074e2afe23bcacc
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544931"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Microsoft id 플랫폼 및 OAuth 2.0 장치 코드 흐름

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft identity 플랫폼 지원 합니다 [장치 코드 부여](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), 스마트 TV와 같은 입력 제한 된 장치, IoT 장치 또는 프린터에 로그인 할 수 있습니다.  이 흐름을 활성화하기 위해, 해당 장비는 사용자가 로그인할 웹 페이지를 다른 장비의 브라우저에서 방문하도록 합니다.  사용자가 로그인하면 디바이스는 필요에 따라 액세스 토큰 및 새로 고침 토큰을 가져올 수 있습니다.  

> [!IMPORTANT]
> 지금은 Microsoft id 플랫폼 끝점 Azure AD 테 넌 트에 있지만 하지 개인 계정에 대 한 장치 흐름만 지원합니다.  즉, 테 넌 트를 설정 하는 끝점을 사용 해야 또는 `organizations` 끝점입니다.  
>
> Azure AD 테넌트에 초대된 개인 계정은 해당 디바이스 흐름 부여를 사용할 수 있지만, 테넌트의 컨텍스트에서만 사용할 수 있습니다.

> [!NOTE]
> Microsoft id 플랫폼 끝점은 모든 Azure Active Directory 시나리오 및 기능만 지원 하지 않습니다. 에 대 한 자세한 내용은 Microsoft id 플랫폼 끝점을 사용 해야 하는지 여부를 확인 합니다 [Microsoft identity 플랫폼 제한](active-directory-v2-limitations.md)합니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램

전체 디바이스 코드 흐름은 다음 다이어그램과 유사합니다. 이 문서의 뒷부분에 각 단계에 대한 설명이 나와 있습니다.

![디바이스 코드 흐름](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>디바이스 권한 부여 요청

클라이언트 인증을 시작 하는 데 사용 되는 장치 및 사용자 코드에 대 한 인증 서버를 사용 하 여 먼저 확인 해야 합니다. 클라이언트는 `/devicecode` 엔드포인트에서 이 요청을 수집합니다. 이 요청에서 클라이언트는 사용자로부터 획득해야 하는 권한을 포함해야 합니다. 이 요청이 전송되는 순간부터, 사용자는 15분 동안 로그인(`expires_in`의 일반적인 값)해야 하므로 사용자가 로그인할 준비가 되었을 때 이 요청을 작성하세요.

> [!TIP]
> Postman에서 이 요청을 실행해 보세요.
> [![Postman에서 실행](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 매개 변수 | 조건 | 설명 |
| --- | --- | --- |
| `tenant` | 필수 |사용 권한을 요청하려는 디렉터리 테넌트입니다. 이는 GUID 또는 친숙한 이름 형식일 수 있습니다.  |
| `client_id` | 필수 | 합니다 **(클라이언트) 응용 프로그램 ID** 는 합니다 [Azure portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경을 앱에 할당 합니다. |
| `scope` | 권장 | 사용자가 동의하게 할 공백으로 구분된 [범위](v2-permissions-and-consent.md) 목록입니다.  |

### <a name="device-authorization-response"></a>디바이스 권한 부여 응답

성공적인 응답은 사용자가 로그인할 수 있는 필수 정보가 포함된 JSON 개체입니다.  

| 매개 변수 | 형식 | 설명 |
| ---              | --- | --- |
|`device_code`     | String | 클라이언트와 권한 서버 간의 세션을 확인하는 데 사용되는 긴 문자열입니다. 클라이언트는 권한 부여 서버의 액세스 토큰을 요청 하려면이 매개 변수를 사용 합니다. |
|`user_code`       | String | 보조 장치에서 세션을 식별 하는 데 사용 되는 사용자에 게 표시 하는 간단한 문자열입니다.|
|`verification_uri`| URI | 로그인하기 위해 사용자가 `user_code`을(를) 사용하여 이동하는 URI입니다. |
|`verification_uri_complete`| URI | 결합 하는 URI를 `user_code` 하며 `verification_uri`예를 들어 (Bluetooth 장치에 또는 QR 코드를 통해)를 통해 사용자에 게 텍스트가 아닌 전송에 사용 되는 합니다.  |
|`expires_in`      | int | `device_code` 및 `user_code`의 만료 전 시간(초)입니다. |
|`interval`        | int | 클라이언트가 폴링 요청 간에 대기해야 하는 시간(초)입니다. |
| `message`        | String | 사용자에 대한 지침이 포함된 사람이 읽을 수 있는 문자열입니다. 이는 `?mkt=xx-XX` 양식의 요청에 **쿼리 매개 변수**를 포함하고 적절한 언어 문화권 코드를 채워서 지역화할 수 있습니다. |

## <a name="authenticating-the-user"></a>사용자 인증 중

수신 후 합니다 `user_code` 및 `verification_uri`, 클라이언트 표시를 사용자에 게 이러한 휴대폰 또는 PC 브라우저를 사용 하 여 로그인 하도록 지시 합니다.  또한 클라이언트는 QR 코드 또는 유사한 메커니즘을 사용하여 사용자의 `user_code`을(를) 입력하는 단계를 수행할 `verfication_uri_complete`을(를) 표시할 수 있습니다.

사용자가 `verification_uri`에서 인증하는 동안 클라이언트는 `device_code`을(를) 사용하여 요청된 토큰에 대한 `/token` 엔드포인트를 폴링해야 합니다.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| 매개 변수 | 필수 | 설명|
| -------- | -------- | ---------- |
| `grant_type` | 필수 | `urn:ietf:params:oauth:grant-type:device_code`이어야 합니다.|
| `client_id`  | 필수 | 초기 요청에 사용된 `client_id`과(와) 일치해야 합니다. |
| `device_code`| 필수 | 디바이스 권한 요청에서 반환된 `device_code`입니다.  |

### <a name="expected-errors"></a>예상 오류

장치 코드 흐름 폴링 프로토콜 이므로 클라이언트는 오류를 수신 하는 사용자가 인증을 완료 하기 전에 예상 해야 합니다.  

| 오류 | 설명 | 클라이언트 작업 |
| ------ | ----------- | -------------|
| `authorization_pending` | 사용자 인증을 완료 하지 않은 있지만 흐름 취소 되지 않았습니다. | 최소 `interval`초 후에 요청을 반복하세요. |
| `authorization_declined` | 일반 사용자가 권한 요청을 거부했습니다.| 폴링을 중지하고 인증되지 않은 상태로 되돌립니다.  |
| `bad_verification_code`| 합니다 `device_code` 보낼는 `/token` 끝점 인식할 수 없습니다. | 클라이언트가 요청에서 올바른 `device_code`을(를) 보내는지 확인하세요. |
| `expired_token` | `expires_in`초 이상이 경과했으며 이 `device_code`을(를) 사용하여 더 이상 인증할 수 없습니다. | 폴링을 중지 하 고 인증 되지 않은 상태로 되돌립니다. |

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

| 매개 변수 | 형식 | 설명 |
| --------- | ------ | ----------- |
| `token_type` | String| 항상 “전달자입니다. |
| `scope` | 공백으로 구분된 문자열 | 액세스 토큰이 반환된 경우, 이는 액세스 토큰이 유효한 범위를 나열합니다. |
| `expires_in`| int | 포함된 액세스 토큰이 유효하기 전의 시간(초)입니다. |
| `access_token`| 불투명 문자열 | 요청된 [범위](v2-permissions-and-consent.md)에 대해 발급되었습니다.  |
| `id_token`   | JWT | 원래 `scope` 매개 변수에 `openid` 범위가 포함된 경우에 발급됩니다.  |
| `refresh_token` | 불투명 문자열 | 원래 `scope` 매개 변수에 `offline_access`가 포함된 경우에 발급됩니다.  |

새로 고침 토큰을 사용 하 여 새 액세스 토큰 획득 및 새로 고침 토큰에 설명 된 동일한 흐름을 사용 하 여 [OAuth 코드 흐름 설명서](v2-oauth2-auth-code-flow.md#refresh-the-access-token)합니다.  
