---
title: Azure AD 권한 관리란? (미리 보기)-Azure Active Directory
description: Azure Active Directory 자격 관리에 대 한 개요와이를 사용 하 여 내부 및 외부 사용자의 그룹, 응용 프로그램 및 SharePoint Online 사이트에 대 한 액세스를 관리 하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/03/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 136a9994415b42c456ebdb0caa8ed6edcc7b4534
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934370"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Azure AD 권한 관리란? (Preview)

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

조직의 직원은 각자의 작업을 수행 하기 위해 다양 한 그룹, 응용 프로그램 및 사이트에 액세스 해야 합니다. 이 액세스를 관리 하는 것은 쉽지 않습니다. 대부분의 경우에는 프로젝트에 대해 사용자가 필요로 하는 모든 리소스에 대 한 구성 된 목록이 없습니다. 프로젝트 관리자는 필요한 리소스, 관련 된 개별 사용자 및 프로젝트가 마지막으로 지속 되는 시간을 잘 이해 하 고 있습니다. 그러나 프로젝트 관리자에 게는 일반적으로 다른 사용자에 게 액세스 권한을 부여 하거나 부여할 수 있는 권한이 없습니다. 이 시나리오는 외부 개인 또는 회사를 사용 하 여 작업을 수행 하는 경우 더 복잡해 집니다.

Azure AD (Azure Active Directory) 자격 관리를 통해 내부 사용자와 조직 외부 사용자의 그룹, 응용 프로그램 및 SharePoint Online 사이트에 대 한 액세스를 관리할 수 있습니다.

이 비디오는 자격 관리 및 비즈니스 가치에 대 한 개요를 제공 합니다.

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="why-use-entitlement-management"></a>자격 관리를 사용 하는 이유

기업 조직은 종종 다음과 같은 리소스에 대 한 액세스를 관리할 때 어려움을 겪고 있습니다.

- 사용자가 어떤 액세스 권한을가지고 있어야 하는지 알 수 없음
- 사용자가 올바른 개인 또는 올바른 리소스를 찾는 데 어려움이 있을 수 있습니다.
- 사용자가 리소스에 대 한 액세스를 찾고 받은 후에는 비즈니스 목적에 필요한 것 보다 더 긴 액세스를 유지할 수 있습니다.

이러한 문제는 공급망 조직 또는 다른 비즈니스 파트너의 외부 사용자와 같이 다른 디렉터리에서 액세스 해야 하는 사용자에 게 복잡해 집니다. 다음은 그 예입니다.

- 조직에서 다른 디렉터리의 모든 특정 개인을 초대할 수 있는 것을 알지 못할 수 있습니다.
- 조직에서 이러한 사용자를 초대할 수 있는 경우에도 조직은 사용자의 모든 액세스를 일관 되 게 관리 하지 못할 수 있습니다.

Azure AD 자격 관리는 이러한 문제를 해결 하는 데 도움이 될 수 있습니다.

## <a name="what-can-i-do-with-entitlement-management"></a>자격 관리로 무엇을 할 수 있나요?

권한 관리의 몇 가지 기능은 다음과 같습니다.

- 사용자가 요청할 수 있는 관련 리소스의 패키지 만들기
- 리소스를 요청 하 고 액세스가 만료 되는 방법에 대 한 규칙을 정의 합니다.
- 내부 및 외부 사용자에 대 한 액세스 수명 주기를 제어 합니다.
- 리소스 관리 위임
- 요청을 승인 하도록 승인자 지정
- 기록을 추적 하는 보고서 만들기

Id 거 버 넌 스 및 자격 관리에 대 한 개요는 Ignite 2018 회의에서 다음 비디오를 시청 하세요.

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>어떤 리소스를 관리할 수 있나요?

권한 관리를 사용 하 여 액세스를 관리할 수 있는 리소스 유형은 다음과 같습니다.

- Azure AD 보안 그룹
- Office 365 그룹
- 페더레이션 또는 프로 비전을 지 원하는 SaaS 응용 프로그램 및 사용자 지정 통합 응용 프로그램을 포함 한 Azure AD 엔터프라이즈 응용 프로그램
- SharePoint Online 사이트 모음 및 사이트

Azure AD 보안 그룹 또는 Office 365 그룹을 사용 하는 다른 리소스에 대 한 액세스를 제어할 수도 있습니다.  다음은 그 예입니다.

- 액세스 패키지의 Azure AD 보안 그룹을 사용 하 고 해당 그룹에 대 한 [그룹 기반 라이선스](../users-groups-roles/licensing-groups-assign.md) 를 구성 하 여 Microsoft Office 365에 대 한 라이선스를 사용자에 게 제공할 수 있습니다.
- 액세스 패키지의 Azure AD 보안 그룹을 사용 하 여 Azure 리소스를 관리 하 고 해당 그룹에 대 한 [azure 역할 할당](../../role-based-access-control/role-assignments-portal.md) 을 만들어 사용자에 게 액세스 권한을 부여할 수 있습니다.

## <a name="what-are-access-packages-and-policies"></a>액세스 패키지 및 정책 이란?

자격 관리에는 *액세스 패키지*의 개념이 도입 되었습니다. 액세스 패키지는 사용자가 프로젝트에서 작업을 수행 하거나 작업을 수행 하는 데 필요한 모든 리소스의 번들입니다. 리소스에는 그룹, 응용 프로그램 또는 사이트에 대 한 액세스 권한이 포함 됩니다. 액세스 패키지는 내부 직원 및 조직 외부 사용자에 대 한 액세스를 제어 하는 데 사용 됩니다. 액세스 패키지는 카탈로그(*catalogs*)라는 컨테이너에 정의됩니다.

액세스 패키지에는 하나 이상의 *정책도*포함 됩니다. 정책은 액세스 패키지에 액세스 하는 규칙 또는 guardrails를 정의 합니다. 정책을 사용 하도록 설정 하면 올바른 사용자 에게만 액세스 권한이 부여 되 고 올바른 리소스에는 적절 한 시간이 적용 됩니다.

![패키지 및 정책 액세스](./media/entitlement-management-overview/elm-overview-access-package.png)

액세스 패키지와 해당 정책을 사용 하 여 액세스 패키지 관리자는 다음을 정의 합니다.

- 리소스
- 리소스에 대해 사용자가 필요로 하는 역할
- 액세스를 요청할 수 있는 외부 사용자의 내부 사용자 및 파트너 조직
- 승인 프로세스 및 액세스를 승인 하거나 거부할 수 있는 사용자
- 사용자 액세스 기간

다음 다이어그램은 자격 관리의 다양 한 요소에 대 한 예를 보여 줍니다. 두 가지 예제 액세스 패키지를 보여 줍니다.

- **액세스 패키지 1** 에는 단일 그룹인 리소스로 포함 됩니다. 액세스는 디렉터리의 사용자 집합에서 액세스를 요청할 수 있도록 하는 정책으로 정의 됩니다.
- **액세스 패키지 2** 는 그룹, 응용 프로그램 및 SharePoint Online 사이트를 리소스로 포함 합니다. 액세스는 두 개의 서로 다른 정책을 사용 하 여 정의 됩니다. 첫 번째 정책을 사용 하면 디렉터리의 사용자 집합에서 액세스를 요청할 수 있습니다. 두 번째 정책을 사용 하면 외부 디렉터리의 사용자가 액세스를 요청할 수 있습니다.

![자격 관리 개요](./media/entitlement-management-overview/elm-overview.png)

## <a name="terminology"></a>용어

권한 관리 및 해당 설명서를 더 잘 이해 하려면 다음 약관을 검토 해야 합니다.

| 용어 또는 개념 | 설명 |
| --- | --- |
| 자격 관리 | 액세스 패키지를 할당, 해지 및 관리 하는 서비스입니다. |
| 액세스 패키지 | 팀 또는 프로젝트에서 필요로 하 고 정책을 적용 하는 리소스 번들입니다. 액세스 패키지는 항상 카탈로그에 포함 되어 있습니다. |
| 액세스 요청 | 액세스 패키지의 리소스에 액세스 하는 요청입니다. 요청은 일반적으로 워크플로를 거칩니다. |
| policy | 액세스 수명 주기를 정의 하는 규칙 집합 (예: 사용자가 액세스 하는 방법, 승인할 수 있는 사용자 및 사용자가 액세스 하는 시간) 예제 정책에는 직원 액세스 및 외부 액세스가 포함 됩니다. |
| catalog | 관련 리소스 및 액세스 패키지의 컨테이너입니다. |
| 일반 카탈로그 | 항상 사용할 수 있는 기본 제공 카탈로그입니다. 일반 카탈로그에 리소스를 추가 하려면 특정 권한이 필요 합니다. |
| resource | 사용자에 게 사용 권한을 부여할 수 있는 자산 또는 서비스 (예: Office 그룹, 보안 그룹, 응용 프로그램 또는 SharePoint Online 사이트)입니다. |
| 리소스 종류 | 그룹, 응용 프로그램 및 SharePoint Online 사이트를 포함 하는 리소스의 유형입니다. |
| 리소스 역할 | 리소스와 연결 된 사용 권한 컬렉션입니다. |
| 리소스 디렉터리 | 공유할 리소스가 하나 이상 있는 디렉터리입니다. |
| 연결 된 조직 | 관계가 있는 외부 Azure AD 디렉터리 또는 도메인입니다. |
| 할당 된 사용자 | 사용자가 해당 액세스 패키지의 모든 리소스 역할을 갖도록 사용자에 게 액세스 패키지를 할당 합니다. |
| enable | 사용자가 요청할 수 있는 액세스 패키지를 만드는 프로세스입니다. |

## <a name="license-requirements"></a>라이선스 요구 사항

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Azure Government, Azure 독일 및 Azure 중국 21Vianet과 같은 특수 클라우드는 현재이 미리 보기에서 사용할 수 없습니다.

### <a name="which-users-must-have-licenses"></a>어떤 사용자에게 라이선스가 있어야 하나요?

활성 구성원 사용자가 있는 경우 테 넌 트에는 적어도 Azure AD Premium P2 라이선스가 있어야 합니다. 자격 관리의 활성 구성원 사용자는 다음과 같습니다.

- 액세스 패키지에 대 한 요청을 시작 하거나 승인 하는 사용자입니다.
- 액세스 패키지가 할당 된 사용자입니다. 
- 액세스 패키지를 관리 하는 사용자입니다.

멤버 사용자에 대 한 라이선스의 일부로 많은 게스트 사용자가 자격 관리와 상호 작용 하도록 허용할 수도 있습니다. 포함할 수 있는 게스트 사용자 수를 계산 하는 방법에 대 한 자세한 내용은 [AZURE ACTIVE DIRECTORY B2B 공동 작업 라이선스 지침](../b2b/licensing-guidance.md)을 참조 하세요.

사용자에 게 라이선스를 할당 하는 방법에 대 한 자세한 내용은 [Azure Active Directory 포털을 사용 하 여 라이선스 할당 또는 제거](../fundamentals/license-users-groups.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [자습서: 첫 번째 액세스 패키지 만들기](entitlement-management-access-package-first.md)
- [일반적인 시나리오](entitlement-management-scenarios.md)
