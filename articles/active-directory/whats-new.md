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
ms.date: 11/06/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 93b80744f68546e37934c987f45d7e2747d64f83
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
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


## <a name="october-2017"></a>2017년 10월

**유형:** 변경 계획  
**서비스 범주:** 보고  
**제품 기능:** ID 수명 주기 관리  


**`https://graph.windows.net/<tenant-name>/reports/` 노드 아래에서 Azure AD 보고서(베타 버전) API가 사용되지 않음**

Azure Portal은 다음을 제공합니다.

- 새로운 Azure Active Directory 관리 콘솔 
- 활동 및 보안 보고서를 위한 새로운 API
 
이러한 새로운 기능으로 인해 **/reports** 끝점의 보고서 API는 2017년 12월 10일에 사용 중지됩니다. 

---

**유형:** 고정   
**서비스 범주:** 내 앱  
**제품 기능:** SSO  


Azure Active Directory는 HTML 사용자 이름 및 암호 필드를 렌더링하는 응용 프로그램의 자동 로그인 필드 검색을 지원합니다.  이 단계는 [응용 프로그램에 대한 로그인 필드를 자동으로 캡처하는 방법](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)에 설명되어 있습니다. 이 기능은 [Azure Portal](http://aad.portal.azure.com)의 **엔터프라이즈 응용 프로그램** 페이지에 *비갤러리* 응용 프로그램을 추가하여 찾을 수 있습니다. 또한 이 새 응용 프로그램의 **Single Sign-On** 모드를 **암호 기반 Single Sign-on**으로 구성하고 웹 URL을 입력한 다음 페이지를 저장할 수 있습니다.
 
서비스 문제로 인해 이 기능은 일정 기간 동안 일시적으로 사용 중지되었습니다. 문제가 해결되어 자동 로그인 필드 검색을 다시 사용할 수 있습니다.

---

**유형:** 새로운 기능  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호  


요즘 시대에는 MFA(다단계 인증)가 조직을 보호하는 데 필수적인 요소입니다. Microsoft의 ID 팀은 다단계 인증을 발전시켜 자격 증명의 적응력과 환경을 보다 원활하게 만듭니다. 오늘 이렇게 두 가지 중요한 단계를 발표하게 된 것을 기쁘게 생각합니다. 

- MFA 결과에 대한 프로그래밍 방식의 액세스를 포함하여 다단계 인증 질문의 결과를 Azure AD 로그인 보고서에 직접 통합

- Azure Portal의 핵심 Azure AD 구성 환경에 MFA 구성을 밀접하게 통합

이 공개 미리 보기를 사용하면 MFA 관리 및 보고가 핵심 Azure AD 구성 환경의 일부로 통합되어 Azure AD 환경에서 MFA 관리 포털 기능을 관리할 수 있습니다.

자세한 내용은 [Azure Portal의 다단계 인증 보고에 대한 참조 정보](active-directory-reporting-activity-sign-ins-mfa.md)를 참조하세요. 


---
**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스  


**Azure AD 사용 약관**은 최종 사용자에게 정보를 제공하는 간단한 방법을 제공합니다. 이렇게 하면 사용자가 법률 또는 규정 준수 요구 사항에 대한 관련 고지 사항을 볼 수 있습니다.

다음 시나리오에서 Azure AD 사용 약관을 사용할 수 있습니다.

- 조직의 모든 사용자에 대한 일반 사용 약관 

- 사용자 특성을 기반으로 하는 특정 사용 약관(예: 의사 및 간호사, 국내 및 국외 직원, 동적 그룹으로 수행). 

- Salesforce와 같은 비즈니스 영향력이 높은 앱에 대한 액세스용 특정 사용 약관

자세한 내용은 [Azure Active Directory 사용 약관](active-directory-tou.md)을 참조하세요.


---
**유형:** 새로운 기능  
**서비스 범주:** PIM  
**제품 기능:** Privileged Identity Management  


Azure Active Directory PIM(Privileged Identity Management)을 사용하면 이제 조직 내에서 Azure Resources(미리 보기)에 대한 액세스를 관리, 제어 및 모니터링할 수 있습니다. 여기에는 구독, 리소스 그룹 및 가상 컴퓨터도 포함됩니다. Azure Portal 내에서 Azure RBAC(역할 기반 액세스 제어) 기능을 활용하는 모든 리소스는 Azure AD PIM에서 제공해야 하는 매우 유용한 보안 및 수명 주기 관리 기능 모두와 Azure AD 역할에 곧 제공될 중요한 새 기능 몇 가지를 활용할 수 있습니다.

자세한 내용은 [Azure Resources용 PIM](privileged-identity-management/azure-pim-resource-rbac.md)을 참조하세요.


---
**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 거버넌스  


액세스 검토(미리 보기)를 통해 조직은 그룹 구성원 자격을 효율적으로 관리하고 엔터프라이즈 응용 프로그램에 액세스할 수 있습니다. 

- 응용 프로그램 및 그룹 구성원의 액세스 권한에 대한 액세스 검토를 사용하여 게스트 사용자 액세스를 다시 인증할 수 있습니다. 검토자는 액세스 검토에서 제공하는 통찰력을 통해 게스트가 계속 액세스해야 하는지 여부를 효율적으로 결정할 수 있습니다.

- 액세스 검토를 통해 응용 프로그램 및 그룹 멤버 자격에 대한 직원 액세스를 재인증할 수 있습니다.

액세스 검토 컨트롤을 조직과 관련된 프로그램으로 수집하여 규정 준수 또는 위험 감지 응용 프로그램에 대한 검토를 추적할 수 있습니다.

자세한 내용은 [Azure AD 액세스 검토](active-directory-azure-ad-controls-access-reviews-overview.md)를 참조하세요.


---
**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO  


**타사 응용 프로그램을 내 앱 및 Office 365 시작 관리자에서 숨길 수 있는 기능**

이제 새 **앱 숨기기** 속성을 통해 사용자 포털에 표시되는 앱을 더 잘 관리할 수 있습니다. 이 기능은 앱 타일이 백 엔드 서비스나 복제 타일에 표시되어 사용자의 앱 시작 관리자가 복잡해지는 경우 도움이 됩니다. 토글은 타사 앱의 속성 섹션에 있으며 **사용자에게 표시**라는 레이블이 지정됩니다. PowerShell을 통해 프로그래밍 방식으로 앱을 숨길 수도 있습니다. 

자세한 내용은 [Hide a third-party application from user's experience in Azure Active Directory](active-directory-coreapps-hide-third-party-app.md)(Azure Active Directory의 사용자 환경에서 타사 응용 프로그램 숨기기)를 참조하세요. 


**사용할 수 있는 기능은 무엇인가요?**

 새 관리 콘솔로 전환하면서 Azure AD 활동 로그를 검색할 수 있는 2개의 새로운 API를 만들었습니다. 새로운 API 집합은 보다 풍부한 감사 및 로그인 활동을 제공할 뿐만 아니라 보다 다양한 필터링 및 정렬 기능을 제공합니다. 이전에 보안 보고서를 통해 사용 가능했던 데이터를 이제 Microsoft Graph의 ID 보호 위험 이벤트 API를 통해 액세스할 수 있습니다.


## <a name="september-2017"></a>2017년 9월

**유형:** 변경된 기능  
**서비스 범주:** Microsoft Identity Manager  
**제품 기능:** ID 수명 주기 관리  


핫픽스 롤업 패키지(빌드 4.4.1642.0)는 2017년 9월 25일부터 MIM(Microsoft Identity Manager) 2016 2016 SP1(서비스 팩 1)용으로 사용할 수 있습니다. 이 롤업 패키지는 다음과 같습니다.

- 문제를 해결하고 개선 사항을 추가합니다.
- Microsoft Identity Manager 2016용 빌드 4.4.1459.0까지 모든 MIM 2016 SP1 업데이트를 대체하는 누적 업데이트입니다. 
- **Microsoft Identity Manager 2016 빌드 4.4.1302.0**이 필요합니다. 

자세한 내용은 [Microsoft Identity Manager 2016 SP1용 핫픽스 롤업 패키지(빌드 4.4.1642.0) 가능](https://support.microsoft.com/en-us/help/4021562)을 참조하세요. 

---
