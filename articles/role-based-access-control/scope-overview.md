---
title: Azure RBAC의 범위 이해
description: Azure RBAC (역할 기반 액세스 제어)의 범위와 리소스에 대 한 범위를 결정 하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: ad906e3665c6ffc354cf6292c2559d1184037594
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856156"
---
# <a name="understand-scope-for-azure-rbac"></a>Azure RBAC의 범위 이해

*범위* 는 액세스가 적용 되는 리소스 집합입니다. 역할을 할당할 때 보안 주체에 게 필요한 액세스만 부여할 수 있도록 범위를 이해 하는 것이 중요 합니다. 범위를 제한 하 여 보안 주체의 보안이 손상 되는 경우 위험에 노출 되는 리소스를 제한 합니다.

## <a name="scope-levels"></a>범위 수준

Azure에서 [관리 그룹](../governance/management-groups/overview.md), 구독, [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups)및 리소스의 네 가지 수준으로 범위를 지정할 수 있습니다. 범위는 부모-자식 관계로 구조화되어 있습니다. 계층의 각 수준은 범위를 보다 구체적으로 만듭니다. 이러한 범위 수준에서 역할을 할당할 수 있습니다. 선택한 수준에 따라 역할이 적용 되는 방식이 결정 됩니다. 하위 수준은 상위 수준에서 역할 권한을 상속 합니다. 

![역할 할당 범위](./media/scope-overview/rbac-scope-no-label.png)

관리 그룹은 구독의 범위 수준 이지만 관리 그룹은 더 복잡 한 계층을 지원 합니다. 다음 다이어그램은 사용자가 정의할 수 있는 관리 그룹 및 구독의 계층 구조 예를 보여 줍니다. 관리 그룹에 대 한 자세한 내용은 [Azure 관리 그룹 이란?](../governance/management-groups/overview.md)을 참조 하세요.

![관리 그룹 및 구독 계층 구조](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>범위 형식

명령줄을 사용 하 여 역할 할당을 추가 하는 경우 범위를 지정 해야 합니다. 명령줄 도구의 경우 범위는 역할 할당의 정확한 범위를 식별 하는 잠재적으로 긴 문자열입니다. Azure Portal에서이 범위는 일반적으로 *리소스 ID*로 나열 됩니다.

범위는 슬래시 (/) 문자로 구분 된 일련의 식별자로 구성 됩니다. 이 문자열을 다음 계층 구조의 표현으로 간주할 수 있으며, 여기서 자리 표시자(`{}`) 없는 텍스트는 고정 식별자입니다.

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}`는 사용할 구독의 ID(GUID)입니다.
- `{resourcesGroupName}`은 포함하는 리소스 그룹의 이름입니다.
- `{providerName}` 리소스를 처리 하는 [리소스 공급자](../azure-resource-manager/management/azure-services-resource-providers.md) 의 이름이 `{resourceType}` 며, `{resourceSubType*}` 해당 리소스 공급자 내에서 추가 수준을 식별 합니다.
- `{resourceName}`은 특정 리소스를 식별하는 문자열의 마지막 부분입니다.

관리 그룹은 구독 상위 수준 이며 가장 광범위 한 (가장 구체적인) 범위를 포함 합니다. 이 수준의 역할 할당은 관리 그룹 내의 구독에 적용 됩니다. 관리 그룹의 범위는 다음과 같은 형식입니다.

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>범위 예

> [!div class="mx-tableFixed"]
> | 범위 | 예제 |
> | --- | --- |
> | 관리 그룹 | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Subscription | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Resource group | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | 리소스 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>리소스 범위를 확인 하는 방법

관리 그룹, 구독 또는 리소스 그룹에 대 한 범위를 결정 하는 것은 매우 간단 합니다. 이름 및 구독 ID를 알고 있어야 합니다. 그러나 리소스에 대 한 범위를 결정 하는 작업은 좀 더 많은 작업을 수행 합니다. 리소스의 범위를 결정할 수 있는 몇 가지 방법은 다음과 같습니다.

- Azure Portal에서 리소스를 연 다음 속성을 확인 합니다. 리소스는 범위를 결정할 수 있는 **리소스 ID** 를 나열 해야 합니다. 예를 들어 저장소 계정에 대 한 리소스 Id는 다음과 같습니다.

    ![Azure Portal의 저장소 계정에 대 한 리소스 Id](./media/scope-overview/scope-resource-id.png)

- 또 다른 방법은 Azure Portal를 사용 하 여 리소스 범위에서 일시적으로 역할을 할당 한 다음 [Azure PowerShell](role-assignments-list-powershell.md) 또는 [Azure CLI](role-assignments-list-cli.md) 를 사용 하 여 역할 할당을 나열 하는 것입니다. 출력에서 범위는 속성으로 나열 됩니다.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>다음 단계

- [역할 할당을 추가하는 단계](role-assignments-steps.md)
- [Azure 서비스의 리소스 공급자](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 관리 그룹이란?](../governance/management-groups/overview.md)
