---
title: 새로운 기능 릴리스 정보 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능, 예정된 변경 내용 등을 알아봅니다.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c58c99faa83d6f04ac37d03789202e111f1b06d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611163"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능

>이 URL을 복사 하 고이 URL을 붙여 넣어 업데이트에 대 한이 페이지 `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` 를 다시 ![방문 하는 시기](./media/whats-new/feed-icon-16x16.png) 에 대 한 알림 받기: RSS 피드 판독기 아이콘 피드 판독기.

Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월 이상된 항목을 찾으려는 경우 [Azure Active Directory의 새로운 기능 아카이브](whats-new-archive.md)에서 찾을 수 있습니다.

---

## <a name="april-2020"></a>2020년 4월

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>결합 된 보안 정보 등록 환경이 이제 일반 공급 됩니다.

**유형:** 새로운 기능

**서비스 범주:** 인증 (로그인)

**제품 기능:** Id 보안 & 보호

Multi-Factor Authentication (MFA) 및 셀프 서비스 암호 재설정 (SSPR)에 대 한 결합 된 등록 환경이 이제 일반 공급 됩니다. 이 새로운 등록 환경을 통해 사용자는 단일 단계별 프로세스에서 MFA 및 SSPR에 등록할 수 있습니다. 조직의 새 환경을 배포 하는 경우 사용자는 더 짧은 시간에 더 많은 작업을 등록할 수 있습니다. 블로그 [게시물을 확인 하세요.](https://bit.ly/3etiRyQ)

---

### <a name="continuous-access-evaluation"></a>연속 액세스 평가

**유형:** 새로운 기능

**서비스 범주:** 인증 (로그인)

**제품 기능:** Id 보안 & 보호

연속 액세스 평가는 Azure AD에서 이벤트가 발생 하는 경우 (예: 사용자 계정 삭제) Azure AD 액세스 토큰을 사용 하는 신뢰 당사자에 대해 거의 실시간으로 정책을 적용할 수 있는 새로운 보안 기능입니다. 팀과 Outlook 클라이언트를 위해이 기능을 먼저 롤링 합니다. 자세한 내용은 [블로그](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) 및 [설명서](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)를 참조 하세요.

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS 로그인: Firstline Worker는 전화 번호를 사용 하 고 암호 없이 Azure AD 지원 응용 프로그램에 로그인 할 수 있습니다.

**유형:** 새로운 기능

**서비스 범주:** 인증 (로그인)

**제품 기능:** 사용자 인증

Office는 기존 이외의 조직에서 사용할 수 있는 일련의 모바일 중심 비즈니스 앱과 주 통신 방법으로 전자 메일을 사용 하지 않는 대기업의 직원을 시작 합니다. 이러한 앱은 frontline 직원, deskless 작업자, 현장 에이전트 또는 소매점에서 전자 메일 주소를 얻지 못할 수도 있고, 컴퓨터에 대 한 액세스 권한이 있거나, 소매 직원을 대상으로 합니다. 이 프로젝트는 전화 번호를 입력 하 고 코드를 왕복 하 여 이러한 직원이 비즈니스 응용 프로그램에 로그인 할 수 있도록 합니다. 자세한 내용은 [관리자 설명서](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) 및 [최종 사용자 설명서](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer)를 참조 하세요.

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>내부 사용자에 게 B2B 공동 작업을 사용 하도록 초대

**유형:** 새로운 기능

**서비스 범주:** B2B

**제품 기능:**

Microsoft는 향후 b2b 공동 작업 자격 증명을 사용 하도록 기존 내부 계정을 초대할 수 있도록 B2B 초대 기능을 확장 하 고 있습니다. 초대 된 전자 메일 주소와 같은 일반적인 매개 변수와 함께 사용자 개체를 초대 API에 전달 하 여이 작업을 수행 합니다. 사용자의 개체 ID, UPN, 그룹 멤버 자격, 앱 할당 등은 그대로 유지 되지만 앞으로는 B2B를 사용 하 여 초대 전에 사용한 내부 자격 증명이 아닌 해당 홈 테 넌 트 자격 증명을 사용 하 여 인증 합니다. 자세한 내용은 [설명서](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users)를 참조 하세요.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>조건부 액세스에 대 한 보고서 전용 모드가 이제 일반 공급 됩니다.

**유형:** 새로운 기능

**서비스 범주:** 조건부 액세스

**제품 기능:** Id 보안 & 보호

[AZURE AD 조건부 액세스에 대 한 보고서 전용 모드](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) 를 통해 액세스 제어를 적용 하지 않고 정책의 결과를 평가할 수 있습니다. 조직 전체에서 보고서 전용 정책을 테스트 하 고 사용 하도록 설정 하기 전에 영향을 파악 하 여 배포를 더 안전 하 고 쉽게 만들 수 있습니다. 지난 몇 달 동안 보고서 전용 정책의 범위에 이미 있는 26M 사용자를 포함 하 여 보고서 전용 모드를 강력 하 게 채택 했습니다. 이 공지를 사용 하면 기본적으로 새 Azure AD 조건부 액세스 정책이 보고서 전용 모드에서 생성 됩니다. 즉, 정책을 만든 순간부터 정책의 영향을 모니터링할 수 있습니다. MS Graph Api를 사용 하는 사용자의 경우에는 [프로그래밍 방식으로 보고서 전용 정책을 관리할](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)수도 있습니다. 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>조건부 액세스 정보 및 보고 통합 문서를 일반적으로 사용할 수 있음

**유형:** 새로운 기능

**서비스 범주:** 조건부 액세스

**제품 기능:** Id 보안 & 보호

조건부 액세스 [정보 및 보고 통합 문서](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) 는 관리자에 게 테 넌 트의 Azure AD 조건부 액세스에 대 한 요약 보기를 제공 합니다. 관리자는 개별 정책을 선택 하는 기능을 사용 하 여 각 정책에서 수행 하는 작업을 더 잘 이해 하 고 실시간으로 변화를 모니터링할 수 있습니다. 통합 문서는 Azure Monitor에 저장 된 데이터를 스트리밍하 며 몇 분 내에 [이러한 지침에 따라](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)설정할 수 있습니다. 대시보드를 더 쉽게 검색할 수 있도록 Azure AD 조건부 액세스 메뉴 내의 새 정보 및 보고 탭으로 이동 했습니다.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>조건부 액세스에 대 한 정책 세부 정보 블레이드가 공개 미리 보기 상태입니다.

**유형:** 새로운 기능

**서비스 범주:** 조건부 액세스

**제품 기능:** Id 보안 & 보호

새 [정책 세부 정보 블레이드](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) 는 조건부 액세스 정책 평가 중에 충족 된 할당, 조건 및 컨트롤을 표시 합니다. 로그인 정보의 **조건부 액세스** 또는 **보고서 전용** 탭에서 행을 선택 하 여 블레이드에 액세스할 수 있습니다.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-4 월 2020

**유형:** 새로운 기능

**서비스 범주:** 엔터프라이즈 앱

**제품 기능:** 타사 통합

4 월 2020에 앱 갤러리에 대 한 페더레이션 지원과 함께 이러한 31 개의 새 앱을 추가 했습니다. 

[SincroPool Apps](https://www.sincropool.com/), [smartdb](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [iwt-9j-u8d 조달 Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/),, [Jira에 대 한](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial) [가상 교육 아카데미](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki 대시보드](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365](https://app.mover.io/login)이동 기 [, 발표자 참여](https://speakerengage.com/login.php), [솔직히](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [동맹](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial), [DutyFlow](https://app.dutyflow.nl/), [alertmedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial), [gr8](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [highground](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [조화](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), [Timetabling 솔루션](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), [SynchroNet 클릭](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [역량](https://www.made-in-office.com/en/), [fortes 자료 변경 클라우드](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial), [Litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial), [groupcoa](https://recorder.grouptalk.com/), Frontify, [MongoDB cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial), [TickitLMS 배우기](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [nitro 생산성 도구](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , [(Trend 마이크로 웹 보안)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial) [Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>공개 미리 보기에 사용할 수 있는 oAuth2PermissionGrant에 대 한 델타 쿼리 지원 Microsoft Graph

**유형:** 새로운 기능

**서비스 범주:** MS 그래프

**제품 기능:** 개발자 환경

OAuth2PermissionGrant에 대 한 델타 쿼리는 공개 미리 보기에서 사용할 수 있습니다. 이제 Microsoft Graph를 지속적으로 폴링할 필요 없이 변경 내용을 추적할 수 있습니다. [자세한 정보](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>조직 연락처에 대 한 Microsoft Graph 델타 쿼리 지원 일반 공급

**유형:** 새로운 기능

**서비스 범주:** MS 그래프

**제품 기능:** 개발자 환경

조직 연락처에 대 한 델타 쿼리를 일반적으로 사용할 수 있습니다. 이제 Microsoft Graph를 지속적으로 폴링 하지 않고도 프로덕션 앱에서 변경 내용을 추적할 수 있습니다. OrgContact 데이터를 지속적으로 폴링하는 기존 코드를 델타 쿼리로 대체 하 여 성능을 크게 향상 시킵니다. [자세한 정보](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>응용 프로그램에 대 한 델타 쿼리 지원이 일반 공급 되는 Microsoft Graph

**유형:** 새로운 기능

**서비스 범주:** MS 그래프

**제품 기능:** 개발자 환경

응용 프로그램에 대 한 델타 쿼리를 일반적으로 사용할 수 있습니다. 이제 Microsoft Graph를 지속적으로 폴링 하지 않고도 프로덕션 앱에서 변경 내용을 추적할 수 있습니다. 응용 프로그램 데이터를 지속적으로 폴링하는 기존 코드를 델타 쿼리로 대체 하 여 성능을 크게 향상 시킵니다. [자세한 정보](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>공개 미리 보기에 사용할 수 있는 관리 단위에 대 한 델타 쿼리 지원 Microsoft Graph

**유형:** 새로운 기능

**서비스 범주:** MS 그래프

**제품 기능:** 관리 단위에 대 한 개발자 환경 델타 쿼리는 공개 미리 보기에서 사용할 수 있습니다. 이제 Microsoft Graph를 지속적으로 폴링할 필요 없이 변경 내용을 추적할 수 있습니다. [자세한 정보](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>새 Microsoft Graph 베타 Api에서 인증 전화 번호 등을 관리 합니다.

**유형:** 새로운 기능

**서비스 범주:** MS 그래프

**제품 기능:** 개발자 환경

이러한 Api는 사용자의 인증 방법을 관리 하기 위한 핵심 도구입니다. 이제 MFA 및 SSPR (셀프 서비스 암호 재설정)에 사용 되는 인증자를 프로그래밍 방식으로 미리 등록 하 고 관리할 수 있습니다. 이는 Azure MFA, SSPR 및 Microsoft Graph 공간에서 가장 많이 요청 되는 기능 중 하나입니다. 이 wave에서 릴리스된 새 Api는 다음을 수행할 수 있는 기능을 제공 합니다.

- 사용자의 인증 전화를 읽고, 추가 하 고, 업데이트 하 고, 제거 합니다.
- 사용자의 암호 다시 설정
- SMS 로그인 설정 및 해제

자세한 내용은 [AZURE AD 인증 방법 API 개요](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)를 참조 하세요.

---

### <a name="administrative-units-public-preview"></a>관리 단위 공개 미리 보기

**유형:** 새로운 기능

**서비스 범주:** RBAC

**제품 기능:** 액세스 제어

관리 단위를 사용 하면 정의한 조직의 부서, 지역 또는 기타 세그먼트로 제한 된 관리자 권한을 부여할 수 있습니다. 관리 단위를 사용 하 여 지역 관리자에 게 권한을 위임 하거나 세부적인 수준에서 정책을 설정할 수 있습니다. 예를 들어 사용자 계정 관리자는 프로필 정보를 업데이트 하 고, 암호를 다시 설정 하 고, 관리 단위 에서만 사용자에 대 한 라이선스를 할당할 수 있습니다.

중앙 관리자는 관리 단위를 사용 하 여 다음과 같은 작업을 할 수 있습니다.

- 리소스의 분산 관리를 위한 관리 단위 만들기
- 관리 단위의 Azure AD 사용자만 관리 권한을 사용 하 여 역할 할당
- 필요에 따라 사용자 및 그룹으로 관리 단위 채우기

자세한 내용은 [Azure Active Directory의 관리 단위 관리 (미리 보기)](https://aka.ms/AdminUnitsDocs)를 참조 하세요.

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>프린터 관리자 및 프린터 기술자 기본 제공 역할

**유형:** 새로운 기능

**서비스 범주:** RBAC

**제품 기능:** 액세스 제어

**프린터 관리자**:이 역할의 사용자는 프린터를 등록 하 고 유니버설 인쇄 커넥터 설정을 포함 하 여 Microsoft 유니버설 인쇄 솔루션의 모든 프린터 구성의 모든 측면을 관리할 수 있습니다. 모든 위임 된 인쇄 권한 요청에 동의할 수 있습니다. 또한 프린터 관리자는 보고서 인쇄에 액세스할 수 있습니다. 

**Printer 기술자**:이 역할의 사용자는 Microsoft 유니버설 인쇄 솔루션에서 프린터를 등록 하 고 프린터 상태를 관리할 수 있습니다. 모든 커넥터 정보를 읽을 수도 있습니다. 프린터 기술 자가 할 수 없는 주요 작업은 프린터 및 공유 프린터에 대 한 사용자 권한을 설정 하는 것입니다. [자세한 정보](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>하이브리드 Id 관리 기본 제공 역할

**유형:** 새로운 기능

**서비스 범주:** RBAC

**제품 기능:** 액세스 제어

이 역할의 사용자는 Azure AD에서 하이브리드 id를 사용 하도록 설정 하는 것과 관련 된 서비스 및 설정을 사용, 구성 및 관리할 수 있습니다. 이 역할은 Azure AD를 PHS (암호 해시 동기화), PTA (통과 인증) 또는 페더레이션 (AD FS 또는 타사 페더레이션 공급자) &#8212;에서 지원 되는 세 가지 인증&#8212;방법 중 하나로 구성 하 고,이를 사용 하도록 설정 하는 데 관련 온-프레미스 인프라를 배포 하는 기능을 제공 합니다. 온-프레미스 인프라에는 프로 비전 및 PTA 에이전트가 포함 됩니다. 이 역할은 원활한 sso (Single Sign-on)를 사용 하 여 비 Windows 10 장치 또는 비 Windows Server 2016 컴퓨터에서 원활한 인증을 사용 하는 기능을 부여 합니다. 또한이 역할은 로그인 로그를 확인 하 고 모니터링 및 문제 해결을 위한 상태 및 분석에 액세스할 수 있는 기능을 부여 합니다. [자세한 정보](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>네트워크 관리자 기본 제공 역할

**유형:** 새로운 기능

**서비스 범주:** RBAC

**제품 기능:** 액세스 제어

이 역할을 가진 사용자는 Microsoft의 네트워크 경계 아키텍처 권장 사항을 검토 하 여 사용자 위치에서 네트워크 원격 분석을 기반으로 할 수 있습니다. Office 365의 네트워크 성능은 일반적으로 사용자 위치와 관련 된 신중한 엔터프라이즈 고객 네트워크 경계 아키텍처를 사용 합니다. 이 역할을 사용 하면 검색 된 사용자 위치를 편집 하 고 해당 위치에 대 한 네트워크 매개 변수를 구성 하 여 원격 분석 측정 및 디자인 권장 사항을 쉽게 개선할 수 있습니다. [자세한 정보](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Azure AD 관리 포털 환경에서 대량 작업 및 다운로드

**유형:** 새로운 기능

**서비스 범주:** 사용자 관리

**제품 기능:** 디렉터리나

이제 Azure AD 관리 포털 환경에서 CSV 파일을 업로드 하 여 Azure AD의 사용자 및 그룹에 대 한 대량 작업을 수행할 수 있습니다. 사용자를 만들고, 사용자를 삭제 하 고, 게스트 사용자를 초대할 수 있습니다. 그리고 그룹에서 구성원을 추가 하 고 제거할 수 있습니다.

Azure AD 관리 포털 환경에서 Azure AD 리소스 목록을 다운로드할 수도 있습니다. 디렉터리, 디렉터리의 그룹 목록 및 특정 그룹의 구성원에 대 한 사용자 목록을 다운로드할 수 있습니다.

자세한 내용은 다음을 확인 하세요.

- [사용자 만들기](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) 또는 [게스트 사용자 초대](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [사용자 삭제](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) 또는 [삭제 된 사용자 복원](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [사용자 목록 다운로드](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) 또는 [그룹 목록 다운로드](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- 그룹에 대 한 멤버 [추가 (가져오기)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) 또는 구성원 [제거](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) 또는 [구성원 목록 다운로드](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)

---

### <a name="my-staff-delegated-user-management"></a>내 직원 위임 된 사용자 관리

**유형:** 새로운 기능

**서비스 범주:** 사용자 관리

**제품 기능:**

직원 들이 자신의 직원 구성원이 자신의 Azure AD 계정에 액세스할 수 있도록 내 직원은 저장소 관리자와 같은 Firstline 관리자를 사용 하도록 설정 합니다. 조직에서는 중앙 기술 지원팀에 의존 하는 대신 암호 재설정 또는 전화 번호 변경과 같은 일반적인 작업을 Firstline Manager로 위임할 수 있습니다. 직원 들이 자신의 계정에 액세스할 수 없는 사용자는 기술 지원팀 또는 IT 담당자가 필요 하지 않은 두 번의 클릭 만으로 액세스 권한을 다시 얻을 수 있습니다. 자세한 내용은 내 [직원 (미리 보기)을 사용](https://aka.ms/MyStaffAdminDocs) 하 여 사용자 관리 및 [내 직원 (미리 보기)을 사용 하 여 사용자 관리 위임 (미리 보기)](https://aka.ms/MyStaffUserDocs)을 참조 하세요.

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>액세스 검토에서 업그레이드 된 최종 사용자 환경

**유형:** 변경된 기능

**서비스 범주:** 액세스 검토

**제품 기능:** Id 거 버 넌 스

내 앱 포털에서 Azure AD 액세스 검토에 대 한 검토자 환경을 업데이트 했습니다. 4 월 말에 Azure AD 액세스를 검토 하는 검토자에 게는 사용자의 액세스에 업데이트 된 환경을 사용해 볼 수 있는 배너가 표시 됩니다. 업데이트 된 액세스 검토 환경은 현재 환경과 동일한 기능을 제공 하지만 사용자가 생산성을 높일 수 있도록 새로운 기능을 기반으로 하는 향상 된 사용자 인터페이스를 제공 합니다. [업데이트 된 환경에 대 한 자세한 내용은 여기에서 확인할 수](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)있습니다. 이 공개 미리 보기는 7 월 2020이 끝날 때까지 마지막으로 발생 합니다. 7 월 말에 미리 보기 환경을 옵트인 하지 않은 검토자는 액세스 검토를 수행 하기 위해 자동으로 내 액세스로 전달 됩니다. 검토자가 내 액세스의 미리 보기 환경으로 영구적으로 전환 되도록 하려면 [여기에서 요청 하세요](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday 인바운드 사용자 프로 비전 및 쓰기 저장 앱은 이제 최신 버전의 Workday 웹 서비스 API를 지원 합니다.

**유형:** 변경된 기능

**서비스 범주:** 앱 프로비전

**제품 기능:** 

이제 고객의 의견에 따라 엔터프라이즈 앱 갤러리에서 Workday 인바운드 사용자 프로 비전 및 쓰기 저장 앱을 업데이트 하 여 최신 버전의 WWS (Workday 웹 서비스) API를 지원 합니다. 이러한 변경을 통해 고객은 연결 문자열에서 사용할 WWS API 버전을 지정할 수 있습니다. 이를 통해 고객은 Workday 릴리스에서 제공 되는 더 많은 HR 특성을 검색할 수 있습니다. Workday 쓰기 저장 앱은 이제 권장 Change_Work_Contact_Info Workday 웹 서비스를 사용 하 여 Maintain_Contact_Info의 제한을 극복 합니다.

연결 문자열에 버전이 지정 되지 않은 경우 기본적으로 Workday 인바운드 프로 비전 앱은 WWS v 21.1를 사용 하 여 인바운드 사용자 프로 비전을 위한 최신 Workday Api로 전환 하 고, 고객은 [자습서에 설명 된](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) 대로 연결 문자열을 업데이트 하 고 [workday 특성 참조 가이드](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)에 설명 된 대로 workday 특성에 사용 되는 xpath도 업데이트 해야 합니다. 

쓰기 저장 (writeback)을 위해 새 API를 사용 하려면 Workday 쓰기 저장 프로 비전 앱에 필요한 변경 내용이 없습니다. Workday 쪽에서 Workday (Workday 통합 시스템 사용자) 계정에 자습서 섹션인 [비즈니스 프로세스 보안 정책 권한 구성](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions)에 설명 된 대로 Change_Work_Contact 비즈니스 프로세스를 호출할 수 있는 권한이 있는지 확인 합니다. 

새 API 버전 지원이 반영 되도록 [자습서 가이드](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) 를 업데이트 했습니다.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>기본 액세스 역할이 있는 사용자는 현재 프로 비전 범위에 있습니다.

**유형:** 변경된 기능

**서비스 범주:** 앱 프로비전

**제품 기능:** Id 수명 주기 관리

지금까지 기본 액세스 역할이 있는 사용자는 프로 비전 범위를 벗어났습니다. 이 역할을 가진 사용자가 프로 비전 범위에 있는 것을 고객에 게 제공할 수 있는 피드백을 받았습니다. 2020 년 4 월 16 일부 터 모든 새 프로 비전 구성을 사용 하면 기본 액세스 역할이 있는 사용자를 프로 비전 할 수 있습니다. 점진적으로 기존 프로 비전 구성의 동작을 변경 하 여이 역할을 사용 하는 사용자 프로 비전을 지원 합니다. [자세한 정보](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>업데이트 된 프로 비전 UI

**유형:** 변경된 기능

**서비스 범주:** 앱 프로비전

**제품 기능:** Id 수명 주기 관리

프로 비전 환경을 새로 고쳐 더 집중 된 관리 보기를 만들었습니다. 이미 구성 된 엔터프라이즈 응용 프로그램의 프로 비전 블레이드로 이동 하면 프로 비전 시작, 중지 및 다시 시작 등의 작업을 프로 비전 하 고 관리 하는 과정을 쉽게 모니터링할 수 있습니다. [자세한 정보](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>동적 그룹 규칙 유효성 검사는 이제 공개 미리 보기로 제공 됩니다.

**유형:** 변경된 기능

**서비스 범주:** 그룹 관리

**제품 기능:** 협업

이제 Azure Active Directory (Azure AD)에서 동적 그룹 규칙의 유효성을 검사 하는 방법을 제공 합니다. **규칙 유효성 검사** 탭에서 샘플 그룹 멤버에 대해 동적 규칙의 유효성을 검사 하 여 규칙이 예상 대로 작동 하는지 확인할 수 있습니다. 관리자는 동적 그룹 규칙을 만들거나 업데이트할 때 사용자 또는 장치가 그룹의 구성원 인지 여부를 확인 하려고 합니다. 이를 통해 사용자 또는 장치가 규칙 기준을 충족 하는지 여부를 평가 하 고, 멤버 자격이 필요 하지 않을 때 문제 해결을 지원 합니다.

자세한 내용은 [동적 그룹 멤버 자격 규칙 유효성 검사 (미리 보기)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation)를 참조 하세요.

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Id 보안 점수-보안 기본값 및 MFA 개선 작업 업데이트

**유형:** 변경된 기능

**서비스 범주:** 해당 없음

**제품 기능:** Id 보안 & 보호

**AZURE AD 개선 작업의 보안 기본값 지원:** Microsoft Secure 점수는 [AZURE AD의 보안 기본값](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)을 지원 하도록 개선 작업을 업데이트 하 여 일반적인 공격에 대해 미리 구성 된 보안 설정으로 조직을 보호 하는 작업을 더 쉽게 수행할 수 있게 해줍니다. 이는 다음과 같은 개선 작업에 영향을 줍니다.

- 모든 사용자가 보안 액세스를 위해 multi-factor authentication을 완료할 수 있는지 확인
- 관리 역할에 대 한 MFA 필요
- 정책을 사용 하 여 레거시 인증 차단
 
**MFA 개선 작업 업데이트:** 비즈니스에 사용 되는 정책을 적용 하는 동시에 비즈니스의 보안을 보장 하기 위한 비즈니스 요구 사항을 반영 하기 위해 Microsoft 보안 점수는 다단계 인증 중심의 세 가지 개선 작업을 제거 하 고 2를 추가 했습니다.

제거 되는 향상 작업:

- Multi-factor authentication에 대 한 모든 사용자 등록
- 모든 사용자용 MFA 필요
- Azure AD 권한 있는 역할에 대 한 MFA 필요

추가 된 개선 작업:

- 모든 사용자가 보안 액세스를 위해 multi-factor authentication을 완료할 수 있는지 확인
- 관리 역할에 대 한 MFA 필요

이러한 새로운 향상 된 작업을 수행 하려면 디렉터리에서 MFA (multi-factor authentication)에 대해 사용자 또는 관리자를 등록 하 고 조직의 요구에 맞는 올바른 정책 집합을 설정 해야 합니다. 주요 목표는 모든 사용자와 관리자가 여러 요소나 위험 기반 id 확인 프롬프트를 사용 하 여 인증할 수 있도록 하는 동시에 유연성을 유지 하는 것입니다. 이는 범위가 지정 된 결정을 적용 하는 여러 정책이 있거나, Microsoft에서 MFA에 대 한 사용자의 시도 시기를 결정 하는 보안 기본값 (3 월 16 일 기준)을 설정 하는 형태를 사용할 수 있습니다. [Microsoft 보안 점수의 새로운 기능에 대해 자세히](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new)알아보세요.

---

## <a name="march-2020"></a>2020년 3월

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>3 월에 대 한 B2B 업데이트의 관리 되지 않는 Azure Active Directory 계정 2021

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
**2021 년 3 월 31 일부 터**MICROSOFT는 B2B 공동 작업 시나리오에 대해 관리 되지 않는 Azure Active Directory (Azure AD) 계정 및 테 넌 트를 만들어 더 이상 초대 상환을 지원 하지 않습니다. 이를 위해 준비 하는 [동안 일회용 암호 인증을 전자 메일로](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)보내도록 옵트인 하는 것이 좋습니다.

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>기본 액세스 역할이 있는 사용자는 프로 비전 범위에 포함 됩니다.

**유형:** 변경 계획  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
지금까지 기본 액세스 역할이 있는 사용자는 프로 비전 범위를 벗어났습니다. 이 역할을 가진 사용자가 프로 비전 범위에 있는 것을 고객에 게 제공할 수 있는 피드백을 받았습니다. 모든 새 프로 비전 구성에서 기본 액세스 역할이 있는 사용자를 프로 비전 할 수 있도록 변경 내용을 배포 하기 위해 노력 하 고 있습니다. 점진적으로 기존 프로 비전 구성의 동작을 변경 하 여이 역할의 사용자 프로 비전을 지원 합니다. 고객 동작은 필요 하지 않습니다. 이 변경 내용이 적용 되 면 [설명서](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) 에 대 한 업데이트를 게시 합니다.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B 공동 작업은 21Vianet (Azure 중국 21Vianet) 테 넌 트에서 운영 하는 Microsoft Azure에서 사용할 수 있습니다.

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
Azure AD B2B 공동 작업 기능은 21Vianet (Azure 중국 21Vianet) 테 넌 트가 운영 하는 Microsoft Azure에서 사용할 수 있으며, Azure 중국 21Vianet 테 넌 트의 사용자가 다른 Azure 중국 21Vianet 테 넌 트의 사용자와 원활 하 게 공동 작업을 수행할 수 있도록 합니다. [AZURE AD B2B 공동 작업에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B 공동 작업 초대 전자 메일 다시 디자인

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
사용자를 디렉터리에 초대 하기 위해 Azure AD B2B 공동 작업 초대 서비스에서 보낸 [전자 메일](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) 은 초대 정보 및 사용자의 다음 단계를 명확 하 게 하기 위해 다시 디자인 됩니다.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Homerealmdiscovery.aspx 정책 변경이 감사 로그에 표시 됩니다.

**유형:** 고정  
**서비스 범주:** 감사  
**제품 기능:** 모니터링 및 보고
 
[Homerealmdiscovery.aspx 정책](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) 에 대 한 변경 내용이 감사 로그에 포함 되지 않은 버그를 수정 했습니다. 이제 정책을 변경 하는 시기와 방법 및 해당 사용자를 확인할 수 있습니다. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-3 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
3 월 2020에 앱 갤러리에 대 한 페더레이션 지원을 통해 이러한 51 새 앱을 추가 했습니다. 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One 중국](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML 앱](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [IPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), [contexxt.ai 구에](https://contexxt-sphere.com/login), [지혜가 By Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [플레어 DIGITAL Signage](https://spark-dev.pixelnebula.com/login), Logz.io [-Cloud 관찰성 for 엔지니어](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [BizzContact](https://bizzcontact.app/), [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), [COMPAS Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [netvision compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM HUB](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), [리브 A/S byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [Datadog, Zscaler](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [B2B 사용자 포털](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [리프트](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial), [WatchTeams, 별표](https://www.devfinition.com/), [기술 워크플로](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [노드](https://admin.nodeinsight.com/AADLogin.aspx)정보, [IP 플랫폼](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [INVISION](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [Pipedrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [전시 워크숍](https://app.showcaseworkshop.com/), [Greenlight Integration Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [Greenlight 규격 액세스 관리](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore Online](https://login.online.miradore.com/), [Khoros 주의](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [askTruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial) [, Smartwaiver,](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial) [Bizagi](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [insuiteX Studio for Digital Process Automation](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [sybo](https://www.insuite.jp/), [Britive](https://www.systexsoftware.com.tw/), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial) [, Kollective](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-일](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Witivio SDN](https://portal.kollective.app/login), [Playvox](https://app.witivio.com/), [한쪽](https://my.playvox.com/login), [Korn 카페 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [캠퍼스 catch 지점](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial) [, Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial), [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial) [Aster](https://demo.asterapp.io/login)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure Government 테 넌 트에서 사용할 수 있는 Azure AD B2B 공동 작업

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
이제 Azure AD B2B 공동 작업 기능을 일부 Azure Government 테 넌 트 간에 사용할 수 있습니다.  테 넌 트가 이러한 기능을 사용할 수 있는지 확인 하려면 [AZURE 미국 정부 테 넌 트에서 B2B 공동 작업을 사용할 수 있는지 어떻게 알 수 있나요?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)를 참조 하세요.

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>이제 Azure Government에서 Azure 로그에 대 한 Azure Monitor 통합을 사용할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
이제 Azure Government에서 Azure AD 로그와 Azure Monitor 통합을 사용할 수 있습니다. Azure AD 로그 (감사 및 로그인 로그)를 저장소 계정, 이벤트 허브 및 Log Analytics로 라우팅할 수 있습니다. Azure AD 시나리오에 대 한 [보고 및 모니터링에 대 한](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) [자세한 설명서](https://aka.ms/aadlogsinamd) 및 배포 계획을 확인 하세요.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure Government에서 id 보호 새로 고침

**유형:** 새로운 기능  
**서비스 범주:** Id 보호  
**제품 기능:** Id 보안 & 보호

이제 [Microsoft Azure Government 포털](https://portal.azure.us/)에서 새로 고친 [Azure AD ID 보호](https://aka.ms/IdentityProtectionDocs) 환경을 출시 했습니다. 자세한 내용은 [발표 블로그 게시물](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)을 참조 하세요.

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>재해 복구: 프로 비전 구성을 다운로드 하 고 저장 합니다.

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
Azure AD 프로 비전 서비스는 다양 한 구성 기능 집합을 제공 합니다. 고객은 나중에 참조 하거나 알려진 올바른 버전으로 롤백할 수 있도록 구성을 저장할 수 있어야 합니다. 프로 비전 구성을 JSON 파일로 다운로드 하 고 필요할 때 업로드 하는 기능을 추가 했습니다. [자세한 정보를 알아보세요](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (셀프 서비스 암호 재설정)은 이제 21Vianet에서 운영 하는 Microsoft Azure의 관리자를 위한 두 개의 게이트 (Azure 중국 21Vianet)가 필요 합니다. 

**유형:** 변경된 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** Id 보안 & 보호
 
이전에는 21Vianet (Azure 중국 21Vianet)에서 운영 하는 Microsoft Azure에서 SSPR (셀프 서비스 암호 재설정)를 사용 하 여 자신의 암호를 재설정 하는 관리자는 본인의 id를 증명 하기 위해 하나의 "게이트" (챌린지)만 필요 했습니다. 공용 및 기타 국가에서 관리자는 일반적으로 SSPR를 사용 하는 경우 두 개의 게이트를 사용 하 여 id를 증명 해야 합니다. 그러나 Azure 중국 21Vianet에서 SMS 또는 전화 통화를 지원 하지 않았기 때문에 관리자가 한 게이트 암호 재설정을 수행할 수 있습니다.

Azure 중국 21Vianet과 공용 클라우드 간에 SSPR 기능 패리티를 만들고 있습니다. 앞으로 관리자는 SSPR를 사용할 때 두 개의 게이트를 사용 해야 합니다. SMS, 전화 통화 및 인증자 앱 알림 및 코드가 지원 됩니다. [자세한 정보를 알아보세요](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>암호 길이는 256 자로 제한 됩니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증
 
Azure AD 서비스의 안정성을 보장 하기 위해 이제 사용자 암호의 길이는 256 자로 제한 됩니다. 이 보다 긴 암호를 사용 하는 사용자는 관리자에 게 문의 하거나 셀프 서비스 암호 재설정 기능을 사용 하 여 후속 로그인 시 암호를 변경 하 라는 메시지가 표시 됩니다.

이 변경 내용은 오전 10 월 13 2020 일 오전 10 시 (18:00 UTC)에 사용 하도록 설정 되었으며 오류는 AADSTS 50052, InvalidPasswordExceedsMaxLength입니다. 자세한 내용은 [주요 변경 공지](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) 를 참조 하세요.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>이제 Azure Portal를 통해 Azure AD 로그인 로그를 모든 사용 가능한 테 넌 트에 사용할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
지금부터 무료 테 넌 트가 있는 고객은 최대 7 일 동안 [Azure Portal에서 AZURE AD 로그인 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) 에 액세스할 수 있습니다. 이전에는 Azure Active Directory Premium 라이선스를 사용 하는 고객 에게만 로그인 로그를 사용할 수 있었습니다. 이러한 변경을 통해 모든 테 넌 트는 포털을 통해 이러한 로그에 액세스할 수 있습니다.

> [!NOTE]
> 고객은 Microsoft Graph API 및 Azure Monitor를 통해 로그인 로그에 액세스 하기 위한 프리미엄 라이선스 (Azure Active Directory Premium P1 또는 P2)도 필요 합니다.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>그룹의 디렉터리 전체 그룹 옵션 사용 중단 Azure Portal의 일반 설정

**유형:** 사용되지 않음  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업

고객이 자신의 요구에 가장 적합 한 디렉터리 전체 그룹을 만들 수 있는 보다 유연한 방법을 제공 하기 위해 Azure Portal의 **그룹** > **일반** 설정에서 [동적 그룹 설명서](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)에 대 한 링크를 사용 하 여 **디렉터리 전체 그룹** 옵션을 대체 했습니다. 관리자가 게스트 사용자를 포함 하거나 제외 하는 모든 사용자 그룹을 만들 수 있도록 더 많은 지침을 포함 하도록 설명서를 개선 했습니다.

---

## <a name="february-2020"></a>2020년 2월

### <a name="upcoming-changes-to-custom-controls"></a>사용자 지정 컨트롤의 예정 된 변경 내용

**유형:** 변경 계획  
**서비스 범주:** MFA  
**제품 기능:** Id 보안 & 보호
 
현재 사용자 지정 컨트롤 미리 보기를 파트너 제공 인증 기능이 Azure Active Directory 관리자 및 최종 사용자 환경에서 원활 하 게 작동 하도록 허용 하는 방법으로 바꿀 계획입니다. 현재 파트너 MFA 솔루션에는 다음과 같은 제한 사항이 있습니다. 암호를 입력 한 후에만 작동 합니다. 다른 주요 시나리오에서의 단계별 인증에 대 한 MFA 역할을 하지 않습니다. 최종 사용자 또는 관리 자격 증명 관리 기능과 통합 되지 않습니다. 새 구현에서는 파트너 제공 인증 요인이 등록, 사용, MFA 클레임, 인증 단계 인증, 보고, 로깅 등의 주요 시나리오에 대 한 기본 제공 요소와 함께 작동할 수 있습니다. 

사용자 지정 컨트롤은 일반 공급에 도달할 때까지 새 디자인과 함께 미리 보기에서 계속 지원 됩니다. 이 시점에서 고객에 게 새 설계로 마이그레이션할 수 있는 시간을 제공 합니다. 현재 방법의 제한 사항 때문에 새로운 디자인을 사용할 수 있을 때까지 새 공급자를 등록 하지 않습니다. Microsoft는 고객과 공급자와 긴밀 하 게 협력 하 고 있으며,이에 따라 일정에 따라 일정을 전달 합니다. [자세한 정보를 알아보세요](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Id 보안 점수-MFA 개선 작업 업데이트

**유형:** 변경 계획  
**서비스 범주:** MFA  
**제품 기능:** Id 보안 & 보호
 
비즈니스에 사용 되는 정책을 적용 하는 동시에 비즈니스의 보안을 보장 하기 위한 비즈니스 요구 사항을 반영 하기 위해 Microsoft 보안 점수는 MFA (multi-factor authentication) 중심의 세 가지 개선 작업을 제거 하 고 두 가지를 추가 하는 것입니다.

다음 개선 작업이 제거 됩니다.

- MFA에 대 한 모든 사용자 등록
- 모든 사용자용 MFA 필요
- Azure AD 권한 있는 역할에 대 한 MFA 필요

다음 개선 작업이 추가 됩니다.

- 모든 사용자가 보안 액세스를 위해 MFA를 완료할 수 있는지 확인
- 관리 역할에 대 한 MFA 필요

이러한 새로운 향상 된 작업을 수행 하려면 디렉터리에서 MFA 용 사용자 또는 관리자를 등록 하 고 조직의 요구에 맞는 올바른 정책 집합을 설정 해야 합니다. 주요 목표는 모든 사용자와 관리자가 여러 요소나 위험 기반 id 확인 프롬프트를 사용 하 여 인증할 수 있도록 하는 동시에 유연성을 유지 하는 것입니다. Microsoft에서 사용자에 게 MFA를 시도 하는 시기를 결정 하거나 범위가 지정 된 결정을 적용 하는 여러 정책을 보유 하는 경우를 결정 하는 보안 기본값 설정의 형식을 사용할 수 있습니다. 이러한 향상 작업 업데이트의 일환으로 기준선 보호 정책은 더 이상 점수 계산에 포함 되지 않습니다. [Microsoft 보안 점수에서 제공 되는 사항에 대해 자세히](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide)알아보세요.

---

### <a name="azure-ad-domain-services-sku-selection"></a>SKU 선택 Azure AD Domain Services

**유형:** 새로운 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services
 
고객이 해당 인스턴스에 대 한 성능 수준을 더 유연 하 게 선택 하는 것을 Azure AD Domain Services 하는 데 필요한 피드백을 받았습니다. 2020 년 2 월 1 일부 터 동적 모델 (Azure AD가 개체 수를 기반으로 성능 및 가격 책정 계층을 결정)을 자체 선택 모델에 대해 전환 했습니다. 이제 고객은 환경에 맞는 성능 계층을 선택할 수 있습니다. 이러한 변경으로 인해 리소스 포리스트와 같은 새로운 시나리오와 매일 백업 같은 프리미엄 기능을 사용할 수도 있습니다. 이제 모든 Sku에 대 한 개체 수에 제한이 없지만 각 계층에 대 한 개체 수 제안을 계속 제공 합니다.

**즉각적인 고객 조치가 필요 하지 않습니다.** 기존 고객의 경우 2020 년 2 월 1 일에 사용 되 던 동적 계층은 새 기본 계층을 결정 합니다. 이러한 변경의 결과로 가격이 나 성능에 영향을 주지 않습니다. 앞으로 Azure AD DS 고객은 디렉터리 크기 및 워크 로드 특성이 변경 됨에 따라 성능 요구 사항을 평가 해야 합니다. 서비스 계층 간을 전환 하면 가동 중지 시간이 발생 하지 않으며, 해당 디렉터리의 증가에 따라 더 이상 고객이 새 계층으로 이동 하지 않습니다. 또한 가격은 늘어나지만 새로운 가격은 현재 청구 모델에 맞게 조정 됩니다. 자세한 내용은 [Azure AD DS sku 설명서](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) 및 [Azure AD Domain Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory-ds/)를 참조 하세요.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-2 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2 월 2020에 앱 갤러리에 대 한 페더레이션 지원과 함께 이러한 31 개의 새 앱을 추가 했습니다. 

[IamIP Patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial)ABa [-온라인 포털](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [bic 클라우드 디자인](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Beekeeper azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial)의 경우, [Barracuda 메일 보안 서비스](https://ess.barracudanetworks.com/sso/azure), [보고](https://myaba.co.uk/client-access/signin/auth/msad), [azure에 대 한](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial) [환경 클라우드](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial) [Korn 한쪽 평가](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [eab 탐색](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [새 유물 (제한 된 릴리스)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [티켓 관리자](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [팀을 위한 템플릿 선택기](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), [의료 지원 시스템](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), Firmex [vdr](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [교사와 학교](https://www.thinglink.com/), [coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [ThingLink](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [wedo](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial) [, NearpodApp, InvitePeople](https://invitepeople.com/login) [Desk-문서 Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD 응용 프로그램 갤러리의 새로운 프로 비전 커넥터-2 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
이제 새로 통합 되는 앱에 대 한 사용자 계정을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

자동 사용자 계정 프로 비전을 사용 하 여 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 Azure AD를 사용 하 여 [SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조 하세요.

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>하이브리드 환경에서 FIDO2 보안 키에 대 한 Azure AD 지원

**유형:** 새로운 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증
 
하이브리드 환경에서 FIDO2 보안 키에 대 한 Azure AD 지원의 공개 미리 보기를 발표 하 고 있습니다. 사용자는 이제 FIDO2 보안 키를 사용 하 여 하이브리드 Azure AD에 가입 된 Windows 10 장치에 로그인 하 고 온-프레미스 및 클라우드 리소스에 원활 하 게 로그온 할 수 있습니다. 하이브리드 환경에 대 한 지원은 Azure AD 조인 장치에서 FIDO2 지원을 위한 공개 미리 보기를 처음 시작 했기 때문에 암호 없는 고객 으로부터 가장 많이 요청 되는 기능입니다. 생체 인식 및 공개/개인 키 암호화와 같은 고급 기술을 사용 하 여 암호 없는 인증을 사용 하면 안전 하 게 편리 하 고 편리 하 게 사용할 수가 제공 됩니다. 이 공개 미리 보기에서는 이제 FIDO2 보안 키와 같은 최신 인증을 사용 하 여 기존 Active Directory 리소스에 액세스할 수 있습니다. 자세한 내용은 [온-프레미스 리소스](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)에 대 한 SSO로 이동 합니다. 

시작 하려면 단계별 지침은 [테 넌 트에 대해 FIDO2 보안 키 사용](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) 을 참조 하세요. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>새 내 계정 환경이 이제 일반 공급 됩니다.

**유형:** 변경된 기능  
**서비스 범주:** 내 프로필/계정  
**제품 기능:** 최종 사용자 환경
 
모든 최종 사용자 계정 관리 요구 사항에 대 한 한 가지 중지 쇼핑 계정이 이제 일반 공급 됩니다. 최종 사용자는 URL 또는 새 내 앱 환경의 헤더를 통해이 새 사이트에 액세스할 수 있습니다. [내 계정 포털 개요](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)에서 새로운 환경을 제공 하는 모든 셀프 서비스 기능에 대해 자세히 알아보세요.

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Myaccount.microsoft.com로 업데이트 하는 내 계정 사이트 URL

**유형:** 변경된 기능  
**서비스 범주:** 내 프로필/계정  
**제품 기능:** 최종 사용자 환경
 
새 내 계정 최종 사용자 환경에서는 다음 달에 해당 URL을 `https://myaccount.microsoft.com` 로 업데이트 합니다. [내 계정 포털 도움말](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)에서 최종 사용자에 게 제공 하는 모든 계정 셀프 서비스 기능 및 환경에 대 한 자세한 정보를 알아보세요.

---
 
## <a name="january-2020"></a>2020년 1월
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>새 내 앱 포털이 이제 일반 공급 됩니다.

**유형:** 변경 계획  
**서비스 범주:** 내 앱  
**제품 기능:** 최종 사용자 환경
 
이제 일반 공급 되는 새 내 앱 포털로 조직을 업그레이드 하세요. [내 앱 포털에서 컬렉션 만들기](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)의 새 포털 및 컬렉션에 대 한 자세한 내용을 확인 하세요.

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD의 작업 영역 이름이 컬렉션으로 바뀌었습니다.

**유형:** 변경된 기능  
**서비스 범주:** 내 앱   
**제품 기능:** 최종 사용자 환경
 
관리자가 사용자의 앱을 구성 하도록 구성할 수 있는 작업 영역은 이제 컬렉션 이라고 합니다. [내 앱 포털에서 컬렉션 만들기](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)에서 구성 하는 방법에 대 한 자세한 정보를 찾습니다.

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>사용자 지정 정책을 사용 하 여 전화 등록 및 로그인 Azure AD B2C (공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
전화 번호 등록 및 로그인을 통해 개발자와 기업은 고객이 SMS를 통해 사용자의 전화 번호로 전송 된 일회용 암호를 사용 하 여 등록 하 고 로그인 할 수 있습니다. 또한이 기능을 통해 고객은 휴대폰에 대 한 액세스 권한을 상실 한 경우 전화 번호를 변경할 수 있습니다. 사용자 지정 정책의 강력한 기능을 통해 전화 등록 및 로그인을 통해 개발자와 기업은 페이지 사용자 지정을 통해 브랜드를 통신할 수 있습니다. [Azure AD B2C에서 사용자 지정 정책을 사용 하 여 전화 등록 및 로그인을 설정](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)하는 방법을 알아봅니다.
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD 응용 프로그램 갤러리의 새로운 프로 비전 커넥터-1 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
이제 새로 통합 되는 앱에 대 한 사용자 계정을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

자동 사용자 계정 프로 비전을 사용 하 여 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 Azure AD를 사용 하 여 [SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조 하세요.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-2020 년 1 월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2020 년 1 월에 앱 갤러리에 대 한 페더레이션 지원을 통해 이러한 33 새 앱을 추가 했습니다. 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [Datacamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SMARTWORK](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN-Oracle E-비즈니스 제품군-EBS에 대 한 Azure AD SSO 게이트웨이 PeopleSoft 및 JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [호스 티 드 MYCIRQA SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [Microsoft 팀을 위한 smartwork](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial) [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), Maxient [upwork Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login) [,](https://app-staging.pingview.io/)Sandwai [ PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial) [, EZRentOut, AssetSonar](https://app.sandwai.com/), Akari, [,,](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [,](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [Virtual Assistant](https://akari.io/akari-virtual-assistant/)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="two-new-identity-protection-detections"></a>두 개의 새로운 Id 보호 검색

**유형:** 새로운 기능  
**서비스 범주:** Id 보호  
**제품 기능:** Id 보안 & 보호
 
Id 보호에 두 가지 로그인 연결 된 검색 유형을 추가 했습니다. 의심 스러운 수신함 조작 규칙 및 불가능 한 이동입니다. 이러한 오프 라인 검색은 MCAS (Microsoft Cloud App Security)에 의해 검색 되며 Id 보호의 사용자 및 로그인 위험에 영향을 줍니다. 이러한 검색에 대 한 자세한 내용은 [로그인 위험 유형](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)을 참조 하세요.
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>주요 변경: URI 조각은 로그인 리디렉션을 통해 전달 되지 않습니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증
 
2020,에서 시작 하 여 사용자를 로그인 하기 위해 login.microsoftonline.com에 요청을 보내면 서비스는 빈 조각을 요청에 추가 합니다.  이렇게 하면 브라우저에서 요청의 기존 조각을 모두 초기화 하 여 공격에 대 한 클래스를 방지할 수 있습니다. 이 동작에 대 한 종속성이 있는 응용 프로그램이 없습니다. 자세한 내용은 Microsoft id 플랫폼 설명서의 [주요 변경 내용](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) 을 참조 하세요.

---

## <a name="december-2019"></a>2019년 12월

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP SuccessFactors 프로 비전을 Azure AD 및 온-프레미스 AD에 통합 (공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리

이제 Azure AD에서 SAP SuccessFactors를 신뢰할 수 있는 id 원본으로 통합할 수 있습니다. 이러한 통합을 통해 새 채용 또는 종료와 같은 HR 기반 이벤트를 사용 하 여 Azure AD 계정 프로 비전을 제어 하는 등 종단 간 id 수명 주기를 자동화할 수 있습니다.

Azure AD에 대 한 SAP SuccessFactors 인바운드 프로 비전을 설정 하는 방법에 대 한 자세한 내용은 [Sap SuccessFactors 자동 프로 비전 구성](https://aka.ms/SAPSuccessFactorsInboundTutorial) 자습서를 참조 하세요.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Azure AD B2C에서 사용자 지정 된 전자 메일 지원 (공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 사용자가 앱을 사용 하기 위해 등록할 때 Azure AD B2C를 사용 하 여 사용자 지정 된 전자 메일을 만들 수 있습니다. DisplayControls (현재 미리 보기 상태)와 타사 전자 메일 공급자 (예: [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)또는 사용자 지정 REST API)를 사용 하 여 사용자 고유의 전자 메일 템플릿, 주소 및 제목 텍스트 **를 사용할** 수 있을 뿐만 아니라 지역화 및 사용자 지정 OTP (일회용 암호) 설정을 지원할 수 있습니다.

자세한 내용은 [Azure Active Directory B2C에서 사용자 지정 전자 메일 확인](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)을 참조 하세요.

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>보안 기본값으로 기본 정책 바꾸기

**유형:** 변경된 기능  
**서비스 범주:** 기타  
**제품 기능:** ID 보안 및 보호

인증을 위한 기본 보안 모델의 일부로 모든 테 넌 트에서 기존 기준 보호 정책을 제거 하 고 있습니다. 이 제거는 2 월 말에 완료 될 대상입니다. 이러한 기준 보호 정책에 대 한 대체는 보안 기본값입니다. 기준 보호 정책을 사용한 경우 새 보안 기본값 정책 또는 조건부 액세스로 이동 하도록 계획 해야 합니다. 이러한 정책을 사용 하지 않은 경우 수행할 작업은 없습니다.

새 보안 기본값에 대 한 자세한 내용은 [보안 기본값 이란?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 을 참조 하세요. 조건부 액세스 정책에 대 한 자세한 내용은 [일반적인 조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)을 참조 하세요.

---

## <a name="november-2019"></a>2019년 11월

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>SameSite 특성 및 크롬 80에 대 한 지원

**유형:** 변경 계획  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증

쿠키에 대 한 기본 보안 모델의 일부로 Chrome 80 브라우저는 `SameSite` 특성 없이 쿠키를 처리 하는 방식을 변경 합니다. `SameSite` 특성을 지정 하지 않는 모든 쿠키는로 `SameSite=Lax`설정 된 것 처럼 처리 되며이로 인해 Chrome에서 앱이 종속 될 수 있는 특정 도메인 간 쿠키 공유 시나리오를 차단 합니다. 이전 Chrome 동작을 유지 하려면 `SameSite=None` 특성을 사용 하 고 추가 `Secure` 특성을 추가 하 여 HTTPS 연결을 통해서만 사이트 간 쿠키에 액세스할 수 있습니다. Chrome은 2020 년 2 월 4 일에이 변경을 완료 하도록 예약 되어 있습니다.

모든 개발자가이 지침을 사용 하 여 앱을 테스트 하는 것이 좋습니다.

- **보안 쿠키 사용** 설정의 기본값을 **예**로 설정 합니다.

- **SameSite** 특성의 기본값을 **None**으로 설정 합니다.

- 보안의 추가 `SameSite` 특성을 **Secure**추가 합니다.

자세한 내용은 [향후 SameSite 쿠키 변경 ASP.NET 및 ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) 및 [Chrome 버전 79 이상에서 고객 websites 및 Microsoft 제품 및 서비스에 대 한 잠재적인 중단](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)을 참조 하세요.

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Microsoft Identity Manager (MIM) 2016 서비스 팩 2 (SP2) 용 새 핫픽스

**유형:** 고정  
**서비스 범주:** Microsoft Identity Manager  
**제품 기능:** Id 수명 주기 관리

핫픽스 롤업 패키지 (build 4.6.34.0)는 Microsoft Identity Manager (MIM) 2016 서비스 팩 2 (SP2)에서 사용할 수 있습니다. 이 롤업 패키지는 문제를 해결 하 고 "이 업데이트에 추가 된 문제 해결 및 개선 사항" 섹션에 설명 된 향상 된 기능을 추가 합니다.

핫픽스 패키지를 다운로드 하 고 다운로드 하는 방법에 대 한 자세한 내용은 [Microsoft Identity Manager 2016 서비스 팩 2 (build 4.6.34.0) 업데이트 롤업을 사용 가능](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)을 참조 하세요.

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>앱을 Azure AD로 마이그레이션하는 데 도움이 되는 새로운 AD FS 앱 활동 보고서 (공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

Azure Portal에서 새 Active Directory Federation Services (AD FS) 앱 활동 보고서를 사용 하 여 Azure AD로 마이그레이션할 수 있는 앱을 식별 합니다. 이 보고서는 Azure AD와의 호환성을 위해 모든 AD FS 앱을 평가 하 고, 문제를 확인 하 고, 개별 앱을 마이그레이션하기 위한 준비에 대 한 지침을 제공 합니다.

자세한 내용은 [AD FS 응용 프로그램 작업 보고서를 사용 하 여 응용 프로그램을 AZURE AD로 마이그레이션](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)을 참조 하세요.

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>사용자가 관리자 동의를 요청 하는 새 워크플로 (공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 액세스 제어

새 관리자 동의 워크플로를 통해 관리자는 관리자 승인이 필요한 앱에 대 한 액세스 권한을 부여할 수 있습니다. 사용자가 앱에 액세스 하려고 하지만 동의를 제공할 수 없는 경우 이제 관리자 승인에 대 한 요청을 보낼 수 있습니다. 요청은 전자 메일을 통해 전송 되 고, Azure Portal에서 액세스할 수 있는 큐에 저장 되며 검토자로 지정 된 모든 관리자에 게 적용 됩니다. 검토자가 보류 중인 요청에 대해 작업을 수행 하면 요청 하는 사용자에 게 작업에 대 한 알림이 표시 됩니다.

자세한 내용은 [관리자 동의 워크플로 구성 (미리 보기)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)을 참조 하세요.

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>선택적인 클레임을 관리 하기 위한 새로운 Azure AD 앱 등록 토큰 구성 환경 (공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 개발자 환경

이제 Azure Portal의 새 **Azure AD 앱 등록 토큰 구성** 블레이드에서 앱 개발자에 게 앱에 대 한 선택적 클레임의 동적 목록을 표시 합니다. 이 새로운 환경을 통해 Azure AD 앱 마이그레이션을 간소화 하 고 선택적 클레임 잘못 된 구성을 최소화할 수 있습니다.

자세한 내용은 [AZURE AD 앱에 선택적 클레임 제공](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)을 참조 하세요.

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD 자격 관리의 새로운 2 단계 승인 워크플로 (공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 자격 관리

액세스 패키지에 대 한 사용자의 요청을 승인 하는 두 명의 승인자를 요구할 수 있는 새로운 2 단계 승인 워크플로가 도입 되었습니다. 예를 들어 요청 하는 사용자의 관리자가 먼저 승인 하도록 설정한 다음 리소스 소유자가 승인 하도록 요구할 수도 있습니다. 승인자 중 하나가 승인 되지 않으면 액세스가 허용 되지 않습니다.

자세한 내용은 [AZURE AD 자격 관리에서 액세스 패키지에 대 한 요청 및 승인 설정 변경](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)을 참조 하세요.

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>새 작업 영역과 함께 내 앱 페이지에 대 한 업데이트 (공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** 타사 통합

이제 조직의 사용자가 새로 고쳐진 내 앱 환경을 보고 액세스 하는 방법을 사용자 지정할 수 있습니다. 이 새로운 환경에는 사용자가 앱을 보다 쉽게 찾고 구성할 수 있도록 하는 새로운 작업 영역 기능도 포함 되어 있습니다.

새 내 앱 환경 및 작업 영역 만들기에 대 한 자세한 내용은 [My apps 포털에서 작업 영역 만들기](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)를 참조 하세요.

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Azure AD B2B 공동 작업을 위한 Google 소셜 ID 지원 (일반 공급)

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** 사용자 인증

Azure AD에서 Google 소셜 Id (Gmail 계정)를 사용 하는 새로운 지원 기능을 통해 사용자와 파트너에 게 더 간단 하 게 공동 작업을 수행할 수 있습니다. 파트너가 새 Microsoft 특정 계정을 만들고 관리 하는 데 더 이상 필요 하지 않습니다. Microsoft 팀은 이제 모든 클라이언트와 공통 및 테 넌 트 관련 인증 끝점에서 Google 사용자를 완전히 지원 합니다.

자세한 내용은 [B2B 게스트 사용자에 대 한 id 공급자로 Google 추가](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)를 참조 하세요.

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>조건부 액세스 및 Single Sign-on을 위한 Microsoft Edge 모바일 지원 (일반 공급)

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호

IOS 및 Android의 Microsoft Edge 용 azure AD는 이제 Azure AD Single Sign-on 및 조건부 액세스를 지원 합니다.

- **SSO (Microsoft Edge Single Sign-On):** 이제 모든 Azure AD 연결 앱에 대 한 기본 클라이언트 (예: Microsoft Outlook 및 Microsoft Edge)에서 Single sign-on을 사용할 수 있습니다.

- **Microsoft Edge 조건부 액세스:** 응용 프로그램 기반 조건부 액세스 정책을 통해 사용자는 Microsoft Edge와 같은 Microsoft Intune 보호 된 브라우저를 사용 해야 합니다.

Microsoft Edge를 사용 하는 조건부 액세스 및 SSO에 대 한 자세한 내용은 [Microsoft Edge Mobile Support For 조건부 액세스 및 Single sign-on을 사용 하 여 일반적으로 사용 가능](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) 블로그 게시물을 참조 하세요. [앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) 또는 [장치 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)를 사용 하 여 클라이언트 앱을 설정 하는 방법에 대 한 자세한 내용은 [Microsoft Intune 정책으로 보호 된 브라우저를 사용 하 여 웹 액세스 관리](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)를 참조 하세요.

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD 자격 관리 (일반 공급)

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 자격 관리

Azure AD 자격 관리는 조직에서 id 및 액세스 수명 주기를 대규모로 관리할 수 있도록 하는 새로운 id 거 버 넌 스 기능입니다. 이 새로운 기능을 통해 그룹, 앱 및 SharePoint Online 사이트에서 액세스 요청 워크플로, 액세스 할당, 검토 및 만료를 자동화할 수 있습니다.

Azure AD 자격 관리를 통해 직원 및 해당 리소스에 액세스 해야 하는 조직 외부 사용자에 대 한 액세스를 보다 효율적으로 관리할 수 있습니다.

자세한 내용은 [AZURE AD 자격 관리 란?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements) 을 참조 하세요.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>새로 지원 되는 SaaS 앱에 대 한 사용자 계정 프로 비전 자동화

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합  

이제 새로 통합 되는 앱에 대 한 사용자 계정을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [officespace software Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

자동 사용자 계정 프로 비전을 사용 하 여 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 Azure AD를 사용 하 여 [SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-11 월 2019

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

11 월 2019에 앱 갤러리에 대 한 페더레이션 지원을 포함 하는 새로운 21 개 앱을 추가 했습니다.

어 [표](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [멤버에 대 한 파랑 액세스 (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitdea](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [reslife 포털](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal sso (Single Sign On)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [beedle](https://teams-web.beedle.co/#/), [visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [onedesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) [qmarkets 아이디어 & 혁신 관리](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [net](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial) [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial) [Jisc Student Voter Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial) [e4enable](https://portal.e4enable.com/)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>새롭고 향상 된 Azure AD 응용 프로그램 갤러리

**유형:** 변경된 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

Azure Active Directory 테 넌 트에서 프로 비전, Openid connect 연결 및 SAML을 지 원하는 사전 통합 된 앱을 보다 쉽게 찾을 수 있도록 Azure AD 응용 프로그램 갤러리를 업데이트 했습니다.

자세한 내용은 [Azure Active Directory 테 넌 트에 응용 프로그램 추가](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)를 참조 하세요.

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>앱 역할 정의 길이 제한을 120에서 240 자로 늘렸습니다.

**유형:** 변경된 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

고객 으로부터 일부 앱 및 서비스의 앱 역할 정의 값에 대 한 길이 제한이 120 자에서 너무 짧습니다. 이에 대 한 응답으로 역할 값 정의의 최대 길이를 240 자로 늘립니다.

응용 프로그램 관련 역할 정의를 사용 하는 방법에 대 한 자세한 내용은 [응용 프로그램에 앱 역할 추가 및 토큰에서 수신](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)을 참조 하세요.

---
