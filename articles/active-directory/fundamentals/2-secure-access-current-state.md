---
title: Azure Active Directory에서 외부 공동 작업의 현재 상태를 검색 합니다.
description: 공동 작업의 현재 상태를 검색 하는 방법을 알아봅니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7557985b23991f1a53d45f6f2d2283500c0d73f3
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222414"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>조직에서 외부 공동 작업의 현재 상태를 검색 합니다. 

외부 공동 작업의 현재 상태를 검색 하기 전에 [원하는 보안](1-secure-access-posture.md)상태를 결정 해야 합니다. 중앙 집중화 된 제어 및 위임 된 제어에 대 한 조직의 요구와 관련 된 거 버 넌 스, 규정 및 규정 준수 목표를 고려 합니다. 

조직의 개인은 이미 다른 조직의 사용자와 공동 작업을 할 수 있습니다. 공동 작업은 Microsoft 365, 전자 메일을 보내거나 외부 사용자와 리소스를 공유 하는 등의 생산성 응용 프로그램의 기능을 통해 수행할 수 있습니다. 거 버 넌 스 계획의 핵심 요소은 검색 됨에 따라 형성 됩니다. 
*   외부 공동 작업을 시작 하는 사용자입니다.
*   공동 작업 하는 외부 사용자 및 조직
*   외부 사용자에 게 부여 되는 액세스입니다.


## <a name="users-initiating-external-collaboration"></a>외부 공동 작업을 시작 하는 사용자

외부 공동 작업을 시작 하는 사용자는 외부 공동 작업과 관련 하 여 가장 적합 한 응용 프로그램을 이해 하 고 액세스를 종료 해야 합니다. 이러한 사용자를 이해 하면 외부 사용자를 초대 하 고, 액세스 패키지를 만들고, 액세스 검토를 완료할 수 있는 권한을 위임 받아야 하는 사람을 결정 하는 데 도움이 됩니다.

현재 공동 작업 중인 사용자를 찾으려면 [공유 및 액세스 요청 작업에 대 한 Microsoft 365 감사 로그](https://docs.microsoft.com/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance?view=o365-worldwide#sharing-and-access-request-activities)를 검토 하세요. Azure AD 감사 로그를 검토 하 여 B2B 사용자를 디렉터리에 [초대한 사용자에 대 한 세부 정보를](../external-identities/auditing-and-reporting.md) 확인할 수도 있습니다.

## <a name="find-current-collaboration-partners"></a>현재 공동 작업 파트너 찾기

외부 사용자는 파트너 관리 자격 증명을 사용 하는 [AZURE AD B2B 사용자](../external-identities/what-is-b2b.md) (권장) 또는 로컬로 프로 비전 된 자격 증명을 사용 하는 외부 사용자 일 수 있습니다. 이러한 사용자는 일반적으로 (항상 그렇지는 않음) 게스트의 UserType 표시 됩니다. [MICROSOFT GRAPH API](https://docs.microsoft.com/graph/api/user-list?view=graph-rest-1.0&tabs=http), [PowerShell](https://docs.microsoft.com/graph/api/user-list?view=graph-rest-1.0&tabs=http)또는 [Azure Portal](../enterprise-users/users-bulk-download.md)를 통해 게스트 사용자를 열거할 수 있습니다.

### <a name="use-email-domains-and-companyname-property"></a>메일 도메인 및 companyName 속성 사용

외부 조직은 외부 사용자 전자 메일 주소의 도메인 이름으로 확인할 수 있습니다. Google과 같은 소비자 id 공급자가 지원 되는 경우에는이 방법을 사용할 수 없습니다. 이 경우 companyName 특성을 작성 하 여 사용자의 외부 조직을 명확 하 게 식별 하는 것이 좋습니다.

### <a name="use-allow-or-deny-lists"></a>허용 또는 거부 목록 사용

조직에서 특정 조직 에서만 공동 작업을 허용할지 여부를 고려 합니다. 또는 조직에서 특정 조직과의 공동 작업을 차단 하려는 경우를 고려 합니다.  테 넌 트 수준에는 소스 (예: 팀, SharePoint 및 Azure Portal)에 관계 없이 전체 B2B 초대 및 횟수가을 제어 하는 데 사용할 수 있는 [허용 또는 거부 목록이](../external-identities/allow-deny-list.md)있습니다.
권한 관리를 사용 하는 경우 아래와 같이 연결 된 특정 조직 설정을 사용 하 여 파트너의 하위 집합에 대 한 액세스 패키지 범위를 지정할 수도 있습니다.


![새 액세스 패키지를 만들 때 거부 목록 허용의 스크린샷](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>외부 사용자에 게 부여 되는 액세스 권한 찾기

외부 사용자 및 조직에 대 한 인벤토리가 있으면 Microsoft Graph API를 사용 하 여 이러한 사용자에 게 부여 된 액세스를 확인 하 고 azure ad에서 Azure AD [그룹 멤버 자격](https://docs.microsoft.com/graph/api/resources/groups-overview?view=graph-rest-1.0) 또는 [직접 응용 프로그램 할당](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0) 을 확인할 수 있습니다.


### <a name="enumerate-application-specific-permissions"></a>응용 프로그램별 권한 열거

응용 프로그램별 권한 열거를 수행할 수도 있습니다. 예를 들어 [이 스크립트](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64)를 사용 하 여 SharePoint Online에 대 한 권한 보고서를 프로그래밍 방식으로 생성할 수 있습니다.

특히 모든 비즈니스에 중요 한 비즈니스에 중요 한 앱에 대 한 액세스를 조사 하므로 모든 외부 액세스를 완벽 하 게 파악할 수 있습니다.

### <a name="detect-ad-hoc-sharing"></a>임시 공유 검색
전자 메일 및 네트워크 요금제를 사용 하는 경우 전자 메일을 통해 또는 권한이 없는 SaaS (software as a service) 앱을 통해 콘텐츠를 조사할 수 있습니다. [Microsoft 365 데이터 손실 보호](https://docs.microsoft.com/microsoft-365/compliance/data-loss-prevention-policies?view=o365-worldwide) 를 사용 하면 Microsoft 365 인프라에서 중요 한 정보를 실수로 공유 하는 것을 식별, 방지 및 모니터링할 수 있습니다. [Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) 사용 하 여 사용자 환경에서 권한이 없는 SaaS 앱 사용을 식별할 수 있습니다.

## <a name="next-steps"></a>다음 단계

리소스에 대 한 외부 액세스 보안에 대 한 다음 문서를 참조 하세요. 이러한 작업은 나열 된 순서 대로 수행 하는 것이 좋습니다.

1. [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태를 검색](2-secure-access-current-state.md) 합니다.

3. [거 버 넌 스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용 하 여 안전 하 게 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용 하 여 안전 하 게 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용 하 여 안전 하 게 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft 팀, OneDrive 및 SharePoint에 대 한 액세스 보호](9-secure-access-teams-sharepoint.md)
