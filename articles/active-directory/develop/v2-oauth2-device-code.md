---
title: OAuth 2.0 디바이스 코드 흐름 | Azure
titleSuffix: Microsoft identity platform
description: 사용자가 브라우저 없이 로그인하도록 합니다. 디바이스 권한 부여를 사용하여 브라우저 없는 포함된 인증 흐름을 빌드하세요.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: marsma
ms.custom: aaddev
ms.openlocfilehash: 053f56f56c28560b700122a99fc2d97f4c8e0279
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "122642222"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft ID 플랫폼 및 OAuth 2.0 디바이스 권한 부여 흐름

Microsoft ID 플랫폼은 [디바이스 권한 부여](https://tools.ietf.org/html/rfc8628)를 지원합니다. 디바이스 권한 부여를 사용하면 사용자가 스마트 TV, IoT 디바이스 또는 프린터와 같은 입력 제한된 디바이스에 로그인할 수 있습니다.  이 흐름을 사용하도록 설정하기 위해 디바이스에서 사용자가 다른 디바이스의 브라우저에 있는 웹 페이지를 방문하여 로그인하도록 합니다.  사용자가 로그인하면 디바이스에서 액세스 토큰을 가져오고 필요에 따라 해당 토큰을 새로 고칠 수 있습니다.

이 문서에서는 애플리케이션에서 프로토콜에 대해 직접 프로그래밍을 수행하는 방법을 설명합니다.  가능하면 [토큰을 획득하고 보안 Web API를 호출](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)하는 대신, 지원되는 MSAL(Microsoft 인증 라이브러리)을 사용하는 것이 좋습니다.  [MSAL을 사용하는 샘플 앱](sample-v2-code.md)도 살펴봅니다.

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram"></a>프로토콜 다이어그램

전체 디바이스 코드 흐름은 다음 다이어그램과 비슷합니다. 이 문서의 뒷부분에서 각 단계에 대해 설명합니다.

![디바이스 코드 흐름](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>디바이스 권한 부여 요청

클라이언트에서 먼저 인증을 시작하는 데 사용되는 디바이스 및 사용자 코드에 대한 인증 서버를 확인해야 합니다. 클라이언트는 `/devicecode` 엔드포인트에서 이 요청을 수집합니다. 이 요청에서 클라이언트는 사용자로부터 획득해야 하는 권한도 포함해야 합니다. 이 요청이 전송되는 순간부터, 사용자는 15분 동안 로그인(`expires_in`의 일반적인 값)해야 하므로 사용자가 로그인할 준비가 되었을 때 이 요청을 작성하세요.

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile

```

| 매개 변수 | 조건 | 설명 |
| --- | --- | --- |
| `tenant` | 필수 | /common, /consumers 또는 /organizations일 수 있습니다.  GUID 또는 식별 이름 형식의 사용 권한을 요청하려는 디렉터리 테넌트가 될 수도 있습니다.  |
| `client_id` | 필수 | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경이 앱에 할당한 **애플리케이션(클라이언트) ID** 입니다. |
| `scope` | 필수 | 사용자가 동의하게 할 공백으로 구분된 [범위](v2-permissions-and-consent.md) 목록입니다.  |

### <a name="device-authorization-response"></a>디바이스 권한 부여 응답

성공적인 응답은 사용자가 로그인할 수 있게 하는 데 필요한 정보가 포함된 JSON 개체입니다.

| 매개 변수 | 형식 | Description |
| ---              | --- | --- |
|`device_code`     | String | 클라이언트와 권한 부여 서버 간의 세션을 확인하는 데 사용되는 긴 문자열입니다. 클라이언트에서 이 매개 변수를 사용하여 권한 부여 서버의 액세스 토큰을 요청합니다. |
|`user_code`       | String | 보조 디바이스의 세션을 식별하는 데 사용되어 사용자에게 표시되는 짧은 문자열입니다.|
|`verification_uri`| URI | 로그인하기 위해 사용자가 `user_code`을(를) 사용하여 이동하는 URI입니다. |
|`expires_in`      | int | `device_code` 및 `user_code`의 만료 전 시간(초)입니다. |
|`interval`        | int | 클라이언트에서 폴링 요청 간에 가 대기해야 하는 시간(초)입니다. |
| `message`        | String | 사용자를 위한 지침이 포함된 사람이 읽을 수 있는 문자열입니다. 이는 `?mkt=xx-XX` 양식의 요청에 **쿼리 매개 변수** 를 포함하고 적절한 언어 문화권 코드를 채워서 지역화할 수 있습니다. |

> [!NOTE]
> `verification_uri_complete` 응답 필드는 현재 포함되어 있지 않거나 지원되지 않습니다.  이 사항을 언급하는 이유는 [표준](https://tools.ietf.org/html/rfc8628)을 참조하는 경우 `verification_uri_complete`가 디바이스 코드 흐름 표준의 선택적 부분으로 나열된 것을 볼 수 있기 때문입니다.

## <a name="authenticating-the-user"></a>사용자 인증

`user_code` 및 `verification_uri`를 받은 후에는, 클라이언트에서 이를 사용자에게 표시하여 휴대폰이나 PC 브라우저로 로그인하도록 알려줍니다.

사용자가 개인 계정으로 인증하는 경우(/common 또는 /consumers에서) 인증 상태를 디바이스로 전송하기 위해 다시 로그인하도록 요청하는 메시지가 표시됩니다.  부여되는 사용 권한 인식을 위해 동의를 제공하도록 요청하는 메시지도 표시됩니다.  인증에 사용되는 회사 또는 학교 계정에는 이 내용이 적용되지 않습니다.

사용자가 `verification_uri`에서 인증하는 동안 클라이언트는 `device_code`을(를) 사용하여 요청된 토큰에 대한 `/token` 엔드포인트를 폴링해야 합니다.

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:device_code
&client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&device_code=GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| 매개 변수 | 필수 | 설명|
| -------- | -------- | ---------- |
| `tenant`  | 필수 | 초기 요청에 사용된 것과 동일한 테넌트 또는 테넌트 별칭입니다. |
| `grant_type` | 필수 | `urn:ietf:params:oauth:grant-type:device_code`이어야 합니다.|
| `client_id`  | 필수 | 초기 요청에 사용된 `client_id`과(와) 일치해야 합니다. |
| `device_code`| 필수 | 디바이스 권한 요청에서 반환된 `device_code`입니다.  |

### <a name="expected-errors"></a>예상 오류

디바이스 코드 흐름이 폴링 프로토콜이므로 사용자가 인증을 완료하기 전에 클라이언트가 오류 수신을 예상해야 합니다.

| Error | Description | 클라이언트 작업 |
| ------ | ----------- | -------------|
| `authorization_pending` | 사용자가 아직 인증을 완료하지 않았지만 흐름을 취소하지 않았습니다. | 최소 `interval`초 후에 요청을 반복하세요. |
| `authorization_declined` | 일반 사용자가 권한 요청을 거부했습니다.| 폴링을 중지하고 인증되지 않은 상태로 되돌립니다.  |
| `bad_verification_code`| `/token` 엔드포인트로 보낸 `device_code`가 인식되지 않았습니다. | 클라이언트가 요청에서 올바른 `device_code`을(를) 보내는지 확인하세요. |
| `expired_token` | `expires_in`초 이상이 경과했으며 이 `device_code`을(를) 사용하여 더 이상 인증할 수 없습니다. | 폴링을 중지하고 인증되지 않은 상태로 되돌립니다. |

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

| 매개 변수 | 형식 | Description |
| --------- | ------ | ----------- |
| `token_type` | String| 항상 Bearer입니다. |
| `scope` | 공백으로 구분된 문자열 | 액세스 토큰이 반환된 경우 이 매개 변수는 액세스 토큰의 유효 범위를 나열합니다. |
| `expires_in`| int | 포함된 액세스 토큰이 유효하게 될 때까지의 시간(초)입니다. |
| `access_token`| 불투명 문자열 | 요청된 [범위](v2-permissions-and-consent.md)에 대해 발급되었습니다.  |
| `id_token`   | JWT | 원래 `scope` 매개 변수에 `openid` 범위가 포함된 경우에 발급됩니다.  |
| `refresh_token` | 불투명 문자열 | 원래 `scope` 매개 변수에 `offline_access`가 포함된 경우에 발급됩니다.  |

새로 고침 토큰은 [OAuth 코드 흐름 문서](v2-oauth2-auth-code-flow.md#refresh-the-access-token)에 설명된 동일한 흐름을 사용하여 새 액세스 토큰과 새로 고침 토큰을 가져올 수 있습니다.

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]
