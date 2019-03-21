---
title: 새로운 기능 릴리스 정보 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능, 예정된 변경 내용 등을 알아봅니다.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eda145f43c9268e5f6b291a767ed51249804f87d
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286451"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능

>복사 하 여이 URL을 붙여넣으면 업데이트에 대 한이 페이지를 다시 방문 하는 경우에 대 한 알림 받기: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` 에 사용자 ![RSS 피드 판독기 아이콘](./media/whats-new/feed-icon-16x16.png) 피드 판독기입니다.

Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월 이상된 항목을 찾으려는 경우 [Azure Active Directory의 새로운 기능 아카이브](whats-new-archive.md)에서 찾을 수 있습니다.

---
## <a name="february-2019"></a>2019년 2월

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>구성 가능한 Azure AD SAML 토큰 암호화(공개 미리 보기) 

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
이제 암호화 된 SAML 토큰을 받을 모든 지원 되는 SAML 앱을 구성할 수 있습니다. 구성 하 고 앱을 사용 하는 경우 Azure AD에서 Azure AD에 저장 된 인증서를 가져온 공개 키를 사용 하 여 내보낸된 SAML 어설션이 암호화 합니다.

SAML 토큰 암호화를 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure AD SAML 구성 토큰 암호화](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)합니다.

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 그룹 또는 앱에 대한 액세스 검토 만들기

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 거버넌스

이제 여러 그룹을 포함할 수 있습니다 또는 단일 Azure AD에 앱 액세스 그룹 멤버 자격에 앱 할당 검토 합니다. 여러 그룹을 사용 하 여 액세스 검토 또는 앱은 동일한 설정을 사용 하 여 설정 하 고 동시에 포함 된 모든 검토자에 게 알림이 표시 됩니다.

방법에 대 한 자세한 내용은 Azure AD 액세스 검토를 사용 하 여 액세스 검토 만들기, 참조 [Azure AD 액세스 검토에 그룹 또는 응용 프로그램의 액세스 검토 만들기](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱 - 2019년 2월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2019 1 월에에서 앱 갤러리에 페더레이션이 포함 된 이러한 27 새 앱 지원 추가 했습니다.

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial)합니다 [오타로](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7)를 [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)를 [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial)를 [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial)를 [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial)를 [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), 사용 권한 클릭 [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial)를 [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial)를 [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial)를 [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [지진 ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [공유는 꿈의](https://www.shareadream.org/how-it-works)를 [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial)를 [webMethods 통합 클라우드](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)를 [기술 원격 LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU 캠퍼스](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial)를 [Periscope 데이터](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial)를 [Netop 포털](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial)를 [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [Genesys여PureCloud](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp 생산성 플랫폼](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="enhanced-combined-mfasspr-registration"></a>향상된 통합 MFA/SSPR 등록

**유형:** 변경된 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 사용자 인증
 
고객 피드백에 대 한 응답을 개선 했습니다 결합 된 MFA/SSPR 등록 미리 보기 환경은 사용자의 MFA 및 SSPR에 대 한 해당 보안 정보를 보다 신속 하 게 등록 하도록 지원 합니다. 

**현재 사용자에 대 한 향상 된 환경을 설정 하려면 다음이 단계를 수행 합니다.**

1. 전역 관리자 또는 사용자 관리자, Azure portal에 로그인 이동할 **Azure Active Directory > 사용자 설정 > 액세스 패널 미리 보기 기능에 대 한 설정을 관리**합니다. 

2. 에 **미리 보기를 사용할 수 있는 사용자는 등록 하 고 보안 정보 관리에 대 한 기능 – 새로 고침** 옵션을 선택에 대 한 기능을 켜려면를 **선택한 사용자 그룹** 또는 **모든 사용자** .

다음 몇 주 동안 우리가 이전 결합 된 MFA/SSPR 등록 미리 보기 환경에 아직 없는 켜져 있는 테 넌 트에서 끌 수를 삭제 하겠습니다.

**테 넌 트에 대 한 컨트롤을 제거할 경우를 확인 하려면 다음이 단계를 수행 합니다.**

1. 전역 관리자 또는 사용자 관리자, Azure portal에 로그인 이동할 **Azure Active Directory > 사용자 설정 > 액세스 패널 미리 보기 기능에 대 한 설정을 관리**합니다.  

2.  경우는 **등록 하 고 보안 정보 관리에 대 한 미리 보기 기능을 사용할 수 있는 사용자** 옵션을 설정 **None**, 테 넌 트에서 옵션이 제거 됩니다.

이전 결합 된 MFA/SSPR 등록으로 이전에 설정한 여부에 관계 없이 미리 보기 사용자를 위한 환경을 여부, 나중에 이전 환경 해제 됩니다. 따라서 이동 하는 새로운, 향상 된 환경에 가능한 한 빨리는 것이 좋습니다.

향상 된 등록 환경에 대 한 자세한 내용은 참조는 [쿨 향상 된 기능을 Azure AD MFA를 결합 하 고 암호 재설정 등록 환경을](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)합니다.

---

### <a name="updated-policy-management-experience-for-user-flows"></a>사용자 흐름의 업데이트된 정책 관리 환경

**유형:** 변경된 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

쉽게 사용자 흐름 (이전에 알려진으로, 기본 제공 정책)에 대 한 정책 만들기 및 관리 프로세스를 업데이트 했습니다. 이 새로운 환경은 이제 모든 Azure AD 테 넌 트에 대 한 기본값입니다.

웃는 얼굴을 사용 하 여 추가 사용자 의견 및 제안을 제공할 수 아이콘 찡그린 얼굴 보내기는 **피드백 보내기** 포털 화면 맨 위에 있는 영역입니다.

새 정책 관리 환경에 대 한 자세한 내용은 참조는 [JavaScript 사용자 정 및 많은 새 기능에 이제 Azure AD B2C에](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) 블로그.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Azure AD B2C에서 제공하는 특정 페이지 요소 버전 선택

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 Azure AD B2C에서 제공 하는 페이지 요소의 특정 버전을 선택할 수 있습니다. 특정 버전을 선택 하면 페이지에 표시 되 고 예측 가능한 동작을 얻을 수 있습니다 전에 업데이트를 테스트할 수 있습니다. 또한 이제 하도록 선택할 수 있습니다에서 JavaScript 사용자 지정을 허용 하도록 특정 페이지 버전을 적용 합니다. 이 기능을 켜려면로 이동 합니다 **속성** 사용자 흐름에서 페이지입니다.

페이지 요소의 특정 버전을 선택 하는 방법에 대 한 자세한 내용은 참조는 [JavaScript 사용자 정 및 많은 새 기능에 이제 Azure AD B2C에](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) 블로그.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>B2C를 위한 구성 가능한 최종 사용자 암호 요구 사항(GA)

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

설정할 수 있습니다 이제 특히 조직의 암호 복잡성 네이티브를 사용 하는 대신 최종 사용자를 Azure AD 암호 정책입니다. **속성** 블레이드에서 사용자의 흐름 (이전에 기본 제공 정책을 알려짐)의 암호 복잡성을 선택할 수 있습니다 **단순** 또는 **강력한**를 할 수 있습니다 만들기는 **사용자 지정** 요구 사항 집합입니다.

암호 복잡성 요구 사항 구성 하는 방법에 대 한 자세한 내용은 참조 하십시오 [Azure Active Directory B2C에서 암호에 복잡성 요구 사항을 구성](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)합니다.

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>사용자 지정 브랜드 인증 환경을 위한 새로운 기본 템플릿

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

에 새 기본 템플릿을 사용할 수 있습니다는 **레이아웃 페이지** 블레이드 (이전에 기본 제공 정책으로 알려짐) 사용자 흐름을 사용자 지정을 만들려면 사용자에 대 한 인증 환경을 브랜드.

템플릿을 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [JavaScript 사용자 정 및 많은 새 기능에 이제 Azure AD B2C에](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)입니다.

---

## <a name="january-2019"></a>2019년 1월

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>일회용 암호 인증을 사용하여 Active Directory B2B 공동 작업(공용 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C

Azure AD MSA(Microsoft 계정) 또는 Google 페더레이션 등의 다른 수단을 통해 인증할 수 없는 B2B 게스트 사용자를 위해 OTP(일회용 암호 인증)를 도입했습니다. 이 새로운 인증 방법을 사용하면 게스트 사용자가 새 Microsoft 계정을 만들 필요가 없습니다. 그 대신 초대를 사용하거나 공유 리소스에 액세스하는 동안 게스트 사용자는 임시 코드를 이메일 주소로 보내 줄 것을 요청할 수 있습니다. 게스트 사용자는 이 임시 코드를 사용하여 계속 로그인할 수 있습니다.

자세한 내용은 [이메일 일회용 암호 인증(미리 보기)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) 및 [Azure AD가 계정이 있는 모든 사용자가 원활하게 공유 및 공동 작업하도록 해](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) 블로그를 참조하세요.

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>새 Azure AD 애플리케이션 프록시 쿠키 설정

**유형:** 새로운 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

애플리케이션 프록시를 통해 게시되는 앱에 사용 가능한 새 쿠키 설정 3개가 도입되었습니다.

- **HTTP 전용 쿠키 사용.** 애플리케이션 프록시 액세스 및 세션 쿠키에 **HTTPOnly** 플래그를 설정합니다. 이 설정을 켜면 클라이언트 쪽 스크립팅을 통한 쿠키 복사나 수정을 방지할 수 있는 등 추가적인 보안 이점이 제공됩니다. 추가 이점을 활용하려면 **예**를 선택하여 이 플래그를 켜는 것이 좋습니다.

- **보안 쿠키 사용.** 애플리케이션 프록시 액세스 및 세션 쿠키에 **Secure** 플래그를 설정합니다. 이 설정을 켜면 HTTPS 등의 TLS 보안 채널을 통해서만 쿠키가 전송되므로 추가적인 보안 이점이 제공됩니다. 추가 이점을 활용하려면 **예**를 선택하여 이 플래그를 켜는 것이 좋습니다.

- **영구적 쿠키 사용.** 웹 브라우저를 닫을 때 액세스 쿠키가 만료되지 않습니다. 이러한 쿠키는 액세스 토큰의 수명 동안 유지됩니다. 그러나 만료 시간이 되거나 사용자가 쿠키를 수동으로 삭제하면 쿠키가 재설정됩니다. 기본 설정인 **아니요**를 유지하고 프로세스 간에 쿠키를 공유하지 않는 구형 앱에서만 설정을 켜는 것이 좋습니다.

새 쿠키에 대한 자세한 내용은 [Azure Active Directory에서 온-프레미스 애플리케이션에 액세스하기 위한 쿠키 설정](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)을 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱 - 2019년 1월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2019년 1월에 페더레이션이 지원되는 다음의 신규 앱 35개가 앱 갤러리에 추가되었습니다.

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial)를 [재능 색상표](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial)를 [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial)를 [Cisco 포괄적인](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler 인터넷 액세스 관리자에 게](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [만료 알림](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial)를 [InstaVR 뷰어](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial)를 [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [동사](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora)합니다 [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital 닫기](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice 도구 키트](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ 클라우드 서비스 PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial)를 [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial)를 [CallPlease](https://webapp.callplease.com/create-account/create-account.html)를 [GTNexus SSO 시스템](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial)하십시오 [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial)를 [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [엔터프라이즈용 아레스](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [Office 365에 대 한 K2](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial)하십시오 [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial)를 [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas 플랫폼](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>새로운 Azure AD ID 보호 향상 기능(공개 미리 보기)

**유형:** 변경된 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호

Azure AD ID 보호 공개 미리 보기 제품에는 다음과 같은 향상 기능이 추가되었습니다.

- 업데이트되고 더욱 긴밀하게 통합된 사용자 인터페이스

- 추가 API

- 기계 학습을 통해 향상된 위험 평가

- 제품 전반에 걸쳐 일관된 위험한 사용자 및 위험한 로그인 정보 제공

향상 기능에 대한 자세한 내용은 [갱신된 Azure Active Directory ID 보호 소개](https://aka.ms/IdentityProtectionDocs)를 참조하세요. 여기서 자세한 내용을 알아보고 제품 내 프롬프트를 통해 정보를 공유하는 방법을 파악할 수 있습니다.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>iOS 및 Android 디바이스의 Microsoft Authenticator 앱용 신규 앱 잠금 기능

**유형:** 새로운 기능  
**서비스 범주:** Microsoft Authenticator 앱  
**제품 기능:** ID 보안 및 보호

일회용 암호, 앱 정보 및 앱 설정을 더욱 안전하게 유지하려는 경우 Microsoft Authenticator 앱에서 앱 잠금 기능을 설정할 수 있습니다. 앱 잠금을 설정하면 Microsoft Authenticator 앱을 열 때마다 PIN이나 생체 인식 정보를 사용하여 인증을 하라는 메시지가 표시됩니다.

자세한 내용은 [Microsoft Authenticator 앱 FAQ](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq)를 참조하세요.

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>향상된 Azure AD PIM(Privileged Identity Management) 내보내기 기능

**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management

이제 PIM(Privileged Identity Management) 관리자는 특정 리소스에 대한 모든 활성/적격 역할 할당(모든 자식 리소스에 대한 역할 할당 포함)을 내보낼 수 있습니다. 이전에는 관리자가 구독의 역할 할당 전체 목록을 가져오기가 어려웠으며, 각 특정 리소스의 역할 할당을 내보내야 했습니다.

자세한 내용은 [PIM에서 Azure 리소스 역할에 대한 활동 및 감사 기록 보기](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)를 참조하세요.

---

## <a name="novemberdecember-2018"></a>2018년 11월/12월

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>동기화 범위에서 제거된 사용자는 더 이상 클라우드 전용 계정으로 전환되지 않습니다.

**유형:** 수정됨  
**서비스 범주:** 사용자 관리  
**제품 기능:** 디렉터리

>[!Important]
>이 수정으로 인한 불편이 접수되어 해당 내용을 파악하였습니다. 따라서, 조직 내에서 더 쉽게 구현할 수 있게 수정할 수 있을 때까지 이 변경 사항을 다시 되돌렸습니다.

AD DS(Active Directory Domain Services) 개체가 동기화 범위에서 제외된 다음, 다음과 같은 동기화 주기에서 Azure AD의 휴지통으로 이동될 때 사용자의 DirSyncEnabled 플래그가 **False**로 잘못 전환되는 버그를 수정했습니다. 이렇게 수정하면 사용자가 동기화 범위에서 제외되고 나중에 Azure AD 휴지통에서 복원되는 경우 인증 원본(SoA)이 온-프레미스 AD로 유지되므로 예상대로 사용자 계정은 온-프레미스 AD에서 동기화된 상태를 유지하고, 클라우드에서 관리될 수 없습니다.

이 문제를 해결하기 전에 DirSyncEnabled 플래그가 False로 전환되는 문제가 발생했습니다. 이로 인해 이러한 계정이 클라우드 전용 개체로 변환되고 클라우드에서 관리될 수 있다는 잘못된 인상을 주었습니다. 그러나 해당 계정은 해당 SoA를 온-프레미스 및 온-프레미스 AD에서 비롯되어 동기화된 모든 속성(섀도 특성)으로 보유했습니다. 이 조건으로 인해 이러한 계정을 AD에서 동기화되도록 처리하는 Azure AD 및 기타 클라우드 워크로드(예: Exchange Online)에서 여러 문제가 발생했지만 이제 클라우드 전용 계정처럼 작동하고 있습니다.

이때 AD의 동기화 계정을 클라우드 전용 계정으로 진정하게 변환하는 유일한 방법은 테넌트 수준에서 DirSync를 사용하지 않도록 설정하는 것입니다. 그러면 SoA를 전송하는 백 엔드 작업을 트리거하게 됩니다. 이러한 형식으로 SoA를 변경하려면 모든 온-프레미스 관련 특성(예: LastDirSyncTime 및 섀도 특성)을 정리하고 다른 클라우드 워크로드에 신호를 보내서 해당 개체를 클라우드 전용 계정으로 변환해야 합니다(단, 이에 국한되지 않음).

이렇게 수정하면 결과적으로 AD에서 동기화된 사용자의 ImmutableID 특성에 대한 직접 업데이트를 방지합니다. 이 작업은 과거의 일부 시나리오에 요구되었습니다. Azure AD에 있는 개체의 ImmutableID는 이름에서 알 수 있듯이 기본적으로 변경할 수 없습니다. Azure AD Connect Health 및 Azure AD Connect 동기화 클라이언트에서 구현된 새 기능은 다음과 같은 시나리오를 해결하기 위해 제공됩니다.

- **많은 사용자에 대한 단계별 접근 방식의 대규모 ImmutableID 업데이트**
  
  예를 들어, 오랫동안 AD DS 포리스트 간 마이그레이션을 수행해야 합니다. 해결 방법: Azure AD Connect를 사용하여 **원본 앵커를 구성**하고 사용자가 마이그레이션한 대로 기존 ImmutableID 값을 Azure AD에서 새 포리스트에 있는 로컬 AD DS 사용자의 ms-DS-Consistency-Guid 특성으로 복사합니다. 자세한 내용은 [ms-DS-ConsistencyGuid를 sourceAnchor로 사용](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)을 참조하세요.

- **많은 사용자에 대한 동시 대규모 ImmutableID 업데이트**

  예를 들어, Azure AD Connect를 구현하는 동안 실수가 있었기 때문에 이제 SourceAnchor 특성을 변경해야 합니다. 해결 방법: 테넌트 수준에서 DirSync를 사용하지 않도록 설정하고 잘못된 모든 ImmutableID 값을 지웁니다. 자세한 내용은 [Office 365에서 디렉터리 동기화 끄기](/office365/enterprise/turn-off-directory-synchronization)를 참조하세요.

- **Azure AD에서 기존 사용자와 온-프레미스 사용자 다시 일치** 예를 들어 AD DS에서 다시 생성된 사용자가 기존 Azure AD 계정(분리된 개체)를 사용하여 다시 일치시키는 대신 Azure AD 계정에 중복을 생성합니다. 해결 방법: Azure Portal에서 Azure AD Connect Health를 사용하여 원본 앵커/ImmutableID를 다시 매핑합니다. 자세한 내용은 [분리된 개체 시나리오](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario)를 참조하세요.

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>호환성이 손상되는 변경: Azure Monitor를 통한 감사 및 로그인 로그 스키마에 대한 업데이트

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

현재 Azure Monitor를 통해 감사 및 로그인 로그 스트림을 둘 다 게시하고 있으므로 SIEM 도구나 Log Analytics를 사용하여 로그 파일을 원활하게 통합할 수 있습니다. 피드백에 따라, 그리고 이 기능의 일반 공급 알림에 대한 준비 작업으로 스키마를 다음과 같이 변경하고 있습니다. 이러한 스키마 변경 내용 및 관련된 문서 업데이트는 1월 첫째 주까지 수행될 예정입니다.

#### <a name="new-fields-in-the-audit-schema"></a>감사 스키마의 새 필드
리소스에 대해 수행된 작업 유형을 제공하기 위해 **작업 유형** 필드가 추가됩니다. 예를 들어 **추가**, **업데이트** 또는 **삭제**입니다.

#### <a name="changed-fields-in-the-audit-schema"></a>감사 스키마의 변경된 필드
감사 스키마에서 다음 필드가 변경됩니다.

|필드 이름|변경 내용|이전 값|새 값|
|----------|------------|----------|----------|
|Category|이전에는 **서비스 이름** 필드였습니다. 이제 **감사 범주** 필드입니다. **서비스 이름**이 **loggedByService** 필드로 이름이 변경되었습니다.|<ul><li>계정 프로비전</li><li>핵심 디렉터리</li><li>셀프 서비스 암호 재설정</li></ul>|<ul><li>사용자 관리</li><li>그룹 관리</li><li>앱 관리</li></ul>|
|targetResources|최상위 수준에 **TargetResourceType**을 포함합니다.|&nbsp;|<ul><li>정책</li><li>앱</li><li>사용자</li><li>그룹</li></ul>|
|loggedByService|감사 로그를 생성한 서비스의 이름을 제공합니다.|Null|<ul><li>계정 프로비전</li><li>핵심 디렉터리</li><li>셀프 서비스 암호 재설정</li></ul>|
|결과|감사 로그의 결과를 제공합니다. 이전에는 열거형이었지만 이제 실제 값을 표시합니다.|<ul><li>0</li><li>1</li></ul>|<ul><li>성공</li><li>실패</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>로그인 스키마의 변경된 필드
로그인 스키마에서 다음 필드가 변경됩니다.

|필드 이름|변경 내용|이전 값|새 값|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|이전에는 **conditionalaccessPolicies** 필드였습니다. 이제 **appliedConditionalAccessPolicies** 필드입니다.|변경 내용 없음|변경 내용 없음|
|conditionalAccessStatus|로그인 시 조건부 액세스 정책 상태의 결과를 제공합니다. 이전에는 열거형이었지만 이제 실제 값을 표시합니다.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>성공</li><li>실패</li><li>적용되지 않음</li><li>사용 안 함</li></ul>|
|appliedConditionalAccessPolicies: 결과|로그인 시 개별 조건부 액세스 정책 상태의 결과를 제공합니다. 이전에는 열거형이었지만 이제 실제 값을 표시합니다.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>성공</li><li>실패</li><li>적용되지 않음</li><li>사용 안 함</li></ul>|

스키마에 대한 자세한 내용은 [Azure Monitor(미리 보기)에서 Azure AD 감사 로그 스키마 해석](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)을 참조하세요.

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>감독된 기계 학습 모델 및 위험 점수 엔진의 ID 보호 개선

**유형:** 변경된 기능  
**서비스 범주:** ID 보호  
**제품 기능:** 위험 점수

ID 보호 관련 사용자 및 로그인 위험 평가 엔진이 개선되어 사용자 위험 정확도 및 적용 범위를 향상하는 데 도움이 됩니다. 관리자는 사용자 위험 수준이 특정 검색의 위험 수준에 더 이상 직접 연결되지 않으며 위험한 로그인 이벤트 수와 수준이 증가함을 확인할 수도 있습니다.

이제 위험 검색이 감독된 기계 학습 모델에서 평가되며, 이 모델은 사용자 로그인 및 검색 패턴의 추가 기능을 사용하여 사용자 위험을 계산합니다. 이 모델을 기준으로, 관리자는 사용자와 관련된 검색 위험이 낮거나 중간 수준이더라도 위험 점수가 높은 사용자를 발견할 수 있습니다. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>관리자는 Microsoft 인증자 앱(공개 미리 보기)을 사용하여 자신의 암호를 재설정할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 사용자 인증

이제 Azure AD 관리자는 Microsoft 인증자 앱 알림이나 임의의 모바일 인증자 앱 또는 하드웨어 토큰의 코드를 사용하여 자신의 암호를 재설정할 수 있습니다. 자신의 암호를 재설정하기 위해 관리자는 이제 다음 두 가지 방법 중 하나를 사용할 수 있습니다.

- Microsoft 인증자 앱 알림

- 기타 모바일 인증자 앱/하드웨어 토큰 코드

- Email

- 전화 통화

- 문자 메시지

Microsoft 인증자 앱을 사용하여 암호를 재설정하는 방법에 대한 자세한 내용은 [Azure AD 셀프 서비스 암호 재설정 - 모바일 앱 및 SSPR(미리 보기)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)을 참조하세요.

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>새 Azure AD 클라우드 디바이스 관리자 역할(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 디바이스 등록 및 관리  
**제품 기능:** Access Control

관리자는 클라우드 디바이스 관리자 작업을 수행할 새 클라우드 디바이스 관리자 역할에 사용자를 할당할 수 있습니다. 클라우드 디바이스 관리자 역할이 할당된 사용자는 Azure AD에서 디바이스를 사용/사용하지 않도록 설정하고, 삭제하고, Azure Portal에서 Windows 10 BitLocker 키(있는 경우)를 읽을 수 있습니다.

역할 및 사용 권한에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)을 참조하세요.

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Azure AD의 새 작업 타임스탬프(공개 미리 보기)를 사용하여 디바이스 관리

**유형:** 새로운 기능  
**서비스 범주:** 디바이스 등록 및 관리  
**제품 기능:** 디바이스 수명 주기 관리

사용자 환경에서 부실 디바이스가 사용되지 않도록 Azure AD에서 시간 경과에 따라 조직의 디바이스를 새로 고치고 사용 중지해야 한다는 것을 알게 되었습니다. 이 프로세스를 지원하기 위해 Azure AD는 이제 새 작업 타임스탬프로 디바이스를 업데이트하여 디바이스 수명 주기를 관리할 수 있도록 지원합니다.

이 타임스탬프를 가져오고 사용하는 방법에 대한 자세한 내용은 [방법: Azure AD에서 부실 디바이스 관리](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)를 참조하세요.

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>관리자는 각 디바이스에서 사용 약관에 동의하도록 사용자에게 요구할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스
 
이제 관리자는 **모든 디바이스에서 사용자 동의 요구** 옵션을 켜서 사용자가 테넌트에서 사용 중인 모든 디바이스에서 사용 약관에 동의하도록 요구할 수 있습니다.

자세한 내용은 [Azure Active Directory 사용 약관 기능의 디바이스별 사용 약관 섹션](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use)을 참조하세요.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>관리자는 되풀이 일정에 따라 만료되도록 사용 약관을 구성할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스
 

이제 관리자는 **콘텐츠 만료** 옵션을 켜서 지정된 되풀이 일정에 따라 모든 사용자에 대해 사용 약관이 만료되도록 설정할 수 있습니다. 일정은 매년, 6개월마다, 매분기 또는 매월일 수 있습니다. 사용 약관이 만료되면 사용자가 다시 동의해야 합니다.

자세한 내용은 [Azure Active Directory 사용 약관 기능의 사용 약관 추가 섹션](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use)을 참조하세요.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>관리자는 각 사용자의 일정에 따라 만료되도록 사용 약관을 구성할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스

이제 관리자는 사용자가 사용 약관에 다시 동의해야 하는 기간을 지정할 수 있습니다. 예를 들어 관리자는 사용자가 90일마다 사용 약관에 다시 동의해야 하도록 지정할 수 있습니다.

자세한 내용은 [Azure Active Directory 사용 약관 기능의 사용 약관 추가 섹션](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use)을 참조하세요.
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory 역할에 대한 새 Azure AD PIM(Privileged Identity Management) 메일

**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
Azure AD PIM(Privileged Identity Management)를 사용하는 고객은 이제 지난 7일간의 다음 정보를 포함하여 매주 요약 메일을 받을 수 있습니다.

- 상위 적격 및 영구 역할 할당 개요

- 역할을 활성화하는 사용자 수

- PIM의 역할에 할당된 사용자 수

- PIM 외부의 역할에 할당된 사용자 수

- PIM의 “영구적” 사용자 수

PIM 및 사용 가능한 메일 알림에 대한 자세한 내용은 [PIM의 메일 알림](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)을 참조하세요.

---

### <a name="group-based-licensing-is-now-generally-available"></a>그룹 기반 라이선스 출시

**유형:** 변경된 기능  
**서비스 범주:** 기타  
**제품 기능:** 디렉터리

그룹 기반 라이선스의 공개 미리 보기가 종료되고 이제 일반 공급됩니다. 이 일반 릴리스의 일부로, 이 기능의 확장성이 개선되었으며 단일 사용자에 대한 그룹 기반 라이선스 할당을 다시 처리하는 기능과 Office 365 E3/A3 라이선스로 그룹 기반 라이선스를 사용하는 기능이 추가되었습니다.

그룹 기반 라이선스에 대한 자세한 내용은 [Azure Active Directory의 그룹 기반 라이선스란?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)을 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 11월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2018년 11월에 페더레이션이 지원되는 신규 앱 26개가 앱 갤러리에 추가되었습니다.

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager(ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

## <a name="october-2018"></a>2018년 10월

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD 로그는 이제 Azure Log Analytics(공개 미리 보기)에서 사용됩니다.

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

이제 Azure Log Analytics에 Azure AD 로그를 전달할 수 있게 되었습니다. 많은 요청이 있었던 이 기능은 인프라를 모니터링하는 데 도움이 줄 뿐만 아니라 비즈니스, 작업 및 보안을 위해 분석에 보다 쉽게 액세스할 수 있도록 합니다. 자세한 내용은 [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)(이제 Azure Log Analytics에서 Azure Active Directory 활동 로그를 사용할 수 있음) 블로그를 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 10월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2018년 10월에 페더레이션이 지원되는 다음과 같은 신규 앱 14개가 앱 갤러리에 추가되었습니다.

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services 이메일 알림

**유형:** 새로운 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services

Azure AD Domain Services는 관리되는 도메인의 구성 오류 또는 문제점에 대한 경고를 Azure Portal에 제공합니다. 이러한 경고에는 지원 서비스에 문의하지 않고 문제를 해결할 수 있는 단계별 가이드가 포함됩니다.

10월부터, 관리되는 도메인에 대한 알림 설정을 사용자 지정할 수 있으므로 새 경고가 발생할 때 지정된 사용자 그룹으로 이메일이 전송됩니다. 따라서 포털에서 업데이트를 지속적으로 확인할 필요가 없습니다.

자세한 내용은 [Azure AD Domain Services의 알림 설정](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications)을 참조하세요.

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD 포털에서는 ForceDelete 도메인 API를 사용하여 사용자 지정 도메인을 삭제하도록 지원합니다. 

**유형:** 변경된 기능  
**서비스 범주:** 디렉터리 관리  
**제품 기능:** 디렉터리

이제 사용자, 그룹 및 앱과 같은 참조의 이름을 사용자 지정 도메인 이름(contoso.com)에서 초기 기본 도메인 이름(contoso.onmicrosoft.com)으로 비동기식으로 다시 바꾸는 방식으로, ForceDelete 도메인 API를 통해 사용자 지정 도메인 이름을 삭제할 수 있습니다.

이러한 변경을 통해 조직이 더 이상 해당 이름을 사용하지 않는 경우 또는 다른 Azure AD와 해당 도메인 이름을 사용해야 하는 경우, 사용자 지정 도메인 이름을 보다 신속하게 삭제할 수 있습니다.

자세한 내용은 [사용자 지정 도메인 이름 삭제](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name)를 참조하세요.

---

## <a name="september-2018"></a>2018년 9월
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>동적 그룹에 대한 관리자 역할 권한 업데이트

**유형:** 수정됨  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

이제 그룹의 소유자가 되지 않아도 특정 관리자 역할이 동적 구성원 규칙을 만들고 업데이트할 수 있도록 문제가 해결되었습니다.

역할은 다음과 같습니다.

- 전역 관리자

- Intune 관리자

- 사용자 관리자

자세한 내용은 [동적 그룹 만들기 및 상태 확인](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)을 참조하세요.

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>일부 타사 앱에 대한 간소화된 SSO(Single Sign-On) 구성 설정

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

각 앱 구성의 고유한 특성 때문에 SaaS(Software as a Service) 앱에 대한 SSO(Single Sign-On)를 설정하기란 쉽지 않은 일입니다. Microsoft는 다음 타사 SaaS 앱의 SSO 구성 설정을 자동으로 채우도록 간소화된 구성 환경을 빌드했습니다.

- Zendesk

- ArcGis Online

- Jamf Pro

이 원클릭 환경을 사용하려면 앱의 **Azure Portal** > **SSO 구성** 페이지로 이동합니다. 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)을 참조하세요.

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory - 데이터가 있는 위치 페이지

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** GoLocal

**Azure Active Directory - 데이터가 있는 위치** 페이지에서 회사가 있는 지역을 선택하고, 모든 Azure AD 서비스의 Azure AD 미사용 데이터를 저장하는 Azure 데이터 센터가 어디인지 살펴봅니다. 회사가 있는 지역의 특정 Azure AD 서비스를 기준으로 정보를 필터링할 수 있습니다.

이 기능 및 자세한 정보에 액세스하려면 [Azure Active Directory - 데이터가 있는 위치](https://aka.ms/AADDataMap)를 참조하세요.

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>내 앱 액세스 패널에 사용할 수 있는 새 배포 계획

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO

내 앱 액세스 패널에 사용할 수 있는 새 배포 계획을 확인하세요(https://aka.ms/deploymentplans).
내 앱 액세스 패널은 사용자에게 앱을 찾고 액세스할 수 있는 단일 위치를 제공합니다. 또한 이 포털은 앱 및 그룹에 대한 액세스 권한 요청, 다른 사람을 대신하여 리소스에 대한 액세스 권한 관리와 같은 셀프 서비스 기회를 사용자에게 제공합니다.

자세한 내용은 [My Apps 포털이란?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)을 참조하세요.

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Azure Portal의 [로그인 로그] 페이지에 있는 새로운 [문제 해결 및 지원] 탭

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

Azure Portal의 **로그인** 페이지에 있는 새로운 **문제 해결 및 지원** 탭의 목적은 관리자 및 지원 엔지니어가 Azure AD 로그인 관련 문제를 해결할 수 있게 도와주는 것입니다. 이 새 탭은 문제 해결에 도움이 되는 오류 코드, 오류 메시지 및 수정 권장 사항(있는 경우)을 제공합니다. 문제를 해결할 수 없는 경우 지원 티켓의 로그 파일에 대한 **요청 ID** 및 **날짜(UTC)** 필드를 채우는 **클립보드에 복사** 환경을 사용하여 지원 티켓을 만들 수 있는 새로운 방법을 제공합니다.  

![로그인 로그 새 탭을 보여 주는](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>동적 구성원 규칙을 만드는 데 사용되는 사용자 지정 확장 속성에 대한 향상된 지원

**유형:** 변경된 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

이 업데이트부터는 동적 사용자 그룹 규칙 작성기에서 **사용자 지정 확장 속성 가져오기**를 클릭하고, 고유한 앱 ID를 입력하고, 사용자에 대한 동적 구성원 규칙을 만들 때 사용할 사용자 지정 확장 속성 전체 목록을 받을 수 있습니다. 이 목록을 새로 고침하여 해당 앱에 대한 새로운 사용자 지정 확장 속성을 가져올 수도 있습니다.

동적 구성원 규칙에 대한 사용자 지정 확장 속성을 사용하는 방법에 대한 자세한 내용은 [확장 속성 및 사용자 지정 확장 속성](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)을 참조하세요.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스의 새로운 승인된 클라이언트 앱

**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

[승인된 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement) 목록에는 다음과 같은 앱이 있습니다.

- Microsoft To-Do

- Microsoft Stream

자세한 내용은 다음을 참조하세요.

- [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Windows 7/8/8.1 잠금 화면에서 셀프 서비스 암호 재설정에 대한 새로운 지원

**유형:** 새로운 기능  
**서비스 범주:** SSPR  
**제품 기능:** 사용자 인증

이 새 기능을 설치하면 Windows 7, Windows 8 또는 Windows 8.1을 실행하는 디바이스의 **잠금** 화면에 사용자가 암호를 다시 설정할 수 있는 링크가 표시됩니다. 이 링크를 클릭하면 웹 브라우저와 동일한 암호 재설정 흐름이 사용자에게 제공됩니다.

자세한 내용은 [Windows 7, 8 및 8.1에서 암호 재설정을 사용하는 방법](https://aka.ms/ssprforwindows78)을 참조하세요.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>변경 알림: 인증 코드를 더 이상 다시 사용할 수 없습니다. 

**유형:** 변경 계획  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

2018년 11월 15일부터 Azure AD는 앱에서 이전에 사용된 인증 코드를 더 이상 수락하지 않습니다. 이 보안 변경은 Azure AD를 OAuth 사양에 맞추는 데 도움이 되며 v1 및 v2 엔드포인트 모두에 적용됩니다.

앱에서 여러 리소스에 대한 토큰을 얻기 위해 인증 코드를 재사용하는 경우에는 코드를 사용하여 새로 고침 토큰을 얻은 다음, 이 새로 고침 토큰을 사용하여 다른 리소스에 대한 추가 토큰을 얻는 것이 좋습니다. 인증 코드는 한 번만 사용할 수 있지만 새로 고침 토큰은 여러 리소스에서 여러 번 사용할 수 있습니다. OAuth 코드 흐름 중에 인증 코드를 다시 사용하려고 하는 앱에서는 invalid_grant 오류가 발생합니다.

이 변경 내용 및 기타 프로토콜 관련 변경 내용은 [인증 관련 새 기능 전체 목록](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)을 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 9월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2018년 9월에 페더레이션이 지원되는 신규 앱 16개가 앱 갤러리에 추가되었습니다.

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="support-for-additional-claims-transformations-methods"></a>추가 클레임 변환 메서드 지원

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

SAML 기반 **Single Sign-On 구성** 페이지에서 SAML 토큰에 적용할 수 있는 새로운 클레임 변환 메서드 ToLower() 및 ToUpper()가 도입되었습니다.

자세한 내용은 [Azure AD의 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임을 사용자 지정하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조하세요.

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>업데이트된 SAML 기반 앱 구성 UI(미리 보기)

**유형:** 변경된 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

업데이트된 SAML 기반 앱 구성 UI의 일부로 다음이 제공됩니다.

- SAML 기반 앱을 구성하는 업데이트된 연습 환경.

- 누락되거나 잘못된 구성에 대한 향상된 가시성.

- 만료 인증서 알림에 대한 여러 이메일 주소를 추가하는 기능.

- 새로운 클레임 변환 메서드 ToLower(), ToUpper() 등.

- 엔터프라이즈 앱의 고유한 토큰 서명 인증서를 업로드하는 방법.

- SAML 앱의 NameID 형식을 설정하는 방법 및 NameID 값을 디렉터리 확장으로 설정하는 방법.

이 업데이트된 보기를 켜려면 **Single Sign-On** 페이지 맨 위에서 **새 환경 사용해 보기** 링크를 클릭합니다. 자세한 내용은 [자습서: Azure Active Directory에서 애플리케이션에 대한 SAML 기반 Single Sign-On 구성](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)을 참조하세요.

---

## <a name="august-2018"></a>2018년 8월

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Azure Active Directory IP 주소 범위 변경

**유형:** 변경 계획  
**서비스 범주:** 기타  
**제품 기능:** 플랫폼

Azure AD에 더 큰 IP 범위를 도입하려고 합니다. 따라서 방화벽, 라우터 또는 네트워크 보안 그룹에 대한 Azure AD IP 주소 범위를 구성한 경우 업데이트해야 합니다. 이 업데이트는 Azure AD에서 새 엔드포인트를 추가하는 경우에 방화벽, 라우터 또는 네트워크 보안 그룹 IP 범위 구성을 다시 변경할 필요가 없도록 하기 위해 진행하고 있습니다. 

네트워크 트래픽은 향후 2개월에 걸쳐 이러한 새 범위로 이동될 것입니다. 서비스를 중단 없이 계속 사용하려면 2018년 9월 10일 전에 이러한 업데이트된 값을 IP 주소에 추가해야 합니다.

- 20.190.128.0/18 

- 40.126.0.0/18 

모든 네트워크 트래픽이 새 범위로 이동될 때까지 이전 IP 주소 범위를 제거하지 않는 것이 좋습니다. 이러한 이동에 대한 업데이트 내용을 확인하고 이전 범위를 제거할 수 있는 시기를 알아보려면 [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)를 참조하세요.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>변경 알림: 인증 코드를 더 이상 다시 사용할 수 없습니다. 

**유형:** 변경 계획  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

2018년 11월 15일부터 Azure AD는 앱에서 이전에 사용된 인증 코드를 더 이상 수락하지 않습니다. 이 보안 변경은 Azure AD를 OAuth 사양에 맞추는 데 도움이 되며 v1 및 v2 엔드포인트 모두에 적용됩니다.

앱에서 여러 리소스에 대한 토큰을 얻기 위해 인증 코드를 재사용하는 경우에는 코드를 사용하여 새로 고침 토큰을 얻은 다음, 이 새로 고침 토큰을 사용하여 다른 리소스에 대한 추가 토큰을 얻는 것이 좋습니다. 인증 코드는 한 번만 사용할 수 있지만 새로 고침 토큰은 여러 리소스에서 여러 번 사용할 수 있습니다. OAuth 코드 흐름 중에 인증 코드를 다시 사용하려고 하는 앱에서는 invalid_grant 오류가 발생합니다.

이 변경 내용 및 기타 프로토콜 관련 변경 내용은 [인증 관련 새 기능 전체 목록](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)을 참조하세요.
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>SSPR(셀프 서비스 암호) 및 MFA(Multi-Factor Authentication)를 위한 융합형 보안 정보 관리

**유형:** 새로운 기능  
**서비스 범주:** SSPR  
**제품 기능:** 사용자 인증
 
이 기능을 사용하면 SSPR 및 MFA에 대한 보안 정보(예: 전화 번호, 모바일 앱 등)를 두 개의 다른 위치에서 관리하던 이전 방식과 달리, 단일 위치 및 환경에서 관리할 수 있습니다.

융합형 환경은 SSPR 또는 MFA 사용자에게도 유용합니다. 또한 조직에서 MFA 또는 SSPR 등록이 적용되지 않는 경우에도 내 앱 포털의 조직에 허용되는 MFA 또는 SSPR 보안 정보 메서드를 사용자가 등록할 수 있습니다.

이 기능은 옵트인 공개 미리 보기입니다. 관리자는 선택한 그룹 또는 테넌트의 모든 사용자에게 원하는 경우 환경을 적용할 수 있습니다. 융합형 환경에 대한 자세한 내용은 [융합형 환경 블로그](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)를 참조하세요.

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD 애플리케이션 프록시 앱에서 새로운 HTTP 전용 쿠키 설정

**유형:** 새로운 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

애플리케이션 프록시 앱에는 **HTTP 전용 쿠키**라는 새로운 설정이 있습니다. 이 설정을 사용하면 애플리케이션 프록시 액세스 및 세션 쿠키 모두에 대한 HTTP 응답 헤더에 HTTPOnly 플래그를 포함시켜서 추가 보안을 제공하여 클라이언트 쪽 스크립트에서 쿠키에 대한 액세스를 중지하고 쿠키 복사 또는 수정과 같은 작업을 방지할 수 있습니다. 전에는 이 플래그가 사용되지 않았지만 부적절한 수정을 방지하기 위해 SSL 연결을 사용하여 항상 쿠키가 암호화되고 전송되었습니다.

이 설정은 원격 데스크톱과 같은 ActiveX 컨트롤을 사용하는 응용 프로그램과 호환되지 않습니다. 이런 경우에 해당하면 이 설정을 해제하는 것이 좋습니다.

HTTP 전용 쿠키 설정에 대한 자세한 내용은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal)를 참조하세요.

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure 리소스에 대한 PIM(Privileged Identity Management)은 관리 그룹 리소스 종류를 지원합니다.

**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
이제 Just-In-Time 활성화 및 할당 설정을 구독, 리소스 그룹 및 리소스(예: VM, App Services 등)에 적용하듯이 관리 그룹 리소스 종류에 적용할 수 있습니다. 또한 관리 그룹에 대한 관리자 액세스를 제공하는 역할이 있는 사람은 PIM에서 해당 리소스를 검색하고 관리할 수 있습니다.

PIM 및 Azure 리소스에 대한 자세한 내용은 [Privileged Identity Management를 사용하여 Azure 리소스 검색 및 관리](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)를 참조하세요.
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>애플리케이션 액세스(미리 보기)를 사용하면 Azure AD 포털에 빠르게 액세스할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
현재 PIM을 사용하여 역할을 활성화하는 경우 권한이 적용되려면 10분 넘게 걸릴 수 있습니다. 현재 미리 보기 상태인 애플리케이션 액세스를 사용하도록 선택하면 활성화 요청이 완료되자마자 관리자가 Azure AD 포털에 액세스할 수 있습니다.

현재 애플리케이션 액세스는 Azure AD 포털 환경과 Azure 리소스만 지원합니다. PIM 및 애플리케이션 액세스에 대한 자세한 내용은 [Azure AD Privileged Identity Management란?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)을 참조하세요.
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 8월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2018년 8월에 페더레이션이 지원되는 신규 앱 16개가 앱 갤러리에 추가되었습니다.

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>이제 Azure AD 애플리케이션 프록시에 Native Tableau 지원이 제공됩니다.

**유형:** 변경된 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

사전 인증 프로토콜을 위해 OpenID Connect가 OAuth 2.0 코드 권한 부여 프로토콜로 업데이트되었기 때문에 애플리케이션 프록시와 Tableau를 사용하기 위해 추가 구성을 수행할 필요가 없습니다. 프로토콜이 변경됨으로써 애플리케이션 프록시가 HTTP 리디렉션(JavaScript 및 HTML 태그에서 일반적으로 지원됨)만 사용하여 최신 앱을 더 잘 지원할 수 있습니다.

Tableau에 대한 기본 지원에 대한 자세한 내용은 [Azure AD 애플리케이션 프록시(기본 Tableau 지원)](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)을 참조하세요.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Azure Active Directory에서 Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가하도록 지원(미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C

조직에 Google과의 페더레이션을 설정하면 초대받은 Gmail 사용자가 MSA(개인 Microsoft 계정)나 Azure AD 계정을 만들 필요 없이 기존 Google 계정을 사용하여 공유 앱 및 리소스에 로그인하도록 할 수 있습니다.

이 기능은 옵트인 공개 미리 보기입니다. Google 페더레이션에 대한 자세한 내용은 [Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)를 참조하세요.

---
