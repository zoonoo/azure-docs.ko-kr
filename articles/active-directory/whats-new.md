---
title: "새로운 기능 Azure Active Directory 릴리스 정보 | Microsoft Docs"
description: "최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능 및 예정된 변경 내용을 비롯한 Azure AD(Azure Active directory)의 새로운 기능에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f1538e1c26cfe658c7f42ccdd57d8bf5aca0b1fb
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능




> 즐겨찾는 RSS 피드 판독기에서 이 [피드](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us)를 구독하여 Azure Active Directory의 새로운 기능을 최신 상태로 유지합니다.



Azure Active Directory는 지속적으로 개선되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 항목에서는 다음에 대한 정보를 제공합니다.

-   최신 릴리스 
-   알려진 문제 
-   버그 수정 
-   사용되지 않는 기능 
-   변경 계획 

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문하세요.

## <a name="november-2017"></a>2017년 11월
 
### <a name="retiring-acs"></a>사용 중지 ACS



**유형:** 변경 계획  
**서비스 범주:** ACS  
**제품 기능:** Access Control Service 


Microsoft Azure Active Directory Access Control(Access Control Service 또는 ACS)은 2018년 말에 사용 중지됩니다.  곧 자세한 일정과 개괄적인 마이그레이션 지침을 비롯한 자세한 정보가 공지될 예정입니다. 공지되기 전까지는 이 페이지에 ACS와 관련된 문의 사항을 남겨주시면 담당자가 도움을 드리겠습니다.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Intune Managed Browser에 대한 브라우저 액세스 제한 


**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호




이 기능을 사용하면 Intune Managed Browser를 승인된 앱으로 사용하여 브라우저 액세스를 Office 365 및 기타 Azure AD에 연결된 클라우드 앱으로 제한할 수 있습니다. 

또한, 응용 프로그램 기반 조건부 액세스에 대해 다음과 같은 조건을 구성할 수 있습니다.

**클라이언트 앱:** 브라우저

**변경되면 무엇이 달라지나요?**

현재는 이 조건을 사용하면 액세스가 차단됩니다. 이 기능의 미리 보기가 공개되면 모든 액세스가 관리 브라우저 응용 프로그램을 사용해야 합니다. 

앞으로 공개될 블로그와 릴리스 정보에서도 이 기능에 대해 알아볼 수 있습니다. 

자세한 내용은 [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 참조하세요.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스의 새로운 승인된 클라이언트 앱

 
**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호




[승인된 클라이언트 앱](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)에 다음과 같은 앱이 추가될 계획입니다.

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


자세한 내용은 다음을 참조하세요.

- [승인된 클라이언트 앱 요구 사항](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory 앱 기반 조건부 액세스](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>사용 약관을 여러 언어로 제공



**유형:** 새로운 기능    
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스/준수





관리자들은 이제 둘 이상의 PDF 문서를 포함하여 새로운 약관(TOU)을 작성할 수 있습니다. PDF 문서에는 문서에서 사용된 언어를 태그로 지정할 수 있습니다. 해당하는 사용자에게는 사용자 기본 설정에 설정된 언어로 된 PDF가 표시됩니다. 일치하는 언어가 없으면 기본 언어가 표시됩니다.


---
 

### <a name="realtime-password-writeback-client-status"></a>실시간 비밀번호 쓰기 저장 클라이언트 상태



**유형:** 새로운 기능  
**서비스 범주:** SSPR  
**제품 기능:** 사용자 인증


 

이제 온-프레미스 비밀번호 쓰기 저장 클라이언트의 상태를 검토할 수 있습니다. 이 옵션은 **[암호 재설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)** 페이지의 **온-프레미스 통합** 섹션에서 확인할 수 있습니다. 

온-프레미스 쓰기 저장 클라이언트와의 연결에 문제가 있는 경우 다음과 같은 정보를 제공하는 오류 메시지가 표시됩니다.

- 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없는 이유 
- 문제 해결에 도움이 되는 설명서 링크 


자세한 내용은 [온-프레미스 통합](active-directory-passwords-how-it-works.md#on-premises-integration)을 참조하세요.

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스 



 
**유형:** 새로운 기능  
**서비스 범주:** Azure AD  
**제품 기능:** ID 보안 및 보호





이제 [Azure AD 앱 기반 조건부 액세스](active-directory-conditional-access-mam.md)를 사용하여 Intune 앱 보호 정책을 지원하는 [승인된 클라이언트 앱](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)으로 Office 365 및 기타 Azure AD에 연결된 클라우드 앱의 액세스를 제한할 수 있습니다. 승인된 클라이언트 응용 프로그램에서 기업 데이터를 구성하고 보호하는 데 Intune 앱 보호 정책이 사용됩니다.

[앱 기반](active-directory-conditional-access-mam.md) 조건부 액세스 정책과 [장치 기반](active-directory-conditional-access-policy-connected-applications.md) 조건부 액세스 정책이 결합됨으로써 개인 장치와 기업 장치의 데이터를 유연하게 보호할 수 있게 됩니다.

다음은 앱 기반 조건부 액세스에 사용 가능한 조건과 컨트롤입니다.

**지원되는 플랫폼 조건**

- iOS
- Android

**클라이언트 앱 조건**

- 모바일 앱 및 데스크톱 클라이언트

**액세스 제어**

- 승인된 클라이언트 앱 필요


자세한 내용은 [Azure Active Directory 앱 기반 조건부 액세스](active-directory-conditional-access-mam.md)를 참조하세요.

 
---

### <a name="managing-azure-ad-devices-in-the-azure-portal"></a>Azure Portal에서 Azure AD 장치 관리



**유형:** 새로운 기능  
**서비스 범주:** 장치 등록 및 관리  
**제품 기능:** ID 보안 및 보호

 



이제 Azure AD에 연결된 장치와 장치의 활동을 모두 한곳에서 확인할 수 있습니다. Microsoft는 Azure Portal에서 모든 장치 ID와 설정을 한 번에 관리할 수 있는 새로운 관리 환경을 선보입니다. 이 릴리스에서는 다음과 같은 기능을 수행할 수 있습니다.

- Azure AD의 조건부 액세스에 사용할 수 있는 모든 장치 보기

- 하이브리드 Azure AD 가입 장치를 비롯한 속성 보기

- Azure AD 가입 장치의 BitLocker 키 찾기, Intune을 사용하여 장치 관리하기

- Azure AD 장치 관련 설정 관리하기


자세한 내용은 [Azure Portal을 사용하여 장치 관리](device-management-azure-portal.md)를 참조하세요.



 
---

### <a name="support-for-macos-as-device-platform-for-azure-ad-conditional-access"></a>Azure AD 조건부 액세스의 장치 플랫폼으로서 macOS 지원 



**유형:** 새로운 기능    
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호 
 

이제 Azure AD 조건부 액세스 정책에서 장치 플랫폼 조건으로서 macOS를 추가 또는 제외할 수 있습니다. 지원되는 장치 플랫폼에 macOS가 추가되어 다음과 같은 기능이 지원됩니다.

- **Intune을 사용하여 macOS 장치 등록 및 관리** - iOS, Android와 같은 여타 플랫폼과 마찬가지로 macOS의 경우에도 통합 등록을 수행하는 기업 포털 응용 프로그램이 제공됩니다. 새로운 macOS용 기업 포털 앱을 이용하면 Intune을 사용하여 장치를 등록하고 등록한 장치를 Azure AD에 등록할 수 있습니다.
 
- **macOS 장치가 Intune에 정의된 조직의 준수 정책을 준수하도록 감독** - Azure Portal의 Intune에서 macOS 장치의 준수 정책을 설정할 수 있습니다. 
  
- **Azure AD에서 규정을 준수하는 macOS 장치만 응용 프로그램에 액세스할 수 있도록 제한** - 조건부 액세스 정책 작성 시 macOS를 별도의 장치 플랫폼 옵션으로 사용할 수 있습니다. 이 옵션을 사용하면 Azure에 설정된 대상 응용 프로그램에 대해 macOS에만 적용되는 조건부 액세스 정책을 작성할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Intune을 사용하여 macOS를 위한 장치 준수 정책 만들기](https://aka.ms/macoscompliancepolicy)
- [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="nps-extension-for-azure-mfa"></a>Azure MFA용 NPS 확장 


**유형:** 새로운 기능    
**서비스 범주:** MFA  
**제품 기능:** 사용자 인증




Azure MFA의 NPS(네트워크 정책 서버) 확장은 기존 서버를 사용하여 인증 인프라에 클라우드 기반 MFA 기능을 추가합니다. NPS 확장을 사용하면 새 서버를 설치, 구성 및 유지할 필요 없이 전화 통화, 문자 메시지 또는 휴대폰 앱 인증을 기존 인증 흐름에 추가할 수 있습니다. 

이 확장은 Azure MFA 서버를 배포하지 않고 VPN 연결을 보호하려는 조직을 위해 작성되었습니다. NPS 확장은 RADIUS 및 클라우드 기반 Azure MFA 간에 어댑터로 작동하여 페더레이션 사용자 또는 동기화된 사용자를 위한 또 다른 인증을 제공합니다.


자세한 내용은 [기존 NPS 인프라를 Azure Multi-Factor Authentication과 통합](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)을 참조하세요.

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>삭제된 사용자 복원 또는 영구 제거


**유형:** 새로운 기능    
**서비스 범주:** 사용자 관리  
**제품 기능:** 디렉터리 



이제 Azure AD 관리 센터에서 다음과 같은 기능을 수행할 수 있습니다.

- 삭제한 사용자 복원 
- 사용자 영구 삭제 


**기능 사용해 보기:**

1. Azure AD 관리 센터의 **관리** 섹션에서 [**모든 사용자**](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users)를 선택합니다. 

2. **표시** 목록에서 **최근 삭제된 사용자**를 선택합니다. 

4. 최근 삭제된 사용자를 하나 이상 선택한 다음 복원하거나 영구 삭제합니다.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스의 새로운 승인된 클라이언트 앱

 
**유형:** 변경된 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호


[승인된 클라이언트 앱](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)에 다음과 같은 앱이 추가되었습니다.

- Microsoft Planner

- Microsoft Azure Information Protection 


자세한 내용은 다음을 참조하세요.

- [승인된 클라이언트 앱 요구 사항](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory 앱 기반 조건부 액세스](active-directory-conditional-access-mam.md)


---

### <a name="ability-to-or-between-controls-in-a-conditional-access-policy"></a>조건부 액세스 정책에서 여러 컨트롤에 대해 ‘OR’ 연산자 사용 가능 


**유형:** 변경된 기능    
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

 
이제 여러 조건부 액세스 컨트롤에 대해 ‘OR’ 연산자를 사용(선택한 컨트롤 중 적어도 하나가 충족되어야 함)할 수 있습니다. 즉, 여러 액세스 제어에 대해 **OR**를 적용하여 정책을 만들 수 있습니다. 사용자가 다단계 인증을 사용하거나 규정을 준수하는 장치를 사용하도록 요구하려는 경우 **OR** 연산자를 사용하여 정책을 만들 수 있습니다.

자세한 내용은 [Azure Active Directory 조건부 액세스의 컨트롤](active-directory-conditional-access-controls.md)을 참조하세요.

 
---

### <a name="aggregation-of-realtime-risk-events"></a>실시간 위험 이벤트의 집계


**유형:** 변경된 기능    
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호


관리 환경을 개선하기 위해 Azure AD ID 보호에서는 하루에 같은 IP 주소에서 발원한 모든 실시간 위험 이벤트가 위험 이벤트 유형별로 집계됩니다. 이로 인해 사용자 보안에 영향을 주지 않으면서 위험 이벤트의 개수가 줄어들게 됩니다.

사용자가 로그인할 때마다 기반 실시간 감지가 작동합니다. 로그인 위험 보안 정책에서 MFA 또는 액세스 차단을 설정해 두었더라도 위험이 있는 로그인이 발생할 때마다 실시간 감지가 트리거됩니다.

 
---
 




## <a name="october-2017"></a>2017년 10월


### <a name="deprecating-azure-ad-reports"></a>Azure AD 보고서 사용 중지


**유형:** 변경 계획  
**서비스 범주:** 보고  
**제품 기능:** ID 수명 주기 관리  



Azure Portal은 다음을 제공합니다.

- 새로운 Azure Active Directory 관리 콘솔 
- 활동 및 보안 보고서를 위한 새로운 API
 
이러한 새로운 기능으로 인해 **/reports** 끝점의 보고서 API는 2017년 12월 10일에 사용 중지됩니다. 

---

### <a name="automatic-sign-in-field-detection"></a>자동 로그인 필드 감지


**유형:** 고정   
**서비스 범주:** 내 앱  
**제품 기능:** SSO  



Azure Active Directory는 HTML 사용자 이름 및 암호 필드를 렌더링하는 응용 프로그램의 자동 로그인 필드 검색을 지원합니다.  이 단계는 [응용 프로그램에 대한 로그인 필드를 자동으로 캡처하는 방법](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)에 설명되어 있습니다. 이 기능은 [Azure Portal](http://aad.portal.azure.com)의 **엔터프라이즈 응용 프로그램** 페이지에 *비갤러리* 응용 프로그램을 추가하여 찾을 수 있습니다. 또한 이 새 응용 프로그램의 **Single Sign-On** 모드를 **암호 기반 Single Sign-on**으로 구성하고 웹 URL을 입력한 다음 페이지를 저장할 수 있습니다.
 
서비스 문제로 인해 이 기능은 일정 기간 동안 일시적으로 사용 중지되었습니다. 문제가 해결되어 자동 로그인 필드 검색을 다시 사용할 수 있습니다.

---

### <a name="new-mfa-features"></a>새로운 MFA 기능


**유형:** 새로운 기능  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호  



MFA(Multi-Factor Authentication)는 조직을 보호하기 위한 필수적인 요소입니다. 자격 증명의 적응성을 향상시키고 MFA 환경을 더욱 원활하게 만들기 위해 다음과 같은 기능이 추가되었습니다. 

- MFA 결과에 대한 프로그래밍 방식의 액세스를 포함하여 다단계 인증 질문의 결과를 Azure AD 로그인 보고서에 직접 통합

- Azure Portal의 Azure AD 구성 환경에 MFA 구성을 심층 통합

이 공개 미리 보기에서는 핵심 Azure AD 구성 환경에 통합된 MFA 관리와 보고 기능을 경험할 수 있습니다. MFA 관리와 보고 기능이 통합됨으로써 관리자는 Azure AD 환경에서 MFA 관리 포털 기능을 관리할 수 있게 됩니다.

자세한 내용은 [Azure Portal의 다단계 인증 보고에 대한 참조 정보](active-directory-reporting-activity-sign-ins-mfa.md)를 참조하세요. 


---

### <a name="introducing-terms-of-use"></a>사용 약관 소개



**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스  



Azure AD 사용 약관 기능을 사용하면 간단한 방법을 이용하여 최종 사용자들에게 정보를 제공할 수 있습니다. 이렇게 하면 사용자가 법률 또는 규정 준수 요구 사항에 대한 관련 고지 사항을 볼 수 있습니다.

다음 시나리오에서 Azure AD 사용 약관을 사용할 수 있습니다.

- 조직의 모든 사용자에 대한 일반 사용 약관 

- 사용자 특성을 기반으로 하는 특정 사용 약관(예: 의사 및 간호사, 국내 및 국외 직원, 동적 그룹으로 수행). 

- Salesforce와 같은 비즈니스 영향력이 높은 앱에 대한 액세스용 특정 사용 약관

자세한 내용은 [Azure Active Directory 사용 약관](active-directory-tou.md)을 참조하세요.


---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management의 기능 개선


**유형:** 새로운 기능  
**서비스 범주:** PIM  
**제품 기능:** Privileged Identity Management  


이제 Azure Active Directory PIM(Privileged Identity Management)을 사용하여 조직에서 Azure Resources(미리 보기)에 대한 액세스를 관리, 제어 및 모니터링할 수 있습니다.

- 구독
- 리소스 그룹
- 가상 머신 

Azure Portal에서 Azure RBAC(역할 기반 액세스 제어) 기능을 사용하는 모든 리소스는 Azure AD PIM이 제공하는 보안 및 수명 주기 관리 기능을 모두 사용할 수 있습니다.

자세한 내용은 [Azure Resources용 PIM](privileged-identity-management/azure-pim-resource-rbac.md)을 참조하세요.


---

### <a name="introducing-access-reviews"></a>액세스 검토 소개


**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 거버넌스  



액세스 검토(미리 보기)를 통해 조직은 그룹 구성원 자격을 효율적으로 관리하고 엔터프라이즈 응용 프로그램에 액세스할 수 있습니다. 

- 응용 프로그램 및 그룹 구성원의 액세스 권한에 대한 액세스 검토를 사용하여 게스트 사용자 액세스를 다시 인증할 수 있습니다. 검토자는 액세스 검토에서 제공하는 통찰력을 통해 게스트가 계속 액세스해야 하는지 여부를 효율적으로 결정할 수 있습니다.

- 액세스 검토를 통해 응용 프로그램 및 그룹 멤버 자격에 대한 직원 액세스를 재인증할 수 있습니다.

액세스 검토 컨트롤을 조직과 관련된 프로그램으로 수집하여 규정 준수 또는 위험 감지 응용 프로그램에 대한 검토를 추적할 수 있습니다.

자세한 내용은 [Azure AD 액세스 검토](active-directory-azure-ad-controls-access-reviews-overview.md)를 참조하세요.


---

### <a name="hiding-third-party-applications-from-my-apps-and-the-office-365-launcher"></a>내 앱 및 Office 365 시작 관리자에서 타사 응용 프로그램 숨기기



**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO  



이제 새 **앱 숨기기** 속성을 통해 사용자 포털에 표시되는 앱을 더 잘 관리할 수 있습니다. 이 기능은 앱 타일이 백엔드 서비스나 복제 타일에 표시되어 사용자의 앱 시작 관리자가 복잡해지는 경우 유용하게 사용할 수 있습니다. 이 기능을 사용하려면 해당 타사 앱의 속성 섹션에 있는 **사용자에게 표시**를 끄거나 켜면 됩니다. PowerShell을 통해 프로그래밍 방식으로 앱을 숨길 수도 있습니다. 

자세한 내용은 [Hide a third-party application from user's experience in Azure Active Directory](active-directory-coreapps-hide-third-party-app.md)(Azure Active Directory의 사용자 환경에서 타사 응용 프로그램 숨기기)를 참조하세요. 


**사용할 수 있는 기능은 무엇인가요?**

 새 관리 콘솔로 전환하면서 Azure AD 활동 로그를 가져오는 2개의 API가 새롭게 적용되었습니다. 새로 적용된 API는 다양한 감사 및 로그인 활동에 더해 다양한 필터링 및 정렬 기능을 제공합니다. 이전에 보안 보고서를 통해 사용 가능했던 데이터를 이제 Microsoft Graph의 ID 보호 위험 이벤트 API를 통해 액세스할 수 있습니다.


## <a name="september-2017"></a>2017년 9월

### <a name="hotfix-for-microsoft-identity-manager"></a>Microsoft Identity Manager용 핫픽스


**유형:** 변경된 기능  
**서비스 범주:** Microsoft Identity Manager  
**제품 기능:** ID 수명 주기 관리  



핫픽스 롤업 패키지(빌드 4.4.1642.0)는 2017년 9월 25일부터 MIM(Microsoft Identity Manager) 2016 2016 SP1(서비스 팩 1)용으로 사용할 수 있습니다. 이 롤업 패키지는 다음과 같습니다.

- 문제를 해결하고 개선 사항을 추가합니다.
- Microsoft Identity Manager 2016용 빌드 4.4.1459.0까지 모든 MIM 2016 SP1 업데이트를 대체하는 누적 업데이트입니다. 
- **Microsoft Identity Manager 2016 빌드 4.4.1302.0**이 필요합니다. 

자세한 내용은 [Microsoft Identity Manager 2016 SP1용 핫픽스 롤업 패키지(빌드 4.4.1642.0) 가능](https://support.microsoft.com/en-us/help/4021562)을 참조하세요. 

---
