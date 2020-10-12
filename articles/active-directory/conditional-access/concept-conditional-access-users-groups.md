---
title: 조건부 액세스 정책의 사용자 및 그룹-Azure Active Directory
description: Azure AD 조건부 액세스 정책의 사용자 및 그룹
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba1fc856ee9093b628bd86b9847f8fc70b7189c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87552903"
---
# <a name="conditional-access-users-and-groups"></a>조건부 액세스: 사용자 및 그룹

조건부 액세스 정책에는 의사 결정 프로세스의 신호 중 하나로 사용자 할당을 포함 해야 합니다. 사용자는 조건부 액세스 정책에서 포함 하거나 제외할 수 있습니다. 

![조건부 액세스의 결정에 대 한 신호로 사용 되는 사용자](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>사용자 포함

일반적으로이 사용자 목록에는 조직이 조건부 액세스 정책에서 대상으로 하는 모든 사용자가 포함 됩니다. 

조건부 액세스 정책을 만들 때 사용할 수 있는 옵션은 다음과 같습니다.

- 없음
   - 사용자를 선택 하지 않음
- 모든 사용자가 액세스할 수 있습니다.
   - B2B 게스트를 비롯 하 여 디렉터리에 있는 모든 사용자입니다.
- 사용자 및 그룹 선택
   - 모든 게스트 및 외부 사용자
      - 이 선택 항목에는로 설정 된 모든 사용자를 포함 하는 모든 B2B 게스트 및 외부 사용자가 포함 됩니다 `user type` `guest` . 이 선택 항목은 CSP (클라우드 솔루션 공급자)와 같은 다른 조직에서 로그인 한 모든 외부 사용자에도 적용 됩니다. 
   - 디렉터리 역할
      - 관리자가 할당을 확인 하는 데 사용 되는 특정 Azure AD 디렉터리 역할을 선택할 수 있습니다. 예를 들어 조직은 전역 관리자 역할이 할당 된 사용자에 게 더 제한적인 정책을 만들 수 있습니다.
   - 사용자 및 그룹
      - 특정 사용자 집합을 대상으로 지정할 수 있습니다. 예를 들어, 조직에서는 HR 앱이 클라우드 앱으로 선택 된 경우 HR 부서의 모든 구성원을 포함 하는 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다. 정책이 중첩 된 사용자 및 그룹에 적용 됩니다.

> [!WARNING]
> 사용자 또는 그룹이 2048를 초과 하는 그룹의 구성원 인 경우 해당 액세스는 차단 될 수 있습니다. 이 제한은 직접 및 중첩 그룹 멤버 자격에 모두 적용 됩니다.

> [!WARNING]
> 조건부 액세스 정책은 사용자 [지정 역할](../users-groups-roles/roles-create-custom.md)을 사용 하는 것과 같이 개체에 직접 범위가 지정 된 [관리 단위나](../users-groups-roles/roles-admin-units-assign-roles.md) 디렉터리 역할로 범위가 지정 된 사용자를 지원 하지 않습니다.

## <a name="exclude-users"></a>사용자 제외

조직에서 사용자 또는 그룹을 포함 하 고 제외 하는 경우 사용자 또는 그룹이 정책에서 제외 됩니다. 제외 작업은 정책에 포함을 재정의 합니다. 제외는 일반적으로 응급 액세스 또는 투명 계정에 사용 됩니다. 응급 액세스 계정에 대 한 자세한 내용 및 중요 한 이유는 다음 문서에서 찾을 수 있습니다. 

* [Azure AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)
* [Azure Active Directory를 사용하여 복원력 있는 액세스 제어 관리 전략 수립](../authentication/concept-resilient-controls.md)

조건부 액세스 정책을 만들 때 다음 옵션을 제외 하는 데 사용할 수 있습니다.

- 모든 게스트 및 외부 사용자
   - 이 선택 항목에는로 설정 된 모든 사용자를 포함 하는 모든 B2B 게스트 및 외부 사용자가 포함 됩니다 `user type` `guest` . 이 선택 항목은 CSP (클라우드 솔루션 공급자)와 같은 다른 조직에서 로그인 한 모든 외부 사용자에도 적용 됩니다. 
- 디렉터리 역할
   - 관리자가 할당을 확인 하는 데 사용 되는 특정 Azure AD 디렉터리 역할을 선택할 수 있습니다. 예를 들어 조직은 전역 관리자 역할이 할당 된 사용자에 게 더 제한적인 정책을 만들 수 있습니다.
- 사용자 및 그룹
   - 특정 사용자 집합을 대상으로 지정할 수 있습니다. 예를 들어, 조직에서는 HR 앱이 클라우드 앱으로 선택 된 경우 HR 부서의 모든 구성원을 포함 하는 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

### <a name="preventing-administrator-lockout"></a>관리자 잠금 방지

**모든 사용자** 및 **모든 앱**에 적용 되는 정책을 만들 때 관리자가 자신의 디렉터리에서 자동으로 잠그지 못하도록 방지 하기 위해 다음과 같은 경고가 표시 됩니다.

> 직접 잠그지 마세요. 먼저 소수의 사용자 집합에 정책을 적용 하 여 예상 대로 작동 하는지 확인 하는 것이 좋습니다. 또한이 정책에서 관리자를 한 명 이상 제외 하는 것이 좋습니다. 이렇게 하면 변경 내용이 필요한 경우에도 계속 액세스할 수 있으며 정책을 업데이트할 수 있습니다. 영향을 받는 사용자 및 앱을 검토 하세요.

기본적으로 정책에서는 현재 사용자를 정책에서 제외 하는 옵션을 제공 하지만 다음 이미지와 같이 관리자가이 기본값을 재정의할 수 있습니다. 

![경고, 잠금 안 함](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 클라우드 앱 또는 작업](concept-conditional-access-cloud-apps.md)

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)
