---
title: 사용자 지정 정책에 대한 개발자 참고 사항
titleSuffix: Azure AD B2C
description: 사용자 지정 정책으로 Azure AD B2C를 구성 및 유지 관리하는 개발자를 위한 정보
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403ca480bcf0743d81e375c122c888db96bbf543
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408710"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 지정 정책에 대한 개발자 참고 사항

Azure Active Directory B2C의 사용자 지정 정책 구성을 일반적으로 사용할 수 있습니다. 이 구성 방법은 복잡한 ID 솔루션을 빌드하는 고급 ID 개발자를 대상으로 합니다. 사용자 지정 정책은 Azure AD B2C 테넌에서 ID 환경 프레임워크의 전원을 사용할 수 있도록 합니다.
사용자 지정 정책을 사용하는 고급 ID 개발자는 워크스루를 완료하고 참조 문서를 읽는 데 시간을 투자할 계획을 세워야 합니다.

사용 가능한 대부분의 사용자 지정 정책 옵션을 사용할 수 있지만 소프트웨어 수명 주기의 여러 단계에 있는 기술 프로필 유형 및 콘텐츠 정의 API와 같은 기본 기능이 있습니다. 더 많은 오고있다. 아래 표는 보다 세부적인 수준에서 가용성 수준을 지정합니다.

## <a name="features-that-are-generally-available"></a>일반적으로 사용할 수 있는 기능

- 사용자 지정 정책을 사용하여 사용자 지정 인증 사용자 경험을 작성 및 업로드합니다.
    - 클레임 공급자 간의 교환으로 사용자 경험을 단계별로 설명합니다.
    - 사용자 경험에서 조건부 분기를 정의합니다.
- 사용자 지정 인증 사용자 여정에서 REST API 지원 서비스와 상호 운용합니다.
- OpenIDConnect 프로토콜을 준수하는 ID 공급자와 페더레이트합니다.
- SAML 2.0 프로토콜을 준수하는 ID 공급자와 페더레이트합니다.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>사용자 지정 정책 기능 집합 개발자의 책임

수동 정책 구성은 Azure AD B2C의 기본 플랫폼에 대한 하위 수준 액세스를 부여하고 고유한 트러스트 프레임워크를 생성합니다. 사용자 지정 ID 공급자, 트러스트 관계, 외부 서비스와의 통합 및 단계별 워크플로의 가능한 많은 순열에는 설계 및 구성에 대한 체계적인 접근 방식이 필요합니다.

사용자 지정 정책 기능 집합을 사용하는 개발자는 다음 지침을 준수해야 합니다.

- 사용자 지정 정책 및 키/보안 암호 관리의 구성 언어에 익숙해집니다. 자세한 내용은 [트러스트프레임워크 정책](trustframeworkpolicy.md).
- 시나리오 및 사용자 지정 통합을 직접 소유합니다. 작업을 문서화하고 라이브 사이트 조직에 알립니다.
- 체계적인 시나리오 테스트를 수행합니다.
- 최소 1개의 개발/테스트 환경과 1개의 프로덕션 환경을 구축하여 소프트웨어 개발 및 스테이징 모범 사례를 준수합니다.
- 사용자와 통합된 ID 공급자 및 서비스에 대한 새로운 개발 정보를 바로 입수합니다. 예를 들어 기밀 변경 내용과 예정되었거나 갑작스럽게 진행되는 서비스 변경 내용을 추적합니다.
- 활성 모니터링을 설정하고 프로덕션 환경의 응답성을 모니터링합니다. 응용 프로그램 인사이트와의 통합에 대한 자세한 내용은 [Azure Active Directory B2C: 로그 수집을](analytics-with-application-insights.md)참조하십시오.
- Azure 구독에서 연락처 전자 메일 주소를 최신 상태로 유지하고 Microsoft 라이브 사이트 팀 전자 메일에 즉시 응답합니다.
- Microsoft 라이브 사이트 팀에서 권고할 때 시기 적절하게 조치를 취합니다.

## <a name="terms-for-features-in-public-preview"></a>공개 미리 보기의 기능에 대한 조건

- 공개 미리 보기 기능은 평가 목적으로만 사용하는 것이 좋습니다.
- 서비스 수준 계약(SLA)은 공개 미리 보기 기능에는 적용되지 않습니다.
- 공개 미리 보기 기능에 대한 지원 요청은 일반 지원 채널을 통해 제출할 수 있습니다.

## <a name="features-by-stage-and-known-issues"></a>단계 및 알려진 문제별 기능

사용자 지정 정책/ID 경험 프레임워크 기능은 지속적으로 빠르게 개발되고 있습니다. 다음 표는 기능 및 구성 요소 가용성의 인덱스입니다.


### <a name="protocols-and-authorization-flows"></a>프로토콜 및 권한 부여 흐름

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2 권한 부여 코드](authorization-code-flow.md) |  |  | X |  |
| PKCE를 가진 OAuth2 권한 부여 코드 |  |  | X | 모바일 애플리케이션만  |
| [OAuth2 암시적 흐름](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2 리소스 소유자 암호 자격 증명](ropc-custom.md) |  | X |  |  |
| [OIDC 커넥트](openid-connect.md) |  |  | X |  |
| [삼L2](connect-with-saml-service-providers.md)  |  |X  |  | POST 및 리디렉션 바인딩. |
| OAuth1 |  |  |  | 지원되지 않습니다. |
| WSFed | X |  |  |  |

### <a name="identify-providers-federation"></a>공급자 페더레이션 식별 

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | 예를 들어, 구글 +.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | 예를 들어, 페이스 북.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | 예를 들어, 트위터. |
| [삼L2](saml-technical-profile.md) |  |   | X | 예를 들어, 세일즈포스, ADFS. |
| WSFed| X |  |  |  |


### <a name="rest-api-integration"></a>REST API 통합

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [기본 인증이 있는 REST API](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [클라이언트 인증서 인증을 가진 REST API](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [OAuth2 베어러 인증과 REST API](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>구성 요소 지원

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [전화 팩터 인증](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure MFA 인증](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [일회용 암호](one-time-password-technical-profile.md) |  | X |  |  |
| [로컬 디렉터리로 Azure Active Directory](active-directory-technical-profile.md) |  |  | X |  |
| 전자 메일 확인을 위한 Azure 전자 메일 하위 시스템 |  |  | X |  |
| [타사 이메일 서비스 제공업체](custom-email.md) |  |X  |  |  |
| [다중 언어 지원](localization.md)|  |  | X |  |
| [조건자 유효성 검사](predicates.md) |  |  | X | 예를 들어 암호의 복잡성이 있습니다. |
| [컨트롤 표시](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>페이지 레이아웃 버전

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [자바 스크립트 지원](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF 통합

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| 쿼리 문자열 매개 변수`domain_hint` |  |  | X | 클레임으로 사용할 수 있으며 IDP로 전달할 수 있습니다. |
| 쿼리 문자열 매개 변수`login_hint` |  |  | X | 클레임으로 사용할 수 있으며 IDP로 전달할 수 있습니다. |
| 를 통해 사용자 여정에 JSON 삽입`client_assertion` | X |  |  | 더 이상 사용되지 않습니다. |
| 사용자 여정에 JSON을 삽입합니다.`id_token_hint` |  | X |  | JSON을 통과하기 위한 전진 접근 방식입니다. |
| [ID 공급자 토큰을 응용 프로그램에 전달](idp-pass-through-custom.md) |  | X |  | 예를 들어, 페이스 북에서 응용 프로그램에. |

### <a name="session-management"></a>세션 관리

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [기본 SSO 세션 공급자](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [외부 로그인 세션 공급자](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO 세션 공급자](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


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
| [애플리케이션 인사이트 사용자 여정 로그](troubleshoot-with-application-insights.md) |  | X |  | 개발 중 문제 해결에 사용됩니다.  |
| [애플리케이션 인사이트 이벤트 로그](application-insights-technical-profile.md) |  | X |  | 프로덕션 환경에서 사용자 흐름을 모니터링하는 데 사용됩니다. |


## <a name="next-steps"></a>다음 단계

- Azure [AD B2C에서 사용할 수 있는 Microsoft 그래프 작업](microsoft-graph-operations.md) 확인
- [사용자 지정 정책 및 사용자 흐름과의 차이점에](custom-policy-overview.md)대해 자세히 알아봅니다.
