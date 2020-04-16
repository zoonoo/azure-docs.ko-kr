---
title: 내 직원을 사용하여 사용자 관리(미리 보기) - Azure AD | 마이크로 소프트 문서
description: 내 직원 및 관리 단위를 사용하여 사용자 관리 위임
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394216"
---
# <a name="manage-your-users-with-my-staff-preview"></a>내 직원으로 사용자 관리(미리 보기)

내 직원을 사용하면 매장 관리자 또는 팀 장과 같은 권한 있는 권한에 대해 위임하여 직원이 Azure AD 계정에 액세스할 수 있도록 할 수 있습니다. 조직은 중앙 헬프 데스크에 의존하는 대신 암호 재설정 또는 전화 번호 변경과 같은 일반적인 작업을 팀 관리자에게 위임할 수 있습니다. 내 직원을 사용하면 계정에 액세스할 수 없는 사용자는 몇 번의 클릭만으로 헬프 데스크 나 IT 직원이 필요하지 않은 방식으로 다시 액세스할 수 있습니다.

조직에 대한 내 직원을 구성하기 전에 이 설명서와 [사용자 설명서를](../user-help/my-staff-team-manager.md) 검토하여 이 기능의 기능과 사용자에게 미치는 영향을 이해하는 것이 좋습니다. 사용자 설명서를 활용하여 새로운 환경을 교육하고 준비하고 성공적인 롤아웃을 보장할 수 있습니다.

## <a name="how-my-staff-works"></a>직원의 작동 방식

내 직원은 역할 할당의 관리 제어 범위를 제한하는 데 사용할 수 있는 리소스 컨테이너인 관리 단위(AUs)를 기반으로 합니다. 내 직원에서 AUS는 저장소 나 부서와 같은 조직의 사용자의 하위 집합을 정의하는 데 사용됩니다. 그런 다음 예를 들어 팀 관리자는 범위가 하나 이상의 AUS인 역할에 할당될 수 있습니다. 아래 예제에서는 사용자에게 인증 관리 역할이 부여되었으며 세 개의 AUs는 역할의 범위입니다. 관리 단위에 대한 자세한 내용은 [Azure Active Directory의 관리 단위 관리를](directory-administrative-units.md)참조하십시오.

## <a name="how-to-enable-my-staff"></a>내 직원을 활성화하는 방법

사용자를 구성한 후에는 내 직원에 액세스하는 사용자에게 이 범위를 적용할 수 있습니다. 관리 역할이 할당된 사용자만 내 직원에 액세스할 수 있습니다. 내 직원을 활성화하려면 다음 단계를 완료하십시오.

1. 사용자 관리자로 Azure 포털에 로그인합니다.
2. Azure **Active Directory** > **사용자 설정** > **사용자 기능 미리 보기** > 로 찾아보기**사용자 기능 미리 보기 설정**입니다.
3. **관리자는 내 직원에 액세스할 수**있으므로 모든 사용자, 선택한 사용자 또는 사용자 액세스 에 대해 사용하도록 설정할 수 있습니다.

> [!Note]
> 관리자 역할이 할당된 사용자만 내 직원에 액세스할 수 있습니다. 관리자 역할이 할당되지 않은 사용자에 대해 내 직원을 사용하도록 설정하면 내 직원에 액세스할 수 없습니다.

## <a name="using-my-staff"></a>내 직원 사용

사용자가 내 직원에게 이동하면 관리 권한이 있는 [관리 단위의](directory-administrative-units.md) 이름이 표시됩니다. 내 [직원 사용자 설명서에서](../user-help/my-staff-team-manager.md)"위치"라는 용어를 사용하여 관리 단위를 참조합니다. 관리자의 사용 권한에 AU 범위가 없는 경우 권한은 조직 전체에 적용됩니다. 내 직원을 사용하도록 설정한 후 관리 역할이 활성화되고 할당된 사용자는 을 [https://mystaff.microsoft.com](https://mystaff.microsoft.com)통해 액세스할 수 있습니다. AU를 선택하여 해당 AU의 사용자를 보고 프로필을 열 사용자를 선택할 수 있습니다.

## <a name="licenses"></a>라이선스

내 직원 포털을 사용하지 않더라도 내 직원에서 사용하도록 설정된 각 사용자에게 라이선스가 부여되어야 합니다. 활성화된 각 사용자는 다음 Azure AD 또는 Microsoft 365 라이선스 중 하나를 가져야 합니다.

- Azure AD Premium P1 또는 P2
- 마이크로 소프트 365 F1 또는 F3

## <a name="reset-a-users-password"></a>사용자의 암호 다시 설정

다음 역할에는 사용자의 암호를 재설정할 수 있는 권한이 있습니다.

- [인증 관리자](directory-assign-admin-roles.md#authentication-administrator)
- [권한 있는 인증 관리자](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [전역 관리자](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [헬프데스크 관리자](directory-assign-admin-roles.md#helpdesk-administrator)
- [사용자 관리자](directory-assign-admin-roles.md#user-administrator)
- [암호 관리자](directory-assign-admin-roles.md#password-administrator)

**내 직원에서**사용자의 프로필을 엽니다. **암호 다시 설정**을 선택합니다.

- 사용자가 클라우드 전용인 경우 사용자에게 줄 수 있는 임시 암호를 볼 수 있습니다.
- 사용자가 온-프레미스 Active Directory에서 동기화된 경우 온-프레미스 AD 정책을 충족하는 암호를 입력할 수 있습니다. 그런 다음 해당 암호를 사용자에게 제공할 수 있습니다.

    ![암호 재설정 진행 지표 및 성공 알림](media/my-staff-configure/reset-password.png)

사용자는 다음에 로그인할 때 암호를 변경해야 합니다.

## <a name="manage-a-phone-number"></a>전화 번호 관리

**내 직원에서**사용자의 프로필을 엽니다.

- 사용자의 전화 번호 추가 섹션 **추가를** 선택합니다.
- **전화 번호 편집을** 선택하여 전화 번호를 변경합니다.
- **전화 번호 제거를** 선택하여 사용자의 전화 번호를 제거합니다.

설정에 따라 사용자는 SMS로 로그인하고 다단계 인증을 수행하고 셀프 서비스 암호 재설정을 수행하기 위해 설정한 전화 번호를 사용할 수 있습니다.

사용자의 전화 번호를 관리하려면 다음 역할 중 하나를 할당받아야 합니다.

- [인증 관리자](directory-assign-admin-roles.md#authentication-administrator)
- [권한 있는 인증 관리자](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [전역 관리자](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>검색

내 직원의 검색 표시줄을 사용하여 조직의 사용자 및 AUS를 검색할 수 있습니다. 조직의 모든 AU 및 사용자를 검색할 수 있지만 관리자 권한이 부여된 AU에 있는 사용자만 변경할 수 있습니다.

AU 내에서 사용자를 검색할 수도 있습니다. 이렇게 하려면 사용자 목록 맨 위에 있는 검색 줄을 사용합니다.

## <a name="audit-logs"></a>감사 로그

Azure Active Directory 포털에서 내 직원에서 수행한 작업에 대한 감사 로그를 볼 수 있습니다. 내 직원에서 수행된 작업으로 감사 로그가 생성된 경우 감사 이벤트의 추가 세부 정보 아래에 이 내용이 표시됩니다.

## <a name="next-steps"></a>다음 단계

[내 직원 사용자 문서](../user-help/my-staff-team-manager.md)
[관리 단위 문서](directory-administrative-units.md)
