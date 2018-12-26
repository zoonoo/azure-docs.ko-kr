---
title: 새로운 기능 Azure AD 릴리스 정보 | Microsoft Docs
description: Azure AD(Azure Active directory)의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능 및 예정된 변경 내용 등을 알아봅니다.
services: active-directory
author: eross-msft
manager: mtillman
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.openlocfilehash: dc4e421808ab0f79070224edea4b75f527affaf0
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426240"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능

>이 URL `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`를 ![RSS 아이콘](./media/whats-new/feed-icon-16x16.png) 피드 판독기에 복사하고 붙어넣어 업데이트를 위해 이 페이지를 다시 방문해야 할 때 알림을 받습니다.

Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월 이상된 항목을 찾으려는 경우 [Azure Active Directory의 새로운 기능에 대한 보관](whats-new-archive.md)에서 찾을 수 있습니다.

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
**제품 기능**: 타사 통합
 
2018년 10월에 페더레이션이 지원되는 다음과 같은 신규 앱 14개가 앱 갤러리에 추가되었습니다.

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 응용 프로그램을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열](https://aka.ms/azureadapprequest)을 참조하세요.

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

**유형:** 고정  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

이제 그룹의 소유자가 되지 않아도 특정 관리자 역할이 동적 구성원 규칙을 만들고 업데이트할 수 있도록 문제가 해결되었습니다.

역할은 다음과 같습니다.

- 글로벌 관리자 또는 회사 작성자

- Intune 서비스 관리자

- 사용자 계정 관리자

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

이 원클릭 환경을 사용하려면 앱의 **Azure Portal** > **SSO 구성** 페이지로 이동합니다. 자세한 내용은 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)을 참조하세요.

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

![새 탭을 사용하는 로그인 로그](media/whats-new/troubleshooting-and-support.png)

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

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>변경 알림: 권한 부여 코드를 더 이상 재사용할 수 없습니다. 

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
**제품 기능**: 타사 통합
 
2018년 9월에 페더레이션이 지원되는 신규 앱 16개가 앱 갤러리에 추가되었습니다.

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 응용 프로그램을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="support-for-additional-claims-transformations-methods"></a>추가 클레임 변환 메서드 지원

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

SAML 기반 **Single Sign-On 구성** 페이지에서 SAML 토큰에 적용할 수 있는 새로운 클레임 변환 메서드 ToLower() 및 ToUpper()가 도입되었습니다.

자세한 내용은 [Azure AD의 엔터프라이즈 응용 프로그램에 대한 SAML 토큰에 발급된 클레임을 사용자 지정하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조하세요.

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

이 업데이트된 보기를 켜려면 **Single Sign-On** 페이지 맨 위에서 **새 환경 사용해 보기** 링크를 클릭합니다. 자세한 내용은 [자습서: Azure Active Directory에서 응용 프로그램에 대한 SAML 기반 Single Sign-On 구성](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)을 참조하세요.

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

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>변경 알림: 권한 부여 코드를 더 이상 재사용할 수 없습니다. 

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

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD 응용 프로그램 프록시 앱에서 새로운 HTTP 전용 쿠키 설정

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어

응용 프로그램 프록시 앱에는 **HTTP 전용 쿠키**라는 새로운 설정이 있습니다. 이 설정을 사용하면 응용 프로그램 프록시 액세스 및 세션 쿠키 모두에 대한 HTTP 응답 헤더에 HTTPOnly 플래그를 포함시켜서 추가 보안을 제공하여 클라이언트 쪽 스크립트에서 쿠키에 대한 액세스를 중지하고 쿠키 복사 또는 수정과 같은 작업을 방지할 수 있습니다. 전에는 이 플래그가 사용되지 않았지만 부적절한 수정을 방지하기 위해 SSL 연결을 사용하여 항상 쿠키가 암호화되고 전송되었습니다.

이 설정은 원격 데스크톱과 같은 ActiveX 컨트롤을 사용하는 응용 프로그램과 호환되지 않습니다. 이런 경우에 해당하면 이 설정을 해제하는 것이 좋습니다.

HTTP 전용 쿠키 설정에 대한 자세한 내용은 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal)를 참조하세요.

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure 리소스에 대한 PIM(Privileged Identity Management)은 관리 그룹 리소스 종류를 지원합니다.

**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
이제 Just-In-Time 활성화 및 할당 설정을 구독, 리소스 그룹 및 리소스(예: VM, App Services 등)에 적용하듯이 관리 그룹 리소스 종류에 적용할 수 있습니다. 또한 관리 그룹에 대한 관리자 액세스를 제공하는 역할이 있는 사람은 PIM에서 해당 리소스를 검색하고 관리할 수 있습니다.

PIM 및 Azure 리소스에 대한 자세한 내용은 [Privileged Identity Management를 사용하여 Azure 리소스 검색 및 관리](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)를 참조하세요.
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>응용 프로그램 액세스(미리 보기)를 사용하면 Azure AD 포털에 빠르게 액세스할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
현재 PIM을 사용하여 역할을 활성화하는 경우 권한이 적용되려면 10분 넘게 걸릴 수 있습니다. 현재 미리 보기 상태인 응용 프로그램 액세스를 사용하도록 선택하면 활성화 요청이 완료되자마자 관리자가 Azure AD 포털에 액세스할 수 있습니다.

현재 응용 프로그램 액세스는 Azure AD 포털 환경과 Azure 리소스만 지원합니다. PIM 및 응용 프로그램 액세스에 대한 자세한 내용은 [Azure AD Privileged Identity Management란?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)을 참조하세요.
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 8월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2018년 8월에 페더레이션이 지원되는 신규 앱 16개가 앱 갤러리에 추가되었습니다.

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 응용 프로그램을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>이제 Azure AD 응용 프로그램 프록시에 Native Tableau 지원이 제공됩니다.

**유형:** 변경된 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어

사전 인증 프로토콜을 위해 OpenID Connect가 OAuth 2.0 코드 권한 부여 프로토콜로 업데이트되었기 때문에 응용 프로그램 프록시와 Tableau를 사용하기 위해 추가 구성을 수행할 필요가 없습니다. 프로토콜이 변경됨으로써 응용 프로그램 프록시가 HTTP 리디렉션(JavaScript 및 HTML 태그에서 일반적으로 지원됨)만 사용하여 최신 앱을 더 잘 지원할 수 있습니다.

Tableau에 대한 기본 지원에 대한 자세한 내용은 [Azure AD 응용 프로그램 프록시(기본 Tableau 지원)](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)을 참조하세요.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Azure Active Directory에서 Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가하도록 지원(미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C

조직에 Google과의 페더레이션을 설정하면 초대받은 Gmail 사용자가 MSA(개인 Microsoft 계정)나 Azure AD 계정을 만들 필요 없이 기존 Google 계정을 사용하여 공유 앱 및 리소스에 로그인하도록 할 수 있습니다.

이 기능은 옵트인 공개 미리 보기입니다. Google 페더레이션에 대한 자세한 내용은 [Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)를 참조하세요.

---

## <a name="july-2018"></a>2018년 7월

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Azure Active Directory 메일 알림에 대한 개선

**유형:** 변경된 기능  
**서비스 범주:** 기타  
**제품 기능:** ID 수명 주기 관리
 
이제 Azure AD(Azure Active Directory) 이메일은 다음 서비스에서 전송된 경우, 보낸 사람 이메일 주소 및 보낸 사람 표시 이름을 변경하고 업데이트된 디자인을 사용합니다.
 
- Azure AD 액세스 검토
- Azure AD Connect Health 
- Azure AD ID 보호 
- Azure AD Privileged Identity Management
- 엔터프라이즈 앱 인증서 만료 알림
- 엔터프라이즈 앱 서비스 프로비저닝 알림
 
메일 알림은 다음 메일 주소 및 표시 이름에서 전송됩니다.

- 메일 주소: azure-noreply@microsoft.com
- 표시 이름: Microsoft Azure
 
새 메일 디자인의 몇 가지 예제와 자세한 내용은 [Azure AD PIM의 메일 알림](https://go.microsoft.com/fwlink/?linkid=2005832)을 참조하세요.

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD 활동 로그는 이제 Azure Monitor를 통해 제공됩니다.

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

이제 Azure Monitor(Azure의 플랫폼 수준 모니터링 서비스)를 위한 공개 미리 보기에서 Azure AD 활동 로그를 사용할 수 있습니다. Azure Monitor는 장기 보존 및 매끄러운 통합 외에도 다음과 같은 향상된 기능을 제공합니다.

- 로그 파일을 사용자 고유의 Azure 저장소 계정에 라우팅하여 장기 보존.

- 사용자 지정 스크립트를 작성하거나 유지 관리할 필요 없이 매끄럽게 SIEM 통합.

- 사용자 고유의 사용자 지정 솔루션, 분석 도구 또는 인시던트 관리 솔루션과 매끄럽게 통합.

이러한 새 기능에 대한 자세한 내용은 [Azure Monitor 진단에서 Azure AD 활동 로그를 공개 미리 보기로 제공](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) 블로그 및 [Azure Monitor의 Azure Active Directory 활동 로그(미리 보기)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview) 문서를 참조하세요.

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Azure AD 로그인 보고서에 추가된 조건부 액세스 정보

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** ID 보안 및 보호
 
이 업데이트는 정책 결과와 함께 사용자가 로그인할 때 어떤 정책이 평가되는지 볼 수 있게 해줍니다. 또한 이제 보고서에 사용자가 사용한 클라이언트 앱 유형이 포함되므로 레거시 프로토콜 트래픽을 식별할 수 있습니다. 또한 이제 보고서 항목에서 상관 관계 ID를 검색할 수 있습니다. 이 ID는 사용자에게 표시되는 오류 메시지에서 찾을 수 있으며 일치하는 로그인 요청을 찾아서 해결하는 데 사용할 수 있습니다.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>로그인 활동 로그를 통해 레거시 인증 보기

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
로그인 활동 로그에 **클라이언트 앱** 필드가 도입되어 레거시 인증을 사용하는 사용자를 고객이 볼 수 있습니다. 고객은 로그인 MS Graph API를 사용하거나 **클라이언트 앱** 컨트롤을 사용하여 레거시 인증을 필터링할 수 있는 Azure AD 포털에서 로그인 활동 로그를 통해 이 정보에 액세스할 수 있습니다. 자세한 내용은 설명서를 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 7월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2018년 7월에 페더레이션이 지원되는 신규 앱 16개가 앱 갤러리에 추가되었습니다.

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 응용 프로그램을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>새 사용자 프로비전 SaaS 앱 통합 - 2018년 7월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 
Azure AD를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 SaaS 응용 프로그램에서 사용자 ID 만들기, 유지 관리 및 제거를 자동화할 수 있습니다. 2018년 7월에 Azure AD 앱 갤러리에서 다음 응용 프로그램에 대한 사용자 프로비전 지원을 추가했습니다.

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Azure AD 갤러리에서 사용자 프로비전을 지원하는 모든 응용 프로그램 목록은 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요.

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>동기화용 Connect Health - 분리되고 중복되는 특성 동기화 오류를 간단하게 해결할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** AD Connect  
**제품 기능:** 모니터링 및 보고
 
Azure AD Connect Health는 동기화 오류를 강조하고 해결할 수 있는 셀프 서비스 업데이트 관리를 도입합니다. 이 기능은 중복된 특성 동기화 오류를 해결하고 Azure AD에서 분리된 개체를 수정합니다. 이 진단은 다음과 같은 이점이 있습니다.

- 중복된 특성 동기화 오류의 범위를 좁혀서 특정 픽스 제공

- 전용 Azure AD 시나리오용 픽스를 적용하여 한 단계로 오류 해결

- 이 기능을 사용하기 위한 별도의 업그레이드 또는 구성이 필요 없음

자세한 내용은 [중복된 특성 동기화 오류 진단 및 수정](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors) 참조

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Azure AD 및 MSA 로그인 환경으로 시각적 업데이트

**유형:** 변경된 기능  
**서비스 범주:** Azure AD  
**제품 기능:** 사용자 인증

Office 365, Azure 등의 Microsoft 온라인 서비스 로그인 환경용 UI를 업데이트했습니다. 이 변화로 화면이 더욱 깔끔하게 정리되고 더 쉬워졌습니다. 이 변화에 대한 자세한 내용은 [Azure AD 로그인 환경에 예정된 기능 개선](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) 블로그를 참조하세요.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Azure AD Connect의 새 릴리스 - 2018년 7월

**유형:** 변경된 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 수명 주기 관리

Azure AD Connect의 최신 릴리스에는 다음이 포함되어 있습니다. 

- 버그 수정 및 지원 가능성 업데이트 

- Ping Federate 통합의 일반 공급

- 최신 SQL 2012 클라이언트의 업데이트 

이 업데이트에 대한 자세한 내용은 [Azure AD Connect: 버전 릴리스 기록](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)을 참조하세요.

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>ToU(사용 약관) 최종 사용자 UI 업데이트

**유형:** 변경된 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스

TOU 최종 사용자 UI의 동의 문자열을 업데이트할 것입니다.

**현재 텍스트.** [tenantName] 리소스에 액세스하려면 사용 약관에 동의해야 합니다.<br>**새 텍스트.** [tenantName] 리소스에 액세스하려면 사용 약관을 읽어야 합니다.

**현재 텍스트:** 동의를 선택하면 위의 모든 사용 약관에 동의하는 것입니다.<br>**새 텍스트:** 사용 약관을 읽고 이해한 것을 확인하려면 [동의]를 클릭하세요.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>통과 인증은 레거시 프로토콜 및 응용 프로그램 지원

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
이제 통과 인증은 레거시 프로토콜 및 응용 프로그램을 지원합니다. 이제 다음 제한 사항이 완전히 지원됩니다.

- 사용자가 최신 인증 없이 레거시 Office 클라이언트 응용 프로그램, Office 2010 및 Office 2013에 로그인.

- Office 2010의 Exchange 하이브리드 환경에서만 일정 공유 및 약속 있음/없음 정보에 액세스하세요.

- 사용자가 최신 인증 없이 비즈니스용 Skype 클라이언트 응용 프로그램에 로그인.

- 사용자가 PowerShell 버전 1.0에 로그인.

- iOS 설정 도우미를 사용하는 Apple DEP(Apple 장비 등록 프로그램). 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>셀프 서비스 암호 재설정 및 Multi-Factor Authentication을 위한 융합형 보안 정보

**유형:** 새로운 기능  
**서비스 범주:** SSPR  
**제품 기능:** 사용자 인증

사용자는 이 새로운 기능을 통해 SSPR(셀프 서비스 암호 재설정) 및 MFA(Multi-Factor Authentication)에 대한 보안 정보(예: 전화 번호, 이메일 주소, 모바일 앱 및 등)를 단일 환경에서 관리할 수 있습니다. 사용자는 더 이상 SSPR 및 MFA에 대한 동일한 보안 정보를 서로 다른 두 환경에 등록할 필요가 없습니다. 이 새로운 환경은 SSPR 또는 MFA를 사용하는 사용자에게도 적용됩니다.

조직에서 MFA 또는 SSPR 등록을 강제로 적용하지 않는 경우 사용자는 **My Apps** 포털을 통해 보안 정보를 등록할 수 있습니다. 여기서 사용자는 MFA 또는 SSPR에 대해 설정된 방법을 등록할 수 있습니다. 

이 기능은 옵트인 공개 미리 보기입니다. 관리자는 원하는 경우 테넌트의 특정 사용자 그룹 또는 모든 사용자에게 새 환경을 적용할 수 있습니다.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>암호를 재설정할 때 Microsoft Authenticator 앱을 사용하여 ID 확인

**유형:** 변경된 기능  
**서비스 범주:** SSPR  
**제품 기능:** 사용자 인증

관리자가 아닌 사용자는 이 기능을 통해 Microsoft Authenticator(또는 다른 인증 앱)에서 알림 또는 코드를 사용하여 암호를 재설정하는 동안 자신의 ID를 확인할 수 있습니다. 관리자가 이 셀프 서비스 암호 재설정 방법을 설정하면 aka.ms/mfasetup 또는 aka.ms/setupsecurityinfo를 통해 모바일 앱을 등록한 사용자는 암호를 재설정하는 동안 모바일 앱을 인증 방법으로 사용할 수 있습니다.

모바일 앱 알림은 암호를 재설정하는 두 가지 방법을 요구하는 정책의 일부로만 설정할 수 있습니다.

---

## <a name="june-2018"></a>2018년 6월

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>변경 통지: Azure AD 활동 로그 API를 사용하여 앱에 대한 위임된 사용 권한 부여 흐름에 대한 보안 수정

**유형:** 변경 계획  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

더 강력한 보안 적용으로 인해 [Azure AD 활동 로그 API](https://aka.ms/aadreportsapi)에 액세스하는 데 위임된 사용 권한 부여 흐름을 사용하는 앱에 대한 사용 권한을 변경해야 했습니다. 이 변경 내용은 **2018년 6월 26일**에 발생합니다.

앱이 Azure AD 활동 로그 API를 사용하는 경우 다음 단계를 따라 변경 내용이 발생한 후에 앱이 중단되지 않는지 확인합니다.

**앱 사용 권한을 업데이트하려면**

1. Azure Portal에 로그인하고 **Azure Active Directory**를 선택한 다음, **앱 등록**을 선택합니다.
2. Azure AD 활동 로그 API를 사용하는 앱을 선택하고 **설정**을 선택하고 **필수 권한**을 선택한 다음, **Windows Azure Active Directory** API를 선택합니다.
3. **액세스 활성화** 블레이드의 **위임된 사용 권한** 영역에서 **디렉터리 읽기** 데이터 옆의 상자를 선택한 다음, **저장**을 선택합니다.
4. **사용 권한 부여**를 선택한 다음, **예**를 선택합니다.
    
    >[!Note]
    >앱에 사용 권한을 부여하려면 전역 관리자여야 합니다.

자세한 내용은 Azure AD Reporting API에 액세스하기 위한 필수 구성 요소 문서에서 [권한 부여](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) 영역을 참조하세요.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>PCI DSS 준수를 위해 Azure AD 서비스에 연결하도록 TLS 설정 구성

**유형:** 새로운 기능  
**서비스 범주:** 해당 없음  
**제품 기능:** 플랫폼

TLS(전송 계층 보안)는 통신하는 두 응용 프로그램 간에 개인 정보 보호 및 데이터 무결성을 제공하는 프로토콜이며 현재 가장 널리 사용되는 보안 프로토콜입니다.

[PCI Security Standards Council](https://www.pcisecuritystandards.org/)에서는 **2018년 6월 30일**부터 보다 새롭고 안전한 앱 프로토콜을 사용하기 위해 TLS 및 SSL(Secure Sockets Layer)의 초기 버전을 사용하지 않기로 결정했습니다. 따라서 Azure AD 서비스에 연결하고 PCI DSS를 준수해야 하는 경우 TLS 1.0을 사용하지 않도록 설정해야 합니다. 여러 가지 버전의 TLS를 사용할 수 있지만 Azure Active Directory 서비스에서 사용할 수 있는 최신 버전은 TLS 1.2입니다. 클라이언트/서버 및 브라우저/서버 조합 모두를 TLS 1.2로 바로 변경하는 것이 좋습니다.

오래된 브라우저에서는 TLS 1.2와 같은 새로운 TLS 버전이 지원되지 않을 수도 있습니다. 브라우저에서 지원되는 TLS 버전을 확인하려면 [Qualys SSL Labs](https://www.ssllabs.com/) 사이트로 이동하여 **Test your browser**(브라우저 테스트)클릭하세요. 최신 버전의 웹 브라우저로 업그레이드하고 TLS 1.2만 사용하도록 설정하는 것이 좋습니다.

**TLS 1.2를 사용하도록 설정하려면, 브라우저별로**

- **Microsoft Edge 및 Internet Explorer(두 가지 모두 Internet Explorer를 사용하여 설정)**

    1. Internet Explorer를 열고 **도구** > **인터넷 옵션** > **고급**을 선택합니다.
    2. **보안** 영역에서 **TLS 1.2 사용**을 선택한 다음, **확인**을 선택합니다.
    3. 브라우저 창을 모두 닫고 Internet Explorer를 다시 시작합니다. 

- **Google Chrome**

    1. Google Chrome을 열고 주소 표시줄에 *chrome://settings/* 을 입력하고 **Enter** 키를 누릅니다.
    2. **고급** 옵션을 펼치고 **시스템** 영역으로 이동하여 **프록시 설정 열기**를 선택합니다.
    3. **인터넷 속성** 상자에서 **고급** 탭을 선택하고 **보안** 영역으로 이동하여 **TLS 1.2 사용**을 선택한 다음, **확인**을 선택합니다.
    4. 브라우저 창을 모두 닫고 Google Chrome을 다시 시작합니다.

- **Mozilla Firefox**

    1. Firefox를 열어서 주소 표시줄에 *about:config*를 입력한 다음, **Enter** 키를 누릅니다.
    2. *TLS*라는 용어를 검색한 다음, **security.tls.version.max** 항목을 선택합니다.
    3. 값을 **3**으로 설정하여 브라우저에서 TLS 1.2 버전까지 사용하도록 하고 **확인**을 선택합니다.

        >[!NOTE]
        >Firefox 버전 60.0은 TLS 1.3을 지원하므로 security.tls.version.max 값을 **4**로 설정할 수도 있습니다.

    4. 브라우저 창을 모두 닫고 Mozilla Firefox를 다시 시작합니다.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 6월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2018년 6월에 페더레이션이 지원되는 신규 앱 15개가 앱 갤러리에 추가되었습니다.

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 토큰 사용 LOB 앱](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint 온-프레미스](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 응용 프로그램을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)을 참조하세요. 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD 암호 보호가 공개 미리 보기로 제공됩니다.

**유형:** 새로운 기능  
**서비스 범주:** ID 보호  
**제품 기능:** 사용자 인증

Azure AD 암호 보호를 사용하면 쉽게 추측되는 암호를 환경에서 제거하는 데 도움이 됩니다. 이러한 암호를 제거하면 암호 스프레이 유형의 공격으로 인해 손상될 위험을 줄일 수 있습니다.

구체적으로는, Azure AD 암호 보호는 다음과 같은 이점을 제공합니다.

- Azure AD와 Windows Server AD(Active Directory) 모두에서 조직의 계정이 보호됩니다. 
- 가장 일반적으로 사용되는 500개를 초과하는 암호 목록과 이러한 암호에 대한 백만개가 넘는 문자 대체 변형에 포함된 암호의 사용이 금지됩니다.
- Azure AD 포털의 단일 위치에서 또는 Azure AD와 온-프레미스 Windows Server AD 모두에서 Azure AD 암호 보호를 관리할 수 있습니다.

Azure AD 암호 보호에 대한 자세한 내용은 [조직에서 잘못된 암호 제거](https://aka.ms/aadpasswordprotectiondocs)를 참조하세요.

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>ToU(사용 약관)를 만드는 동안 "모든 게스트" 조건부 액세스 정책 템플릿이 새로 만들어집니다.

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스

ToU(사용 약관)를 만드는 동안 "모든 게스트"와 "모드 앱"에 대한 조건부 액세스 정책 템플릿이 새로 만들어집니다. 새로운 정책 템플릿은 새로 만들어진 ToU를 적용하며 게스트에 대한 생성 및 적용 프로세스를 간소화합니다.

자세한 내용은 [Azure Active Directory 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/active-directory-tou)을 참조하세요.

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>ToU(사용 약관)를 만드는 동안 "사용자 지정" 조건부 액세스 정책 템플릿이 새로 만들어집니다.

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스

ToU(사용 약관)를 만드는 동안 "사용자 지정" 조건부 액세스 정책 템플릿도 새로 만들어집니다. 새 정책 템플릿을 사용하면 ToU를 만든 다음, 수동으로 포털을 탐색할 필요 없이 조건부 액세스 정책 생성 블레이드로 즉시 이동할 수 있습니다.

자세한 내용은 [Azure Active Directory 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/active-directory-tou)을 참조하세요.

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 배포에 대한 새롭고 포괄적인 지침

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** ID 보안 및 보호
 
조직에 Azure MFA(Multi-Factor Authentication)를 배포하는 방법에 대한 새로운 단계별 지침이 릴리즈되었습니다.

MFA 배포 가이드를 보려면 GitHub의 [Identity Deployment Guides](https://aka.ms/DeploymentPlans)(ID 배포 가이드) 리포지토리로 이동하세요. 배포 가이드에 대한 피드백을 제공하려면 [배포 계획 사용자 의견 양식](https://aka.ms/deploymentplanfeedback)을 참조하세요. 배포 가이드에 대한 질문이 있는 경우 [IDGitDeploy](mailto:idgitdeploy@microsoft.com)에 문의하세요.

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD 위임된 앱 관리 역할이 공개 미리 보기 상태입니다.

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 액세스 제어

관리자는 전역 관리자 역할을 할당하지 않고도 앱 관리 작업을 위임할 수 있습니다. 새로운 역할과 기능은 다음과 같습니다.

- **새로운 표준 Azure AD 관리자 역할:**

    - **응용 프로그램 관리자.** 모든 앱의 모든 측면(예: 등록, SSO 설정, 앱 할당 및 라이선싱, 앱 프록시 설정 및 승인)을 관리하는 기능을 부여합니다(Azure AD 리소스 제외).

    - **클라우드 응용 프로그램 관리자.** 모든 응용 프로그램 관리자 권한을 부여합니다. 앱 프록시는 온-프레미스 액세스를 제공하지 않기 때문에 예외입니다.

    - **응용 프로그램 개발자.** **사용자가 앱을 등록하도록 허용**하는 옵션이 꺼져 있는 경우에도 앱 등록을 만들 수 있는 권한을 부여합니다.

- **소유권(그룹 소유권 프로세스와 유사하게 앱별 등록 및 엔터프라이즈별 앱을 설정합니다.):**
 
    - **앱 등록 소유자.** 앱 매니페스트를 비롯하여 소유한 앱 등록의 모든 측면을 관리하고 소유자를 더 추가할 수 있는 권한을 부여합니다.

    - **엔터프라이즈 앱 소유자.** 소유한 엔터프라이즈 앱의 많은 측면(예: SSO 설정, 앱 할당 및 동의)을 관리할 수 있는 권한을 부여합니다(Azure AD 리소스 제외).

공개 미리 보기에 대한 자세한 내용은 [Azure AD 위임된 응용 프로그램 관리 역할이 공개 미리 보기 상태입니다!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) 블로그를 참조하세요. 역할 및 권한에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)을 참조하세요.

---

## <a name="may-2018"></a>2018년 5월

### <a name="expressroute-support-changes"></a>ExpressRoute 지원 변경

**유형:** 변경 계획  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 플랫폼  

Azure AD(Azure Active Directory)와 같은 서비스 제공으로 소프트웨어는 ExpressRoute 또는 다른 개인 VPN 터널의 필요 없이 인터넷을 통해 직접 이동하여 가장 잘 작동하도록 설계됩니다. 이로 인해 **2018년 8월 1일**에 Azure 공용 피어링 및 Microsoft 피어링의 Microsoft 커뮤니티를 사용하여 Azure AD 서비스에 대한 ExpressRoute 지원을 중지합니다. 이러한 변경 내용의 영향을 받는 모든 서비스는 Azure AD 트래픽이 ExpressRoute에서 인터넷으로 점차적으로 이동하는 것을 확인할 수 있습니다.

지원을 변경하는 동안 인증 트래픽에 대해 전용 회로 집합을 사용해야 할 수 있는 상황이 여전히 있다는 것을 알고 있습니다. 이로 인해 Azure AD는 ExpressRoute 및 "기타 Office 365 온라인 서비스" 커뮤니티와 함께 Microsoft 피어링에 이미 있는 서비스를 사용하여 테넌트당 IP 범위 제한을 계속해서 지원할 예정입니다. 서비스가 영향을 받지만 ExpressRoute가 필요한 경우 다음을 수행해야 합니다.

- **Azure 공용 피어링에 있는 경우** Microsoft 피어링으로 이동하고 **기타 Office 365 온라인 서비스(12076:5100)** 커뮤니티에 등록합니다. Azure 공용 피어링에서 Microsoft 피어링으로 이동하는 방법에 대한 자세한 내용은 [Microsoft 피어링으로 공용 피어링 이동](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) 문서를 참조하세요.

- **Microsoft 피어링에 있는 경우** **기타 Office 365 온라인 서비스(12076:5100)** 커뮤니티에 등록합니다. 라우팅 요구 사항에 대한 자세한 내용은 ExpressRoute 라우팅 요구 사항 문서의 [BGP 커뮤니티에 대한 지원 섹션](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp)을 참조하세요.

전용 회로를 계속해서 사용해야 하는 경우 **기타 Office 365 온라인 서비스(12076:5100)** 커뮤니티를 사용하도록 권한을 얻는 방법에 대해 Microsoft 계정 팀에게 문의해야 합니다. MS Office 관리 검토 위원회는 해당 회로가 필요한지 여부를 확인하고 이를 유지하는 기술적 의미를 이해하도록 합니다. Office 365에 대한 경로 필터를 만들도록 시도하는 인증되지 않은 구독은 오류 메시지를 받습니다. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>TOU의 관리 시나리오를 위한 Microsoft Graph API

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 개발자 환경
 
Azure AD 사용 약관의 관리 작업에 대한 Microsoft Graph API가 추가되었습니다. 사용 약관 개체를 만들고, 업데이트하고, 삭제할 수 있습니다.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD 다중 테넌트 엔드포인트를 Azure AD B2C의 ID 공급자로 추가

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
사용자 지정 정책을 사용하여 이제 Azure AD B2C에서 ID 공급자로 Azure AD 공용 엔드포인트를 추가할 수 있습니다. 이를 통해 응용 프로그램에 로그인하는 모든 Azure AD 사용자에 대한 항목의 단일 지점을 가질 수 있습니다. 자세한 내용은 [Azure Active Directory B2C: 사용자 지정 정책을 사용하여 사용자가 다중 테넌트 Azure AD ID 공급자에 로그인할 수 있게 함](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom)을 참조하세요.

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>내 앱 로그인 확장 및 Azure AD 응용 프로그램 프록시를 통해 내부 URL을 사용하여 어디서나 앱에 액세스

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO
 
사용자는 이제 Azure AD에 대한 내 앱 보안 로그인 확장을 사용하여 회사 네트워크 외부에서도 내부 URL을 통해 응용 프로그램에 액세스할 수 있습니다. 이는 액세스 패널 브라우저 확장이 설치되어 있는 모든 브라우저에서 Azure AD 응용 프로그램 프록시를 사용하여 게시한 모든 응용 프로그램에 사용할 수 있습니다. URL 리디렉션 기능은 사용자가 확장에 로그인하면 자동으로 활성화됩니다. 확장은 [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) 및 [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)에서 다운로드에 사용할 수 있습니다.

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - 유럽 고객을 위한 유럽의 데이터

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** GoLocal

유럽의 고객은 데이터를 유럽에 유지해야 하며 개인 정보 보호 및 유럽 법을 충족하도록 유럽 데이터 센터 외부에서 복제되지 않습니다. 이 [문서](https://go.microsoft.com/fwlink/?linkid=872328)는 유럽 내에서 저장되는 ID 정보에 대한 특정 세부 정보를 제공하고 유럽 데이터 센터 외부에 저장되는 정보에 대한 세부 정보도 제공합니다. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>새 사용자 프로비저닝 SaaS 앱 통합 - 2018년 5월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 
Azure AD를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 SaaS 응용 프로그램에서 사용자 ID 만들기, 유지 관리 및 제거를 자동화할 수 있습니다. 2018년 5월에 Azure AD 앱 갤러리에서 다음 응용 프로그램에 대한 사용자 프로비저닝 지원을 추가했습니다.

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Azure AD 갤러리에서 사용자 프로비저닝을 지원하는 모든 응용 프로그램의 목록은 [https://aka.ms/appstutorial](https://aka.ms/appstutorial)을 참조하세요.

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>이제 그룹 및 앱 액세스에 대한 Azure AD 액세스 검토가 반복 검토 제공

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 거버넌스
 
이제 Azure AD Premium P2의 일부로 그룹 및 앱에 대한 액세스 검토를 일반적으로 사용할 수 있습니다.  관리자는 월간 또는 분기별과 같은 정기적인 간격으로 그룹 멤버 자격 및 응용 프로그램 할당에 대한 액세스 검토를 자동으로 반복하도록 구성할 수 있습니다.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>이제 Azure AD 활동 로그(로그인 및 감사)를 MS Graph를 통해 사용 가능

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
이제 Azure AD 활동 로그(로그인 및 감사 로그 포함)를 MS Graph를 통해 사용할 수 있습니다. 이러한 로그에 액세스하도록 MS Graph를 통해 2개의 엔드포인트를 노출했습니다. Azure AD Reporting API에 프로그래밍 방식으로 액세스하여 시작하려면 [문서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)를 참조하세요. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>B2B 상환 환경 및 조직 떠나기에 대한 개선

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C

**JIT(Just in time) 상환:** B2B API를 사용하여 게스트 사용자와 리소스를 공유하면 특별한 초대 이메일을 보낼 필요가 없습니다. 대부분의 경우에서 게스트 사용자는 리소스에 액세스할 수 있으며 필요한 때에 상환 환경을 통해 제거됩니다. 누락된 이메일로 인한 영향이 더 이상 없습니다. 게스트 사용자에게 "시스템이 보낸 해당 상환 링크를 클릭했나요?"라고 더 이상 묻지 않습니다. 즉, SPO에서 초대 관리자를 사용하면 흐린 첨부 파일은 모든 상환 상태에서 내부 및 외부 모든 사용자에 대해 동일한 정식 URL을 가질 수 있습니다.

**최신 상환 환경:** 더 이상 화면 상환 방문 페이지를 분할하지 않습니다. 사용자는 타사 앱에 대해 수행하는 것과 같이 초대 조직의 개인정보처리방침과 함께 최신 동의 환경을 봅니다.

**게스트 사용자는 조직을 떠날 수 있음:** 조직과의 사용자의 관계가 끝나면 사용자는 스스로 조직을 떠날 수 있습니다. "제거되는" 초대 조직의 관리자를 더 이상 호출하지 않고, 지원 티켓을 더 이상 발생하지 않습니다.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 5월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2018년 5월에 페더레이션이 지원되는 신규 앱 18개가 앱 갤러리에 추가되었습니다.

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요.

Azure AD 앱 갤러리에 응용 프로그램을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)을 참조하세요.

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Azure Active Directory에 대한 새로운 단계별 배포 가이드

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 디렉터리
 
Azure AD(Azure Active Directory)를 배포하는 방법에 대한 새로운 단계별 가이드이며 SSPR(셀프 서비스 암호 재설정), SSO(Single Sign-On), CA(조건부 액세스), 앱 프록시, 사용자 프로비전, PTA(통과 인증)에 대한 ADFS(Active Directory Federation Services), PHS(암호 해시 동기화)에 대한 ADFS가 포함됩니다.

배포 가이드를 보려면 GitHub의 [Identity Deployment Guides](https://aka.ms/DeploymentPlans)(ID 배포 가이드) 리포지토리로 이동하세요. 배포 가이드에 대한 피드백을 제공하려면 [배포 계획 사용자 의견 양식](https://aka.ms/deploymentplanfeedback)을 참조하세요. 배포 가이드에 대한 질문이 있는 경우 [IDGitDeploy](mailto:idgitdeploy@microsoft.com)에 문의하세요.

---

### <a name="enterprise-applications-search---load-more-apps"></a>Enterprise 응용 프로그램 검색 - 앱 추가 로드

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
응용 프로그램/서비스 사용자를 찾는 데 문제가 있나요? 엔터프라이즈 응용 프로그램 모든 응용 프로그램 목록에 더 많은 응용 프로그램을 로드하는 기능을 추가했습니다. 기본적으로 20개의 응용 프로그램을 표시합니다. 이제 **추가 로드**를 클릭하여 추가 응용 프로그램을 볼 수 있습니다. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>AADConnect의 5월 릴리스에는 PingFederate와의 통합, 중요 보안 업데이트, 여러 가지 버그 수정 및 뛰어난 새 문제 해결 도구에 대한 공개 미리 보기가 포함되어 있습니다. 

**유형:** 변경된 기능  
**서비스 범주:** AD Connect  
**제품 기능:** ID 수명 주기 관리
 
AADConnect의 5월 릴리스에는 PingFederate와의 통합, 중요 보안 업데이트, 여러 가지 버그 수정 및 뛰어난 새 문제 해결 도구에 대한 공개 미리 보기가 포함되어 있습니다. [여기](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190)에서 릴리스 정보를 찾을 수 있습니다.

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD 액세스 검토: 자동 적용

**유형:** 변경된 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 거버넌스

이제 Azure AD Premium P2의 일부로 그룹 및 앱에 대한 액세스 검토를 일반적으로 사용할 수 있습니다. 관리자는 액세스 검토가 완료되면 검토자의 변경 내용을 해당 그룹 또는 앱에 자동으로 적용하도록 구성할 수 있습니다. 관리자는 검토자가 응답하지 않는 경우 사용자가 지속적으로 액세스하면 어떤 상황이 발생하는지 지정하고, 액세스를 제거하고, 액세스를 유지하거나 시스템 권장 사항을 사용할 수도 있습니다. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>ID 토큰은 새 앱의 response_mode 쿼리를 사용하여 더 이상 반환할 수 없습니다. 

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
2018년 4월 25일 이후에 생성되는 앱은 더 이상 response_mode **쿼리**를 사용하여 **id_token**을 요청할 수 없습니다.  OIDC 사양으로 Azure AD 인라인을 가져오고 앱 공격 노출 영역을 줄일 수 있습니다.  2018년 4월 25일 이전에 생성되는 앱은 **id_token**의 response_type으로 response_mode **쿼리** 사용이 차단됩니다.  AAD에서 id_token을 요청할 때 반환되는 오류는 **AADSTS70007: 토큰을 요청할 때 ‘쿼리’는 ‘response_mode’의 지원되는 값이 아닙니다**입니다.

새 응용 프로그램 개체(예: 앱 프록시 사용)를 만들 때 **fragment** 및 **form_post** response_mode는 계속해서 작동합니다. 새 응용 프로그램을 만들기 전에 이러한 response_mode 중 하나를 사용하는지 확인합니다.  

---