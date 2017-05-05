---
title: "Azure Active Directory B2B 공동 작업 FAQ | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업에 대해 자주 묻는 질문"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/13/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: c06e8b98fea6036704b07b0f12397fc4bb59cb3f
ms.lasthandoff: 04/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Azure Active Directory B2B 공동 작업 FAQ(자주 묻는 질문)

자주 묻는 질문은 새로운 관심사를 반영하도록 주기적으로 업데이트됩니다.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>Azure 클래식 포털에서 이 기능을 사용할 수 있습니까?
Azure AD B2B 공동 작업의 새 기능은 [Azure Portal](https://portal.azure.com) 및 새 액세스 패널을 통해서만 사용할 수 있습니다. 

### <a name="is-it-possible-to-customize-our-sign-in-page-so-its-more-intuitive-for-your-b2b-collaboration-guest-users"></a>B2B 공동 작업 게스트 사용자에게 더 직관적인 환경이 되도록 로그인 페이지를 사용자 지정할 수 있나요?
그렇습니다. [기능을 설명하는 블로그 게시물](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/)이 있고 [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가](active-directory-add-company-branding.md)에 설명되어 있습니다.

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B 공동 작업 사용자가 SharePoint Online 및 OneDrive에 액세스할 수 있습니까?
예. 그러나 SharePoint Online 사용자 선택에서 기존 게스트 사용자를 검색하는 기능은 기존 동작과의 일치를 위해 기본적으로 꺼져 있습니다. 이 기능은 테넌트 및 사이트 모음 수준에서 설정 'ShowPeoplePickerSuggestionsForGuestUsers'를 통해 사용하도록 설정할 수 있습니다. 또한 멤버가 디렉터리에서 모든 기존 게스트 사용자를 검색할 수 있도록 허용하는 Set-SPOTenant 및 Set-SPOSite cmdlet을 사용하여 설정할 수 있습니다. 테넌트 범위에 대한 변경 내용은 이미 프로비전된 SharePoint Online 사이트에 영향을 주지 않습니다.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>CSV 업로드 메커니즘이 계속 지원됩니까?
예. 포함된 [PowerShell 샘플](active-directory-b2b-code-samples.md)을 참조하세요.

### <a name="how-can-i-customize-my-invitation-emails"></a>초대 전자 메일을 어떻게 사용자 지정할 수 있습니까?
[B2B 초대 API](active-directory-b2b-api.md)를 사용하여 거의 모든 초대자 프로세스를 사용자 지정할 수 있습니다.

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>초대된 외부 사용자가 자신을 초대한 조직을 떠날 수 있습니까?
현재는 이 기능을 사용할 수 없습니다.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>이제 게스트 사용자가 MFA(Multi-Factor Authentication)를 사용할 수 있으므로 MFA 메서드도 다시 설정할 수 있습니까?
예, 일반 사용자가 설정할 수 있는 방법과 동일합니다.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>어떤 조직이 MFA 라이선스를 담당합니까?
초대 조직은 MFA에 개입하여 MFA를 수행하는 조직입니다. 따라서 초대 조직은 MFA를 수행하는 B2B 사용자에게 충분한 라이선스가 있는지 확인할 책임이 있습니다.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>이미 파트너 조직에 MFA가 설정되어 있다면 어떻게 됩니까? MFA를 신뢰하지만 사용하지 않을 수 있습니까?
이후 릴리스에서 이 기능을 지원할 예정입니다. 해당 릴리스가 발표되면 자신(초대 조직)의 MFA에서 제외할 특정 파트너를 선택할 수 있을 것입니다.

### <a name="how-can-i-achieve-delayed-invitations"></a>지연된 초대는 어떻게 얻을 수 있습니까?
일부 조직에서는 B2B 공동 작업 사용자를 추가하고 프로비전이 필요한 응용 프로그램에 프로비전한 다음 초대를 보내려고 합니다. 그렇다면 B2B 공동 작업 초대 API를 사용하여 온보딩 워크플로를 사용자 지정할 수 있습니다.

### <a name="can-i-make-my-guest-users-limited-admins"></a>내 게스트 사용자를 제한된 관리자로 만들 수 있습니까?
그렇습니다. 조직에 필요한 경우 [역할에 게스트 사용자 추가](active-directory-users-assign-role-azure-portal.md)에서 설명하는 방법을 확인합니다.

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B 공동 작업 지원을 통해 B2B 사용자가 Azure Portal에 액세스할 수 있습니까?
B2B 공동 작업 사용자는 제한된 관리자 또는 전역 관리자 역할이 할당되지 않으면 Azure Portal에 액세스할 필요가 없습니다. 이 경우 포털에 액세스할 수 있습니다. 이 역할에 속하지 않은 게스트 사용자가 포털에 액세스하면 이전 섹션에서 설명한 대로 게스트 사용자 역할에 디렉터리에 대한 특정 권한이 있으므로 사용자는 환경의 특정 부분에 액세스할 수 있습니다.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>게스트 사용자를 위해 Azure Portal에 대한 액세스를 차단할 수 있습니까?
예! 그러나 이 정책을 구성할 때는 실수로 구성원과 관리자에 대한 액세스를 차단하지 않도록 주의하세요.
게스트 사용자가 다음 세 단계에 따라 Windows Azure Service Management API의 조건부 액세스 정책을 통해 [Azure Portal](https://portal.azure.com)에 대한 액세스를 차단할 수 있습니다.
1. **모든 사용자** 그룹을 구성원만 포함하도록 수정합니다. ![그룹 수정 스크린샷](media/active-directory-b2b-faq/modify-all-users-group.png)
2. 게스트 사용자를 포함하는 동적 그룹을 만듭니다. ![그룹 만들기 스크린샷](media/active-directory-b2b-faq/group-with-guest-users.png)
3. 다음 비디오에서 보여 주듯이 조건부 액세스 정책을 설정하여 게스트 사용자가 포털에 액세스하지 못하도록 차단합니다.
  
  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Azure AD B2B 공동 작업이 MFA 및 소비자 전자 메일 계정에 대한 지원을 시작할 일정은 어떻게 됩니까?
현재 MFA 및 소비자 메일 계정이 둘 다 지원됩니다.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Azure AD B2B 공동 작업 사용자를 위한 암호 다시 설정을 지원할 계획이 있습니까?
예. ID 테넌트에서 리소스 테넌트에 초대된 B2B 사용자의 SSPR(셀프 서비스 암호 재설정)에 대한 자세한 내용은 다음과 같습니다.
 
* SSPR는 B2B 사용자의 ID 테넌트에서만 발생합니다.
* ID 테넌트가 Microsoft 계정이면 Microsoft 계정 SSPR 메커니즘을 사용합니다.
* ID 테넌트가 JIT(Just-In-Time)/바이럴 테넌트이면 암호 재설정 메일이 전송됩니다.
* 이외의 경우에는 B2B 사용자에 대한 표준 SSPR 프로세스가 수행되고, 구성원과 마찬가지로 리소스 테넌트의 컨텍스트에서 B2B 사용자에 대한 SSPR가 차단됩니다.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>바이럴 테넌트의 사용자도 사용할 수 있습니까?
현재는 아닙니다.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM에서 Azure AD B2B 공동 작업에 대한 온라인 지원을 제공합니까?
이를 지원하기 위한 작업이 진행 중입니다.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>새로 만든 B2B 공동 작업 사용자의 초기 암호 수명은 어떻게 됩니까?
Azure AD에는 모든 Azure AD 클라우드 사용자 계정에 동등하게 적용되는 고정된 문자 집합, 암호 강도 및 계정 잠금 요구 사항이 있습니다. 클라우드 사용자 계정은 Microsoft 계정, Facebook, ADFS 또는 다른 클라우드 테넌트(B2B 공동 작업의 경우)와 같은 다른 ID 공급자와 페더레이션 되지 않는 계정입니다. 페더레이션 계정의 경우 암호 정책은 온-프레미스 테넌트의 정책과 사용자의 Microsoft 계정 설정에 따라 다릅니다.

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>응용 프로그램에서 테넌트 사용자와 게스트 사용자 간 환경을 차별화하고자 합니다. 이에 대한 표준 지침이 있습니까? ID 공급자 클레임의 현재 상태가 이에 대한 모델이 맞습니까?
 
게스트 사용자는 [B2B 공동 작업 사용자의 속성](active-directory-b2b-user-properties.md)에서 논의한 대로 ID 공급자를 사용하여 인증할 수 있습니다. 따라서 UserType은 이를 확인하기 위한 속성이 맞습니다. UserType 클레임은 현재 토큰에 현재 포함되지 않습니다. 응용 프로그램은 Graph API를 사용하여 사용자 및 사용자의 UserType 가져오기에 대한 디렉터리를 쿼리해야 합니다.

### <a name="where-can-find-a-b2b-collaboration-community-to-share-solutions-and-submit-ideas"></a>솔루션을 공유하고 아이디어를 제출하는 B2B 공동 작업 커뮤니티를 어디에서 찾을 수 있습니까?

B2B 공동 작업 개선 방법에 대한 의견을 지속적으로 수렴하고 있습니다. [Microsoft 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)에서 토론에 참가하여 Azure AD B2B 공동 작업에 대한 사용자 시나리오, 모범 사례 및 원하는 사항을 공유해 보세요.
 
[B2B 공동 작업 아이디어](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas) 사이트에서 아이디어를 제출하고 향후 기능에 대해 투표할 수도 있습니다.

### <a name="is-there-a-way-to-invite-the-user-such-that-the-invitation-is-automatically-redeemed-and-the-user-is-just-ready-to-go-or-will-the-user-always-have-to-click-through-to-the-redemption-url"></a>초대가 자동으로 상환되고 사용자가 “준비 완료” 상태가 되도록 사용자를 초대할 방법이 있나요? 아니면 사용자가 항상 상환 URL을 클릭해야 하나요?
초대된 조직(B2B 사용자 조직)의 구성원이기도 한 초대하는 조직의 사용자가 보낸 초대는 B2B 사용자가 상환할 필요가 없습니다.

초대된 조직에서 초대하는 조직으로 한 명의 사용자를 초대하는 방식으로 이 작업을 수행하는 것이 좋습니다. [리소스 조직에서 게스트 초대자 역할에 이 사용자를 추가합니다](active-directory-b2b-add-guest-to-role.md). 이 사용자는 해당 조직의 B2B 사용자가 초대를 상환하도록 요청할 필요 없이 로그인 UI, PowerShell 스크립트 또는 API를 통해 초대된 조직의 다른 사용자를 초대할 수 있습니다.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>초대된 파트너가 페더레이션을 사용하여 자체 온-프레미스 인증을 추가하면 B2B 공동 작업이 어떻게 진행되나요?
파트너에게 온-프레미스 인증 인프라로 페더레이션된 Azure AD 테넌트가 있는 경우 온-프레미스 SSO(Single Sign-On)가 자동으로 이루어집니다. 파트너에게 Azure AD 테넌트가 없는 경우 들어오는 사용자에 대한 Azure AD 계정이 생성됩니다. 

### <a name="i-didnt-think-azure-ad-b2b-would-accept-gmailcom-and-outlookcom-email-addresses-b2c-was-what-you-used-for-those"></a>Azure AD B2B가 gmail.com 및 outlook.com 메일 주소를 허용하는 것 같지 않습니다. 해당 메일 주소를 B2C에 사용했습니다.
지원되는 ID에 관한 B2B 및 B2C의 차이점을 제거하고 있습니다. 사용된 ID는 B2B 또는 B2C를 사용할지 결정하기 위한 좋은 기준이 아닙니다. 사용할 항목을 결정하려면 [Azure Active Directory에서 B2B 공동 작업과 B2C 비교](active-directory-b2b-compare-b2c.md) 문서를 참조하세요.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>어떤 응용 프로그램 및 서비스에서 Azure B2B 게스트 사용자를 지원하나요?
모든 Azure AD 통합 응용 프로그램. 

### <a name="is-it-possible-to-force-mfa-for-b2b-guest-users-if-partners-have-no-mfa-enabled"></a>파트너가 MFA를 사용하지 않는 경우 B2B 게스트 사용자에 대한 MFA를 적용할 수 있나요?
예. [B2B 공동 작업 사용자에 대한 조건부 액세스](active-directory-b2b-mfa-instructions.md)에서 설명됩니다.

### <a name="within-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-any-plans-to-extend-this-to-azure-or-across-all-of-office-365"></a>SharePoint 내에서 외부 사용자에 대한 “허용” 또는 “거부” 목록을 정의할 수 있습니다. 이 목록을 Azure로 또는 모든 Office 365에서 확장할 계획이 있나요?
예. Azure AD B2B 공동 작업은 allowlist/denylist 기능을 지원합니다. 

### <a name="what-licenses-are-needed-for-azure-ad-b2b"></a>Azure AD B2B에는 어떤 라이선스가 필요한가요?
[Azure Active Directory B2B 공동 작업 라이선스 지침](active-directory-b2b-licensing.md)을 참조하세요.

### <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-admin-add-users.md)
* [정보 근로자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-iw-add-users.md)
* [B2B 공동 작업 초대 전자 메일의 요소](active-directory-b2b-invitation-email.md)
* [B2B 공동 작업 초대 상환](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 공동 작업 라이선스](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 공동 작업 문제 해결](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 공동 작업 API 및 사용자 지정](active-directory-b2b-api.md)
* [B2B 공동 작업 사용자에 대한 다단계 인증](active-directory-b2b-mfa-instructions.md)
* [초대 없이 B2B 공동 작업 사용자 추가](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

