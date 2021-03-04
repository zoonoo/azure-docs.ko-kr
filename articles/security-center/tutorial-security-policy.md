---
title: 보안 정책 작업 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 보안 정책을 사용 하는 방법을 설명 합니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 6ecedc20cf6924a82b6b4640d3caa75bc5958de0
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101327"
---
# <a name="manage-security-policies"></a>보안 정책 관리

이 문서에서는 보안 정책을 구성하는 방법 및 Security Center에서 보안 정책을 보는 방법을 설명합니다. 

## <a name="who-can-edit-security-policies"></a>보안 정책은 누가 편집할 수 있나요?

REST API를 통해 또는 Windows PowerShell을 사용하여 Azure Policy 포털을 통해 보안 정책을 편집할 수 있습니다.

Security Center는 azure 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 역할을 제공 하는 azure 역할 기반 액세스 제어 (Azure RBAC)를 사용 합니다. 사용자가 Security Center 열면 액세스할 수 있는 리소스와 관련 된 정보만 표시 됩니다. 즉, 사용자에 게 리소스 구독에 대 한 *소유자*, *참가자* 또는 *읽기 권한자* 역할이 할당 됩니다. 또한 두 가지 특정 Security Center 역할이 있습니다.

- **보안 읽기 권한자**: 권장 사항, 경고, 정책 및 상태와 같은 Security Center 항목을 볼 수 있는 권한을 가집니다. 변경할 수 없습니다.
- **보안 관리자**: *보안 판독기* 와 동일한 보기 권한을 가집니다. 보안 정책을 업데이트 하 고 경고를 해제할 수도 있습니다.

## <a name="manage-your-security-policies"></a>보안 정책 관리

Security Center에서 보안 정책을 보려면

1. **Security Center** 대시보드에서 **보안 정책** 을 선택합니다.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="정책 관리 페이지":::

   **정책 관리** 화면에서 관리 그룹 구조뿐만 아니라 관리 그룹, 구독 및 작업 영역 수를 확인할 수 있습니다.

1. 확인하려는 구독 또는 관리 그룹의 정책을 선택합니다.

1. 해당 구독 또는 관리 그룹에 대 한 보안 정책 페이지가 나타납니다. 사용 가능한 정책과 할당 된 정책을 표시 합니다.

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Security Center의 보안 정책 페이지" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > 기본 정책과 함께 "MG 상속" 이라는 레이블이 있으면 정책이 관리 그룹에 할당 되 고 사용자가 보고 있는 구독에서 상속 된 것입니다.

1. 이 페이지에서 사용할 수 있는 옵션 중에서 선택 합니다.

    1. 산업 표준을 사용 하려면 **추가 표준** 을 선택 합니다. 자세한 내용은 [규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)을 참조 하세요.

    1. 사용자 지정 이니셔티브를 할당 하 고 관리 하려면 **사용자 지정 이니셔티브 추가** 를 선택 합니다. 자세한 내용은 [사용자 지정 보안 이니셔티브 및 정책 사용](custom-security-policies.md)을 참조 하세요.

    1. 기본 이니셔티브를 보고 편집 하려면 **유효한 정책 보기** 를 선택 하 고 아래에 설명 된 대로 진행 합니다. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="유효 정책 화면":::

       이 **보안 정책** 화면은 선택한 구독 또는 관리 그룹에 할당 된 정책에 의해 수행 된 작업을 반영 합니다.
       
       * 맨 위에 있는 링크를 사용 하 여 구독 또는 관리 그룹에 적용 되는 정책 **할당** 을 엽니다. 이러한 링크를 사용 하 여 할당에 액세스 하 고 정책을 편집 하거나 사용 하지 않도록 설정할 수 있습니다. 예를 들어 특정 정책 할당이 끝점 보호를 효과적으로 거부 하는 것으로 확인 되 면이 링크를 사용 하 여 정책을 편집 하거나 사용 하지 않도록 설정 합니다.
       
       * 정책 목록에서 구독 또는 관리 그룹에 정책을 효과적으로 적용하는 것을 확인할 수 있습니다. 범위에 적용 되는 각 정책의 설정을 고려 하 여 정책에서 수행 하는 작업의 누적 결과가 표시 됩니다. 예를 들어 한 할당에서 정책 할당을 사용 하지 않도록 설정 했지만 다른 AuditIfNotExist로 설정 된 경우 누적 효과는 AuditIfNotExist 적용 됩니다. 더 활성화된 효과가 항상 우선합니다.
       
       * 정책의 효과는 Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled일 수 있습니다. 효과를 적용하는 방법에 대한 자세한 내용은 [Policy 효과 이해](../governance/policy/concepts/effects.md)를 참조하세요.

       > [!NOTE]
       > 할당된 정책을 보는 경우 여러 할당을 확인할 수 있으며 각 할당이 자체적으로 구성되는 방법도 확인할 수 있습니다.


## <a name="disable-security-policies-and-disable-recommendations"></a>보안 정책 사용 안 함 및 권장 구성 사용 안 함

보안 이니셔티브 환경에 적합 하지 않은 권장 사항을 트리거하는 경우 해당 권장 구성이 다시 표시 되지 않도록 할 수 있습니다. 권장 사항을 사용 하지 않도록 설정 하려면 권장 구성을 생성 하는 특정 정책을 사용 하지 않도록 설정 합니다.

사용 하지 않도록 설정할 권장 사항은 Security Center의 규정 준수 도구를 사용 하 여 적용 한 규정 표준에 필요한 경우에도 표시 됩니다. 기본 제공 이니셔티브에서 정책을 사용 하지 않도록 설정한 경우에도 규정 표준 이니셔티브의 정책은 규정 준수에 필요한 경우 계속 해 서 권장 사항을 트리거합니다. 규제 표준 이니셔티브에서 정책을 사용 하지 않도록 설정할 수 없습니다.

권장 사항에 대 한 자세한 내용은 [보안 권장 사항 관리](security-center-recommendations.md)를 참조 하세요.

1. Security Center의 **정책 & 준수** 섹션에서 **보안 정책** 을 선택 합니다.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Azure Security Center에서 정책 관리 프로세스 시작":::

2. 권장 사항을 사용 하지 않도록 설정할 구독 또는 관리 그룹을 선택 합니다.

   > [!NOTE]
   > 관리 그룹은 해당 구독에 정책을 적용합니다. 따라서 정책을 사용하지 않도록 설정한 구독이 동일한 정책을 사용하는 관리 그룹에 속해 있는 경우 정책 권장 사항을 계속 받게 됩니다. 관리 수준에서 계속 정책이 적용되고 권장 사항이 생성됩니다.

1. **유효 정책 보기** 를 선택 합니다.

    :::image type="content" source="./media/tutorial-security-policy/view-effective-policy.png" alt-text="구독에 할당 된 유효 정책을 여는 방법":::

1. 할당 된 정책을 선택 합니다.

   ![정책 선택](./media/tutorial-security-policy/security-policy.png)

1. **매개 변수** 섹션에서 사용 하지 않도록 설정할 권장 사항을 호출 하는 정책을 검색 하 고 드롭다운 목록에서 **사용 안 함** 을 선택 합니다.

   ![정책 사용 안 함](./media/tutorial-security-policy/disable-policy.png)

1. **저장** 을 선택합니다.

   > [!NOTE]
   > 정책 사용 안 함 변경을 적용 하는 데 최대 12 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 페이지에서는 보안 정책에 대해 설명 했습니다. 관련 정보는 다음 페이지를 참조 하세요.

- [PowerShell을 사용 하 여 정책을 설정 하는 방법 알아보기](../governance/policy/assign-policy-powershell.md)
- [Azure Policy에서 보안 정책을 편집 하는 방법에 대해 알아봅니다.](../governance/policy/tutorials/create-and-manage.md)
- [Azure Policy를 사용 하 여 구독 또는 관리 그룹에서 정책을 설정 하는 방법에 대해 알아봅니다.](../governance/policy/overview.md)
- [관리 그룹의 모든 구독에서 Security Center를 사용 하도록 설정 하는 방법에 대해 알아봅니다.](onboard-management-group.md)