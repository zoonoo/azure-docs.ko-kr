---
title: 새로운 기능은 무엇입니까? 릴리스 정보 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능, 예정된 변경 내용 등을 알아봅니다.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab14df388579f46d9467c9d4d68146450691270c
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108180395"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능

>이 URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us`를 ![RSS 피드 판독기 아이콘](./media/whats-new/feed-icon-16x16.png) 피드 판독기에 복사하고 붙어넣어 업데이트를 위해 이 페이지를 다시 방문해야 할 때 알림을 받습니다.

Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월이 넘은 항목을 찾으려는 경우 [Azure Active Directory의 새로운 기능 아카이브](whats-new-archive.md)에서 찾을 수 있습니다.

---

## <a name="march-2021"></a>2021년 3월

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>예정된 Azure AD TLS 1.0/1.1 사용 중단을 준비하기 위해 사용자 환경에서 TLS 1.2 지원을 사용하는 방법에 관한 참고 자료

**유형:** 변경 계획  
**서비스 범주:** 해당 없음  
**제품 기능:** 기준

Azure Active Directory는 2021년 6월 30일부터 전 세계에서 다음 Azure Active Directory 프로토콜을 사용 중단합니다.


- TLS 1.0
- TLS 1.1
- 3DES 암호화 그룹(TLS_RSA_WITH_3DES_EDE_CBC_SHA)

영향을 받는 환경은 다음과 같습니다.

- Azure 상용 클라우드
- Office 365 GCC 및 WW

자세한 참고 자료는 [Azure AD TLS 1.1 및 1.0 지원 중단을 위해 사용자 환경에서 TLS 1.2 지원 사용](/troubleshoot/azure/active-directory/enable-support-tls-environment)을 참고하세요.

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>퍼블릭 미리 보기 - Azure AD 권한 관리는 이제 다중 지역 SharePoint Online을 지원합니다.

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 권한 관리
 
다중 지역 SharePoint Online을 사용하는 조직의 경우 이제 특정 다중 지역 환경의 사이트를 권한 관리 액세스 패키지에 포함할 수 있습니다. [자세한 정보를 알아보세요](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview).

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>퍼블릭 미리 보기 - 앱 등록에서 삭제된 앱 복원

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 개발자 환경
 
이제 고객은 Azure Portal에서 삭제된 앱 등록을 보고, 복원하고, 영구 제거할 수 있습니다. 이는 개인 Microsoft 계정의 애플리케이션이 아니라 디렉터리에 연결된 애플리케이션에만 적용됩니다. [자세한 정보를 알아보세요](../develop/howto-restore-app.md).
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>퍼블릭 미리 보기 - 디바이스를 등록하거나 조인하기 위한 조건부 액세스에서의 새로운 "사용자 작업"

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호
 
 조건부 액세스에서 "디바이스 등록 또는 조인"이라는 새로운 사용자 작업을 사용할 수 있습니다. 이 사용자 작업을 통해 Azure AD 디바이스 등록을 위한 MFA(다단계 인증) 정책을 제어할 수 있습니다. 

현재 이 사용자 작업에서는 사용자가 Azure AD에 디바이스를 등록하거나 조인하는 경우 MFA를 시각적 개체로 사용하는 것만 가능합니다. Azure AD 디바이스 등록에 종속되거나 적용되지 않는 다른 시각적 개체는 해당 사용자 작업에는 사용할 수 없습니다. [자세한 정보를 알아보세요](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>퍼블릭 미리 보기 - 가장 가까운 애플리케이션 프록시 클라우드 서비스를 사용하도록 커넥터 그룹 최적화

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어
 
이 새로운 기능을 사용하면 애플리케이션이 호스트되는 가장 가까운 지역 애플리케이션 프록시 서비스에 커넥터 그룹을 할당할 수 있습니다. 이렇게 하면 홈 테넌트의 지역이 아닌, 앱이 지역에서 호스팅되는 시나리오에서 앱 성능을 향상할 수 있습니다. [자세한 정보를 알아보세요](../app-proxy/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>퍼블릭 미리 보기 - 메일 일회용 암호 계정을 사용하여 AAD에서 External Identities 셀프 서비스 가입

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C

외부 사용자는 이제 메일 일회용 암호 계정을 사용하여 Azure AD의 자사 앱 및 LOB 앱에 가입할 수 있습니다. [자세한 정보를 알아보세요](../external-identities/one-time-passcode.md).

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>퍼블릭 미리 보기 - Azure AD에서 AD FS 로그인의 가용성

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 모니터링 및 보고
 
이제 AD FS 로그인 작업을 Azure AD 작업 보고와 통합하여 하이브리드 ID 인프라의 통합 보기를 제공할 수 있습니다. Azure AD 로그인 보고서, Log Analytics 및 Azure Monitor Workbooks를 사용하면 AD FS 계정 잠금, 잘못된 암호 시도, 예기치 않은 로그인 시도 급증 등 AAD 및 AD FS 로그인 시나리오에 관한 심층 분석을 수행할 수 있습니다.

자세한 내용은 [Connect Health를 사용하여 Azure AD에서 AD FS 로그인](../hybrid/how-to-connect-health-ad-fs-sign-in.md)을 참조하세요.

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>일반 공급 - 클라우드 인증을 위한 단계적 롤아웃

**유형:** 새로운 기능  
**서비스 범주:** AD Connect  
**제품 기능:** 사용자 인증
 
클라우드 인증을 위한 단계적 롤아웃이 이제 일반 공급됩니다. 단계적 롤아웃 기능을 사용하면 PTA(통과 인증) 또는 PHA(암호 해시 동기화)와 같은 클라우드 인증 방법을 사용하여 사용자 그룹을 선택적으로 테스트할 수 있습니다. 한편 페더레이션된 도메인의 다른 모든 사용자는 AD FS 또는 다른 페더레이션 서비스와 같은 페더레이션 서비스를 계속 사용하여 사용자를 인증합니다. [자세한 정보를 알아보세요](../hybrid/how-to-connect-staged-rollout.md).

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>일반 공급 - 이제 Azure 관리 포털에서 사용자 형식 특성을 업데이트할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 사용자 환경 및 관리  
**제품 기능:** 사용자 관리
 
이제 고객은 Azure 관리 포털에서 사용자 프로필 정보를 업데이트할 때 Azure AD 사용자의 사용자 형식을 업데이트할 수 있습니다. Microsoft Graph에서 사용자 형식을 업데이트할 수도 있습니다. 자세히 알아보려면 [사용자 프로필 정보 추가 또는 업데이트](active-directory-users-profile-azure-portal.md)를 참조하세요.
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>일반 공급 - Azure Active Directory Domain Services의 복제본 세트

**유형:** 새로운 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services
 
Azure AD DS의 복제본 세트 기능이 이제 일반 공급됩니다. [자세한 정보를 알아보세요](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>일반 공급 - Azure Government 클라우드의 메일 일회용 암호를 사용하여 파트너와 공동 작업

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
이제 Microsoft Azure Government 클라우드의 조직은 게스트가 메일 일회용 암호를 통해 초대를 사용할 수 있게 설정할 수 있습니다. 이렇게 하면 Azure Government 클라우드에서 Azure AD, Microsoft 또는 Gmail 계정이 없는 모든 게스트 사용자는 공유 리소스에 로그인할 임시 코드를 요청하고 입력하여 파트너와 공동 작업을 수행할 수 있습니다. [자세한 정보를 알아보세요](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2021년 3월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2021년 3월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 37개의 새 앱을 추가했습니다.

[Bambuser Live Video Shopping](https://lcx.bambuser.com/), [DeepDyve Inc](https://www.deepdyve.com/azure-sso), [Moqups](../saas-apps/moqups-tutorial.md), [RICOH Spaces Mobile](https://ricohspaces.app/welcome), [Flipgrid](https://auth.flipgrid.com/), [hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md), [SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md), [SimplificaCI](https://app.simplificaci.com.br/), [Thrive LXP](../saas-apps/thrive-lxp-tutorial.md), [Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md), [Exium](../saas-apps/exium-tutorial.md), [Sapient](../saas-apps/sapient-tutorial.md), [TrueChoice](../saas-apps/truechoice-tutorial.md), [RICOH Spaces](https://ricohspaces.app/welcome), [Saba Cloud](../saas-apps/learning-at-work-tutorial.md), [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md), [Enterprise Vault.cloud for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook](../saas-apps/smartlook-tutorial.md), [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape](../saas-apps/onshape-tutorial.md), [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox](../saas-apps/juriblox-tutorial.md), [SecurityStudio](../saas-apps/securitystudio-tutorial.md), [ClicData](https://app.clicdata.com/), [Evergreen](../saas-apps/evergreen-tutorial.md), [Patchdeck](https://patchdeck.com/ad_auth/authenticate/), [FAX.PLUS](../saas-apps/fax.plus-tutorial.md), [ValidSign](../saas-apps/validsign-tutorial.md), [AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md), [Nura Space](https://dashboard.nuraspace.com/login), [Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md), [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

여기(https://aka.ms/AppsTutorial )에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다.

Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보를 참조하세요. https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Azure AD 앱 갤러리의 새로운 프로비저닝 커넥터 - 2021년 3월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합

다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [AWS Single Sign-on](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>회사 브랜딩용 MS Graph API 소개

**유형:** 변경된 기능  
**서비스 범주:** MS Graph  
**제품 기능:** B2B/B2C

[회사 브랜딩용 MS Graph API](/graph/api/resources/organizationalbrandingproperties)는 Azure AD 또는 Microsoft 365 로그인 환경에서 사용할 수 있으며 프로그래밍 방식으로 브랜딩 매개 변수를 관리할 수 있습니다.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>일반 공급 - 애플리케이션 프록시를 사용하는 헤더 기반 인증 SSO

**유형:** 변경된 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어
 
헤더 기반 인증을 위한 Azure AD 애플리케이션 프록시 기본 지원이 이제 일반 공급됩니다. 이 기능을 사용하면 배포하는 데 필요한 추가 구성 요소 없이 필요한 사용자 특성을 애플리케이션의 HTTP 헤더로 구성할 수 있습니다. [자세한 정보를 알아보세요](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>MFA 서버용 양방향 SMS는 이제 지원되지 않습니다.

**유형:** 사용되지 않음  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호
 

MFA 서버용 양방향 SMS는 2018년부터 사용되지 않으며, 2021년 2월 24일 이후에는 지원되지 않습니다. 관리자는 아직도 양방향 SMS를 사용하는 사용자들이 다른 방법을 사용하게 합니다.

2020년 12월 8일 및 2021년 1월 28일에 영향을 받는 관리자에게 메일 알림 및 Azure Portal Service Health 알림이 전송되었습니다. 이 경고는 구독에 연결된 소유자, 공동소유자, 관리자, 서비스 관리자 RBAC 역할에 전달되었습니다. [자세한 정보를 알아보세요](../authentication/how-to-authentication-two-way-sms-unsupported.md).
 
---
 
## <a name="february-2021"></a>2021년 2월

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>2021년 10월부터 기본적으로 일회용 암호 인증을 메일로 전송

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

2021년 10월 31일부터 Microsoft Azure Active Directory [메일 일회용 암호 인증](../external-identities/one-time-passcode.md)은 B2B 협업 시나리오를 위한 계정 및 테넌트를 초대하는 기본 방법이 됩니다. 현재 Microsoft는 관리되지 않는 Azure Active Directory 계정을 사용한 초대의 사용을 허용하지 않습니다. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>조건부 액세스를 실행하는 경우 요청되지 않았지만 동의한 권한이 토큰에 더 추가되지 않습니다.

**유형:** 변경 계획  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 플랫폼
 
현재 [동적 권한](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)을 사용하는 애플리케이션에는 동의한 모든 액세스 권한이 부여됩니다. 여기에는 사용자가 조건부 액세스를 실행하는 경우에도 요청되지 않은 애플리케이션이 포함됩니다. 예를 들어, 이로 인해 `user.read`만 요청하였지만 `files.read`에도 동의하는 앱이 `files.read` 사용 권한에 할당된 조건부 액세스를 강제로 통과하게 할 수 있습니다. 

불필요한 조건부 액세스 프롬프트 수를 줄이기 위해 Azure AD는 애플리케이션에 요청되지 않은 범위가 제공되는 방식을 변경합니다. 앱은 명시적으로 요청하는 권한에 대한 조건부 액세스만 트리거합니다. 자세한 내용은 [인증의 새로운 기능](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)을 참조하세요.
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>퍼블릭 미리 보기 - 임시 액세스 패스를 사용하여 암호 없는 자격 증명 등록

**유형:** 새로운 기능  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호

임시 액세스 패스는 강력한 자격 증명으로 사용되며, 사용자가 강력한 인증 요소(예: FIDO2 보안 키 또는 Microsoft Authenticator) 앱을 분실하거나 잊은 경우 암호 없는 자격 증명과 복구를 온보딩하고, 새 강력한 인증 방법을 등록하기 위해 로그인해야 하는 시간제한 암호입니다. [자세한 정보를 알아보세요](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>퍼블릭 미리 보기 - 차세대 사용자 흐름에서 KMSI(로그인 유지)

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 차세대 B2C 사용자 흐름은 고객이 영구 쿠키를 사용하여 웹 애플리케이션 및 네이티브 애플리케이션의 사용자에게 세션 수명을 연장할 수 있는 [KMSI(로그인 유지)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) 기능을 지원합니다.  기능은 사용자가 브라우저를 닫았다가 다시 열 때도 세션을 활성 상태로 유지하고, 사용자가 로그아웃할 때 해지됩니다.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>퍼블릭 미리 보기 - MSA 계정을 사용하여 AAD에서 External Identities 셀프 서비스 가입

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
외부 사용자는 이제 Microsoft 계정을 사용하여 Azure AD 자사 앱 및 LOB 앱에 로그인할 수 있습니다. [자세한 정보를 알아보세요](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>퍼블릭 미리 보기 - 게스트 사용자의 사용 상태 초기화

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
고객은 이제 기존 외부 게스트 사용자를 다시 초대하여 사용 상태를 초기화할 수 있습니다. 그러면 게스트 사용자 계정이 액세스를 잃지 않고 남아 있을 수 있습니다. [자세한 정보를 알아보세요](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>퍼블릭 미리 보기 - /동기화(프로비저닝) API는 이제 애플리케이션 권한을 지원합니다.

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 수명 주기 관리
 
이제 고객은 application.readwrite.ownedby를 애플리케이션 권한으로 사용하여 동기화 API를 호출할 수 있습니다. 참고로 이 기능은 Azure AD에서 타사 애플리케이션(예: AWS, Data Brick 등)으로 프로비저닝하는 경우에만 지원됩니다. 현재 HR-프로비저닝(Workday / Successfactors) 또는 클라우드 동기화(AD에서 Azure AD로)에는 지원되지 않습니다. [자세한 정보를 알아보세요](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>일반 공급 - 인증 정책 관리자 기본 제공 역할

**유형:** 새로운 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
이 역할을 가진 사용자는 인증 방법 정책, 테넌트 전체 MFA 설정, 암호 보호 정책을 구성할 수 있습니다. 이 역할은 암호 보호 설정을 관리할 수 있는 권한( 스마트 잠금 구성 및 사용자 지정 금지 암호 목록 업데이트)을 부여합니다. [자세한 정보를 알아보세요](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>일반 공급 - 내 앱의 사용자 컬렉션을 지금 사용할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** 최종 사용자 환경
 
이제 사용자는 내 앱의 앱 시작 관리자에서 자신의 앱 그룹을 만들 수 있습니다. 또한 관리자가 공유하는 컬렉션을 다시 정렬하고 숨길 수 있습니다. [자세한 정보를 알아보세요](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>일반 공급 - Authenticator에서 자동 채우기

**유형:** 새로운 기능  
**서비스 범주:** Microsoft Authenticator 앱  
**제품 기능:** ID 보안 및 보호
 
Microsoft Authenticator는 MFA(다단계 인증) 및 계정 관리 기능을 제공하며, 사용자가 모바일(iOS 및 Android)에서 방문하는 사이트 및 앱의 암호를 자동으로 자동 채우기도 합니다. 

Authenticator에서 자동 채우기를 사용하려면 사용자가 Authenticator에 개인 Microsoft 계정을 추가하고 이를 통해 암호를 동기화해야 합니다. 지금은 회사 계정 또는 학교 계정을 사용하여 암호를 동기화할 수 없습니다. [자세한 정보를 알아보세요](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>일반 공급 - 내부 사용자를 B2B 협업에 초대

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
고객은 이제 기존 내부 계정에 초대를 보내는 대신 내부 게스트를 초대하여 B2B 협업을 사용할 수 있습니다. 이를 통해 고객은 해당 사용자의 개체 ID, UPN, 그룹 멤버 자격, 앱 할당을 유지할 수 있습니다. [자세한 정보를 알아보세요](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>일반 공급 - 도메인 이름 관리자 기본 제공 역할

**유형:** 새로운 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
이 역할을 가진 사용자는 도메인 이름을 관리(읽기, 추가, 확인, 업데이트, 삭제)할 수 있습니다. 해당 개체에는 도메인 종속성이 있어서 사용자, 그룹, 애플리케이션에 관한 디렉터리 정보를 읽을 수도 있습니다. 

온-프레미스 환경의 경우, 해당 역할을 가진 사용자는 연결된 사용자가 항상 온-프레미스에서 인증되도록 페더레이션을 위한 도메인 이름을 구성할 수 있습니다. 해당 사용자는 Single Sign-On을 통해 온-프레미스 암호를 사용하여 Azure AD 기반 서비스에 로그인할 수 있습니다. 페더레이션 설정은 Azure AD Connect를 통해 동기화해야 하므로 사용자는 Azure AD Connect를 관리할 수 있는 권한도 있습니다. [자세한 정보를 알아보세요](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱 - 2021년 2월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2021년 2월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 37개의 새 앱을 추가했습니다.

[Loop Messenger Extension](https://loopworks.com/loop-flow-messenger/), [Silverfort Azure AD Adapter](http://www.silverfort.com/), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [introDus Pre- and Onboarding Platform](http://app.introdus.dk/login), [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), [Fenwick Gold Account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & TMS](http://www.learnsoft.com/), [P-TH+](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IoT Bridge](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [Rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot - Car park management](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), [Samsung Knox 및 비즈니스 서비스](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis- Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician](../saas-apps/maptician-tutorial.md), [Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md), [Sigma Computing](../saas-apps/sigma-computing-tutorial.md), [CloudKnox 권한 관리 플랫폼](../saas-apps/cloudknox-permissions-management-platform-tutorial.md), [Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md), [Enablon](../saas-apps/enablon-tutorial.md)

여기(https://aka.ms/AppsTutorial )에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다.

Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보를 참조하세요. https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Azure AD 앱 갤러리의 새로운 프로비저닝 커넥터 - 2021년 2월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 

다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝](../app-provisioning/user-provisioning.md)을 참조하세요.

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>일반 공급 - 10 Azure Active Directory 역할이 이름 변경됨

**유형:** 변경된 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
10 Azure AD 기본 제공 역할의 이름이 변경되어 [Microsoft 365 관리 센터](/microsoft-365/admin/microsoft-365-admin-center-preview), [Azure AD 포털](https://portal.azure.com/), [Microsoft Graph](https://developer.microsoft.com/graph/)에 정렬되었습니다. 새 역할에 관한 자세한 내용은 [Azure Active Directory의 관리자 역할](../roles/permissions-reference.md#all-roles)을 참조하세요.

![MS Graph API의 역할 이름과 Azure Portal 그리고 API, Azure Portal, Mac에서 제안된 최종 이름을 보여 주는 표입니다.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>MFA/SSPR 결합 등록의 새로운 회사 브랜딩

**유형:** 변경된 기능  
**서비스 범주:** 사용자 환경 및 관리  
**제품 기능:** 최종 사용자 환경
 
이전에는 회사 로고가 Azure Active Directory 로그인 페이지에서 사용되지 않았습니다. 이제 회사 브랜딩은 MFA/SSPR 결합 등록의 왼쪽 위에 있습니다. 회사 브랜딩도 내 로그인 및 보안 정보 페이지에 포함되어 있습니다. [자세한 정보를 알아보세요](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>일반 공급 - 두 번째 수준 관리자를 대체 승인자로 설정할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 사용자 액세스 관리  
**제품 기능:** 권한 관리
 
이제 승인자를 선택할 때 추가 옵션을 권한 관리에서 사용할 수 있습니다. 첫 번째 승인자를 "승인자로서 관리자"로 선택하는 경우, 대체 승인자 필드에서 선택할 수 있는 "두 번째 수준 관리자를 대체 승인자로"를 또 다른 옵션으로 가지게 됩니다. 이 옵션을 선택하는 경우 시스템에서 두 번째 수준 관리자를 찾을 수 없으면 요청을 전달할 대체 승인자를 추가해야 합니다. [자세한 정보를 알아보세요](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>인증 방법 작업 대시보드

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 

새로 고쳐진 인증 방법 작업 대시보드는 관리자에게 테넌트의 인증 방법 등록 및 사용 작업에 관한 개요를 제공합니다. 이 보고서는 각 방법의 등록된 사용자 수를 요약하고 로그인 및 암호를 다시 설정할 때 사용하는 방법도 요약합니다. [자세한 정보를 알아보세요](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>CTL(구성 가능한 토큰 수명)의 새로 고침 및 세션 토큰 수명 구성이 사용 중지됨

**유형:** 사용되지 않음  
**서비스 범주:** 기타  
**제품 기능:** 사용자 인증
 
CTL에서 새로 고침 및 세션 수명 구성이 사용 중지되었습니다. Azure Acti0ve Directory는 기존 정책에서 새로 고침 및 세션 토큰 구성을 더 적용하지 않습니다. [자세한 정보를 알아보세요](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---
 
## <a name="january-2021"></a>2021년 1월

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>프로비저닝을 구성할 때 비밀 토큰은 필수 필드입니다.

**유형:** 변경 계획  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 수명 주기 관리

이전에는 사용자 지정/BYOA 애플리케이션에서 프로비저닝을 설정할 때 비밀 토큰 필드를 비워둘 수 있었습니다. 이 함수는 테스트용으로만 사용하기 위한 것입니다. UI를 업데이트하여 필수 필드로 만듭니다. 

고객은 브라우저 URL에 기능 플래그를 사용하여 테스트 목적으로 요구 사항을 해결할 수 있습니다. [자세한 정보를 알아보세요](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>퍼블릭 미리 보기 - 최전방 직원을 위한 대규모 Android 공유 디바이스 사용자 지정 및 구성

**유형:** 새로운 기능  
**서비스 범주:** 디바이스 등록 및 관리  
**제품 기능:** ID 보안 및 보호
 
Azure AD 및 Microsoft Endpoint Manager를 결합하여 최전방 직원 디바이스를 사용자 지정하고, 크기를 조정하고, 보호하는 기능을 제공합니다.

다음 미리 보기 기능을 사용하여 다음을 수행할 수 있습니다.
- Microsoft Endpoint Manager를 사용하여 대규모로 Android 공유 디바이스 프로비저닝
- 디바이스 기반 조건부 액세스를 사용하여 교대 작업자에 대한 액세스 보호
- 관리형 홈 화면을 사용하여 교대 작업자의 로그인 환경 사용자 지정

자세히 알아보려면 [최전방 직원을 위한 대규모 공유 디바이스 사용자 지정 및 구성](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)을 참조하세요.

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>퍼블릭 미리 보기 - 이제 프로비저닝 로그를 CSV 또는 JSON으로 다운로드할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 수명 주기 관리

고객은 UI 및 그래프 API를 통해 프로비저닝 로그를 CSV 파일 또는 JSON 파일로 다운로드할 수 있습니다. 자세히 알아보려면 [Azure Active Directory 포털에서 보고서 프로비저닝](../reports-monitoring/concept-provisioning-logs.md)을 참조하세요.

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>퍼블릭 미리 보기 - Azure AD 사용자 지정 역할 및 관리 단위 범위 역할에 클라우드 그룹 할당

**유형:** 새로운 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
고객은 Azure AD 사용자 지정 역할 또는 관리 단위 범위가 지정된 역할에 클라우드 그룹을 할당할 수 있습니다. 이 기능을 사용하는 방법을 알아보려면 [클라우드 그룹을 사용하여 Azure Active Directory에서 역할 할당 관리](../roles/groups-concept.md)를 참조하세요.

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>일반 공급 - Azure AD Connect 클라우드 동기화(이전에는 클라우드 프로비저닝이라고 함)

**유형:** 새로운 기능  
**서비스 범주:** Azure AD Connect 클라우드 동기화  
**제품 기능:** ID 수명 주기 관리
 
Azure AD Connect 클라우드 동기화는 이제 모든 고객에게 일반 공급됩니다.

Azure AD Connect 클라우드는 대량 변환 논리를 클라우드로 이동하여 온-프레미스 공간을 줄입니다. 또한 여러 개의 경량 에이전트 배포를 사용할 수 있어 동기화 가용성이 개선됩니다. [자세한 정보를 알아보세요](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>일반 공급 - 공격 시뮬레이션 관리자 및 공격 페이로드 작성자 기본 제공 역할

**유형:** 새로운 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
역할 기반 액세스 제어의 새로운 두 역할은 사용자, 공격 시뮬레이션 관리자, 공격 페이로드 작성자에게 할당하는 데 사용할 수 있습니다. 

[공격 시뮬레이션 관리자](../roles/permissions-reference.md#attack-simulation-administrator) 역할의 사용자는 테넌트의 모든 시뮬레이션에 대한 액세스 권한을 가지며 다음 작업을 수행할 수 있습니다.
- 공격 시뮬레이션 생성의 모든 측면을 만들고 관리합니다.
- 시뮬레이션을 시작/예약합니다.
-  시뮬레이션 결과를 검토합니다. 

[공격 페이로드 작성자](../roles/permissions-reference.md#attack-payload-author) 역할의 사용자는 공격 페이로드를 만들 수 있지만 실제로 시작하거나 예약할 수는 없습니다. 그러면 테넌트의 모든 관리자가 공격 페이로드를 사용하여 시뮬레이션을 만들 수 있습니다.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>일반 공급 - 사용 요약 보고서 구독자 기본 제공 역할

**유형:** 새로운 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
사용 요약 보고서 구독자 역할을 가진 사용자는 사용 및 생산성 점수를 위한 Microsoft 365 관리 센터에서 테넌트 수준 집계 데이터 및 관련 인사이트에 액세스할 수 있습니다. 그러나 사용자 수준 세부 정보 또는 인사이트에는 액세스할 수 없습니다. 

두 보고서를 위한 Microsoft 365 관리 센터에서는 테넌트 수준 집계 데이터와 사용자 수준 세부 정보가 구분되어 있습니다. 이 역할은 개별 사용자 식별 가능 데이터에 추가 보호 계층을 제공합니다. [자세한 정보를 알아보세요](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>일반 공급 - Azure AD 조건부 액세스에 앱 보호 정책 권한 부여 필요

**형식:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호
 
"앱 보호 정책 필요"를 위한 Azure AD 조건부 액세스 권한 부여는 이제 GA입니다. 

정책은 다음과 같은 기능을 제공합니다.
- Intune 앱 보호를 지원하는 모바일 애플리케이션을 사용하는 경우에만 액세스 허용
- 사용자가 모바일 애플리케이션에 제공된 Intune 앱 보호 정책을 보유한 경우에만 액세스를 허용합니다.

앱 보호를 위해 조건부 액세스 정책을 설정하는 방법에 관한 자세한 내용은 [여기](../conditional-access/app-protection-based-conditional-access.md)를 참조하세요.
 
---

### <a name="general-availability---email-one-time-passcode"></a>일반 공급 - 메일 일회용 암호

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
메일 OTP를 사용하면 전 세계의 조직이 메일을 통해 링크나 초대를 전송하여 누구와도 협업할 수 있습니다. 초대된 사용자는 파트너의 리소스에 액세스하기 위해 메일에 전송된 일회용 암호를 사용하여 ID를 확인할 수 있습니다. [자세한 정보를 알아보세요](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Azure AD 앱 갤러리의 새로운 프로비저닝 커넥터 - 2021년 1월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

자세한 내용은 [Azure AD에서 자동화된 SaaS 앱 사용자 프로비저닝이란?](../app-provisioning/user-provisioning.md)을 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2021년 1월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합

2021년 1월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 29개의 새 앱을 추가했습니다.

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[Bienvenue Formation](https://formation.bienvenue.pro/login), [AIDA Healthcare SSO](https://aidaforparents.com/login/organizations), [International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTO DAM](../saas-apps/ephoto-dam-tutorial.md), [Notion](../saas-apps/notion-tutorial.md), [Syndio](../saas-apps/syndio-tutorial.md), [Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md), [Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-data](https://www.ecolab.com/), [Vacancy Filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), [Synerise AI Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md), [Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md), [Illusive Networks](../saas-apps/illusive-networks-tutorial.md), [Proware](../saas-apps/proware-tutorial.md), [Splan Visitor](../saas-apps/splan-visitor-tutorial.md), [Aruba User Experience Insight](../saas-apps/aruba-user-experience-insight-tutorial.md), [Contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md), [Perimeter 81](../saas-apps/perimeter-81-tutorial.md), [Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

여기(https://aka.ms/AppsTutorial )에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다.

Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보를 읽으세요. https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>퍼블릭 미리 보기 - 두 번째 수준 관리자를 대체 승인자로 설정할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 사용자 액세스 관리  
**제품 기능:** 권한 관리
 
이제 승인자를 선택할 때 추가 옵션을 권한 관리에서 사용할 수 있습니다. 첫 번째 승인자를 "승인자로서 관리자"로 선택하는 경우, 대체 승인자 필드에서 선택할 수 있는 "두 번째 수준 관리자를 대체 승인자로"를 또 다른 옵션으로 가지게 됩니다. 이 옵션을 선택하는 경우 시스템에서 두 번째 수준 관리자를 찾을 수 없으면 요청을 전달할 대체 승인자를 추가해야 합니다. [자세한 정보](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>일반 공급 - 내 액세스 포털에서 직접 Teams로 이동

**유형:** 변경된 기능  
**서비스 범주:** 사용자 액세스 관리  
**제품 기능:** 권한 관리
 
이제 내 액세스 포털에서 Teams 직접 시작할 수 있습니다. 

이를 위해서는 내 액세스(https://myaccess.microsoft.com/) )에 로그인하고, "액세스 패키지"로 이동한 다음 "활성" 탭으로 이동하여 이미 액세스하고 있는 모든 액세스 패키지를 확인합니다. 선택한 액세스 패키지를 확장하고 Teams를 마우스로 가리키면 "열기" 단추를 클릭하여 시작할 수 있습니다. [자세한 정보를 알아보세요](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>위험한 게스트 사용자에 대한 로깅 및 최종 사용자 프롬프트 개선

**유형:** 변경된 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호
 

위험한 게스트 사용자에 대한 로깅 및 최종 사용자 프롬프트가 업데이트되었습니다. [ID 보호 및 B2B 사용자](../identity-protection/concept-identity-protection-b2b.md)에 관해 자세히 알아보세요.
 
---
 
## <a name="december-2020"></a>2020년 12월

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>퍼블릭 미리 보기 - 기본 제공 정책을 사용하여 Azure AD B2C 전화 가입 및 로그인

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
기본 제공 정책을 사용하는 B2C 전화 가입 및 로그인을 통해 조직의 IT 관리자 및 개발자는 최종 사용자가 사용자 흐름에서 전화번호를 사용하여 로그인하고 가입하게 합니다. 자세한 내용은 [사용자 흐름에 대해 전화 가입 및 로그인 설정하기(미리 보기)](../../active-directory-b2c/phone-authentication-user-flows.md)를 참조하세요.

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>일반 공급 - 기본적으로 모든 새 테넌트에 보안 기본값을 사용하도록 설정되었습니다.

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** ID 보안 및 보호
 
사용자 계정을 보호하기 위해 2020년 11월 12일 및 그 이후에 생성된 모든 새 테넌트에는 보안 기본값이 사용할 수 있는 상태로 제공됩니다. 보안 기본값은 다음을 비롯한 여러 정책을 적용합니다.
- 모든 사용자 및 관리자가 Microsoft Authenticator 앱을 사용하여 MFA에 등록해야 합니다.
- 로그인할 때마다 MFA를 사용하기 위해서는 핵심 관리자 역할이 필요합니다. 필요할 때마다 다른 모든 사용자에게 MFA를 요청하는 메시지가 표시됩니다. 
- 레거시 인증은 테넌트 전체에서 차단됩니다. 

자세한 내용은 [보안 기본값이란?](../fundamentals/concept-fundamentals-security-defaults.md)을 참조하세요.

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>일반 공급 - AADConnect에서 그룹 지원(멤버 수는 최대 25만 명까지)

**유형:** 변경된 기능  
**서비스 범주:** AD Connect  
**제품 기능:** ID 수명 주기 관리
 
Microsoft는 Azure AD Connect에 대한 새 엔드포인트(API)를 배포하여 Azure Active Directory에 대한 동기화 서비스 작업의 성능을 향상시켰습니다. 새 [V2 엔드포인트](../hybrid/how-to-connect-sync-endpoint-api-v2.md)를 활용하면 Azure AD로 내보내기 및 가져오기의 성능이 크게 향상됩니다. 이 새 엔드포인트는 다음 시나리오를 지원합니다.

- 최대 25만 명의 멤버가 있는 그룹 동기화
- Azure AD에서 내보내기 및 Azure AD로 가져오기 성능 향상

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>일반 공급 - Azure 중국 클라우드 테넌트에 사용할 수 있는 권한 관리

**유형:** 새로운 기능  
**서비스 범주:** 사용자 액세스 관리  
**제품 기능:** 권한 관리
 

이제 Azure 중국 클라우드의 모든 테넌트에 권한 관리 기능을 사용할 수 있습니다. 자세한 내용은 [ID 거버넌스 설명서](https://docs.azure.cn/zh-cn/active-directory/governance/) 사이트를 참조하세요.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Azure AD 앱 갤러리의 새로운 프로비저닝 커넥터 - 2020년 12월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합

다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [디지털 프로세스 자동화를 위한 Bizagi Studio](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut 클라우드 인쇄 관리](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [구문 분석 가능](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2020년 12월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2020년 12월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 18개의 새 앱을 추가했습니다.

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold), [Guider](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [Pims 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [vonage](../saas-apps/vonage-tutorial.md), [Count Me In - Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md), [Shutterstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md)

여기(https://aka.ms/AppsTutorial )에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다.

Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보를 읽으세요. https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>내 액세스 포털에서 직접 Teams로 이동

**유형:** 변경된 기능  
**서비스 범주:** 사용자 액세스 관리 **제품 기능:** 권한 관리

이제 내 액세스 포털에서 Teams를 직접 시작할 수 있습니다. 이를 위해서는 [내 액세스](https://myaccess.microsoft.com/)에 로그인하고, **액세스 패키지** 로 이동한 다음 **활성** 탭으로 이동하여 이미 액세스하고 있는 모든 액세스 패키지를 확인합니다. 액세스 패키지를 확장하고 Teams를 마우스로 가리키면 **열기** 단추를 클릭하여 실행할 수 있습니다. 

내 액세스 포털을 사용하는 방법에 관한 자세한 내용을 보려면 [Azure AD 권한 관리에서 액세스 패키지에 대한 액세스 요청](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)으로 이동하세요.

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>퍼블릭 미리 보기 - 두 번째 수준 관리자를 대체 승인자로 설정할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 사용자 액세스 관리  
**제품 기능:** 권한 관리

이제 권한 관리의 승인 프로세스에서 추가 옵션을 사용할 수 있습니다. 첫 번째 승인자를 승인자로서 관리자로 선택하는 경우, 대체 승인자 필드에서 선택할 수 있는 두 번째 수준 관리자를 대체 승인자로를 또 다른 옵션으로 가지게 됩니다. 이 옵션을 선택하는 경우 시스템에서 두 번째 수준 관리자를 찾을 수 없으면 요청을 전달할 대체 승인자를 추가해야 합니다.

자세한 내용은 [Azure AD 권한 관리에서 액세스 패키지를 위한 승인 설정 변경](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)을 참조하세요.

--- 

## <a name="november-2020"></a>2020년 11월

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1.0, TLS 1.1, 3DES 사용 중단

**유형:** 변경 계획  
**서비스 범주:** 모든 Azure AD 애플리케이션  
**제품 기능:** 기준

Azure Active Directory는 2021년 6월 30일부터 전 세계에서 다음 Azure Active Directory 프로토콜을 사용 중단합니다.

- TLS 1.0
- TLS 1.1
- 3DES 암호화 그룹(TLS_RSA_WITH_3DES_EDE_CBC_SHA)

영향을 받는 환경은 다음과 같습니다.
- Azure 상용 클라우드
- Office 365 GCC 및 WW

관련 공지 모든 클라이언트 서버와 브라우저 서버 조합은 TLS 1.2 및 최신 암호 그룹을 사용하여 Azure, Office 365, Microsoft 365 서비스를 위한 Azure Active Directory 보안 연결을 유지해야 합니다. 이 변경 [내용은 US Gov 클라우드에서 Azure Active Directory TLS 1.0 & 1.1 및 3DES 암호 모음](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud)사용 중단과 관련되어 있습니다.

사용 중단 프로토콜 종속성을 제거하는 방법에 관한 참고 자료는 [Azure AD TLS 1.1 및 1.0 사용 중단을 위한 사용자 환경에서 TLS 1.2 지원 사용](/troubleshoot/azure/active-directory/enable-support-tls-environment)을 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2020년 11월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합

2020년 11월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 52개의 새 앱을 추가했습니다.

[Travel & Expense Management](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [Itslearning File Picker](https://pmteam.itslearning.com/), [Crises Control](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum - Study Island](https://app.studyisland.com/cfw/login/), [Virtual Risk Manager](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview - Recruit](https://recruit.talview.com/login), 실시간 변환기, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [En-trak Tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), [EVA 체크 인](https://my.evacheckin.com/organization), [HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [Coupa Risk Assess](../saas-apps/coupa-risk-assess-tutorial.md), [Lucid(모든 제품)](../saas-apps/lucid-tutorial.md), [GoBright](https://portal.brightbooking.eu/), [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md),[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [Freight Measure](https://www.gpcsl.com/freight.html), [Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), [Nature Research](../saas-apps/nature-research-tutorial.md), [Play Digital Signage](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Sunrise Software Relations CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [Mentor® by eDriving: Business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/)

여기(https://aka.ms/AppsTutorial )에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다.

Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보를 읽으세요. https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>퍼블릭 미리 보기 - 엔터프라이즈 앱을 위한 사용자 지정 역할

**유형:** 새로운 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
 [위임된 엔터프라이즈 애플리케이션 관리를 위한 사용자 지정 RBAC 역할](../roles/custom-available-permissions.md)은 현재 퍼블릭 미리 보기로 제공됩니다. 이런 새 권한은 앱 등록 관리를 위한 사용자 지정 역할을 기반으로 하며, 이를 통해 관리자의 액세스를 세밀하게 제어할 수 있습니다. 시간이 지남에 따라 Azure AD의 관리를 위임하는 추가 권한이 릴리스됩니다.

몇 가지 일반적인 위임 시나리오는 다음과 같습니다.
- SAML 기반 Single Sign-On 애플리케이션에 액세스할 수 있는 사용자 및 그룹의 할당
- Azure AD 갤러리 앱 생성
- SAML 기반 Single Sign-On 애플리케이션을 위한 기본 SAML 구성의 업데이트 및 읽기
- SAML 기반 Single Sign-On 애플리케이션을 위한 서명 인증서 관리
- SAML 기반 Single Sign-On 애플리케이션에 만료되는 로그인 인증서 알림 메일 주소 업데이트
- SAML 기반 Single Sign-On 애플리케이션을 위한 SAML 토큰 서명 및 로그인 알고리즘의 업데이트
- SAML 기반 Single Sign-On 애플리케이션을 위한 사용자 특성 및 클레임의 만들기, 삭제 및 업데이트.
- 프로비저닝 작업을 설정, 해제, 다시 시작하는 기능
- 특성 매핑 업데이트
- 개체와 연결된 프로비저닝 설정을 읽는 기능
- 서비스 주체와 연결된 프로비저닝 설정을 읽는 기능
- 프로비저닝을 위해 애플리케이션 액세스 권한을 부여하는 기능

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>퍼블릭 미리 보기 - Azure AD 애플리케이션 프록시는 기본적으로 인증에 헤더를 사용하는 애플리케이션의 Single Sign-On 액세스를 지원합니다.

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어
 
Azure Active Directory(Azure AD) 애플리케이션 프록시는 기본적으로 인증에 헤더를 사용하는 애플리케이션의 Single Sign-On 액세스를 지원합니다. Azure AD에서 애플리케이션에 필요한 헤더 값을 구성할 수 있습니다. 헤더 값은 애플리케이션 프록시를 통해 애플리케이션으로 전송됩니다. 자세한 내용은 [Azure AD 앱 프록시로 온-프레미스 앱에 대한 헤더 기반 Single Sign-On 제공](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>일반 공급 - 사용자 지정 정책을 사용한 Azure AD B2C 전화 가입 및 로그인

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

전화번호 가입 및 로그인을 사용하는 개발자와 기업은 고객이 SMS를 통해 사용자의 전화번호로 전송된 일회용 암호를 사용하여 가입하고 로그인하게 할 수 있습니다. 또한 해당 기능을 사용하는 고객은 휴대폰 액세스 권한을 상실한 경우 전화번호를 변경할 수 있습니다. 사용자 지정 정책을 사용하면 개발자와 기업이 페이지 사용자 지정을 통해 브랜드를 알릴 수 있습니다. [Azure AD B2C에서 사용자 지정 정책을 사용하여 전화 가입 및 로그인을 설정](../../active-directory-b2c/phone-authentication-user-flows.md)하는 방법을 알아봅니다.
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Azure AD 앱 갤러리의 새로운 프로비저닝 커넥터 - 2020년 11월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [Adobe ID 관리](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic - Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝](../app-provisioning/user-provisioning.md)을 참조하세요.
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>퍼블릭 미리 보기 - ProxyAddresses를 사용하는 메일 로그인을 이제 단계적 롤아웃을 통해 배포 가능

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
이제 테넌트 관리자는 단계적 롤아웃을 사용하여 특정 Azure AD 그룹에 ProxyAddresses를 사용하는 메일 로그인을 배포할 수 있습니다. 그러면 홈 영역 검색 정책을 통해 전체 테넌트에 배포하기 전에 해당 기능을 사용해 볼 수 있습니다. 단계적 롤아웃을 통해 ProxyAddresses를 사용하는 메일 로그인 배포에 대한 지침은 [설명서](../authentication/howto-authentication-use-email-signin.md)에 있습니다.
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>제한된 미리 보기 - 로그인 진단

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
로그인 진단의 초기 미리 보기 릴리스에서는 이제 관리자가 사용자 로그인을 검토할 수 있습니다. 관리자는 로그인 중에 발생하는 작업과 문제를 해결하는 방법에 대해 상황에 맞는 특정 세부 정보 및 참고 자료를 받을 수 있습니다. 이 진단은 Azure AD 수준 및 조건부 액세스 진단 블레이드 및 해결 블레이드 모두에서 사용할 수 있습니다. 이 릴리스에 포함된 진단 시나리오는 조건부 액세스, 다단계 인증, 성공적인 로그인입니다.

자세한 내용은 [Azure AD의 로그인 진단이란?](../reports-monitoring/overview-sign-in-diagnostics.md)을 참조하세요.
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>일반적이지 않은 로그인 속성 개선

**유형:** 변경된 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호

  일반적이지 않은 로그인 속성 검색이 업데이트되었습니다. 고객은 더 위험한 일반적이지 않은 로그인 속성 검색을 확인할 수 있습니다. 자세한 내용은 [위험이란?](../identity-protection/concept-identity-protection-risks.md)을 참조하세요.
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>이제 클라우드 프로비저닝 에이전트의 퍼블릭 미리 보기 새로 고침을 사용할 수 있습니다(버전: 1.1.281.0).

**유형:** 변경된 기능  
**서비스 범주:** Azure AD 클라우드 프로비저닝  
**제품 기능:** ID 수명 주기 관리
 
클라우드 프로비저닝 에이전트는 퍼블릭 미리 보기로 출시되었으며 이제 포털을 통해 사용할 수 있습니다. 이 릴리스에는 도메인을 위한 GMSA 지원 등 몇 가지 개선 사항이 포함되어, 향상된 보안과 개선된 초기 동기화 주기, 대량 그룹을 위한 지원을 제공합니다. 자세한 내용은 릴리스 버전 [기록](../app-provisioning/provisioning-agent-release-version-history.md)을 확인하세요. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>/informationProtection에서 BitLocker 복구 키 API 엔드포인트

**유형:** 변경된 기능  
**서비스 범주:** 디바이스 액세스 관리  
**제품 기능:** 디바이스 수명 주기 관리
 
이전에는 /bitlocker 엔드포인트를 통해 BitLocker 키를 복구할 수 있었습니다. 드디어 해당 엔드포인트를 사용 중단하고, 고객은 이제/informationProtection 아래에 있는 API를 사용하기 시작해야 합니다. 

변경 내용을 반영하는 설명서 업데이트는 [BitLocker 복구 API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true)를 참조하세요.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>원격 데스크톱 서비스 HTML5 웹 클라이언트를 위한 애플리케이션 프록시 지원의 일반 공급

**유형:** 변경된 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어
 
RDS(원격 데스크톱 서비스) 웹 클라이언트를 위한 Azure AD 애플리케이션 프록시 지원은 이제 일반 공급으로 제공됩니다. 사용자는 RDS 웹 클라이언트를 사용하여 Microsoft Edge, Internet Explorer 11, Google Chrome 등의 HTLM5 지원 브라우저를 통해 원격 데스크톱 인프라에 액세스할 수 있습니다. 사용자는 어디서나 로컬 디바이스를 사용하는 것처럼 원격 앱 또는 원격 데스크톱과 상호 작용할 수 있습니다. 

Azure AD 애플리케이션 프록시를 사용하여 모든 형식의 리치 클라이언트 앱을 위한 사전 인증 및 조건부 액세스 정책을 적용하여 RDS 배포의 보안을 강화할 수 있습니다. 자세히 알아보려면 [Azure AD 애플리케이션 프록시를 사용하여 원격 데스크톱 게시](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)를 참조하세요.
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>퍼블릭 미리 보기 상태인 새 향상된 동적 그룹 서비스

**유형:** 변경된 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업
 
향상된 동적 그룹 서비스는 현재 퍼블릭 미리 보기로 제공됩니다. 테넌트에 동적 그룹을 만드는 새 고객은 새 서비스를 사용하게 됩니다. 동적 그룹을 만드는 데 필요한 시간은 테넌트의 크기가 아니라 생성되는 그룹의 크기에 비례합니다. 이 업데이트는 고객이 더 작은 그룹을 만들 때 큰 테넌트의 성능을 크게 향상합니다. 

또한 새 서비스는 특성 변경으로 인한 멤버 추가 및 제거를 몇 분 내에 완료하는 것을 목표로 합니다. 또한 단일 처리 오류는 테넌트 처리를 차단하지 않습니다. 동적 그룹을 만드는 방법에 관한 자세한 내용은 [설명서](../enterprise-users/groups-create-rule.md)를 참조하세요.
 
---
## <a name="october-2020"></a>2020년 10월

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure TLS 인증서 변경으로 영향을 받는 Azure AD 온-프레미스 하이브리드 에이전트

**유형:** 변경 계획  
**서비스 범주:** 해당 없음  
**제품 기능:** 플랫폼

Microsoft는 다른 루트 CA(인증 기관)의 TLS 인증서를 사용하도록 Azure 서비스를 업데이트하 고 있습니다. 해당 업데이트를 실시한 것은 현재 CA 인증서가 CA/브라우저 포럼 기준 요구 사항 중 하나를 준수하지 않기 때문입니다. 이 변경 내용은 고정된 루트 인증서 목록과 함께 강화된 환경을 갖춘 온-프레미스에 설치된 Azure AD 하이브리드 에이전트에 영향을 주므로 새 인증서 발급자를 신뢰하도록 업데이트해야 합니다.

이렇게 변경하면 즉시 조처를 하지 않는 경우 서비스가 중단됩니다. 에이전트에는 온-프레미스에 원격 액세스하기 위한 [애플리케이션 프록시 커넥터](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy), 사용자가 동일한 암호를 사용하여 애플리케이션에 로그인할 수 있게 하는 [통과 인증](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 에이전트 및 Azure AD Sync에 AD를 수행하는 [클라우드 프로비저닝 미리 보기](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 에이전트가 포함됩니다. 

특정 CRL(인증서 해지 목록) 다운로드에만 아웃 바운드 호출을 허용하도록 방화벽 규칙을 설정한 환경에서는 다음 CRL 및 OCSP URL을 허용해야 합니다. 액세스를 사용하기 위한 CRL URL 및 OCSP URL과 변경 내용에 관한 자세한 내용은 [Azure TLS 인증서 변경](../../security/fundamentals/tls-certificate-changes.md)을 참조하세요.

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>프로비저닝 이벤트는 감사 로그에서 제거되고 프로비저닝 로그에만 게시됩니다.

**유형:** 변경 계획  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
SCIM [프로비저닝 서비스](../app-provisioning/user-provisioning.md)에 의한 작업은 감사 로그와 프로비저닝 로그 모두에 로깅됩니다. 여기에는 ServiceNow에서 사용자 만들기, GSuite의 그룹 또는 AWS에서 역할 가져오기 등의 작업이 포함됩니다. 향후 해당 이벤트는 프로비저닝 로그에만 게시됩니다. 이 변경은 로그에서 중복 이벤트를 방지하고, 로그 분석에서 로그를 사용하는 고객에 의한 추가 비용 발생을 방지하기 위해 구현됩니다. 

날짜가 되면 업데이트를 제공합니다. 2020년에는 사용 중단 계획이 없습니다. 

> [!NOTE]
> 이는 프로비저닝 서비스에서 내보낸 동기화 이벤트 외부의 감사 로그에 있는 이벤트에는 영향을 주지 않습니다. 애플리케이션 생성, 조건부 액세스 정책, 디렉터리의 사용자 등의 이벤트는 감사 로그에서 계속 내보내집니다. [자세한 정보를 알아보세요](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure TLS(전송 계층 보안) 인증서 변경의 영향을 받는 Azure AD 온-프레미스 하이브리드 에이전트

**유형:** 변경 계획  
**서비스 범주:** 해당 없음  
**제품 기능:** 플랫폼
 
Microsoft는 다른 루트 CA(인증 기관)의 TLS 인증서를 사용하도록 Azure 서비스를 업데이트하 고 있습니다. CA/브라우저 포럼 기준 요구 사항 중 하나를 따르지 않는 현재 CA 인증서로 인해 업데이트가 수행됩니다. 해당 변경 내용은 고정된 루트 인증서 목록을 사용하여 강화된 환경을 보유한 온-프레미스에 설치된 Azure AD 하이브리드 에이전트에 영향을 줍니다. 에이전트는 새 인증서 발급자를 신뢰하도록 업데이트해야 합니다.

이렇게 변경하면 즉시 조처를 하지 않는 경우 서비스가 중단됩니다. 해당 에이전트는 다음과 같습니다. 
- 온-프레미스에 대한 원격 액세스를 위한 [애플리케이션 프록시 커넥터](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) 
- 사용자가 동일한 암호를 사용하여 애플리케이션에 로그인할 수 있게 하는 [통과 인증](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 에이전트
- Azure AD Sync에 AD를 수행하는 [클라우드 프로비저닝 미리 보기](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 에이전트. 

특정 CRL(인증서 해지 목록) 다운로드에만 아웃 바운드 호출을 허용하도록 방화벽 규칙을 설정한 환경에서는 CRL URL 및 OCSP URL을 허용해야 합니다. 액세스를 사용하기 위한 CRL URL 및 OCSP URL과 변경 내용에 관한 자세한 내용은 [Azure TLS 인증서 변경](../../security/fundamentals/tls-certificate-changes.md)을 참조하세요.
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>US Gov 클라우드에서 Azure Active Directory TLS 1.0, TLS 1.1, 3DES 사용 중단

**유형:** 변경 계획  
**서비스 범주:** 모든 Azure AD 애플리케이션  
**제품 기능:** 기준
 
Azure Active Directory는 2021년 3월 31일부터 다음 프로토콜을 사용 중단합니다.
- TLS 1.0
- TLS 1.1
- 3DES 암호화 그룹(TLS_RSA_WITH_3DES_EDE_CBC_SHA)

모든 클라이언트 서버와 브라우저 서버 조합은 TLS 1.2 및 최신 암호 그룹을 사용하여 Azure, Office 365, Microsoft 365 서비스를 위한 Azure Active Directory 보안 연결을 유지해야 합니다.

영향을 받는 환경은 다음과 같습니다.
- Azure US Gov
- [Office 365 GCC High 및 DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)

사용 중단 프로토콜 종속성을 제거하는 방법에 관한 참고 자료는 [Azure AD TLS 1.1 및 1.0 사용 중단을 위한 사용자 환경에서 TLS 1.2 지원 사용](/troubleshoot/azure/active-directory/enable-support-tls-environment)을 참조하세요.
 
---

### <a name="assign-applications-to-roles-on-administrative-unit-and-object-scope"></a>관리 단위 및 개체 범위의 역할에 애플리케이션 할당

**유형:** 새로운 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
이 기능을 사용하면 관리 단위 범위에 대한 관리자 역할에 애플리케이션(SPN)을 할당할 수 있습니다. 자세한 내용은 [관리 단위에 범위가 지정된 역할 할당](../roles/admin-units-assign-roles.md)을 참조하세요.

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>이제 리소스에 대한 액세스가 거부된 경우, 게스트 사용자를 사용하지 않도록 설정하고 삭제할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** ID 거버넌스
 
비활성화 및 삭제는 조직이 그룹 및 앱에서 외부 게스트를 더 효율적으로 관리할 수 있게 돕는 Azure AD 액세스 검토의 고급 제어입니다. 액세스 검토에서 게스트가 거부된 경우 **비활성화 및 삭제** 는 30일 동안 로그인하지 못하도록 자동으로 차단합니다. 30일 후에는 테넌트에서 완전히 제거됩니다.

이 기능에 관한 자세한 내용은 [Azure AD 액세스 검토를 사용하여 외부 ID 비활성화 및 삭제](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews)를 참조하세요.
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>액세스 검토 작성자는 메일의 사용자 지정 메시지를 검토자에게 추가할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** ID 거버넌스
 
Azure AD 액세스 검토에서 검토를 만드는 관리자는 이제 검토자에게 사용자 지정 메시지를 작성할 수 있습니다. 검토자는 검토를 수신한 메일에서 검토를 완료하라는 메시지를 봅니다. 이 기능을 사용하는 방법 관련한 자세한 내용은 [하나 이상의 액세스 검토 만들기](../governance/create-access-review.md#create-one-or-more-access-reviews) 섹션의 14단계를 참조하세요.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Azure AD 앱 갤러리의 새로운 프로비저닝 커넥터 - 2020년 10월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [글로벌 릴레이 ID 동기화](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Azure AD B2C용 통합 도우미

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
이제 Azure AD B2C 앱 등록에서 통합 도우미(미리 보기) 환경을 사용할 수 있습니다. 이 환경에서는 일반적인 시나리오에 맞게 애플리케이션을 구성하는 방법을 안내합니다. [Microsoft ID 플랫폼 모범 사례 및 권장 사항](../develop/identity-platform-integration-checklist.md)에 관한 자세한 정보.
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Azure Portal UI에서 역할 템플릿 ID 보기

**유형:** 새로운 기능  
**서비스 범주:** Azure 역할  
**제품 기능:** 액세스 제어
 

이제 Azure Portal에서 각 Azure AD 역할의 템플릿 ID를 볼 수 있습니다. Azure AD에서 선택한 역할에 관한 **설명** 을 선택합니다. 

고객은 PowerShell 스크립트 및 코드에서 표시 이름 대신 역할 템플릿 ID를 사용하는 것이 좋습니다. 역할 템플릿 ID는 [directoryRoles](/graph/api/resources/directoryrole) 및 [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta&preserve-view=true) 개체를 사용하도록 지원됩니다. 역할 템플릿 ID 관련한 자세한 내용은 [Azure AD 기본 제공 역할](../roles/permissions-reference.md)을 참조하세요.

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Azure AD B2C 가입 사용자 흐름을 위한 API 커넥터는 현재 퍼블릭 미리 보기로 제공됩니다.

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 

이제 API 커넥터를 Azure Active Directory B2C에서 사용할 수 있습니다. API 커넥터를 사용하면 웹 API를 사용하여 가입 사용자 흐름을 사용자 지정하고 외부 클라우드 시스템과 통합할 수 있습니다. API 커넥터를 사용하여 다음을 수행할 수 있습니다.

- 사용자 지정 승인 워크플로와 통합
- 사용자 입력 데이터 유효성 검사
- 사용자 특성 덮어쓰기 
- 사용자 지정 비즈니스 논리 실행 

 [API 커넥터를 사용하여 등록 사용자 지정 및 확장](../../active-directory-b2c/api-connectors-overview.md) 설명서로 이동하여 자세한 내용을 알아보세요.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>권한 관리에서 연결된 조직의 상태 속성

**유형:** 새로운 기능  
**서비스 범주:** 디렉터리 관리 **제품 기능:** 권한 관리
 

 이제 연결된 모든 조직에는 "상태"라는 추가 속성이 있습니다. 상태는 "모든 구성된 연결된 조직"을 참조하는 정책에서 연결된 조직을 사용하는 방법을 제어합니다. 이 값은 "구성됨"(조직이 "모든" 절을 사용하는 정책 범위에 있음을 의미) 또는 "제안됨"(조직이 범위에 있지 않음을 의미)입니다.  

수동으로 만든 연결된 조직의 기본 설정은 "구성됨"입니다. 한편, 자동 생성된(인터넷의 모든 사용자가 액세스를 요청하도록 허용하는 정책을 통해 생성된) 항목은 "제안됨"으로 기본 설정됩니다.  2020년 9월 9일 이전에 생성된 모든 연결된 조직은 "구성됨"으로 설정됩니다. 관리자는 필요에 따라 해당 속성을 업데이트할 수 있습니다. [자세한 정보를 알아보세요](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>이제 Azure Active Directory External Identities에 B2C를 위한 프리미엄 고급 보안 설정이 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
ID 보호의 위험 기반 조건부 액세스 및 위험 검색 기능을 이제 [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md)에서 사용할 수 있습니다. 이제 고객은 해당 고급 보안 기능을 사용하여 다음을 수행할 수 있습니다.
- 지능형 인사이트를 활용하여 B2C 앱 및 최종 사용자 계정으로 위험을 평가합니다. 검색에는 비정상적인 이동, 익명 IP 주소, 맬웨어에 연결된 IP 주소, Azure AD 위협 인텔리전스가 포함됩니다. 포털 및 API 기반 보고서를 사용할 수도 있습니다.
- B2C 사용자를 위한 적응 인증 정책을 구성하여 위험을 자동으로 해결합니다. 앱 개발자와 관리자는 검색된 사용자 위험 수준에 따라 MFA(다단계 인증)를 요구하거나 액세스를 차단하고 위치, 그룹, 앱을 기반으로 사용 가능한 추가 제어를 사용하여 실시간 위험을 완화할 수 있습니다.
- Azure AD B2C 사용자 흐름 및 사용자 지정 정책과 통합. 조건은 Azure AD B2C의 기본 제공 사용자 흐름에서 실행되거나 B2C 사용자 지정 정책에 통합될 수 있습니다. B2C 사용자 흐름의 다른 측면과 마찬가지로 최종 사용자 환경 메시지를 사용자 정의할 수 있습니다. 사용자 지정은 조직의 음성, 브랜드, 완화 대안에 따라 지정됩니다.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2020년 10월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2020년 10월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 27개의 새 앱을 추가했습니다.

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee - Productivity Superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/), [Zone Technologies Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [Appointment Reminder](https://app.appointmentreminder.co.nz/account/login), [Cloud.Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Greetly](https://app.greetly.com/), [OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), [Loop Flow CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [Retail Zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

여기(https://aka.ms/AppsTutorial )에서 모든 애플리케이션의 설명서를 찾을 수도 있습니다.

Azure AD 앱 갤러리에서 애플리케이션을 나열하려면 여기에서 세부 정보를 읽으세요. https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>이제 프로비저닝 로그를 로그 분석으로 스트리밍할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 

프로비저닝 로그를 로그 분석에 게시하여 다음을 수행합니다.
- 30일 이상 프로비저닝 로그 저장
- 사용자 지정 경고 및 알림 정의
- 로그를 시각화하는 대시보드 빌드
- 복잡한 쿼리를 실행하여 로그 분석 

기능을 사용하는 방법을 알아보려면 [프로비저닝이 Azure Monitor 로그와 통합되는 방식 이해](../app-provisioning/application-provisioning-log-analytics.md)를 참조하세요.
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>이제 애플리케이션 소유자가 프로비저닝 로그를 볼 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
이제 애플리케이션 소유자가 프로비저닝 서비스에서 작업을 모니터링하게 하고, 권한 있는 역할을 제공하지 않아도 IT 병목 상태 없이 문제를 해결하게 할 수 있습니다. [자세한 정보를 알아보세요](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>10개의 Azure Active Directory 역할 이름 바꾸기

**유형:** 변경된 기능  
**서비스 범주:** Azure 역할  
**제품 기능:** 액세스 제어
 
일부 Azure AD(Azure Active Directory) 기본 제공 역할에는 Microsoft 365 관리 센터, Azure AD 포털, Microsoft Graph에 표시되는 이름과 다른 이름이 있습니다. 이런 차이로 인해 자동화된 프로세스에서 문제가 발생할 수 있습니다. 이 업데이트를 사용하면 10개의 역할 이름이 일치하도록 수정합니다. 다음 표에는 새 역할 이름이 있습니다.

![MS Graph API 및 Azure Portal의 역할 이름 및 M365 Azure Portal, Azure Portal, API의 제안된 새 역할 이름을 보여 주는 표.](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>MSAL JS 2.x를 사용하는 SPA용 인증 코드 흐름을 위한 Azure AD B2C 지원

**유형:** 변경된 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
MSAL.js 버전 2.x에는 이제 SPA(단일 페이지 웹앱)를 위한 인증 코드 흐름 지원이 포함됩니다. Azure AD B2C는 이제 Azure Portal에서 SPA 앱 형식 사용을 지원하고, 단일 페이지 앱용 PKCE를 통한 MSAL.js 인증 코드 흐름의 사용을 지원합니다. 이렇게 하면 SPA가 Azure AD B2C를 사용하여 최신 브라우저로 SSO를 유지하고 최신 인증 프로토콜 권장 사항을 준수할 수 있습니다. [Azure Active Directory B2C에서 SPA(단일 페이지 애플리케이션) 등록](../../active-directory-b2c/tutorial-register-spa.md) 자습서를 시작합니다.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>신뢰할 수 있는 디바이스 설정에서 MFA(다단계 인증)를 기억하게 하는 업데이트

**유형:** 변경된 기능  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호
 

신뢰할 수 있는 디바이스 기능에서 최대 365일 동안 인증을 확장하도록 [MFA(다단계 인증)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication)를 최근 업데이트했습니다. Azure AD(Azure Active Directory) 프리미엄 라이선스를 통해 재인증 설정에 더 많은 유연성을 제공하는 [조건부 액세스 – 로그인 빈도 정책](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)을 사용할 수도 있습니다.

최적의 사용자 환경을 위해서는 신뢰할 수 있는 디바이스 설정에서 MFA를 기억하는 대신 조건부 액세스 로그인 빈도를 사용하여 신뢰할 수 있는 디바이스, 위치 또는 위험 수준이 낮은 세션에서 세션 수명을 연장하는 것이 좋습니다. 시작하려면 [재인증 환경 최적화에 관한 최신 참고 자료](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)를 검토하세요.

---