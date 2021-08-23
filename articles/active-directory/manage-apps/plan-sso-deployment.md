---
title: Azure Active Directory Single Sign-On 배포 계획
description: 조직에서 SSO를 계획, 배포 및 관리하는 데 유용한 가이드입니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: eacb1d03ce4d7148543948f4bf107d8a99181a0e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536524"
---
# <a name="plan-a-single-sign-on-deployment"></a>Single Sign-On 배포 계획

SSO(Single Sign-On)란 단일 사용자 계정을 사용하여 한 번만 로그인하여 사용자에게 필요한 모든 애플리케이션과 리소스에 액세스하는 것을 의미합니다. SSO를 사용하면 사용자가 두 번째 인증 없이 필요한 모든 애플리케이션에 액세스할 수 있습니다.

## <a name="benefits-of-sso"></a>SSO의 이점

SSO(Single Sign-On)는 사용자가 Azure AD(Azure Active Directory)의 애플리케이션에 로그인할 때 보안 및 편리함을 제공합니다.

대부분의 조직에서는 최종 사용자 생산성을 위해 Microsoft 365, Box, Salesforce와 같은 SaaS(Software as a Service) 애플리케이션에 의존합니다. 지금까지 IT 담당자는 각 SaaS 애플리케이션에서 사용자 계정을 개별적으로 만들고 업데이트해야 하며, 사용자는 각각에 대한 암호를 기억해야 했습니다.

Azure Marketplace에는 미리 통합된 SSO 연결을 사용하는 3000개가 넘는 애플리케이션이 있어서 귀사의 테넌트에 쉽게 통합할 수 있습니다.

## <a name="licensing"></a>라이선싱

- **Azure AD 라이선스** - 미리 통합된 SaaS 애플리케이션용 SSO는 무료입니다. 단, 디렉터리의 개체 수 및 배포하려는 기능에는 추가 라이선스가 필요할 수 있습니다. 라이선스 요구 사항에 대한 전체 목록은 [Azure Active Directory 가격 책정](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)을 참조하세요.
- **애플리케이션 라이선스** - 비즈니스 요구 사항을 충족하려면 SaaS 애플리케이션에 적합한 라이선스가 필요합니다. 애플리케이션 소유자와 협력하여 애플리케이션에 할당된 사용자에게 애플리케이션 내 역할에 적합한 라이선스가 있는지 확인합니다. Azure AD가 역할을 기반으로 자동 프로비저닝을 관리하는 경우에는 Azure AD에 할당된 역할이 애플리케이션 내에서 소유하는 라이선스 수와 일치해야 합니다. 애플리케이션에서 소유한 라이선스가 타당하지 않으면 사용자 프로비저닝/업데이트 중에 오류가 발생할 수 있습니다.

## <a name="plan-your-sso-team"></a>SSO 팀 계획

- **적절한 이해 관계자 참여** - 기술 프로젝트가 실패하는 경우 일반적으로 영향, 결과 및 책임에 대한 기대치가 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여](../fundamentals/active-directory-deployment-plans.md)시키고 관련자가 자신의 역할을 이해하고 있는지 확인해야 합니다.
- **커뮤니케이션 계획** - 커뮤니케이션은 신규 서비스의 성공에 대단히 중요합니다. 사용자 환경이 언제 어떻게 변할 것인지, 문제가 발생하면 어떻게 지원을 받을 수 있는지에 대해에 대해 사용자와 적극적으로 소통해야 합니다. [최종 사용자가 SSO 사용 애플리케이션에 액세스하는 방법](end-user-experiences.md)에 대한 옵션을 검토하고 선택에 맞게 소통해야 합니다.

## <a name="plan-your-sso-protocol"></a>SSO 프로토콜 계획

페더레이션 프로토콜을 기반으로 SSO를 구현하면 보안, 안정성 및 최종 사용자 환경을 개선하고 구현하기가 더 쉽습니다. [사용 가능한 단계별 가이드](../saas-apps/tutorial-list.md)를 통해 많은 애플리케이션이 Azure AD에 미리 통합되어 있습니다. 이러한 가이드는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)에서 찾을 수 있습니다. 각 SSO 방법에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](what-is-single-sign-on.md) 문서에서 찾을 수 있습니다.

사용자가 앱에 Single Sign-On하는 데 사용할 수 있는 주요 방법은 두 가지입니다.

- **페더레이션된 Single Sign-On을 사용하는 경우** Azure AD는 사용자의 Azure AD 계정을 사용하여 사용자를 애플리케이션에 인증합니다. 이 방법은 SAML 2.0, WS-Federation 또는 OpenID Connect와 같은 프로토콜을 지원하는 애플리케이션에 대해 지원되며, 가장 강력한 Single Sign-On 모드입니다. 애플리케이션에서 지원하는 경우 암호 기반 SSO 및 ADFS 대신 Azure AD에서 페더레이션된 SSO를 사용하는 것이 좋습니다.

- **암호 기반 로그온을 사용하는 경우** 사용자는 애플리케이션에 처음 액세스할 때 사용자 이름 및 암호를 사용하여 애플리케이션에 로그인합니다. 첫 번째 로그온 후에는 Azure AD가 사용자 이름 및 암호를 애플리케이션에 제공합니다. 암호 기반 Single Sign-On을 사용하면 웹 브라우저 확장 또는 모바일 앱을 사용하여 안전하게 애플리케이션 암호를 스토리지하고 재생할 수 있습니다. 이 옵션은 애플리케이션에서 제공하는 기존 로그인 프로세스를 활용하지만, 관리자가 암호를 관리할 수 있으므로 사용자는 암호를 몰라도 됩니다.

### <a name="considerations-for-federation-based-sso"></a>페더레이션 기반 SSO에 대한 고려 사항

- **OpenID Connect 및 OAuth 사용** - 연결하려는 애플리케이션이 지원하는 경우 OIDC/OAuth 2.0 메서드를 사용하여 해당 애플리케이션에 대한 SSO를 사용하도록 설정합니다. 이 방법을 사용하면 필요한 구성은 더 적고 사용자 환경은 더 풍부합니다. 자세한 내용은 [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md), [Azure Active Directory 개발자 가이드](../develop/index.yml)를 참조하세요.
- **SAML 기반 SSO에 대한 엔드포인트 구성** - SAML을 사용하는 경우 애플리케이션을 구성하기 전에 개발자에게 특정 정보가 필요합니다. 자세한 내용은 [SAML 기반 Single Sign-On 구성](configure-saml-single-sign-on.md)을 참조하세요.
- **SAML 기반 SSO를 위한 인증서 관리** - 애플리케이션에 페더레이션 SSO를 사용하도록 설정하면 Azure AD는 기본적으로 3년간 유효한 인증서를 만듭니다. 필요한 경우 해당 인증서의 만료 날짜를 사용자 지정할 수 있습니다. 만료되기 전에 인증서 갱신을 위한 프로세스가 준비되었는지 확인해야 합니다. 자세히 알아보려면 [Azure AD 인증서 관리](./manage-certificates-for-federated-single-sign-on.md)를 참조하세요.

### <a name="considerations-for-password-based-sso"></a>암호 기반 SSO에 대한 고려 사항

암호 기반 SSO에 Azure AD를 사용하려면 자격 증명을 안전하게 검색하고 로그인 양식을 작성하는 브라우저 확장을 배포해야 합니다. [지원 되는 브라우저](../user-help/my-apps-portal-end-user-access.md)를 사용하여 확장을 대규모로 배포하는 메커니즘을 정의합니다. 다음 옵션을 사용할 수 있습니다.

- [Internet Explorer에 대한 그룹 정책](my-apps-deployment-plan.md)
- [Internet Explorer용 Configuration Manager](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Chrome, Firefox, Microsoft Edge 또는 IE에 대한 사용자 중심 다운로드 및 구성](../user-help/my-apps-portal-end-user-access.md)

자세히 알아보려면 [암호 Single Sign-On을 구성하는 방법](./configure-password-single-sign-on-non-gallery-applications.md)을 참조하세요.

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>갤러리에 없는 애플리케이션에 대한 로그인 양식 메타데이터 캡처

Microsoft는 암호 보관(사용자 이름 및 암호 필드 캡처)을 위해 웹 애플리케이션에서 메타데이터 캡처를 지원합니다. 양식 메타데이터를 캡처하기 위해 웹 애플리케이션을 구성하는 과정에서 로그인 URL로 이동합니다. 애플리케이션 소유자에게 정확한 로그인 URL을 요청합니다. 이 정보는 로그온 프로세스 중에 사용되며 로그온하는 동안 Azure AD 자격 증명을 애플리케이션에 매핑합니다.

자세히 알아보려면 [Azure AD를 사용한 애플리케이션 액세스 및 SSO란? – 암호 기반 SSO](./what-is-single-sign-on.md)를 참조하세요.

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>양식의 메타데이터를 다시 캡처해야 하는 경우

애플리케이션의 HTML 레이아웃이 변경되는 경우 변경 내용에 맞춰 조정하기 위해 메타데이터를 다시 캡처해야 할 수도 있습니다. HTML 레이아웃에 변화가 있음을 나타내는 일반적인 증상은 다음과 같습니다.

- 애플리케이션을 클릭하면 로그인 페이지에서 "중단"된다고 사용자가 보고하는 경우
- 사용자 이름 또는 암호가 입력되지 않는다고 사용자가 보고하는 경우

#### <a name="shared-accounts"></a>공유 계정

로그인 관점에서 공유 계정이 있는 애플리케이션은 개별 사용자에 대해 암호 SSO를 사용하는 갤러리 애플리케이션과 다르지 않습니다. 단, 공유 계정을 사용하는 애플리케이션을 계획하고 구성하는 경우 몇 가지 단계가 추가로 필요합니다.

1. 애플리케이션 비즈니스 사용자와 협력하여 다음을 문서화합니다.
   1. 애플리케이션을 사용할 조직의 사용자 집합
   1. 사용자 집합과 연결된 애플리케이션의 기존 자격 증명 집합
1. 사용자 집합과 자격 증명의 각 조합에 대해, 요구 사항에 따라 클라우드 또는 온-프레미스에 보안 그룹을 만듭니다.
1. 공유 자격 증명을 재설정합니다. 앱이 Azure AD에 배포되면 개인은 공유 계정의 암호가 필요하지 않습니다. Azure AD가 암호를 저장하므로 아주 길고 복잡하게 설정하는 것이 좋습니다.
1. 애플리케이션에서 지원하는 경우 암호의 자동 롤오버를 구성합니다. 이렇게 하면 초기 설치를 수행한 관리자도 공유 계정의 암호를 알 수 없습니다.

## <a name="plan-your-authentication-method"></a>인증 방법 계획

따라서 올바른 인증 방법을 선택하는 것은 Azure AD 하이브리드 ID 솔루션 설정에서 매우 중요한 첫 번째 결정입니다. 클라우드에서 사용자를 프로비저닝하기도 하는 Azure AD Connect를 사용하여 구성되는 인증 방법을 구현하세요.

인증 방법을 선택하려면 시간, 기존 인프라, 복잡성 및 선택을 구현하는 데 드는 비용을 고려해야 합니다. 이러한 요소는 조직마다 다르며 시간이 흐름에 따라 변경될 수 있습니다. 특정 시나리오와 가장 일치하는 방법을 선택해야 합니다. 자세한 내용은 [Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택](../hybrid/choose-ad-authn.md)을 참조하세요.

## <a name="plan-your-security-and-governance"></a>보안 및 거버넌스 계획

BYOD 디바이스와 클라우드 애플리케이션의 폭발적 증가로 인해 네트워크 경계의 틈이 증가하고 효율성이 떨어지면서 ID는 보안 주의 및 투자의 새로운 축이 되었습니다.

### <a name="plan-access-reviews"></a>액세스 검토 계획

[액세스 검토](../governance/create-access-review.md)를 통해 조직은 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스, 역할 할당을 효율적으로 관리할 수 있습니다. 적절한 사람만 계속 액세스할 수 있도록 정기적으로 사용자 액세스를 검토하는 계획을 세워야 합니다.

액세스 검토를 설정하는 동안 계획해야 하는 몇 가지 주요 항목은 다음과 같습니다.

1. 비즈니스 요구에 맞는 액세스 검토 주기를 파악합니다. 빈도를 매주, 매월, 매년으로 설정하거나 요청 시 수행할 수 있습니다.

1. 앱 액세스 보고서의 검토자를 나타내는 그룹을 만듭니다. 앱과 해당 대상 사용자 및 사용 사례에 가장 익숙한 관련자가 액세스 검토의 참여자인지 확인해야 합니다.

1. 액세스 검토를 완료하려면 더 이상 액세스가 필요하지 않은 사용자의 앱 액세스 권한을 삭제하는 작업이 포함됩니다. 거부된 사용자의 지원 요청이 있을 경우 처리할 계획을 세웁니다. 삭제된 사용자는 30일 동안 Azure AD에 삭제된 상태로 남아 있고, 이 기간 동안은 필요하면 관리자가 복원할 수 있습니다. 30일 후 해당 사용자가 영구적으로 삭제됩니다. 이 기간이 만료되기 전에 전역 관리자는 Azure Active Directory 포털을 사용하여 최근에 삭제된 사용자를 명시적으로 영구적으로 삭제할 수 있습니다.

### <a name="plan-auditing"></a>감사 계획

Azure AD는 [기술 및 비즈니스 인사이트가 포함된 보고서](../reports-monitoring/overview-reports.md)를 제공합니다.

보안 및 활동 보고서를 모두 사용할 수 있습니다. 보안 보고서에는 위험 플래그가 지정된 사용자 및 위험한 로그인이 표시됩니다. 활동 보고서에는 로그인 활동이 자세히 설명되고 모든 로그인에 대한 감사 내역이 제공되기 때문에 조직에서 사용자의 행동을 파악하는 데 유용합니다. 보고서를 사용하여 위험을 관리하고 생산성을 높이고 규정 준수를 모니터링할 수 있습니다.

| 보고서 종류 | 액세스 검토 | 보안 보고서 | 로그인 보고서 |
|-------------|---------------|------------------|----------------|
| 검토에 사용 | 애플리케이션 사용 권한 및 사용량 | 잠재적으로 손상된 계정 | 애플리케이션에 액세스한 사람 |
| 잠재적 작업 | 액세스 감사; 사용 권한 철회 | 액세스 권한 철회; 강제 보안 초기화 | 액세스 취소 |

Azure AD는 대부분의 감사 데이터를 30일 동안 유지하며, 이러한 데이터는 Azure 관리 포털 또는 API를 통해 분석 시스템에 다운로드할 수 있습니다.

### <a name="consider-using-microsoft-cloud-application-security"></a>Microsoft Cloud Application Security 사용 권장

MCAS(Microsoft Cloud App Security)는 CASB(클라우드 액세스 보안 브로커) 솔루션입니다. 클라우드 앱 및 서비스에 대한 가시성을 제공하고, 사이버 위협을 식별 및 차단하기 위한 정교한 분석 기능을 제공하며, 데이터가 이동하는 방식을 제어할 수 있도록 지원합니다.

MCAS를 배포하면 다음을 수행할 수 있습니다.

- Cloud Discovery를 사용하여 조직에서 사용 중인 클라우드 환경과 클라우드 앱을 매핑하고 식별합니다.
- 클라우드에서 앱 사용 권한 및 비사용 권한을 적용합니다.
- 연결하는 앱의 가시성과 거버넌스를 위해 공급자 API를 활용하는 배포하기 쉬운 앱 커넥터를 사용합니다.
- 조건부 액세스 앱 제어 보호를 사용하여 클라우드 앱 내에서 액세스와 활동에 대한 실시간 가시성과 제어를 확보합니다.
- 정책을 설정하고 지속적으로 자세히 튜닝하여 지속적으로 제어하도록 지원합니다.

MCAS(Microsoft Cloud Application Security) 세션 컨트롤은 모든 운영 체제의 모든 주요 플랫폼에서 어느 브라우저에서나 사용할 수 있습니다. 모바일 앱과 데스크톱 앱도 차단되거나 허용될 수 있습니다. 기본적으로 Azure AD와 통합하면Azure AD의 Single Sign-On을 사용하는 Open ID Connect 앱 또는 SAML로 구성된 모든 앱을 지원할 수 있으며, 여기에는 [몇 가지 주요 앱](/cloud-app-security/proxy-intro-aad)이 포함됩니다.

MCAS에 대한 자세한 내용은 [Microsoft Cloud App Security 개요](/cloud-app-security/what-is-cloud-app-security)를 참조하세요. MCAS는 사용자 기반 구독 서비스입니다. 라이선스 세부 정보는 [MCAS 라이선스 세부 정보](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)에서 검토할 수 있습니다.

### <a name="use-conditional-access"></a>조건부 액세스 사용

조건부 액세스를 사용하면 클라우드 앱에 대한 조건 기반 액세스 제어 결정을 자동화할 수 있습니다.

1단계 인증이 완료된 후 조건부 액세스 정책이 적용됩니다. 따라서, 조건부 액세스는 DoS(서비스 거부) 공격과 같은 시나리오에서 최전방 방어선으로 사용하기 위해 개발된 것은 아니지만, 이러한 이벤트의 신호를 활용하여 액세스를 결정할 수 있습니다. 예를 들어 로그인 위험 수준, 요청 위치 등을 사용할 수 있습니다. 조건부 액세스에 대한 자세한 내용은 [개요](../conditional-access/plan-conditional-access.md) 및 [배포 계획](../conditional-access/plan-conditional-access.md)을 참조하세요.

## <a name="azure-sso-technical-requirements"></a>Azure SSO 기술 요구 사항

다음 섹션에서는 특정 애플리케이션을 구성하기 위한 요구 사항(필요한 환경, 엔드포인트, 클레임 매핑, 필수 특성, 인증서, 사용된 프로토콜)을 자세히 설명합니다. [Azure AD 포털](https://portal.azure.com/)에서 SSO를 구성하려면 이 정보가 필요합니다.

### <a name="authentication-mechanism-details"></a>인증 메커니즘 세부 정보

사전 통합된 모든 SaaS 앱에 대해서는 Microsoft가 자습서를 제공하며, 이 정보를 따로 준비할 필요가 없습니다. 애플리케이션이 당사의 애플리케이션 마켓플레이스/갤러리에 없으면 다음 데이터를 수집해야 할 수도 있습니다.

- **애플리케이션이 SSO에 사용하는 현재 ID 공급자(해당하는 경우)** - 예: AD FS, PingFederate, Okta
- **대상 애플리케이션이 지원하는 프로토콜** - 예: SAML 2.0, OpenID Connect, OAuth, 양식 기반 인증, WS-Fed, WS-Trust
- **Azure AD를 사용하여 구성되는 프로토콜**  - 예: SAML 2.0 또는 1.1, OpenID Connect, OAuth, 양식 기반, WS-Fed

### <a name="attribute-requirements"></a>특성 요구 사항

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 사이에는 미리 구성된 특성 및 특성 매핑 세트가 있습니다. 일부 앱은 그룹과 같은 다른 유형의 개체를 관리합니다. Azure AD에서 애플리케이션으로의 사용자 특성 매핑을 계획하고 비즈니스 요구에 따라 [기본 특성 매핑을 사용자 지정](../app-provisioning/customize-application-attributes.md)합니다.

### <a name="certificate-requirements"></a>인증서 요구 사항

애플리케이션의 인증서는 최신 상태여야 합니다. 그렇지 않으면 사용자가 애플리케이션에 액세스하지 못할 위험이 있습니다. 대부분의 SaaS 애플리케이션 인증서는 36개월 동안 유효합니다. 인증서 기간은 애플리케이션 블레이드에서 변경할 수 있습니다. 만료를 문서화하고 인증서 갱신을 관리하는 방법을 알고 있어야 합니다.

인증서를 관리하는 방법은 두 가지입니다.

- **자동 인증서 롤오버** - Microsoft는 [Azure AD에서 서명 키 롤오버](../develop/active-directory-signing-key-rollover.md)를 지원합니다. 이 방식은 당사가 선호하는 방법이지만 이 시나리오를 지원하지 않는 ISV도 있습니다.

- **수동 업데이트** - 애플리케이션마다 정의된 방식에 따라 만료되는 자체 인증서가 있습니다. 애플리케이션의 인증서가 만료되기 전에 새 인증서를 만들어서 ISV에 보냅니다. 이 정보는 페더레이션 메타데이터에서 가져올 수 있습니다. [페더레이션 메타데이터에 대한 자세한 내용은 여기를 읽어보세요.](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>SSO 구현

다음 단계를 사용하여 조직에서 솔루션을 계획하고 배포합니다.

### <a name="user-configuration-for-sso"></a>SSO에 대한 사용자 구성

- **테스트 사용자 식별**

   앱 소유자에게 연락하여 애플리케이션 내에서 테스트 사용자를 3개 이상 만들도록 요청합니다. 기본 식별자로 사용할 정보가 올바르게 채워지고 Azure AD에서 사용할 수 있는 특성과 일치하는지 확인합니다. 대부분의 경우 SAML 기반 애플리케이션의 "NameID"에 매핑됩니다. JWT 토큰의 경우 "preferred_username"입니다.

   Azure AD에서 사용자를 만듭니다. 클라우드 기반 사용자로 수동으로 만들거나 Azure AD Connect 동기화 엔진을 사용하여 온-프레미스에서 사용자를 동기화합니다. 정보가 애플리케이션으로 전송되는 클레임과 일치하는지 확인합니다.

- **SSL 구성**

   [애플리케이션 목록](../saas-apps/tutorial-list.md)에서 애플리케이션에 대한 SSO 자습서를 찾아서 열고 자습서의 단계에 따라 SaaS 애플리케이션을 성공적으로 구성합니다.

   애플리케이션을 찾을 수 없는 경우 [사용자 지정 애플리케이션 설명서](./configure-saml-single-sign-on.md)를 참조하세요. 여기에서는 Azure AD 갤러리에 없는 애플리케이션을 추가하는 방법을 안내합니다.

   필요에 따라 [Microsoft의 지침 설명서](../develop/active-directory-claims-mapping.md)를 사용하여 엔터프라이즈 애플리케이션에 대해 SAML 토큰에서 발급된 클레임을 사용할 수 있습니다. 이것이 애플리케이션에 대한 SAML 응답으로 받아야 하는 것과 매핑되는지 확인합니다. 구성 중에 문제가 발생하면 [SSO 통합 디버그 방법](./debug-saml-sso-issues.md)에 대한 지침을 참조하세요.

### <a name="provide-sso-change-communications-to-end-users"></a>최종 사용자에게 SSO 변경 사항 알리기

커뮤니케이션 계획을 이행합니다. 변경이 예정되어 있으며, 변경이 되면 무엇을 해야 하는지 어떻게 도움을 요청할 수 있는지에 대해 최종 사용자에게 알려 주어야 합니다.

### <a name="verify-end-user-scenarios-for-sso"></a>SSO에 대한 최종 사용자 시나리오 검증

다음 테스트 사례를 사용하여 회사 소유 디바이스 및 개인 디바이스에서 테스트를 수행하여 SSO 구성이 예상대로 작동하는지 확인할 수 있습니다. 아래 시나리오에서는 사용자가 애플리케이션 URL로 이동하고 서비스 공급자가 시작한 인증 흐름(SP가 시작한 인증 흐름)을 거친다고 가정합니다.

| 시나리오 | 사용자가 SP에서 시작한 인증 흐름에 대한 예상 결과 |
|----------|---------------------------------------------------|
| Corpnet에서 IE로 애플리케이션에 로그인합니다. | IWA(Windows 통합 인증)가 추가 프롬프트 없이 발생합니다. |
| corpnet을 벗어나 IE에서 새로 로그인을 시도하여 애플리케이션에 로그인합니다. | AD FS 서버의 양식 기반 프롬프트. 사용자가 로그인에 성공하고 브라우저에 MFA를 요청하는 프롬프트가 표시됩니다. |
| corpnet을 벗어나 현재 세션으로 IE로 애플리케이션에 로그인하고 MFA를 수행한 적이 없습니다. | 첫 번째 요소에 대해 사용자에게 프롬프트가 표시되지 않습니다. 사용자에게 MFA에 대한 프롬프트가 표시됩니다. |
| corpnet을 벗어나 현재 세션으로 IE로 애플리케이션에 로그인하고 이 세션에서 이미 MFA를 수행했습니다. | 첫 번째 요소에 대해 사용자에게 프롬프트가 표시되지 않습니다. 사용자가 MFA를 수신하지 않습니다. 사용자가 애플리케이션에 SSO로 로그인합니다. |
| corpnet을 벗어나 현재 세션으로 Chrome/Firefox/Safari로 애플리케이션에 로그인하고 이 세션에서 이미 MFA를 수행했습니다. | 첫 번째 요소에 대해 사용자에게 프롬프트가 표시되지 않습니다. 사용자가 MFA를 수신하지 않습니다. 사용자가 애플리케이션에 SSO로 로그인합니다. |
| corpnet을 벗어나 Chrome/Firefox/Safari에서 새로 로그인을 시도하여 애플리케이션에 로그인합니다. | AD FS 서버의 양식 기반 프롬프트. 사용자가 로그인에 성공하고 브라우저에 MFA를 요청하는 프롬프트가 표시됩니다. |
| corpnet에서 현재 세션으로 Chrome/Firefox로 애플리케이션에 로그인합니다. | 첫 번째 요소에 대해 사용자에게 프롬프트가 표시되지 않습니다. 사용자가 MFA를 수신하지 않습니다. 사용자가 애플리케이션에 SSO로 로그인합니다. |
| 애플리케이션 모바일 앱에서 새로 로그인을 시도하여 애플리케이션에 로그인합니다. | AD FS 서버의 양식 기반 프롬프트. 사용자가 로그인에 성공하고 ADAL 클라이언트에 MFA를 요청하는 프롬프트가 표시됩니다. |
| 권한 없는 사용자가 로그인 URL을 사용하여 애플리케이션에 로그인을 시도합니다. | AD FS 서버의 양식 기반 프롬프트. 사용자가 첫 번째 요소를 사용하여 로그인하지 못합니다. |
| 권한 있는 사용자가 로그인을 시도하지만 잘못된 암호를 입력합니다. | 사용자가 애플리케이션 URL로 이동하고 잘못된 사용자 이름/암호 오류를 수신합니다. |
| 권한 있는 사용자가 이메일의 링크를 클릭하면 이미 인증되어 있습니다. | 사용자가 URL을 클릭하면 추가 프롬프트 없이 애플리케이션에 로그인됩니다. |
| 권한 있는 사용자가 이메일의 링크를 클릭하면 아직 인증되지 않았습니다. | 사용자가 URL을 클릭하면 첫 번째 요소를 사용하여 인증하라는 메시지가 표시됩니다. |
| 권한 있는 사용자가 애플리케이션 모바일 앱(SP 시작)에서 새로 로그인을 시도하여 애플리케이션에 로그인합니다. | AD FS 서버의 양식 기반 프롬프트. 사용자가 로그인에 성공하고 ADAL 클라이언트에 MFA를 요청하는 프롬프트가 표시됩니다. |
| 권한 없는 사용자가 로그인 URL(SP 시작)을 사용하여 애플리케이션에 로그인을 시도합니다. | AD FS 서버의 양식 기반 프롬프트. 사용자가 첫 번째 요소를 사용하여 로그인하지 못합니다. |
| 권한 있는 사용자가 로그인을 시도하지만 잘못된 암호를 입력합니다.| 사용자가 애플리케이션 URL로 이동하고 잘못된 사용자 이름/암호 오류를 수신합니다. |
| 권한 있는 사용자가 로그아웃했다가 다시 로그인합니다. | 로그아웃 URL이 구성된 경우 모든 서비스에서 사용자가 로그아웃되고 인증하라는 메시지가 표시됩니다. |
| 권한 있는 사용자가 로그아웃했다가 다시 로그인합니다. | 로그아웃 URL이 구성되지 않은 경우 기존 Azure AD 브라우저 세션의 기존 토큰을 사용하여 사용자가 자동으로 다시 로그인됩니다. |
| 권한 있는 사용자가 이메일의 링크를 클릭하면 이미 인증되어 있습니다. | 사용자가 URL을 클릭하면 추가 프롬프트 없이 애플리케이션에 로그인됩니다. |
| 권한 있는 사용자가 이메일의 링크를 클릭하면 아직 인증되지 않았습니다. | 사용자가 URL을 클릭하면 첫 번째 요소를 사용하여 인증하라는 메시지가 표시됩니다. |

## <a name="manage-sso"></a>SSO 관리

이 섹션에서는 SSO를 성공적으로 관리하기 위한 요구 사항 및 권장 사항에 대해 간략하게 설명합니다.

### <a name="required-administrative-roles"></a>필요한 관리 역할

항상 Azure Active Directory 내에서 필요한 작업을 수행하는 데 사용할 수 있는 가장 적은 권한이 있는 역할을 사용합니다. [사용 가능한 다양한 역할을 검토](../roles/permissions-reference.md)하고 애플리케이션의 각 가상 사용자에 대한 요구 사항을 해결하는 데 적합한 역할을 선택하는 것이 좋습니다. 일부 역할은 임시로 적용했다가 배포가 완료된 후 제거해야 할 수도 있습니다.

| 가상 사용자| 역할 | Azure AD 역할(필요한 경우) |
|--------|-------|-----------------------------|
| 지원 센터 관리자 | 계층 1 지원 | 없음 |
| ID 관리자 | 문제가 Azure AD에 영향을 주는 경우 구성 및 디버그 | 글로벌 관리자 |
| 애플리케이션 관리자 | 애플리케이션의 사용자 증명, 권한이 있는 사용자의 구성 | 없음 |
| 인프라 관리자 | 인증서 롤오버 소유자 | 글로벌 관리자 |
| 비즈니스 소유자/관련자 | 애플리케이션의 사용자 증명, 권한이 있는 사용자의 구성 | 없음 |

PIM([Privileged Identity Management](../privileged-identity-management/pim-configure.md))을 사용하여 역할을 관리하고, 디렉터리 권한이 있는 사용자에게 추가 감사, 제어 및 액세스 검토를 제공하는 것이 좋습니다.

### <a name="sso-certificate-lifecycle-management"></a>SSO 인증서 수명 주기 관리

Azure AD와 Single Sign-On으로 구성되는 애플리케이션 간의 서명 인증서 수명 주기 관리를 담당하는 올바른 역할 및 이메일 배포 목록을 식별하는 것이 중요합니다. 권장되는 몇 가지 주요 역할은 다음과 같습니다.

- 애플리케이션에서 사용자 속성 업데이트를 담당하는 소유자
- 애플리케이션 중단/수정 지원 요청에 대기하는 소유자
- 인증서 관련 변경 알림을 위해 면밀히 모니터링되는 이메일 배포 목록

인증서의 최대 수명은 3년입니다. Azure AD와 애플리케이션 간의 인증서 변경을 처리하는 방식에 대한 프로세스를 설정하는 것이 좋습니다. 그러면 인증서 만료 또는 인증서 롤오버 적용으로 인한 중단을 방지하거나 최소화할 수 있습니다.

### <a name="rollback-process"></a>롤백 프로세스

테스트 사례를 기반으로 테스트를 완료한 후에는 애플리케이션을 사용하여 프로덕션으로 이동할 수 있습니다. 프로덕션으로 이동한다는 것은 계획하고 테스트한 구성을 프로덕션 테넌트에서 구현하고 사용자에게 배포하는 것을 의미합니다. 단, 배포가 계획대로 진행되지 않을 경우 수행할 작업을 계획하는 것이 중요합니다. 배포 중에 SSO 구성이 실패하면 중단을 완화하고 사용자에게 미치는 영향을 줄이는 방법을 파악해야 합니다.

애플리케이션 내 인증 방법의 가용성에 따라 최상의 전략이 결정됩니다. 배포에 문제가 발생하는 경우 앱 소유자를 위해 원래 로그인 구성 상태로 돌아가는 정확한 방법에 대한 문서가 있는지 항상 확인해야 합니다.

- **앱이 여러 ID 공급자**(예: LDAP 및 AD FS 및 Ping)를 지원하는 경우 롤아웃 중에 기존 SSO 구성을 삭제하지 마십시오. 대신 나중에 다시 전환해야 하는 경우를 대비하여 마이그레이션 중에 사용하지 않도록 설정합니다.

- **앱이 여러 IDP를 지원하지 않지만** 사용자가 양식 기반 인증(사용자 이름/암호)을 사용하여 로그인할 수 있는 경우 새 SSO 구성 롤아웃이 실패하면 사용자가 기존 방식으로 대체할 수 있는지 확인해야 합니다.

### <a name="access-management"></a>액세스 관리

리소스에 대한 액세스를 관리할 때는 확장된 접근 방식을 선택하는 것이 좋습니다. 일반적인 방식에는 Azure AD Connect를 통해 동기화하여 온-프레미스 그룹 활용, [사용자 특성을 기반으로 Azure AD에서 동적 그룹 만들기](../enterprise-users/groups-dynamic-membership.md) 또는 리소스 소유자가 관리하는 Azure AD에서 [셀프 서비스 그룹](../enterprise-users/groups-self-service-management.md) 만들기가 포함됩니다.

### <a name="monitor-security"></a>보안 모니터링

SaaS 앱 보안의 다양한 측면을 검토하고 필요한 수정 조치를 수행하는 정기적인 주기를 설정하는 것이 좋습니다.

### <a name="troubleshooting"></a>문제 해결

다음 링크는 문제 해결 시나리오를 제공합니다. 지원 담당자를 위해, 이러한 시나리오와 시나리오의 문제를 해결하는 단계를 통합한 구체적인 가이드를 만들 수도 있습니다.

#### <a name="consent-issues"></a>동의 이슈

- [예기치 않은 동의 오류](./application-sign-in-unexpected-user-consent-prompt.md)

- [사용자 동의 오류](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>로그인 문제

- [사용자 지정 포털에서 로그인하는 문제](./application-sign-in-other-problem-access-panel.md)

- [내 앱에서 로그인하는 문제](./application-sign-in-other-problem-access-panel.md)

- [애플리케이션 로그인 페이지의 오류](./application-sign-in-problem-application-error.md)

- [Microsoft 애플리케이션에 로그인하는 문제](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications"></a>애플리케이션의 SSO 문제

- [애플리케이션의 암호 SSO 문제](./troubleshoot-password-based-sso.md)

- [SAML 기반 Single Sign-On이 구성된 앱에 로그인하는 것과 관련된 문제](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)

## <a name="next-steps"></a>다음 단계

[SAML 기반 SSO 디버그](./debug-saml-sso-issues.md)

[PowerShell을 통한 앱에 대한 클레임 매핑](../develop/active-directory-claims-mapping.md)

[SAML 토큰에 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)

[Single Sign-On SAML 프로토콜](../develop/single-sign-on-saml-protocol.md)

[Single Sign-Out SAML 프로토콜](../develop/single-sign-out-saml-protocol.md)

[Azure AD B2B](../external-identities/what-is-b2b.md)(파트너 및 공급업체와 같은 외부 사용자용)

[Azure AD 조건부 액세스](../conditional-access/overview.md)

[Azure ID 보호](../identity-protection/overview-identity-protection.md)

[SSO 액세스](./what-is-single-sign-on.md)

[애플리케이션 SSO 자습서](../saas-apps/tutorial-list.md)

[백서 다운로드 - Single Sign-On 배포 계획](https://aka.ms/SSODeploymentPlan)
