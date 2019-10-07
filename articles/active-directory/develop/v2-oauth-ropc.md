---
title: Microsoft id 플랫폼을 사용 하 여 ROPC (리소스 소유자 암호 자격 증명) 권한 부여를 사용 하 여 사용자 로그인 | Microsoft
description: 리소스 소유자 암호 자격 증명 권한 부여를 사용하여 브라우저 없는 인증 흐름을 지원합니다.
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
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d5324aba5202abb76f07d1eaf43fe214e690393
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193210"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Microsoft id 플랫폼 및 OAuth 2.0 리소스 소유자 암호 자격 증명

Microsoft id 플랫폼은 사용자가 암호를 직접 처리 하 여 응용 프로그램에서 사용자에 게 로그인 할 수 있도록 하는 [ROPC (리소스 소유자 암호 자격 증명) 권한을](https://tools.ietf.org/html/rfc6749#section-4.3)지원 합니다. ROPC flow에는 높은 수준의 신뢰 및 사용자 노출이 필요 하며, 다른 보안 흐름을 사용할 수 없는 경우에만이 흐름을 사용 해야 합니다.

> [!IMPORTANT]
>
> * Microsoft id 플랫폼 끝점은 개인 계정이 아닌 Azure AD 테 넌 트에 대해 ROPC만 지원 합니다. 이는 테넌트별 엔드포인트(`https://login.microsoftonline.com/{TenantId_or_Name}`) 또는 `organizations` 엔드포인트를 사용해야 함을 의미합니다.
> * Azure AD 테넌트에 초대된 개인 계정은 ROPC를 사용할 수 없습니다.
> * 암호가 없는 계정은 ROPC를 통해 로그인할 수 없습니다. 이 경우 앱에 다른 흐름을 사용하는 것이 좋습니다.
> * 사용자가 MFA(Multi-Factor Authentication)를 사용하여 애플리케이션에 로그인해야 하는 경우 사용자가 차단됩니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램

다음 다이어그램은 ROPC 흐름을 보여 줍니다.

![리소스 소유자 암호 자격 증명 흐름을 보여 주는 다이어그램](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>권한 부여 요청

ROPC 흐름은 단일 요청으로, 클라이언트 id와 사용자의 자격 증명을 IDP에 보낸 다음 반환 되는 토큰을 받습니다. 클라이언트는 작업을 수행하기 전에 사용자의 메일 주소(UPN) 및 암호를 요청해야 합니다. 요청이 성공한 즉시 클라이언트는 메모리에서 사용자의 자격 증명을 안전하게 해제해야 합니다. 이 자격 증명을 저장하면 안 됩니다.

> [!TIP]
> Postman에서 이 요청을 실행해 보세요.
> [![Postman에서이 요청을 실행 해 보세요.](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required. 

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| 매개 변수 | 조건 | Description |
| --- | --- | --- |
| `tenant` | 필수 | 사용자를 로그인할 디렉터리 테넌트입니다. 이는 GUID 또는 친숙한 이름 형식일 수 있습니다. 이 매개 변수는 `common` 또는 `consumers`로 설정할 수 없고 `organizations`로 설정할 수 있습니다. |
| `client_id` | 필수 | [Azure Portal 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지가 앱에 할당 된 응용 프로그램 (클라이언트) ID입니다. | 
| `grant_type` | 필수 | `password`로 설정해야 합니다. |
| `username` | 필수 | 사용자의 메일 주소입니다. |
| `password` | 필수 | 사용자 암호입니다. |
| `scope` | 권장 | 앱에 필요한 [범위](v2-permissions-and-consent.md) 또는 권한의 공백으로 구분된 목록입니다. 대화형 흐름에서 관리자 또는 사용자는 미리 이러한 범위에 동의 해야 합니다. |
| `client_secret`| 경우에 따라 필요 | 앱이 공용 클라이언트 `client_secret` 인 경우 또는 `client_assertion` 을 포함할 수 없습니다.  앱이 기밀 클라이언트 인 경우 포함 해야 합니다. | 
| `client_assertion` | 경우에 따라 필요 | 인증서를 사용 하 `client_secret`여 생성 되는 다른 형식입니다.  자세한 내용은 [인증서 자격 증명](active-directory-certificate-credentials.md) 을 참조 하세요. | 

### <a name="successful-authentication-response"></a>성공적인 인증 응답

다음 예에서는 성공적인 토큰 응답을 보여 줍니다.

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
| `token_type` | 문자열 | 항상 `Bearer`로 설정합니다. |
| `scope` | 공백으로 구분된 문자열 | 액세스 토큰이 반환된 경우 이 매개 변수는 액세스 토큰이 유효한 범위를 나열합니다. |
| `expires_in`| int | 포함된 액세스 토큰이 유효한 시간(초)입니다. |
| `access_token`| 불투명 문자열 | 요청된 [범위](v2-permissions-and-consent.md)에 대해 발급되었습니다. |
| `id_token` | JWT | 원래 `scope` 매개 변수에 `openid` 범위가 포함된 경우에 발급됩니다. |
| `refresh_token` | 불투명 문자열 | 원래 `scope` 매개 변수에 `offline_access`가 포함된 경우에 발급됩니다. |

새로 고침 토큰은 [OAuth 코드 흐름 문서](v2-oauth2-auth-code-flow.md#refresh-the-access-token)에 설명된 동일한 흐름을 사용하여 새 액세스 토큰과 새로 고침 토큰을 가져올 수 있습니다.

### <a name="error-response"></a>오류 응답

사용자가 올바른 사용자 이름 또는 암호를 입력하지 않았거나 클라이언트가 요청된 동의를 수신하지 못한 경우 인증에 실패합니다.

| Error | Description | 클라이언트 작업 |
|------ | ----------- | -------------|
| `invalid_grant` | 인증에 실패했습니다. | 자격 증명이 올바르지 않거나 클라이언트에 요청된 범위에 대한 동의가 없습니다. 범위가 부여 `consent_required` 되지 않은 경우 오류가 반환 됩니다. 이 오류가 발생하면 클라이언트는 WebView 또는 브라우저를 사용하여 대화형 프롬프트로 사용자를 전송해야 합니다. |
| `invalid_request` | 요청이 잘못 구성되었습니다. | 권한 부여 형식은 `/common` 또는 `/consumers` 인증 컨텍스트에서 지원 되지 않습니다.  대신 `/organizations` 또는 테 넌 트 ID를 사용 합니다. |

## <a name="learn-more"></a>자세한 정보

* [샘플 콘솔 애플리케이션](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)을 사용하여 직접 ROPC를 사용해 보세요.
* V2.0 끝점을 사용 해야 하는지 여부를 확인 하려면 [Microsoft id 플랫폼 제한 사항](active-directory-v2-limitations.md)을 참조 하세요.
