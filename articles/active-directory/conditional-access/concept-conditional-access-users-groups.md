---
title: 조건부 액세스 정책의 사용자 및 그룹 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 사용자 및 그룹
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192130"
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
   - 모든 게스트 및 외부 사용자(미리 보기)
      - 이 선택에는 에 설정된 `user type` 특성이 있는 사용자를 포함하여 모든 `guest`B2B 게스트 및 외부 사용자가 포함됩니다. 이 선택은 CSP(클라우드 솔루션 공급자)와 같은 다른 조직에서 로그인한 외부 사용자에게도 적용됩니다. 
   - 디렉터리 역할(미리 보기)
      - 관리자가 할당을 결정하는 데 사용되는 특정 Azure AD 디렉터리 역할을 선택할 수 있습니다. 예를 들어 조직은 전역 관리자 역할을 할당한 사용자에 대해 보다 제한적인 정책을 만들 수 있습니다.
   - 개요
      - 특정 사용자 집합을 타겟팅할 수 있습니다. 예를 들어 조직은 HR 앱이 클라우드 앱으로 선택될 때 HR 부서의 모든 구성원이 포함된 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

## <a name="exclude-users"></a>사용자 제외

제외는 일반적으로 비상 액세스 또는 브레이크 글라스 계정에 사용됩니다. 긴급 액세스 계정에 대한 자세한 정보와 중요한 이유에 대한 자세한 내용은 다음 문서에서 찾을 수 있습니다. 

* [Azure AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)
* [Azure Active Directory를 사용하여 복원력 있는 액세스 제어 관리 전략 수립](../authentication/concept-resilient-controls.md)

조건부 액세스 정책을 만들 때 다음 옵션을 제외할 수 있습니다.

- 모든 게스트 및 외부 사용자(미리 보기)
   - 이 선택에는 에 설정된 `user type` 특성이 있는 사용자를 포함하여 모든 `guest`B2B 게스트 및 외부 사용자가 포함됩니다. 이 선택은 CSP(클라우드 솔루션 공급자)와 같은 다른 조직에서 로그인한 외부 사용자에게도 적용됩니다. 
- 디렉터리 역할(미리 보기)
   - 관리자가 할당을 결정하는 데 사용되는 특정 Azure AD 디렉터리 역할을 선택할 수 있습니다. 예를 들어 조직은 전역 관리자 역할을 할당한 사용자에 대해 보다 제한적인 정책을 만들 수 있습니다.
- 개요
   - 특정 사용자 집합을 타겟팅할 수 있습니다. 예를 들어 조직은 HR 앱이 클라우드 앱으로 선택될 때 HR 부서의 모든 구성원이 포함된 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 클라우드 앱 또는 작업](concept-conditional-access-cloud-apps.md)

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)
