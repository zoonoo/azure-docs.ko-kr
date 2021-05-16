---
title: Azure Active Directory에서 외부 협업의 현재 상태 파악
description: 협업의 현재 상태를 파악하는 방법을 알아봅니다.
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
ms.openlocfilehash: 30858e9978f7e8857c5f8a2dcdfd7455f6e97b60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553428"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>조직에서 외부 협업의 현재 상태 파악 

외부 협업의 현재 상태를 파악하기 전에 [원하는 보안 상태를 결정](1-secure-access-posture.md)해야 합니다. 중앙 집중화된 제어 및 위임된 제어에 대한 조직의 요구와 관련된 거버넌스, 규제 및 규정 준수 목표를 고려합니다. 

조직의 개인은 이미 다른 조직의 사용자와 협업할 수 있습니다. 협업은 Microsoft 365와 같은 생산성 애플리케이션의 기능을 사용하거나 이메일을 보내거나 외부 사용자와 리소스를 공유하는 등의 방법으로 수행할 수 있습니다. 거버넌스 계획의 핵심 요소는 다음이 파악되면 형성됩니다. 
*   외부 협업을 시작하는 사용자
*   협업 중인 외부 사용자 및 조직
*   외부 사용자에게 부여되는 액세스 권한


## <a name="users-initiating-external-collaboration"></a>외부 협업을 시작하는 사용자

외부 협업을 시작하는 사용자는 외부 협업에 가장 적합한 애플리케이션과 해당 액세스를 종료해야 하는 시기를 가장 잘 알고 있습니다. 이러한 사용자를 이해하면 외부 사용자를 초대하고, 액세스 패키지를 만들고, 액세스 검토를 완료할 수 있는 권한을 위임받아야 하는 사람을 결정하는 데 도움이 됩니다.

현재 협업 중인 사용자를 찾으려면 [Microsoft 365 감사 로그](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities)에서 공유 및 액세스 요청 작업을 검토하세요. B2B 사용자를 디렉터리에 초대한 사용자에 대한 세부 정보는 [Azure AD 감사 로그](../external-identities/auditing-and-reporting.md)에서 확인할 수 있습니다.

## <a name="find-current-collaboration-partners"></a>현재 협업 파트너 찾기

외부 사용자는 파트너 관리 자격 증명을 사용하는 [Azure AD B2B 사용자](../external-identities/what-is-b2b.md)(권장) 또는 로컬로 프로비전된 자격 증명을 사용하는 외부 사용자일 수 있습니다. 이러한 사용자는 항상 그렇지는 않지만 일반적으로 UserType 또는 Guest로 표시됩니다. [Microsoft Graph API](/graph/api/user-list?tabs=http), [PowerShell](/graph/api/user-list?tabs=http)또는 [Azure Portal](../enterprise-users/users-bulk-download.md)을 통해 게스트 사용자를 열거할 수 있습니다.

### <a name="use-email-domains-and-companyname-property"></a>이메일 도메인 및 companyName 속성 사용

외부 조직은 외부 사용자 이메일 주소의 도메인 이름으로 확인할 수 있습니다. Google과 같은 소비자 ID 공급자가 지원되는 경우에는 이 방법을 사용할 수 없습니다. 이 경우 companyName 속성을 작성하여 사용자의 외부 조직을 명확하게 식별하는 것이 좋습니다.

### <a name="use-allow-or-deny-lists"></a>허용 또는 거부 목록 사용

조직에서 특정 조직과의 협업만 허용할지 여부를 고려합니다. 또는 조직에서 특정 조직과의 협업을 차단할지 여부를 고려합니다.  테넌트 수준에는 소스(예: Teams, SharePoint, Azure Portal)에 관계없이 전체 B2B 초대 및 상환을 제어하는 데 사용할 수 있는 [허용 또는 거부 목록](../external-identities/allow-deny-list.md)이 있습니다.
권한 관리를 사용하는 경우 아래와 같이 연결된 특정 조직 설정을 사용하여 파트너의 하위 집합에 대한 액세스 패키지 범위를 지정할 수도 있습니다.


![새 액세스 패키지를 만들 때 허용 또는 거부 목록 스크린샷](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>외부 사용자에게 부여되는 액세스 권한 찾기

외부 사용자 및 조직에 대한 인벤토리가 있으면 Microsoft Graph API를 사용해 이러한 사용자에게 부여된 액세스를 확인하여 Azure AD [그룹 멤버 자격](/graph/api/resources/groups-overview)을 확인하거나 Azure AD에서 [직접 애플리케이션을 할당](/graph/api/resources/approleassignment)할 수 있습니다.


### <a name="enumerate-application-specific-permissions"></a>애플리케이션별 권한 열거

애플리케이션별 권한 열거를 수행할 수도 있습니다. 예를 들어 [이 스크립트](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64)를 사용하면 SharePoint Online에 대한 권한 보고서를 프로그래밍 방식으로 생성할 수 있습니다.

모든 외부 액세스를 완벽하게 파악할 수 있도록 특히 비즈니스에 민감한 애플리케이션과 비즈니스에 중요한 애플리케이션을 모두 조사합니다.

### <a name="detect-ad-hoc-sharing"></a>임시 공유 감지
이메일 및 네트워크 요금제를 사용하는 경우 이메일을 통해 또는 권한이 없는 SaaS(software as a service) 앱을 통해 콘텐츠를 조사할 수 있습니다. [Microsoft 365 Data Loss Protection](/microsoft-365/compliance/data-loss-prevention-policies)을 사용하면 Microsoft 365 인프라 전반에서 중요한 정보를 실수로 공유하는 것을 식별, 방지 및 모니터링할 수 있습니다. [Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security)를 사용하면 사용자 환경에서 권한이 없는 SaaS 앱 사용을 식별할 수 있습니다.

## <a name="next-steps"></a>다음 단계

리소스에 대한 외부 액세스 보안에 관해 다음 문서를 참조하세요. 작업은 나열된 순서대로 수행하는 것이 좋습니다.

1. [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 파악](2-secure-access-current-state.md)

3. [거버넌스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위한 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용한 보안 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용한 보안 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용한 보안 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams, OneDrive 및 SharePoint에 대한 보안 액세스](9-secure-access-teams-sharepoint.md)