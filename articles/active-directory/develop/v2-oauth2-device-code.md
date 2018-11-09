---
title: Azure AD v2.0을 사용하여 브라우저가 없는 장치에서 사용자 로그인 | Microsoft Docs
description: 장치 코드 부여를 사용하여 브라우저 없는 포함된 인증 흐름을 빌드하세요.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 780eec4d-7ee1-48b7-b29f-cd0b8cb41ed3
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 29cbb96cc6dec4bac601e8795599f77c955c418a
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230832"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-device-code-flow"></a>Azure Active Directory v2.0 및 OAuth 2.0 장치 코드 흐름

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Azure AD는 [장치 코드 부여](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12)를 지원합니다. 이 코드를 사용하면 사용자가 스마트 TV, IoT 장치 또는 프린터와 같은 입력 제한된 장치에 로그인할 수 있습니다.  이 흐름을 활성화하기 위해, 해당 장비는 사용자가 로그인할 웹 페이지를 다른 장비의 브라우저에서 방문하도록 합니다.  사용자가 로그인하면 장치는 필요에 따라 액세스 토큰 및 새로 고침 토큰을 가져올 수 있습니다.  

> [!Important] 
> 이때 v2.0 엔드포인트는 Azure AD 테넌트의 장치 흐름만 지원하지만 개인 계정은 지원하지 않습니다.  이는 테넌트 엔드포인트 또는 조직 엔드포인트를 사용해야 함을 의미합니다.  
>
> Azure AD 테넌트에 초대된 개인 계정은 해당 장치 흐름 부여를 사용할 수 있지만, 테넌트의 컨텍스트에서만 사용할 수 있습니다.

> [!NOTE]
> v2.0 엔드포인트에서는 일부 Azure Active Directory 시나리오 및 기능만 지원합니다. v2.0 엔드포인트를 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.
>

## <a name="protocol-diagram"></a>프로토콜 다이어그램

전체 장치 코드 흐름은 다음 다이어그램과 유사합니다. 이 문서의 뒷부분에 각 단계에 대한 설명이 나와 있습니다.

![장치 코드 흐름](media/v2-oauth2-device-flow/v2-oauth-device-flow.png)

## <a name="device-authorization-request"></a>장치 권한 부여 요청

클라이언트는 인증을 시작하는 데 사용되는 디바이스 및 사용자 코드에 대해 인증 서버를 먼저 확인해야 합니다.  클라이언트는 `/devicecode` 엔드포인트에서 이 요청을 수집합니다. 이 요청에서 클라이언트는 사용자로부터 획득해야 하는 권한을 포함해야 합니다.  이 요청이 전송되는 순간부터, 사용자는 15분 동안 로그인(`expires_in`의 일반적인 값)해야 하므로 사용자가 로그인할 준비가 되었을 때 이 요청을 작성하세요.

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 매개 변수 | 조건 | 설명 |
| --- | --- | --- |
| tenant |필수 |사용 권한을 요청하려는 디렉터리 테넌트입니다. 이는 GUID 또는 친숙한 이름 형식일 수 있습니다.  |
| client_id |필수 |[응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 앱에 할당한 응용 프로그램 ID입니다. |
| scope | 권장 | 사용자가 동의하게 할 공백으로 구분된 [범위](v2-permissions-and-consent.md) 목록입니다.  |

### <a name="device-authorization-response"></a>장치 권한 부여 응답

성공적인 응답은 사용자가 로그인할 수 있는 필수 정보가 포함된 JSON 개체입니다.  

| 매개 변수 | 형식 | 설명 |
| ---              | --- | --- |
|`device_code`     |문자열| 클라이언트와 권한 서버 간의 세션을 확인하는 데 사용되는 긴 문자열입니다.  이는 클라이언트가 권한 서버에서 액세스 토큰을 요청하는 데 사용됩니다. |
|`user_code`       |문자열| 보조 장치에서 세션을 식별하는 데 사용되는 짧은 문자열로 사용자에게 표시됩니다.|
|`verification_uri`|URI| 로그인하기 위해 사용자가 `user_code`을(를) 사용하여 이동하는 URI입니다. |
|`verification_uri_complete`|URI| 사용자(예를 들어, Bluetooth를 통해 장비로 또는 QR 코드를 통해)로 전송하는 텍스트가 아닌 전송에 사용되는 `user_code` 및 `verification_uri`를 결합하는 URI입니다.  |
|`expires_in`      |int| `device_code` 및 `user_code`의 만료 전 시간(초)입니다. |
|`interval`        |int| 클라이언트가 폴링 요청 간에 대기해야 하는 시간(초)입니다. |
| `message`        |문자열| 사용자에 대한 지침이 포함된 사람이 읽을 수 있는 문자열입니다.  이는 `?mkt=xx-XX` 양식의 요청에 **쿼리 매개 변수**를 포함하고 적절한 언어 문화권 코드를 채워서 지역화할 수 있습니다. |

## <a name="authenticating-the-user"></a>사용자 인증 중

`user_code` 및 `verification_uri`을(를) 받은 후에는, 클라이언트에서 이를 사용자에게 표시하여 휴대폰이나 PC 브라우저로 로그인하도록 알려줍니다.  또한 클라이언트는 QR 코드 또는 유사한 메커니즘을 사용하여 사용자의 `user_code`을(를) 입력하는 단계를 수행할 `verfication_uri_complete`을(를) 표시할 수 있습니다.

사용자가 `verification_uri`에서 인증하는 동안 클라이언트는 `device_code`을(를) 사용하여 요청된 토큰에 대한 `/token` 엔드포인트를 폴링해야 합니다.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

|매개 변수 | 필수 | 설명|
| -------- | -------- | ---------- |
|`grant_type` | 필수| `urn:ietf:params:oauth:grant-type:device_code`이어야 합니다.|
|`client_id`  | 필수| 초기 요청에 사용된 `client_id`과(와) 일치해야 합니다. |
|`device_code`| 필수| 장치 권한 요청에서 반환된 `device_code`입니다.  |

### <a name="expected-errors"></a>예상 오류

장치 코드 흐름이 폴링 프로토콜이므로 사용자가 인증을 완료하기 전에 클라이언트가 오류 수신을 예상해야 합니다.  

| 오류 | 설명 | 클라이언트 작업 |
|------ | ----------- | -------------|
| `authorization_pending` |  사용자가 아직 인증을 완료하지 않았지만 흐름을 취소하지 않았습니다. | 최소 `interval`초 후에 요청을 반복하세요. |
| `authorization_declined`|  일반 사용자가 권한 요청을 거부했습니다.| 폴링을 중지하고 인증되지 않은 상태로 되돌립니다.  |
| `bad_verification_code`|`/token` 엔드포인트로 보낸 `device_code`이(가) 인식되지 않았습니다. | 클라이언트가 요청에서 올바른 `device_code`을(를) 보내는지 확인하세요. |
| `expired_token`|  `expires_in`초 이상이 경과했으며 이 `device_code`을(를) 사용하여 더 이상 인증할 수 없습니다. | 폴링을 중지하고 인증되지 않은 상태로 되돌립니다. |


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
|`token_type` | 문자열| 항상 “전달자입니다. |
|`scope` | 공백으로 구분된 문자열 | 액세스 토큰이 반환된 경우, 이는 액세스 토큰이 유효한 범위를 나열합니다. |
|`expires_in`| int | 포함된 액세스 토큰이 유효하기 전의 시간(초)입니다. |
|`access_token`| 불투명 문자열 | 요청된 [범위](v2-permissions-and-consent.md)에 대해 발급되었습니다.  |
|`id_token`   | JWT | 원래 `scope` 매개 변수에 `openid` 범위가 포함된 경우에 발급됩니다.  |
|`refresh_token` | 불투명 문자열 | 원래 `scope` 매개 변수에 `offline_access`가 포함된 경우에 발급됩니다.  |

새로 고침 토큰은 [OAuth 코드 흐름 문서](v2-oauth2-auth-code-flow.md#refresh-the-access-token)에서 자세히 설명된 동일한 흐름을 사용하여 새 액세스 토큰과 새로 고침 토큰을 가져오는 데 사용할 수 있습니다.  