---
title: Azure Stack에 대 한 등록 역할을 만드는 방법
description: 등록에 대 한 전역 관리자를 사용 하지 않는 사용자 지정 역할을 만드는 방법입니다.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.openlocfilehash: 89fe0889e08da2365523b27262e912ff5403f7f1
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54233179"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Azure Stack에 대 한 등록 역할 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure 구독에서 소유자 권한 부여 않으려는 경우 Azure Stack에 등록 하는 사용자 계정에 권한을 할당 하려면 사용자 지정 역할을 만들 수 있습니다.

> [!WARNING]
> 이 보안 포스 처 기능이 아닙니다. 이 시나리오에서 사용 하려는 Azure 구독에 실수로 인 한 변경을 방지 하기 위해 제약 조건. 사용자가이 사용자 지정 역할에 대 한 위임 된 권한을, 사용자 권한이 사용 권한을 편집 권한을 상승 하 합니다. 만 사용자가 신뢰 하는 사용자 지정 역할을 할당 합니다.

Azure Stack을 등록할 때 등록 계정에는 다음 Azure Active Directory 사용 권한 및 Azure 구독 권한 필요 합니다.

* **Azure Active Directory 테 넌 트에서 응용 프로그램 등록 사용 권한:** 관리자 응용 프로그램 등록 권한을 가집니다. 사용자에 대 한 권한 테 넌 트의 모든 사용자에 대 한 전역 설정입니다. 보기 또는 변경 설정 참조 [Azure AD 리소스에 액세스할 수 있는 응용 프로그램 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)합니다.

    *사용자는 응용 프로그램을 등록할 수 있습니다* 으로 설정 되어 있어야 합니다 **예** 사용자 계정을 Azure Stack 등록에 사용 하도록 합니다. 앱 등록 설정이로 설정 되어 있으면 **No**, 사용자 계정을 사용할 수 없습니다 하 고 Azure Stack 등록 하려면 전역 관리자 계정을 사용 해야 합니다.

* **Azure 구독 사용 권한 집합:** 소유자 그룹의 사용자에 충분 한 권한이 있습니다. 다른 계정에 대 한 다음 섹션에 설명 된 대로 사용자 지정 역할을 할당 하 여 권한 집합을 할당할 수 있습니다.

## <a name="create-a-custom-role-using-powershell"></a>PowerShell을 사용 하 여 사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 [소유자](../role-based-access-control/built-in-roles.md#owner) 또는 [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator)와 같이 모든 `AssignableScopes`에 대한 `Microsoft.Authorization/roleDefinitions/write` 권한이 있어야 합니다. 다음 JSON 템플릿을 사용 하 여 사용자 지정 역할 정의 간소화 합니다. 이 템플릿은 필요한 읽기 및 Azure Stack 등록에 대 한 쓰기 액세스를 허용 하는 사용자 지정 역할을 만듭니다.

1. JSON 파일을 만듭니다. 예: `C:\CustomRoles\registrationrole.json`
2. 파일에 다음 JSON을 추가합니다. <SubscriptionID> 를 Azure 구독 ID로 바꿉니다.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. PowerShell에서 Azure Resource Manager를 사용 하는 Azure에 연결 합니다. 메시지가 표시 되 면 인증와 같은 충분 한 권한이 있는 계정 사용 [소유자](../role-based-access-control/built-in-roles.md#owner) 하거나 [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator)합니다.

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. 구독에 역할을 추가 하려면 **New-azurermroledefinition** JSON 템플릿 파일을 지정 합니다.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>등록 역할에 사용자 할당

등록 사용자 지정 역할을 만든 후에 Azure Stack 등록 역할 사용자를 할당 합니다.

1. 와 같은 권한-위임 하려면 Azure 구독에 충분 한 권한이 있는 계정으로 로그인 [소유자](../role-based-access-control/built-in-roles.md#owner) 하거나 [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator) 합니다.
2. **구독**를 선택 **액세스 제어 (IAM) > 역할 할당 추가**합니다.
3. **역할**를 만든 사용자 지정 역할을 선택 *Azure Stack 등록 역할*입니다.
4. 역할에 할당 하려는 사용자를 선택 합니다.
5. 선택 **저장할** 선택한 사용자 역할에 할당 합니다.

    ![역할을 할당 하는 사용자를 선택 합니다.](media/azure-stack-registration-role/assign-role.png)

사용자 지정 역할을 사용 하 여 자세한 내용은 [RBAC 및 Azure portal을 사용 하 여 액세스를 관리](../role-based-access-control/role-assignments-portal.md)합니다.

## <a name="next-steps"></a>다음 단계

[Azure를 사용 하 여 Azure Stack 등록](azure-stack-registration.md)
