---
title: 동적 그룹 구성원 자격에 대한 규칙 유효성 검사(미리 보기) - Azure AD | 마이크로 소프트 문서
description: Azure Active Directory에서 동적 그룹에 대 한 멤버 자격 규칙에 대해 멤버를 테스트 하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115522"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Azure Active Directory에서 동적 그룹 구성원 규칙(미리 보기) 유효성 검사

Azure Active Directory(Azure AD)는 이제 공용 미리 보기에서 동적 그룹 규칙의 유효성을 검사하는 방법을 제공합니다. 규칙 **유효성 검사** 탭에서 샘플 그룹 구성원에 대해 동적 규칙의 유효성을 검사하여 규칙이 예상대로 작동하는지 확인할 수 있습니다. 동적 그룹 규칙을 만들거나 업데이트할 때 관리자는 사용자 또는 장치가 그룹의 구성원인지 여부를 알고 싶어합니다. 이렇게 하면 사용자 또는 장치가 규칙 기준을 충족하는지 여부를 평가하고 멤버 자격이 예상되지 않을 때 문제 해결에 도움이 됩니다.

## <a name="step-by-step-walk-through"></a>단계별 워크스루

시작하려면 **Azure Active 디렉터리** > **그룹으로**이동하십시오. 기존 동적 그룹을 선택하거나 새 동적 그룹을 만들고 동적 구성원 자격 규칙을 클릭합니다. 그런 다음 **규칙 유효성 검사** 탭을 볼 수 있습니다.

![규칙 유효성 검사 탭을 찾고 기존 규칙으로 시작](./media/groups-dynamic-rule-validation/validate-tab.png)

**규칙 유효성 검사** 탭에서 구성원자격의 유효성을 검사할 사용자를 선택할 수 있습니다. 한 번에 20명의 사용자 또는 장치를 선택할 수 있습니다.

![사용자를 추가하여 기존 규칙의 유효성을 검사합니다.](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

선택기에서 사용자 또는 장치를 선택하면 **유효성**검사가 자동으로 시작되고 유효성 검사 결과가 나타납니다.

![규칙 유효성 검사 결과 보기](./media/groups-dynamic-rule-validation/validate-tab-results.png)

결과는 사용자가 그룹의 구성원인지 여부를 알려줍니다. 규칙이 유효하지 않거나 네트워크 문제가 있는 경우 결과는 **알 수 없음으로**표시됩니다. **알 수 없는**경우 자세한 오류 메시지에 필요한 문제 및 작업이 설명됩니다.

![규칙 유효성 검사 결과에 대한 세부 정보 보기](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

규칙을 수정할 수 있으며 멤버 자격의 유효성 검사가 트리거됩니다. 사용자가 그룹의 구성원이 아닌 이유를 보려면 "세부 정보 보기"를 클릭하고 확인 세부 정보를 클릭하면 규칙을 구성하는 각 식의 결과가 표시됩니다. **확인을** 클릭하여 종료합니다.

## <a name="next-steps"></a>다음 단계

- [그룹에 대한 동적 멤버 자격 규칙](groups-dynamic-membership.md)
