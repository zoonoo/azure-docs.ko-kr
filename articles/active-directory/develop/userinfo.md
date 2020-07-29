---
title: Microsoft id 플랫폼 UserInfo 끝점 | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft id 플랫폼의 UserInfo 끝점에 대해 알아봅니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 08386e2c54a45fe4a6e35ce6d7ebb51145827a6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84268441"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Microsoft id 플랫폼 UserInfo 끝점

사용자 정보 끝점은 인증 된 사용자에 대 한 클레임을 반환 하도록 설계 된 oidc ( [Openid connect Connect standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) )의 일부입니다.  Microsoft id 플랫폼의 경우 UserInfo 끝점은 Microsoft Graph ()에서 호스팅됩니다 https://graph.microsoft.com/oidc/userinfo) . 

## <a name="find-the-well-known-configuration-endpoint"></a>잘 알려진 구성 끝점을 찾습니다.

에서 Openid connect Connect 검색 문서를 사용 하 여 사용자 정보 끝점을 프로그래밍 방식으로 검색할 수 있습니다 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . `userinfo_endpoint`이 패턴은 필드에 나열 되며이 패턴은 올바른 끝점을 가리키는 데 도움이 되는 클라우드 전체에서 사용할 수 있습니다.  앱에서 사용자 정보 끝점을 하드 코딩 하는 것은 권장 되지 않습니다. 대신 OIDC 검색 문서를 사용 하 여이 끝점을 대신 찾습니다.

Openid connect Connect 사양의 일부로 사용자 정보 끝점은 사용자에 대 한 정보를 얻기 위해 [Oidc 규격 라이브러리](https://openid.net/developers/certified/) 에서 자동으로 호출 되는 경우가 많습니다.  이러한 끝점을 호스팅하지 않고 Microsoft id 플랫폼은 표준 규격이 아니고 일부 라이브러리가 실패 합니다.  [OIDC 표준에서 식별 된 클레임 목록](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) 에서 동의한에 대 한 이름 클레임, 주체 클레임 및 전자 메일을 생성 합니다.  

## <a name="consider-use-an-id-token-instead"></a>고려 사항: ID 토큰을 대신 사용 합니다.

앱에서 받을 수 있는 ID 토큰에서 사용할 수 있는 정보는 UserInfo 끝점에서 얻을 수 있는 정보의 상위 집합입니다.  사용자가 사용자 정보 끝점을 호출 하는 토큰을 가져오는 동시에 ID 토큰을 가져올 수 있으므로 사용자 정보 끝점을 호출 하는 대신 해당 ID 토큰을 사용 하 여 사용자에 대 한 정보를 가져오는 것이 좋습니다.  ID 토큰을 사용 하면 응용 프로그램 시작에서 1 ~ 2 개의 네트워크 요청을 제거 하 여 응용 프로그램의 대기 시간을 줄일 수 있습니다.

사용자에 대 한 자세한 정보를 요구 하는 경우에는 [Microsoft Graph `/user` API](https://docs.microsoft.com/graph/api/user-get) 를 호출 하 여 사무실 번호 또는 직위와 같은 정보를 얻어야 합니다.   [선택적 클레임](active-directory-optional-claims.md) 을 사용 하 여 ID 및 액세스 토큰에 추가 사용자 정보를 포함할 수도 있습니다.

## <a name="calling-the-userinfo-endpoint"></a>UserInfo 끝점 호출

UserInfo는 Microsoft Graph에 대 한 토큰을 가져올 때 받은 액세스 토큰을 사용 하 여 다른 Microsoft Graph API와 마찬가지로 호출 되는 표준 OAuth 전달자 토큰 API입니다. 사용자에 대 한 클레임이 포함 된 JSON 응답을 반환 합니다.

### <a name="permissions"></a>사용 권한

다음 [Oidc 사용 권한을](v2-permissions-and-consent.md#openid-connect-scopes) 사용 하 여 UserInfo API를 호출 합니다. `openid`는 필수 이며 `profile` 및 범위는 `email` 응답에 추가 정보가 제공 되도록 합니다.

|사용 권한 유형      | 사용 권한    |
|:--------------------|:---------------------------------------------------------|
|위임 됨 (회사 또는 학교 계정) | openid connect (필수), 프로필, 전자 메일 |
|위임 됨 (개인 Microsoft 계정) | openid connect (필수), 프로필, 전자 메일 |
|애플리케이션 | 적용할 수 없음 |

> [!TIP]
> 브라우저에서이 URL을 복사 하 여 [ID 토큰](id-tokens.md) 뿐만 아니라 UserInfo 끝점에 대 한 토큰을 가져온 다음 클라이언트 id 및 리디렉션 URI를 고유한 이름으로 바꿉니다. Openid connect 또는 Graph 범위에 대 한 범위만 요청 하 고 다른 것은 요청 하지 않습니다.  동일한 토큰 요청에서 서로 다른 두 리소스에 대 한 사용 권한을 요청할 수 없기 때문에이는 필수입니다.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> 다음 섹션에서이 액세스 토큰을 사용할 수 있습니다.

다른 Microsoft Graph 토큰과 마찬가지로 여기에서 받은 토큰은 JWT가 될 수 없습니다. Microsoft 계정 사용자가 로그인 한 경우 암호화 된 토큰 형식이 됩니다. Microsoft Graph에는 특별 한 토큰 발급 패턴이 있으므로이는입니다. 액세스 토큰을 사용 하 여 UserInfo 끝점을 호출 하는 기능에는 영향을 주지 않습니다.

### <a name="calling-the-api"></a>API 호출

UserInfo API는 OIDC 사양에 따라 GET 및 POST를 모두 지원 합니다.

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

을 포함 하 여 여기에 나열 된 클레임은 앱에 대해 `sub` 발급 된 [ID 토큰](id-tokens.md) 에서 앱이 표시 하는 것과 동일한 클레임입니다.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>UserInfo 끝점에 대 한 참고 사항 및 주의 사항

* 이 UserInfo 끝점을 호출 하려면 v2.0 끝점을 사용 해야 합니다.  V2.0 끝점을 사용 하는 경우 login.microsoftonline.com에 호스트 된 v 1.0 UserInfo 끝점에 대 한 토큰을 가져옵니다.  모든 OIDC 규격 앱 및 라이브러리는 v2.0 끝점을 사용 하 여 호환성을 보장 하는 것이 좋습니다.
* UserInfo 끝점의 응답은 사용자 지정할 수 없습니다.  클레임을 사용자 지정 하려면 [클레임 매핑을]( active-directory-claims-mapping.md) 사용 하 여 토큰에서 반환 되는 정보를 편집 하세요.
* UserInfo 끝점의 응답을에 추가할 수 없습니다.  사용자에 대 한 추가 클레임을 받으려면 [선택적 클레임]( active-directory-optional-claims.md) 을 사용 하 여 토큰에 새 클레임을 추가 하세요.

## <a name="next-steps"></a>다음 단계

* [ID 토큰의 내용 검토](id-tokens.md)
* [선택적 클레임을 사용 하 여 ID 토큰의 콘텐츠 사용자 지정](active-directory-optional-claims.md)
* [OAuth2 프로토콜을 사용 하 여 액세스 토큰 및 ID 토큰 요청](v2-protocols-oidc.md)