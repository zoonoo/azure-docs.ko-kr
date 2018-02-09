---
title: "새로운 기능 Azure Active Directory 릴리스 정보 | Microsoft Docs"
description: "Azure AD(Azure Active directory)의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능 및 예정된 변경 내용 등을 알아봅니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 34826332db2e63d442d47ada74fcbad4cad600ae
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능




> [![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [피드](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us)를 구독하여 Azure AD(Azure Active Directory)의 새로운 기능에 대한 최신 정보를 얻으세요.



Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

-   최신 릴리스
-   알려진 문제
-   버그 수정
-   사용되지 않는 기능
-   변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요.


## <a name="january-2018"></a>2018년 1월
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 앱 갤러리에 있는 신규 페더레이션 앱 

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 

2018년 1월에, 페더레이션 지원을 포함하는 새 앱이 앱 갤러리에 추가되었습니다.

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory](https://go.microsoft.com/fwlink/?linkid=864699) 및 [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

사용 가능한 모든 자습서에 대한 개요는 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요.
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>추가 위험이 있는 로그인이 감지됨

**유형:** 새로운 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호
 

감지된 위험 이벤트에 대해 얻은 정보는 Azure AD 구독에 연결됩니다. Azure AD Premium P2 버전에서 모든 기본 감지에 대한 가장 자세한 정보를 가져옵니다.

Azure AD Premium P1 버전에서 라이선스가 적용되지 않는 검색 항목이 ‘추가 위험이 있는 로그인이 감지됨’이라는 위험 이벤트로 표시됩니다.

자세한 내용은 [Azure Active Directory 위험 이벤트](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)를 참조하세요.
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>최종 사용자의 액세스 패널에서 Office 365 응용 프로그램 숨기기

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO
 

새로운 사용자 설정을 통해 Office 365 응용 프로그램을 사용자의 액세스 패널에 표시하는 방법을 보다 효과적으로 관리할 수 있습니다. Office 포털에 Office 응용 프로그램만 표시하려는 경우, 이 옵션은 사용자의 액세스 패널에 있는 앱을 줄이는 데 유용합니다. 이 설정은 **사용자 설정** 아래에 있으며 **사용자가 Office 365 포털에서 Office 365 앱만 볼 수 있음**이라는 레이블이 지정됩니다.
 

자세한 내용은 [Azure Active Directory의 사용자 환경에서 응용 프로그램 숨기기](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)를 참조하세요.

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>앱의 URL을 통해 암호 SSO가 가능한 앱에 간편하게 로그인 

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO
 

내 앱 SSO(Single-Sign On) 기능을 브라우저의 바로 가기로 제공하는 간편한 도구를 통해 내 앱 브라우저 확장을 사용할 수 있습니다. 사용자의 앱을 설치하면 앱에 빠른 액세스를 제공하는 와플 아이콘이 브라우저에 표시됩니다. 사용자는 이제 다음과 같은 이점을 활용할 수 있습니다.

- 앱의 로그인 페이지에서 암호-SSO 기반 앱에 직접 로그인할 수 있는 기능
- 빠른 검색 기능을 사용하여 원하는 앱 실행
- 확장 프로그램의 최근 사용한 앱에 대한 바로 가기
- 확장 프로그램은 Edge, Chrome 및 Firefox에서 사용할 수 있습니다.
 
자세한 내용은 [내 앱 보안 로그인 확장](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension)을 참조하세요.

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure 클래식 포털에서 Azure AD 관리 환경 사용 중지

**유형:** 사용되지 않음   
**서비스 범주:** Azure AD  
**제품 기능:** 디렉터리
 

2018년 1월 8일부터 Azure 클래식 포털의 Azure AD 관리 환경 사용이 중지됩니다. Azure 클래식 포털의 사용 중지와 함께 적용됩니다. 앞으로 Azure AD의 포털 기반 관리를 위해서는 [Azure AD 관리 센터](https://aad.portal.azure.com)를 사용해야 합니다.
 
---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure 클래식 포털에서 Azure AD 관리 환경 사용 중지

**유형:** 사용되지 않음  
**서비스 범주:** Azure AD  
**제품 기능:** 디렉터리
 

2018년 1월 8일부터 PhoneFactor 웹 포털의 사용이 중지됩니다. 이 포털은 MFA 서버 관리에 사용되었으며, 해당 기능은 Azure Portal(portal.azure.com)로 이동되었습니다. 

MFA 구성의 위치는 **Azure Active Directory \> MFA 서버**입니다.
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Azure AD 보고서 사용 중지


**유형:** 사용되지 않음  
**서비스 범주:** 보고  
**제품 기능:** ID 수명 주기 관리  


새로운 Azure Active Directory 관리 콘솔이 일반 공급되고 활동 및 보안 보고서를 위해 새로운 API가 제공됨에 따라서, 2017년 12월 31일부로 "/reports" 엔드포인트에 있는 보고서 API 사용이 중지되었습니다.


**사용할 수 있는 기능은 무엇인가요?**

새 관리 콘솔로 전환하면서 Azure AD 활동 로그를 검색할 수 있는 2개의 새로운 API를 만들었습니다. 새로운 API 집합은 보다 풍부한 감사 및 로그인 활동을 제공할 뿐만 아니라 보다 다양한 필터링 및 정렬 기능을 제공합니다. 이전에 보안 보고서를 통해 사용 가능했던 데이터를 이제 Microsoft Graph의 ID 보호 위험 이벤트 API를 통해 액세스할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 보고 API를 시작](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Azure Active Directory ID 보호 및 Microsoft Graph 시작](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>2017년 12월
 

### <a name="terms-of-use-in-the-access-panel"></a>액세스 패널의 사용 약관

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스/준수
 
이제 액세스 패널로 이동하면 이전에 수락한 사용 약관을 볼 수 있습니다.

다음 단계를 수행하세요.

1. [MyApps 포털](https://myapps.microsoft.com)로 이동하고 로그인합니다.

2. 오른쪽 위 모서리에서 자신의 이름을 선택한 다음 목록에서 **프로필**을 선택합니다. 

3. **프로필**에서 **사용 약관 검토**를 클릭합니다. 

4. 이제 내가 동의한 사용 약관을 검토할 수 있습니다. 

자세한 내용은 [Azure AD 사용 약관 기능(미리 보기)](https://docs.microsoft.com/azure/active-directory/active-directory-tou)을 참조하세요.
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>새 Azure AD 로그인 환경

**유형:** 새로운 기능  
**서비스 범주:** Azure AD  
**제품 기능:** 사용자 인증
 
Azure AD와 Microsoft 계정 ID 시스템의 UI 모양과 느낌이 일관된 스타일로 새롭게 디자인되었습니다. 또한 Azure AD 로그인 페이지에서는 먼저 사용자 이름을 수집하고 두 번째 화면에서 자격 증명을 수집합니다.

자세한 내용은 [The new Azure AD Signin Experience is now in Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)(새 Azure AD 로그인 환경을 공개 미리 보기로 제공)를 참조하세요.
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>로그인 프롬프트 감소: Azure AD 로그인을 위한 새로운 "로그인 유지" 환경

**유형:** 새로운 기능  
**서비스 범주:** Azure AD  
**제품 기능:** 사용자 인증
 
Azure AD 로그인 페이지의 **로그인 상태 유지** 확인란을 사용자가 인증된 후에 표시되는 새 프롬프트로 바꾸었습니다. 

이 프롬프트에 사용자가 **예**로 응답하면 영구 새로 고침 토큰이 제공됩니다. 이 동작은 사용자가 이전 환경에서 **로그인 유지** 확인란을 선택할 때와 동일합니다. 페더레이션 된 테넌트에게는 페더레이션된 서비스로 인증이 성공한 후에 이 프롬프트가 표시됩니다.

자세한 내용은 [로그인 프롬프트 감소: Azure AD 로그인을 위한 새로운 "로그인 유지" 환경을 미리 보기로 제공](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)을 참조하세요. 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>동의하기 전에 사용 약관을 펼치도록 하는 구성 추가

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스
 
사용 약관을 수락하기 전에 사용 약관을 펼치도록 사용자에게 요구하는 옵션이 관리자에게 필요합니다.

사용자에게 사용 약관을 펼치도록 요구하려면 **On** 또는 **Off** 중 하나를 선택합니다. **On** 설정은 사용 약관을 수락하기 전에 확인하도록 사용자에게 요구합니다.

자세한 내용은 [Azure AD 사용 약관 기능(미리 보기)](https://docs.microsoft.com/azure/active-directory/active-directory-tou)을 참조하세요.
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>적격 역할 할당에 대한 범위 지정 활성화

**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
범위가 지정된 활성화를 사용하면 원래 할당 기본값보다 낮은 자율성을 적용하여 적절한 Azure 리소스 역할 할당을 활성화할 수 있습니다. 예를 들어, 테넌트의 구독 소유자로 할당되는 경우가 있습니다. 범위가 지정된 활성화를 사용하면 구독 내에 포함된 리소스 최대 5개에 대해 소유자 역할을 활성화할 수 있습니다(예: 리소스 그룹, 가상 머신). 활성화의 범위를 지정하면 중요한 Azure 리소스가 원치 않게 변경될 가능성을 줄일 수 있습니다.

자세한 내용은 [Azure AD Privileged Identity Management란?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)을 참조하세요.
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD 앱 갤러리의 새로운 페더레이션된 앱

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2017년 12월에, 페더레이션 지원을 포함하는 다음과 같은 새 앱이 앱 갤러리에 추가되었습니다.

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

사용 가능한 모든 자습서에 대한 개요는 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요.

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD 디렉터리 역할에 대한 승인 워크플로

**유형:** 변경된 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
Azure AD 디렉터리 역할의 승인 워크플로가 일반 공급됩니다.

승인 워크플로를 통해, 권한 있는 역할 관리자는 자격을 갖춘 역할 멤버가 권한 있는 역할을 사용하려면 그 전에 역할 활성화를 요청하도록 요청할 수 있습니다. 여러 사용자와 그룹이 승인 책임을 위임 받을 수 있습니다. 승인이 완료되고 역할이 활성화되면 자격을 갖춘 역할 멤버에게 알림이 전송됩니다.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>통과 인증: 비즈니스용 Skype 지원

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증


통과 인증은 사용자가 온라인 및 하이브리드 토폴로지를 비롯한 최신 인증을 지원하는 비즈니스용 Skype 클라이언트 응용 프로그램에 로그인하도록 지원합니다. 

자세한 내용은 [Skype for Business topologies supported with Modern Authentication](https://technet.microsoft.com/library/mt803262.aspx)(최신 인증에서 비즈니스용 Skype 토폴로지 지원됨)을 참조하세요.
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Azure RBAC에 대한 Azure AD Privileged Identity Management 업데이트(미리 보기)

**유형:** 변경된 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
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

응용 프로그램 기반 조건부 액세스에 대해 다음과 같은 조건을 구성할 수 있습니다.

**클라이언트 앱:** 브라우저

**변경되면 무엇이 달라지나요?**

현재는 이 조건을 사용하면 액세스가 차단됩니다. 미리 보기를 사용할 수 있게 되면 모든 액세스에는 관리되는 브라우저 응용 프로그램을 사용해야 합니다. 

앞으로 공개될 블로그와 릴리스 정보에서도 이 기능과 자세한 정보를 찾아볼 수 있습니다. 

자세한 내용은 [Azure AD의 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)를 참조하세요.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스의 새로운 승인된 클라이언트 앱

 
**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호




[승인된 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)에 다음과 같은 앱이 추가될 계획입니다.

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


자세한 내용은 다음을 참조하세요.

- [승인된 클라이언트 앱 요구 사항](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>사용 약관을 여러 언어로 제공



**유형:** 새로운 기능    
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스/준수





이제 관리자가 여러 PDF 문서가 포함된 새로운 사용 약관을 작성할 수 있습니다. PDF 문서에는 문서에서 사용된 언어를 태그로 지정할 수 있습니다. 사용자 기본 설정에 따라 사용자에게 일치하는 언어로 된 PDF가 표시됩니다. 일치하는 언어가 없으면 기본 언어가 표시됩니다.


---
 

### <a name="real-time-password-writeback-client-status"></a>실시간 비밀번호 쓰기 저장 클라이언트 상태



**유형:** 새로운 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 사용자 인증


 

이제 온-프레미스 비밀번호 쓰기 저장 클라이언트의 상태를 검토할 수 있습니다. 이 옵션은 [암호 재설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) 페이지의 **온-프레미스 통합** 섹션에서 확인할 수 있습니다. 

온-프레미스 쓰기 저장 클라이언트와의 연결에 문제가 있는 경우 다음과 같은 정보를 제공하는 오류 메시지가 표시됩니다.

- 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없는 이유
- 문제 해결에 도움이 되는 설명서 링크 


자세한 내용은 [온-프레미스 통합](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)을 참조하세요.

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스 



 
**유형:** 새로운 기능  
**서비스 범주:** Azure AD  
**제품 기능:** ID 보안 및 보호





이제 [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)를 사용하여 Intune 앱 보호 정책을 지원하는 [승인된 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)으로 Office 365 및 기타 Azure AD에 연결된 클라우드 앱의 액세스를 제한할 수 있습니다. 승인된 클라이언트 응용 프로그램에서 기업 데이터를 구성하고 보호하는 데 Intune 앱 보호 정책이 사용됩니다.

[앱 기반](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) 조건부 액세스 정책과 [장치 기반](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) 조건부 액세스 정책이 결합됨으로써 개인 장치와 기업 장치의 데이터를 유연하게 보호할 수 있게 됩니다.

다음은 앱 기반 조건부 액세스에 사용 가능한 조건과 컨트롤입니다.

**지원되는 플랫폼 조건**

- iOS
- Android

**클라이언트 앱 조건**

- 모바일 앱 및 데스크톱 클라이언트

**액세스 제어**

- 승인된 클라이언트 앱 필요


자세한 내용은 [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)를 참조하세요.

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure Portal에서 Azure AD 장치 관리



**유형:** 새로운 기능  
**서비스 범주:** 장치 등록 및 관리  
**제품 기능:** ID 보안 및 보호

 



이제 Azure AD에 연결된 장치와 장치 관련 활동을 모두 한 곳에서 확인할 수 있습니다. Microsoft는 Azure Portal에서 모든 장치 ID와 설정을 한 번에 관리할 수 있는 새로운 관리 환경을 선보입니다. 이 릴리스에서는 다음과 같은 기능을 수행할 수 있습니다.

- Azure AD의 조건부 액세스에 사용할 수 있는 모든 장치 보기
- 하이브리드 Azure AD 가입 장치를 비롯한 속성 보기
- Azure AD 가입 장치의 BitLocker 키 찾기, Intune을 사용하여 장치 관리하기
- Azure AD 장치 관련 설정 관리하기

자세한 내용은 [Azure Portal을 사용하여 장치 관리](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal)를 참조하세요.



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Azure AD 조건부 액세스의 장치 플랫폼으로서 macOS 지원 



**유형:** 새로운 기능    
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호 
 

이제 Azure AD 조건부 액세스 정책에서 장치 플랫폼 조건으로서 macOS를 추가 또는 제외할 수 있습니다. 지원되는 장치 플랫폼에 macOS가 추가되어 다음과 같은 기능이 지원됩니다.

- **Intune을 사용하여 macOS 장치를 등록하고 관리합니다.** iOS, Android와 같은 여타 플랫폼과 마찬가지로 macOS의 경우에도 통합 등록을 수행하는 기업 포털 응용 프로그램이 제공됩니다. 새로운 macOS용 기업 포털 앱을 이용하면 Intune을 사용하여 장치를 등록하고 등록한 장치를 Azure AD에 등록할 수 있습니다.
- **macOS 장치가 Intune에 정의된 조직의 준수 정책을 준수하는지 확인합니다.** 이제 Azure Portal의 Intune에서 macOS 장치에 대한 준수 정책을 설정할 수 있습니다. 
- **Azure AD에서 응용 프로그램에 대한 액세스를 호환 가능한 macOS 장치로 제한합니다.** 조건부 액세스 정책에는 macOS가 별도의 장치 플랫폼 옵션으로 있습니다. 이제 Azure에 설정된 대상 응용 프로그램에 대해 macOS 전용 조건부 액세스 정책을 작성할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Intune을 사용하여 macOS를 위한 장치 준수 정책 만들기](https://aka.ms/macoscompliancepolicy)
- [Azure AD의 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication용 네트워크 정책 서버 확장 


**유형:** 새로운 기능    
**서비스 범주:** Multi-Factor Authentication  
**제품 기능:** 사용자 인증




Azure Multi-Factor Authentication용 네트워크 정책 서버 확장은 기존 서버를 사용하여 인증 인프라에 클라우드 기반 다단계 인증 기능을 추가합니다. 네트워크 정책 서버 확장을 사용하면, 전화 통화, 문자 메시지 또는 휴대폰 앱 확인을 기존 인증 흐름에 추가할 수 있습니다. 새 서버를 설치, 구성 및 유지 관리할 필요가 없습니다. 

이 확장은 Azure Multi-Factor Authentication 서버를 배포하지 않고 가상 사설망 연결을 보호하려는 조직을 위해 작성되었습니다. 네트워크 정책 서버 확장은 RADIUS 및 클라우드 기반 Azure Multi-Factor Authentication 간에 어댑터로 작동하여 페더레이션 사용자 또는 동기화된 사용자를 위한 또 다른 인증을 제공합니다.


자세한 내용은 [기존 네트워크 정책 서버 인프라를 Azure Multi-Factor Authentication과 통합](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension)을 참조하세요.

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>삭제된 사용자 복원 또는 영구 제거


**유형:** 새로운 기능    
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

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스의 새로운 승인된 클라이언트 앱

 
**유형:** 변경된 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호


[승인된 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)에 다음과 같은 앱이 추가되어 있습니다.

- Microsoft Planner
- Azure Information Protection 


자세한 내용은 다음을 참조하세요.

- [승인된 클라이언트 앱 요구 사항](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>조건부 액세스 정책에서 여러 컨트롤 사이에 ‘OR’ 사용 


**유형:** 변경된 기능    
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

 
이제 조건부 액세스 제어에 "OR"(선택한 컨트롤 중 하나 필요) 연산자를 사용할 수 있습니다. 이 기능을 사용하여 액세스 제어 사이에 "OR"이 있는 정책을 만들 수 있습니다. 사용자가 다단계 인증을 사용하거나 규정을 준수하는 장치를 사용하도록 요구하려는 경우 "OR" 연산자를 사용하여 정책을 만들 수 있습니다.

자세한 내용은 [Azure AD 조건부 액세스의 컨트롤](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls)을 참조하세요.

 
---

### <a name="aggregation-of-real-time-risk-events"></a>실시간 위험 이벤트의 집계


**유형:** 변경된 기능    
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호


Azure AD ID 보호에서는 특정 날짜에 동일한 IP 주소에서 발생한 모든 실시간 위험 이벤트가 위험 이벤트 유형별로 집계됩니다. 이 변경으로 인해 사용자 보안에 영향을 주지 않지만, 표시되는 위험 이벤트의 양이 제한됩니다.

사용자가 로그인할 때마다 기반 실시간 감지가 작동합니다. 다단계 인증이나 액세스 차단이 설정된 로그인 위험 보안 정책이 있어도 위험한 로그인이 있을 때마다 계속 트리거됩니다.

 
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


**유형:** 고정   
**서비스 범주:** 내 앱  
**제품 기능:** Single Sign-On  



Azure AD는 HTML 사용자 이름 및 암호 필드를 렌더링하는 응용 프로그램의 자동 로그인 필드 검색을 지원합니다. 이 단계는 [응용 프로그램에 대한 로그인 필드를 자동으로 캡처하는 방법](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application)에 설명되어 있습니다. 이 기능은 [Azure Portal](http://aad.portal.azure.com)의 **엔터프라이즈 응용 프로그램** 페이지에 *비갤러리* 응용 프로그램을 추가하여 찾을 수 있습니다. 또한 이 새 응용 프로그램의 **Single Sign-On** 모드를 **암호 기반 Single Sign-on**으로 구성하고 웹 URL을 입력한 다음 페이지를 저장할 수 있습니다.
 
서비스 문제로 인해 이 기능은 일시적으로 사용하지 않도록 설정되어 있었습니다. 이제 문제가 해결되어 자동 로그인 필드 검색을 다시 사용할 수 있습니다.

---

### <a name="new-multi-factor-authentication-features"></a>새 다단계 인증 기능


**유형:** 새로운 기능  
**서비스 범주:** Multi-Factor Authentication  
**제품 기능:** ID 보안 및 보호  



MFA(Multi-Factor Authentication)는 조직을 보호하기 위한 필수적인 요소입니다. 자격 증명의 적응성을 향상시키고 MFA 환경을 더욱 원활하게 만들기 위해 다음과 같은 기능이 추가되어 있습니다. 

- 다단계 인증 결과는 Azure AD 로그인 보고서에 직접 통합되며, 여기에는 MFA 결과에 대한 프로그래밍 방식의 액세스가 포함됩니다.
- MFA 구성이 Azure Portal의 Azure AD 구성 환경에 보다 깊숙이 통합되었습니다.

이 공개 미리 보기에서는 핵심 Azure AD 구성 환경에 통합된 MFA 관리와 보고 기능을 경험할 수 있습니다. 이제 Azure AD 환경에서 MFA 관리 포털 기능을 관리할 수 있습니다.

자세한 내용은 [Azure Portal의 MFA보고에 대한 참조](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa)를 참조하세요. 


---

### <a name="terms-of-use"></a>사용 약관



**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스  



Azure AD 이용 약관을 사용하여 법률 요구 사항 또는 규정 준수 요구 사항에 대한 관련 면책 조항과 같은 정보를 사용자에게 제공할 수 있습니다.

다음 시나리오에서 Azure AD 사용 약관을 사용할 수 있습니다.

- 조직의 모든 사용자에 대한 일반 사용 약관
- 사용자의 특성에 기반한 특정 사용 약관(예: 의사 및 간호사 또는 국내 및 해외 직원, 동적 그룹에 의한 수행)
- Salesforce와 같은 영향력이 높은 비즈니스 앱에 액세스하기 위한 특정 이용 약관

자세한 내용은 [Azure AD 사용 약관](https://docs.microsoft.com/azure/active-directory/active-directory-tou)을 참조하세요.


---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management의 기능 개선


**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management  


이제 Azure AD Privileged Identity Management를 사용하여 조직에서 Azure Resources(미리 보기)에 대한 액세스를 관리, 제어 및 모니터링할 수 있습니다.

- 구독
- 리소스 그룹
- 가상 머신 

Azure Portal에서 Azure RBAC 기능을 사용하는 모든 리소스는 Azure AD Privileged Identity Management가 제공하는 보안 및 수명 주기 관리 기능을 모두 사용할 수 있습니다.

자세한 내용은 [Azure 리소스용 Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)를 참조하세요.


---

### <a name="access-reviews"></a>액세스 검토


**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 거버넌스  



조직은 액세스 검토(미리 보기)를 통해 그룹 멤버 자격을 효율적으로 관리하고 엔터프라이즈 응용 프로그램에 액세스할 수 있습니다. 

- 응용 프로그램 및 그룹 멤버 자격의 액세스 권한에 대한 액세스 검토를 사용하여 게스트 사용자 액세스를 다시 인증할 수 있습니다. 검토자는 액세스 검토로 제공되는 통찰력을 기반으로 게스트에게 계속 액세스를 허용할지를 효율적으로 결정할 수 있습니다.
- 액세스 검토를 사용하여 응용 프로그램 및 그룹 멤버 자격에 대한 직원 액세스를 다시 인증할 수 있습니다.

액세스 검토 컨트롤을 조직과 관련된 프로그램으로 수집하여 규정 준수 또는 위험 감지 응용 프로그램에 대한 검토를 추적할 수 있습니다.

자세한 내용은 [Azure AD 액세스 검토](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)를 참조하세요.


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>내 앱 및 Office 365 앱 시작 관리자에서 타사 응용 프로그램 숨기기



**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** Single Sign-On  



이제 새 **앱 숨기기** 속성을 통해 사용자 포털에 표시되는 앱을 더 잘 관리할 수 있습니다. 백 엔드 서비스를 위해 앱 타일이 표시되거나 타일이 중복되고 사용자의 앱 시작 관리자가 복잡하게 표시되는 경우 앱을 숨기면 도움이 됩니다. 토글은 타사 앱 섹션의 **속성** 섹션에 있으며 **사용자가 볼 수 있습니까?**라는 레이블이 지정되어 있습니다. PowerShell을 통해 프로그래밍 방식으로 앱을 숨길 수도 있습니다. 

자세한 내용은 [Hide a third-party application from user's experience in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)(Azure AD의 사용자 환경에서 타사 응용 프로그램 숨기기)를 참조하세요. 


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
