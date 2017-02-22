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
ms.date: 02/10/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0eef684115f9c21ea61502a10576f74cac0ace8e
ms.openlocfilehash: 2054cb65b5192e08fc64e56f59a456fc911f0829


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Azure Active Directory B2B 공동 작업 FAQ(자주 묻는 질문)

자주 묻는 질문은 새로운 관심사를 반영하도록 주기적으로 업데이트됩니다.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>Azure 클래식 포털에서 이 기능을 사용할 수 있습니까?
이 Azure AD B2B 공동 작업(공개 미리 보기) 새로 고침의 새 기능은 [Azure Portal](https://portal.azure.com) 및 새 액세스 패널을 통해서만 사용할 수 있습니다. 사용해 보세요.

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B 공동 작업 사용자가 SharePoint Online 및 OneDrive에 액세스할 수 있습니까?
B2B 공동 작업 게스트 사용자가 디렉터리에 있습니다. 이 게스트 사용자는 SharePoint Online 및 OneDrive 사이트에 권한을 부여할 수 있는 그룹에 추가하거나 SharePoint Online 사용자 선택에서 직접 선택할 수도 있습니다. 게스트 사용자이므로 SharePoint Online 사이트에서 외부 공유를 사용하도록 설정해야 합니다.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>CSV 업로드 메커니즘이 계속 지원됩니까?
예. 포함된 PowerShell 샘플을 참조하세요.

### <a name="how-can-i-customize-my-invitation-emails"></a>초대 전자 메일을 어떻게 사용자 지정할 수 있습니까?
B2B 초대 API를 사용하여 거의 모든 초대자 프로세스를 사용자 지정할 수 있습니다.

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>초대된 외부 사용자가 자신을 초대한 조직을 떠날 수 있습니까?
현재 이 공개 미리 보기 새로 고침에서는 사용할 수 없습니다.

### <a name="can-i-use-my-microsoft-account-johncontosomicrosoftacctcom-to-sign-in-to-resources"></a>내 Microsoft 계정(John@contosomicrosoftacct.com)을 사용하여 리소스에 로그인할 수 있습니까?
이 공개 미리 보기 새로 고침에서는 Microsoft 계정을 사용할 수 없습니다. 표준이 아닌 Microsoft 계정 접미사(예: @contoso.com),과 같은 회사 메일의 경우)를 사용하는 경우 Azure Active Directory 테넌트를 만들어 사용하면 됩니다.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>이제 게스트 사용자가 MFA(Multi-Factor Authentication)를 사용할 수 있으므로 MFA 메서드도 다시 설정할 수 있습니까?
예, 일반 사용자가 설정할 수 있는 방법과 동일합니다.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>어떤 조직이 MFA 라이선스를 담당합니까?
초대 조직은 MFA에 개입하여 MFA를 수행하는 조직입니다. 따라서 초대 조직은 MFA를 수행하는 B2B 사용자에게 충분한 라이선스가 있는지 확인할 책임이 있습니다.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>이미 파트너 조직에 MFA가 설정되어 있다면 어떻게 됩니까? MFA를 신뢰하지만 사용하지 않을 수 있습니까?
이 공개 미리 보기 새로 고침에는 포함되지 않았지만 이후 릴리스에서 이를 지원할 예정입니다. 해당 릴리스가 발표되면 자신(초대 조직)의 MFA에서 제외할 특정 파트너를 선택할 수 있을 것입니다.

### <a name="how-can-i-achieve-delayed-invitations"></a>지연된 초대는 어떻게 얻을 수 있습니까?
일부 조직에서는 B2B 공동 작업 사용자를 추가하고 프로비전이 필요한 응용 프로그램에 프로비전한 다음 초대를 보내려고 합니다. 그렇다면 B2B 공동 작업 초대 API를 사용하여 온보딩 워크플로를 사용자 지정할 수 있습니다.

### <a name="can-guest-users-and-contacts-co-exist"></a>게스트 사용자와 연락처가 공존할 수 있습니까?
조직에서 외부 공동 작업자를 나타내는 연락처를 추가하여 [전체 주소 목록]에 표시하고 전자 메일 작성 중에 전자 메일 주소 제안으로 표시할 수 있습니다. 디렉터리에 B2B 공동 작업 사용자와 동일한 공동 작업자를 추가하면 어떻게 됩니까? 이후 릴리스에서는 B2B 공동 작업 사용자와 연락처 개체가 회사 디렉터리에 공존할 수 있습니다. 향후의 발표에 대해 계속 주목해 주세요!

### <a name="can-i-make-my-guest-users-limited-admins"></a>내 게스트 사용자를 제한된 관리자로 만들 수 있습니까?
그렇습니다. 조직에 필요한 경우 [역할에 게스트 사용자 추가](active-directory-users-assign-role-azure-portal.md)에서 설명하는 방법을 확인합니다.

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B 공동 작업 지원을 통해 B2B 사용자가 Azure Portal에 액세스할 수 있습니까?
B2B 공동 작업 사용자는 제한된 관리자 또는 전역 관리자 역할이 할당되지 않으면 Azure Portal에 액세스할 필요가 없습니다. 이 경우 포털에 액세스할 수 있습니다. 이 역할에 속하지 않은 게스트 사용자가 포털에 액세스하면 이전 섹션에서 설명한 대로 게스트 사용자 역할에 디렉터리에 대한 특정 권한이 있으므로 사용자는 환경의 특정 부분에 액세스할 수 있습니다.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>게스트 사용자를 위해 Azure Portal에 대한 액세스를 차단할 수 있습니까?
예! 그러나 이 정책을 구성할 때는 실수로 구성원과 관리자에 대한 액세스를 차단하지 않도록 주의하세요.
게스트 사용자가 다음 세 단계에 따라 Windows Azure Service Management API의 조건부 액세스 정책을 통해 [Azure Portal](https://portal.azure.com)에 대한 액세스를 차단할 수 있습니다.
1. **모든 사용자** 그룹을 ![](media/active-directory-b2b-faq/modify-all-users-group.png) 구성원만 포함하도록 수정합니다.
2. ![](media/active-directory-b2b-faq/group-with-guest-users.png) 게스트 사용자를 포함하는 동적 그룹을 만듭니다.
3. 다음 비디오에서 보여 주듯이 조건부 액세스 정책을 설정하여 게스트 사용자가 포털에 액세스하지 못하도록 차단합니다.

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user]

  이 비디오가 포함된 것으로 표시되지 않으면 [여기](https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user)서 연결할 수 있습니다.

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Azure AD B2B 공동 작업이 MFA 및 소비자 전자 메일 계정에 대한 지원을 시작할 일정은 어떻게 됩니까?
이 공개 미리 보기 새로 고침에서는 현재 MFA 및 고객 전자 메일 계정이 모두 지원됩니다.

### <a name="what-is-the-ga-timeline-for-azure-ad-b2b"></a>Azure AD B2B의 GA 일정은 어떻게 됩니까?
이를 수행할 때 현재 기능 집합이 고객으로부터 받는 피드백에 따라 다릅니다.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Azure AD B2B 공동 작업 사용자를 위한 암호 다시 설정을 지원할 계획이 있습니까?
예, B2B 공동 작업(게스트) 사용자를 위해 암호 설정 및 암호 다시 설정을 모두 지원합니다.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>바이럴 테넌트의 사용자도 사용할 수 있습니까?
현재는 아닙니다.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM에서 Azure AD B2B 공동 작업에 대한 온라인 지원을 제공합니까?
Azure AD B2B 공동 작업이 일반적으로 사용할 수 있게 되면 CRM에서 지원을 제공할 예정입니다.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>새로 만든 B2B 공동 작업 사용자의 초기 암호 수명은 어떻게 됩니까?
Azure AD에는 모든 Azure AD 클라우드 사용자 계정에 동등하게 적용되는 고정된 문자 집합, 암호 강도 및 계정 잠금 요구 사항이 있습니다. 클라우드 사용자 계정은 Microsoft 계정, Facebook, ADFS 또는 다른 클라우드 테넌트(B2B 공동 작업의 경우)와 같은 다른 ID 공급자와 페더레이션 되지 않는 계정입니다. 페더레이션 계정의 경우 암호 정책은 온-프레미스 테넌트의 정책과 사용자의 Microsoft 계정 설정에 따라 다릅니다.

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



<!--HONumber=Feb17_HO2-->


