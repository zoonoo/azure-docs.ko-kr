---
title: Azure Active Directory 주요 변경 내용 참조
description: 애플리케이션에 영향을 줄 수 있는 Azure AD 프로토콜의 변경 내용을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a60b927f7239818b582ffcd85ddb4b7d69594482
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535964"
---
# <a name="whats-new-for-authentication"></a>인증의 새로운 기능?

>이 페이지의 업데이트에 대한 알림을 받습니다. RSS 피드 리더에 [이 URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us)을 추가하면 됩니다.

인증 시스템에서는 보안 및 표준 준수를 개선하기 위해 지속적으로 변경하고 기능을 추가합니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 세부 정보를 제공합니다.

- 최신 기능
- 알려진 문제
- 프로토콜 변경 내용
- 사용되지 않는 기능

> [!TIP]
> 이 페이지는 정기적으로 업데이트되므로 자주 방문해 주세요. 별도로 명시되지 않는 한, 이 변경 내용은 새로 등록된 애플리케이션에만 적용됩니다.

## <a name="upcoming-changes"></a>예정된 변경

지금은 예약이 없습니다.  생산 중이거나 생산 중인 변경 사항은 아래를 참조하십시오.

## <a name="march-2020"></a>2020년 3월

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>사용자 암호는 256자로 제한됩니다.

**발효일**: 2020년 3월 13일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받은 프로토콜**: 모든 사용자 흐름.

ADFS와 같은 페더레이션된 IDP와 달리 Azure AD에 직접 로그인하는 256자 보다 긴 암호를 가진 사용자는 2020년 3월 13일부터 로그인할 수 없으며 대신 암호를 재설정하라는 메시지가 표시됩니다.  관리자는 사용자 암호를 재설정하는 데 도움이 되는 요청을 받을 수 있습니다.

로그인 로그의 오류는 AADSTS 50052: 유효하지 않은PasswordExceedsMaxLength입니다.

메시지:`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

수정:

암호가 허용된 최대 길이를 초과하여 사용자가 로그인할 수 없습니다. 암호를 재설정하려면 관리자에게 문의해야 합니다. 테넌트에 대해 SSPR을 사용하도록 설정한 경우 "비밀번호 잊어버린" 링크를 따라 암호를 재설정할 수 있습니다.



## <a name="february-2020"></a>2020년 2월

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>빈 조각은 로그인 끝점에서 모든 HTTP 리디렉션에 추가됩니다.

**발효일**: 2020년 2월 8일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받은 프로토콜**: response_type=쿼리를 사용하는 OAuth 및 OIDC 흐름 - 경우에 따라 [권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md) 및 [암시적 흐름을](v2-oauth2-implicit-grant-flow.md)다룹니다.

HTTP 리디렉션을 통해 login.microsoftonline.com 응용 프로그램으로 인증 응답이 전송되면 서비스는 회신 URL에 빈 조각을 추가합니다.  이렇게 하면 브라우저가 인증 요청의 기존 조각을 지워내도록 하여 리디렉션 공격 클래스를 방지할 수 있습니다.  어떤 앱도 이 동작에 대한 종속성을 가져야 합니다.


## <a name="august-2019"></a>2019년 8월

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>POST 양식 의미 체계는 더 엄격하게 적용됩니다 - 공백 및 따옴표는 무시됩니다.

**발효일**: 2019년 9월 2일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**프로토콜 영향**: 어디서나 POST가 사용됩니다[(클라이언트 자격 증명,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) [권한 부여 코드 사용,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow) [ROPC,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)및 [새로 고침 토큰 사용)](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)

9/2 주부터 POST 메서드를 사용하는 인증 요청은 보다 엄격한 HTTP 표준을 사용하여 유효성을 검사합니다.  특히 공백 및 큰따옴표(")는 요청 양식 값에서 더 이상 제거되지 않습니다. 이러한 변경 사항은 기존 클라이언트를 중단하지 않을 것으로 예상되며 Azure AD로 전송된 요청이 매번 안정적으로 처리되도록 합니다. 향후(위 참조) 중복 매개 변수를 추가로 거부하고 요청 내에서 BOM을 무시할 계획입니다.

예:

오늘, `?e=    "f"&g=h` `?e=f&g=h` 동일시 구문 분석된다 `e`  ==  `f`- 그래서 .  이 변경으로 이제 구문 분석되어 `e`  ==  `    "f"` 유효한 인수가 될 가능성이 없으며 요청이 실패할 수 있습니다.


## <a name="july-2019"></a>2019년 7월

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>단일 테넌트 응용 프로그램에 대한 앱 전용 토큰은 클라이언트 앱이 리소스 테넌트에 있는 경우에만 발급됩니다.

**발효일**: 2019년 7월 26일

**영향 받은 끝점**: [v1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) 및 [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**영향을 받은 프로토콜**: [클라이언트 자격 증명(앱 전용 토큰)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

7월 26일에는 클라이언트 자격 증명 부여를 통해 앱 전용 토큰이 발급되는 방식을 변경하는 보안 변경 사항이 적용되었습니다. 이전에는 응용 프로그램이 테넌트 또는 해당 응용 프로그램에 동의한 역할에 관계없이 토큰이 다른 앱을 호출하도록 허용되었습니다.  이 동작은 리소스(웹 API라고도 함)가 단일 테넌트(기본값)로 설정된 경우 클라이언트 응용 프로그램이 리소스 테넌트 내에 존재하도록 업데이트되었습니다.  클라이언트와 API 간의 기존 동의는 여전히 필요하지 않으며 앱은 여전히 `roles` 자체 권한 부여 검사를 수행하여 클레임이 존재하고 API에 대한 예상 값을 포함해야 합니다.

이 시나리오에 대한 오류 메시지는 현재 다음과 같은 상태를 말합니다.

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

이 문제를 해결하려면 관리자 동의 환경을 사용하여 테넌트에서 클라이언트 응용 프로그램 서비스 주체를 만들거나 수동으로 만듭니다.  이 요구 사항은 테넌트가 테넌트 내에서 작동할 수 있는 응용 프로그램 권한을 부여했는지 확인합니다.

#### <a name="example-request"></a>요청 예

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`이 예제에서는 리소스 테넌트(기관)가 contoso.com 리소스 앱은 Contoso 테넌트에 대해 `gateway.contoso.com/api` 호출된 `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`단일 테넌트 앱이며 클라이언트 앱은 .  클라이언트 앱에 Contoso.com 내에 서비스 주체가 있는 경우 이 요청을 계속할 수 있습니다.  그러나 그렇지 않으면 위의 오류로 요청이 실패합니다.

그러나 Contoso 게이트웨이 앱이 다중 테넌트 응용 프로그램인 경우 Contoso.com 내에 서비스 주체가 있는 클라이언트 앱에 관계없이 요청이 계속됩니다.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>리디렉션 URI는 이제 쿼리 문자열 매개 변수를 포함할 수 있습니다.

**발효일**: 2019년 7월 22일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받은 프로토콜**: 모든 흐름

[RFC 6749에](https://tools.ietf.org/html/rfc6749#section-3.1.2)따라 Azure AD 응용 프로그램은 이제 OAuth 2.0 요청에 대해 `https://contoso.com/oauth2?idp=microsoft`정적 쿼리 매개 변수(예: )를 사용하여 리디렉션(회신) URI를 등록하고 사용할 수 있습니다.  동적 리디렉션 URI는 보안 위험을 나타내므로 여전히 금지되며, 이를 위해 `state` 매개 변수를 사용하여 인증 요청 전반에 걸쳐 상태 정보를 유지하는 데 사용할 수 없습니다.

정적 쿼리 매개 변수는 리디렉션 URI의 다른 부분과 마찬가지로 리디렉션 URI에 대한 문자열 일치의 대상이 됩니다 redirect_uri .  앱 등록에서 URI가 발견되면 정적 쿼리 매개 변수를 포함하여 전체 문자열이 사용자를 리디렉션하는 데 사용됩니다.

이 때(2019년 7월 말) Azure 포털의 앱 등록 UX는 여전히 쿼리 매개 변수를 차단합니다.  그러나 응용 프로그램 매니페스트를 수동으로 편집하여 쿼리 매개 변수를 추가하고 앱에서 테스트할 수 있습니다.


## <a name="march-2019"></a>2019년 3월

### <a name="looping-clients-will-be-interrupted"></a>루핑 클라이언트가 중단됩니다.

**발효일**: 2019년 3월 25일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받은 프로토콜**: 모든 흐름

클라이언트 응용 프로그램은 때때로 잘못된 방식으로 행동하여 짧은 기간 동안 수백 개의 동일한 로그인 요청을 발행할 수 있습니다.  이러한 요청은 성공할 수도 있고 그렇지 않을 수도 있지만 모두 IDP에 대한 사용자 환경 저하 및 워크로드 증가로 인해 모든 사용자의 대기 시간이 증가하고 IDP의 가용성이 줄어듭니다.  이러한 응용 프로그램은 정상적인 사용 범위를 벗어나 작동하며 올바르게 작동하도록 업데이트해야 합니다.

중복 요청을 여러 번 발행하는 `invalid_grant` 클라이언트는 `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`다음과 같은 오류가 전송됩니다.

대부분의 클라이언트는 이 오류를 방지하기 위해 동작을 변경할 필요가 없습니다.  잘못 구성된 클라이언트(토큰 캐싱이 없거나 프롬프트 루프가 이미 표시되지 않은 클라이언트)만 이 오류의 영향을 받게 됩니다.  클라이언트는 다음과 같은 요소에 따라 로컬(쿠키를 통해) 인스턴스별로 추적됩니다.

* 사용자 힌트(있는 경우)

* 요청되는 범위 또는 리소스

* 클라이언트 ID

* 리디렉션 URI

* 응답 유형 및 모드

단기간(5분)에 여러 번 요청(15+)을 하는 `invalid_grant` 앱에는 반복중임을 설명하는 오류가 표시됩니다.  요청되는 토큰의 수명이 충분히 깁니다(최소 10분, 기본적으로 60분) 이 기간 동안 반복되는 요청은 필요하지 않습니다.

모든 앱은 `invalid_grant` 토큰을 자동으로 요청하는 대신 대화형 프롬프트를 표시하여 처리해야 합니다.  이 오류를 방지하려면 클라이언트가 받는 토큰을 올바르게 캐싱하는지 확인해야 합니다.


## <a name="october-2018"></a>2018년 10월

### <a name="authorization-codes-can-no-longer-be-reused"></a>권한 부여 코드는 더 이상 재사용할 수 없습니다.

**개시 날짜**: 2018년 11월 15일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: [코드 흐름](v2-oauth2-auth-code-flow.md)

2018년 11월 15일부터 Azure AD는 앱에서 이전에 사용된 인증 코드를 더 이상 수락하지 않습니다. 이 보안 변경은 Azure AD를 OAuth 사양에 맞추는 데 도움이 되며 v1 및 v2 엔드포인트 모두에 적용됩니다.

앱에서 여러 리소스에 대한 토큰을 얻기 위해 인증 코드를 재사용하는 경우에는 코드를 사용하여 새로 고침 토큰을 얻은 다음, 이 새로 고침 토큰을 사용하여 다른 리소스에 대한 추가 토큰을 얻는 것이 좋습니다. 인증 코드는 한 번만 사용할 수 있지만 새로 고침 토큰은 여러 리소스에서 여러 번 사용할 수 있습니다. OAuth 코드 흐름 중에 인증 코드를 다시 사용하려고 하는 새 앱에서는 invalid_grant 오류가 발생합니다.

새로 고침 토큰에 대한 자세한 내용은 [액세스 토큰 새로 고침](v2-oauth2-auth-code-flow.md#refresh-the-access-token)을 참조하세요.  ADAL 또는 MSAL을 사용하는 경우 이 작업은 라이브러리를 통해 처리됩니다. 'AcquireTokenByAuthorizationCodeAsync'의 두 번째 인스턴스를 'AcquireTokenSilentAsync'로 바꿉니다.

## <a name="may-2018"></a>2018년 5월

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID 토큰은 OBO 흐름에 사용할 수 없습니다.

**날짜**: 2018년 5월 1일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받은 프로토콜**: 암시적 흐름 및 [흐름을 대신합니다.](v2-oauth2-on-behalf-of-flow.md)

2018년 5월 1일 이 OBO 흐름에서 새 애플리케이션의 어설션으로 id_token을 사용할 수 없습니다. API를 보호하려면 클라이언트와 동일한 애플리케이션의 중간 계층 사이에서도 액세스 토큰을 대신 사용해야 합니다. 2018년 5월 1일 전에 등록된 앱은 계속 작동하고 id_token을 액세스 토큰으로 교환할 수 있지만 이 패턴은 모범 사례로 간주되지 않습니다.

이 변경 문제를 해결하려면 다음을 수행할 수 있습니다.

1. 하나 이상의 범위로 응용 프로그램에 대한 웹 API를 만듭니다. 이 명시적 진입점으로 인해 더 세분화된 제어 및 보안이 가능합니다.
1. 앱 매니페스트, [Azure Portal](https://portal.azure.com) 또는 [앱 등록 포털](https://apps.dev.microsoft.com)에서 앱이 암시적 흐름을 통해 액세스 토큰을 발급할 수 있는지 확인합니다. 이 작업은 `oauth2AllowImplicitFlow` 키를 통해 제어됩니다.
1. 클라이언트 응용 프로그램이 통해 `response_type=id_token`id_token 요청하는 경우 위에`response_type=token`생성된 웹 API에 대한 액세스 토큰 () 도 요청합니다. 따라서 v2.0 엔드포인트를 사용하는 경우 `scope` 매개 변수는 `api://GUID/SCOPE`와 비슷하게 표시되어야 합니다. V1.0 엔드포인트에서 `resource` 매개 변수는 Web API의 앱 URI여야 합니다.
1. Id_token 대신 중간 계층으로 이 액세스 토큰을 전달합니다.
