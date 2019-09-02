---
title: Id 거 버 넌 스-Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance를 사용 하면 적절 한 프로세스 및 가시성을 통해 보안 및 직원 생산성에 대 한 조직의 필요성을 균형 있게 조정할 수 있습니다.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/28/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74e0539095701ec104495bf5a44dec668494d444
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207181"
---
# <a name="what-is-azure-ad-identity-governance"></a>Azure AD Identity Governance란?

Azure AD (Azure Active Directory) Id 거 버 넌 스를 사용 하면 적절 한 프로세스 및 가시성을 통해 보안 및 직원 생산성에 대 한 조직의 필요성을 균형 있게 조정할 수 있습니다. 적절한 사용자가 적절한 리소스에 대한 권한 액세스하는지 확인하는 기능을 제공하고, 직원의 생산성을 보장하면서 중요한 자산에 대한 액세스를 보호하고, 모니터링하고, 감사할 수 있습니다.  

Id 거 버 넌 스는 직원, 비즈니스 파트너 및 공급 업체, 서비스 및 응용 프로그램에서 다음 작업을 수행할 수 있는 기능을 조직에 제공 합니다.

- ID 수명 주기 거버넌스
- 액세스 수명 주기 거버넌스
- 보안 관리

특히 조직이 이러한 네 가지 주요 질문을 해결할 수 있도록 합니다.

- 액세스할 수 있는 리소스 및 사용자는 무엇인가요?
- 해당 사용자가 해당 액세스를 사용하여 수행하는 작업은 무엇인가요?
- 액세스를 관리하기 위한 유효한 조직 컨트롤이 있나요?
- 감사자는 컨트롤이 작동하는지 확인할 수 있나요?

## <a name="identity-lifecycle"></a>ID 수명 주기

Id 거 버 넌 스를 통해 조직은 *생산성* 간에 균형을 유지할 수 있습니다. 조직에 가입 하는 경우와 같이 사용자가 필요한 리소스에 얼마나 빠르게 액세스할 수 있나요? *보안* - 해당하는 사람의 고용 상태의 변경 내용과 같이 시간이 지남에 따라 해당하는 액세스를 어떻게 변경해야 하나요?  Id 수명 주기 관리는 Id 거 버 넌 스를 위한 토대 이며, 대규모의 효율적인 관리를 위해서는 응용 프로그램에 대 한 id 수명 주기 관리 인프라를 현대화 해야 합니다.

![ID 수명 주기](./media/identity-governance-overview/identity-lifecycle.png)

많은 조직에서 직원의 ID 수명 주기가 HCM(인적 자원 관리) 시스템에서 해당 사용자의 표현에 연결됩니다.  Azure AD Premium은 [Workday 인바운드 프로비전(미리 보기) 자습서](../saas-apps/workday-inbound-tutorial.md)에 설명된 대로 Active Directory와 Azure Active Directory 모두의 Workday에 표시된 사람에 대한 사용자 ID를 자동으로 유지합니다.  또한 Azure AD Premium에는 SAP, Oracle eBusiness 및 Oracle PeopleSoft와 같은 온-프레미스 HCM 시스템에서 레코드를 가져올 수 있는 [Microsoft Identity Manager](/microsoft-identity-manager/)가 포함됩니다.

점점 더 많은 시나리오에서는 조직 외부 사람들과 협업해야 합니다. [Azure AD B2B](/azure/active-directory/b2b/) 협업을 사용하면 조직의 애플리케이션과 서비스를 게스트 사용자 및 모든 조직의 외부 파트너와 안전하게 공유하면서 고유한 회사 데이터에 대한 제어를 유지합니다.

## <a name="access-lifecycle"></a>액세스 수명 주기

조직은 사용자의 ID를 만들 때 해당 사용자에 대해 처음으로 프로비전된 범위를 넘어서 액세스를 관리하는 프로세스가 필요합니다.  또한 기업 조직은 액세스 정책 및 컨트롤을 지속적으로 개발하고 적용할 수 있도록 효율적으로 규모를 조정해야 합니다.

![액세스 수명 주기](./media/identity-governance-overview/access-lifecycle.png)

IT 대리자는 일반적으로 비즈니스 의사 결정권자에 대한 승인 결정에 액세스합니다.  또한 IT에는 사용자가 스스로가 포함될 수 있습니다.  예를 들어 유럽에 있는 회사의 마케팅 애플리케이션에서 기밀 고객 데이터에 액세스하는 사용자는 회사의 정책을 알아야 합니다. 게스트 사용자는 초대된 조직의 데이터에 대해 처리 중인 요구 사항을 인식하지 못할 수 있습니다.

조직은 [동적 그룹](../users-groups-roles/groups-dynamic-membership.md)과 같은 기술을 통해 액세스 수명 주기 프로세스를 자동화하여 [SaaS 앱](../saas-apps/tutorial-list.md) 또는 [SCIM과 통합된 앱](../manage-apps/use-scim-to-provision-users-and-groups.md)에 프로비전한 사용자와 결합할 수 있습니다.  조직은 [온-프레미스 애플리케이션에 액세스할 수 있는 게스트 사용자](../b2b/hybrid-cloud-to-on-premises.md)를 제어할 수도 있습니다.  그런 다음, 정기 [Azure AD 액세스 검토](access-reviews-overview.md)를 사용하여 이러한 액세스 권한을 정기적으로 검토할 수 있습니다.

사용자가 응용 프로그램에 액세스 하려고 하면 Azure AD는 [조건부 액세스](/azure/active-directory/conditional-access/) 정책을 적용 합니다. 예를 들어 조건부 액세스 정책에는 [사용 약관](../conditional-access/terms-of-use.md) 을 표시 하 고 사용자가 응용 프로그램에 액세스 하기 전에 [해당 약관에 동의](../conditional-access/require-tou.md) 했는지 확인 하는 작업이 포함 될 수 있습니다.

## <a name="privileged-access-lifecycle"></a>권한 있는 액세스 수명 주기

지금까지 다른 공급 업체에서 Id 거 버 넌 스와 별도의 기능으로 권한 있는 액세스를 설명 했습니다. 그러나 Microsoft에서는 권한 있는 액세스를 관리 하는 것이 중요 합니다. 특히, 해당 관리자 권한과 관련 하 여 오용 될 가능성이 있으면 조직으로 인해 발생할 수 있습니다. 관리 권한을 사용하는 직원, 공급 업체 및 계약자를 관리해야 합니다.

![권한 있는 액세스 수명 주기](./media/identity-governance-overview/privileged-access-lifecycle.png)

PIM(Azure AD Privileged Identity Management)에서는 Azure AD, Azure 및 기타 Microsoft 온라인 서비스에서 리소스에 대한 액세스 권한을 보호하기 위해 조정된 추가 컨트롤을 제공합니다.  Multi-factor authentication 및 조건부 액세스 뿐만 아니라 Azure AD PIM에서 제공 하는 just-in-time 액세스 및 역할 변경 경고 기능을 사용 하면 회사의 리소스를 보호 하는 포괄적인 거 버 넌 스 컨트롤을 제공할 수 있습니다 (디렉터리, Office 365 및 Azure 리소스 역할). 다른 양식의 액세스를 사용하는 경우와 마찬가지로 조직은 액세스 검토를 사용하여 관리자 역할의 모든 사용자에 대해 정기 액세스 재인증을 구성할 수 있습니다.

## <a name="least-privileged-roles"></a>최소 권한 역할

PIM을 사용 하면 추가 보안을 위해 사용자의 관리 권한을 제한할 수 있습니다. Azure AD id 거 버 넌 스 기능에서 구성에 대 한 최소 권한 있는 디렉터리 역할은 다음과 같습니다. 

| 기능 | 최소 권한 역할 |
| ------- | --------------------- |
| 권한 관리 | 사용자 관리자 (전역 관리자가 필요한 카탈로그에 SharePoint Online 사이트를 추가 하는 것은 제외) |
| 서비스 계약 | 보안 관리자 또는 조건부 액세스 관리자 |
| 액세스 검토 | 사용자 관리자 (권한 있는 역할 관리자가 필요한 Azure 또는 Azure AD 역할의 액세스 검토를 제외 하 고) |
|권위 있는 ID 관리 | 권한 있는 역할 관리자 |


## <a name="getting-started"></a>시작

모든 고객에 게 완벽 한 솔루션이 나 권장 사항은 없지만 다음 구성은 Microsoft에서 더 안전 하 고 생산적인 인력을 보장 하기 위해 권장 하는 기본 정책에 대 한 지침을 제공 합니다.

- [ID 및 디바이스 액세스 구성](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [권한 있는 액세스 보안](../users-groups-roles/directory-admin-roles-secure.md)

Azure Portal에서 **Id 거 버 넌 스** 의 시작 탭을 확인 하 여 권한 부여 관리, 액세스 검토, Privileged Identity Management 및 사용 약관를 시작할 수도 있습니다.

![Id 거 버 넌 스 시작](./media/identity-governance-overview/getting-started.png)

Id 거 버 넌 스 기능에 대 한 의견이 있는 경우 피드백을 제출 하려면 Azure Portal에서 **피드백을 받았습니다** .를 클릭 합니다. 팀에서 사용자 의견을 정기적으로 검토 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 자격 관리 란? (미리 보기)](entitlement-management-overview.md)
- [Azure AD 액세스 검토란?](access-reviews-overview.md)
- [Azure AD Privileged Identity Management란?](../privileged-identity-management/pim-configure.md)
- [사용 약관으로 무엇을 할 수 있나요?](active-directory-tou.md)
