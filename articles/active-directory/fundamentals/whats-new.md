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
ms.date: 05/23/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6a66aefec69a0551f85b11a380c90d1915bd776
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474196"
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

## <a name="may-2019"></a>2019년 5월

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>서비스 변경 내용: 응용 프로그램 프록시 서비스에서 TLS 1.2 프로토콜만 대 한 지원 되지 않음

**유형:** 변경 계획  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

고객을 위한 최고의에서 암호화를 제공 하려면 응용 프로그램 프록시 서비스에서 TLS 1.2 프로토콜만에 대 한 액세스를 제한 했습니다. 이 변경은 점진적으로 롤아웃하는 내용이 표시 되지 않아야 하므로 TLS 1.2 프로토콜을 이미만 사용 하는 고객.

2019 년 8 월 31 일에 TLS 1.0 및 TLS 1.1의 사용 중단 발생 하지만이 변경에 대 한 준비 시간을 볼 수 있도록 추가 사전된 통지를 제공 합니다. 준비 하려면이 변경 해야 사용자가 응용 프로그램 프록시를 통해 게시 된 앱에 액세스를 사용 하 여 클라이언트를 포함 하 여 사용자 클라이언트-서버 및 브라우저-서버 조합, 응용 프로그램에 대 한 연결을 유지 하기 위해 TLS 1.2 프로토콜을 사용 하도록 업데이트 프록시 서비스입니다. 자세한 내용은 [Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램을 추가 합니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)합니다.

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>사용량 및 insights 보고서를 사용 하 여 앱 관련 로그인 데이터를 보려면

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 모니터링 및 보고

이제는 사용량과 insights 보고서에 사용할 수 있습니다 합니다 **엔터프라이즈 응용 프로그램** 중심 응용 프로그램에 대 한 정보를 포함 하 여 로그인 데이터를 가져오기 위해 Azure 포털의 영역:

- 상위 조직에 대 한 앱 사용

- 가장 많이 실패 한 로그인을 사용 하 여 앱

- 각 앱에 대 한 상위 로그인 오류

이 기능에 대 한 자세한 내용은 참조 하세요. [Azure Active Directory 포털의 사용량 및 insights 보고서](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>사용자가 Azure AD를 사용 하 여 앱을 클라우드로 프로 비전 자동화

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 모니터링 및 보고

이러한 새 자습서에 따라 Azure AD 프로 비전 서비스 만들기, 삭제, 자동화를 사용 하 고 다음 클라우드 기반 앱에 대 한 사용자 계정 업데이트:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

수행할 수 있습니다이 새로운 [Dropbox 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), 그룹 개체 프로 비전 하는 방법에 대 한 정보를 제공 하는 합니다.

자동화 된 사용자 계정 프로 비전을 통해 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure AD와 SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)합니다.

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identity 보안 점수 (일반 공급)를 Azure AD에서 제공 됩니다.

**유형:** 새 기능  
**서비스 범주:** N/A  
**제품 기능:** ID 보안 및 보호

이제 모니터링 하 고 본인을 향상 시킬 수 id를 사용 하 여 보안 상태를 Azure AD에서 점수 매기기 기능을 보호 합니다. 점수 기능 사용 하는 데 단일 대시보드에서 보안 하는 id:

- 1에서 223 사이의 점수를 기준으로 하 여 identity 보안 상태를 객관적으로 측정 합니다.

- Id 보안 개선 프로그램에 대 한 계획

- 사용자 보안 개선의 성공 여부를 검토 합니다.

Id 보안 점수 기능에 대 한 자세한 내용은 참조 하세요. [Azure Active Directory에서 identity 보안 점수 란?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)합니다.

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>새 앱 등록 환경을 사용할 수 있습니다 (일반 공급) 되었습니다.

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 개발자 환경

새 [앱 등록](https://aka.ms/appregistrations) 환경은 이제 일반적 가용성입니다. 이 새로운 환경은 익숙한 응용 프로그램 등록 포털 및 Azure portal에서 모든 주요 기능을 포함 하 고 통해 모아 개선:

- **더 나은 앱 관리** 다른 포털에서 앱을 표시 하는 대신 이제 한곳에서 모든 앱을 볼 수 있습니다.

- **앱 등록을 간소화 합니다.** 개선 된 사용 권한 선택 환경이 개선 된 탐색 경험을 통해 쉽습니다 이제 등록 및 앱을 관리 합니다.

- **자세한 정보입니다.** 빠른 시작 가이드 및 기타 정보를 포함 하 여 앱에 대 한 자세한 세부 정보를 찾을 수 있습니다.

자세한 내용은 [Microsoft id 플랫폼](https://docs.microsoft.com/azure/active-directory/develop/) 및 [앱 등록 환경에 출시 되었습니다!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 블로그 공지 합니다.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Id 보호에 대 한 위험한 사용자 API에서 사용할 수 있는 새로운 기능

**유형:** 새 기능  
**서비스 범주:** Identity Protection  
**제품 기능:** ID 보안 및 보호

우리는 이제 사용할 수 있습니다 위험한 사용자 API 사용자 위험 기록을 검색, 위험한 사용자를 해제 하 고 손상 된 대로 사용자를 확인 가능해졌습니다. 이 변경을 사용 하면 보다 효율적으로 사용자의 위험 상태를 업데이트 하 고 해당 위험 기록 이해 하도록 도와줍니다.

자세한 내용은 참조는 [위험한 사용자 API 참조 설명서](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)합니다.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>있는 신규 페더레이션 앱에서 Azure AD 앱 갤러리-2019 년 5 월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

월 2019에 앱 갤러리에 페더레이션이 포함 된 이러한 21 새 앱 지원 추가 했습니다.

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [실제 링크](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/)를 [간단한 로그인](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial)를 [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial)를 [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales 참여](https://toutapp.com/login)합니다 [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial)를 [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 전역 HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [퀀텀 Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial)합니다 [코발트](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API 클라우드](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [제어](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial)를 [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial)를 [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial)를 [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Azure AD 포털의 향상 된 그룹 만들기 및 관리 환경

**유형:** 새 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업

Azure AD 포털의 그룹 관련 경험을 개선을 했습니다. 이러한 향상 된이 기능에는 관리자가 추가 만들기 옵션을 제공 하 고 그룹 목록, 멤버 목록에 더 효율적으로 관리할 수 있습니다.

향상된 기능은 다음과 같습니다.

- 기본 멤버 자격 유형 및 그룹 유형을 필터링 합니다.

- 원본 및 전자 메일 주소와 같은 새 열을 추가 합니다.

- 다중 선택 그룹, 멤버 및 소유자 수 있는 기능 쉽게 삭제를 나열합니다.

- 전자 메일 주소를 선택 하 고 그룹을 만들 때 소유자를 추가 하는 기능.

자세한 내용은 [기본 그룹을 만들고 Azure Active Directory를 사용 하 여 멤버를 추가](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)합니다.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Azure AD 포털 (일반 공급)에서 Office 365 그룹 명명 정책 구성

**유형:** 변경된 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업

관리자 Azure AD 포털을 사용 하 여 Office 365 그룹에 대 한 명명 정책 이제 구성할 수 있습니다. 이 변경은 조직의 사용자가 생성 하거나 편집한 Office 365 그룹에 대 한 일관성 있는 명명 규칙을 적용할 수 있습니다.

두 가지 방법으로 Office 365 그룹 명명 정책을 구성할 수 있습니다.

- 접두사 또는 접미사는 그룹 이름에 자동으로 추가 되는 정의 합니다.

- 그룹 이름 (예: "CEO, Payroll, HR")에 허용 되지 않는 조직에 대 한 차단 된 단어의 사용자 지정된 된 집합을 업로드 합니다.

자세한 내용은 [Office 365 그룹 명명 정책 적용](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)합니다.

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API 끝점의 Azure AD 활동 로그 (일반 공급) 제공 됩니다.

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

Azure AD 활동에 대 한 Microsoft Graph API 끝점 지원의 일반 공급 발표를 로그 합니다. 이 릴리스에서 사용 하 여 Azure AD의 버전 1.0 감사 로그, 로그인 로그 뿐만 아니라 Api 이제 수 있습니다.

자세한 내용은 [Azure AD 감사 로그 API 개요](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)합니다.

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>관리자가 결합 된 등록 프로세스 (공개 미리 보기)에 대 한 조건부 액세스를 이제 사용할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호  

관리자 결합 된 등록 페이지 사용에 대 한 조건부 액세스 정책을 만들 수 있습니다. 여기에 등록을 허용 하도록 정책을 적용 합니다.

- 사용자가 신뢰할 수 있는 네트워크에 연결 되어 있습니다.

- 사용자가 로그인 위험이 적습니다.

- 사용자가 관리 되는 장치에 있습니다.

- 사용자는 조직의 사용 약관 (TOU)에 동의합니다.

조건부 액세스 및 암호 재설정에 대 한 자세한 내용은 참조는 [MFA를 결합 하는 Azure AD에 대 한 조건부 액세스 및 암호 재설정 등록 환경을 블로그 게시물](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)합니다. 결합 된 등록 프로세스에 대 한 조건부 액세스 정책에 대 한 자세한 내용은 참조 하세요. [결합 된 등록에 대 한 조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)합니다. 기능 사용의 Azure AD 용어에 대 한 자세한 내용은 참조 [Azure Active Directory 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)합니다.

---

## <a name="april-2019"></a>2019년 4월

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>새 Azure AD 위협 인텔리전스 검색 새로 고침된의 Azure AD Id 보호에서 제공 됩니다.

**유형:** 새 기능  
**서비스 범주:** Azure AD Identity Protection  
**제품 기능:** ID 보안 및 보호

이제 azure AD 위협 인텔리전스 검색 새로 고침된 하는 Azure AD Id 보호에서 제공 됩니다. 이 새로운 기능 하는 특정 사용자에 대 한 일반적이 지 않습니다. 또는 Microsoft의 내부 및 외부 위협 인텔리전스를 기반으로 하는 알려진된 공격 패턴과 일치 하는 사용자 활동을 나타낼 수 있습니다.

새로 고친된 버전의 Azure AD Id 보호에 대 한 자세한 내용은 참조는 [네 가지 주요 Azure AD Id 보호 기능이 공개 미리 보기로 제공 됩니다](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) 블로그 및 [Azure Active Directory 란 Id 보호 (새로 고침된)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) 문서입니다. Azure AD 위협 인텔리전스 검색에 대 한 자세한 내용은 참조는 [Azure Active Directory Id 보호 위험 이벤트](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) 문서.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD 권한 관리는 이제 사용할 수 있습니다 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** ID 거버넌스  
**제품 기능:** ID 거버넌스

Azure AD 권한 관리, 이제 공개 미리 보기로 제공에서 하는 데 도움이 직원 및 비즈니스 파트너 액세스를 요청 하는 방법, 승인 해야 하는 및 액세스할 수 있는 기간을 정의 하는 액세스 패키지 관리를 위임 합니다. 패키지 액세스 자격이 Azure AD 및 Office 365 그룹, 엔터프라이즈 응용 프로그램에서 역할 할당 및 SharePoint Online 사이트에 대 한 역할 할당을 관리할 수 있습니다. 권한 관리에 대 한 자세한 내용은 합니다 [Azure AD 권한 관리의 개요](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)합니다. Privileged Identity Management, 액세스 검토 및 사용 약관을 포함 하 여 Azure AD Id 거 버 넌 스 기능을 다양 한 방법에 대 한 자세한 내용은 [Azure AD Id 거 버 넌 스를 란?](../governance/identity-governance-overview.md)합니다.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Azure AD 포털 (공개 미리 보기)에서 Office 365 그룹 명명 정책 구성

**유형:** 새 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업

관리자 Azure AD 포털을 사용 하 여 Office 365 그룹에 대 한 명명 정책 이제 구성할 수 있습니다. 이 변경은 조직의 사용자가 생성 하거나 편집한 Office 365 그룹에 대 한 일관성 있는 명명 규칙을 적용할 수 있습니다.

두 가지 방법으로 Office 365 그룹 명명 정책을 구성할 수 있습니다.

- 접두사 또는 접미사는 그룹 이름에 자동으로 추가 되는 정의 합니다.

- 그룹 이름 (예: "CEO, Payroll, HR")에 허용 되지 않는 조직에 대 한 차단 된 단어의 사용자 지정된 된 집합을 업로드 합니다.

자세한 내용은 [Office 365 그룹 명명 정책 적용](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)합니다.

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD 활동 로그 (일반 공급) Azure Monitor에서 제공 됩니다.

**유형:** 새 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

를 Azure AD 활동 로그를 사용 하 여 시각화에 대 한 의견을 해결할 수 있도록 Log Analytics의 새로운 Insights 기능을 소개 합니다. 이 기능을 사용 하면 통합 문서를 호출, 대화형이 템플릿을 사용 하 여 Azure AD 리소스에 대 한 정보를 얻을 수 있습니다. 이들 미리 작성 된 통합 문서 앱 또는 사용자에 대 한 세부 정보를 제공 하 고 포함할 수 있습니다.

- **로그인 합니다.** 앱 및 로그인 위치, 사용 중인 운영 체제 또는 브라우저 클라이언트 및 버전 및 성공 또는 실패 한 로그인 수를 포함 한 사용자에 대 한 세부 정보를 제공 합니다.

- **레거시 인증 및 조건부 액세스를 제공 합니다.** 앱 및 조건부 액세스 정책을 조건부 액세스 정책을 사용 하 여 앱에 의해 트리거되는 Multi-factor Authentication 사용을 비롯 한 레거시 인증을 사용 하 여 사용자에 대 한 세부 정보를 제공 등에입니다.

- **로그인 실패 분석 합니다.** 로그인 오류가 사용자 작업, 정책 문제 또는 인프라도 인해 발생 하는 경우 확인할 수 있습니다.

- **사용자 지정 보고서입니다.** 새로 만들 수도 있고 조직 Insights 기능을 사용자 지정 하는 데 기존 통합 문서 편집.

자세한 내용은 [Azure Active Directory 보고에 대 한 Azure Monitor 통합 문서를 사용 하는 방법을](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)합니다.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>있는 신규 페더레이션 앱에서 Azure AD 앱 갤러리-2019 년 4 월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

2019 년 4 월에 앱 갤러리에 페더레이션이 포함 된 이러한 21 새 앱 지원 추가 했습니다.

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)를 [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial)를 [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial)를 [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial)합니다 [MileIQ](https://mileiq.onelink.me/991934284/7e980085)를 [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial)를 [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio 연결](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial)를 [Mitel 연결](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial)를 [Alibaba 클라우드 (역할 기반 SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent 지분 Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo 인증서 관리자](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial)합니다 [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial)를 [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>새 액세스 검토 빈도 옵션과 여러 역할 선택

**유형:** 새 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** ID 거버넌스

새 업데이트를 Azure AD에서 액세스 검토를 사용 하면:

- 변경 액세스의 빈도를 검토 **반 annually**, 매주, 매월, 매분기 및 매년의 기존 옵션 외에도 합니다.

- 여러 Azure AD를 선택 하 고 단일 액세스를 만들 때 Azure 리소스 역할 검토 합니다. 이 경우 동일한 설정을 사용 하 여 모든 역할 설정 및 동시에 모든 검토자에 게 알림이 표시 됩니다.

액세스 검토를 만드는 방법에 대 한 자세한 내용은 참조 하세요. [그룹의 액세스 검토를 만들거나 Azure AD에서 응용 프로그램 액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)합니다.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect의 전자 메일 경고 시스템 전환 하 고, 일부 고객에 대 한 새 전자 메일 보낸 사람 정보 보내기

**유형:** 변경된 기능  
**서비스 범주:** AD Sync  
**제품 기능:** 플랫폼

Azure AD Connect 잠재적으로 일부 고객은 새 전자 메일 보낸 사람으로 표시는 전자 메일 경고 시스템을 전환 하는 중입니다. 이 해결 하려면 추가 해야 `azure-noreply@microsoft.com` 를 조직의 허용 목록 또는 계속 Office 365, Azure 또는 동기화 서비스에서 중요 한 경고를 수신할 수 없습니다.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Azure AD Connect에서 페더레이션된 도메인 간에 성공적으로 UPN 접미사 변경 됩니다.

**유형:** 수정됨  
**서비스 범주:** AD Sync  
**제품 기능:** 플랫폼

이제 변경할 수 있습니다 성공적으로 사용자의 UPN 접미사가 하나의 페더레이션된 도메인에서 Azure AD Connect에서 다른 페더레이션된 도메인으로 합니다. 동기화 주기 동안 FederatedDomainChangeError 오류 메시지가 발생할 하거나 나타날는 알림 전자 메일을 더 이상 해야이 문제가 해결 의미 "때문에 Azure Active Directory에서이 개체를 업데이트할 수 없습니다 특성 [ FederatedUser.UserPrincipalName], 유효 하지 않습니다. 로컬 디렉터리 서비스에서 값을 업데이트할 "입니다.

자세한 내용은 [동기화 중 오류 문제 해결](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)합니다.

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Azure AD (공개 미리 보기)에서 앱 보호 기반 조건부 액세스 정책을 사용 하는 향상 된 보안

**유형:** 새 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

앱 보호 기반 조건부 액세스를 사용 하 여 출시 되었습니다 합니다 **앱 보호가 필요한** 정책입니다. 이 새 정책을 방지 하 여 조직의 보안을 향상 시킬 수 있습니다:

- 사용자는 Microsoft Intune 라이선스 없이 앱에 대 한 액세스를 얻을 수 없습니다.

- 사용자가 Microsoft Intune 앱 보호 정책을 가져올 수 없게 합니다.

- 사용자 없이 구성 된 Microsoft Intune 앱 보호 정책 앱에 대 한 액세스를 얻을 수 없습니다.

자세한 내용은 [조건부 액세스를 사용 하 여 클라우드 앱 액세스를 위해 앱 보호 정책을 요구 하는 방법](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)합니다.

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>새 Azure AD single sign-on 및 조건부 액세스 (공개 미리 보기)는 Microsoft Edge에서 지원

**유형:** 새 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

Azure AD single sign-on 및 조건부 액세스에 대 한 새로운 지원 제공이 포함 되는 Microsoft Edge에 대 한 Azure AD 지원과 개선 했습니다. 이전에 Microsoft Intune Managed Browser에서 사용 하는 경우 이제 사용할 수 있습니다 Microsoft Edge 대신 합니다.

설정 및 장치 및 조건부 액세스를 사용 하 여 앱을 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [필요한 조건부 액세스를 사용 하 여 클라우드 앱 액세스를 위해 장치를 관리 되는](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) 및 [필요한 승인 된 클라우드에 대 한 클라이언트 앱 조건부 액세스를 사용 하 여 앱 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)합니다. Microsoft Edge를 사용 하 여 Microsoft Intune 정책을 사용 하 여 액세스를 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [인터넷 액세스 관리를 Microsoft Intune 정책으로 보호 된 브라우저를 사용 하 여](https://docs.microsoft.com/intune/app-configuration-managed-browser)입니다.

---

## <a name="march-2019"></a>2019 년 3 월

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Id 경험 프레임 워크와 사용자 지정 정책을 Azure Active Directory B2C에서 지원 공급 (GA) 됩니다.

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 다음 작업을 포함 하 여 Azure AD b2c에서 사용자 지정 정책을 만들 수 있습니다 규모를 지원 되 고 Azure SLA:

- 만들고 사용자 지정 정책을 사용 하 여 사용자 지정 인증 사용자 경험을 업로드 합니다.

- 클레임 공급자 간의 교환으로 사용자 경험을 단계별로 설명합니다.

- 사용자 경험에서 조건부 분기를 정의합니다.

- 변환 하 고 실시간 의사 결정 및 통신에 사용 하기 위해 클레임을 매핑하십시오.

- 사용자 지정 인증 사용자 경험에서 REST API 사용 서비스를 사용 합니다. 예를 들어와 전자 메일 공급자, Crm을 전용 권한 부여 시스템입니다.

- OpenIDConnect 프로토콜을 호환 되는 id 공급자와 페더레이션 합니다. 예를 들어 다중 테 넌 트 Azure AD, 소셜 계정 공급자 또는 2 단계 인증 공급자입니다.

사용자 지정 정책을 만드는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Active Directory B2C에서 사용자 지정 정책에 대 한 개발자 노트](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) 읽고 [Alex Simon의 블로그 게시물, 사례 연구를 포함 하 여](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)입니다.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>있는 신규 페더레이션 앱에서 Azure AD 앱 갤러리-2019 년 3 월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

2019 년 3 월에 앱 갤러리에 페더레이션이 포함 된 이러한 14 새 앱 지원 추가 했습니다.

[ISEC7 Mobile Exchange 대리자](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial)를 [된 받침](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial)합니다 [설명 기반 감사 시스템](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool 성능 문제](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial)합니다 [Cinode](https://cinode.com/), [아이리스 인트라넷](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial)를 [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial)하십시오 [Confirmit 지평](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [작업](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Zscaler 새롭고 Atlassian 프로 비전 커넥터를 Azure AD 갤러리에서-2019 년 3 월

**유형:** 새 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합

만들기, 업데이트 및 다음 앱에 대 한 사용자 계정 삭제를 자동화 합니다.

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning)를 [Zscaler One](https://aka.ms/ZscalerOneProvisioning)를 [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning)를 [Zscaler 3](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

자동화 된 사용자 계정 프로 비전을 통해 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure AD와 SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://aka.ms/ProvisioningDocumentation)합니다.

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>복원 하 고 Azure AD 포털에서 삭제 된 Office 365 그룹 관리

**유형:** 새 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업

이제 확인 하 고 Azure AD 포털에서 삭제 된 Office 365 그룹을 관리할 수 있습니다. 이 변경을 사용 하는 그룹은 복원, 영구적으로 수와 함께 조직에서 필요 하지 않은 모든 그룹을 삭제 하는 데 수를 확인할 수 있습니다.

자세한 내용은 [복원 만료 되었거나 삭제 된 그룹](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)합니다.

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Single sign on이 출시 되었으며 응용 프로그램 프록시 (공개 미리 보기)를 통해 Azure AD SAML로 보호 된 온-프레미스 앱

**유형:** 새 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

이제 온-프레미스에서 응용 프로그램 프록시를 통해 이러한 앱에 대 한 원격 액세스와 함께 SAML 인증 앱에서 single sign-on (SSO) 경험을 제공할 수 있습니다. 온-프레미스 앱을 사용 하 여 SAML SSO를 설정 하는 방법에 대 한 자세한 내용은 참조 하세요. [SAML에서 single sign-on 응용 프로그램 프록시 (미리 보기)를 사용 하 여 온-프레미스 응용 프로그램에 대 한](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)합니다.

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>클라이언트 앱 요청 루프에서 안정성 및 사용자 환경을 개선 하기 위해 중단 됩니다.

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

클라이언트 앱 올바르게 실행할 수 없습니다 수백 개의 동일한 로그인 요청 짧은 기간 동안. 이러한 요청을 성공적으로 든 상관 없이 모든에 기여 사용자 환경이 저하 및 강화 된 워크 로드 IDP의 가용성을 줄이고 모든 사용자에 대 한 대기 시간 증가 IDP에 대 한 합니다.

이 업데이트를 보냅니다는 `invalid_grant` 오류: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` 정상적인 작업의 범위를 벗어납니다 시간의 짧은 기간 동안 여러 번 중복 요청을 발급 하는 클라이언트 앱입니다. 이 문제가 발생 하는 클라이언트 앱에 사용자 다시 로그인 하도록 요구 하는 대화형 프롬프트를 표시 됩니다. 이 변경에 대 한 및이 오류를 발견할 경우 앱을 수정 하는 방법에 대 한 자세한 내용은 참조 하세요. [인증의 새로운 기능?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)합니다.

---

### <a name="new-audit-logs-user-experience-now-available"></a>새 감사 로그 사용자 환경을 출시 되었습니다.

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

새 Azure AD를 만들었습니다 **감사 로그** 가독성 및 정보를 검색 하는 방법을 개선 하기 위해 페이지입니다. 새 보려는 **감사 로그** 페이지에서 **감사 로그** 에 **활동** 섹션에서는 Azure AD의 합니다.

![샘플 정보를 사용 하 여 새 감사 로그 페이지](media/whats-new/audit-logs-page.png)

새에 대 한 자세한 내용은 **감사 로그** 페이지를 참조 하십시오 [Azure Active Directory 포털의 감사 활동 보고서](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs)합니다.

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>잘못 구성 된 조건부 액세스 정책에서 실수로 관리자 잠금을 방지 하려면 새 경고 및 지침

**유형:** 변경된 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

를 방지 하려면 관리자가 실수로 잘못 구성 된 조건부 액세스 정책을 통해 자신의 테 넌 트는 자신을 잠그지에서 새 경고 및 업데이트 된 지침 Azure portal에서 만든 했습니다. 새 지침에 대 한 자세한 내용은 참조 하세요. [Azure Active Directory 조건부 액세스의 서비스 종속성을 이란](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)합니다.

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>모바일 장치에서 사용 하 여 환경의 향상 된 최종 사용자 사용 약관

**유형:** 변경된 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스

기존 약관 검토 및 모바일 장치에서 사용 약관에 동의 하는 방법을 개선 하기 위해 사용 하 여 환경을 업데이트 했습니다. 이제 확대 / 축소, 돌아가서, 정보를 다운로드 하 수 하이퍼링크를 선택 합니다. 업데이트 된 사용 약관에 대 한 자세한 내용은 참조 하세요. [Azure Active Directory 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)합니다.

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>새 Azure AD 활동 로그 사용 가능한 환경 다운로드

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

이제 Azure portal에서 직접 대량의 활동 로그를 다운로드할 수 있습니다. 이 업데이트 수 있습니다.

- 최대 250,000 행 다운로드 합니다.

- 다운로드가 완료 되 면 알림을 받습니다.

- 파일 이름에 사용자 지정 합니다.

- 출력 형식으로 JSON 또는 CSV를 결정 합니다.

이 기능에 대 한 자세한 내용은 참조 하세요. [빠른 시작: Azure portal을 사용 하는 감사 보고서를 다운로드 합니다.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>호환성이 손상되는 변경 내용: 조건 평가 하 여 EAS (Exchange ActiveSync)에 대 한 업데이트

**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Access Control

Exchange ActiveSync (EAS) 다음 조건을 평가 하는 방법을 업데이트 릴리스하고:

- 사용자 위치, 국가, 지역 또는 IP 주소 기반

- 로그인 위험

- 디바이스 플랫폼

이전에 조건부 액세스 정책에서 이러한 상황을 적 수 조건 동작을 변경할 수 있습니다. 예를 들어, 이전에 사용자 위치 조건의 사용 하는 정책의 경우 사용자의 위치를 기준으로 이제 건너뛴 정책을 찾을 수 있습니다.

---

## <a name="february-2019"></a>2019년 2월

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>구성 가능한 Azure AD SAML 토큰 암호화(공개 미리 보기) 

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

이제 암호화 된 SAML 토큰을 받을 모든 지원 되는 SAML 앱을 구성할 수 있습니다. 구성 하 고 앱을 사용 하는 경우 Azure AD에서 Azure AD에 저장 된 인증서를 가져온 공개 키를 사용 하 여 내보낸된 SAML 어설션이 암호화 합니다.

SAML 토큰 암호화를 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure AD SAML 구성 토큰 암호화](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)합니다.

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 그룹 또는 앱에 대한 액세스 검토 만들기

**유형:** 새 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 거버넌스

이제 여러 그룹을 포함할 수 있습니다 또는 단일 Azure AD에 앱 액세스 그룹 멤버 자격에 앱 할당 검토 합니다. 여러 그룹을 사용 하 여 액세스 검토 또는 앱은 동일한 설정을 사용 하 여 설정 하 고 동시에 포함 된 모든 검토자에 게 알림이 표시 됩니다.

방법에 대 한 자세한 내용은 Azure AD 액세스 검토를 사용 하 여 액세스 검토 만들기, 참조 [Azure AD 액세스 검토에 그룹 또는 응용 프로그램의 액세스 검토 만들기](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱 - 2019년 2월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2019 년 2 월에 앱 갤러리에 페더레이션이 포함 된 이러한 27 새 앱 지원 추가 했습니다.

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial)합니다 [오타로](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7)를 [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)를 [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial)를 [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial)를 [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial)를 [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), 사용 권한 클릭 [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial)를 [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial)를 [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial)를 [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [지진 ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [공유는 꿈의](https://www.shareadream.org/how-it-works)를 [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial)를 [webMethods 통합 클라우드](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)를 [기술 원격 LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU 캠퍼스](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial)를 [Periscope 데이터](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial)를 [Netop 포털](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial)를 [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [Genesys여PureCloud](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp 생산성 플랫폼](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="enhanced-combined-mfasspr-registration"></a>향상된 통합 MFA/SSPR 등록

**유형:** 변경된 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 사용자 인증

고객 피드백에 대 한 응답을 개선 했습니다 결합 된 MFA/SSPR 등록 미리 보기 환경은 사용자의 MFA 및 SSPR에 대 한 해당 보안 정보를 보다 신속 하 게 등록 하도록 지원 합니다. 

**설정 하려면 사용자에 대 한 향상 된 경험에이 단계를 따라 현재:**

1. 전역 관리자 또는 사용자 관리자, Azure portal에 로그인 이동할 **Azure Active Directory > 사용자 설정 > 액세스 패널 미리 보기 기능에 대 한 설정을 관리**합니다. 

2. 에 **미리 보기를 사용할 수 있는 사용자는 등록 하 고 보안 정보 관리에 대 한 기능 – 새로 고침** 옵션을 선택에 대 한 기능을 켜려면를 **선택한 사용자 그룹** 또는 **모든 사용자** .

다음 몇 주 동안 우리가 이전 결합 된 MFA/SSPR 등록 미리 보기 환경에 아직 없는 켜져 있는 테 넌 트에서 끌 수를 삭제 하겠습니다.

**테 넌 트에 대 한 컨트롤을 제거할 경우를 확인 하려면 다음이 단계를 수행 합니다.**

1. 전역 관리자 또는 사용자 관리자, Azure portal에 로그인 이동할 **Azure Active Directory > 사용자 설정 > 액세스 패널 미리 보기 기능에 대 한 설정을 관리**합니다.  

2. 경우는 **등록 하 고 보안 정보 관리에 대 한 미리 보기 기능을 사용할 수 있는 사용자** 옵션을 설정 **None**, 테 넌 트에서 옵션이 제거 됩니다.

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

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 Azure AD B2C에서 제공 하는 페이지 요소의 특정 버전을 선택할 수 있습니다. 특정 버전을 선택 하면 페이지에 표시 되 고 예측 가능한 동작을 얻을 수 있습니다 전에 업데이트를 테스트할 수 있습니다. 또한 이제 하도록 선택할 수 있습니다에서 JavaScript 사용자 지정을 허용 하도록 특정 페이지 버전을 적용 합니다. 이 기능을 설정 하려면로 이동 합니다 **속성** 사용자 흐름에서 페이지입니다.

페이지 요소의 특정 버전을 선택 하는 방법에 대 한 자세한 내용은 참조는 [JavaScript 사용자 정 및 많은 새 기능에 이제 Azure AD B2C에](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) 블로그.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>B2C를 위한 구성 가능한 최종 사용자 암호 요구 사항(GA)

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

네이티브를 사용 하는 대신 최종 사용자에 대 한 조직의 암호 복잡성 설정 이제 Azure AD 암호 정책입니다. **속성** 블레이드에서 사용자의 흐름 (이전에 기본 제공 정책을 알려짐)의 암호 복잡성을 선택할 수 있습니다 **단순** 또는 **강력한**를 할 수 있습니다 만들기는 **사용자 지정** 요구 사항 집합입니다.

암호 복잡성 요구 사항 구성 하는 방법에 대 한 자세한 내용은 참조 하십시오 [Azure Active Directory B2C에서 암호에 복잡성 요구 사항을 구성](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)합니다.

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>사용자 지정 브랜드 인증 환경을 위한 새로운 기본 템플릿

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

에 새 기본 템플릿을 사용할 수 있습니다는 **레이아웃 페이지** 블레이드 (이전에 기본 제공 정책으로 알려짐) 사용자 흐름을 사용자 지정을 만들려면 사용자에 대 한 인증 환경을 브랜드.

템플릿을 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [JavaScript 사용자 정 및 많은 새 기능에 이제 Azure AD B2C에](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)입니다.

---

## <a name="january-2019"></a>2019년 1월

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>일회용 암호 인증을 사용하여 Active Directory B2B 협업(공용 미리 보기)

**유형:** 새 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C

Azure AD MSA(Microsoft 계정) 또는 Google 페더레이션 등의 다른 수단을 통해 인증할 수 없는 B2B 게스트 사용자를 위해 OTP(일회용 암호 인증)를 도입했습니다. 이 새로운 인증 방법을 사용하면 게스트 사용자가 새 Microsoft 계정을 만들 필요가 없습니다. 그 대신 초대를 사용하거나 공유 리소스에 액세스하는 동안 게스트 사용자는 임시 코드를 이메일 주소로 보내 줄 것을 요청할 수 있습니다. 게스트 사용자는 이 임시 코드를 사용하여 계속 로그인할 수 있습니다.

자세한 내용은 [이메일 일회용 암호 인증(미리 보기)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) 및 [Azure AD가 계정이 있는 모든 사용자가 원활하게 공유 및 협업하도록 하는](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) 블로그를 참조하세요.

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>새 Azure AD 애플리케이션 프록시 쿠키 설정

**유형:** 새 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

애플리케이션 프록시를 통해 게시되는 앱에 사용 가능한 새 쿠키 설정 3개가 도입되었습니다.

- **HTTP 전용 쿠키 사용.** 애플리케이션 프록시 액세스 및 세션 쿠키에 **HTTPOnly** 플래그를 설정합니다. 이 설정을 켜면 클라이언트 쪽 스크립팅을 통한 쿠키 복사나 수정을 방지할 수 있는 등 추가적인 보안 이점이 제공됩니다. 추가 이점을 활용하려면 **예**를 선택하여 이 플래그를 켜는 것이 좋습니다.

- **보안 쿠키 사용.** 애플리케이션 프록시 액세스 및 세션 쿠키에 **Secure** 플래그를 설정합니다. 이 설정을 켜면 HTTPS 등의 TLS 보안 채널을 통해서만 쿠키가 전송되므로 추가적인 보안 이점이 제공됩니다. 추가 이점을 활용하려면 **예**를 선택하여 이 플래그를 켜는 것이 좋습니다.

- **영구적 쿠키 사용.** 웹 브라우저를 닫을 때 액세스 쿠키가 만료되지 않습니다. 이러한 쿠키는 액세스 토큰의 수명 동안 유지됩니다. 그러나 만료 시간이 되거나 사용자가 쿠키를 수동으로 삭제하면 쿠키가 재설정됩니다. 기본 설정인 **아니요**를 유지하고 프로세스 간에 쿠키를 공유하지 않는 구형 앱에서만 설정을 켜는 것이 좋습니다.

새 쿠키에 대한 자세한 내용은 [Azure Active Directory에서 온-프레미스 애플리케이션에 액세스하기 위한 쿠키 설정](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)을 참조하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱 - 2019년 1월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2019년 1월에 페더레이션이 지원되는 다음의 신규 앱 35개가 앱 갤러리에 추가되었습니다.

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial)를 [재능 색상표](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial)를 [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial)를 [Cisco 포괄적인](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler 인터넷 액세스 관리자에 게](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [만료 알림](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial)를 [InstaVR 뷰어](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial)를 [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [동사](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora)합니다 [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital 닫기](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice 도구 키트](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ 클라우드 서비스 PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial)를 [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial)를 [CallPlease](https://webapp.callplease.com/create-account/create-account.html)를 [GTNexus SSO 시스템](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial)하십시오 [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial)를 [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [엔터프라이즈용 아레스](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [Office 365에 대 한 K2](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial)하십시오 [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial)를 [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas 플랫폼](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>새로운 Azure AD ID 보호 향상 기능(공개 미리 보기)

**유형:** 변경된 기능  
**서비스 범주:** Identity Protection  
**제품 기능:** ID 보안 및 보호

Azure AD ID 보호 공개 미리 보기 제품에는 다음과 같은 향상 기능이 추가되었습니다.

- 업데이트되고 더욱 긴밀하게 통합된 사용자 인터페이스

- 추가 API

- 기계 학습을 통해 향상된 위험 평가

- 제품 전반에 걸쳐 일관된 위험한 사용자 및 위험한 로그인 정보 제공

향상 기능에 대한 자세한 내용은 [갱신된 Azure Active Directory ID 보호 소개](https://aka.ms/IdentityProtectionDocs)를 참조하세요. 여기서 자세한 내용을 알아보고 제품 내 프롬프트를 통해 정보를 공유하는 방법을 파악할 수 있습니다.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>iOS 및 Android 디바이스의 Microsoft Authenticator 앱용 신규 앱 잠금 기능

**유형:** 새 기능  
**서비스 범주:** Microsoft Authenticator 앱  
**제품 기능:** ID 보안 및 보호

일회용 암호, 앱 정보 및 앱 설정을 더욱 안전하게 유지하려는 경우 Microsoft Authenticator 앱에서 앱 잠금 기능을 설정할 수 있습니다. 앱 잠금을 설정하면 Microsoft Authenticator 앱을 열 때마다 PIN이나 생체 인식 정보를 사용하여 인증을 하라는 메시지가 표시됩니다.

자세한 내용은 [Microsoft Authenticator 앱 FAQ](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq)를 참조하세요.

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>향상된 Azure AD PIM(Privileged Identity Management) 내보내기 기능

**유형:** 새 기능  
**서비스 범주:** 권한 있는 ID 관리  
**제품 기능:** 권한 있는 ID 관리

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
**서비스 범주:** Identity Protection  
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

**유형:** 새 기능  
**서비스 범주:** 디바이스 등록 및 관리  
**제품 기능:** 액세스 제어

관리자는 클라우드 디바이스 관리자 작업을 수행할 새 클라우드 디바이스 관리자 역할에 사용자를 할당할 수 있습니다. 클라우드 디바이스 관리자 역할이 할당된 사용자는 Azure AD에서 디바이스를 사용/사용하지 않도록 설정하고, 삭제하고, Azure Portal에서 Windows 10 BitLocker 키(있는 경우)를 읽을 수 있습니다.

역할 및 사용 권한에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)을 참조하세요.

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Azure AD의 새 작업 타임스탬프(공개 미리 보기)를 사용하여 디바이스 관리

**유형:** 새 기능  
**서비스 범주:** 디바이스 등록 및 관리  
**제품 기능:** 디바이스 수명 주기 관리

시간이 지남에 따라 해야 새로 고침 하는 사용자 환경에서 오래 된 장치 것을 방지 하려면 Azure AD에서 조직의 장치를 사용 중지는 것입니다. 이 프로세스를 지원하기 위해 Azure AD는 이제 새 작업 타임스탬프로 디바이스를 업데이트하여 디바이스 수명 주기를 관리할 수 있도록 지원합니다.

이 타임스탬프를 가져오고 사용하는 방법에 대한 자세한 내용은 [방법: Azure AD에서 부실 디바이스 관리](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)를 참조하세요.

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>관리자는 사용자가 각 장치에서 사용 약관을 수락 하도록 요구할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스
 
관리자 수 이제 설정 합니다 **사용자가 모든 장치에서 동의 하도록 요구** 사용자가 테 넌 트에서 사용 하 든 모든 장치에서 사용 약관에 동의 해야 하는 옵션입니다.

자세한 내용은 참조는 [장치별 약관은 Azure Active Directory 사용 약관 기능 사용 섹션](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)합니다.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>관리자의 되풀이 일정에 따라 만료를 사용 하 여 사용 약관을 구성할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스
 

관리자 켤 수는 **동의 만료** 모든 지정 된 되풀이 일정에 따라 사용자에 대 한 만료 사용 약관을 확인 하는 옵션입니다. 일정은 매년, 6개월마다, 매분기 또는 매월일 수 있습니다. 사용 약관 만료 된 후 사용자가 다시 수락 해야 합니다.

자세한 내용은 참조는 [는 Azure Active Directory 사용 약관 기능 섹션 사용 약관을 추가](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)합니다.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>관리자는 각 사용자의 일정에 따라 만료 사용 약관을 구성할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 거버넌스

기간을 지정 하는 사용자 사용 약관을 다시 수락 해야 합니다. 예를 들어 관리자는 사용자가 약관 90 일 마다 다시 수락 해야 합니다는 지정할 수 있습니다.

자세한 내용은 참조는 [는 Azure Active Directory 사용 약관 기능 섹션 사용 약관을 추가](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)합니다.
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory 역할에 대한 새 Azure AD PIM(Privileged Identity Management) 메일

**유형:** 새 기능  
**서비스 범주:** 권한 있는 ID 관리  
**제품 기능:** 권한 있는 ID 관리
 
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

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2018년 11월에 페더레이션이 지원되는 신규 앱 26개가 앱 갤러리에 추가되었습니다.

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager(ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---