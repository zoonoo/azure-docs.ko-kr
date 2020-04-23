---
title: Microsoft ID 플랫폼 관리자 동의 프로토콜
description: 범위, 사용 권한 및 동의를 포함하여 Microsoft ID 플랫폼 끝점에서 권한 부여에 대한 설명입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 537d609c1281929203d1891f37614b7627e1683a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868677"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼에 대한 관리자 동의

일부 사용 권한은 테넌트 내에서 부여되기 전에 관리자의 동의가 필요합니다.  관리자 동의 끝점을 사용하여 전체 테넌트에 권한을 부여할 수도 있습니다.

## <a name="recommended-sign-the-user-into-your-app"></a>권장 사항: 앱에 사용자를 로그인

일반적으로 관리 동의 엔드포인트를 사용하는 애플리케이션을 빌드할 때 앱에는 관리자가 앱의 사용 권한을 승인할 수 있는 페이지 또는 보기가 필요합니다. 이 페이지는 앱 로그인 흐름의 일부, 앱 설정의 일부 또는 전용 "연결" 흐름일 수 있습니다. 대부분의 경우에 사용자가 회사 또는 학교 Microsoft 계정으로 로그인한 후에 앱은 이 "연결" 보기만을 표시하게 됩니다.

사용자로 앱에 로그인하면 사용자에게 필요한 사용 권한을 승인하도록 요청하기 전에 관리자가 속해 있는 조직을 식별할 수 있습니다. 반드시 필요하지는 않지만 조직 사용자를 위한 직관적인 환경을 만들 수 있습니다. 사용자에 로그인하려면 [Microsoft ID 플랫폼 프로토콜 자습서를 따르십시오.](active-directory-v2-protocols.md)

## <a name="request-the-permissions-from-a-directory-admin"></a>디렉터리 관리에서 사용 권한 요청

조직의 관리자로부터 권한을 요청할 준비가 되면 사용자를 Microsoft ID 플랫폼 *관리자 동의 끝점으로*리디렉션할 수 있습니다.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| 매개 변수     | 조건     | 설명                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | 필수 | 사용 권한을 요청하려는 디렉터리 테넌트입니다. GUID에서 제공한 이름이거나, 친근한 이름 형식이거나, 예제에서처럼 `organizations`으로 일반 참조될 수 있습니다. 개인 계정은 테넌트의 컨텍스트를 제외하고 관리자동의를 제공할 수 없으므로 '일반'을 사용하지 마십시오. 테넌트를 관리하는 개인 계정과의 최상의 호환성을 보장하려면 가능하면 테넌트 ID를 사용하십시오. |
| `client_id` | 필수 | Azure 포털 - 앱 [등록이](https://go.microsoft.com/fwlink/?linkid=2083908) 앱에 할당된 응용 **프로그램(클라이언트) ID입니다.** |
| `redirect_uri` | 필수 |리디렉션 URI는 처리할 앱에 응답을 전송하려는 위치입니다. 앱 등록 포털에 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. |
| `state` | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태를 사용하여 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩할 수 있습니다. |
|`scope`        | 필수      | 응용 프로그램에서 요청하는 사용 권한 집합을 정의합니다. 정적(/.default 사용) 또는 동적 범위일 수 있습니다.  여기에는 OIDC 범위`openid`(, `profile` `email` |


이 시점에서 Azure AD는 테넌트 관리자에게 요청을 완료하기 위해 로그인하도록 요구합니다. 관리자는 매개 변수에서 요청한 모든 권한을 승인하라는 `scope` 메시지가 표시됩니다.  정적 ()`/.default`값을 사용한 경우 v1.0 관리자 동의 끝점처럼 작동하고 앱에 필요한 권한에 있는 모든 범위에 대한 동의를 요청합니다.

### <a name="successful-response"></a>성공적인 응답

관리자가 앱에 대한 사용 권한을 승인하는 경우 성공적인 응답은 다음과 같습니다.

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| 매개 변수         | Description                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| 디렉터리 테넌트는 GUID 형식으로 요청한 권한을 애플리케이션에 부여합니다.|
| `state`           | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다.|
| `scope`          | 응용 프로그램에 대한 액세스 권한이 부여된 권한 집합입니다.|
| `admin_consent`   | `True`로 설정합니다.|

### <a name="error-response"></a>오류 응답

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

성공적인 응답에서 볼 수 있는 매개 변수에 추가하면 오류 매개 변수는 다음과 같습니다.

| 매개 변수          | Description                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다.|
| `error_description`| 개발자가 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다.|
| `tenant`| 디렉터리 테넌트는 GUID 형식으로 요청한 권한을 애플리케이션에 부여합니다.|
| `state`           | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다.|
| `admin_consent`   | 관리자 동의 `True` 흐름에서 이 응답이 발생했음을 나타내도록 설정됩니다.|

## <a name="next-steps"></a>다음 단계
- [앱을 다중 테넌트로 변환하는 방법](howto-convert-app-to-be-multi-tenant.md) 참조
- 권한 [부여 코드 부여 흐름 중에 OAuth 2.0 프로토콜 계층에서 동의가 어떻게 지원되는지](v2-oauth2-auth-code-flow.md#request-an-authorization-code)알아보십시오.
- [다중 테넌트 응용 프로그램이 동의 프레임워크를 사용하여](active-directory-devhowto-multi-tenant-overview.md) "사용자" 및 "관리자" 동의를 구현하여 고급 다중 계층 응용 프로그램 패턴을 지원하는 방법을 알아봅니다.
- [Azure AD 응용 프로그램 동의 환경](application-consent-experience.md) 이해
