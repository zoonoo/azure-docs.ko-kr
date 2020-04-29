---
title: Azure 리소스에 대한 사용자 지정 역할 | Microsoft Docs
description: Azure 리소스에 대한 세분화된 액세스 관리를 위해 RBAC(역할 기반 액세스 제어)를 사용하여 사용자 지정 역할을 만드는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062181"
---
# <a name="custom-roles-for-azure-resources"></a>Azure 리소스에 대한 사용자 지정 역할

> [!IMPORTANT]
> 에 관리 그룹을 추가 `AssignableScopes` 하는 것은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure 리소스에 대한 기본 제공 역할](built-in-roles.md)이 조직의 특정 요구 사항을 충족하지 않는 경우 사용자 지정 역할을 만들 수 있습니다. 기본 제공 역할과 마찬가지로 관리 그룹, 구독 및 리소스 그룹 범위에서 사용자, 그룹 및 서비스 사용자에 게 사용자 지정 역할을 할당할 수 있습니다.

동일한 Azure AD 디렉터리를 신뢰 하는 구독 간에 사용자 지정 역할을 공유할 수 있습니다. 디렉터리 당 사용자 지정 역할은 **5000** 개로 제한 됩니다. (Azure 독일 및 Azure 중국 21Vianet의 경우 제한은 2000 사용자 지정 역할입니다.) Azure Portal (미리 보기), Azure PowerShell, Azure CLI 또는 REST API를 사용 하 여 사용자 지정 역할을 만들 수 있습니다.

## <a name="custom-role-example"></a>사용자 지정 역할 예제

다음은 JSON 형식으로 표시된 대로 사용자 지정 역할을 보여줍니다. 이 사용자 지정 역할은 가상 머신의 모니터링 및 재시작에 사용할 수 있습니다.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

사용자 지정 역할을 만들면 Azure Portal에 주황색 리소스 아이콘이 표시됩니다.

![사용자 지정 역할 아이콘](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>사용자 지정 역할을 만드는 단계

1. 사용자 지정 역할을 만들 방법을 결정 합니다.

    [Azure Portal](custom-roles-portal.md) (미리 보기), [Azure PowerShell](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)또는 [REST API](custom-roles-rest.md)를 사용 하 여 사용자 지정 역할을 만들 수 있습니다.

1. 필요한 권한 결정

    사용자 지정 역할을 만드는 경우 권한을 정의하는 데 사용할 수 있는 리소스 공급자 작업을 알고 있어야 합니다. 작업 목록을 보려면 [Azure Resource Manager 리소스 공급자 작업](resource-provider-operations.md)을 참조 하세요. `Actions` [역할 정의](role-definitions.md)의 또는 `NotActions` 속성에 작업을 추가 합니다. 데이터 작업을 수행 하는 경우 `DataActions` 또는 `NotDataActions` 속성에 해당 작업을 추가 합니다.

1. 사용자 지정 역할 만들기

    일반적으로 기존 기본 제공 역할로 시작한 다음, 필요에 따라 수정합니다. 그런 다음, [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) 또는 [az role definition create](/cli/azure/role/definition#az-role-definition-create) 명령을 사용하여 사용자 지정 역할을 만듭니다. 사용자 지정 역할을 만들려면 [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)와 같이 모든 `AssignableScopes`에 대한 `Microsoft.Authorization/roleDefinitions/write` 권한이 있어야 합니다.

1. 사용자 지정 역할 테스트

    사용자 지정 역할이 있으면 해당 역할을 테스트하여 예상대로 작동하는지 확인해야 합니다. 나중에 조정해야 하는 경우 사용자 지정 역할을 업데이트할 수 있습니다.

사용자 지정 역할을 만드는 방법에 대한 단계별 자습서는 [자습서: Azure PowerShell을 사용하여 사용자 지정 역할 만들기](tutorial-custom-role-powershell.md) 또는 [자습서: Azure CLI를 사용하여 사용자 지정 역할 만들기](tutorial-custom-role-cli.md)를 참조하세요.

## <a name="custom-role-properties"></a>사용자 지정 역할 속성

사용자 지정 역할의 속성은 다음과 같습니다.

| 속성 | 필수 | Type | 설명 |
| --- | --- | --- | --- |
| `Name` | 예 | String | 사용자 지정 역할의 표시 이름입니다. 역할 정의는 관리 그룹 또는 구독 수준 리소스 이지만 동일한 Azure AD 디렉터리를 공유 하는 여러 구독에서 역할 정의를 사용할 수 있습니다. 표시 이름은 Azure AD 디렉터리의 범위에서 고유해야 합니다. 문자, 숫자, 공백 및 특수 문자를 포함할 수 있습니다. 최대 문자 수는 128자입니다. |
| `Id` | 예 | String | 사용자 지정 역할의 고유 ID입니다. Azure PowerShell 및 Azure CLI의 경우 이 ID는 새 역할을 만들 때 자동으로 생성됩니다. |
| `IsCustom` | 예 | String | 사용자 지정 역할인지 여부를 나타냅니다. 사용자 지정 역할인 경우 `true`로 설정합니다. |
| `Description` | 예 | String | 사용자 지정 역할에 대한 설명입니다. 문자, 숫자, 공백 및 특수 문자를 포함할 수 있습니다. 최대 문자 수는 1,024자입니다. |
| `Actions` | 예 | String[] | 역할에서 수행할 수 있는 관리 작업을 지정하는 문자열 배열입니다. 자세한 내용은 [Actions](role-definitions.md#actions)를 참조하세요. |
| `NotActions` | 아니요 | String[] | 허용된 `Actions`에서 제외되는 관리 작업을 지정하는 문자열 배열입니다. 자세한 내용은 [NotActions](role-definitions.md#notactions)를 참조하세요. |
| `DataActions` | 아니요 | String[] | 역할에서 해당 개체 내의 데이터에 대해 수행할 수 있는 데이터 작업을 지정하는 문자열 배열입니다. 를 사용 `DataActions`하 여 사용자 지정 역할을 만드는 경우 해당 역할은 관리 그룹 범위에서 할당할 수 없습니다. 자세한 내용은 [Dataactions](role-definitions.md#dataactions)를 참조 하세요. |
| `NotDataActions` | 아니요 | String[] | 허용된 `DataActions`에서 제외되는 데이터 작업을 지정하는 문자열 배열입니다. 자세한 내용은 [Notdataactions](role-definitions.md#notdataactions)를 참조 하세요. |
| `AssignableScopes` | 예 | String[] | 할당에 사용할 수 있는 사용자 지정 역할에 대한 범위를 지정하는 문자열 배열입니다. 사용자 지정 역할에 `AssignableScopes` 는 하나의 관리 그룹만 정의할 수 있습니다. 에 관리 그룹을 추가 `AssignableScopes` 하는 것은 현재 미리 보기 상태입니다. 자세한 내용은 [AssignableScopes](role-definitions.md#assignablescopes)를 참조하세요. |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>사용자 지정 역할을 생성, 삭제, 업데이트 또는 볼 수 있는 사용자

기본 제공 역할과 마찬가지로, `AssignableScopes` 속성은 할당에 사용할 수 있는 역할에 대한 범위를 지정합니다. 또한 사용자 지정 역할에 대한 `AssignableScopes` 속성은 사용자 지정 역할을 생성, 삭제, 업데이트 또는 볼 수 있는 사용자도 제어합니다.

| 작업 | 작업(Operation) | Description |
| --- | --- | --- |
| 사용자 지정 역할 만들기/삭제 | `Microsoft.Authorization/ roleDefinitions/write` | 사용자 지정 역할의 모든 `AssignableScopes`에 이 작업이 부여된 사용자는 해당 범위에서 사용할 사용자 지정 역할을 만들거나 삭제할 수 있습니다. 예를 들어 관리 그룹, 구독 및 리소스 그룹의 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 가 있습니다. |
| 사용자 지정 역할 업데이트 | `Microsoft.Authorization/ roleDefinitions/write` | 사용자 지정 역할의 모든 `AssignableScopes`에 이 작업이 부여된 사용자는 해당 범위에의 사용자 지정 역할을 업데이트할 수 있습니다. 예를 들어 관리 그룹, 구독 및 리소스 그룹의 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 가 있습니다. |
| 사용자 지정 역할 보기 | `Microsoft.Authorization/ roleDefinitions/read` | 범위에서 이 작업이 부여된 사용자는 해당 범위에서 할당에 사용할 수 있는 사용자 지정 역할을 볼 수 있습니다. 모든 기본 제공 역할을 통해 사용자 지정 역할을 할당할 수 있습니다. |

## <a name="custom-role-limits"></a>사용자 지정 역할 제한

다음 목록에서는 사용자 지정 역할에 대 한 제한을 설명 합니다.

- 각 디렉터리에는 최대 **5000** 개의 사용자 지정 역할이 있을 수 있습니다.
- Azure 독일 및 Azure 중국 21Vianet에는 각 디렉터리에 대해 최대 2000 개의 사용자 지정 역할이 있을 수 있습니다.
- 루트 범위 ( `AssignableScopes` `"/"`)로 설정할 수 없습니다.
- 사용자 지정 역할에 `AssignableScopes` 는 하나의 관리 그룹만 정의할 수 있습니다. 에 관리 그룹을 추가 `AssignableScopes` 하는 것은 현재 미리 보기 상태입니다.
- 를 사용 하 `DataActions` 는 사용자 지정 역할은 관리 그룹 범위에서 할당할 수 없습니다.
- Azure Resource Manager는 관리 그룹이 역할 정의의 할당 가능한 범위에 있는지 확인 하지 않습니다.

사용자 지정 역할 및 관리 그룹에 대 한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
- [Azure Portal (미리 보기)를 사용 하 여 Azure 사용자 지정 역할 만들기 또는 업데이트](custom-roles-portal.md)
- [Azure 리소스에 대한 역할 정의 이해](role-definitions.md)
- [Azure 리소스에 대한 RBAC 문제 해결](troubleshooting.md)
