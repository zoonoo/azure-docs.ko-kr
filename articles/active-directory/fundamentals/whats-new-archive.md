---
title: 새로운 기능 보관 - Azure Active Directory | Microsoft Docs
description: 이 콘텐츠 세트의 개요 섹션에 있는 새로운 기능 릴리스 정보에는 6개월간의 작업이 포함됩니다. 6개월 후에는 항목이 기본 문서에서 제거되고 이 보관 문서에 포함됩니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e55d11fa44aca65e2d4e487852bd242a4ba75a5f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474173"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능 보관

기본 [새로운 기능 릴리스 정보](whats-new.md) 문서에는 가장 최근 6개월의 정보가 포함되지만, 이 문서에는 더 오래된 정보도 모두 포함됩니다.

새로운 기능 릴리스 정보는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

---

## <a name="october-2018"></a>2018년 10월

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD 로그는 이제 Azure Log Analytics(공개 미리 보기)에서 사용됩니다.

**유형:** 새 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

이제 Azure Log Analytics에 Azure AD 로그를 전달할 수 있게 되었습니다. 많은 요청이 있었던 이 기능은 인프라를 모니터링하는 데 도움이 줄 뿐만 아니라 비즈니스, 작업 및 보안을 위해 분석에 보다 쉽게 액세스할 수 있도록 합니다. 자세한 내용은 [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)(이제 Azure Log Analytics에서 Azure Active Directory 활동 로그를 사용할 수 있음) 블로그를 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 10월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

2018년 10월에 페더레이션이 지원되는 다음과 같은 신규 앱 14개가 앱 갤러리에 추가되었습니다.

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services 이메일 알림

**유형:** 새 기능  
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
**제품 기능:** 협업

이제 그룹의 소유자가 되지 않아도 특정 관리자 역할이 동적 구성원 규칙을 만들고 업데이트할 수 있도록 문제가 해결되었습니다.

역할은 다음과 같습니다.

- 전역 관리자

- Intune 관리자

- 사용자 관리자

자세한 내용은 [동적 그룹 만들기 및 상태 확인](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)을 참조하세요.

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>일부 타사 앱에 대한 간소화된 SSO(Single Sign-On) 구성 설정

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

각 앱 구성의 고유한 특성 때문에 SaaS(Software as a Service) 앱에 대한 SSO(Single Sign-On)를 설정하기란 쉽지 않은 일입니다. Microsoft는 다음 타사 SaaS 앱의 SSO 구성 설정을 자동으로 채우도록 간소화된 구성 환경을 빌드했습니다.

- Zendesk

- ArcGis Online

- Jamf Pro

이 원클릭 환경을 사용하려면 앱의 **Azure Portal** > **SSO 구성** 페이지로 이동합니다. 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)을 참조하세요.

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory - 데이터가 있는 위치 페이지

**유형:** 새 기능  
**서비스 범주:** 기타  
**제품 기능:** GoLocal

**Azure Active Directory - 데이터가 있는 위치** 페이지에서 회사가 있는 지역을 선택하고, 모든 Azure AD 서비스의 Azure AD 미사용 데이터를 저장하는 Azure 데이터 센터가 어디인지 살펴봅니다. 회사가 있는 지역의 특정 Azure AD 서비스를 기준으로 정보를 필터링할 수 있습니다.

이 기능 및 자세한 정보에 액세스하려면 [Azure Active Directory - 데이터가 있는 위치](https://aka.ms/AADDataMap)를 참조하세요.

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>내 앱 액세스 패널에 사용할 수 있는 새 배포 계획

**유형:** 새 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO

내 앱 액세스 패널에 사용할 수 있는 새 배포 계획을 확인하세요(https://aka.ms/deploymentplans).
내 앱 액세스 패널은 사용자에게 앱을 찾고 액세스할 수 있는 단일 위치를 제공합니다. 또한 이 포털은 앱 및 그룹에 대한 액세스 권한 요청, 다른 사람을 대신하여 리소스에 대한 액세스 권한 관리와 같은 셀프 서비스 기회를 사용자에게 제공합니다.

자세한 내용은 [My Apps 포털이란?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)을 참조하세요.

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Azure Portal의 [로그인 로그] 페이지에 있는 새로운 [문제 해결 및 지원] 탭

**유형:** 새 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

Azure Portal의 **로그인** 페이지에 있는 새로운 **문제 해결 및 지원** 탭의 목적은 관리자 및 지원 엔지니어가 Azure AD 로그인 관련 문제를 해결할 수 있게 도와주는 것입니다. 이 새 탭은 문제 해결에 도움이 되는 오류 코드, 오류 메시지 및 수정 권장 사항(있는 경우)을 제공합니다. 문제를 해결할 수 없는 경우 지원 티켓의 로그 파일에 대한 **요청 ID** 및 **날짜(UTC)** 필드를 채우는 **클립보드에 복사** 환경을 사용하여 지원 티켓을 만들 수 있는 새로운 방법을 제공합니다.  

![로그인 로그 새 탭을 보여 주는](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>동적 구성원 규칙을 만드는 데 사용되는 사용자 지정 확장 속성에 대한 향상된 지원

**유형:** 변경된 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업

이 업데이트부터는 동적 사용자 그룹 규칙 작성기에서 **사용자 지정 확장 속성 가져오기**를 클릭하고, 고유한 앱 ID를 입력하고, 사용자에 대한 동적 구성원 규칙을 만들 때 사용할 사용자 지정 확장 속성 전체 목록을 받을 수 있습니다. 이 목록을 새로 고침하여 해당 앱에 대한 새로운 사용자 지정 확장 속성을 가져올 수도 있습니다.

동적 구성원 규칙에 대한 사용자 지정 확장 속성을 사용하는 방법에 대한 자세한 내용은 [확장 속성 및 사용자 지정 확장 속성](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)을 참조하세요.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스에 대해 승인된 새 클라이언트 앱

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

**유형:** 새 기능  
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

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2018년 9월에 페더레이션이 지원되는 신규 앱 16개가 앱 갤러리에 추가되었습니다.

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="support-for-additional-claims-transformations-methods"></a>추가 클레임 변환 메서드 지원

**유형:** 새 기능  
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

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>SSPR(셀프 서비스 암호) 및 MFA(Multi-Factor Authentication)을 위한 수렴된 보안 정보 관리

**유형:** 새 기능  
**서비스 범주:** SSPR  
**제품 기능:** 사용자 인증
 
이 기능을 사용하면 SSPR 및 MFA에 대한 보안 정보(예: 전화 번호, 모바일 앱 등)를 두 개의 다른 위치에서 관리하던 이전 방식과 달리, 단일 위치 및 환경에서 관리할 수 있습니다.

융합형 환경은 SSPR 또는 MFA 사용자에게도 유용합니다. 또한 조직에서 MFA 또는 SSPR 등록이 적용되지 않는 경우에도 내 앱 포털의 조직에 허용되는 MFA 또는 SSPR 보안 정보 메서드를 사용자가 등록할 수 있습니다.

이 기능은 옵트인 공개 미리 보기입니다. 관리자는 선택한 그룹 또는 테넌트의 모든 사용자에게 원하는 경우 환경을 적용할 수 있습니다. 융합형 환경에 대한 자세한 내용은 [융합형 환경 블로그](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)를 참조하세요.

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD 애플리케이션 프록시 앱의 새 HTTP 전용 쿠키 설정

**유형:** 새 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

애플리케이션 프록시 앱에는 **HTTP 전용 쿠키**라는 새로운 설정이 있습니다. 이 설정을 사용하면 애플리케이션 프록시 액세스 및 세션 쿠키 모두에 대한 HTTP 응답 헤더에 HTTPOnly 플래그를 포함시켜서 추가 보안을 제공하여 클라이언트 쪽 스크립트에서 쿠키에 대한 액세스를 중지하고 쿠키 복사 또는 수정과 같은 작업을 방지할 수 있습니다. 전에는 이 플래그가 사용되지 않았지만 부적절한 수정을 방지하기 위해 SSL 연결을 사용하여 항상 쿠키가 암호화되고 전송되었습니다.

이 설정은 원격 데스크톱과 같은 ActiveX 컨트롤을 사용하는 응용 프로그램과 호환되지 않습니다. 이런 경우에 해당하면 이 설정을 해제하는 것이 좋습니다.

HTTP 전용 쿠키 설정에 대한 자세한 내용은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal)를 참조하세요.

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure 리소스의 PIM(Privileged Identity Management)에서 관리 그룹 리소스 종류 지원

**유형:** 새 기능  
**서비스 범주:** 권한 있는 ID 관리  
**제품 기능:** 권한 있는 ID 관리
 
이제 Just-In-Time 활성화 및 할당 설정을 구독, 리소스 그룹 및 리소스(예: VM, App Services 등)에 적용하듯이 관리 그룹 리소스 종류에 적용할 수 있습니다. 또한 관리 그룹에 대한 관리자 액세스를 제공하는 역할이 있는 사람은 PIM에서 해당 리소스를 검색하고 관리할 수 있습니다.

PIM 및 Azure 리소스에 대한 자세한 내용은 [Privileged Identity Management를 사용하여 Azure 리소스 검색 및 관리](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)를 참조하세요.
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>응용 프로그램 액세스(미리 보기)에서 Azure AD 포털의 빠른 액세스 제공

**유형:** 새 기능  
**서비스 범주:** 권한 있는 ID 관리  
**제품 기능:** 권한 있는 ID 관리
 
현재 PIM을 사용하여 역할을 활성화하는 경우 권한이 적용되려면 10분 넘게 걸릴 수 있습니다. 현재 미리 보기 상태인 애플리케이션 액세스를 사용하도록 선택하면 활성화 요청이 완료되자마자 관리자가 Azure AD 포털에 액세스할 수 있습니다.

현재 애플리케이션 액세스는 Azure AD 포털 환경과 Azure 리소스만 지원합니다. PIM 및 애플리케이션 액세스에 대한 자세한 내용은 [Azure AD Privileged Identity Management란?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)을 참조하세요.
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱 - 2018년 8월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2018년 8월에 페더레이션이 지원되는 신규 앱 16개가 앱 갤러리에 추가되었습니다.

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>이제 Azure AD 애플리케이션 프록시에서 네이티브 Tableau 지원을 사용할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

사전 인증 프로토콜을 위해 OpenID Connect가 OAuth 2.0 코드 권한 부여 프로토콜로 업데이트되었기 때문에 애플리케이션 프록시와 Tableau를 사용하기 위해 추가 구성을 수행할 필요가 없습니다. 프로토콜이 변경됨으로써 애플리케이션 프록시가 HTTP 리디렉션(JavaScript 및 HTML 태그에서 일반적으로 지원됨)만 사용하여 최신 앱을 더 잘 지원할 수 있습니다.

Tableau에 대한 기본 지원에 대한 자세한 내용은 [Azure AD 애플리케이션 프록시(기본 Tableau 지원)](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)을 참조하세요.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Google을 Azure Active Directory(미리 보기)에서 B2B 게스트 사용자의 ID 공급자로 추가하기 위한 새로운 지원

**유형:** 새 기능  
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
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- 엔터프라이즈 앱 인증서 만료 알림
- 엔터프라이즈 앱 서비스 프로비저닝 알림
 
메일 알림은 다음 메일 주소 및 표시 이름에서 전송됩니다.

- 메일 주소: azure-noreply@microsoft.com
- 표시 이름: Microsoft Azure
 
새 메일 디자인의 몇 가지 예제와 자세한 내용은 [Azure AD PIM의 메일 알림](https://go.microsoft.com/fwlink/?linkid=2005832)을 참조하세요.

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD 활동 로그는 이제 Azure Monitor를 통해 제공됩니다.

**유형:** 새 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

이제 Azure Monitor(Azure의 플랫폼 수준 모니터링 서비스)를 위한 공개 미리 보기에서 Azure AD 활동 로그를 사용할 수 있습니다. Azure Monitor는 장기 보존 및 매끄러운 통합 외에도 다음과 같은 향상된 기능을 제공합니다.

- 로그 파일을 사용자 고유의 Azure 저장소 계정에 라우팅하여 장기 보존.

- 사용자 지정 스크립트를 작성하거나 유지 관리할 필요 없이 매끄럽게 SIEM 통합.

- 사용자 고유의 사용자 지정 솔루션, 분석 도구 또는 인시던트 관리 솔루션과 매끄럽게 통합.

이러한 새 기능에 대한 자세한 내용은 [Azure Monitor 진단에서 Azure AD 활동 로그를 공개 미리 보기로 제공](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) 블로그 및 [Azure Monitor의 Azure Active Directory 활동 로그(미리 보기)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview) 문서를 참조하세요.

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Azure AD 로그인 보고서에 추가된 조건부 액세스 정보

**유형:** 새 기능  
**서비스 범주:** 보고  
**제품 기능:** ID 보안 및 보호
 
이 업데이트는 정책 결과와 함께 사용자가 로그인할 때 어떤 정책이 평가되는지 볼 수 있게 해줍니다. 또한 이제 보고서에 사용자가 사용한 클라이언트 앱 유형이 포함되므로 레거시 프로토콜 트래픽을 식별할 수 있습니다. 또한 이제 보고서 항목에서 상관 관계 ID를 검색할 수 있습니다. 이 ID는 사용자에게 표시되는 오류 메시지에서 찾을 수 있으며 일치하는 로그인 요청을 찾아서 해결하는 데 사용할 수 있습니다.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>로그인 활동 로그를 통해 레거시 인증 보기

**유형:** 새 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
로그인 활동 로그에 **클라이언트 앱** 필드가 도입되어 레거시 인증을 사용하는 사용자를 고객이 볼 수 있습니다. 고객은 로그인 MS Graph API를 사용하거나 **클라이언트 앱** 컨트롤을 사용하여 레거시 인증을 필터링할 수 있는 Azure AD 포털에서 로그인 활동 로그를 통해 이 정보에 액세스할 수 있습니다. 자세한 내용은 설명서를 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 7월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2018년 7월에 페더레이션이 지원되는 신규 앱 16개가 앱 갤러리에 추가되었습니다.

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>새 사용자 프로비전 SaaS 앱 통합 - 2018년 7월

**유형:** 새 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합
 
Azure AD를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 SaaS 애플리케이션에서 사용자 ID 만들기, 유지 관리 및 제거를 자동화할 수 있습니다. 2018년 7월에 Azure AD 앱 갤러리에서 다음 애플리케이션에 대한 사용자 프로비전 지원을 추가했습니다.

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Azure AD 갤러리에서 사용자 프로비전을 지원하는 모든 애플리케이션 목록은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요.

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>동기화용 Connect Health - 분리되고 중복되는 특성 동기화 오류를 간단하게 해결할 수 있습니다.

**유형:** 새 기능  
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

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>최종 사용자 UI를 사용 하 여 약관에 대 한 업데이트

**유형:** 변경된 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스

TOU 최종 사용자 UI의 동의 문자열을 업데이트할 것입니다.

**현재 텍스트.** [tenantName] 리소스에 액세스하려면 사용 약관에 동의해야 합니다.<br>**새 텍스트.** [tenantName] 리소스에 액세스하려면 사용 약관을 읽어야 합니다.

**현재 텍스트:** 동의를 선택하면 위의 모든 사용 약관에 동의하는 것입니다.<br>**새 텍스트:** 사용 약관을 읽고 이해한 것을 확인하려면 동의를 클릭합니다.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>통과 인증은 레거시 프로토콜 및 애플리케이션 지원

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
이제 통과 인증은 레거시 프로토콜 및 응용 프로그램을 지원합니다. 이제 다음 제한 사항이 완전히 지원됩니다.

- 사용자가 최신 인증 없이 레거시 Office 클라이언트 애플리케이션, Office 2010 및 Office 2013에 로그인.

- Office 2010의 Exchange 하이브리드 환경에서만 일정 공유 및 약속 있음/없음 정보에 액세스하세요.

- 사용자가 최신 인증 없이 비즈니스용 Skype 클라이언트 애플리케이션에 로그인.

- 사용자가 PowerShell 버전 1.0에 로그인.

- iOS 설정 도우미를 사용하는 Apple DEP(Apple 장비 등록 프로그램). 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>셀프 서비스 암호 재설정 및 Multi-Factor Authentication을 위한 융합형 보안 정보

**유형:** 새 기능  
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

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>변경 알림: Azure AD 활동 로그 API를 사용하여 앱에 대한 위임된 사용 권한 부여 흐름의 보안 수정

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

**유형:** 새 기능  
**서비스 범주:** N/A  
**제품 기능:** 플랫폼

TLS(전송 계층 보안)는 통신하는 두 애플리케이션 간에 개인 정보 보호 및 데이터 무결성을 제공하는 프로토콜이며 현재 가장 널리 사용되는 보안 프로토콜입니다.

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

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2018년 6월에 페더레이션이 지원되는 신규 앱 15개가 앱 갤러리에 추가되었습니다.

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 토큰 사용 LOB 앱](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint 온-프레미스](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)을 참조하세요. 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD 암호 보호가 공개 미리 보기로 제공됩니다.

**유형:** 새 기능  
**서비스 범주:** Identity Protection  
**제품 기능:** 사용자 인증

Azure AD 암호 보호를 사용하면 쉽게 추측되는 암호를 환경에서 제거하는 데 도움이 됩니다. 이러한 암호를 제거하면 암호 스프레이 유형의 공격으로 인해 손상될 위험을 줄일 수 있습니다.

구체적으로는, Azure AD 암호 보호는 다음과 같은 이점을 제공합니다.

- Azure AD와 Windows Server AD(Active Directory) 모두에서 조직의 계정이 보호됩니다. 
- 가장 일반적으로 사용되는 500개를 초과하는 암호 목록과 이러한 암호에 대한 백만개가 넘는 문자 대체 변형에 포함된 암호의 사용이 금지됩니다.
- Azure AD 포털의 단일 위치에서 또는 Azure AD와 온-프레미스 Windows Server AD 모두에서 Azure AD 암호 보호를 관리할 수 있습니다.

Azure AD 암호 보호에 대한 자세한 내용은 [조직에서 잘못된 암호 제거](https://aka.ms/aadpasswordprotectiondocs)를 참조하세요.

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>새 "모든 게스트" 조건부 액세스 정책 템플릿 만들기 사용 약관 중에 생성

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스

사용 약관을 만드는 동안 새 조건부 액세스 정책 템플릿으로가 만들어집니다 "모든 게스트" 및 "모든 앱"에 대 한 합니다. 새로운 정책 템플릿은 새로 만들어진 ToU를 적용하며 게스트에 대한 생성 및 적용 프로세스를 간소화합니다.

자세한 내용은 [Azure Active Directory 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)을 참조하세요.

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>용어를 사용 하 여 생성 중에 만들어진 새 "custom" 조건부 액세스 정책 템플릿

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스

사용 약관을 만드는 동안 새 "custom" 조건부 액세스 정책 템플릿으로가 만들어집니다. 새 정책 템플릿을 사용하면 ToU를 만든 다음, 수동으로 포털을 탐색할 필요 없이 조건부 액세스 정책 생성 블레이드로 즉시 이동할 수 있습니다.

자세한 내용은 [Azure Active Directory 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)을 참조하세요.

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 배포에 대한 새롭고 포괄적인 지침

**유형:** 새 기능  
**서비스 범주:** 기타  
**제품 기능:** ID 보안 및 보호
 
조직에 Azure MFA(Multi-Factor Authentication)를 배포하는 방법에 대한 새로운 단계별 지침이 릴리즈되었습니다.

MFA 배포 가이드를 보려면 GitHub의 [Identity Deployment Guides](https://aka.ms/DeploymentPlans)(ID 배포 가이드) 리포지토리로 이동하세요. 배포 가이드에 대한 피드백을 제공하려면 [배포 계획 사용자 의견 양식](https://aka.ms/deploymentplanfeedback)을 참조하세요. 배포 가이드에 대한 질문이 있는 경우 [IDGitDeploy](mailto:idgitdeploy@microsoft.com)에 문의하세요.

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD 위임된 앱 관리 역할이 공개 미리 보기 상태입니다.

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** Access Control

관리자는 전역 관리자 역할을 할당하지 않고도 앱 관리 작업을 위임할 수 있습니다. 새로운 역할과 기능은 다음과 같습니다.

- **새로운 표준 Azure AD 관리자 역할:**

    - **애플리케이션 관리자.** 모든 앱의 모든 측면(예: 등록, SSO 설정, 앱 할당 및 라이선싱, 앱 프록시 설정 및 승인)을 관리하는 기능을 부여합니다(Azure AD 리소스 제외).

    - **클라우드 애플리케이션 관리자.** 모든 애플리케이션 관리자 권한을 부여합니다. 앱 프록시는 온-프레미스 액세스를 제공하지 않기 때문에 예외입니다.

    - **애플리케이션 개발자.** **사용자가 앱을 등록하도록 허용**하는 옵션이 꺼져 있는 경우에도 앱 등록을 만들 수 있는 권한을 부여합니다.

- **소유권(그룹 소유권 프로세스와 유사하게 앱별 등록 및 엔터프라이즈별 앱을 설정합니다.):**
 
    - **앱 등록 소유자.** 앱 매니페스트를 비롯하여 소유한 앱 등록의 모든 측면을 관리하고 소유자를 더 추가할 수 있는 권한을 부여합니다.

    - **엔터프라이즈 앱 소유자.** 소유한 엔터프라이즈 앱의 많은 측면(예: SSO 설정, 앱 할당 및 동의)을 관리할 수 있는 권한을 부여합니다(Azure AD 리소스 제외).

공개 미리 보기에 대한 자세한 내용은 [Azure AD 위임된 애플리케이션 관리 역할이 공개 미리 보기 상태입니다!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) 블로그를 참조하세요. 역할 및 권한에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)을 참조하세요.

---

## <a name="may-2018"></a>2018년 5월

### <a name="expressroute-support-changes"></a>ExpressRoute 지원 변경

**유형:** 변경 계획  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 플랫폼  

Azure AD(Azure Active Directory)와 같은 서비스 제공으로 소프트웨어는 ExpressRoute 또는 다른 프라이빗 VPN 터널의 필요 없이 인터넷을 통해 직접 이동하여 가장 잘 작동하도록 설계됩니다. 이로 인해 **2018년 8월 1일**에 Azure 공용 피어링 및 Microsoft 피어링의 Microsoft 커뮤니티를 사용하여 Azure AD 서비스에 대한 ExpressRoute 지원을 중지합니다. 이러한 변경 내용의 영향을 받는 모든 서비스는 Azure AD 트래픽이 ExpressRoute에서 인터넷으로 점차적으로 이동하는 것을 확인할 수 있습니다.

지원을 변경하는 동안 인증 트래픽에 대해 전용 회로 집합을 사용해야 할 수 있는 상황이 여전히 있다는 것을 알고 있습니다. 이로 인해 Azure AD는 ExpressRoute 및 "기타 Office 365 온라인 서비스" 커뮤니티와 함께 Microsoft 피어링에 이미 있는 서비스를 사용하여 테넌트당 IP 범위 제한을 계속해서 지원할 예정입니다. 서비스가 영향을 받지만 ExpressRoute가 필요한 경우 다음을 수행해야 합니다.

- **Azure 공용 피어링에 있는 경우** Microsoft 피어링으로 이동하고 **기타 Office 365 온라인 서비스(12076:5100)** 커뮤니티에 등록합니다. Azure 공용 피어링에서 Microsoft 피어링으로 이동하는 방법에 대한 자세한 내용은 [Microsoft 피어링으로 공용 피어링 이동](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) 문서를 참조하세요.

- **Microsoft 피어링에 있는 경우** **기타 Office 365 온라인 서비스(12076:5100)** 커뮤니티에 등록합니다. 라우팅 요구 사항에 대한 자세한 내용은 ExpressRoute 라우팅 요구 사항 문서의 [BGP 커뮤니티에 대한 지원 섹션](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp)을 참조하세요.

전용 회로를 계속해서 사용해야 하는 경우 **기타 Office 365 온라인 서비스(12076:5100)** 커뮤니티를 사용하도록 권한을 얻는 방법에 대해 Microsoft 계정 팀에게 문의해야 합니다. MS Office 관리 검토 위원회는 해당 회로가 필요한지 여부를 확인하고 이를 유지하는 기술적 의미를 이해하도록 합니다. Office 365에 대한 경로 필터를 만들도록 시도하는 인증되지 않은 구독은 오류 메시지를 받습니다. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>TOU의 관리 시나리오를 위한 Microsoft Graph API

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 개발자 환경
 
Azure AD 사용 약관의 관리 작업에 대 한 Microsoft Graph Api를 추가 했습니다. 하면 만들기, 업데이트, 사용 하 여 개체의 용어를 삭제할 수 있습니다.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD 다중 테넌트 엔드포인트를 Azure AD B2C의 ID 공급자로 추가

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
사용자 지정 정책을 사용하여 이제 Azure AD B2C에서 ID 공급자로 Azure AD 공용 엔드포인트를 추가할 수 있습니다. 이를 통해 애플리케이션에 로그인하는 모든 Azure AD 사용자에 대한 항목의 단일 지점을 가질 수 있습니다. 자세한 내용은 [Azure Active Directory B2C: 사용자 지정 정책을 사용하여 사용자가 다중 테넌트 Azure AD ID 공급자에 로그인하도록 허용](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom)을 참조하세요.

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>내 앱 로그인 확장 및 Azure AD 애플리케이션 프록시를 통해 내부 URL을 사용하여 어디서나 앱에 액세스

**유형:** 새 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO
 
사용자는 이제 Azure AD에 대한 내 앱 보안 로그인 확장을 사용하여 회사 네트워크 외부에서도 내부 URL을 통해 애플리케이션에 액세스할 수 있습니다. 이는 액세스 패널 브라우저 확장이 설치되어 있는 모든 브라우저에서 Azure AD 애플리케이션 프록시를 사용하여 게시한 모든 애플리케이션에 사용할 수 있습니다. URL 리디렉션 기능은 사용자가 확장에 로그인하면 자동으로 활성화됩니다. 확장은 [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) 및 [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)에서 다운로드에 사용할 수 있습니다.

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - 유럽 고객을 위한 유럽의 데이터

**유형:** 새 기능  
**서비스 범주:** 기타  
**제품 기능:** GoLocal

유럽의 고객은 데이터를 유럽에 유지해야 하며 개인 정보 보호 및 유럽 법을 충족하도록 유럽 데이터 센터 외부에서 복제되지 않습니다. 이 [문서](https://go.microsoft.com/fwlink/?linkid=872328)는 유럽 내에서 저장되는 ID 정보에 대한 특정 세부 정보를 제공하고 유럽 데이터 센터 외부에 저장되는 정보에 대한 세부 정보도 제공합니다. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>새 사용자 프로비저닝 SaaS 앱 통합 - 2018년 5월

**유형:** 새 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합
 
Azure AD를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 SaaS 애플리케이션에서 사용자 ID 만들기, 유지 관리 및 제거를 자동화할 수 있습니다. 2018년 5월에 Azure AD 앱 갤러리에서 다음 애플리케이션에 대한 사용자 프로비저닝 지원을 추가했습니다.

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Azure AD 갤러리에서 사용자 프로비저닝을 지원하는 모든 응용 프로그램의 목록은 [https://aka.ms/appstutorial](https://aka.ms/appstutorial)을 참조하세요.

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>이제 그룹 및 앱 액세스에 대한 Azure AD 액세스 검토가 반복 검토 제공

**유형:** 새 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 거버넌스
 
이제 Azure AD Premium P2의 일부로 그룹 및 앱에 대한 액세스 검토를 일반적으로 사용할 수 있습니다.  관리자는 월간 또는 분기별과 같은 정기적인 간격으로 그룹 멤버 자격 및 애플리케이션 할당에 대한 액세스 검토를 자동으로 반복하도록 구성할 수 있습니다.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>이제 Azure AD 활동 로그(로그인 및 감사)를 MS Graph를 통해 사용 가능

**유형:** 새 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
이제 Azure AD 활동 로그(로그인 및 감사 로그 포함)를 MS Graph를 통해 사용할 수 있습니다. 이러한 로그에 액세스하도록 MS Graph를 통해 2개의 엔드포인트를 노출했습니다. Azure AD Reporting API에 프로그래밍 방식으로 액세스하여 시작하려면 [문서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)를 참조하세요. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>B2B 상환 환경 및 조직 떠나기에 대한 개선

**유형:** 새 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C

**JIT(Just in time) 상환:** B2B API를 사용하여 게스트 사용자와 리소스를 공유하면 특별한 초대 이메일을 보낼 필요가 없습니다. 대부분의 경우에서 게스트 사용자는 리소스에 액세스할 수 있으며 필요한 때에 상환 환경을 통해 제거됩니다. 누락된 이메일로 인한 영향이 더 이상 없습니다. 게스트 사용자에게 "시스템이 보낸 해당 상환 링크를 클릭했나요?"라고 더 이상 묻지 않습니다. 즉, SPO에서 초대 관리자를 사용하면 흐린 첨부 파일은 모든 상환 상태에서 내부 및 외부 모든 사용자에 대해 동일한 정식 URL을 가질 수 있습니다.

**최신 상환 환경:** 더 이상 화면 상환 방문 페이지를 분할하지 않습니다. 사용자는 타사 앱에 대해 수행하는 것과 같이 초대 조직의 개인정보처리방침과 함께 최신 동의 환경을 봅니다.

**게스트 사용자는 조직을 떠날 수 있음:** 조직과의 사용자의 관계가 끝나면 사용자는 스스로 조직을 떠날 수 있습니다. "제거되는" 초대 조직의 관리자를 더 이상 호출하지 않고, 지원 티켓을 더 이상 발생하지 않습니다.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 - 2018년 5월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2018년 5월에 페더레이션이 지원되는 신규 앱 18개가 앱 갤러리에 추가되었습니다.

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요.

Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)을 참조하세요.

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Azure Active Directory에 대한 새로운 단계별 배포 가이드

**유형:** 새 기능  
**서비스 범주:** 기타  
**제품 기능:** 디렉터리
 
Azure AD(Azure Active Directory)를 배포하는 방법에 대한 새로운 단계별 가이드이며 SSPR(셀프 서비스 암호 재설정), SSO(Single Sign-On), CA(조건부 액세스), 앱 프록시, 사용자 프로비전, PTA(통과 인증)에 대한 ADFS(Active Directory Federation Services), PHS(암호 해시 동기화)에 대한 ADFS가 포함됩니다.

배포 가이드를 보려면 GitHub의 [Identity Deployment Guides](https://aka.ms/DeploymentPlans)(ID 배포 가이드) 리포지토리로 이동하세요. 배포 가이드에 대한 피드백을 제공하려면 [배포 계획 사용자 의견 양식](https://aka.ms/deploymentplanfeedback)을 참조하세요. 배포 가이드에 대한 질문이 있는 경우 [IDGitDeploy](mailto:idgitdeploy@microsoft.com)에 문의하세요.

---

### <a name="enterprise-applications-search---load-more-apps"></a>Enterprise 애플리케이션 검색 - 앱 추가 로드

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
애플리케이션/서비스 사용자를 찾는 데 문제가 있나요? 엔터프라이즈 애플리케이션 모든 애플리케이션 목록에 더 많은 애플리케이션을 로드하는 기능을 추가했습니다. 기본적으로 20개의 애플리케이션을 표시합니다. 이제 **추가 로드**를 클릭하여 추가 애플리케이션을 볼 수 있습니다. 

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

새 애플리케이션 개체(예: 앱 프록시 사용)를 만들 때 **fragment** 및 **form_post** response_mode는 계속해서 작동합니다. 새 애플리케이션을 만들기 전에 이러한 response_mode 중 하나를 사용하는지 확인합니다.  

---
 
## <a name="april-2018"></a>2018년 4월 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C 액세스 토큰이 GA 상태입니다.

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C 

이제 액세스 토큰을 사용하여 Azure AD B2C로 보호된 Web API에 액세스할 수 있습니다. 기능은 공개 미리 보기에서 GA로 전환되고 있습니다. Azure AD B2C 애플리케이션 및 웹 API를 구성하는 UI 환경이 향상되었고 기타 사소한 사항이 개선되었습니다.
 
자세한 내용은 [Azure AD B2C: 액세스 토큰 요청](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)을 참조하세요.

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>SAML 기반 애플리케이션에 대한 Single Sign-On 구성 테스트

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

SAML 기반 SSO 애플리케이션을 구성하는 경우 구성 페이지에서 통합을 테스트할 수 있습니다. 로그인하는 동안 오류가 발생하면 테스트 환경에서 오류를 제공할 수 있으며 Azure AD는 특정 문제를 해결하는 해결 단계를 제공합니다.

자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 애플리케이션 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD 사용 약관 이제는 사용자 당

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 준수
 
이제 관리자는 지정된 ToU를 선택하고 해당 ToU에 동의한 사용자 및 발생한 날짜/시간을 확인할 수 있습니다.

자세한 내용은 [Azure AD 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)을 참조하세요.

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: AD FS 엑스트라넷 잠금 보호를 위한 위험한 IP 

**유형:** 새 기능  
**서비스 범주:** 기타  
**제품 기능:** 모니터링 및 보고

Connect Health는 이제 매시간 또는 매일 별로 실패한 U/P 로그인의 임계값을 초과하는 IP 주소를 검색하는 기능을 지원합니다. 이 기능에서 제공하는 기능은 다음과 같습니다.

- IP 주소 및 사용자 지정 가능한 임계값으로 매시간/매일 별로 생성된 로그인 실패 횟수를 보여주는 포괄적인 보고서
- 매시간/매일 별로 특정 IP 주소가 실패한 U/P 로그인의 임계값을 초과한 경우를 보여주는 이메일 기반 경고
- 데이터의 상세한 분석을 수행하는 다운로드 옵션

자세한 내용은 [위험한 IP 보고서](https://aka.ms/aadchriskyip)를 참조하세요.

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>메타데이터 파일 또는 URL을 사용한 간편한 앱 구성

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

Enterprise 애플리케이션 페이지에서 관리자는 SAML 메타데이터 파일을 업로드하여 AAD 갤러리 및 비 갤러리 애플리케이션에 대해 SAML 기반 로그온을 구성할 수 있습니다.

또한 Azure AD 애플리케이션 페더레이션 메타데이터 URL을 사용하여 대상 애플리케이션으로 SSO를 구성할 수 있습니다.

자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)을 참조하세요.

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>이제 Azure AD 사용 약관을 일반적으로 사용할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 준수
 

일반 공급을 공개 미리 보기에서 azure AD 사용 약관을 옮겼습니다.

자세한 내용은 [Azure AD 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)을 참조하세요.

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>특정 조직의 B2B 사용자 초대 허용 또는 차단

**유형:** 새 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

이제 Azure AD B2B 협업에서 공유하고 협업하려는 파트너 조직을 지정할 수 있습니다. 이를 위해 특정 허용 또는 거부 도메인의 목록을 만들도록 선택할 수 있습니다. 이러한 기능을 사용하여 도메인이 차단되면 직원은 해당 도메인의 사용자에게 더 이상 초대를 보낼 수 없습니다.

이렇게 하면 승인된 사용자를 위해 부드러운 환경을 활성화하는 동안 리소스에 대한 액세스를 제어할 수 있습니다.

이 B2B 협업 기능은 모든 Azure Active Directory 고객에 대해 사용할 수 있으며 외부 비즈니스 사용자가 로그인하고 액세스를 획득하는 시기 및 방법의 더욱 세부적인 제어를 위해 조건부 액세스 및 ID 보호와 같은 Azure AD Premium 기능과 함께 사용할 수 있습니다.

자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list)을 참조하세요.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

2018년 4월에 페더레이션이 지원되는 신규 앱 13개가 앱 갤러리에 추가되었습니다.

조건 HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [보안 서버(온-프레미스)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [동적 신호](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta 성능 모니터](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Shelf, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요.

Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)을 참조하세요.

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Azure AD의 B2B 사용자에게 온-프레미스 애플리케이션에 대한 액세스 권한 부여(공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C

Azure AD(Azure Active Directory) B2B 협업 기능을 사용하여 파트너 조직의 게스트 사용자를 Azure AD로 초대하는 조직의 경우 이제 이러한 B2B 사용자에게 온-프레미스 앱에 대한 액세스를 제공할 수 있습니다. 이러한 온-프레미스 앱은 SAML 기반 인증 또는 KCD(Kerberos 제한 위임)과 함께 IWA(Windows 통합 인증)를 사용할 수 있습니다.

자세한 내용은 [Azure AD의 B2B 사용자에게 온-프레미스 애플리케이션에 대한 액세스 권한 부여](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)를 참조하세요.

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Azure Marketplace에서 SSO 통합 자습서 가져오기

**유형:** 변경된 기능  
**서비스 범주:** 기타  
**제품 기능:** 타사 통합

[Azure 마켓플레이스](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1)에 나열된 애플리케이션이 SAML 기반 Single Sign-On을 지원하는 경우 **지금 사용해 보세요**를 클릭하면 해당 애플리케이션과 관련된 통합 자습서를 제공합니다. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>더 빨라진 SaaS 애플리케이션에 대한 Azure AD 자동 사용자 프로비저닝 성능

**유형:** 변경된 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합
 
이전에는 고객이 SaaS 애플리케이션용 Azure Active Directory 사용자 프로비저닝 커넥터(예: Salesforce, ServiceNow 및 Box)를 사용할 때 Azure AD 테넌트에 100,000개 이상의 결합된 사용자 및 그룹이 포함된 경우 성능이 저하되었으며 프로비전할 사용자를 결정하기 위해 사용자 및 그룹 할당이 사용되었습니다.

2018년 4월 2일에 Azure Active Directory와 대상 SaaS 애플리케이션 간의 초기 동기화를 수행하는 데 필요한 시간을 크게 줄이는 매우 중요한 성능 향상이 Azure AD 프로비저닝 서비스에 배포되었습니다.

따라서 앱에 대한 초기 동기화를 수행하는 데 수 일이 걸리거나 완료되지 않았던 많은 고객은 이제 몇 분 또는 몇 시간 내에 이를 완료합니다.

자세한 내용은 [프로비전하는 동안 어떻게 됩니까?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)를 참조하세요.

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>하이브리드 Azure AD 가입 컴퓨터에 대해 Windows 10 잠금 화면에서 셀프 서비스 암호 재설정

**유형:** 변경된 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 사용자 인증
 
하이브리드 Azure AD에 가입된 컴퓨터에 대한 지원을 포함하도록 Windows 10 SSPR 기능을 업데이트했습니다. 이 기능은 사용자가 Windows 10 컴퓨터의 잠금 화면에서 암호를 재설정하도록 허용하는 Windows 10 RS4에서 사용할 수 있습니다. 셀프 서비스 암호 재설정에 대해 활성화되거나 등록된 사용자는 이 기능을 활용할 수 있습니다.

자세한 내용은 [로그인 화면에서 Azure AD 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows)을 참조하세요.

---

## <a name="march-2018"></a>2018년 3월
 
### <a name="certificate-expire-notification"></a>인증서 만료 알림

**유형:** 수정됨  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
Azure AD는 갤러리 및 비갤러리 애플리케이션에 대한 인증서가 만료되려고 할 때 알림을 전송합니다. 

일부 사용자에게 SAML 기반 Single Sign On에 대해 구성된 엔터프라이즈 애플리케이션에 대한 알림이 수신되지 않았습니다. 이 문제가 해결되었습니다. Azure AD는 7, 30 및 60일 후에 만료되는 인증서에 대한 알림을 보냅니다. 감사 로그에서 이 이벤트를 볼 수 있습니다. 

자세한 내용은 다음을 참조하세요.

- [Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Azure Active Directory 포털의 감사 활동 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C의 Twitter 및 GitHub ID 공급자

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
이제 Azure AD B2C에서 ID 공급자로 Twitter 또는 GitHub를 추가할 수 있습니다. Twitter는 공개 미리 보기에서 GA로 전환되고 있습니다. GitHub는 공개 미리 보기로 릴리스되고 있습니다.

자세한 내용은 [Azure AD B2B 협업이란?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)을 참조하세요.
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>iOS 및 Android용 Azure AD 애플리케이션 기반 조건부 액세스와 함께 Intune Managed Browser를 사용하여 브라우저 액세스를 제한합니다.

**유형:** 새 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호
 
**현재 공개 미리 보기 상태입니다.**

**Intune Managed Browser SSO:** 직원은 모든 Azure AD 연결 앱에 대한 기본 클라이언트(예: Microsoft Outlook)와 Intune Managed Browser에서 Single Sign-On을 사용할 수 있습니다.

**Intune Managed Browser 조건부 액세스 지원:** 이제 직원이 애플리케이션 기반 조건부 액세스 정책을 사용하여 Intune Managed Browser를 사용하도록 할 수 있습니다.

[블로그 게시물](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)에서 이에 대해 자세히 읽어보세요.

자세한 내용은 다음을 참조하세요.

- [애플리케이션 기반 조건부 액세스 설정](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Managed Browser 정책 구성](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Powershell GA 모듈의 앱 프록시 Cmdlet

**유형:** 새 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control
 
애플리케이션 프록시 cmdlet에 대한 지원이 이제 Powershell GA 모듈에 포함되었습니다. 따라서 Powershell 모듈을 최신 상태로 유지해야 합니다. 버전이 1년보다 더 오래되면 일부 cmdlet이 작동하지 않을 수 있습니다. 

자세한 내용은 [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)를 참조하세요.
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365 네이티브 클라이언트는 비대화형 프로토콜을 사용하여 Seamless SSO에서 지원됨

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
Office 365 네이티브 클라이언트(버전 16.0.8730.xxxx 이상)를 사용하는 사용자는 Seamless SSO를 통해 자동 로그온 환경을 사용할 수 있게 됩니다. 이러한 지원은 비대화형 프로토콜(WS-Trust)을 Azure AD에 추가하여 제공됩니다.

자세한 내용은 [네이티브 클라이언트에서 Seamless SSO로 로그인하는 방식](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)을 참조하세요.
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>애플리케이션이 Azure AD의 테넌트 엔드포인트로 로그인 요청을 전송할 경우 Seamless SSO를 사용하여 자동 로그온 환경을 사용할 수 있음

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
애플리케이션(예: `https://contoso.sharepoint.com`)이 Azure AD의 공통 엔드포인트(`https://login.microsoftonline.com/common/<...>`) 대신 Azure AD의 테넌트 엔드포인트(즉, `https://login.microsoftonline.com/contoso.com/<..>` 또는 `https://login.microsoftonline.com/<tenant_ID>/<..>`)로 로그인 요청을 전송할 경우 사용자는 Seamless SSO를 사용하여 자동 로그온 환경을 사용할 수 있습니다.

자세한 내용은 [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)을 참조하세요. 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Seamless SSO를 롤아웃하기 위해 사용자의 인트라넷 영역 설정에 이전의 두 URL 대신 하나의 Azure AD URL만 추가해야 함

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
사용자에게 Seamless SSO를 롤아웃하려면 Active Directory의 그룹 정책을 사용하여 사용자의 인트라넷 영역 설정에 하나의 Azure AD URL만 추가해야합 니다. `https://autologon.microsoftazuread-sso.com` 이전에는 고객이 두 개의 URL을 추가해야 했습니다.

자세한 내용은 [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)을 참조하세요. 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

2018년 3월에 페더레이션이 지원되는 신규 앱 15개가 앱 갤러리에 추가되었습니다.

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요.

Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)을 참조하세요. 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Azure 리소스에 대한 PIM은 일반 공급됩니다.

**유형:** 새 기능  
**서비스 범주:** 권한 있는 ID 관리  
**제품 기능:** 권한 있는 ID 관리
 
디렉터리 역할에 대한 Azure AD Privileged Identity Management를 사용하는 경우, 이제 구독, 리소스 그룹, VIrtual Machines 등과 같은 Azure 리소스 역할 및 Azure Resource Manager에서 지원하는 기타 리소스에 대해 PIM의 시간 제한 액세스 및 할당 기능을 사용할 수 있습니다. Just-In-Time에 역할을 활성화할 경우 Multi-Factor Authentication을 적용하고 승인된 변경 기간에 맞춰 활성화를 예약합니다. 또한 이 릴리스에서는 업데이트 UI, 승인 워크플로 및 곧 만료되는 역할을 확장하고 만료된 역할을 갱신하는 기능을 비롯하여 공개 미리 보기 기간 동안 사용할 수 없는 개선 기능을 추가적으로 제공합니다.

자세한 내용은 [Azure 리소스에 대한 PIM(미리 보기)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)을 참조하세요.
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>앱 토큰에 선택적 클레임 추가(공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
이제 Azure AD 앱은 JWT 또는 SAML 토큰에서 사용자 지정 또는 선택적 클레임을 요청할 수 있습니다.  이러한 클레임은 크기 또는 적용 가능한 제약으로 인해 토큰에 기본적으로 포함되지 않는 사용자 또는 테넌트에 대한 클레임입니다.  현재는 v1.0 및 v2.0 엔드포인트에서 Azure AD 앱에 대한 공개 미리 보기로 사용됩니다.  추가될 수 있는 클레임과 해당 클레임을 요청하도록 애플리케이션 매니페스트를 편집하는 방법에 대한 내용은 해당 설명서를 참조하세요.  

자세한 내용은 [Azure AD의 선택적 클레임](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)을 참조하세요.
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD에서 보다 안전한 OAuth 흐름을 위해 PKCE를 지원함

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
Azure AD 설명서가 OAuth 2.0 권한 부여 코드 부여 흐름 동안 좀 더 안전한 통신을 허용하는 PKCE에 대한 지원 내용을 포함하도록 업데이트되었습니다.  S256 및 일반 텍스트 code_challenges 둘 다 v1.0 및 v2.0 엔드포인트에서 지원됩니다. 

자세한 내용은 [권한 부여 코드 요청](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code)을 참조하세요. 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Workday Get_Workers API에서 사용할 수 있는 모든 사용자 특성 값을 프로비전하도록 지원

**유형:** 새 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합
 
Workday에서 Active Directory 및 Azure AD로의 인바운드 프로비전에 대한 공개 미리 보기는 이제 Workday Get_Workers API에서 사용할 수 있는 모든 특성 값을 추출하고 프로비전하는 기능을 지원합니다. 이를 통해 Workday 인바운드 프로비전 커넥터의 초기 버전에 제공된 특성 외에, 수백 개의 추가 표준 및 사용자 지정 특성이 추가적으로 지원됩니다.

자세한 내용은 다음을 참조하세요. [Workday 사용자 특성 목록 사용자 지정](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>그룹 멤버 자격을 동적에서 정적으로 또는 그 반대로 변경

**유형:** 새 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업
 
그룹에서 멤버 자격을 관리하는 방식을 변경할 수 있습니다. 이것은 시스템에 동일한 그룹 이름과 ID를 유지하려는 경우에 유용하므로 그룹에 대한 기존 참조는 여전히 유효합니다. 새 그룹을 만들면 해당 참조를 업데이트해야 합니다.
이 기능을 지원하도록 Azure AD 관리 센터가 업데이트되었습니다. 이제, 고객은 기존 그룹을 동적 멤버 자격에서 할당된 멤버 자격으로 또는 그 반대로 변환할 수 있습니다. 기존 PowerShell cmdlet도 계속 사용할 수 있습니다.

자세한 내용은 참조 하세요. [Azure Active Directory에서 그룹에 대 한 동적 멤버 관리 규칙](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Seamless SSO로 로그아웃 동작 개선

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
이전에는 사용자가 Azure AD로 보호되는 애플리케이션에서 명시적으로 로그아웃하더라도 도메인에 가입된 장치에서 corpnet 내의 Azure AD 애플리케이션에 다시 액세스하려고 하면 Seamless SSO를 사용하여 자동으로 다시 로그인되었습니다. 이러한 변경으로 인해 이제 로그아웃이 지원됩니다.  따라서 사용자는 Seamless SSO를 사용하여 자동으로 로그인되는 대신, 다시 로그인하는 데 사용할 같거나 다른 Azure AD 계정을 선택할 수 있습니다.

자세한 내용은 [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)을 참조하세요.
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>애플리케이션 프록시 커넥터 버전 1.5.402.0이 릴리스됨

**유형:** 변경된 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** ID 보안 및 보호
 
이 커넥터 버전은 11월까지 점진적으로 롤아웃될 예정입니다. 이 새 커넥터 버전에는 다음과 같은 변경 내용이 포함되어 있습니다.

- 이제 커넥터는 하위 도메인 수준 대신, 도메인 수준 쿠키를 설정합니다. 이를 통해 SSO 환경이 좀 더 원활하게 진행되고 불필요한 인증 프롬프트가 방지됩니다.
- 청크 인코딩 요청 지원
- 향상된 커넥터 상태 모니터링 
- 몇 가지 버그 수정 및 안정성 개선

자세한 내용은 [Azure AD 애플리케이션 프록시 커넥터 이해](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)를 참조하세요.
 
---

## <a name="february-2018"></a>2018년 2월
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>사용자 및 그룹 관리를 위한 향상된 탐색

**유형:** 변경 계획  
**서비스 범주:** 디렉터리 관리  
**제품 기능:** 디렉터리

사용자 및 그룹 관리를 위한 탐색 환경이 간소화되었습니다. 이제 디렉터리 개요에서 직접 모든 사용자 목록으로 이동하여 삭제된 사용자 목록에 더 쉽게 액세스할 수 있습니다. 디렉터리 개요에서 모든 그룹 목록으로 직접 이동하여 그룹 관리 설정에 쉽게 액세스할 수 있습니다. 또한 디렉터리 개요 페이지에서 사용자, 그룹, 엔터프라이즈 애플리케이션 또는 앱 등록을 검색할 수 있습니다. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>21Vianet(Azure China 21Vianet)에서 운영하는 Microsoft Azure의 로그인 및 감사 보고서에 대한 가용성

**유형:** 새 기능  
**서비스 범주:** Azure Stack  
**제품 기능:** 모니터링 및 보고

Azure AD 활동 로그 보고서는 이제 21Vianet(Azure China 21Vianet) 인스턴스로 운영되는 Microsoft Azure에서 사용할 수 있습니다. 포함되는 로그는 다음과 같습니다.

- **로그인 활동 로그** - 테넌트와 관련된 모든 로그인 로그를 포함합니다.

- **셀프 서비스 암호 감사 로그** - 모든 SSPR 감사 로그를 포함합니다.

- **디렉터리 관리 감사 로그** - 사용자 관리, 앱 관리 등과 같은 디렉터리 관리와 관련된 모든 감사 로그를 포함합니다.

이러한 로그를 사용하면 환경이 작동하는 방식에 대한 인사이트를 얻을 수 있습니다. 제공된 데이터를 통해 다음을 수행할 수 있습니다.

- 사용자가 앱 및 서비스를 어떻게 활용하는지 정확히 파악할 수 있습니다.

- 사용자의 작업 진행에 장애가 되는 문제를 해결합니다.

이러한 보고서를 사용하는 방법에 대한 자세한 내용은 [Azure Active Directory 보고](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)를 참조하세요.

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>"보고서 구독자" 역할(관리자가 아닌 역할)을 사용하여 Azure AD 활동 보고서 보기

**유형:** 새 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

관리자가 아닌 역할을 통해 Azure AD 활동 로그에 액세스할 수 있도록 하는 고객 의견의 일환으로, "보고서 구독자" 역할에 속한 사용자가 Azure Portal 내에서 로그인 및 감사 활동에 액세스하고 Graph API를 사용할 수 있게 되었습니다. 

이러한 보고서를 사용하는 방법에 대한 자세한 내용은 [Azure Active Directory 보고](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)를 참조하세요. 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID 클레임을 사용자 특성 및 사용자 식별자로 사용할 수 있음

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
엔터프라이즈 애플리케이션 UI의 SAML 기반 로그온 애플리케이션에서 **EmployeeID**를 멤버 사용자 및 B2B 게스트에 대한 사용자 식별자 및 사용자 특성으로 구성할 수 있습니다.

자세한 내용은 [Azure Active Directory의 엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발급된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조하세요.

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시에서 와일드카드를 사용하여 간소화된 애플리케이션 관리

**유형:** 새 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** 사용자 인증
 
애플리케이션을 더 쉽게 배포하고 관리 오버헤드를 줄이기 위해 이제 와일드카드를 사용하여 애플리케이션을 게시하는 기능을 지원합니다. 와일드카드 애플리케이션을 게시하려면 표준 애플리케이션 게시 흐름을 따르지만 내부 및 외부 URL에 와일드카드를 사용할 수 있습니다.

자세한 내용은 [Azure Active Directory 애플리케이션 프록시의 와일드카드 애플리케이션](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)을 참조하세요.

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>애플리케이션 프록시 구성을 지원하는 새로운 cmdlet

**유형:** 새 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** 플랫폼

AzureAD PowerShell 미리 보기 모듈의 최신 릴리스에는 고객이 PowerShell을 사용하여 애플리케이션 프록시 애플리케이션을 구성할 수 있는 새로운 cmdlet이 포함되어 있습니다.

새로운 cmdlet은 다음과 같습니다. 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>그룹 구성을 지원하는 새로운 cmdlet

**유형:** 새 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** 플랫폼

AzureAD PowerShell 모듈의 최신 릴리스에는 Azure AD에서 그룹을 관리하는 cmdlet이 포함되어 있습니다. 이러한 cmdlet은 이전에 AzureADPreview 모듈에서 사용할 수 있었지만, 이제는 AzureAD 모듈에 추가되었습니다

현재 일반 공급으로 릴리스된 Group cmdlet은 다음과 같습니다. 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Azure AD Connect의 새 릴리스를 사용할 수 있음

**유형:** 새 기능  
**서비스 범주:** AD Sync  
**제품 기능:** 플랫폼
 
Azure AD Connect는 Windows Server Active Directory 및 LDAP를 포함하여 Azure AD와 온-프레미스 데이터 원본 간에 데이터를 동기화하는 기본 설정 도구입니다.

>[!Important]
>이 빌드는 스키마 및 동기화 규칙 변경 내용을 도입했습니다. Azure AD Connect 동기화 서비스는 업그레이드 후에 전체 가져오기 및 전체 동기화 단계를 트리거합니다. 이 동작을 변경하는 방법에 대한 자세한 내용은 [업그레이드 후 전체 동기화를 연기하는 방법](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade)을 참조하세요.

이 릴리스에 포함된 업데이트 및 변경 내용은 다음과 같습니다.

**수정된 문제**

- 다음 페이지로 전환할 때 파티션 필터링 페이지에 대한 백그라운드 작업에서 타이밍 창 수정

- ConfigDB 사용자 지정 작업을 수행하는 동안 액세스 위반이 발생하는 버그가 수정되었습니다.

- SQL 연결 시간 제한에서 복구할 버그가 수정되었습니다.

- SAN 와일드카드가 포함된 인증서에서 필수 구성 요소 검사에 실패하는 버그가 수정되었습니다.

- AAD 커넥터를 내보내는 동안 miiserver.exe가 충돌하는 버그가 수정되었습니다.

- 실행 시 DC에 잘못된 암호 시도가 기록되어 AAD 연결 마법사에서 구성이 변경되는 버그가 수정되었습니다.

**새 기능 및 향상된 기능**
 
- 애플리케이션 원격 분석 - 관리자가 이 데이터 클래스를 설정/해제할 수 있습니다.

- Azure AD 상태 데이터 - 관리자가 상태 포털을 방문하여 상태 설정을 제어해야 합니다. 서비스 정책이 변경되고 나면 에이전트에서 읽고 적용합니다.

- 디바이스 쓰기 저장 구성 작업 및 페이지 초기화에 대한 진행률 표시줄이 추가되었습니다.

- ZIP 텍스트/HTML 보고서에서 HTML 보고서 및 전체 데이터 수집을 통해 일반 진단 기능이 향상되었습니다.

- 자동 업그레이드의 안정성이 향상되고, 서버의 상태를 확인할 수 있도록 추가 원격 분석이 추가되었습니다.

- AD 커넥터 계정에서 권한 있는 계정에 사용할 수 있는 권한이 제한되었습니다. 새로 설치하는 경우 마법사는 MSOL 계정을 만든 후 권한 있는 계정이 MSOL 계정에 대해 갖는 권한을 제한합니다. 이 변경 내용은 자동 생성 계정을 통한 빠른 설치 및 사용자 지정 설치에 영향을 줍니다.

- AAD Connect를 새로 설치할 때 SA 권한이 필요하지 않도록 설치 관리자가 변경되었습니다.

- 특정 개체에 대한 동기화 문제를 해결하는 새로운 유틸리티가 추가되었습니다. 현재 유틸리티는 다음 사항을 확인합니다.

    - 동기화된 사용자 개체와 Azure AD 테넌트의 사용자 계정 간의 UserPrincipalName이 일치하지 않습니다.
  
    - 도메인 필터링으로 인해 개체가 동기화에서 필터링된 경우
  
    - OU(조직 구성 단위) 필터링으로 인해 개체가 동기화에서 필터링된 경우

- 특정 사용자 계정에 대해 온-프레미스 Active Directory에 저장된 현재 암호 해시를 동기화하는 새로운 유틸리티가 추가되었습니다. 유틸리티는 암호 변경이 필요하지 않습니다. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Azure AD 애플리케이션 기반 조건부 액세스에서 사용하기 위해 추가된 Intune 앱 보호 정책을 지원하는 애플리케이션

**유형:** 변경된 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

애플리케이션 기반 조건부 액세스를 지원하는 애플리케이션을 더 많이 추가했습니다. 이제 승인된 이러한 클라이언트 응용 프로그램을 사용하여 Office 365 및 기타 Azure AD 연결 클라우드 응용 프로그램에 액세스할 수 있습니다.

2월 말까지 추가될 애플리케이션은 다음과 같습니다.

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

자세한 내용은 다음을 참조하세요.

- [승인된 클라이언트 앱 요구 사항](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>모바일 환경에 update 사용 약관 

**유형:** 변경된 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 준수

사용 약관이 표시되면 **보는 데 문제가 있나요? 여기를 클릭하세요.** 를 클릭할 수 있습니다. 이 링크를 클릭하면 디바이스에서 기본적으로 사용 약관이 열립니다. 문서의 글꼴 크기 또는 디바이스의 화면 크기에 관계없이 필요에 따라 문서를 확대/축소하고 읽을 수 있습니다. 

---
 
## <a name="january-2018"></a>2018년 1월
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로 페더레이션된 앱 

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

2018년 1월에 페더레이션이 지원되는 다음과 같은 신규 앱이 앱 갤러리에 추가되었습니다.

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory 및 [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요.

Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)을 참조하세요. 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>추가 위험이 있는 로그인이 감지됨

**유형:** 새 기능  
**서비스 범주:** Identity Protection  
**제품 기능:** ID 보안 및 보호

감지된 위험 이벤트에 대해 얻은 정보는 Azure AD 구독에 연결됩니다. Azure AD Premium P2 버전에서 모든 기본 감지에 대한 가장 자세한 정보를 가져옵니다.

Azure AD Premium P1 버전에서 라이선스가 적용되지 않는 검색 항목이 ‘추가 위험이 있는 로그인이 감지됨’이라는 위험 이벤트로 표시됩니다.

자세한 내용은 [Azure Active Directory 위험 이벤트](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)를 참조하세요.
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>최종 사용자의 액세스 패널에서 Office 365 애플리케이션 숨기기

**유형:** 새 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO

새로운 사용자 설정을 통해 Office 365 애플리케이션을 사용자의 액세스 패널에 표시하는 방법을 보다 효과적으로 관리할 수 있습니다. Office 포털에 Office 앱만 표시하려는 경우, 이 옵션은 사용자의 액세스 패널에 있는 앱의 수를 줄이는 데 유용합니다. 이 설정은 **사용자 설정** 아래에 있으며 **사용자가 Office 365 포털에서 Office 365 앱만 볼 수 있음**이라는 레이블이 지정되어 있습니다.

자세한 내용은 [Azure Active Directory의 사용자 환경에서 애플리케이션 숨기기](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)를 참조하세요.

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>앱의 URL을 통해 암호 SSO가 가능한 앱에 간편하게 로그인 

**유형:** 새 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO

내 앱 SSO(Single-Sign On) 기능을 브라우저의 바로 가기로 제공하는 간편한 도구를 통해 내 앱 브라우저 확장을 사용할 수 있습니다. 앱을 설치하면 앱에 빠른 액세스를 제공하는 와플 아이콘이 브라우저에 표시됩니다. 사용자는 이제 다음과 같은 이점을 활용할 수 있습니다.

- 앱의 로그인 페이지에서 암호-SSO 기반 앱에 직접 로그인할 수 있는 기능
- 빠른 검색 기능을 사용하여 원하는 앱 실행
- 확장 프로그램의 최근 사용한 앱에 대한 바로 가기
- Microsoft Edge, Chrome 및 Firefox에 대한 확장을 사용할 수 있습니다.
 
자세한 내용은 [내 앱 보안 로그인 확장](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)을 참조하세요.

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure 클래식 포털에서 Azure AD 관리 환경 사용 중지

**유형:** 사용되지 않음   
**서비스 범주:** Azure AD  
**제품 기능:** 디렉터리

2018년 1월 8일부터 Azure 클래식 포털의 Azure AD 관리 환경 사용이 중지됩니다. Azure 클래식 포털의 사용 중지와 함께 적용됩니다. 앞으로 Azure AD의 포털 기반 관리를 위해서는 [Azure AD 관리 센터](https://aad.portal.azure.com)를 사용해야 합니다.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor 웹 포털의 사용이 중지됩니다.

**유형:** 사용되지 않음  
**서비스 범주:** Azure AD  
**제품 기능:** 디렉터리
 
2018년 1월 8일부터 PhoneFactor 웹 포털의 사용이 중지됩니다. 이 포털은 MFA 서버 관리에 사용되었으며, 해당 기능은 Azure Portal(portal.azure.com)로 이동되었습니다. 

MFA 구성 위치: **Azure Active Directory \> MFA 서버**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Azure AD 보고서 사용 중지

**유형:** 사용되지 않음  
**서비스 범주:** 보고  
**제품 기능:** ID 수명 주기 관리  


새로운 Azure Active Directory 관리 콘솔이 일반 공급되고 활동 및 보안 보고서를 위해 새로운 API가 제공됨에 따라서, 2017년 12월 31일부로 "/reports" 엔드포인트에 있는 보고서 API 사용이 중지되었습니다.

**사용할 수 있는 기능은 무엇인가요?**

새 관리 콘솔로 전환하면서 Azure AD 활동 로그를 검색할 수 있는 2개의 새로운 API를 만들었습니다. 새로 적용된 API는 다양한 감사 및 로그인 활동에 더해 다양한 필터링 및 정렬 기능을 제공합니다. 이전에 보안 보고서를 통해 사용 가능했던 데이터를 이제 Microsoft Graph의 ID 보호 위험 이벤트 API를 통해 액세스할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 보고 API를 시작](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Azure Active Directory ID 보호 및 Microsoft Graph 시작](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>2017년 12월

### <a name="terms-of-use-in-the-access-panel"></a>액세스 패널의 사용 약관

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 준수
 
이제 액세스 패널로 이동하면 이전에 수락한 사용 약관을 볼 수 있습니다.

다음 단계를 수행하세요.

1. [MyApps 포털](https://myapps.microsoft.com)로 이동하고 로그인합니다.

2. 오른쪽 위 모서리에서 자신의 이름을 선택한 다음 목록에서 **프로필**을 선택합니다. 

3. **프로필**에서 **사용 약관 검토**를 클릭합니다. 

4. 이제 내가 동의한 사용 약관을 검토할 수 있습니다. 

자세한 내용은 [Azure AD 사용 약관 기능(미리 보기)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)을 참조하세요.
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>새 Azure AD 로그인 환경

**유형:** 새 기능  
**서비스 범주:** Azure AD  
**제품 기능:** 사용자 인증
 
Azure AD와 Microsoft 계정 ID 시스템의 UI 모양과 느낌이 일관된 스타일로 새롭게 디자인되었습니다. 또한 Azure AD 로그인 페이지에서는 먼저 사용자 이름을 수집하고 두 번째 화면에서 자격 증명을 수집합니다.

자세한 내용은 [The new Azure AD Signin Experience is now in Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)(새 Azure AD 로그인 환경을 공개 미리 보기로 제공)를 참조하세요.
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>로그인 프롬프트 감소: Azure AD 로그인을 위한 새로운 "로그인 유지" 환경

**유형:** 새 기능  
**서비스 범주:** Azure AD  
**제품 기능:** 사용자 인증
 
Azure AD 로그인 페이지의 **로그인 상태 유지** 확인란을 사용자가 인증된 후에 표시되는 새 프롬프트로 바꾸었습니다. 

이 프롬프트에 사용자가 **예**로 응답하면 영구 새로 고침 토큰이 제공됩니다. 이 동작은 사용자가 이전 환경에서 **로그인 유지** 확인란을 선택할 때와 동일합니다. 페더레이션 된 테넌트에게는 페더레이션된 서비스로 인증이 성공한 후에 이 프롬프트가 표시됩니다.

자세한 내용은 [로그인 프롬프트 감소: Azure AD 로그인을 위한 새로운 "로그인 유지" 환경이 미리 보기로 제공됨](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)을 참조하세요. 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>동의하기 전에 사용 약관을 펼치도록 하는 구성 추가

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 준수
 
사용 약관을 수락하기 전에 사용 약관을 펼치도록 사용자에게 요구하는 옵션이 관리자에게 필요합니다.

사용자에게 사용 약관을 펼치도록 요구하려면 **On** 또는 **Off** 중 하나를 선택합니다. **On** 설정은 사용 약관을 수락하기 전에 확인하도록 사용자에게 요구합니다.

자세한 내용은 [Azure AD 사용 약관 기능(미리 보기)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)을 참조하세요.
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>적격 역할 할당에 대한 범위 지정 활성화

**유형:** 새 기능  
**서비스 범주:** 권한 있는 ID 관리  
**제품 기능:** 권한 있는 ID 관리
 
범위가 지정된 활성화를 사용하면 원래 할당 기본값보다 낮은 자율성을 적용하여 적절한 Azure 리소스 역할 할당을 활성화할 수 있습니다. 예를 들어, 테넌트의 구독 소유자로 할당되는 경우가 있습니다. 범위가 지정된 활성화를 사용하면 구독 내에 포함된 리소스 최대 5개에 대해 소유자 역할을 활성화할 수 있습니다(예: 리소스 그룹, 가상 머신). 활성화의 범위를 지정하면 중요한 Azure 리소스가 원치 않게 변경될 가능성을 줄일 수 있습니다.

자세한 내용은 [Azure AD Privileged Identity Management란?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)을 참조하세요.
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD 앱 갤러리의 새로운 페더레이션된 앱

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

2017년 12월에 페더레이션이 지원되는 다음과 같은 신규 앱이 앱 갤러리에 추가되었습니다.

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요.

Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)을 참조하세요. 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD 디렉터리 역할에 대한 승인 워크플로

**유형:** 변경된 기능  
**서비스 범주:** 권한 있는 ID 관리  
**제품 기능:** 권한 있는 ID 관리
 
Azure AD 디렉터리 역할의 승인 워크플로가 일반 공급됩니다.

승인 워크플로를 통해, 권한 있는 역할 관리자는 자격을 갖춘 역할 멤버가 권한 있는 역할을 사용하려면 그 전에 역할 활성화를 요청하도록 요청할 수 있습니다. 여러 사용자와 그룹이 승인 책임을 위임 받을 수 있습니다. 승인이 완료되고 역할이 활성화되면 자격을 갖춘 역할 멤버에게 알림이 전송됩니다.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>통과 인증: 비즈니스용 Skype 지원

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

통과 인증은 사용자가 온라인 및 하이브리드 토폴로지를 비롯한 최신 인증을 지원하는 비즈니스용 Skype 클라이언트 애플리케이션에 로그인하도록 지원합니다. 

자세한 내용은 [Skype for Business topologies supported with Modern Authentication](https://technet.microsoft.com/library/mt803262.aspx)(최신 인증에서 비즈니스용 Skype 토폴로지 지원됨)을 참조하세요.
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Azure RBAC에 대한 Azure AD Privileged Identity Management 업데이트(미리 보기)

**유형:** 변경된 기능  
**서비스 범주:** 권한 있는 ID 관리  
**제품 기능:** 권한 있는 ID 관리
 
Azure RBAC(Role-Based Access Control)에 대한 Azure AD PIM(Privileged Identity Management)의 공개 미리 보기 새로 고침으로 다음을 수행할 수 있습니다.

* Just Enough Administration을 사용할 수 있습니다.
* 리소스 역할을 활성화하기 위한 승인을 요청할 수 있습니다.
* Azure AD 및 Azure RBAC 역할 모두에 대한 승인이 필요한 역할의 향후 활성화를 예약할 수 있습니다.
 
자세한 내용은 [Azure 리소스용 Privileged Identity Management(미리 보기)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)를 참조하세요.

---
 
## <a name="november-2017"></a>2017년 11월
 
### <a name="access-control-service-retirement"></a>Access Control Service사용 중지

**유형:** 변경 계획  
**서비스 범주:** Access Control Service  
**제품 기능:** Access Control Service 

Azure Active Directory Access Control(또는 Access Control Service)은 2018년 말에 사용이 중지됩니다. 자세한 일정과 개괄적인 마이그레이션 지침을 비롯한 자세한 정보가 몇 주 내에 제공될 예정입니다. Access Control Service에 대한 질문이 있으면 이 페이지에 의견을 남겨주세요. 팀 멤버가 연락 드리겠습니다.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Intune Managed Browser에 대한 브라우저 액세스 제한 

**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

Intune Managed Browser를 승인된 앱으로 사용하면 Office 365 및 기타 Azure AD 연결 클라우드 앱에 대한 브라우저 액세스를 제한할 수 있습니다. 

애플리케이션 기반 조건부 액세스에 대해 다음과 같은 조건을 구성할 수 있습니다.

**클라이언트 앱:** 브라우저

**변경되면 무엇이 달라지나요?**

현재는 이 조건을 사용하면 액세스가 차단됩니다. 미리 보기를 사용할 수 있게 되면 모든 액세스에는 관리되는 브라우저 애플리케이션을 사용해야 합니다. 

앞으로 공개될 블로그와 릴리스 정보에서도 이 기능과 자세한 정보를 찾아볼 수 있습니다. 

자세한 내용은 [Azure AD의 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)를 참조하세요.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스에 대해 승인된 새 클라이언트 앱

**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

[승인된 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) 목록에는 다음과 같은 앱이 있습니다.

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

자세한 내용은 다음을 참조하세요.

- [승인된 클라이언트 앱 요구 사항](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>사용 약관을 여러 언어로 제공

**유형:** 새 기능    
**서비스 범주:** 사용 약관  
**제품 기능:** 준수

이제 관리자가 여러 PDF 문서가 포함된 새로운 사용 약관을 작성할 수 있습니다. PDF 문서에는 문서에서 사용된 언어를 태그로 지정할 수 있습니다. 사용자 기본 설정에 따라 사용자에게 일치하는 언어로 된 PDF가 표시됩니다. 일치하는 언어가 없으면 기본 언어가 표시됩니다.

---
 
### <a name="real-time-password-writeback-client-status"></a>실시간 비밀번호 쓰기 저장 클라이언트 상태

**유형:** 새 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 사용자 인증

이제 온-프레미스 비밀번호 쓰기 저장 클라이언트의 상태를 검토할 수 있습니다. 이 옵션은 [암호 재설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) 페이지의 **온-프레미스 통합** 섹션에서 확인할 수 있습니다. 

온-프레미스 쓰기 저장 클라이언트와의 연결에 문제가 있는 경우 다음과 같은 정보를 제공하는 오류 메시지가 표시됩니다.

- 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없는 이유
- 문제 해결에 도움이 되는 설명서 링크 

자세한 내용은 [온-프레미스 통합](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)을 참조하세요.

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스 
 
**유형:** 새 기능  
**서비스 범주:** Azure AD  
**제품 기능:** ID 보안 및 보호

이제 [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)를 사용하여 Intune 앱 보호 정책을 지원하는 [승인된 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)으로 Office 365 및 기타 Azure AD에 연결된 클라우드 앱의 액세스를 제한할 수 있습니다. 승인된 클라이언트 애플리케이션에서 기업 데이터를 구성하고 보호하는 데 Intune 앱 보호 정책이 사용됩니다.

[앱 기반](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) 조건부 액세스 정책과 [디바이스 기반](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) 조건부 액세스 정책이 결합됨으로써 개인 디바이스와 기업 디바이스의 데이터를 유연하게 보호할 수 있게 됩니다.

다음은 앱 기반 조건부 액세스에 사용 가능한 조건과 컨트롤입니다.

**지원되는 플랫폼 조건**

- iOS
- Android

**클라이언트 앱 조건**

- 모바일 앱 및 데스크톱 클라이언트

**액세스 제어**

- 승인된 클라이언트 앱 필요

자세한 내용은 [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)를 참조하세요.
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure Portal에서 Azure AD 디바이스 관리

**유형:** 새 기능  
**서비스 범주:** 디바이스 등록 및 관리  
**제품 기능:** ID 보안 및 보호

이제 Azure AD에 연결된 디바이스와 디바이스 관련 활동을 모두 한 곳에서 확인할 수 있습니다. Microsoft는 Azure Portal에서 모든 디바이스 ID와 설정을 한 번에 관리할 수 있는 새로운 관리 환경을 선보입니다. 이 릴리스에서는 다음과 같은 기능을 수행할 수 있습니다.

- Azure AD의 조건부 액세스에 사용할 수 있는 모든 디바이스를 봅니다.
- 하이브리드 Azure AD 조인 디바이스를 비롯한 속성 보기
- Azure AD 조인 디바이스의 BitLocker 키 찾기, Intune을 사용하여 디바이스 관리하기
- Azure AD 디바이스 관련 설정을 관리합니다.

자세한 내용은 [Azure Portal을 사용하여 디바이스 관리](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal)를 참조하세요.

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Azure AD 조건부 액세스의 디바이스 플랫폼으로서 macOS 지원 

**유형:** 새 기능    
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호 

이제 Azure AD 조건부 액세스 정책에서 디바이스 플랫폼 조건으로서 macOS를 추가 또는 제외할 수 있습니다. 지원되는 디바이스 플랫폼에 macOS가 추가되어 다음과 같은 기능이 지원됩니다.

- **Intune을 사용하여 macOS 디바이스를 등록하고 관리합니다.** iOS, Android와 같은 여타 플랫폼과 마찬가지로 macOS의 경우에도 통합 등록을 수행하는 기업 포털 애플리케이션이 제공됩니다. 새로운 macOS용 기업 포털 앱을 이용하면 Intune을 사용하여 디바이스를 등록하고 등록한 디바이스를 Azure AD에 등록할 수 있습니다.
- **macOS 디바이스가 Intune에 정의된 조직의 준수 정책을 준수하는지 확인합니다.** 이제 Azure Portal의 Intune에서 macOS 디바이스에 대한 준수 정책을 설정할 수 있습니다. 
- **Azure AD에서 애플리케이션에 대한 액세스를 호환 가능한 macOS 장치로 제한합니다.** 조건부 액세스 정책에는 macOS가 별도의 디바이스 플랫폼 옵션으로 있습니다. 이제 Azure에 설정된 대상 애플리케이션에 대해 macOS 전용 조건부 액세스 정책을 작성할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Intune을 사용하여 macOS 디바이스를 위한 디바이스 준수 정책 만들기](https://aka.ms/macoscompliancepolicy)
- [Azure AD의 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication용 네트워크 정책 서버 확장 

**유형:** 새 기능    
**서비스 범주:**  Multi-Factor Authentication  
**제품 기능:** 사용자 인증

Azure Multi-Factor Authentication용 네트워크 정책 서버 확장은 기존 서버를 사용하여 인증 인프라에 클라우드 기반 다단계 인증 기능을 추가합니다. 네트워크 정책 서버 확장을 사용하면, 전화 통화, 문자 메시지 또는 휴대폰 앱 확인을 기존 인증 흐름에 추가할 수 있습니다. 새 서버를 설치, 구성 및 유지 관리할 필요가 없습니다. 

이 확장은 Azure Multi-Factor Authentication 서버를 배포하지 않고 가상 사설망 연결을 보호하려는 조직을 위해 작성되었습니다. 네트워크 정책 서버 확장은 RADIUS 및 클라우드 기반 Azure Multi-Factor Authentication 간에 어댑터로 작동하여 페더레이션 사용자 또는 동기화된 사용자를 위한 또 다른 인증을 제공합니다.

자세한 내용은 [기존 네트워크 정책 서버 인프라를 Azure Multi-Factor Authentication과 통합](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension)을 참조하세요.
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>삭제된 사용자 복원 또는 영구 제거

**유형:** 새 기능    
**서비스 범주:** 사용자 관리  
**제품 기능:** 디렉터리 

이제 Azure AD 관리 센터에서 다음과 같은 기능을 수행할 수 있습니다.

- 삭제된 사용자 복원 
- 사용자 영구 삭제

**기능 사용해 보기:**

1. Azure AD 관리 센터의 **관리** 섹션에서 [모든 사용자](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All)를 선택합니다. 

2. **표시** 목록에서 **최근 삭제된 사용자**를 선택합니다. 

3. 최근 삭제된 사용자를 하나 이상 선택한 다음 복원하거나 영구 삭제합니다.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스에 대해 승인된 새 클라이언트 앱
 
**유형:** 변경된 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

[승인된 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)에 다음과 같은 앱이 추가되어 있습니다.

- Microsoft Planner
- Azure Information Protection 

자세한 내용은 다음을 참조하세요.

- [승인된 클라이언트 앱 요구 사항](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>조건부 액세스 정책에서 여러 컨트롤 사이에 ‘OR’ 사용 

**유형:** 변경된 기능    
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호
 
이제 조건부 액세스 제어에 "OR"(선택한 컨트롤 중 하나 필요) 연산자를 사용할 수 있습니다. 이 기능을 사용하여 액세스 제어 사이에 "OR"이 있는 정책을 만들 수 있습니다. 사용자가 다단계 인증을 사용하거나 규정을 준수하는 디바이스를 사용하도록 요구하려는 경우 "OR" 연산자를 사용하여 정책을 만들 수 있습니다.

자세한 내용은 [Azure AD 조건부 액세스의 컨트롤](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls)을 참조하세요.
 
---

### <a name="aggregation-of-real-time-risk-events"></a>실시간 위험 이벤트의 집계

**유형:** 변경된 기능    
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호

Azure AD ID 보호에서는 특정 날짜에 동일한 IP 주소에서 발생한 모든 실시간 위험 이벤트가 위험 이벤트 유형별로 집계됩니다. 이 변경으로 인해 사용자 보안에 영향을 주지 않지만, 표시되는 위험 이벤트의 양이 제한됩니다.

사용자가 로그인할 때마다 기반 실시간 감지가 작동합니다. Multi-Factor Authentication 또는 액세스 차단이 설정된 로그인 위험 보안 정책이 있어도 위험한 로그인이 있을 때마다 계속 트리거됩니다.
 
---
 
## <a name="october-2017"></a>2017년 10월

### <a name="deprecate-azure-ad-reports"></a>Azure AD 보고서 사용 중지

**유형:** 변경 계획  
**서비스 범주:** 보고  
**제품 기능:** ID 수명 주기 관리  

Azure Portal은 다음을 제공합니다.

- 새로운 Azure AD 관리 콘솔
- 활동 및 보안 보고서를 위한 새로운 API
 
새로운 기능으로 인해 /reports 엔드포인트의 보고서 API는 2017년 12월 10일에 사용 중지됩니다. 

---

### <a name="automatic-sign-in-field-detection"></a>자동 로그인 필드 감지

**유형:** 수정됨   
**서비스 범주:** 내 앱  
**제품 기능:** SSO(Single sign-on)  

Azure AD는 HTML 사용자 이름 및 암호 필드를 렌더링하는 애플리케이션의 자동 로그인 필드 검색을 지원합니다. 이 단계는 [애플리케이션에 대한 로그인 필드를 자동으로 캡처하는 방법](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application)에 설명되어 있습니다. 이 기능은 [Azure Portal](https://aad.portal.azure.com)의 **엔터프라이즈 애플리케이션** 페이지에 *비갤러리* 애플리케이션을 추가하여 찾을 수 있습니다. 또한 이 새 애플리케이션의 **Single Sign-On** 모드를 **암호 기반 Single Sign-on**으로 구성하고 웹 URL을 입력한 다음, 페이지를 저장할 수 있습니다.
 
서비스 문제로 인해 이 기능은 일시적으로 사용하지 않도록 설정되어 있었습니다. 이제 문제가 해결되어 자동 로그인 필드 검색을 다시 사용할 수 있습니다.

---

### <a name="new-multi-factor-authentication-features"></a>새로운 Multi-Factor Authentication 기능

**유형:** 새 기능  
**서비스 범주:** Multi-Factor Authentication  
**제품 기능:** ID 보안 및 보호  

MFA(Multi-Factor Authentication)는 조직을 보호하기 위한 필수적인 요소입니다. 자격 증명의 적응성을 향상시키고 MFA 환경을 더욱 원활하게 만들기 위해 다음과 같은 기능이 추가되어 있습니다. 

- 다단계 인증 결과는 Azure AD 로그인 보고서에 직접 통합되며, 여기에는 MFA 결과에 대한 프로그래밍 방식의 액세스가 포함됩니다.
- MFA 구성이 Azure Portal의 Azure AD 구성 환경에 보다 깊숙이 통합되었습니다.

이 공개 미리 보기에서는 핵심 Azure AD 구성 환경에 통합된 MFA 관리와 보고 기능을 경험할 수 있습니다. 이제 Azure AD 환경에서 MFA 관리 포털 기능을 관리할 수 있습니다.

자세한 내용은 [Azure Portal의 MFA보고에 대한 참조](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa)를 참조하세요. 

---

### <a name="terms-of-use"></a>사용 약관

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 준수  

Azure AD 이용 약관을 사용하여 법률 요구 사항 또는 규정 준수 요구 사항에 대한 관련 면책 조항과 같은 정보를 사용자에게 제공할 수 있습니다.

다음 시나리오에서 Azure AD 사용 약관을 사용할 수 있습니다.

- 조직의 모든 사용자에 대한 일반 사용 약관
- 사용자의 특성에 기반한 특정 사용 약관(예: 의사 및 간호사 또는 국내 및 해외 직원, 동적 그룹에 의한 수행)
- Salesforce와 같은 영향력이 높은 비즈니스 앱에 액세스하기 위한 특정 이용 약관

자세한 내용은 [Azure AD 사용 약관](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)을 참조하세요.

---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management의 기능 개선

**유형:** 새 기능  
**서비스 범주:** 권한 있는 ID 관리  
**제품 기능:** 권한 있는 ID 관리  

이제 Azure AD Privileged Identity Management를 사용하여 조직에서 Azure Resources(미리 보기)에 대한 액세스를 관리, 제어 및 모니터링할 수 있습니다.

- 구독
- 리소스 그룹
- 가상 머신 

Azure Portal에서 Azure RBAC 기능을 사용하는 모든 리소스는 Azure AD Privileged Identity Management가 제공하는 보안 및 수명 주기 관리 기능을 모두 사용할 수 있습니다.

자세한 내용은 [Azure 리소스용 Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)를 참조하세요.

---

### <a name="access-reviews"></a>액세스 검토

**유형:** 새 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 준수  

조직은 액세스 검토(미리 보기)를 통해 그룹 멤버 자격을 효율적으로 관리하고 엔터프라이즈 애플리케이션에 액세스할 수 있습니다. 

- 애플리케이션 및 그룹 멤버 자격의 액세스 권한에 대한 액세스 검토를 사용하여 게스트 사용자 액세스를 다시 인증할 수 있습니다. 검토자는 액세스 검토로 제공되는 통찰력을 기반으로 게스트에게 계속 액세스를 허용할지를 효율적으로 결정할 수 있습니다.
- 액세스 검토를 사용하여 애플리케이션 및 그룹 멤버 자격에 대한 직원 액세스를 다시 인증할 수 있습니다.

액세스 검토 컨트롤을 조직과 관련된 프로그램으로 수집하여 규정 준수 또는 위험 감지 애플리케이션에 대한 검토를 추적할 수 있습니다.

자세한 내용은 [Azure AD 액세스 검토](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)를 참조하세요.

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>내 앱 및 Office 365 앱 시작 관리자에서 타사 애플리케이션 숨기기

**유형:** 새 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO(Single sign-on)  

이제 새 **앱 숨기기** 속성을 통해 사용자 포털에 표시되는 앱을 더 잘 관리할 수 있습니다. 백 엔드 서비스를 위해 앱 타일이 표시되거나 타일이 중복되고 사용자의 앱 시작 관리자가 복잡하게 표시되는 경우 앱을 숨기면 도움이 됩니다. 토글은 타사 앱 섹션의 **속성** 섹션에 있으며 **사용자가 볼 수 있습니까?** 라는 레이블이 지정되어 있습니다. PowerShell을 통해 프로그래밍 방식으로 앱을 숨길 수도 있습니다. 

자세한 내용은 [Hide a third-party application from user's experience in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)(Azure AD의 사용자 환경에서 타사 애플리케이션 숨기기)를 참조하세요. 


**사용할 수 있는 기능은 무엇인가요?**

 새 관리 콘솔로 전환하면서 Azure AD 활동 로그를 가져오는 2개의 API가 새롭게 제공됩니다. 새로 적용된 API는 다양한 감사 및 로그인 활동에 더해 다양한 필터링 및 정렬 기능을 제공합니다. 이전에 보안 보고서를 통해 사용 가능했던 데이터를 이제 Microsoft Graph의 ID 보호 위험 이벤트 API를 통해 액세스할 수 있습니다.


## <a name="september-2017"></a>2017년 9월

### <a name="hotfix-for-identity-manager"></a>Identity Manager용 핫픽스

**유형:** 변경된 기능  
**서비스 범주:** Identity Manager  
**제품 기능:** ID 수명 주기 관리  

핫픽스 롤업 패키지(빌드 4.4.1642.0)는 2017년 9월 25일부터 Identity Manager 2016 서비스 팩 1용으로 사용할 수 있습니다. 이 롤업 패키지는 다음과 같습니다.

- 문제를 해결하고 개선 사항을 추가합니다.
- Identity Manager 2016용 빌드 4.4.1459.0까지 모든 Identity Manager 2016 서비스 팩 1 업데이트를 대체하는 누적 업데이트입니다. 
- Identity Manager 2016 빌드 4.4.1302.0이 필요합니다. 

자세한 내용은 [Identity Manager 2016 서비스 팩 1용 핫픽스 롤업 패키지(빌드 4.4.1642.0) 사용 가능](https://support.microsoft.com/help/4021562)을 참조하세요. 

---
