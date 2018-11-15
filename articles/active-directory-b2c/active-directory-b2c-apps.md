---
title: Azure Active Directory B2C에 사용할 수 있는 응용 프로그램 유형 | Microsoft Docs
description: Azure Active Directory B2C에 사용할 수 있는 응용 프로그램 유형을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7671a0a99e12463fcce5ff33fbcba7e8677dde05
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006197"
---
# <a name="applications-types-that-can-be-used-in-active-directory-b2c"></a>Azure Active Directory B2C에 사용할 수 있는 응용 프로그램 유형

Azure AD(Azure Active Directory) B2C는 다양한 최신 응용 프로그램 아키텍처의 인증을 지원합니다. 모두 업계 표준 프로토콜인 [OAuth 2.0](active-directory-b2c-reference-protocols.md) 또는 [OpenID Connect](active-directory-b2c-reference-protocols.md)를 기반으로 합니다. 이 문서에서는 선호하는 언어 또는 플랫폼에 독립적으로 빌드할 수 있는 응용 프로그램 유형을 설명합니다. 또한 응용 프로그램 빌드를 시작하기 전에 대략적인 시나리오에 대한 이해를 돕습니다.

Azure AD B2C를 사용하는 모든 응용 프로그램은 [Azure Portal](https://portal.azure.com/)을 사용하여 [Azure AD B2C 테넌트](active-directory-b2c-get-started.md)에 등록되어야 합니다. 응용 프로그램 등록 프로세스는 다음과 같은 값을 수집하고 할당합니다.

* 응용 프로그램을 고유하게 식별하는 **응용 프로그램 ID**
* 응답을 다시 애플리케이션으로 보내는 데 사용할 수 있는 **회신 URL**

Azure AD B2C에 전송되는 각 요청은 **정책**을 지정합니다. 정책은 Azure AD의 동작을 제어합니다. 또한 이러한 엔드포인트를 사용하여 사용자 환경에 대해 고도로 사용자 지정 가능한 집합을 만들 수 있습니다. 공통 정책에는 등록 정책, 로그인 정책 및 프로필 편집 정책이 포함됩니다. 정책에 익숙하지 않은 경우 계속 읽기 전에 Azure AD B2C의 [확장할 수 있는 정책 프레임워크](active-directory-b2c-reference-policies.md) 에 대해 읽어야 합니다.

모든 응용 프로그램의 상호 작용은 높은 수준에서 비슷한 패턴을 따릅니다.

1. 응용 프로그램이 사용자를 v2.0 엔드포인트로 보내어 [정책](active-directory-b2c-reference-policies.md)을 실행합니다.
2. 사용자는 정책 정의에 따라 정책을 완료합니다.
3. 응용 프로그램은 v2.0 엔드포인트에서 보안 토큰을 수신합니다.
4. 응용 프로그램이 보안 토큰을 사용하여 보호된 정보 또는 보호된 리소스에 액세스합니다.
5. 리소스 서버가 보안 토큰의 유효성을 검사하여 액세스 권한을 부여할 수 있는지 확인합니다.
6. 응용 프로그램은 주기적으로 보안 토큰을 새로 고칩니다.

이러한 단계는 빌드 중인 응용 프로그램의 유형에 따라 약간씩 다를 수 있습니다.

## <a name="web-applications"></a>웹 응용 프로그램

서버에서 호스트되며 브라우저를 통해 액세스하는 웹 응용 프로그램(.NET, PHP, Java, Ruby, Python, Node.js 등)의 경우, Azure AD B2C는 모든 사용자 환경에 [OpenID Connect](active-directory-b2c-reference-protocols.md)를 지원합니다. 여기에는 로그인, 등록 및 프로필 관리가 포함됩니다. OpenID Connect의 Azure AD B2C 구현에서 웹 응용 프로그램은 Azure AD로 인증 요청을 발급하여 이러한 사용자 환경을 시작합니다. 요청의 결과는 `id_token`입니다. 이 보안 토큰은 사용자의 ID를 나타냅니다. 또한 클레임 형태로 사용자에 대한 정보를 제공합니다.

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

[Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)에서 응용 프로그램이 사용할 수 있는 토큰 및 클레임 유형에 대해 알아볼 수 있습니다.

웹 응용 프로그램에서 [정책](active-directory-b2c-reference-policies.md)의 각 실행에서는 이러한 수준 높은 단계를 수행합니다.

1. 사용자가 웹 응용 프로그램으로 이동합니다.
2. 웹 응용 프로그램에서 사용자를 Azure AD B2C로 리디렉션하여 실행할 정책을 나타냅니다.
3. 사용자가 정책을 완성합니다.
4. Azure AD B2C에서 `id_token`을 브라우저에 반환합니다.
5. `id_token`이 리디렉션 URI에 게시됩니다.
6. `id_token`의 유효성이 검사되고 세션 쿠키가 설정됩니다.
7. 보안 페이지가 사용자에게 반환됩니다.

Azure AD에서 수신한 공개 서명 키를 사용하여 `id_token` 의 유효성을 확인하는 것으로 사용자의 ID를 충분히 확인할 수 있습니다. 또한 후속 페이지 요청에서 사용자를 식별하는 데 사용할 수 있는 세션 쿠키도 설정합니다.

이 시나리오의 작동 방식을 확인하려면 [시작 섹션](active-directory-b2c-overview.md)의 웹 응용 프로그램 로그인 코드 샘플 중 하나를 체험해 보세요.

간단한 로그인뿐 아니라 웹 서버 응용 프로그램은 백 엔드 웹 서비스에 액세스해야 할 수도 있습니다. 이 경우, 웹 응용 프로그램은 약간 다른 [OpenID Connect 흐름](active-directory-b2c-reference-oidc.md)을 수행하고 권한 부여 코드를 사용하여 토큰을 획득하며 토큰을 새로 고칠 수 있습니다. 이 시나리오는 다음 [Web API 섹션](#web-apis)에서 설명합니다.

## <a name="web-apis"></a>Web API

Azure AD B2C를 사용하여 응용 프로그램의 RESTful Web API와 같은 웹 서비스의 보안을 유지할 수 있습니다. Web API는 토큰을 사용하는 들어오는 HTTP 요청을 인증하여 해당 데이터를 보호하는 데 OAuth 2.0을 사용할 수 있습니다. Web API 호출자는 HTTP 요청의 권한 부여 헤더에 토큰을 추가합니다.

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

그러면 Web API는 토큰을 사용하여 API 호출자의 ID를 확인하고 토큰에 인코드된 클레임에서 호출자에 대한 정보를 추출할 수 있습니다. [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)에서 앱이 사용할 수 있는 토큰 및 클레임 유형에 대해 알아볼 수 있습니다.

Web API는 웹 응용 프로그램, 데스크톱 및 모바일 응용 프로그램, 단일 페이지 응용 프로그램, 서버 쪽 데몬 및 다른 Web API까지 포함하여 많은 유형의 클라이언트에서 토큰을 받을 수 있습니다. 다음은 Web API를 호출하는 웹 응용 프로그램에 대한 전체 흐름을 보여주는 예입니다.

1. 웹 응용 프로그램에서 정책을 실행하고, 사용자는 사용자 환경을 완료합니다.
2. Azure AD B2C에서 `access_token`과 권한 부여 코드를 브라우저에 반환합니다.
3. 브라우저에서 `access_token` 및 권한 부여 코드를 리디렉션 URI에 게시합니다.
4. 웹 서버에서 `access token`의 유효성을 검사하고 세션 쿠키를 설정합니다.
5. `access_token`이 권한 부여 코드, 응용 프로그램 클라이언트 ID 및 자격 증명을 사용하여 Azure AD B2C에 제공됩니다.
6. `access_token` 및 `refresh_token`이 웹 서버에 반환됩니다.
7. 웹 API가 권한 부여 헤더에서 `access_token`을 사용하여 호출됩니다.
8. 웹 API에서 토큰의 유효성을 검사합니다.
9. 보안 데이터가 웹 서버에 반환됩니다.

권한 부여 코드, 새로 고침 토큰 및 토큰을 가져오는 단계에 대한 자세한 내용은 [OAuth 2.0 프로토콜](active-directory-b2c-reference-oauth-code.md)을 참조하세요.

Azure AD B2C를 사용하여 Web API를 보호하는 방법을 알아보려면 [시작 섹션](active-directory-b2c-overview.md)에서 Web API 자습서를 확인하세요.

## <a name="mobile-and-native-applications"></a>모바일 및 네이티브 응용 프로그램

모바일 및 데스크톱 응용 프로그램과 같은 장치에 설치된 응용 프로그램은 사용자 대신 백 엔드 서비스 또는 Web API에 액세스해야 하는 경우가 많습니다. 네이티브 응용 프로그램에 사용자 지정된 ID 관리 환경을 추가하고 Azure AD B2C 및 [OAuth 2.0 권한 부여 코드 흐름](active-directory-b2c-reference-oauth-code.md)을 사용하여 안전하게 백 엔드 서비스를 호출할 수 있습니다.  

이 흐름에서 응용 프로그램은 [정책](active-directory-b2c-reference-policies.md)을 실행하고 사용자가 정책을 완료하면 Azure AD에서 `authorization_code`를 수신합니다. `authorization_code`는 현재 로그인한 사용자를 대신하여 백 엔드 서비스를 호출할 응용 프로그램의 사용 권한을 나타냅니다. 그러면 응용 프로그램은 백그라운드에서 `id_token` 및 `refresh_token`에 대한 `authorization_code`를 교환할 수 있습니다.  응용 프로그램은 HTTP 요청에서 백 엔드 Web API를 인증하는 데 `id_token`을 사용할 수 있습니다. 또한 이전 항목이 만료된 경우 `refresh_token`을 사용하여 새 `id_token`을 가져올 수도 있습니다.

## <a name="current-limitations"></a>현재 제한 사항

### <a name="application-not-supported"></a>애플리케이션 지원 안 됨 

#### <a name="daemonsserver-side-applications"></a>디먼/서버 쪽 응용 프로그램

장기 실행 프로세스를 포함하거나 사용자 없이 작동하는 응용 프로그램은 Web API와 같은 보안 리소스에 액세스하는 방법도 필요합니다. 이러한 응용 프로그램은 사용자의 위임된 ID 대신 응용 프로그램의 ID를 사용하거나 OAuth 2.0 클라이언트 자격 증명 흐름을 사용하여 인증하고 토큰을 가져올 수 있습니다. 클라이언트 자격 증명 흐름은 대리 흐름과 동일하지 않으며 대리 흐름은 서버 간 인증에 사용하지 않아야 합니다.

클라이언트 자격 증명 흐름은 현재 Azure AD B2C에서 지원되지 않지만 Azure AD를 사용하여 클라이언트 자격 증명 흐름을 설정할 수 있습니다. Azure AD B2C 테넌트는 Azure AD Enterprise 테넌트와 일부 기능을 공유합니다.  클라이언트 자격 증명 흐름은 Azure AD B2C 테넌트의 Azure AD 기능을 사용하여 지원됩니다. 

클라이언트 자격 증명 흐름을 설정하려면 [Azure Active Directory v2.0 및 OAuth 2.0 클라이언트 자격 증명 흐름](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds)을 참조하세요. 인증에 성공하면 [Azure AD 토큰 참조](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)에 설명된 대로 Azure AD에서 사용할 수 있도록 형식이 지정된 토큰을 수신합니다.

#### <a name="web-api-chains-on-behalf-of-flow"></a>Web API 체인(On-Behalf-Of 흐름)

많은 아키텍처에는 다른 다운스트림 Web API를 호출해야 하는 Web API가 포함되어 있으며 둘 다 Azure AD B2C로 보안됩니다. 이 시나리오는 Web API 백 엔드를 가지고 있는 네이티브 클라이언트에서 일반적입니다. 그런 다음 Azure AD Graph API와 같은 Microsoft 온라인 서비스를 호출합니다.

On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 JWT 전달자 자격 증명 권한 부여를 사용하여 이 연결된 Web API 시나리오를 지원할 수 있습니다.  그러나 On-Behalf-Of 흐름은 현재 Azure AD B2C에 구현되어 있지 않습니다.

### <a name="reply-url-values"></a>회신 URL 값

현재, Azure AD B2C에 등록된 앱은 제한된 회신 URL 값 집합으로 제한됩니다. 웹앱 및 서비스에 대한 회산 URL은 스키마 `https`로 시작해야 하고 모든 회신 URL 값은 단일 DNS 도메인을 공유해야 합니다. 예를 들어 다음 회신 URL 중 하나가 있는 웹앱은 등록할 수 없습니다.

`https://login-east.contoso.com`

`https://login-west.contoso.com`

등록 시스템은 기존 회신 URL의 전체 DNS 이름을 편집하려는 회신 URL의 DNS 이름과 비교합니다. 다음 조건 중 하나라도 충족되는 경우 DNS 이름을 추가하는 요청이 실패하게 됩니다.

- 새 회신 URL의 전체 DNS 이름이 기존 회신 URL의 DNS 이름과 일치하지 않는 경우
- 새 회신 URL의 전체 DNS 이름이 기존 회신 URL의 하위 도메인이 아닌 경우

예를 들어 앱에 다음 회신 URL이 있는 경우

`https://login.contoso.com`

다음과 같이 추가할 수 있습니다.

`https://login.contoso.com/new`

이 경우 DNS 이름이 정확히 일치합니다. 또는 다음을 수행할 수 있습니다.

`https://new.login.contoso.com`

이 경우 login.contoso.com의 DNS 하위 도메인을 참조합니다. 회신 URL로 login-east.contoso.com 및 login-west.contoso.com을 사용하는 앱을 원하는 경우 다음 회신 URL을 순서대로 추가해야 합니다.

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

뒤의 두 개는 첫 번째 회신 URL의 하위 도메인이므로 추가할 수 있습니다. 

모바일/원시 애플리케이션을 만들 경우 **회신 URL** 대신 **리디렉션 URI**를 정의합니다. 리디렉션 URI를 선택하는 경우 두 가지 중요한 고려 사항이 있습니다.

- **고유**: 리디렉션 URI의 체계는 모든 응용 프로그램에 대해 고유해야 합니다. 보기 `com.onmicrosoft.contoso.appname://redirect/path`에서 `com.onmicrosoft.contoso.appname`은 구성표입니다. 이 패턴을 따라야 합니다. 두 개의 애플리케이션이 동일한 체계를 공유하는 경우 **앱 선택** 대화 상자가 나타납니다. 사용자가 잘못 선택하는 경우 로그인이 실패합니다.
- **전체**: 리디렉션 URI에는 체계 및 경로가 있어야 합니다. 경로는 도메인 다음에 하나 이상의 포워드 슬래시를 포함해야 합니다. 예를 들어 `//contoso/`는 실행되고 `//contoso`는 실행되지 않습니다. 리디렉션 URI에 밑줄과 같은 특수 문자가 없는지 확인합니다.

### <a name="faulted-apps"></a>오류가 발생한 앱

Azure AD B2C 애플리케이션은 다음에서 편집할 수 없습니다.

-  [애플리케이션 등록 포털](https://apps.dev.microsoft.com/)과 같은 다른 애플리케이션 관리 포털
- Graph API 또는 PowerShell 사용

Azure Portal 외부에서 Azure AD B2C 애플리케이션을 편집하는 경우 오류가 발생한 애플리케이션이 되며 더 이상 Azure AD B2C에 사용할 수 없습니다. 응용 프로그램을 삭제하고 다시 만들어야 합니다.

애플리케이션을 삭제하려면 [애플리케이션 등록 포털](https://apps.dev.microsoft.com/)로 이동하고 거기에서 애플리케이션을 삭제합니다. 응용 프로그램을 표시하려면 응용 프로그램의 소유자여야 합니다(테넌트의 관리자가 아님).

