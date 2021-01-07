---
title: Azure AD B2C를 사용 하는 개발자 모범 사례를 통한 복원 력 Microsoft Docs
description: Azure AD B2C를 사용 하는 고객 Id 및 액세스 관리의 개발자 모범 사례를 통한 복원 력
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecde474abf3c814b7c3afa4ae18d044868785cf5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920063"
---
# <a name="resilience-through-developer-best-practices"></a>개발자 모범 사례를 통한 복원 력

이 문서에서는 microsoft의 경험을 기반으로 하는 일부 학습을 대기업에서 공유 합니다. 서비스의 디자인 및 구현에서 이러한 권장 사항을 고려할 수 있습니다.

![개발자 환경 구성 요소를 보여 주는 이미지](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>MSAL (Microsoft 인증 라이브러리) 사용

[MSAL (Microsoft 인증 라이브러리](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) ) 및 [microsoft identity web Authentication library for ASP.NET](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries) 는 응용 프로그램에 필요한 토큰의 획득, 관리, 캐싱 및 새로 고침을 간소화 합니다. 이러한 라이브러리는 응용 프로그램 복원 력을 향상 시키는 기능을 포함 하 여 Microsoft Id를 지원 하도록 특별히 최적화 되어 있습니다.

개발자는 MSAL의 최신 릴리스를 채택 하 고 최신 상태를 유지 해야 합니다. 응용 프로그램에서 [인증 및 권한 부여의 복원 력을 높이는 방법을](resilience-app-development-overview.md) 참조 하세요. 가능 하면 사용자 고유의 인증 스택을 구현 하지 말고 잘 구성 된 라이브러리를 사용 하십시오.

## <a name="optimize-directory-reads-and-writes"></a>디렉터리 읽기 및 쓰기 최적화

Microsoft Azure AD B2C directory 서비스는 하루에 수십억 개의 인증을 지원 합니다. 초당 읽기 비율이 높은 것으로 설계 되었습니다. 쓰기를 최적화 하 여 종속성을 최소화 하 고 복원 력을 늘립니다.

### <a name="how-to-optimize-directory-reads-and-writes"></a>디렉터리 읽기 및 쓰기를 최적화 하는 방법

- **로그인 시 디렉터리에 함수를 쓰지 마세요**. 사용자 지정 정책에서 사전 조건 (if 절)을 사용 하지 않고 로그인 시 쓰기를 실행 하지 마십시오. 로그인에 대 한 쓰기를 필요로 하는 한 가지 사용 사례는 [사용자 암호의 just-in-time 마이그레이션](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration)입니다. 모든 로그인에 대 한 쓰기를 필요로 하는 시나리오를 방지 합니다.

  - 사용자 경험에서 [사전 조건은](https://docs.microsoft.com/azure/active-directory-b2c/userjourneys) 다음과 같이 표시 됩니다.

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - [CAPTCHA 시스템과 통합 하 여](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration)봇 기반 등록에 저항을 빌드 하세요.

  - [부하 테스트 샘플](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing) 을 사용 하 여 등록 및 로그인을 시뮬레이션할 수 있습니다. 

- **제한 이해**: 디렉터리는 응용 프로그램 및 테 넌 트 수준 제한 규칙을 모두 구현 합니다. 읽기/받기, 쓰기/게시, 업데이트/배치 및 삭제/삭제 작업에 대 한 추가 요금 제한이 있으며 각 작업에는 다른 제한이 적용 됩니다.

  - 로그인 시 쓰기는 새 사용자에 대 한 게시물에 속하거나 기존 사용자를 위해 배치 됩니다.

  - 모든 로그인에서 사용자를 만들거나 업데이트 하는 사용자 지정 정책은 잠재적으로 응용 프로그램 수준 올리기 또는 사후 요금 제한에 도달할 수 있습니다. Azure AD 또는 Microsoft Graph를 통해 디렉터리 개체를 업데이트 하는 경우에도 동일한 제한이 적용 됩니다. 마찬가지로 읽기를 검토 하 여 모든 로그인에 대 한 읽기 수를 최소로 유지 합니다.

  - 최대 부하를 예상 하 여 디렉터리 쓰기 율을 예측 하 고 제한을 방지 합니다. 최고 트래픽 예상치에는 등록, 로그인 및 MFA (Multi-factor authentication)와 같은 작업에 대 한 예측이 포함 됩니다. Azure AD B2C 시스템과 응용 프로그램 모두에서 최대 트래픽을 테스트 해야 합니다. 다운스트림 응용 프로그램이 나 서비스가 불가능 한 경우 제한 없이 부하를 처리할 수 Azure AD B2C 수 있습니다.

  - 마이그레이션 타임 라인을 이해 하 고 계획 합니다. Microsoft Graph를 사용 하 여 Azure AD B2C 사용자를 마이그레이션하도록 계획할 때 응용 프로그램 및 테 넌 트 제한을 사용 하 여 사용자의 마이그레이션을 완료 하는 데 필요한 시간을 계산 하는 것이 좋습니다. 두 개의 응용 프로그램을 사용 하 여 사용자 생성 작업 또는 스크립트를 분할 하는 경우 응용 프로그램 당 한도를 사용할 수 있습니다. 이는 테 넌 트 당 임계값 미만으로 유지 되어야 합니다.

  - 다른 응용 프로그램에 대 한 마이그레이션 작업의 영향을 이해 합니다. 다른 신뢰 응용 프로그램에서 제공 하는 라이브 트래픽이 테 넌 트 수준에서 제한을 발생 시 키 지 않고 라이브 응용 프로그램에 대 한 리소스 부족을 고려 합니다. 자세한 내용은 [Microsoft Graph 제한 지침](https://docs.microsoft.com/graph/throttling)을 참조 하세요.
  
## <a name="extend-token-lifetimes"></a>토큰 수명 연장

드문 경우 지만 Azure AD B2C authentication 서비스에서 새 등록 및 로그인을 완료할 수 없는 경우에도 로그인 한 사용자에 대 한 완화를 제공할 수 있습니다. [구성을](https://docs.microsoft.com/azure/active-directory-b2c/configure-tokens)사용 하 여 이미 로그인 한 사용자가 응용 프로그램에서 로그 아웃 하거나 비활성으로 인해 [세션](https://docs.microsoft.com/azure/active-directory-b2c/session-behavior) 시간이 초과 될 때까지 계속 해 서 응용 프로그램을 사용 하도록 허용할 수 있습니다.

비즈니스 요구 사항 및 원하는 최종 사용자 환경에 따라 웹 및 SPAs (단일 페이지 응용 프로그램)에 대 한 토큰 새로 고침 빈도가 결정 됩니다.

### <a name="how-to-extend-token-lifetimes"></a>토큰 수명을 확장 하는 방법

- **웹 응용 프로그램**: 로그인 시작 시 인증 토큰의 유효성을 검사 하는 웹 응용 프로그램의 경우 세션 쿠키에 따라 응용 프로그램이 세션 유효성을 계속 확장 합니다.

  - 사용자 활동을 기반으로 세션을 계속 갱신 하는 롤링 세션 시간을 구현 하 여 사용자가 로그인 상태를 유지할 수 있도록 합니다. 장기 토큰 발급 중단이 발생 한 경우 이러한 세션 시간은 응용 프로그램에서 onetime 구성으로 추가로 증가할 수 있습니다. 세션의 수명을 허용 된 최대값으로 유지 합니다.

- **Spas**: SPA는 api에 대 한 호출을 수행 하기 위해 액세스 토큰에 따라 달라질 수 있습니다. 일반적으로 SPA는 새로 고침 토큰을 생성 하지 않는 암시적 흐름을 사용 합니다. SPA는 숨겨진 iframe을 사용 하 여 인증 Azure AD B2C 끝점에 대 한 새 토큰 요청을 수행할 수 있습니다. SPAs의 경우 사용자가 응용 프로그램을 계속 사용할 수 있도록 하는 몇 가지 옵션이 있습니다.

  - 비즈니스 요구 사항에 맞게 액세스 토큰의 유효 기간을 연장 합니다.

  - API 게이트웨이를 인증 프록시로 사용 하는 응용 프로그램을 빌드합니다. 이 구성에서는 인증 없이 SPA가 로드 되 고 api를 호출 하는 api가 만들어집니다. API 게이트웨이는 정책에 따라 [권한 부여 코드](https://oauth.net/2/grant-types/authorization-code/) 를 사용 하 여 로그인 프로세스를 통해 사용자를 보내고 사용자를 인증 합니다. 그런 다음 API 게이트웨이와 클라이언트 간의 인증 세션이 인증 쿠키를 사용 하 여 유지 관리 됩니다. Api는 api 게이트웨이에서 가져온 토큰 또는 인증서, 클라이언트 자격 증명, API 키 등의 다른 직접 인증 방법을 사용 하 여 API 게이트웨이에서 서비스 됩니다.

  - , PKCE (코드 교환) 및 CORS (크로스-원본 자원 공유) 지원에 대 한 [암시적 권한 부여에서](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) [인증 코드 부여 흐름](https://docs.microsoft.com/azure/active-directory-b2c/implicit-flow-single-page-application) 으로 SPA를 마이그레이션합니다. 응용 프로그램을 MSAL.js 1.x에서 MSAL.js 2.x로 마이그레이션하여 웹 응용 프로그램의 복원 력을 실현할 수 있습니다.

  - 모바일 응용 프로그램의 경우 새로 고침 및 액세스 토큰 수명을 모두 확장 하는 것이 좋습니다.

- **백엔드 또는 마이크로 서비스 응용 프로그램**: 백 엔드 (디먼) 응용 프로그램은 비 대화형이 고 사용자 컨텍스트에 있지 않기 때문에 토큰 도난의 잠재이 크게 저하 됩니다. 보안과 수명 간에 균형을 맞추도록 하 고 긴 토큰 수명을 설정 하는 것이 좋습니다.

## <a name="configure-single-sign-on"></a>Single sign-on 구성

[Sso (single sign-on)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 사용 하면 사용자는 단일 계정으로 한 번 로그인 하 여 여러 응용 프로그램에 액세스할 수 있습니다. 응용 프로그램은 플랫폼 또는 도메인 이름에 관계 없이 웹, 모바일 또는 SPA (단일 페이지 응용 프로그램) 일 수 있습니다. 사용자가 응용 프로그램에 처음으로 로그인 하면 Azure AD B2C [쿠키 기반 세션](https://docs.microsoft.com/azure/active-directory-b2c/session-overview)을 유지 합니다.

후속 인증 요청 시 Azure AD B2C은 쿠키 기반 세션을 읽고 유효성을 검사 하 고 사용자에 게 다시 로그인 하 라는 메시지를 표시 하지 않고 액세스 토큰을 발급 합니다. 정책이 나 응용 프로그램에서 제한 된 범위로 SSO를 구성 하는 경우 나중에 다른 정책 및 응용 프로그램에 액세스 하려면 새 인증을 수행 해야 합니다.

### <a name="how-to-configure-sso"></a>SSO를 구성 하는 방법

여러 응용 프로그램 및 테 넌 트의 사용자 흐름이 동일한 사용자 세션을 공유할 수 있도록 SSO를 테 넌 트 전체 (기본값)로 [구성](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start) 합니다. 테 넌 트 전체 구성은 새 인증에 대 한 대부분의 복원 력을 제공 합니다.  

## <a name="safe-deployment-practices"></a>안전한 배포 사례

가장 일반적인 서비스 disrupters 코드 및 구성을 변경 하는 것입니다. 지속적인 통합 및 지속적인 업데이트 (CICD) 프로세스와 도구를 채택 하면 대규모로 신속 하 게 배포 하 고 테스트 및 배포 중에 사용자의 오류를 줄일 수 있습니다. 오류 감소, 효율성 및 일관성을 위해 CICD을 채택 합니다. [AZURE PIPELINES](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) CICD의 예입니다.

## <a name="web-application-firewall"></a>웹 애플리케이션 방화벽

[OWASP Top 10](https://owasp.org/www-project-top-ten/)에 설명 된 대로 DDoS (분산 서비스 거부) 공격, SQL 삽입, 사이트 간 스크립팅, 원격 코드 실행 등의 알려진 취약성 으로부터 응용 프로그램을 보호할 수 있습니다. WAF (웹 응용 프로그램 방화벽) 배포는 일반적인 악용 및 취약성 으로부터 방어할 수 있습니다.

- 공격 으로부터 중앙 집중화 된 보호를 제공 하는 Azure [Waf](https://docs.microsoft.com/azure/web-application-firewall/overview)를 사용 합니다.

- Azure AD B2C 사용 하는 경우 Azure AD [Id 보호 및 조건부 액세스와 함께 WAF를 사용 하 여 다중 계층 보호 기능을 제공](https://docs.microsoft.com/azure/active-directory-b2c/conditional-access-identity-protection-overview) 합니다.  

## <a name="secrets-rotation"></a>비밀 회전

Azure AD B2C는 응용 프로그램, Api, 정책 및 암호화에 대 한 암호를 사용 합니다. 비밀 보안 인증, 외부 상호 작용 및 저장소입니다. NIST (표준 협회)는 특정 키가 cryptoperiod 인 합법적인 엔터티에서 사용 하도록 허가 된 시간 범위를 호출 합니다. 비즈니스 요구에 맞게 올바른 [길이를 선택](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) 합니다. 개발자는 만료를 사전에 수동으로 설정 하 고 암호를 회전 해야 합니다.

### <a name="how-to-implement-secret-rotation"></a>비밀 회전을 구현 하는 방법

- 지원 되는 리소스에 대해 [관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증 합니다. 관리 id를 사용 하는 경우 자격 증명의 순환을 포함 하 여 리소스를 자동으로 관리할 수 있습니다.

- Azure AD B2C에서 구성 된 모든 [키 및 인증서](https://docs.microsoft.com/azure/active-directory-b2c/policy-keys-overview) 의 인벤토리를 가져옵니다. 이 목록에는 사용자 지정 정책, [api](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api), 서명 ID 토큰 및 SAML 인증서에 사용 되는 키가 포함 될 수 있습니다.

- CICD를 사용 하 여 예상 되는 피크 계절 으로부터 2 개월 이내에 만료 될 비밀을 회전 합니다. 인증서에 연결 된 개인 키의 권장 되는 최대 cryptoperiod는 1 년입니다.

- 암호 및 인증서와 같은 API 액세스 자격 증명을 사전에 모니터링 하 고 회전 합니다.

## <a name="test-rest-apis"></a>REST Api 테스트

복원 력 컨텍스트에서 REST Api 테스트에는 HTTP 코드, 응답 페이로드, 헤더 및 성능 확인을 포함 해야 합니다. 테스트에는 행복 한 경로 테스트만 포함 되지 않아야 하 고 API가 문제 시나리오를 정상적으로 처리 하는지 여부도 확인 합니다.

### <a name="how-to-test-apis"></a>Api를 테스트 하는 방법

[포괄적인 API 테스트](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing)를 포함 하는 테스트 계획을 권장 합니다. 프로 모션 또는 휴일 트래픽으로 인해 예정 된 서 수를 계획 하는 경우 새로운 추정치를 사용 하 여 부하 테스트를 수정 해야 합니다. 프로덕션이 아닌 개발자 환경에서 Api 및 Content Delivery Network (CDN)의 부하 테스트를 수행 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 개발자를 위한 복원 력 리소스](resilience-b2c.md)
  - [복원 력 최종 사용자 환경](resilient-end-user-experience.md)
  - [외부 프로세스를 사용 하는 복원 인터페이스](resilient-external-processes.md)
  - [모니터링 및 분석을 통한 복원 력](resilience-with-monitoring-alerting.md)
- [인증 인프라의 빌드 복원 력](resilience-in-infrastructure.md)
- [응용 프로그램에서 인증 및 권한 부여의 복원 력 향상](resilience-app-development-overview.md)
