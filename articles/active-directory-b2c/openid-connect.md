---
title: 오픈ID 연결을 사용 하 여 웹 로그인 - Azure 활성 디렉터리 B2C
description: Azure Active Directory B2C에서 OpenID Connect 인증 프로토콜을 사용하여 웹 응용 프로그램을 빌드합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6640ab1660e6499a97a8c990a0001d5fbae4e997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264389"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 OpenID Connect로 웹 로그인

OpenID Connect는 웹 애플리케이션에 사용자를 안전하게 로그인하는 데 사용할 수 있도록 OAuth 2.0을 기반으로 하여 빌드된 인증 프로토콜입니다. OpenID Connect의 Azure AD B2C(Azure Active Directory B2C) 구현을 사용하여 웹 애플리케이션의 등록, 로그인 및 기타 ID 관리 환경을 Azure AD( Azure Active Directory)로 아웃소싱할 수 있습니다. 이 가이드에서는 언어에 관계 없이 이 작업을 수행하는 방법을 보여 줍니다. 오픈 소스 라이브러리를 사용하지 않고 HTTP 메시지를 보내고 받는 방법을 설명합니다.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)는 *인증* 프로토콜로 사용하기 위해 OAuth 2.0 *권한 부여* 프로토콜을 확장합니다. 이 인증 프로토콜을 사용하면 단일 사인온을 수행할 수 있습니다. 클라이언트가 사용자의 ID를 확인하고 사용자에 대한 기본 프로필 정보를 얻을 수 있는 *ID 토큰의*개념을 소개합니다.

OAuth 2.0을 확장하기 때문에 응용 프로그램이 액세스 *토큰을*안전하게 획득할 수 있습니다. 액세스 토큰을 사용하여 [권한 부여 서버](protocols-overview.md)로 보안이 유지되는 리소스에 액세스할 수 있습니다. OpenID Connect는 서버에서 호스팅되고 브라우저를 통해 액세스되는 웹 응용 프로그램을 빌드하는 경우 권장됩니다. 토큰에 대한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요를](tokens-overview.md) 참조하십시오.

Azure AD B2C는 단순한 인증 및 권한 부여 보다 더 많은 작업으로 표준 OpenID Connect 프로토콜을 확장합니다. OpenID Connect를 사용하여 등록, 로그인 및 프로필 관리와 같은 사용자 환경을 응용 프로그램에 추가할 수 있는 [사용자 흐름 매개 변수를](user-flow-overview.md)소개합니다.

## <a name="send-authentication-requests"></a>인증 요청 보내기

웹 응용 프로그램이 사용자를 인증하고 사용자 흐름을 실행해야 하는 경우 사용자를 `/authorize` 끝점으로 안내할 수 있습니다. 사용자는 사용자 흐름에 따라 작업을 수행합니다.

이 요청에서 클라이언트는 `scope` 매개 변수의 사용자로부터 획득해야 하는 권한을 나타내고 실행할 사용자 흐름을 지정합니다. 요청의 작동 방식을 보려면 요청을 브라우저에 붙여 넣고 실행해 보십시오. `{tenant}`을 테넌트 이름으로 바꿉니다. 테넌트에 이전에 등록한 응용 프로그램의 앱 ID로 바꿉습니다. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` 또한 정책 이름`{policy}`() 을 테넌트에 있는 정책 `b2c_1_sign_in`이름으로 변경합니다.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| 매개 변수 | 필수 | 설명 |
| --------- | -------- | ----------- |
| {테넌트} | yes | Azure AD B2C 테넌트의 이름 |
| {정책} | yes | 실행할 사용자 흐름입니다. Azure AD B2C 테넌트에서 만든 사용자 흐름의 이름을 지정합니다. 예를 `b2c_1_sign_in`들어: `b2c_1_sign_up`" `b2c_1_edit_profile`또는 . |
| client_id | yes | [Azure 포털이](https://portal.azure.com/) 응용 프로그램에 할당한 응용 프로그램 ID입니다. |
| nonce | yes | 결과 ID 토큰에 클레임으로 포함된 요청에 포함된 값(응용 프로그램에서 생성)입니다. 그런 다음 응용 프로그램에서 이 값을 확인하여 토큰 재생 공격을 완화할 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| response_type | yes | OpenID 연결에 대한 ID 토큰을 포함해야 합니다. 웹 응용 프로그램에 웹 API를 호출하기 위한 토큰도 필요한 경우 을 사용할 `code+id_token`수 있습니다. |
| scope | yes | 공백으로 구분된 범위 목록입니다. `openid` 범위는 사용자에게 로그인하고 ID 토큰 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. 범위는 `offline_access` 웹 응용 프로그램의 경우 선택 사항입니다. 리소스에 대한 확장된 액세스를 위해 응용 프로그램에 *새로 고침 토큰이* 필요하다는 것을 나타냅니다. |
| prompt | 예 | 필요한 사용자 상호 작용의 형식입니다. 현재 유효한 값은 `login`뿐이며, 이는 사용자가 해당 요청에 대한 자격 증명을 입력하도록 합니다. |
| redirect_uri | 예 | 응용 `redirect_uri` 프로그램에서 인증 응답을 보내고 받을 수 있는 응용 프로그램의 매개 변수입니다. URL 인코딩을 해야 `redirect_uri` 한다는 점을 제외하면 Azure 포털에 등록한 매개 변수 중 하나와 정확히 일치해야 합니다. |
| response_mode | 예 | 결과 권한 부여 코드를 응용 프로그램으로 다시 보내는 데 사용되는 메서드입니다. `query`, `form_post` 또는 `fragment` 중 하나일 수 있습니다.  최상의 보안을 위해 `form_post` 응답 모드를 사용하는 것이 좋습니다. |
| state | 예 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전에 응용 프로그램에서 사용자의 상태에 대한 정보를 인코딩하는 데도 사용됩니다(예: 해당 상태가 있는 페이지). |

이 때 사용자에게 워크플로를 완료하라는 메시지가 표시됩니다. 사용자는 사용자 이름과 암호를 입력하거나, 소셜 ID로 로그인하거나, 디렉터리에 등록해야 할 수 있습니다. 사용자 흐름이 정의되는 방식에 따라 다른 수의 단계가 있을 수 있습니다.

사용자가 사용자 흐름을 완료하면 `redirect_uri` `response_mode` 매개 변수에 지정된 메서드를 사용하여 표시된 매개 변수에서 응용 프로그램에 응답이 반환됩니다. 응답은 사용자 흐름과 관계없이 앞의 각 사례에 대해 동일합니다.

`response_mode=fragment` 를 사용한 성공적인 응답은 다음과 같습니다.

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| --------- | ----------- |
| id_token | 응용 프로그램이 요청한 ID 토큰입니다. ID 토큰을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. |
| 코드 | 을 사용하는 `response_type=code+id_token`경우 응용 프로그램에서 요청한 권한 부여 코드입니다. 응용 프로그램은 권한 부여 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. 권한 부여 코드는 일반적으로 약 10분 후에 만료됩니다. |
| state | 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 응용 프로그램은 요청 `state` 및 응답의 값이 동일한지 확인해야 합니다. |

오류 응답은 응용 프로그램이 `redirect_uri` 적절하게 처리할 수 있도록 매개 변수로 전송할 수도 있습니다.

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| --------- | ----------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있는 코드입니다. |
| error_description | 인증 오류의 근본 원인을 식별하는 데 도움이 되는 특정 오류 메시지입니다. |
| state | 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 응용 프로그램은 요청 `state` 및 응답의 값이 동일한지 확인해야 합니다. |

## <a name="validate-the-id-token"></a>ID 토큰 유효성 검사

ID 토큰을 받는 것만으로는 사용자를 인증할 수 없습니다. ID 토큰의 서명을 확인하고 응용 프로그램의 요구 사항에 따라 토큰의 클레임을 확인합니다. Azure AD B2C는 [JWT(JSON 웹 토큰)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다. 기본 설정의 언어에 따라 JWT의 유효성 검사에 사용할 수 있는 다양한 공개 소스 라이브러리가 있습니다. 고유한 유효성 검사 논리를 구현하는 것보다 이러한 옵션을 탐색하는 것이 좋습니다.

Azure AD B2C에는 OpenID Connect 메타데이터 끝점이 있어 응용 프로그램에서 런타임시 Azure AD B2C에 대한 정보를 얻을 수 있습니다. 이 정보에는 엔드포인트, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. B2C 테넌트에서 각 사용자 흐름에 대한 JSON 메타데이터 문서가 있습니다. 예를 들어 `fabrikamb2c.onmicrosoft.com`의 `b2c_1_sign_in` 사용자 흐름에 대한 메타데이터 문서는 다음 위치에 있습니다.

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

이 구성 문서의 속성 중 하나가 `jwks_uri`이며, 동일한 사용자 흐름에 대한 값은 다음과 같습니다.

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

ID 토큰에 서명하는 데 사용된 사용자 흐름과 메타데이터를 가져오는 위치를 확인하려면 두 가지 옵션이 있습니다. 먼저 사용자 흐름 이름이 ID 토큰의 `acr` 클레임에 포함됩니다. 다른 옵션은 요청을 실행할 때 `state` 매개 변수의 값에 사용자 흐름을 인코딩한 다음 이를 디코딩하여 어떤 사용자 흐름을 사용할지 결정하는 것입니다. 두 방법 중 하나는 유효합니다.

OpenID Connect 메타데이터 끝점에서 메타데이터 문서를 구입한 후 RSA 256 공개 키를 사용하여 ID 토큰의 서명을 확인할 수 있습니다. 이 끝점에 나열된 키가 여러 개 있을 `kid` 수 있으며 각 키는 클레임으로 식별됩니다. ID 토큰의 헤더에는 `kid` 클레임도 포함되어 있으며, 이는 이러한 키 중에서 ID 토큰 서명에 사용된 키를 나타냅니다.

Azure AD B2C에서 토큰을 확인하려면 지수(e) 및 모듈러스(n)를 사용하여 공개 키를 생성해야 합니다. 그에 따라 해당 프로그래밍 언어로이 작업을 수행하는 방법을 결정해야합니다. RSA 프로토콜을 가진 공개 키 생성에 대한 공식 문서는 여기에서 찾을 수 있습니다.https://tools.ietf.org/html/rfc3447#section-3.1

ID 토큰의 서명에 대한 유효성을 검사한 후에는 확인해야 할 몇 가지 클레임이 있습니다. 예:

- `nonce` 클레임의 유효성을 검사하여 토큰 재생 공격을 방지합니다. 해당 값이 로그인 요청에 지정된 값이어야 합니다.
- 클레임의 `aud` 유효성을 검사하여 응용 프로그램에 대해 ID 토큰이 발급되었는지 확인합니다. 해당 값은 응용 프로그램의 응용 프로그램 ID여야 합니다.
- `iat` ID 토큰이 만료되지 않았는지 확인합니다. `exp`

또한 수행해야 하는 몇 가지 추가 유효성 검사가 있습니다. 유효성 검사는 [OpenID 연결 코어 사양에](https://openid.net/specs/openid-connect-core-1_0.html)자세히 설명되어 있습니다. 시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

- 사용자/조직이 응용 프로그램에 등록했는지 확인합니다.
- 사용자에게 적절한 권한 부여/권한이 있는지 확인
- Azure Multi-Factor Authentication과 같은 특정 강도의 인증이 발생했는지 확인

ID 토큰의 유효성을 검사한 후 사용자와 세션을 시작할 수 있습니다. ID 토큰의 클레임을 사용하여 응용 프로그램의 사용자에 대한 정보를 얻을 수 있습니다. 이 정보의 용도로 표시, 레코드 및 권한 부여가 있습니다.

## <a name="get-a-token"></a>토큰 가져오기

사용자 흐름만 실행하려면 웹 응용 프로그램이 필요한 경우 다음 몇 섹션을 건너뛸 수 있습니다. 이러한 섹션은 웹 API에 대한 인증된 호출을 수행해야 하는 웹 응용 프로그램에만 적용되며 Azure AD B2C에서 보호됩니다.

`response_type=code+id_token`을 사용하여 `POST` 요청을 `/token` 엔드포인트로 보내 원하는 리소스에 대한 토큰에 대해 얻은 권한 부여 코드를 사용할 수 있습니다. Azure AD B2C에서 요청에서 해당 범위를 지정하여 평소와 같이 [다른 API에 대한 액세스 토큰을 요청할](access-tokens.md#request-a-token) 수 있습니다.

또한 앱의 클라이언트 ID를 요청된 범위로 사용하는 규칙에 따라 앱 의 백 엔드 웹 API에 대한 액세스 토큰을 요청할 수 있습니다(이 경우 해당 클라이언트 ID가 있는 액세스 토큰이 "대상"으로 표시됨).

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 매개 변수 | 필수 | 설명 |
| --------- | -------- | ----------- |
| {테넌트} | yes | Azure AD B2C 테넌트의 이름 |
| {정책} | yes | 권한 부여 코드를 획득하는 데 사용된 사용자 흐름입니다. 이 요청에서는 다른 사용자 흐름을 사용할 수 없습니다. POST 본문이 아닌 쿼리 문자열에 이 매개 변수를 추가합니다. |
| client_id | yes | [Azure 포털이](https://portal.azure.com/) 응용 프로그램에 할당한 응용 프로그램 ID입니다. |
| client_secret | 예, 웹 앱에서 | [Azure 포털에서](https://portal.azure.com/)생성된 응용 프로그램 보안 입니다. 클라이언트 암호는 클라이언트가 클라이언트 보안을 안전하게 저장할 수 있는 웹 앱 시나리오에 이 흐름에서 사용됩니다. 네이티브 앱(공용 클라이언트) 시나리오의 경우 클라이언트 보안을 안전하게 저장할 수 없으며 이 흐름에서 사용되지 않습니다. 클라이언트 비밀을 사용하는 경우 주기적으로 변경하십시오. |
| 코드 | yes | 사용자 흐름의 시작 부분에서 획득한 권한 부여 코드입니다. |
| grant_type | yes | 권한 유형입니다. 인증 코드 흐름의 경우 `authorization_code`이어야 합니다. |
| redirect_uri | yes | 권한 부여 코드를 받은 애플리케이션의 `redirect_uri` 매개 변수입니다. |
| scope | 예 | 공백으로 구분된 범위 목록입니다. `openid` 범위는 사용자에게 로그인하고 id_token 매개 변수의 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. 클라이언트와 동일한 응용 프로그램 ID로 표시되는 응용 프로그램의 백 엔드 웹 API에 대한 토큰을 얻는 데 사용할 수 있습니다. 범위는 `offline_access` 응용 프로그램에 리소스에 대한 확장된 액세스를 위해 새로 고침 토큰이 필요하다는 것을 나타냅니다. |

성공적인 토큰 응답은 다음과 같습니다.

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 매개 변수 | 설명 |
| --------- | ----------- |
| not_before | epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token_type | 토큰 형식 값입니다. `Bearer`은 지원되는 유일한 형식입니다. |
| access_token | 사용자가 요청한 서명된 JWT 토큰입니다. |
| scope | 토큰이 유효한 범위입니다. |
| expires_in | 액세스 토큰이 유효한 시간(초)입니다. |
| refresh_token | OAuth 2.0 새로 고침 토큰입니다. 응용 프로그램은 이 토큰을 사용하여 현재 토큰이 만료된 후 추가 토큰을 획득할 수 있습니다. 새로 고침 토큰은 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 새로 `offline_access` 고침 토큰을 받으려면 권한 부여 및 토큰 요청 모두에서 범위가 사용되어야 합니다. |

오류 응답은 다음과 같습니다.

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | 설명 |
| --------- | ----------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있는 코드입니다. |
| error_description | 인증 오류의 근본 원인을 식별하는 데 도움이 되는 메시지입니다. |

## <a name="use-the-token"></a>토큰 사용

액세스 토큰을 성공적으로 얻었으므로 이제 `Authorization` 헤더에 포함하여 백 엔드 웹 API에 대한 요청에서 토큰을 사용할 수 있습니다.

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>토큰 새로 고침

ID 토큰은 짧은 기간 에 만료됩니다. 토큰이 만료된 후 토큰을 새로 고쳐 리소스에 계속 액세스할 수 있습니다. 끝점에 다른 `POST` 요청을 제출하여 토큰을 `/token` 새로 고칠 수 있습니다. 이번에는 `code` 매개 변수 대신 `refresh_token` 매개 변수를 제공합니다.

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 매개 변수 | 필수 | 설명 |
| --------- | -------- | ----------- |
| {테넌트} | yes | Azure AD B2C 테넌트의 이름 |
| {정책} | yes | 원래의 새로 고침 토큰을 얻는 데 사용된 사용자 흐름입니다. 이 요청에서는 다른 사용자 흐름을 사용할 수 없습니다. POST 본문이 아닌 쿼리 문자열에 이 매개 변수를 추가합니다. |
| client_id | yes | [Azure 포털이](https://portal.azure.com/) 응용 프로그램에 할당한 응용 프로그램 ID입니다. |
| client_secret | 예, 웹 앱에서 | [Azure 포털에서](https://portal.azure.com/)생성된 응용 프로그램 보안 입니다. 클라이언트 암호는 클라이언트가 클라이언트 보안을 안전하게 저장할 수 있는 웹 앱 시나리오에 이 흐름에서 사용됩니다. 네이티브 앱(공용 클라이언트) 시나리오의 경우 클라이언트 보안을 안전하게 저장할 수 없으며 이 호출에서 사용되지 않습니다. 클라이언트 비밀을 사용하는 경우 주기적으로 변경하십시오. |
| grant_type | yes | 권한 부여 코드 흐름의 이 부분에 대한 새로 고침 토큰이어야 하는 권한 부여 유형입니다. |
| refresh_token | yes | 흐름의 두 번째 부분에서 획득한 원래 새로 고침 토큰입니다. 새로 `offline_access` 고침 토큰을 받으려면 권한 부여 및 토큰 요청 모두에서 범위를 사용해야 합니다. |
| redirect_uri | 예 | 권한 부여 코드를 받은 애플리케이션의 `redirect_uri` 매개 변수입니다. |
| scope | 예 | 공백으로 구분된 범위 목록입니다. `openid` 범위는 사용자에게 로그인하고 ID 토큰 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. 클라이언트와 동일한 응용 프로그램 ID로 표시되는 응용 프로그램의 백 엔드 웹 API로 토큰을 보내는 데 사용할 수 있습니다. 범위는 `offline_access` 응용 프로그램에 리소스에 대한 확장된 액세스를 위해 새로 고침 토큰이 필요하다는 것을 나타냅니다. |

성공적인 토큰 응답은 다음과 같습니다.

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 매개 변수 | 설명 |
| --------- | ----------- |
| not_before | epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token_type | 토큰 형식 값입니다. `Bearer`은 지원되는 유일한 형식입니다. |
| access_token | 요청된 서명된 JWT 토큰입니다. |
| scope | 토큰이 유효한 범위입니다. |
| expires_in | 액세스 토큰이 유효한 시간(초)입니다. |
| refresh_token | OAuth 2.0 새로 고침 토큰입니다. 응용 프로그램은 이 토큰을 사용하여 현재 토큰이 만료된 후 추가 토큰을 획득할 수 있습니다. 새로 고침 토큰은 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. |

오류 응답은 다음과 같습니다.

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | 설명 |
| --------- | ----------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있는 코드입니다. |
| error_description | 인증 오류의 근본 원인을 식별하는 데 도움이 되는 메시지입니다. |

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기

응용 프로그램에서 사용자를 로그아웃하려는 경우 응용 프로그램의 쿠키를 지우거나 사용자와 세션을 종료하는 것만으로는 충분하지 않습니다. 사용자를 Azure AD B2C로 리디렉션하여 로그아웃합니다. 이렇게 하지 않으면 사용자는 자격 증명을 다시 입력하지 않고 응용 프로그램에 다시 인증할 수 있습니다.

사용자를 로그아웃하려면 앞에서 설명한 `end_session` OpenID Connect 메타데이터 문서에 나열된 끝점으로 사용자를 리디렉션합니다.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| 매개 변수 | 필수 | 설명 |
| --------- | -------- | ----------- |
| {테넌트} | yes | Azure AD B2C 테넌트의 이름 |
| {정책} | yes | 애플리케이션에서 사용자를 로그아웃하는 데 사용하려는 사용자 흐름입니다. |
| id_token_hint| 예 | 클라이언트와 최종 사용자의 현재 인증 된 세션에 대 한 힌트로 로그아웃 끝점에 전달 하는 이전에 발급 된 ID 토큰입니다. 을 `id_token_hint` 사용하면 Azure `post_logout_redirect_uri` AD B2C 응용 프로그램 설정에서 등록된 회신 URL이 있는지 확인합니다. |
| client_id | 아니요* | [Azure 포털이](https://portal.azure.com/) 응용 프로그램에 할당한 응용 프로그램 ID입니다.<br><br>\**격리 SSO `Application` 구성을 사용할 때 필요하며 _ID 토큰_ 로그인 `No`요청이 로고로 설정되어 있습니다.* |
| post_logout_redirect_uri | 예 | 사용자가 로그아웃한 후 리디렉션되어야 하는 URL입니다. 포함되지 않은 경우 Azure AD B2C는 사용자에게 일반 메시지를 표시합니다. `id_token_hint`을 제공하지 않는 한 Azure AD B2C 응용 프로그램 설정에서 이 URL을 회신 URL로 등록해서는 안 됩니다. |
| state | 예 | 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 응용 프로그램은 요청 `state` 및 응답의 값이 동일한지 확인해야 합니다. |

### <a name="secure-your-logout-redirect"></a>로그아웃 리디렉션 보안

로그아웃 후 사용자는 응용 프로그램에 대해 지정된 `post_logout_redirect_uri` 회신 URL에 관계없이 매개 변수에 지정된 URI로 리디렉션됩니다. 그러나 유효한 값이 `id_token_hint` 전달되면 Azure AD B2C는 리디렉션을 수행하기 전에 응용 프로그램의 구성된 리디렉션 URI 중 하나와 `post_logout_redirect_uri` 일치하는지 확인합니다. 응용 프로그램에 대해 일치하는 회신 URL이 구성되지 않은 경우 오류 메시지가 표시되고 사용자가 리디렉션되지 않습니다.

### <a name="external-identity-provider-sign-out"></a>외부 ID 공급자 로그아웃

사용자를 `end_session` 끝점으로 향하게 하면 Azure AD B2C를 사용하여 사용자의 단일 사인온 상태 중 일부가 지워지지만 IDP(소셜 ID 공급자) 세션에서 사용자를 로그아웃하지는 않습니다. 사용자가 후속 로그인 중에 동일한 IDP를 선택하면 자격 증명을 입력하지 않고 다시 인증됩니다. 사용자가 응용 프로그램에서 로그아웃하려는 경우 반드시 Facebook 계정에서 로그아웃하려는 것은 아닙니다. 그러나 로컬 계정을 사용하는 경우 사용자의 세션이 제대로 종료됩니다.
