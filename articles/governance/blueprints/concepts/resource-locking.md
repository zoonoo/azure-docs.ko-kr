---
title: 리소스 잠금 이해
description: Learn about the locking options in Azure Blueprints to protect resources when assigning a blueprint.
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: 50f506cc57f67ca2ae2b07e342750d6c5099e739
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406399"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Azure Blueprints의 리소스 잠금 이해

일관성 있는 환경을 원하는 규모로 만든다고 해도 해당 일관성을 유지할 수 있는 메커니즘이 있어야 환경을 적절하게 활용할 수 있습니다. 이 문서에서는 Azure Blueprints에서 리소스 잠금이 작동하는 방식에 대해 설명합니다. To see an example of resource locking and application of _deny assignments_, see the [protecting new resources](../tutorials/protect-new-resources.md) tutorial.

## <a name="locking-modes-and-states"></a>잠금 모드 및 상태

Locking Mode applies to the blueprint assignment and it has three options: **Don't Lock**, **Read Only**, or **Do Not Delete**. 잠금 모드는 청사진 할당을 수행하는 동안 아티팩트 배포 중에 구성됩니다. 청사진 할당을 업데이트하여 다른 잠금 모드를 설정할 수 있습니다.
그러나 잠금 모드는 청사진 외부에서 변경할 수 없습니다.

Resources created by artifacts in a blueprint assignment have four states: **Not Locked**, **Read Only**, **Cannot Edit / Delete**, or **Cannot Delete**. 각 아티팩트 형식은 **잠겨있지 않음** 상태일 수 있습니다. 다음 표를 사용하여 리소스의 상태를 확인할 수 있습니다.

|Mode|아티팩트 리소스 형식|상태|설명|
|-|-|-|-|
|잠그지 않음|*|잠겨있지 않음|리소스는 청사진에 의해 보호되지 않습니다. 이 상태는 또한 청사진 할당 외부에서 **읽기 전용** 또는 **삭제 안 함** 리소스 그룹 아티팩트에 추가된 리소스에 사용됩니다.|
|읽기 전용|Resource group|편집/삭제할 수 없음|리소스 그룹이 읽기 전용이며 리소스 그룹의 태그를 수정할 수 없습니다. **잠겨 있지 않음** 리소스는 이 리소스 그룹에서 추가, 이동, 변경 또는 삭제할 수 있습니다.|
|읽기 전용|리소스 그룹이 아님|읽기 전용|리소스를 어떤 방식으로도 변경할 수 없으며 삭제할 수도 없습니다.|
|삭제 안 함|*|삭제할 수 없음|리소스를 변경할 수 있지만 삭제할 수 없습니다. **잠겨 있지 않음** 리소스는 이 리소스 그룹에서 추가, 이동, 변경 또는 삭제할 수 있습니다.|

## <a name="overriding-locking-states"></a>잠금 상태 재정의

일반적으로 구독에서 ‘소유자’ 역할과 같은 RBAC([역할 기반 액세스 제어](../../../role-based-access-control/overview.md)) 구독이 있는 사용자는 리소스를 변경하거나 삭제할 수 있습니다. Blueprint가 배포된 할당의 일부로 잠금을 적용하는 경우는 여기에 해당하지 않습니다. 할당이 **읽기 전용** 또는 **삭제 안 함** 옵션으로 설정된 경우에는 구독 소유자여도 보호된 리소스에서 차단된 작업을 수행할 수 없습니다.

이 보안 조치는 정의된 청사진과 해당 청사진을 통해 생성하려는 환경을 실수로/프로그래밍 방식으로 삭제하거나 변경하는 상황을 방지하여 일관성을 유지합니다.

## <a name="removing-locking-states"></a>잠금 상태 제거

할당에서 보호되는 리소스를 수정하거나 삭제해야 하는 경우 두 가지 방법이 있습니다.

- 청사진 할당을 **잠그지 않음** 잠금 모드로 업데이트
- 청사진 할당 삭제

할당을 제거하면 Blueprints를 통해 생성된 잠금이 제거됩니다. 그러나 리소스는 그대로 남아 있으므로 일반적인 방법으로 삭제해야 합니다.

## <a name="how-blueprint-locks-work"></a>청사진 잠금의 작동 방식

할당에서 **읽기 전용** 또는 **삭제 안 함** 옵션을 선택한 경우 RBAC [거부 할당](../../../role-based-access-control/deny-assignments.md) 거부 동작이 청사진 할당 중에 아티팩트 리소스에 적용됩니다. 거부 동작은 청사진 할당의 관리 ID를 통해 추가되며 같은 관리 ID를 통해서만 아티팩트 리소스에서 제거할 수 있습니다. 이 보안 조치는 잠금 메커니즘을 적용하고, Blueprints 외부에서 청사진을 제거하지 못하도록 합니다.

![Blueprint deny assignment on resource group](../media/resource-locking/blueprint-deny-assignment.png)

The [deny assignment properties](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) of each mode are as follows:

|Mode |Permissions.Actions |Permissions.NotActions |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|읽기 전용 |**\*** |**\*/read** |SystemDefined (Everyone) |blueprint assignment and user-defined in **excludedPrincipals** |Resource group - _true_; Resource - _false_ |
|삭제 안 함 |**\*/delete** | |SystemDefined (Everyone) |blueprint assignment and user-defined in **excludedPrincipals** |Resource group - _true_; Resource - _false_ |

> [!IMPORTANT]
> Azure Resource Manager는 최대 30분 동안 역할 할당 정보를 캐시합니다. 결과적으로, 청사진 리소스에 대한 거부 할당 거부 동작은 즉시 완전히 적용되지는 않을 수도 있습니다. 이 기간 동안 청사진 잠금으로 보호해야 하는 리소스가 삭제될 수도 있습니다.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Exclude a principal from a deny assignment

In some design or security scenarios, it may be necessary to exclude a principal from the [deny assignment](../../../role-based-access-control/deny-assignments.md) the blueprint assignment creates. This is done in REST API by adding up to five values to the **excludedPrincipals** array in the **locks** property when [creating the assignment](/rest/api/blueprints/assignments/createorupdate). This is an example of a request body that includes **excludedPrincipals**:

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

## <a name="next-steps"></a>다음 단계

- Follow the [protect new resources](../tutorials/protect-new-resources.md) tutorial.
- [청사진 수명 주기](lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결