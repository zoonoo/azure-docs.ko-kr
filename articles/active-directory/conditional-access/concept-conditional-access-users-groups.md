---
title: 조건부 액세스 정책의 사용자 및 그룹 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 사용자 및 그룹
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17312e44714c8bdb20e22ad9aeb950e46eb71e3e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755275"
---
# <a name="conditional-access-users-and-groups"></a>조건부 액세스: 사용자 및 그룹

조건부 액세스 정책에는 의사 결정 프로세스의 신호 중 하나로 사용자 할당이 포함되어야 합니다. 사용자는 조건부 액세스 정책에 포함되거나 제외될 수 있습니다. 

![조건부 액세스에 의해 결정의 신호로 사용자](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>사용자 포함

이 사용자 목록에는 일반적으로 조직이 대상으로 하는 모든 사용자가 조건부 액세스 정책에서 포함됩니다. 

조건부 액세스 정책을 만들 때 다음 옵션을 포함할 수 있습니다.

- None
   - 선택한 사용자 없음
- 모든 사용자가 액세스할 수 있습니다.
   - B2B 게스트를 포함하여 디렉터리에 있는 모든 사용자입니다.
- 사용자 및 그룹 선택
   - 모든 게스트 및 외부 사용자
      - 이 선택에는 에 설정된 `user type` 특성이 있는 사용자를 포함하여 모든 `guest`B2B 게스트 및 외부 사용자가 포함됩니다. 이 선택은 CSP(클라우드 솔루션 공급자)와 같은 다른 조직에서 로그인한 외부 사용자에게도 적용됩니다. 
   - 디렉터리 역할
      - 관리자가 할당을 결정하는 데 사용되는 특정 Azure AD 디렉터리 역할을 선택할 수 있습니다. 예를 들어 조직은 전역 관리자 역할을 할당한 사용자에 대해 보다 제한적인 정책을 만들 수 있습니다.
   - 개요
      - 특정 사용자 집합을 타겟팅할 수 있습니다. 예를 들어 조직은 HR 앱이 클라우드 앱으로 선택될 때 HR 부서의 모든 구성원이 포함된 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

## <a name="exclude-users"></a>사용자 제외

조직에서 사용자 또는 그룹을 포함 하거나 제외 하는 경우 사용자 또는 그룹 정책에서 제외 됩니다. 제외는 일반적으로 비상 액세스 또는 브레이크 글라스 계정에 사용됩니다. 긴급 액세스 계정에 대한 자세한 정보와 중요한 이유에 대한 자세한 내용은 다음 문서에서 찾을 수 있습니다. 

* [Azure AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)
* [Azure Active Directory를 사용하여 복원력 있는 액세스 제어 관리 전략 수립](../authentication/concept-resilient-controls.md)

조건부 액세스 정책을 만들 때 다음 옵션을 제외할 수 있습니다.

- 모든 게스트 및 외부 사용자
   - 이 선택에는 에 설정된 `user type` 특성이 있는 사용자를 포함하여 모든 `guest`B2B 게스트 및 외부 사용자가 포함됩니다. 이 선택은 CSP(클라우드 솔루션 공급자)와 같은 다른 조직에서 로그인한 외부 사용자에게도 적용됩니다. 
- 디렉터리 역할
   - 관리자가 할당을 결정하는 데 사용되는 특정 Azure AD 디렉터리 역할을 선택할 수 있습니다. 예를 들어 조직은 전역 관리자 역할을 할당한 사용자에 대해 보다 제한적인 정책을 만들 수 있습니다.
- 개요
   - 특정 사용자 집합을 타겟팅할 수 있습니다. 예를 들어 조직은 HR 앱이 클라우드 앱으로 선택될 때 HR 부서의 모든 구성원이 포함된 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

### <a name="preventing-administrator-lockout"></a>관리자 잠금 방지

**모든 사용자** 및 **모든 앱에**적용되는 정책을 만들 때 관리자가 디렉터리에서 자신을 잠그지 못하도록 하려면 다음 경고가 표시됩니다.

> 자신을 잠그지 마십시오! 작은 사용자 집합에 정책을 먼저 적용하여 예상대로 작동하는지 확인하는 것이 좋습니다. 또한 이 정책에서 관리자를 하나 이상 제외하는 것이 좋습니다. 이렇게 하면 계속 액세스할 수 있으며 변경이 필요한 경우 정책을 업데이트할 수 있습니다. 영향을 받는 사용자 및 앱을 검토하십시오.

기본적으로 정책은 정책에서 현재 사용자를 제외하는 옵션을 제공하지만 다음 이미지와 같이 관리자가 이 기본값을 재정의할 수 있습니다. 

![경고, 자신을 잠그지 마십시오!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 클라우드 앱 또는 작업](concept-conditional-access-cloud-apps.md)

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)
