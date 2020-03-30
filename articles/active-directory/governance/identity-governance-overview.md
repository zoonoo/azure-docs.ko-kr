---
title: ID 거버넌스 - Azure Active Directory | 마이크로 소프트 문서
description: Azure Active Directory ID 거버넌스를 사용하면 조직의 보안 및 직원 생산성 요구와 올바른 프로세스 및 가시성의 균형을 맞출 수 있습니다.
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
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd319dd6a83a392f6df26d07a58be22a9c8bdb61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063682"
---
# <a name="what-is-azure-ad-identity-governance"></a>Azure AD Identity Governance란?

Azure Active Directory(Azure AD) ID 거버넌스를 사용하면 조직의 보안 및 직원 생산성 요구와 올바른 프로세스 및 가시성의 균형을 맞출 수 있습니다. 적절한 사람이 올바른 리소스에 액세스할 수 있도록 하는 기능을 제공합니다. 이러한 Azure AD 및 엔터프라이즈 모빌리티 + 보안 기능을 사용하면 중요한 자산에 대한 액세스를 보호, 모니터링 및 감사하는 동시에 직원 및 비즈니스 파트너의 생산성을 보장하여 액세스 위험을 완화할 수 있습니다.  

ID 거버넌스를 통해 조직은 직원, 비즈니스 파트너 및 공급업체, 온-프레미스 및 클라우드 모두에서 서비스 및 응용 프로그램 전반에 걸쳐 다음과 같은 작업을 수행할 수 있습니다.

- ID 수명 주기 거버넌스
- 액세스 수명 주기 거버넌스
- 관리를 위한 안전한 권한 액세스

특히 조직이 이러한 네 가지 주요 질문을 해결할 수 있도록 합니다.

- 액세스할 수 있는 리소스 및 사용자는 무엇인가요?
- 해당 사용자가 해당 액세스를 사용하여 수행하는 작업은 무엇인가요?
- 액세스를 관리하기 위한 유효한 조직 컨트롤이 있나요?
- 감사자는 컨트롤이 작동하는지 확인할 수 있나요?

## <a name="identity-lifecycle"></a>ID 수명 주기

ID 거버넌스를 통해 조직은 *생산성* 간의 균형을 맞출 수 있습니다 - 사용자가 조직에 가입할 때와 같이 필요한 리소스에 얼마나 빨리 액세스할 수 있습니까? *보안* - 해당하는 사람의 고용 상태의 변경 내용과 같이 시간이 지남에 따라 해당하는 액세스를 어떻게 변경해야 하나요?  ID 수명 주기 관리는 ID 거버넌스의 토대이며, 대규모의 효과적인 거버넌스는 응용 프로그램에 대한 ID 라이프사이클 관리 인프라를 현대화해야 합니다.

![ID 수명 주기](./media/identity-governance-overview/identity-lifecycle.png)

많은 조직에서 직원의 ID 수명 주기가 HCM(인적 자원 관리) 시스템에서 해당 사용자의 표현에 연결됩니다.  Azure AD Premium은 [Workday 인바운드 프로비저닝 자습서에](../saas-apps/workday-inbound-tutorial.md)설명된 대로 Active Directory 및 Azure Active Directory모두에서 Workday에 표시되는 사용자의 사용자 ID를 자동으로 유지 관리합니다.  또한 Azure AD Premium에는 SAP, Oracle eBusiness 및 Oracle PeopleSoft와 같은 온-프레미스 HCM 시스템에서 레코드를 가져올 수 있는 [Microsoft Identity Manager](/microsoft-identity-manager/)가 포함됩니다.

점점 더 많은 시나리오에서는 조직 외부 사람들과 협업해야 합니다. [Azure AD B2B](/azure/active-directory/b2b/) 협업을 사용하면 조직의 애플리케이션과 서비스를 게스트 사용자 및 모든 조직의 외부 파트너와 안전하게 공유하면서 고유한 회사 데이터에 대한 제어를 유지합니다.  [Azure AD 권한 관리를](entitlement-management-overview.md) 사용하면 액세스를 요청하고 조직의 디렉터리에 B2B 게스트로 추가할 수 있는 조직의 사용자를 선택할 수 있으며 더 이상 액세스가 필요하지 않을 때 이러한 게스트가 제거되도록 할 수 있습니다.

## <a name="access-lifecycle"></a>액세스 수명 주기

조직은 사용자의 ID를 만들 때 해당 사용자에 대해 처음으로 프로비전된 범위를 넘어서 액세스를 관리하는 프로세스가 필요합니다.  또한 기업 조직은 액세스 정책 및 컨트롤을 지속적으로 개발하고 적용할 수 있도록 효율적으로 규모를 조정해야 합니다.

![액세스 수명 주기](./media/identity-governance-overview/access-lifecycle.png)

IT 대리자는 일반적으로 비즈니스 의사 결정권자에 대한 승인 결정에 액세스합니다.  또한 IT에는 사용자가 스스로가 포함될 수 있습니다.  예를 들어 유럽에 있는 회사의 마케팅 애플리케이션에서 기밀 고객 데이터에 액세스하는 사용자는 회사의 정책을 알아야 합니다. 게스트 사용자는 초대된 조직의 데이터에 대해 처리 중인 요구 사항을 인식하지 못할 수 있습니다.

조직은 [동적 그룹](../users-groups-roles/groups-dynamic-membership.md)과 같은 기술을 통해 액세스 수명 주기 프로세스를 자동화하여 [SaaS 앱](../saas-apps/tutorial-list.md) 또는 [SCIM과 통합된 앱](../app-provisioning/use-scim-to-provision-users-and-groups.md)에 프로비전한 사용자와 결합할 수 있습니다.  조직은 [온-프레미스 애플리케이션에 액세스할 수 있는 게스트 사용자](../b2b/hybrid-cloud-to-on-premises.md)를 제어할 수도 있습니다.  그런 다음, 정기 [Azure AD 액세스 검토](access-reviews-overview.md)를 사용하여 이러한 액세스 권한을 정기적으로 검토할 수 있습니다.   또한 [Azure AD 권한 관리를](entitlement-management-overview.md) 사용하면 그룹 및 팀 구성원, 응용 프로그램 역할 및 SharePoint Online 역할의 패키지 간에 사용자가 액세스를 요청하는 방법을 정의할 수 있습니다.

사용자가 응용 프로그램에 액세스하려고 하면 Azure AD가 [조건부 액세스](/azure/active-directory/conditional-access/) 정책을 적용합니다. 예를 들어 조건부 액세스 정책에는 [사용 약관을](../conditional-access/terms-of-use.md) 표시하고 사용자가 응용 프로그램에 액세스하기 전에 [해당 약관에 동의했는지 확인하는](../conditional-access/require-tou.md) 것이 포함될 수 있습니다.

## <a name="privileged-access-lifecycle"></a>권한 있는 액세스 수명 주기

이전에는 다른 공급업체에서 ID 거버넌스와 는 별개의 기능으로 권한 있는 액세스 권한을 설명했습니다. 그러나 Microsoft에서는 권한 있는 액세스 관리가 ID 거버넌스의 핵심 부분이라고 생각하며, 특히 이러한 관리자 권한과 관련된 오용이 조직에 발생할 수 있습니다. 관리 권한을 사용하는 직원, 공급 업체 및 계약자를 관리해야 합니다.

![권한 있는 액세스 수명 주기](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD 권한 ID 관리(PIM)는](../privileged-identity-management/pim-configure.md) Azure AD, Azure 및 기타 Microsoft 온라인 서비스 전반에서 리소스에 대한 액세스 권한을 보호하는 데 필요한 추가 컨트롤을 제공합니다.  Azure AD PIM에서 제공하는 적시 액세스 및 역할 변경 경고 기능은 다단계 인증 및 조건부 액세스 외에도 회사의 리소스를 보호하는 데 도움이 되는 포괄적인 거버넌스 제어 집합을 제공합니다(디렉터리, Office 365 및 Azure 리소스 역할). 다른 양식의 액세스를 사용하는 경우와 마찬가지로 조직은 액세스 검토를 사용하여 관리자 역할의 모든 사용자에 대해 정기 액세스 재인증을 구성할 수 있습니다.

## <a name="getting-started"></a>시작

Azure 포털에서 ID **거버넌스의** 시작 시작 탭을 확인하여 권한 관리, 액세스 검토, 권한 있는 ID 관리 및 사용 약관을 사용하기 시작합니다.

![ID 거버넌스 시작](./media/identity-governance-overview/getting-started.png)


ID 거버넌스 기능에 대한 피드백이 있는 경우 Azure 포털에서 **피드백 이 확인을** 클릭하여 피드백을 제출합니다. 팀은 정기적으로 피드백을 검토합니다.

모든 고객에게 완벽한 솔루션이나 권장 사항은 없지만 다음 구성 가이드는 보다 안전하고 생산적인 인력을 확보하기 위해 권장하는 기준 정책도 제공합니다.

- [ID 및 디바이스 액세스 구성](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [권한 있는 액세스 보안](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>부록 - ID 거버넌스 기능 관리를 위한 최소 권한 역할

ID 거버넌스에서 관리 작업을 수행하기 위해 최소 권한 역할을 사용하는 것이 좋습니다. Azure AD PIM을 사용하여 이러한 작업을 수행하는 데 필요한 역할을 활성화하는 것이 좋습니다. 다음은 ID 거버넌스 기능을 구성하는 가장 권한 있는 디렉터리 역할입니다.

| 기능 | 최소 권한 역할 |
| ------- | --------------------- |
| 권한 관리 | 사용자 관리자(SharePoint Online 사이트를 카탈로그에 추가하는 경우 제외) |
| 액세스 검토 | 사용자 관리자(권한 있는 역할 관리자가 필요한 Azure 또는 Azure AD 역할의 액세스 검토 제외) |
|Privileged Identity Management | 권한 있는 역할 관리자 |
| 사용 약관 | 보안 관리자 또는 조건부 액세스 관리자 |

## <a name="next-steps"></a>다음 단계

- [Azure AD 권한 관리란?](entitlement-management-overview.md)
- [Azure AD 액세스 리뷰란 무엇입니까?](access-reviews-overview.md)
- [Azure AD Privileged Identity Management란?](../privileged-identity-management/pim-configure.md)
- [사용 약관으로 무엇을 할 수 있나요?](active-directory-tou.md)


