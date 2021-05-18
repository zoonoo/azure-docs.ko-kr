---
title: Microsoft ID UserInfo 엔드포인트 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼의 UserInfo 엔드포인트에 대해 알아봅니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3bd0621d607b121f19bc47c717343b8e2e39b04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752724"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Microsoft ID 플랫폼 UserInfo 엔드포인트

UserInfo 엔드포인트는 OIDC([OpenID Connect 표준](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo)) 일부이며 인증된 사용자에 대한 클레임을 반환하도록 설계되었습니다.  Microsoft ID 플랫폼의 경우 UserInfo 엔드포인트는 Microsoft Graph(https://graph.microsoft.com/oidc/userinfo) 에서 호스팅됩니다. 

## <a name="find-the-well-known-configuration-endpoint"></a>잘 알려진 구성 엔드포인트 찾기

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`에서 OpenID Connect 검색 문서를 사용하여 UserInfo 엔드포인트를 프로그래밍 방식으로 검색할 수 있습니다. 이 패턴은 `userinfo_endpoint` 필드에 나열되며 이 패턴은 올바른 엔드포인트를 가리키는 데 도움이 되는 클라우드 전반에서 사용할 수 있습니다.  앱에서 UserInfo 엔드포인트를 하드 코딩 하는 것은 권장되지 않습니다. 대신 OIDC 검색 문서를 사용하여 이 엔드포인트를 찾습니다.

OpenID Connect 사양의 일부로 UserInfo 엔드포인트는 사용자에 대한 정보를 얻기 위해 [OIDC 규격 라이브러리](https://openid.net/developers/certified/)에서 자동으로 호출되는 경우가 많습니다.  이러한 엔드포인트를 호스팅하지 않고 Microsoft ID 플랫폼은 표준 규격을 준수하지 않으며 일부 라이브러리가 실패합니다.  [OIDC 표준에서 식별된 클레임 목록](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims)에서 이용 가능하고 동의한 이름 클레임, 주체 클레임 및 이메일을 생성합니다.  

## <a name="consider-use-an-id-token-instead"></a>고려 사항: ID 토큰을 대신 사용합니다

앱에서 받을 수 있는 ID 토큰에서 사용할 수 있는 정보는 UserInfo 엔드포인트에서 얻을 수 있는 정보의 상위 집합입니다.  사용자가 사용자 정보 엔드포인트를 호출하는 토큰을 가져오는 동시에 ID 토큰을 가져올 수 있으므로 UserInfo 엔드포인트를 호출하는 대신 해당 ID 토큰을 사용하여 사용자에 대한 정보를 가져오는 것이 좋습니다.  ID 토큰을 사용하면 애플리케이션 시작에서 1 ~ 2개의 네트워크 요청을 제거하여 애플리케이션의 대기 시간을 줄일 수 있습니다.

사용자에 대한 자세한 정보를 요구하는 경우에는 [Microsoft Graph `/user` API](/graph/api/user-get)를 호출하여 사무실 번호 또는 직위와 같은 정보를 얻어야 합니다.   [선택적 클레임](active-directory-optional-claims.md)을 사용하여 ID 및 액세스 토큰에 추가 사용자 정보를 포함할 수도 있습니다.

## <a name="calling-the-userinfo-endpoint"></a>UserInfo 엔드포인트 호출

UserInfo는 Microsoft Graph에 대한 토큰을 가져올 때 받은 액세스 토큰을 사용하여 다른 Microsoft Graph API와 마찬가지로 호출되는 표준 OAuth Bearer 토큰 API입니다. 사용자에 대한 클레임이 포함된 JSON 응답을 반환합니다.

### <a name="permissions"></a>권한

다음 [OIDC 사용 권한](v2-permissions-and-consent.md#openid-connect-scopes)을 사용하여 UserInfo API를 호출합니다. `openid`이 필요하며, `profile` 및 `email` 범위는 응답에 추가 정보가 제공되도록 합니다.

|사용 권한 유형      | 권한    |
|:--------------------|:---------------------------------------------------------|
|위임됨(회사 또는 학교 계정) | openid(필수), 프로필, 이메일 |
|위임됨 (개인 Microsoft 계정) | openid(필수), 프로필, 이메일 |
|애플리케이션 | 해당 없음 |

> [!TIP]
> 브라우저에서 이 URL을 복사하여 [ID 토큰](id-tokens.md) 뿐만 아니라 UserInfo 엔드포인트에 대한 토큰을 가져온 다음 클라이언트 ID 및 리디렉션 URI를 고유한 이름으로 바꿉니다. OpenID 또는 Graph 범위에 대한 범위만 요청하고 다른 것은 요청하지 않습니다.  동일한 토큰 요청에서 서로 다른 두 리소스에 대한 사용 권한을 요청할 수 없기 때문에 이는 필수입니다.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> 다음 섹션에서 이 액세스 토큰을 사용할 수 있습니다.

다른 Microsoft Graph 토큰과 마찬가지로 여기에서 받은 토큰은 JWT가 될 수 없습니다. Microsoft 계정 사용자가 로그인한 경우 암호화된 토큰 형식이 됩니다. 이는 Microsoft Graph가 특별한 토큰 발급 패턴을 가지고 있기 때문입니다. 액세스 토큰을 사용하여 UserInfo 엔드포인트를 호출하는 기능에는 영향을 주지 않습니다.

### <a name="calling-the-api"></a>API 호출

UserInfo API는 OIDC 사양에 따라 GET 및 POST를 모두 지원합니다.

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>UserInfo 응답

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

여기에 나열된 클레임은 UserInfo 엔드포인트가 반환할 수 있는 모든 클레임입니다.  앱에 발급된 [ID 토큰](id-tokens.md)에서 앱이 표시하는 것과 동일한 값입니다.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>UserInfo 엔드포인트에 대한 참고 사항 및 제한 사항

* 이 UserInfo 엔드포인트를 호출하려면 v2.0 엔드포인트를 사용해야 합니다.  v1.0 엔드포인트를 사용하는 경우 login.microsoftonline.com에 호스트된 v1.0 UserInfo 엔드포인트에 대한 토큰을 가져옵니다.  모든 OIDC 규격 앱 및 라이브러리는 v2.0 엔드포인트를 사용하여 호환성을 보장하는 것이 좋습니다.
* UserInfo 엔드포인트의 응답은 사용자 지정할 수 없습니다.  클레임을 사용자 지정하려면 [클레임 매핑을]( active-directory-claims-mapping.md) 사용하여 토큰에서 반환되는 정보를 편집하세요.
* UserInfo 엔드포인트의 응답에 추가할 수 없습니다.  사용자에 대한 추가 클레임을 받으려면 [선택적 클레임]( active-directory-optional-claims.md)을 사용하여 토큰에 새 클레임을 추가하세요.

## <a name="next-steps"></a>다음 단계

* [ID 토큰의 콘텐츠 검토](id-tokens.md)
* [선택적 클레임을 사용하여 ID 토큰의 콘텐츠 사용자 지정](active-directory-optional-claims.md)
* [OAuth2 프로토콜을 사용하여 액세스 토큰 및 ID 토큰 요청](v2-protocols-oidc.md)
