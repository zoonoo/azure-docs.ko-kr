---
title: Azure AD ID 거버넌스 | Microsoft Docs
description: Azure AD ID 거버넌스를 사용하면 올바른 프로세스 및 표시 유형을 사용하여 보안 및 직원 생산성에 대한 조직의 필요를 분산시킬 수 있습니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.openlocfilehash: 20b1c8673bfdb3b2207ed63749f79539c396642c
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167755"
---
# <a name="what-is-azure-ad-identity-governance"></a>Azure AD ID 거버넌스란?

Azure AD(Azure Active Directory) ID 거버넌스를 사용하면 올바른 프로세스 및 표시 유형을 사용하여 보안 및 직원 생산성에 대한 조직의 필요를 분산시킬 수 있습니다. 적절한 사용자가 적절한 리소스에 대한 권한 액세스하는지 확인하는 기능을 제공하고, 직원의 생산성을 보장하면서 중요한 자산에 대한 액세스를 보호하고, 모니터링하고, 감사할 수 있습니다.  

ID 거버넌스는 조직에 직원, 비즈니스 파트너와 공급 업체 및 서비스와 애플리케이션에서 다음을 수행하는 기능을 제공합니다.

- ID 수명 주기 거버넌스
- 액세스 수명 주기 거버넌스
- 보안 관리

특히 조직이 이러한 네 가지 주요 질문을 해결할 수 있도록 합니다.

- 액세스할 수 있는 리소스 및 사용자는 무엇인가요?
- 해당 사용자가 해당 액세스를 사용하여 수행하는 작업은 무엇인가요?
- 액세스를 관리하기 위한 유효한 조직 컨트롤이 있나요?
- 감사자는 컨트롤이 작동하는지 확인할 수 있나요?

## <a name="identity-lifecycle"></a>ID 수명 주기

ID 거버넌스를 통해 조직이 *생산성* 간에 균형을 설정할 수 있습니다. 사용자가 조직에 조인할 때와 같이 필요한 리소스에 얼마나 빠르게 액세스할 수 있나요? *보안* - 해당하는 사람의 고용 상태의 변경 내용과 같이 시간이 지남에 따라 해당하는 액세스를 어떻게 변경해야 하나요?  ID 수명 주기 관리는 ID 거버넌스의 기반이며 대규모의 효과적인 거버넌스에는 애플리케이션에 대한 ID 수명 주기 관리 인프라를 현대화해야 합니다.

많은 조직에서 직원의 ID 수명 주기가 HCM(인적 자원 관리) 시스템에서 해당 사용자의 표현에 연결됩니다.  Azure AD Premium은 [Workday 인바운드 프로비전(미리 보기) 자습서](../saas-apps/workday-inbound-tutorial.md)에 설명된 대로 Active Directory와 Azure Active Directory 모두의 Workday에 표시된 사람에 대한 사용자 ID를 자동으로 유지합니다.  또한 Azure AD Premium에는 SAP, Oracle eBusiness 및 Oracle PeopleSoft와 같은 온-프레미스 HCM 시스템에서 레코드를 가져올 수 있는 [Microsoft Identity Manager](/microsoft-identity-manager/)가 포함됩니다.

점점 더 많은 시나리오에서는 조직 외부 사람들과 공동 작업해야 합니다. [Azure AD B2B](/azure/active-directory/b2b/) 공동 작업을 사용하면 조직의 응용 프로그램과 서비스를 게스트 사용자 및 모든 조직의 외부 파트너와 안전하게 공유하면서 고유한 회사 데이터에 대한 제어를 유지합니다.

## <a name="access-lifecycle"></a>액세스 수명 주기

조직은 사용자의 ID를 만들 때 해당 사용자에 대해 처음으로 프로비전된 범위를 넘어서 액세스를 관리하는 프로세스가 필요합니다.  또한 기업 조직은 액세스 정책 및 컨트롤을 지속적으로 개발하고 적용할 수 있도록 효율적으로 규모를 조정해야 합니다.

IT 대리자는 일반적으로 비즈니스 의사 결정권자에 대한 승인 결정에 액세스합니다.  또한 IT에는 사용자가 스스로가 포함될 수 있습니다.  예를 들어 유럽에 있는 회사의 마케팅 애플리케이션에서 기밀 고객 데이터에 액세스하는 사용자는 회사의 정책을 알아야 합니다. 게스트 사용자는 초대된 조직의 데이터에 대해 처리 중인 요구 사항을 인식하지 못할 수 있습니다.

조직은 [동적 그룹](../users-groups-roles/groups-dynamic-membership.md)과 같은 기술을 통해 액세스 수명 주기 프로세스를 자동화하여 [SaaS 앱](../saas-apps/tutorial-list.md) 또는 [SCIM과 통합된 앱](../manage-apps/use-scim-to-provision-users-and-groups.md)에 프로비전한 사용자와 결합할 수 있습니다.  조직은 [온-프레미스 애플리케이션에 액세스할 수 있는 게스트 사용자](../b2b/hybrid-cloud-to-on-premises.md)를 제어할 수도 있습니다.  그런 다음, 정기 [Azure AD 액세스 검토](access-reviews-overview.md)를 사용하여 이러한 액세스 권한을 정기적으로 검토할 수 있습니다.

사용자가 애플리케이션에 액세스하려고 하는 경우 Azure AD는 [조건부 액세스](/azure/active-directory/conditional-access/) 정책을 적용합니다. 예를 들어, 조건부 액세스 정책에는 애플리케이션에 액세스하기 전에 [사용 약관](active-directory-tou.md)을 표시하고 [사용자가 해당 약관에 동의했는지 확인](../conditional-access/require-tou.md)하는 작업이 포함될 수 있습니다.

## <a name="privileged-access-lifecycle"></a>권한 있는 액세스 수명 주기

지금까지 권한 있는 액세스는 다른 공급 업체에서 ID 거버넌스의 별도 기능으로 설명되었습니다. 그러나 Microsoft에서는 권한 있는 액세스가 ID 거버넌스의 중요한 부분이라고 생각합니다. 특히 해당 관리자 권한과 연결된 오용 가능성이 조직에서 발생할 수 있기 때문입니다. 관리 권한을 사용하는 직원, 공급 업체 및 계약자를 관리해야 합니다.

PIM(Azure AD Privileged Identity Management)에서는 Azure AD, Azure 및 기타 Microsoft 온라인 서비스에서 리소스에 대한 액세스 권한을 보호하기 위해 조정된 추가 컨트롤을 제공합니다.  다단계 인증 및 조건부 액세스 외에도 Just-In-Time 액세스 및 Azure AD PIM에서 제공된 역할 변경 내용 경고 기능은 포괄적인 집합의 거버넌스 제어를 제공하여 회사의 리소스(디렉터리, Office 365 및 Azure 리소스 역할)를 보호하는 데 도움이 됩니다. 다른 양식의 액세스를 사용하는 경우와 마찬가지로 조직은 액세스 검토를 사용하여 관리자 역할의 모든 사용자에 대해 정기 액세스 재인증을 구성할 수 있습니다.

## <a name="getting-started"></a>시작

모든 고객에 대해 완벽한 솔루션이나 권장 사항이 없는 반면 다음 구성에서는 더 안전하고 생산적인 인력을 보장하기 위해 Microsoft에서 권장하는 기준 정책이 무엇인지에 대한 가이드를 제공합니다.

- [ID 및 장치 액세스 구성](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [권한 있는 액세스 보안](../users-groups-roles/directory-admin-roles-secure.md)


### <a name="access-reviews"></a>액세스 검토

- [액세스 검토란?](access-reviews-overview.md)
- [액세스 검토를 사용하여 사용자 액세스 관리](manage-user-access-with-access-reviews.md)
- [액세스 검토를 사용하여 게스트 액세스 관리](manage-guest-access-with-access-reviews.md)
- [디렉터리 역할의 액세스 검토 시작](../privileged-identity-management/pim-how-to-start-security-review.md)

### <a name="terms-of-use"></a>사용 약관

- [사용 약관으로 무엇을 할 수 있나요?](active-directory-tou.md)

### <a name="privileged-identity-management"></a>Privileged Identity Management

- [Azure AD PIM이란?](../privileged-identity-management/pim-configure.md)
