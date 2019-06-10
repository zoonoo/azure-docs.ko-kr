---
title: Azure Active Directory single sign-on 배포 계획
description: 계획, 배포 및 조직에서 SSO를 관리할 수 있도록 안내 합니다.
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
ms.openlocfilehash: 29569302d20e23c95b6508a5b58c7ed96e005885
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499251"
---
# <a name="plan-a-single-sign-on-deployment"></a>Single sign-on 배포 계획

single sign-on (SSO) 사용자가 단일 사용자 계정을 사용 하 여 한 번만 로그인 해야 하는 모든 응용 프로그램 형식 및 리소스에 액세스를 의미 합니다. SSO를 사용 하 여 사용자를 한 번 인증을 수행 하지 않고 필요한 모든 응용 프로그램을 액세스할 수 있습니다.

## <a name="benefits-of-sso"></a>SSO의 이점

single sign-on (SSO) 사용자가 Azure Active Directory (Azure AD)에서 응용 프로그램에 로그온 하는 경우 보안과 편리 하 게 추가 합니다. 

대부분의 조직 서비스 (SaaS) 응용 프로그램을 Office 365, Box 및 Salesforce와 같은 최종 사용자 생산성에 대 한 소프트웨어에 의존합니다. 지금까지 IT 직원이 개별적으로 만들고 각 SaaS 응용 프로그램 및 각각에 대 한 암호를 기억 하는 데 필요한 사용자의 사용자 계정을 업데이트 해야 합니다.

Azure Marketplace에 사전 통합 된 SSO 연결과 3,000 응용 프로그램이 테 넌 트에 통합할 수 있도록 합니다.

## <a name="licensing"></a>라이선스

- **Azure AD 라이선스** -SSO 사전 통합 된 SaaS 응용 프로그램은 무료입니다. 그러나 배포 하려는 기능과 디렉터리의 개체 수가 추가 라이선스가 필요할 수 있습니다. 라이선스 요구 사항의 전체 목록을 참조 하세요 [Azure Active Directory 가격](https://azure.microsoft.com/pricing/details/active-directory/)합니다.
- **응용 프로그램 라이선스** -비즈니스 요구 사항에 맞게 SaaS 응용 프로그램에 대 한 적절 한 라이선스가 필요 합니다. 응용 프로그램에 할당 된 사용자 응용 프로그램 내에서 해당 역할에 대 한 적절 한 라이선스가 있는지 여부를 확인 하려면 응용 프로그램 소유자를 사용 하 여 작동 합니다. Azure AD 관리 역할을 기반으로 자동 프로 비전 하는 경우 Azure AD에서 할당 된 역할은 응용 프로그램 내에서 소유 된 라이선스의 수를 사용 하 여 정렬 되어야 합니다. 프로 비전/업데이트 하는 중 사용자의 응용 프로그램에서 소유 된 라이선스의 수를 부적절 한 오류가 발생할 수 있습니다.

## <a name="plan-your-sso-team"></a>SSO 팀 계획

- **오른쪽 관련자 참여 유도** -기술 프로젝트 실패 하는 경우 일반적으로 일치 하지 않는 기대 영향, 결과 및 책임에 때문에 나타나는 것입니다. 이러한 문제를 방지 하려면 [오른쪽 관련자에 참여 하는 확인](https://aka.ms/deploymentplans) 관련자에 게 해당 역할을 이해 하 고 있습니다.
- **통신 계획** -통신은 새 서비스의 성공에 중요 합니다. 사전에 통신할 사용자가 접하는 경험 어떻게 변경 됩니까, 됩니다 변경 경우 및 방법 지원 문제를 겪고 하는 경우. 에 대 한 옵션을 검토 [사용 응용 프로그램을 최종 사용자가 SSO 액세스 하는 방법을](end-user-experiences.md), 선택에 맞춰 통신을 작성 하 고 있습니다. 

## <a name="plan-your-sso-protocol"></a>SSO 프로토콜 계획

SSO 구현을 페더레이션 프로토콜을 기반으로 보안, 안정성 및 향상 하 고 최종 사용자 환경을 구현 하기가 쉽습니다. 많은 응용 프로그램은 Azure ad에 사전 통합 된 [단계별 가이드 제공](../saas-apps/tutorial-list.md)합니다. 찾을 수 있습니다 우리의 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)합니다. 이 문서에서 각 SSO 메서드에 대 한 자세한 정보를 찾을 수 있습니다 [Azure Active Directory에서 응용 프로그램에서 Single sign-on](what-is-single-sign-on.md)합니다.

두 가지 기본는 앱에 사용자가 single sign on으로 설정할 수 있습니다.

- **페더레이션된 single sign-on을 사용 하 여** Azure AD가 해당 Azure AD 계정을 사용 하 여 응용 프로그램에 사용자를 인증 합니다. 이 메서드는 지원 응용 프로그램에 대 한 지원 SAML 2.0, Ws-federation 또는 OpenID Connect와 같은 프로토콜 및 가장 모드에서 single sign-on입니다. 응용 프로그램 암호 기반 SSO 및 ADFS 대신,으로 지원 되는 경우 Azure AD와 페더레이션된 SSO를 사용 하는 것이 좋습니다.

- **암호 기반 single sign on을 사용 하 여** 사용자가 응용 프로그램에 사용자 이름 및 암호를 사용 하 여 처음으로 로그인 액세스 하는 날짜입니다. 첫 번째 로그온 후에는 Azure AD가 사용자 이름 및 암호를 애플리케이션에 제공합니다. 암호 기반 Single Sign-On을 사용하면 웹 브라우저 확장 또는 모바일 앱을 사용하여 안전하게 애플리케이션 암호를 저장하고 재생할 수 있습니다. 이 옵션 기존 로그인 프로세스 응용 프로그램 제공을 활용 하는 관리자가 암호를 관리할 수 있으며 사용자 암호를 알아야 할 필요 하지 않습니다.

### <a name="considerations-for-federation-based-sso"></a>페더레이션 기반 SSO에 대 한 고려 사항

- **OpenID Connect 및 OAuth를 사용 하 여** -연결 하려는 지원, 응용 프로그램 OIDC/OAuth 2.0 메서드를 사용 하 여 해당 응용 프로그램에 SSO를 사용 하도록 합니다. 이 메서드는 작은 구성이 필요 하 고 풍부한 사용자 환경을 활성화 합니다. 자세한 내용은 [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)를 [OpenID Connect 1.0](../develop/v2-protocols-oidc.md), 및 [Azure Active Directory 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)합니다.
- **SAML 기반 SSO에 대 한 끝점 구성을** -개발자가 응용 프로그램을 구성 하기 전에 특정 정보가 SAML을 사용 하는 경우 필요 합니다. 자세한 내용은 참조 하세요. [기본 SAML 옵션을 구성할](configure-single-sign-on-portal.md)합니다.
- **SAML 기반 SSO에 대 한 관리 인증서** -페더레이션된 SSO 사용 하면 응용 프로그램, Azure AD에는 기본적으로 3 년 동안 유효한 인증서를 만듭니다. 필요한 경우 해당 인증서의 만료 날짜를 사용자 지정할 수 있습니다. 인증서가 만료 되기 전에 갱신 하기 위해 준비에서 프로세스 했는지 확인 합니다. 자세한 내용은 참조 하세요 [Azure AD 관리 인증서](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)합니다.

### <a name="considerations-for-password-based-sso"></a>암호 기반 SSO에 대 한 고려 사항

암호 기반 SSO에 대 한 Azure AD를 사용 하 여 안전 하 게 자격 증명을 검색 하 고 로그인 양식을 작성 하는 브라우저 확장을 배포 해야 합니다. 사용 하 여 대규모 확장을 배포 하는 메커니즘을 정의 [지원 되는 브라우저](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)합니다. 다음 옵션을 사용할 수 있습니다.

- [Internet Explorer에 대 한 그룹 정책 ](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Internet Explorer 용 system Center Configuration Manager (SCCM) ](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [다운로드 및 Chrome, Firefox, Microsoft Edge 또는 IE에 대 한 구성을 기반 사용자 ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

자세한 내용은 참조 하세요 [암호 single sign-on을 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)합니다.

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>갤러리에 없는 응용 프로그램에 대 한 로그인 폼 메타 데이터를 캡처

Microsoft는 웹 응용 프로그램 암호 (사용자 이름 및 암호 필드를 캡처)를 자격 증명 모음 설정에 대 한 캡처 메타 데이터를 지원 합니다. Forms 메타 데이터를 캡처하는 응용 프로그램을 구성 하는 과정의 로그인 URL로 이동 합니다. 정확한 로그인 URL에 대 한 응용 프로그램 소유자를 요청 합니다. 이 정보는 로그온 하는 동안 응용 프로그램에 Azure AD 자격 증명 매핑 로그온 프로세스 중에 사용 됩니다.

자세한 내용은 참조 하세요 [응용 프로그램 액세스 및 SSO를 Azure AD 란?-암호 기반 SSO](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)합니다.

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>다시 캡처할 수 표시가 형태로 메타 데이터는 필요

응용 프로그램의 HTML 레이아웃이 변경 될 때 변경 내용에 맞게 메타 데이터를 캡처해야 해야 할 수 있습니다. HTML 레이아웃에 변경 내용이 있는지 여부를 나타내는 일반적인 증상은 다음과 같습니다.

- 사용자는 응용 프로그램을 클릭할 "중지" 로그인 페이지에서 보고
- 사용자 이름 또는 암호가 채워져 있지 않은 reporting 사용자

#### <a name="shared-accounts"></a>공유 계정

로그인 관점에서 응용 프로그램 공유 계정 사용 하 여 개별 사용자 용 암호 SSO를 사용 하는 갤러리 응용 프로그램에서 다른 없습니다. 그러나 몇 가지 추가 단계 계획 및 응용 프로그램을 구성할 공유 계정을 사용 하려는 경우 필수:

1. 다음 문서를 비즈니스 사용자가 응용 프로그램을 사용 하 여 작동 합니다.
   1. 응용 프로그램을 사용 하는 조직에서 사용자의 집합
   1. 기존 사용자 집합을 사용 하 여 연결 된 응용 프로그램에서 자격 증명 집합 
1. 자격 증명 및 사용자 집합의 각 조합에 대 한 클라우드 또는 온-프레미스 요구 사항에 따라 보안 그룹을 만듭니다.
1. 공유 자격 증명을 재설정 합니다. Azure AD에 앱을 배포한 후 개인 공유 계정의 암호가 필요 하지 않습니다. Azure AD 암호를 저장 하므로 매우 길고 복잡 한 수로 설정 하는 것이 좋습니다. 
1. 응용 프로그램에서 지 원하는 경우 암호의 자동 롤오버를 구성 합니다. 이런 방식으로 초기 설치를 수행한 관리자도 공유 계정의 암호를 알고 됩니다. 

## <a name="plan-your-authentication-method"></a>인증 방법 계획

따라서 올바른 인증 방법을 선택하는 것은 Azure AD 하이브리드 ID 솔루션 설정에서 매우 중요한 첫 번째 결정입니다. 클라우드에서 사용자를 프로비저닝하기도 하는 Azure AD Connect를 사용하여 구성되는 인증 방법을 구현하세요.

인증 방법을 선택하려면 시간, 기존 인프라, 복잡성 및 선택을 구현하는 데 드는 비용을 고려해야 합니다. 이러한 요소는 조직마다 다르며 시간이 흐름에 따라 변경될 수 있습니다. 특정 시나리오에 가장 잘 맞는 하나를 선택 해야 합니다. 자세한 내용은 [Azure Active Directory 하이브리드 id 솔루션에 대 한 올바른 인증 방법을 선택](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)합니다.

## <a name="plan-your-security-and-governance"></a>보안 및 거 버 넌 스를 계획 합니다. 

Identity 보안 주 및 투자에 대 한 새 기본 피벗 이므로 네트워크 경계가 급증 하는 BYOD 장치를 사용 하 여 점차 구멍이 줄이고 적용 했으면 및 클라우드 응용 프로그램입니다. 

### <a name="plan-access-reviews"></a>액세스 검토를 계획 합니다.

[액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) 조직이 효율적으로 그룹 멤버 자격을 관리, 엔터프라이즈 응용 프로그램 및 역할 할당에 대 한 액세스를 사용 하도록 설정 합니다. 사용자 액세스 권한이 있는 사용자만 계속 해 서 액세스할 수 있는지를 정기적으로 검토 하도록 계획 해야 합니다.

액세스 검토를 설정 하는 동안에 대 한 계획 키 항목 중 일부는 다음과 같습니다.

1. 비즈니스 요구 사항에 맞는 액세스 검토에 대 한 흐름을 식별 합니다. 월간, 연간 또는 주문형으로 연습으로이 방법을 일주일에 한 번으로 수 있습니다.

1. 앱 액세스 보고서에 대 한 검토자를 나타내는 그룹을 만듭니다. 관련자에 게 가장 널리 사용 되는 앱 및 대상 사용자 및 사용 사례에 대 한 액세스 검토의 참여자 되는지 확인 해야 합니다.

1. 액세스 검토를 완료 합니다. 더 이상 액세스 해야 하는 사용자에 게 앱 액세스 권한을 박탈 포함 되어 없습니다. 거부 된 사용자의 잠재적인 지원 요청을 처리 하는 것에 대 한 계획 합니다. 삭제 된 사용자는이 시간 동안 복원할 수 있습니다는 관리자가 필요한 경우 30 일 동안 Azure AD에서 삭제 된 상태로 유지 됩니다. 30일 후 해당 사용자가 영구적으로 삭제됩니다. Azure Active Directory 포털을 사용 전역 관리자 명시적으로 영구적으로 삭제할 수 최근에 삭제 된 사용자는 해당 기간에 도달 하기 전에 합니다.

### <a name="plan-auditing"></a>감사 계획

Azure AD에서 제공 [기술 및 비즈니스 정보를 포함 하는 보고서](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)합니다. 

보안 및 작업 보고서를 모두 사용할 수 있습니다. 보고서 보안 위험 및 위험한 로그인에 대 한 플래그가 지정 된 사용자를 보여 줍니다. 활동 보고서의 로그인 활동을 자세히 설명 하 고 모든 로그인의 감사 내역을 제공 하 여 조직 내에서 사용자의 동작을 이해 하는 데 도움이 됩니다. 위험을 관리 하 고 생산성을 높이고 규정 준수를 모니터링 하려면 보고서를 사용할 수 있습니다.

| 보고서 유형 | 액세스 검토 | 보안 보고서 | 로그인 보고서 |
|-------------|---------------|------------------|----------------|
| 검토를 사용 하 여 | 응용 프로그램 사용 권한 및 사용 합니다. | 잠재적으로 손상 된 계정 | 응용 프로그램에 액세스 하는 |
| 잠재적인 작업 | 액세스 감사 사용 권한 해지 | 액세스 해지 강제 보안 재설정 | 액세스 해지 |

Azure AD는 대부분의 감사 데이터 30 일 동안 유지 하 고는 데이터 분석 시스템으로 다운로드할 수 있는 API 또는 Azure 관리 포털을 통해 사용할 수 있도록 합니다.

### <a name="consider-using-microsoft-cloud-application-security"></a>Microsoft 클라우드 응용 프로그램 보안을 사용 하는 것이 좋습니다.

Microsoft Cloud App Security (MCAS) 클라우드 액세스 보안 브로커 (CASB) 솔루션입니다. 클라우드 앱 및 서비스에 대 한 가시성을 제공를 식별 하 여 사이버 위협에 대처 하는 정교한 분석을 제공 하 고 데이터 이동 하는 방법을 제어할 수 있습니다.

MCAS를 배포할 수 있습니다.

- Cloud Discovery를 사용 하 여 매핑 및 클라우드 환경 및 조직에서 사용 하는 클라우드 앱을 식별 합니다.
- 클라우드의 앱 사용 권한 부여 및 취소-사용 권한 부여
- 표시 유형 및 연결 하는 앱의 거 버 넌 스에 대 한 공급자 Api 활용 하는 배포 하기 쉬운 앱 커넥터를 사용 합니다.
- 조건부 액세스 앱 제어 보호 액세스 및 클라우드 앱 내에서 작업 한 실시간 가시성과 제어를 사용 하 여
- 설정 하 고 다음 지속적으로 미세 조정 정책 하 여 연속 제어 해야 하는 데 도움이 됩니다.

Microsoft 클라우드 응용 프로그램 보안 (MCAS) 세션 제어는 모든 운영 체제에서 모든 주요 플랫폼에서 모든 브라우저에 대해 사용할 수 있습니다. 모바일 앱 및 데스크톱 앱을도 차단 또는 허용 합니다. Azure ad와 통합 하는 고유 하 게 하 여 SAML을 사용 하 여 구성 된 앱 또는 Azure ad에서에서 single sign-on을 사용 하 여 앱 Open ID Connect 지원 될 수 등 [여러 추천 앱](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)합니다.

MCAS에 대 한 내용은 참조는 [Microsoft Cloud App Security 개요](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)합니다. MCAS 구독 사용자 기반 서비스입니다. 라이선스 정보를 검토할 수 있습니다 합니다 [MCAS 라이선싱 데이터 시트](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)합니다.

### <a name="use-conditional-access"></a>조건부 액세스 사용

조건부 액세스를 사용 하 여 클라우드 앱에 대 한 조건 기반 액세스 제어 결정을 자동화할 수 있습니다.

1단계 인증이 완료된 후 조건부 액세스 정책이 적용됩니다. 따라서 조건부 액세스 시나리오에 대 한 첫 번째 방어선을 서비스 거부 (DoS) 공격을 액세스 확인을 위해 이러한 이벤트에서 신호를 사용할 수 있지만으로 적절 하지 않습니다. 예를 들어 로그인 위험 수준, 요청, 및 등의 위치를 사용할 수 있습니다. 조건부 액세스에 대 한 자세한 내용은 참조 하세요. [개요](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) 하며 [배포 계획](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)합니다.

## <a name="azure-sso-technical-requirements"></a>Azure SSO 기술 요구 사항

다음 섹션에는 필요한 환경, 끝점, 클레임 매핑, 필수 특성, 인증서 및 사용 되는 프로토콜을 포함 하 여 특정 응용 프로그램을 구성 하는 요구 사항 자세히 설명 합니다. SSO를 구성 하려면이 정보가 필요 합니다 [Azure AD 포털](https://portal.azure.com/)합니다.

### <a name="authentication-mechanism-details"></a>인증 메커니즘 세부 정보

모든 사전 통합 된 SaaS 앱에 대해 Microsoft 자습서를 제공 하 고이 정보는 필요 하지 않습니다. 응용 프로그램 마켓플레이스에서 되지 않으면 응용 프로그램 / 갤러리, 데이터는 다음 정보를 수집 하도록 할 수도 있습니다.

- **해당 하는 경우 응용 프로그램에서는 SSO에 대 한 현재 id 공급자** -예를 들어: AD FS PingFederate Okta
- **대상 응용 프로그램에서 지 원하는 프로토콜** -예를 들어, SAML 2.0, OpenID Connect, OAuth, 폼 기반 인증 Ws-fed, Ws-trust
- **Azure AD를 사용 하 여 구성 되는 프로토콜** -예를 들어 SAML 2.0 또는 1.1, OpenID Connect, OAuth, 폼 기반 Ws-fed

### <a name="attribute-requirements"></a>특성 요구 사항

특성 및 특성-Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 간의 매핑 집합을 미리 구성 된 경우 일부 앱은 다른 유형의 그룹과 같은 개체 관리 합니다. 응용 프로그램에 Azure AD의 사용자 특성 매핑 계획 하 고 [기본 특성 매핑 사용자 지정](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 비즈니스에 따라 필요 합니다.

### <a name="certificate-requirements"></a>인증서 요구 사항

응용 프로그램에 대 한 인증서는, 최신 화 되어야 합니다. 또는 사용자 응용 프로그램에 액세스할 수 없는 위험이 있습니다. 대부분의 SaaS 응용 프로그램 인증서는 36 개월 동안 유용 합니다. 해당 인증서 기간 동안 응용 프로그램 블레이드에서 변경할 수 있습니다. 만료를 문서화 하 고 관리 하는 방법에 인증서 갱신을 알고 해야 합니다. 

인증서를 관리 하는 방법은 두 가지가 있습니다. 

- **자동 인증서 롤오버** -Microsoft 지원 [Azure AD의 서명 키 롤오버](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)합니다. 인증서 관리는 기본 방법 이지만, 일부 ISV는이 시나리오를 지원 합니다.

- **수동으로 업데이트** -모든 응용 프로그램에 정의 되는 방식에 따라 만료 되는 자체 인증서입니다. 응용 프로그램의 인증서의 만료 날짜 전에 새 인증서를 만들고 ISV에 게 보냅니다. 페더레이션 메타 데이터에서이 정보를 가져올 수 있습니다. [페더레이션 메타 데이터는 여기에서 자세히 알아보세요입니다.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>SSO 구현

계획 하 고 조직에서 솔루션을 배포 하려면 다음 단계를 사용 합니다.

### <a name="user-configuration-for-sso"></a>SSO에 대 한 사용자 구성

- **테스트 사용자를 식별 합니다.**

   앱 소유자에 게 문의 하 고를 최소 응용 프로그램 내에서 세 가지 테스트 사용자를 만들도록 요청 하세요. 기본 식별자로 사용 하는 정보를 올바르게 채워집니다 및 Azure AD에서 사용할 수 있는 특성을 일치를 확인 합니다. 대부분의 경우가 SAML 기반 응용 프로그램에 대 한 "NameID"에 매핑됩니다. JWT 토큰에 대 한 것이 "preferred_username"
   
   사용자를 Azure AD에서 하거나 클라우드 기반 사용자를 수동으로 만들거나 온-프레미스에서 Azure AD Connect 동기화 엔진을 사용 하 여 사용자를 동기화 합니다. 정보가 일치 되는 응용 프로그램에 전송 된 클레임을 확인 합니다.

- **SSO를 구성 합니다.**

   [응용 프로그램 목록을](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)찾기 및 응용 프로그램에 대 한 SSO 자습서를 연 다음 성공적으로 SaaS 응용 프로그램을 구성 하려면에서 자습서의 단계를 수행 합니다.

   응용 프로그램을 찾을 수 없는 경우 [사용자 지정 응용 프로그램 설명서](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)합니다. 이 안내에 Azure AD 갤러리 응용 프로그램을 추가 하는 방법에 있습니다.

   필요에 따라 사용 하 여 엔터프라이즈 응용 프로그램에 SAML 토큰에서 발급 된 클레임을 사용할 수 있습니다 [Microsoft의 지침 설명서](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)합니다. 응용 프로그램에 대 한 SAML 응답에서 수신 하려는 매핑됩니다이 있는지 확인 합니다. 구성 하는 동안 문제가 발생 하는 경우 지침을 사용 하 여 온 [디버그 SSO 통합 하는 방법을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)합니다.

사용자 지정 응용 프로그램 등록에는 Azure AD Premium P1 또는 P2 라이선스 기능입니다.

### <a name="provide-sso-change-communications-to-end-users"></a>최종 사용자에 게 SSO 변경 통신을 제공 합니다.

통신 계획을 구현 합니다. 최종 사용자는 변경을 들어오는 도착 하는 경우 이제 수행할 작업 및 지원을 요청 하는 방법을 알 수 있도록 하 고 있는지 확인 합니다.

### <a name="verify-end-user-scenarios-for-sso"></a>SSO에 대 한 최종 사용자 시나리오를 확인 하십시오.

다음 테스트 사례를 사용 하면 회사 소유의에서 테스트를 수행할 수 있습니다 및 SSO 구성 되도록 개인 장치는 예상 대로 작동 됩니다. 아래 시나리오에는 사용자의 서비스 공급자 (SP에서 시작한 인증 흐름)에 의해 시작 된 인증 흐름을 통해 응용 프로그램 URL로 이동 하 가정 합니다.

| 시나리오 | 사용자가 SP에서 시작한 인증 흐름에 예상된 결과 |
|----------|---------------------------------------------------|
| 회사 네트워크에 있는 동안 IE 사용 하 여 응용 프로그램에 로그인 합니다. | 통합된 Windows 인증 (IWA) 추가 프롬프트 없이 발생합니다. |
| 새 로그인 시도 사용 하 여 회사 네트워크에서 해제 하는 동안 IE 사용 하 여 응용 프로그램에 로그인 합니다. | 양식 기반 AD FS 서버 프롬프트입니다. 사용자가 성공적으로 로그인 하 고 MFA에 대 한 브라우저 라는 메시지가 표시 됩니다. |
| 현재 세션을 사용 하 여 회사 네트워크에서 해제 하는 동안 IE 사용 하 여 응용 프로그램에 로그인 있으며가 MFA를 수행 하지 않습니다. | 사용자는 첫 번째 요소에 대 한 프롬프트를 수신 하지 않습니다. MFA에 대 한 사용자 프롬프트를 받습니다. |
| 현재 세션을 사용 하 여 회사 네트워크에서 해제 하는 동안 IE 사용 하 여 응용 프로그램에 로그인 하 고이 세션에서 MFA를 이미 수행 했습니다. | 사용자는 첫 번째 요소에 대 한 프롬프트를 수신 하지 않습니다. 사용자가 MFA를 받지 않습니다. 응용 프로그램에 사용자 SSOs 합니다. |
| 현재 세션을 사용 하 여 회사 네트워크에서 해제 하는 동안 Safari/Chrome/Firefox를 사용 하 여 응용 프로그램에 로그인 하 고이 세션에서 MFA를 이미 수행 했습니다. | 사용자는 첫 번째 요소에 대 한 프롬프트를 수신 하지 않습니다. 사용자가 MFA를 받지 않습니다. SSO의 응용 프로그램에 사용자입니다. |
| 새 로그인 시도 사용 하 여 회사 네트워크에서 해제 하는 동안 Safari/Chrome/Firefox를 사용 하 여 응용 프로그램에 로그인 합니다. | 양식 기반 AD FS 서버 프롬프트입니다. 사용자가 성공적으로 로그인 하 고 MFA에 대 한 브라우저 라는 메시지가 표시 됩니다. |
| 현재 세션을 사용 하 여 회사 네트워크에서의 크롬/Firefox를 사용 하 여 응용 프로그램에 로그인 합니다. | 사용자는 첫 번째 요소에 대 한 프롬프트를 수신 하지 않습니다. 사용자가 MFA를 받지 않습니다. SSO의 응용 프로그램에 사용자입니다. |
| 새 로그인을 사용 하 여 모바일 앱 응용 프로그램을 사용 하 여 응용 프로그램에 로그인 합니다. | 양식 기반 AD FS 서버 프롬프트입니다. 사용자가 성공적으로 로그인 하 고 MFA에 대 한 ADAL 클라이언트 라는 메시지가 표시 됩니다. |
| 권한이 없는 사용자는 로그인 URL 사용 하 여 응용 프로그램에 로그인 하려고 합니다. | 양식 기반 AD FS 서버 프롬프트입니다. 사용자는 첫 번째 요소를 사용 하 여 로그인 하는 데 실패 합니다. |
| 권한 있는 사용자 로그인을 시도 하지만 잘못 된 암호를 입력 합니다. | 사용자가 응용 프로그램 URL로 이동 하 고 잘못 된 사용자 이름/암호 오류를 받습니다. |
| 권한 있는 사용자가 전자 메일의 링크를 클릭 하 고 인증 이미 됩니다. | 사용자는 URL에서 클릭 하 고 추가 프롬프트 없이 응용 프로그램에 로그인 됩니다. |
| 권한 있는 사용자는 전자 메일의 링크를 클릭할 및 아직 인증 되지 않습니다. | 사용자는 URL에서 클릭 하 고 첫 번째 요소를 사용 하 여 인증 하 라는 메시지가 표시 됩니다. |
| 사용자가 모바일 앱 응용 프로그램을 사용 하 여 응용 프로그램에 권한이 부여 된 (SP에서 시작한) 새 로그인을 사용 하 여 합니다. | 양식 기반 AD FS 서버 프롬프트입니다. 사용자가 성공적으로 로그인 하 고 MFA에 대 한 ADAL 클라이언트 라는 메시지가 표시 됩니다. |
| 권한이 없는 사용자는 로그인 URL (SP에서 시작한)를 사용 하 여 응용 프로그램에 로그인 하려고 합니다. | 양식 기반 AD FS 서버 프롬프트입니다. 사용자는 첫 번째 요소를 사용 하 여 로그인 하는 데 실패 합니다. |
| 권한 있는 사용자 로그인을 시도 하지만 잘못 된 암호를 입력 합니다.| 사용자가 응용 프로그램 URL로 이동 하 고 잘못 된 사용자 이름/암호 오류를 받습니다. |
| 권한 있는 사용자를 로그 아웃 하 고 다시 로그인 합니다. | 로그 아웃 URL을 구성 하는 경우 사용자는 모든 서비스 및 인증 하 라는 메시지가 기록 됩니다. |
| 권한 있는 사용자를 로그 아웃 하 고 다시 로그인 합니다. | 로그 아웃 URL을 구성 하지 않은 경우 사용자는 기존 Azure AD 브라우저 세션에서 기존 토큰을 사용 하 여 년대 자동으로 로깅됩니다. |
| 권한 있는 사용자가 전자 메일의 링크를 클릭 하 고 인증 이미 됩니다. | 사용자는 URL에서 클릭 하 고 추가 프롬프트 없이 응용 프로그램에 로그인 됩니다. |
| 권한 있는 사용자는 전자 메일의 링크를 클릭할 및 아직 인증 되지 않습니다. | 사용자는 URL에서 클릭 하 고 첫 번째 요소를 사용 하 여 인증 하 라는 메시지가 표시 됩니다. |

## <a name="manage-sso"></a>SSO 관리

이 섹션에서는 요구 사항 및 SSO를 성공적으로 관리 되는 권장 사항을 간략하게 설명 합니다.

### <a name="required-administrative-roles"></a>필요한 관리 역할

항상 Azure Active Directory 내에서 필요한 작업을 수행 하는 사용 가능한 가장 적은 권한을 가진 역할을 사용 합니다. 것이 좋습니다 [사용할 수 있는 다양 한 역할 검토](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 이 응용 프로그램에 대 한 각 가상 사용자의 요구를 해결 하기 위해 적합 한 선택입니다. 일부 역할을 일시적으로 적용 되며 배포 완료 된 후 제거 해야 합니다.

| 가상 사용자| 역할 | Azure AD 역할 (필요한 경우) |
|--------|-------|-----------------------------|
| 기술 지원팀 관리자 도움말 | 계층 1 지원 | 없음 |
| Identity 관리자 | 구성 하 고 Azure AD 문제에 영향을 줄 때 디버깅 | 글로벌 관리자 |
| 응용 프로그램 관리자 | 응용 프로그램에서 사용 권한이 있는 사용자의 구성 사용자 증명 | 없음 |
| 인프라 관리자 | 인증서 롤오버 소유자 | 글로벌 관리자 |
| 비즈니스 소유자/관련자 | 응용 프로그램에서 사용 권한이 있는 사용자의 구성 사용자 증명 | 없음 |

사용 하는 것이 좋습니다 [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) 디렉터리 권한이 있는 사용자에 대 한 추가 감사, 제어 및 액세스 검토를 제공 하 여 역할 관리 (PIM).

### <a name="sso-certificate-lifecycle-management"></a>SSO 인증서 수명 주기 관리

적절 한 역할을 식별 하 고 Azure AD 간의 서명 인증서의 수명 주기 관리를 전담 하는 메일 그룹 메일을 고려해 야 하 고 응용 프로그램에서 single sign-on을 사용 하 여 구성 중인 합니다. 다음은 일부 핵심 역할 진행에서 것이 좋습니다.

- 응용 프로그램에서 사용자 속성을 업데이트 하는 것에 대 한 소유자
- 소유자 전화 고장/수리 지원 응용 프로그램에 대 한
- 인증서 관련 변경 알림에 대 한 밀접 하 게 모니터링 된 전자 메일 배포 목록

인증서의 최대 수명은 3 년입니다. Azure AD 간의 인증서 변경을 처리 하는 방법의 프로세스를 설정 하는 것이 좋습니다 및 응용 프로그램입니다. 이 방지 또는 인증서 만료로 인해 가동 중단을 최소화 하거나 인증서 롤오버를 강제로 도움이 됩니다.

### <a name="rollback-process"></a>롤백 프로세스

테스트 사례를 기반으로 테스트를 완료 한 후 응용 프로그램을 사용 하 여 프로덕션으로 이동할 수 차례입니다. 프로덕션 테 넌 트의 계획 및 테스트 구성에 구현 하 고 사용자에 게 롤아웃하는 의미 프로덕션으로 전환 합니다. 그러나 배포 계획으로 이동 하지 않을 경우 수행할 작업을 계획 하는 것이 반드시 합니다. SSO 구성 배포 중에 실패 하면 가동 중단이 발생을 완화 하 고 사용자에 게 영향을 감소 하는 방법을 알아야 합니다.

응용 프로그램 내에서 인증 방법의 가용성에 가장 적합 한 전략을 결정 합니다. 항상 정확 하 게 가져오는 방법은 다시 원래 로그인 구성 상태로 배포 문제를 실행 하는 경우 앱 소유자에 대 한 설명서 세부 있습니다 확인 합니다.

- **앱에서 여러 id 공급자를 지 원하는 경우**, 예를 LDAP와 AD FS, Ping 출시 중에 기존 SSO 구성을 삭제 하지 마세요. 대신 불가능 하 게 마이그레이션하는 동안 다시 전환할 나중에 필요한 경우. 

- **앱에서 여러 Idp를 지원 하지 않으면** 하지만 폼 기반 인증 (사용자 이름/암호)를 사용 하 여 로그인 하는 사용자를 위해 대체할 수이 이렇게 새 SSO 구성 출시 실패 하는 경우 확인을 허용 합니다.

### <a name="access-management"></a>액세스 관리

리소스에 대 한 액세스를 관리 하는 경우 크기 조정 된 방법을 선택 하는 것이 좋습니다. Azure AD Connect를 통해 동기화 하 여 온-프레미스 그룹을 활용 하 여 일반적인 접근 방식을 포함 [사용자 특성에 따라 Azure AD의 동적 그룹을 만드는](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)를 만들거나 [셀프 서비스 그룹](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) Azure AD에서 리소스 소유자가 관리합니다.

### <a name="monitor-security"></a>보안을 모니터링

권장 SaaS 앱 보안의 다양 한 측면을 검토 하는 일반 작업 리듬을 설정 하 고 필요한 모든 수정 작업을 수행 합니다.

### <a name="troubleshooting"></a>문제 해결

다음 링크를 문제 해결 시나리오를 제공합니다. 이러한 시나리오 및 해결 하는 단계를 통합 하는 지원 담당자에 대 한 특정 지침을 만들 수도 있습니다.

#### <a name="consent-issues"></a>동의 문제

- [예기치 않은 동의 오류](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [사용자 동의 오류](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>로그인 문제

- [사용자 지정 포털에서 로그인 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [액세스 패널에서 로그인 시 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [애플리케이션 로그인 페이지에서의 오류](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Microsoft 응용 프로그램에 로그인 하는 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Azure 응용 프로그램 갤러리에 나열 된 응용 프로그램에 대 한 SSO 문제

- [Azure 응용 프로그램 갤러리에 응용 프로그램에 대 한 암호 SSO 사용 하 여 문제 나열](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Azure 응용 프로그램 갤러리에 나열 된 응용 프로그램에 대해 페더레이션된 SSO에 문제가 있습니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Azure 응용 프로그램 갤러리에 나열 되지 않은 응용 프로그램에 대 한 SSO 문제

- [Azure 응용 프로그램 갤러리에 나열 되지 않은 응용 프로그램에 대 한 암호 SSO 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Azure 응용 프로그램 갤러리에 나열 되지 않은 응용 프로그램에 대해 페더레이션된 SSO에 문제가 있습니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>다음 단계

[SAML 기반 SSO 디버그](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[PowerShell 통해 앱에 대 한 클레임 매핑](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[SAML 토큰에서 발급 된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Single Sign on SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Single Sign-Out SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (외부 사용자 용 파트너 및 공급 업체 등)

[Azure AD 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Id 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[응용 프로그램 SSO 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
