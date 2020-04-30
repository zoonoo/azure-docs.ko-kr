---
title: 사용자 지정 정책에 대 한 개발자 정보
titleSuffix: Azure AD B2C
description: 사용자 지정 정책으로 Azure AD B2C를 구성 및 유지 관리하는 개발자를 위한 정보
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 471c2d45e28e634adde78c2d96d407ec219be68c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229666"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 지정 정책에 대 한 개발자 정보

Azure Active Directory B2C의 사용자 지정 정책 구성이 이제 일반 공급 됩니다. 이 구성 방법은 복잡 한 id 솔루션을 구축 하는 고급 id 개발자를 대상으로 합니다. 사용자 지정 정책은 Azure AD B2C 테 넌 트에서 사용할 수 있는 Id 경험 프레임 워크의 강력한 기능을 만듭니다.
사용자 지정 정책을 사용 하는 고급 id 개발자는 연습을 완료 하 고 참조 문서를 읽는 데 약간의 시간을 투자할 계획입니다.

현재 사용할 수 있는 대부분의 사용자 지정 정책 옵션은 일반적으로 사용할 수 있지만 소프트웨어 수명 주기의 다른 단계에 있는 기술 프로필 유형 및 콘텐츠 정의 Api와 같은 기본 기능이 있습니다. 더 많은 정보를 제공 합니다. 아래 표에서는 가용성 수준을 보다 세분화 된 수준으로 지정 합니다.

## <a name="features-that-are-generally-available"></a>일반적으로 사용할 수 있는 기능

- 사용자 지정 정책을 사용하여 사용자 지정 인증 사용자 경험을 작성 및 업로드합니다.
    - 클레임 공급자 간의 교환으로 사용자 경험을 단계별로 설명합니다.
    - 사용자 경험에서 조건부 분기를 정의합니다.
- 사용자 지정 인증 사용자 경험에서 REST API 사용 서비스와 상호 운용할 수 있습니다.
- OpenIDConnect 프로토콜을 준수 하는 id 공급자와 페더레이션 합니다.
- SAML 2.0 프로토콜을 준수 하는 id 공급자와 페더레이션 합니다.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>사용자 지정 정책 기능 집합 개발자의 책임

수동 정책 구성은 Azure AD B2C의 기본 플랫폼에 대해 낮은 수준의 액세스 권한을 부여 하 고 고유한 신뢰 프레임 워크를 만듭니다. 사용자 지정 id 공급자의 가능한 여러 순열, 트러스트 관계, 외부 서비스와의 통합 및 단계별 워크플로를 사용 하려면 설계 및 구성에 대 한 체계적인 접근 방식이 필요 합니다.

사용자 지정 정책 기능 집합을 사용 하는 개발자는 다음 지침을 따라야 합니다.

- 사용자 지정 정책 및 키/비밀 관리의 구성 언어에 대해 잘 알고 있어야 합니다. 자세한 내용은 [TrustFrameworkPolicy](trustframeworkpolicy.md)를 참조 하세요.
- 시나리오 및 사용자 지정 통합을 직접 소유합니다. 작업을 문서화 하 고 라이브 사이트 조직에 알립니다.
- 체계적인 시나리오 테스트를 수행합니다.
- 최소 1개의 개발/테스트 환경과 1개의 프로덕션 환경을 구축하여 소프트웨어 개발 및 스테이징 모범 사례를 준수합니다.
- 사용자와 통합된 ID 공급자 및 서비스에 대한 새로운 개발 정보를 바로 입수합니다. 예를 들어 기밀 변경 내용과 예정되었거나 갑작스럽게 진행되는 서비스 변경 내용을 추적합니다.
- 활성 모니터링을 설정하고 프로덕션 환경의 응답성을 모니터링합니다. Application Insights와 통합 하는 방법에 대 한 자세한 내용은 [Azure Active Directory B2C: 로그 수집](analytics-with-application-insights.md)을 참조 하세요.
- Azure 구독에서 연락처 전자 메일 주소를 최신 상태로 유지하고 Microsoft 라이브 사이트 팀 전자 메일에 즉시 응답합니다.
- Microsoft 라이브 사이트 팀에서 권고할 때 시기 적절하게 조치를 취합니다.

## <a name="terms-for-features-in-public-preview"></a>공개 미리 보기의 기능에 대 한 용어

- 평가 목적 으로만 공개 미리 보기 기능을 사용 하는 것이 좋습니다.
- Sla (서비스 수준 계약)는 공개 미리 보기 기능에 적용 되지 않습니다.
- 공개 미리 보기 기능에 대 한 지원 요청은 일반 지원 채널을 통해 정리할 수 있습니다.

## <a name="features-by-stage-and-known-issues"></a>단계 및 알려진 문제별 기능

사용자 지정 정책/i d 경험 프레임 워크 기능은 일정 하 고 신속 하 게 개발할 수 있습니다. 다음 표는 기능 및 구성 요소 가용성의 인덱스입니다.


### <a name="protocols-and-authorization-flows"></a>프로토콜 및 권한 부여 흐름

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2 인증 코드](authorization-code-flow.md) |  |  | X |  |
| PKCE를 사용 하 여 OAuth2 권한 부여 코드 |  |  | X | 모바일 응용 프로그램에만 해당  |
| [OAuth2 암시적 흐름](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2 리소스 소유자 암호 자격 증명](ropc-custom.md) |  | X |  |  |
| [OIDC Connect](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |X  |  | 사후 및 리디렉션 바인딩입니다. |
| OAuth1 |  |  |  | 지원 안 됨 |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>공급자 페더레이션 식별 

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | 예: Google +.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | 예: Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | 예: Twitter. |
| [SAML2](saml-identity-provider-technical-profile.md) |  |   | X | 예: Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST API 통합

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [기본 인증을 사용 하는 REST API](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [클라이언트 인증서 인증을 사용 하는 REST API](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [OAuth2 전달자 인증을 사용 하는 REST API](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>구성 요소 지원

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [전화 요소 인증](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure MFA 인증](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [일회용 암호](one-time-password-technical-profile.md) |  | X |  |  |
| 로컬 디렉터리로 [Azure Active Directory](active-directory-technical-profile.md) |  |  | X |  |
| 전자 메일 확인을 위한 Azure 전자 메일 하위 시스템 |  |  | X |  |
| [타사 전자 메일 서비스 공급자](custom-email.md) |  |X  |  |  |
| [다중 언어 지원](localization.md)|  |  | X |  |
| [조건자 유효성 검사](predicates.md) |  |  | X | 예를 들어 암호 복잡성이 있습니다. |
| [컨트롤 표시](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>페이지 레이아웃 버전

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [JavaScript 지원](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF 통합

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| 쿼리 문자열 매개 변수`domain_hint` |  |  | X | 클레임으로 사용할 수 있으며 IDP에 전달 될 수 있습니다. |
| 쿼리 문자열 매개 변수`login_hint` |  |  | X | 클레임으로 사용할 수 있으며 IDP에 전달 될 수 있습니다. |
| 를 통해 사용자 경험에 JSON 삽입`client_assertion` | X |  |  | 는 사용 되지 않습니다. |
| 사용자 경험에 JSON 삽입`id_token_hint` |  | X |  | JSON을 전달 하는 데 앞으로 접근 합니다. |
| [응용 프로그램에 id 공급자 토큰 전달](idp-pass-through-custom.md) |  | X |  | 예를 들어 Facebook에서 앱으로 |

### <a name="session-management"></a>세션 관리

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [기본 SSO 세션 공급자](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [외부 로그인 세션 공급자](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO 세션 공급자](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [Single Sign-Out](session-overview.md#sign-out)  |  | X |  |  |

### <a name="security"></a>보안

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| 정책 키 - 생성, 수동, 업로드 |  |  | X |  |
| 정책 키 - RSA/Cert, 비밀 |  |  | X |  |


### <a name="developer-interface"></a>개발자 인터페이스

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| 정책 업로드 |  |  | X |  |
| [사용자 경험 로그 Application Insights](troubleshoot-with-application-insights.md) |  | X |  | 개발 하는 동안 문제를 해결 하는 데 사용 됩니다.  |
| [이벤트 로그 Application Insights](application-insights-technical-profile.md) |  | X |  | 프로덕션 환경에서 사용자 흐름을 모니터링 하는 데 사용 됩니다. |


## <a name="next-steps"></a>다음 단계

- Azure AD B2C에서 [사용할 수 있는 Microsoft Graph 작업](microsoft-graph-operations.md) 을 확인 하세요.
- 사용자 [지정 정책 및 사용자 흐름과의 차이점](custom-policy-overview.md)에 대해 자세히 알아보세요.
