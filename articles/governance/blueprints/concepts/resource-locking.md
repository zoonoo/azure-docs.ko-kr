---
title: 리소스 잠금 이해
description: 청사진을 할당할 때 리소스를 보호하기 위한 Azure Blueprints의 잠금 옵션에 대해 알아봅니다.
ms.date: 04/22/2021
ms.topic: conceptual
ms.openlocfilehash: 3ad6bbc80818a1ba749607c44403e3efbdb064c9
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107897760"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Azure Blueprints의 리소스 잠금 이해

일관성 있는 환경을 원하는 규모로 만든다고 해도 해당 일관성을 유지할 수 있는 메커니즘이 있어야 환경을 적절하게 활용할 수 있습니다. 이 문서에서는 Azure Blueprints에서 리소스 잠금이 작동하는 방식에 대해 설명합니다. 리소스 잠금 및 _거부 할당_ 적용의 예를 보려면 [새 리소스 보호](../tutorials/protect-new-resources.md) 자습서를 참조하세요.

> [!NOTE]
> Azure Blueprints에서 배포한 리소스 잠금은 청사진 할당으로 배포된 [비확장 리소스](../../../azure-resource-manager/templates/scope-extension-resources.md)에만 적용됩니다. 이미 존재하는 리소스 그룹의 리소스와 같이 기존 리소스에는 잠금이 추가되지 않습니다.

## <a name="locking-modes-and-states"></a>잠금 모드 및 상태

잠금 모드는 청사진 할당에 적용되며 **잠그지 않음**, **읽기 전용** 또는 **삭제 안 함** 의 세 가지 옵션이 있습니다. 잠금 모드는 청사진 할당을 수행하는 동안 아티팩트 배포 중에 구성됩니다. 청사진 할당을 업데이트하여 다른 잠금 모드를 설정할 수 있습니다.
그러나 잠금 모드는 Azure Blueprints 외부에서 변경할 수 없습니다.

청사진 할당의 아티팩트에 의해 생성되는 리소스의 상태는 **잠겨있지 않음**, **읽기 전용**, **편집/삭제할 수 없음** 또는 **삭제할 수 없음** 의 네 가지입니다. 각 아티팩트 형식은 **잠겨있지 않음** 상태일 수 있습니다. 다음 표를 사용하여 리소스의 상태를 확인할 수 있습니다.

|모드|아티팩트 리소스 형식|주|Description|
|-|-|-|-|
|잠그지 않음|*|잠겨있지 않음|리소스는 Azure Blueprints에 의해 보호되지 않습니다. 이 상태는 또한 청사진 할당 외부에서 **읽기 전용** 또는 **삭제 안 함** 리소스 그룹 아티팩트에 추가된 리소스에 사용됩니다.|
|읽기 전용|리소스 그룹|편집/삭제할 수 없음|리소스 그룹이 읽기 전용이며 리소스 그룹의 태그를 수정할 수 없습니다. **잠겨 있지 않음** 리소스는 이 리소스 그룹에서 추가, 이동, 변경 또는 삭제할 수 있습니다.|
|읽기 전용|리소스 그룹이 아님|읽기 전용|리소스는 어떤 방식으로든 변경할 수 없습니다. 변경되지 않으며 삭제할 수 없습니다.|
|삭제 안 함|*|삭제할 수 없음|리소스를 변경할 수 있지만 삭제할 수 없습니다. **잠겨 있지 않음** 리소스는 이 리소스 그룹에서 추가, 이동, 변경 또는 삭제할 수 있습니다.|

## <a name="overriding-locking-states"></a>잠금 상태 재정의

일반적으로 '소유자' 역할과 같이 구독에 대한 적절한 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../../role-based-access-control/overview.md)가 있는 사용자가 리소스를 변경하거나 삭제할 수 있습니다. Azure Blueprints가 배포된 할당의 일부로 잠금을 적용하는 경우는 여기에 해당하지 않습니다. 할당이 **읽기 전용** 또는 **삭제 안 함** 옵션으로 설정된 경우에는 구독 소유자여도 보호된 리소스에서 차단된 작업을 수행할 수 없습니다.

이 보안 조치는 정의된 청사진과 해당 청사진을 통해 생성하려는 환경을 실수로/프로그래밍 방식으로 삭제하거나 변경하는 상황을 방지하여 일관성을 유지합니다.

### <a name="assign-at-management-group"></a>관리 그룹에서 할당

구독 소유자가 청사진 할당을 제거하지 못하도록 방지하는 유일한 옵션은 청사진을 관리 그룹에 할당하는 것입니다. 이 시나리오에서는 관리 그룹의 **소유자** 에게만 청사진 할당을 제거하는 데 필요한 권한이 있습니다.

구독 대신 관리 그룹에 청사진을 할당하기 위해 REST API 호출은 다음과 같이 변경됩니다.

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

`{assignmentMG}`에 의해 정의된 관리 그룹은 관리 그룹 계층 구조 내에 있거나 청사진 정의가 저장된 동일한 관리 그룹이어야 합니다.

청사진 할당의 요청 본문은 다음과 같습니다.

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

이 요청 본문과 구독에 할당된 요청 본문의 주요 차이점은 `properties.scope` 속성입니다. 이 필수 속성은 청사진 할당이 적용되는 구독으로 설정되어야 합니다. 구독은 청사진 할당이 저장된 관리 그룹 계층의 직계 하위 항목이어야 합니다.

> [!NOTE]
> 관리 그룹 범위에 할당된 청사진은 여전히 구독 수준 청사진 할당으로 작동합니다. 유일한 차이점은 구독 소유자가 할당 및 연관된 잠금을 제거하지 못하도록 청사진 할당을 저장하는 위치입니다.

## <a name="removing-locking-states"></a>잠금 상태 제거

할당에서 보호되는 리소스를 수정하거나 삭제해야 하는 경우 두 가지 방법이 있습니다.

- 청사진 할당을 **잠그지 않음** 잠금 모드로 업데이트
- 청사진 할당 삭제

할당을 제거하면 Azure Blueprints를 통해 생성된 잠금이 제거됩니다. 그러나 리소스는 그대로 남아 있으므로 일반적인 방법으로 삭제해야 합니다.

## <a name="how-blueprint-locks-work"></a>청사진 잠금의 작동 방식

할당에서 **읽기 전용** 또는 **삭제 안 함** 옵션을 선택한 경우 Azure RBAC [거부 할당](../../../role-based-access-control/deny-assignments.md) 거부 동작이 청사진 할당 중에 아티팩트 리소스에 적용됩니다. 거부 동작은 청사진 할당의 관리 ID를 통해 추가되며 같은 관리 ID를 통해서만 아티팩트 리소스에서 제거할 수 있습니다. 이 보안 조치는 잠금 메커니즘을 적용하고, Azure Blueprints 외부에서 청사진을 제거하지 못하도록 합니다.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="리소스 그룹에 대한 액세스 제어(IAM) 페이지 및 거부 할당 탭의 스크린샷." border="false":::

각 모드의 [거부 할당 속성](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties)은 다음과 같습니다.

|모드 |Permissions.Actions |Permissions.NotActions |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|읽기 전용 |**\** _ |_ *\*/read **<br />** Microsoft.Authorization/locks/delete **<br />** Microsoft.Network/virtualNetwork/subnets/join/action** |SystemDefined (Everyone) |**excludedPrincipals** 의 청사진 할당 및 사용자 정의 |리소스 그룹 - _true_; 리소스 - _false_ |
|삭제 안 함 |**\*/delete** | **Microsoft.Authorization/locks/delete**<br />**Microsoft.Network/virtualNetwork/subnets/join/action** |SystemDefined (Everyone) |**excludedPrincipals** 의 청사진 할당 및 사용자 정의 |리소스 그룹 - _true_; 리소스 - _false_ |

> [!IMPORTANT]
> Azure Resource Manager는 최대 30분 동안 역할 할당 정보를 캐시합니다. 결과적으로, 청사진 리소스에 대한 거부 할당 거부 동작은 즉시 완전히 적용되지는 않을 수도 있습니다. 이 기간 동안 청사진 잠금으로 보호해야 하는 리소스가 삭제될 수도 있습니다.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>거부 할당에서 보안 주체 제외

일부 설계 또는 보안 시나리오에서는 청사진 할당이 생성하는 [거부 할당](../../../role-based-access-control/deny-assignments.md)에서 보안 주체를 제외해야 할 수 있습니다. 이 단계는 [할당 생성](/rest/api/blueprints/assignments/createorupdate) 시 **locks** 속성의 **excludedPrincipals** 배열에 최대 5개의 값을 추가하여 REST API에서 수행됩니다. 다음 할당 정의는 **excludedPrincipals** 를 포함하는 요청 본문의 예입니다.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>거부 할당에서 작업 제외

청사진 할당의 [거부 할당](../../../role-based-access-control/deny-assignments.md)에서 [보안 주체를 제외](#exclude-a-principal-from-a-deny-assignment)하는 것과 유사하게 특정 [Azure 리소스 공급자 작업](../../../role-based-access-control/resource-provider-operations.md)을 제외할 수 있습니다. **properties.locks** 블록 내에서 **excludedPrincipals** 가 있는 동일한 위치에 **excludedActions** 를 추가할 수 있습니다.

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

**excludedPrincipals** 는 명시적이어야 하지만 **excludedActions** 항목은 리소스 공급자 작업의 와일드카드 일치에 `*`를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [새 리소스 보호](../tutorials/protect-new-resources.md) 자습서 수행
- [청사진 수명 주기](./lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](./parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](./sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결
