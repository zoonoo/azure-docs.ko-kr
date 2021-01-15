---
title: Azure Active Directory 조건부 액세스를 사용 하 여 외부 액세스 관리
description: Azure Active Directory 조건부 액세스 정책을 사용 하 여 리소스에 대 한 외부 액세스를 보호 하는 방법입니다.
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
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222312"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>조건부 액세스 정책을 사용 하 여 외부 액세스 관리 

[조건부 액세스](../conditional-access/overview.md) 는 Azure AD에서 신호를 보내고 정책을 적용 하며 사용자가 리소스에 대 한 액세스를 허용 해야 하는지 여부를 결정 하는 데 사용 하는 도구입니다. 조건부 액세스 정책 (조건부 액세스 정책)을 만들고 사용 하는 방법에 대 한 자세한 내용은 [조건부 액세스 배포 계획](../conditional-access/plan-conditional-access.md)을 참조 하세요. 

![조건부 액세스 신호 및 결정 다이어그램](media/secure-external-access//7-conditional-access-signals.png)



이 문서에서는 외부 사용자에 게 조건부 액세스 정책을 적용 하는 방법을 설명 하며 [권한 관리](../governance/entitlement-management-overview.md) 기능에 대 한 액세스 권한이 없는 것으로 가정 합니다. 조건부 액세스 정책은 자격 관리와 함께 사용 될 수 있습니다.

이 문서 집합의 앞부분에서 설명한 [보안 계획을 만들었습니다](3-secure-access-plan.md) .

* 응용 프로그램 및 리소스는 동일한 보안 요구 사항을 가지 며 액세스를 위해 그룹화 할 수 있습니다.

* 외부 사용자에 대 한 로그인 요구 사항

이 계획을 사용 하 여 외부 액세스에 대 한 조건부 액세스 정책을 만듭니다. 

> [!IMPORTANT]
> 일부 외부 사용자 테스트 계정을 만들어 모든 외부 사용자에 게 적용 하기 전에 만든 정책을 테스트할 수 있습니다.

## <a name="conditional-access-policies-for-external-access"></a>외부 액세스에 대 한 조건부 액세스 정책

다음은 조건부 액세스 정책을 사용 하 여 외부 액세스를 관리 하는 것과 관련 된 모범 사례입니다.

* 자격 관리에서 연결 된 조직을 사용할 수 없는 경우 작업 하는 각 파트너 조직에 대해 Azure AD 보안 그룹 또는 Microsoft 365 그룹을 만듭니다. 해당 파트너의 모든 사용자를 그룹에 할당 합니다. 그런 다음 조건부 액세스 정책에서 이러한 그룹을 사용할 수 있습니다.

* 가능 하면 몇 가지 조건부 액세스 정책을 만듭니다. 동일한 액세스 요구 사항이 있는 응용 프로그램의 경우 모두 동일한 정책에 추가 합니다.  
‎ 
   > [!NOTE]
   > 조건부 액세스 정책은 최대 250 응용 프로그램에 적용할 수 있습니다. 250 개 이상의 앱에 동일한 액세스 요구 사항이 있는 경우 중복 된 정책을 만듭니다. 정책 A는 앱 1-250에 적용 되 고, 정책 B는 앱 251-500 등에 적용 됩니다.

* 명명 규칙을 사용 하 여 외부 액세스와 관련 된 정책 이름을 명확 하 게 지정 합니다. 하나의 명명 규칙을 *ExternalAccess_actiontaken_AppGroup* 합니다. 예를 ExternalAccess_Block_FinanceApps 합니다.

## <a name="block-all-external-users-from-resources"></a>리소스에서 모든 외부 사용자 차단

외부 사용자가 조건부 액세스 정책을 사용 하 여 특정 리소스 집합에 액세스 하지 못하도록 차단할 수 있습니다. 액세스를 차단 하려는 리소스 집합을 결정 했으면 정책을 만듭니다.

응용 프로그램 집합에 대 한 외부 사용자의 액세스를 차단 하는 정책을 만들려면 다음을 수행 합니다.

1. **Azure Portal** 액세스 하 고 **Azure Active Directory** 을 선택한 다음 **보안** 을 선택 하 고 **조건부 액세스** 를 선택 합니다.

2. **새 정책** 을 선택 하 고 **이름을** 입력 합니다 (예: ExternalAccess_Block_FinanceApps

3. **사용자 및 그룹을** 선택 합니다. 포함 탭에서 **사용자 및 그룹 선택** 을 선택 하 고 **모든 게스트 및 외부 사용자** 를 선택 합니다. 

4. **제외** 를 선택 하 고 관리자 그룹 및 모든 긴급 액세스 (차단) 계정을 입력 합니다.

5. **클라우드 앱 또는 작업** 을 선택 하 고 **앱 선택** 을 선택 하 고 외부 액세스를 차단 하려는 모든 앱을 선택한 다음 **선택** 을 선택 합니다.

6. **조건** 을 선택 하 고, **위치** 를 선택 하 고, 구성에서 **예** 를 선택 하 고, **위치** 를 선택 합니다.

7. 액세스 제어에서 **부여** 를 선택 하 고, 토글을 **블록** 으로 변경한 다음, **선택** 을 선택 합니다.

8. 정책 사용 설정이 **보고 전용** 으로 설정 되어 있는지 확인 한 다음 **만들기** 를 선택 합니다.

## <a name="block-external-access-to-all-except-specific-external-users"></a>특정 외부 사용자를 제외한 모든 사용자에 대 한 외부 액세스 차단

특정 그룹을 제외한 외부 사용자를 차단 하려는 경우가 있을 수 있습니다. 예를 들어 재무 응용 프로그램에서 재무 팀에 대해 작업 하는 경우를 제외 하 고 모든 외부 사용자를 차단 하는 것이 좋습니다. 가상 하드 디스크 파일에 대한 중요 정보를 제공하려면

1. 재무 그룹에 액세스 해야 하는 외부 사용자를 보관할 보안 그룹을 만듭니다.

2. 위의 리소스에서 외부 액세스 차단의 1-3 단계를 수행 합니다.

3. 4 단계에서 제외 하려는 보안 그룹을 재무 앱에서 차단 하지 않도록 추가 합니다.

4. 나머지 단계를 수행 합니다.

## <a name="implement-conditional-access"></a>조건부 액세스 구현

많은 일반적인 조건부 액세스 정책이 문서화 되어 있습니다. 외부 사용자에 맞게 조정할 수 있는 다음 항목을 참조 하세요.

* [모든 사용자에 대 한 Multi-Factor Authentication 필요](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [사용자 위험 기반 조건부 액세스](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [신뢰할 수 없는 네트워크에서 액세스 하기 위한 Multi-Factor Authentication 필요](../conditional-access/untrusted-networks.md) 

* [사용 약관 필요](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>다음 단계

리소스에 대 한 외부 액세스 보안에 대 한 다음 문서를 참조 하세요. 이러한 작업은 나열 된 순서 대로 수행 하는 것이 좋습니다.

1. [외부 액세스를 위해 원하는 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거 버 넌 스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용 하 여 안전 하 게 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용 하 여 안전 하 게 액세스](7-secure-access-conditional-access.md) (여기)

8. [민감도 레이블을 사용 하 여 안전 하 게 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft 팀, OneDrive 및 SharePoint에 대 한 액세스 보호](9-secure-access-teams-sharepoint.md)
