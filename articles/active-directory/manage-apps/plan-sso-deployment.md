---
title: Azure Active Directory 단일 사인온 배포 계획
description: 조직에서 SSO를 계획, 배포 및 관리하는 데 도움이 되는 가이드입니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512830"
---
# <a name="plan-a-single-sign-on-deployment"></a>Single Sign-On 배포 계획

단일 사인온(SSO)은 단일 사용자 계정을 사용하여 한 번만 로그인하여 사용자에게 필요한 모든 응용 프로그램 및 리소스에 액세스하는 것을 의미합니다. SSO를 사용하면 두 번째 인증없이 필요한 모든 응용 프로그램에 액세스할 수 있습니다.

## <a name="benefits-of-sso"></a>SSO의 이점

단일 사인온(SSO)은 사용자가 Azure Active Directory(Azure AD)의 응용 프로그램에 로그온할 때 보안과 편의성을 추가합니다. 

대부분의 조직에서는 최종 사용자 생산성을 위해 Office 365, Box 및 Salesforce와 같은 SaaS(서비스) 응용 프로그램으로 소프트웨어를 사용합니다. 지금까지 IT 직원은 각 SaaS 응용 프로그램에서 사용자 계정을 개별적으로 만들고 업데이트해야 했으며 사용자는 각 응용 프로그램에 대한 암호를 기억해야 했습니다.

Azure Marketplace에는 사전 통합된 SSO 연결이 있는 3,000개 이상의 응용 프로그램이 있으므로 테넌트에 쉽게 통합할 수 있습니다.

## <a name="licensing"></a>라이선스

- **Azure AD 라이선스** - 사전 통합된 SaaS 응용 프로그램에 대한 SSO는 무료입니다. 그러나 디렉터리에 있는 개체 수와 배포하려는 기능에는 추가 라이선스가 필요할 수 있습니다. 전체 라이센스 요구 사항 목록은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하십시오.
- **응용 프로그램 라이선스** - 비즈니스 요구 사항을 충족하기 위해 SaaS 응용 프로그램에 적합한 라이선스가 필요합니다. 응용 프로그램 소유자와 협력하여 응용 프로그램에 할당된 사용자가 응용 프로그램 내에서 해당 역할에 적합한 라이선스를 가지고 있는지 확인합니다. Azure AD가 역할에 따라 자동 프로비저닝을 관리하는 경우 Azure AD에 할당된 역할은 응용 프로그램 내에서 소유한 라이선스 수와 일치해야 합니다. 응용 프로그램에 소유된 라이센스수가 잘못되어 사용자의 프로비저닝/업데이트 중에 오류가 발생할 수 있습니다.

## <a name="plan-your-sso-team"></a>SSO 팀 계획

- **올바른 이해 관계자 참여** - 기술 프로젝트가 실패하면 일반적으로 영향, 결과 및 책임에 대한 불일치 기대로 인해 수행됩니다. 이러한 위험을 방지하려면 [올바른 이해 관계자와](https://aka.ms/deploymentplans) 이해 관계자가 자신의 역할을 이해해야 합니다.
- **계획 커뮤니케이션** - 통신은 새로운 서비스의 성공에 매우 중요합니다. 사용자에게 환경이 어떻게 변할지, 언제 변경될지, 문제가 발생할 경우 지원을 받는 방법에 대해 사전에 사용자에게 전달합니다. 최종 사용자가 [SSO 지원 응용 프로그램에 액세스하는 방법에](end-user-experiences.md)대한 옵션을 검토하고 선택한 응용 프로그램에 맞게 통신을 만들 수 있습니다. 

## <a name="plan-your-sso-protocol"></a>SSO 프로토콜 계획

페더레이션 프로토콜을 기반으로 하는 SSO 구현은 보안, 안정성 및 최종 사용자 환경을 향상시키고 구현하기가 더 쉽습니다. 대부분의 응용 프로그램은 Azure AD에 미리 통합되어 [있으며 사용 가능한 단계 별 가이드가 있습니다.](../saas-apps/tutorial-list.md) [Azure 마켓플레이스에서](https://azuremarketplace.microsoft.com/marketplace/)찾을 수 있습니다. 각 SSO 방법에 대한 자세한 정보는 [Azure Active Directory의 응용 프로그램에 대한 Single Sign-on](what-is-single-sign-on.md)문서에서 찾을 수 있습니다.

사용자가 앱에 대해 단일 로그온할 수 있도록 하는 두 가지 기본 방법이 있습니다.

- **페더레이션된 단일 사인온** Azure AD는 Azure AD 계정을 사용하여 사용자를 응용 프로그램에 인증합니다. 이 메서드는 SAML 2.0, WS-페더레이션 또는 OpenID Connect와 같은 프로토콜을 지원하는 응용 프로그램에 지원되며 단일 사인온의 가장 풍부한 모드입니다. 암호 기반 SSO 및 ADFS 대신 응용 프로그램이 지원하는 경우 Azure AD와 함께 페더레이션된 SSO를 사용하는 것이 좋습니다.

- **암호 기반 단일 사인온** 사용자가 사용자 이름과 암호를 사용하여 응용 프로그램에 처음 로그인하면 액세스합니다. 첫 번째 로그온 후에는 Azure AD가 사용자 이름 및 암호를 애플리케이션에 제공합니다. 암호 기반 Single Sign-On을 사용하면 웹 브라우저 확장 또는 모바일 앱을 사용하여 안전하게 애플리케이션 암호를 스토리지하고 재생할 수 있습니다. 이 옵션은 응용 프로그램에서 제공하는 기존 로그인 프로세스를 활용하고, 관리자가 암호를 관리할 수 있도록 하며, 사용자가 암호를 알 필요가 없습니다.

### <a name="considerations-for-federation-based-sso"></a>페더레이션 기반 SSO에 대한 고려 사항

- **OpenID Connect 및 OAuth 사용** - 연결하는 응용 프로그램이 이를 지원하는 경우 OIDC/OAuth 2.0 방법을 사용하여 해당 응용 프로그램에 SSO를 사용하도록 설정합니다. 이 방법을 사용하면 구성이 덜 필요하고 사용자 환경이 풍부해지습니다. 자세한 내용은 [OAuth 2.0,](../develop/v2-oauth2-auth-code-flow.md) [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)및 [Azure Active Directory 개발자 가이드를](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)참조하십시오.
- **SAML 기반 SSO에 대한 엔드포인트 구성** - SAML을 사용하는 경우 개발자는 응용 프로그램을 구성하기 전에 특정 정보가 필요합니다. 자세한 내용은 [기본 SAML 구성 편집을](configure-single-sign-on-non-gallery-applications.md)참조하십시오.
- **SAML 기반 SSO에 대한 인증서 관리** - 응용 프로그램에 대해 페더레이션 된 SSO를 사용하도록 설정하면 Azure AD는 기본적으로 3년 동안 유효한 인증서를 만듭니다. 필요한 경우 해당 인증서의 만료 날짜를 사용자 지정할 수 있습니다. 인증서가 만료되기 전에 인증서를 갱신할 수 있는 프로세스가 마련되어 있는지 확인합니다. 자세한 내용은 [Azure AD 관리 인증서 를](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)참조하십시오.

### <a name="considerations-for-password-based-sso"></a>암호 기반 SSO에 대한 고려 사항

암호 기반 SSO에 Azure AD를 사용하려면 자격 증명을 안전하게 검색하고 로그인 양식을 작성하는 브라우저 확장을 배포해야 합니다. 지원되는 브라우저를 사용하여 확장을 대규모로 배포하는 [메커니즘을 정의합니다.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) 옵션은 다음과 같습니다.

- [인터넷 익스플로러에 대한 그룹 정책](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [인터넷 익스플로러용 구성 관리자](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [크롬에 대 한 사용자 중심다운로드 및 구성, 파이어 폭스, 마이크로소프트 가장자리, 또는 IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

자세한 내용은 [에서 암호 단일 로그를 구성하는 방법을 참조하세요.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>갤러리에 없는 응용 프로그램에 대한 로그인 양식 메타데이터 캡처

Microsoft는 암호 보관(사용자 이름 및 암호 필드 캡처)을 위해 웹 응용 프로그램에서 메타데이터를 캡처할 수 있습니다. 양식 메타데이터를 캡처하도록 응용 프로그램을 구성하는 과정에서 로그인 URL로 이동합니다. 응용 프로그램 소유자에게 정확한 로그인 URL을 요청합니다. 이 정보는 로그온 프로세스 중에 Azure AD 자격 증명을 로그온하는 동안 응용 프로그램에 매핑하는 데 사용됩니다.

자세한 내용은 [Azure AD를 사용하여 응용 프로그램 액세스 및 SSO란 무엇입니까? – 암호 기반 SSO 를](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)참조하십시오.

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>양식의 메타데이터를 다시 캡처해야 한다는 표시

응용 프로그램이 HTML 레이아웃을 변경하는 경우 변경 내용을 조정하기 위해 메타데이터를 다시 캡처해야 할 수 있습니다. HTML 레이아웃이 변경되었음을 나타내는 일반적인 증상은 다음과 같습니다.

- 응용 프로그램을 클릭하는 사용자가 로그인 페이지에서 "멈췄다"고 보고하는 사용자
- 사용자 이름 또는 암호가 채워지지 않는다고 보고하는 사용자

#### <a name="shared-accounts"></a>공유 계정

로그인 관점에서 공유 계정이 있는 응용 프로그램은 개별 사용자에 대해 암호 SSO를 사용하는 갤러리 응용 프로그램과 다르지 않습니다. 그러나 공유 계정을 사용하기 위한 응용 프로그램을 계획하고 구성할 때 몇 가지 추가 단계가 필요합니다.

1. 응용 프로그램 비즈니스 사용자와 협력하여 다음을 문서화합니다.
   1. 응용 프로그램을 사용할 조직의 사용자 집합
   1. 사용자 집합과 연결된 응용 프로그램의 기존 자격 증명 집합 
1. 사용자 집합과 자격 증명의 각 조합에 대해 요구 사항에 따라 클라우드 또는 온-프레미스에 보안 그룹을 만듭니다.
1. 공유 자격 증명을 재설정합니다. 앱이 Azure AD에 배포되면 개인은 공유 계정의 암호를 필요로 하지 않습니다. Azure AD는 암호를 저장하므로 암호를 매우 길고 복잡하게 설정하는 것이 좋습니다. 
1. 응용 프로그램에서 암호를 지원하는 경우 암호의 자동 롤오버를 구성합니다. 이렇게 하면 초기 설정을 한 관리자조차도 공유 계정의 암호를 알 수 없습니다. 

## <a name="plan-your-authentication-method"></a>인증 방법 계획

따라서 올바른 인증 방법을 선택하는 것은 Azure AD 하이브리드 ID 솔루션 설정에서 매우 중요한 첫 번째 결정입니다. 클라우드에서 사용자를 프로비저닝하기도 하는 Azure AD Connect를 사용하여 구성되는 인증 방법을 구현하세요.

인증 방법을 선택하려면 시간, 기존 인프라, 복잡성 및 선택을 구현하는 데 드는 비용을 고려해야 합니다. 이러한 요소는 조직마다 다르며 시간이 흐름에 따라 변경될 수 있습니다. 특정 시나리오와 가장 밀접하게 일치하는 시나리오를 선택해야 합니다. 자세한 내용은 [Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택을](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)참조하십시오.

## <a name="plan-your-security-and-governance"></a>보안 및 거버넌스 계획 

ID는 BYOD 장치 및 클라우드 응용 프로그램의 폭발로 인해 네트워크 경계가 점점 다공성및 덜 효과적이기 때문에 보안 주의 및 투자의 새로운 주요 피벗입니다. 

### <a name="plan-access-reviews"></a>액세스 검토 계획

[Access Review를](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) 사용하면 조직에서 그룹 구성원 자격, 엔터프라이즈 응용 프로그램 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스를 정기적으로 검토하여 올바른 사용자만 계속 액세스할 수 있도록 해야 합니다.

액세스 검토를 설정하는 동안 계획해야 할 주요 항목 중 일부는 다음과 같습니다.

1. 비즈니스 요구에 맞는 액세스 검토의 흐름을 식별합니다. 이는 일주일에 한 번, 매월, 매년 또는 주문형 운동으로 자주 사용할 수 있습니다.

1. 앱 액세스 보고서의 검토자를 나타내는 그룹을 만듭니다. 앱에 가장 잘 익숙한 이해 관계자와 대상 사용자 및 사용 사례에 액세스 검토의 참여자인지 확인해야 합니다.

1. 액세스 검토를 완료하는 것은 더 이상 액세스가 필요하지 않은 사용자에게 앱 액세스 권한을 빼앗는 것입니다. 거부된 사용자의 잠재적지원 요청을 처리하기 위한 계획입니다. 삭제된 사용자는 30일 동안 Azure AD에서 삭제된 상태로 유지되며, 이 기간 동안 필요한 경우 관리자가 복원할 수 있습니다. 30일 후 해당 사용자가 영구적으로 삭제됩니다. Azure Active Directory 포털을 사용하여 글로벌 관리자는 해당 기간에 도달하기 전에 최근에 삭제된 사용자를 명시적으로 삭제할 수 있습니다.

### <a name="plan-auditing"></a>계획 감사

Azure AD는 [기술 및 비즈니스 통찰력을 포함하는 보고서를](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)제공합니다. 

보안 및 활동 보고서를 모두 사용할 수 있습니다. 보안 보고서에는 위험에 플래그가 지정된 사용자와 위험한 로그인이 표시됩니다. 활동 보고서는 로그인 활동을 자세히 설명하고 모든 로그인에 대한 감사 추적을 제공하여 조직의 사용자의 동작을 이해하는 데 도움이 됩니다. 보고서를 사용하여 위험을 관리하고 생산성을 높이며 규정 준수를 모니터링할 수 있습니다.

| 보고서 종류 | 액세스 검토 | 보안 보고서 | 로그인 보고서 |
|-------------|---------------|------------------|----------------|
| 검토하는 데 사용 | 응용 프로그램 권한 및 사용 권한입니다. | 잠재적으로 손상된 계정 | 응용 프로그램에 액세스하는 사용자 |
| 잠재적인 작업 | 감사 액세스; 권한 취소 | 액세스 취소; 강제 보안 재설정 | 액세스 취소 |

Azure AD는 30일 동안 대부분의 감사 데이터를 유지하며 Azure 관리 포털 또는 API를 통해 데이터를 사용하여 분석 시스템에 다운로드할 수 있도록 합니다.

### <a name="consider-using-microsoft-cloud-application-security"></a>Microsoft 클라우드 응용 프로그램 보안 사용 고려

마이크로소프트 클라우드 앱 보안 (MCAS)는 클라우드 액세스 보안 브로커 (CASB) 솔루션입니다. 클라우드 앱 및 서비스에 대한 가시성을 제공하고, 사이버 위협을 식별하고 대처하는 정교한 분석을 제공하며, 데이터 이동 방식을 제어할 수 있습니다.

MCAS를 배포하면 다음을 수행할 수 있습니다.

- 클라우드 검색을 사용하여 조직에서 사용하는 클라우드 환경과 클라우드 앱을 매핑하고 식별합니다.
- 클라우드에서 승인 및 비승인 앱
- 공급자 API를 활용하는 배포가 간편한 앱 커넥터를 사용하여 연결하는 앱의 가시성 및 거버넌스
- 조건부 액세스 앱 제어 보호를 사용하여 클라우드 앱 내의 액세스 및 활동에 대한 실시간 가시성 및 제어
- 정책을 설정하고 지속적으로 미세 조정하여 지속적으로 제어할 수 있습니다.

Microsoft 클라우드 응용 프로그램 보안(MCAS) 세션 컨트롤은 모든 운영 체제의 모든 주요 플랫폼의 모든 브라우저에서 사용할 수 있습니다. 모바일 앱과 데스크톱 앱도 차단되거나 허용될 수 있습니다. 기본적으로 Azure AD와 통합하면 SAML로 구성된 모든 앱 또는 Azure AD에서 단일 사인온이 있는 Open ID Connect 앱을 지원할 수 있습니다( [여러 주요 응용 프로그램을](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)포함)

MCAS에 대한 자세한 내용은 [Microsoft 클라우드 앱 보안 개요를](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)참조하십시오. MCAS는 사용자 기반 구독 서비스입니다. [MCAS 라이선스 데이터시트에서](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)라이선스 세부 정보를 검토할 수 있습니다.

### <a name="use-conditional-access"></a>조건부 액세스 사용

조건부 액세스를 사용하면 클라우드 앱에 대한 조건 기반 액세스 제어 결정을 자동화할 수 있습니다.

1단계 인증이 완료된 후 조건부 액세스 정책이 적용됩니다. 따라서 조건부 액세스는 DoS(서비스 거부) 공격과 같은 시나리오에 대한 첫 번째 줄 방어용이 아니라 이러한 이벤트의 신호를 사용하여 액세스를 확인할 수 있습니다. 예를 들어 로그인 위험 수준, 요청 위치 등을 사용할 수 있습니다. 조건부 액세스에 대한 자세한 내용은 [개요](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) 및 [배포 계획을](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)참조하십시오.

## <a name="azure-sso-technical-requirements"></a>Azure SSO 기술 요구 사항

다음 섹션에서는 필요한 환경, 끝점, 클레임 매핑, 필요한 특성, 인증서 및 사용되는 프로토콜을 포함하여 특정 응용 프로그램을 구성하는 요구 사항을 자세히 설명합니다. [Azure AD 포털에서](https://portal.azure.com/)SSO를 구성하려면 이 정보가 필요합니다.

### <a name="authentication-mechanism-details"></a>인증 메커니즘 세부 정보

사전 통합된 모든 SaaS 앱의 경우 Microsoft는 자습서를 제공하며 이 정보가 필요하지 않습니다. 응용 프로그램이 응용 프로그램 마켓플레이스/갤러리에 없는 경우 다음과 같은 데이터를 수집해야 할 수 있습니다.

- **응용 프로그램이 SSO에 사용하는 현재 ID 공급자 (예:** AD FS, PingFederate, Okta)
- **대상 응용 프로그램에서 지원하는 프로토콜** - 예를 들어, SAML 2.0, OpenID Connect, OAuth, 양식 기반 인증, WS-Fed, WS-Trust
- **Azure AD로 구성되는 프로토콜** - 예를 들어 SAML 2.0 또는 1.1, OpenID 연결, OAuth, 양식 기반, WS-Fed

### <a name="attribute-requirements"></a>특성 요구 사항

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 간에 미리 구성된 특성 및 특성 매핑 집합이 있습니다. 일부 앱은 그룹과 같은 다른 유형의 개체를 관리합니다. Azure AD에서 응용 프로그램에 대한 사용자 특성 매핑을 계획하고 비즈니스 요구 사항에 따라 [기본 특성 매핑을 사용자 지정합니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)

### <a name="certificate-requirements"></a>인증서 요구 사항

응용 프로그램에 대한 인증서는 최신 상태여야 하거나 사용자가 응용 프로그램에 액세스할 수 없는 위험이 있습니다. 대부분의 SaaS 응용 프로그램 인증서는 36개월 동안 적합합니다. 응용 프로그램 블레이드에서 해당 인증서 기간을 변경합니다. 만료를 문서화하고 인증서 갱신을 관리하는 방법을 알고 있는지 확인하십시오. 

인증서를 관리하는 방법에는 두 가지가 있습니다. 

- **자동 인증서 롤오버** - Microsoft는 [Azure AD에서 키 롤오버 서명을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)지원합니다. 인증서를 관리하는 데 선호되는 방법이지만 모든 ISV가 이 시나리오를 지원하는 것은 아닙니다.

- **수동으로 업데이트** - 모든 응용 프로그램에는 정의된 방식에 따라 만료되는 자체 인증서가 있습니다. 응용 프로그램의 인증서가 만료되기 전에 새 인증서를 만들어 ISV로 보냅니다. 이 정보는 페더레이션 메타데이터에서 가져온 것입니다. [페더레이션 메타데이터에 대한 자세한 내용은 여기를 참조하십시오.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>SSO 구현

다음 단계를 사용하여 조직에서 솔루션을 계획하고 배포합니다.

### <a name="user-configuration-for-sso"></a>SSO용 사용자 구성

- **테스트 사용자 식별**

   앱 소유자에게 연락하여 응용 프로그램 내에서 최소 3명의 테스트 사용자를 만들어 달라고 요청합니다. 기본 식별자로 사용할 정보가 올바르게 채워지고 Azure AD에서 사용할 수 있는 특성과 일치하는지 확인합니다. 대부분의 경우 SAML 기반 응용 프로그램의 "NameID"에 매핑됩니다. JWT 토큰의 경우 "preferred_username"입니다.
   
   Azure AD에서 사용자를 클라우드 기반 사용자로 수동으로 만들거나 Azure AD Connect 동기화 엔진을 사용하여 온-프레미스에서 사용자를 동기화합니다. 정보가 응용 프로그램으로 전송되는 클레임과 일치하는지 확인합니다.

- **SSL 구성**

   응용 [프로그램 목록에서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)응용 프로그램에 대한 SSO 자습서를 찾아 여은 다음 자습서의 단계를 수행하여 SaaS 응용 프로그램을 성공적으로 구성합니다.

   응용 프로그램을 찾을 수 없는 경우 [사용자 지정 응용 프로그램 설명서를](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)참조하십시오. Azure AD 갤러리에 없는 응용 프로그램을 추가 하는 방법을 설명 합니다.

   선택적으로 [Microsoft의 지침 설명서를](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)사용하여 엔터프라이즈 응용 프로그램에 대해 SAML 토큰에서 발급된 클레임을 사용할 수 있습니다. 응용 프로그램에 대한 SAML 응답에서 수신할 것으로 예상되는 맵으로 이 맵을 확인합니다. 구성 중에 문제가 발생하면 [SSO 통합을 디버깅하는 방법에](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)대한 지침을 사용합니다.

사용자 지정 응용 프로그램 온보딩AzureAzure AD 프리미엄 P1 또는 P2 라이센스 기능입니다.

### <a name="provide-sso-change-communications-to-end-users"></a>최종 사용자에게 SSO 변경 통신 제공

커뮤니케이션 계획을 구현합니다. 최종 사용자에게 변경이 오고 있는지, 언제 도착했는지, 지금 해야 할 일, 도움을 구하는 방법을 알려주십시오.

### <a name="verify-end-user-scenarios-for-sso"></a>SSO에 대한 최종 사용자 시나리오 확인

다음 테스트 사례를 사용하여 회사 소유 및 개인 장치에서 테스트를 수행하여 SSO 구성이 예상대로 작동하는지 확인할 수 있습니다. 아래 시나리오에서는 사용자가 응용 프로그램 URL로 이동하여 서비스 공급자가 시작한 인증 흐름(SP-시작 인증 흐름)을 거치고 있다고 가정합니다.

| 시나리오 | 사용자별 SP 시작 인증 흐름에 대한 예상 결과 |
|----------|---------------------------------------------------|
| corpnet에있는 동안 IE와 응용 프로그램에 로그인합니다. | 통합 된 Windows 인증 (IWA) 추가 프롬프트 없이 발생 합니다. |
| 새 로그인 시도로 corpnet을 해제하는 동안 IE를 사용하여 응용 프로그램에 로그인합니다. | AD FS 서버에서 양식 기반 프롬프트. 사용자가 성공적으로 로그인하고 MFA에 대한 브라우저 프롬프트를 표시합니다. |
| 현재 세션으로 corpnet을 해제하는 동안 IE를 사용하여 응용 프로그램에 로그인하고 MFA를 수행한 적이 없습니다. | 사용자가 첫 번째 요소에 대한 프롬프트를 받지 않습니다. 사용자는 MFA에 대한 프롬프트를 받습니다. |
| 현재 세션이 있는 코프넷을 끄고 이 세션에서 이미 MFA를 수행한 상태에서 IE를 사용하여 응용 프로그램에 로그인합니다. | 사용자가 첫 번째 요소에 대한 프롬프트를 받지 않습니다. 사용자가 MFA를 받지 않습니다. 응용 프로그램에 사용자 SSO. |
| 현재 세션과 함께 corpnet을 해제하는 동안 크롬 / 파이어 폭스 / 사파리와 응용 프로그램에 로그인하고 이미이 세션에서 MFA를 수행했습니다. | 사용자가 첫 번째 요소에 대한 프롬프트를 받지 않습니다. 사용자가 MFA를 받지 않습니다. 사용자 SSO가 응용 프로그램에 들어갑니다. |
| 새로운 로그인 시도와 corpnet오프하는 동안 크롬 / 파이어 폭스 / 사파리와 응용 프로그램에 로그인. | AD FS 서버에서 양식 기반 프롬프트. 사용자가 성공적으로 로그인하고 MFA에 대한 브라우저 프롬프트를 표시합니다. |
| 현재 세션이 있는 회사 네트워크에 있는 동안 Chrome/Firefox를 사용하여 애플리케이션에 로그인합니다. | 사용자가 첫 번째 요소에 대한 프롬프트를 받지 않습니다. 사용자가 MFA를 받지 않습니다. 사용자 SSO가 응용 프로그램에 들어갑니다. |
| 새 로그인 시도로 응용 프로그램 모바일 앱으로 응용 프로그램에 로그인합니다. | AD FS 서버에서 양식 기반 프롬프트. 사용자가 성공적으로 로그인하고 ADAL 클라이언트가 MFA에 대한 프롬프트를 표시합니다. |
| 권한이 있는 사용자가 로그인 URL을 사용하여 응용 프로그램에 로그인하려고 시도합니다. | AD FS 서버에서 양식 기반 프롬프트. 사용자가 첫 번째 요소로 로그인하지 못합니다. |
| 권한이 있는 사용자가 로그인을 시도하지만 잘못된 암호를 입력합니다. | 사용자가 응용 프로그램 URL로 이동하여 잘못된 사용자 이름/암호 오류를 받습니다. |
| 권한이 있는 사용자는 이메일의 링크를 클릭하고 이미 인증되었습니다. | 사용자가 URL을 클릭하고 추가 프롬프트 없이 응용 프로그램에 로그인합니다. |
| 권한이 있는 사용자는 전자 메일의 링크를 클릭하며 아직 인증되지 않았습니다. | 사용자가 URL을 클릭하고 첫 번째 요소로 인증하라는 메시지가 표시됩니다. |
| 권한이 있는 사용자는 새 로그인 시도로 응용 프로그램 모바일 앱(SP 시작)을 통해 응용 프로그램에 로그인합니다. | AD FS 서버에서 양식 기반 프롬프트. 사용자가 성공적으로 로그인하고 ADAL 클라이언트가 MFA에 대한 프롬프트를 표시합니다. |
| 권한이 있는 사용자가 로그인 URL(SP 시작)을 사용하여 응용 프로그램에 로그인하려고 시도합니다. | AD FS 서버에서 양식 기반 프롬프트. 사용자가 첫 번째 요소로 로그인하지 못합니다. |
| 권한이 있는 사용자가 로그인을 시도하지만 잘못된 암호를 입력합니다.| 사용자가 응용 프로그램 URL로 이동하여 잘못된 사용자 이름/암호 오류를 받습니다. |
| 권한이 있는 사용자는 로그아웃한 다음 다시 로그인합니다. | 로그아웃 URL이 구성된 경우 사용자는 모든 서비스에서 로그아웃되고 인증하라는 메시지가 표시됩니다. |
| 권한이 있는 사용자는 로그아웃한 다음 다시 로그인합니다. | 로그아웃 URL이 구성되지 않은 경우 사용자는 기존 Azure AD 브라우저 세션의 기존 토큰을 사용하여 자동으로 로그온됩니다. |
| 권한이 있는 사용자는 이메일의 링크를 클릭하고 이미 인증되었습니다. | 사용자가 URL을 클릭하고 추가 프롬프트 없이 응용 프로그램에 로그인합니다. |
| 권한이 있는 사용자는 전자 메일의 링크를 클릭하며 아직 인증되지 않았습니다. | 사용자가 URL을 클릭하고 첫 번째 요소로 인증하라는 메시지가 표시됩니다. |

## <a name="manage-sso"></a>SSO 관리

이 섹션에서는 SSO를 성공적으로 관리하기 위한 요구 사항 및 권장 사항을 간략하게 설명합니다.

### <a name="required-administrative-roles"></a>필수 관리 역할

Azure Active Directory 내에서 필요한 작업을 수행하는 데 사용할 수 있는 사용 권한이 가장 적은 경우 항상 역할을 사용합니다. 사용 [가능한 다양한 역할을 검토하고](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 이 응용 프로그램에 대한 각 사용자 에 대한 요구를 해결하기 위해 올바른 역할을 선택하는 것이 좋습니다. 배포가 완료된 후 일부 역할을 일시적으로 적용하고 제거해야 할 수 있습니다.

| 인물| 역할 | Azure AD 역할(필요한 경우) |
|--------|-------|-----------------------------|
| 헬프 데스크 관리자 | 계층 1 지원 | None |
| ID 관리자 | 문제가 Azure AD에 영향을 미치는 경우 구성 및 디버그 | 글로벌 관리자 |
| 응용 프로그램 관리자 | 응용 프로그램의 사용자 증명, 권한이있는 사용자에 대한 구성 | None |
| 인프라 관리자 | 인증서 롤오버 소유자 | 글로벌 관리자 |
| 비즈니스 소유자/이해 관계자 | 응용 프로그램의 사용자 증명, 권한이있는 사용자에 대한 구성 | None |

[PIM(권한 있는 ID 관리)을](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) 사용하여 역할을 관리하여 디렉터리 권한이 있는 사용자에 대한 추가 감사, 제어 및 액세스 검토를 제공하는 것이 좋습니다.

### <a name="sso-certificate-lifecycle-management"></a>SSO 인증서 수명 주기 관리

Azure AD와 단일 사인온으로 구성된 응용 프로그램 간의 서명 인증서 수명 주기를 관리하는 임무를 맡은 올바른 역할 및 전자 메일 배포 목록을 식별하는 것이 중요합니다. 다음은 권장되는 몇 가지 주요 역할입니다.

- 응용 프로그램에서 사용자 속성을 업데이트하는 소유자
- 응용 프로그램 중단/수정 지원을 위한 소유자 온-콜
- 인증서 관련 변경 알림에 대해 면밀히 모니터링된 전자 메일 배포 목록

인증서의 최대 수명은 3년입니다. Azure AD와 응용 프로그램 간에 인증서 변경을 처리하는 방법에 대한 프로세스를 설정하는 것이 좋습니다. 이렇게 하면 인증서 만료 또는 강제 인증서 롤오버로 인한 중단을 방지하거나 최소화할 수 있습니다.

### <a name="rollback-process"></a>롤백 프로세스

테스트 사례를 기반으로 테스트를 완료한 후에는 응용 프로그램과 함께 프로덕션으로 전환할 차례입니다. 프로덕션으로 이동하면 프로덕션 테넌트에서 계획되고 테스트된 구성을 구현하여 사용자에게 롤아웃할 수 있습니다. 그러나 배포가 계획대로 진행되지 않을 경우 를 대비하여 수행할 작업을 계획하는 것이 중요합니다. 배포 중에 SSO 구성에 장애가 발생하면 중단을 완화하고 사용자에게 미치는 영향을 줄이는 방법을 이해해야 합니다.

응용 프로그램 내에서 인증 방법의 가용성에 따라 최상의 전략이 결정됩니다. 배포에 문제가 발생했을 때 원래 로그인 구성 상태로 돌아가는 방법에 대한 앱 소유자에 대한 자세한 설명서를 항상 준비해야 합니다.

- **앱이**LDAP 및 AD FS 및 Ping과 같은 여러 ID 공급자를 지원하는 경우 롤아웃 중에 기존 SSO 구성을 삭제하지 마십시오. 대신 나중에 다시 전환해야 하는 경우 마이그레이션 중에 사용하지 않도록 설정합니다. 

- **앱에서 여러 IDP를 지원하지 않지만** 사용자가 양식 기반 인증(사용자 이름/암호)을 사용하여 로그인할 수 있도록 허용하는 경우 새 SSO 구성 롤아웃에 실패할 경우 사용자가 이 접근 방식으로 대체할 수 있는지 확인합니다.

### <a name="access-management"></a>액세스 관리

리소스에 대한 액세스를 관리할 때 확장된 접근 방식을 선택하는 것이 좋습니다. 일반적인 방법은 Azure AD Connect를 통해 동기화하거나, [사용자 특성을 기반으로 Azure AD에서 동적 그룹을 만들거나,](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)리소스 소유자가 관리하는 Azure AD에서 [셀프 서비스 그룹을](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) 만들어 온-프레미스 그룹을 활용하는 것입니다.

### <a name="monitor-security"></a>보안 모니터링

SaaS 앱 보안의 다양한 측면을 검토하고 필요한 수정 작업을 수행하는 정기적인 케이던스를 설정하는 것이 좋습니다.

### <a name="troubleshooting"></a>문제 해결

다음 링크는 문제 해결 시나리오를 제시합니다. 이러한 시나리오와 이를 해결하는 단계를 통합하는 지원 담당자를 위한 특정 가이드를 만들 수 있습니다.

#### <a name="consent-issues"></a>동의 문제

- [예기치 않은 동의 오류](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [사용자 동의 오류](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>로그인 문제

- [사용자 지정 포털에서 로그인하는 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [액세스 패널에서 로그인 시 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [애플리케이션 로그인 페이지에서의 오류](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Microsoft 응용 프로그램에 로그인하는 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Azure 응용 프로그램 갤러리에 나열된 응용 프로그램에 대한 SSO 문제

- [Azure 응용 프로그램 갤러리에 나열 된 응용 프로그램에 대 한 암호 SSO 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Azure 응용 프로그램 갤러리에 나열된 응용 프로그램에 대한 페더레이션된 SSO 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Azure 응용 프로그램 갤러리에 나열 되지 않은 응용 프로그램에 대 한 SSO 문제

- [Azure 응용 프로그램 갤러리에 나열 되지 않은 응용 프로그램에 대 한 암호 SSO 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Azure 응용 프로그램 갤러리에 나열 되지 않은 응용 프로그램에 대 한 페더레이션 된 SSO 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>다음 단계

[SAML 기반 SSO 디버그](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[PowerShell을 통한 앱에 대한 클레임 매핑](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[SAML 토큰에서 발행된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[단일 사인온 SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Single Sign-Out SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B(파트너](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) 및 공급업체와 같은 외부 사용자용)

[Azure AD 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[응용 프로그램 SSO 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
