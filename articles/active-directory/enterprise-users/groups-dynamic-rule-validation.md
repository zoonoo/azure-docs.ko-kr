---
title: 동적 그룹 멤버 자격에 대 한 규칙 유효성 검사 (미리 보기)-Azure AD | Microsoft Docs
description: Azure Active Directory에서 동적 그룹에 대 한 멤버 자격 규칙에 대해 멤버를 테스트 하는 방법입니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ccd17dc4112bd286257a6ed95ecc55dd13af428
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650753"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Azure Active Directory에서 동적 그룹 멤버 자격 규칙 (미리 보기)의 유효성을 검사 합니다.

이제 Azure Active Directory (Azure AD)에서 동적 그룹 규칙의 유효성을 검사 하는 방법을 제공 합니다 (공개 미리 보기). **규칙 유효성 검사** 탭에서 샘플 그룹 멤버에 대해 동적 규칙의 유효성을 검사 하 여 규칙이 예상 대로 작동 하는지 확인할 수 있습니다. 관리자는 동적 그룹 규칙을 만들거나 업데이트할 때 사용자 또는 장치가 그룹의 구성원 인지 여부를 확인 하려고 합니다. 이를 통해 사용자 또는 장치가 규칙 조건을 충족 하는지 여부를 평가 하 고, 멤버 자격이 필요 하지 않을 때 문제 해결을 도울 수 있습니다.

## <a name="step-by-step-walk-through"></a>단계별 단계별 연습

시작 하려면 **Azure Active Directory**  >  **그룹** 으로 이동 합니다. 기존 동적 그룹을 선택 하거나 새 동적 그룹을 만들고 동적 멤버 자격 규칙을 클릭 합니다. 그러면 **규칙 유효성 검사** 탭을 볼 수 있습니다.

![규칙 유효성 검사 탭을 찾고 기존 규칙을 사용 하 여 시작](./media/groups-dynamic-rule-validation/validate-tab.png)

**규칙 유효성 검사** 탭에서 사용자를 선택 하 여 해당 멤버 자격의 유효성을 검사할 수 있습니다. 한 번에 20 명의 사용자 또는 장치를 선택할 수 있습니다.

![사용자를 추가 하 여 기존 규칙의 유효성을 검사 합니다.](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

선택에서 사용자 또는 장치를 선택 하 고를 **선택** 하면 유효성 검사가 자동으로 시작 되 고 유효성 검사 결과가 표시 됩니다.

![규칙 유효성 검사 결과 보기](./media/groups-dynamic-rule-validation/validate-tab-results.png)

결과는 사용자가 그룹의 멤버 인지 여부를 알려 줍니다. 규칙이 유효 하지 않거나 네트워크 문제가 있는 경우 결과는 **알 수 없음** 으로 표시 됩니다. **알 수 없는** 경우 자세한 오류 메시지에는 필요한 문제 및 작업이 설명 되어 있습니다.

![규칙 유효성 검사의 결과에 대 한 세부 정보 보기](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

규칙을 수정 하 고 멤버 자격의 유효성 검사를 트리거할 수 있습니다. 사용자가 그룹의 멤버가 아닌 이유를 확인 하려면 "세부 정보 보기"를 클릭 하 고 확인 세부 정보에는 규칙을 작성 하는 각 식의 결과가 표시 됩니다. **확인** 을 클릭 하 여 종료 합니다.

## <a name="next-steps"></a>다음 단계

- [그룹에 대 한 동적 멤버 자격 규칙](groups-dynamic-membership.md)
