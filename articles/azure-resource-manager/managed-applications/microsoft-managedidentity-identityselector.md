---
title: 아이덴티티 셀렉터 UI 요소
description: Azure 포털에 대한 Microsoft.ManagedIdentity.Identity.IdentitySelector UI 요소에 대해 설명합니다. 관리되는 ID를 리소스에 할당하는 데 사용합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087545"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>마이크로소프트.ManagedIdentity.IdSelector UI 요소

배포의 리소스에 대해 [관리되는 ID를](../../active-directory/managed-identities-azure-resources/overview.md) 할당하기 위한 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

컨트롤은 다음 요소로 구성됩니다.

![마이크로소프트.ManagedIdentity.IdSelector 첫 번째 단계](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

사용자가 **Add를**선택하면 다음 양식이 열립니다. 사용자는 리소스에 대해 하나 이상의 사용자 할당ID를 선택할 수 있습니다.

![마이크로소프트.ManagedIdentity.IdSelector 두 번째 단계](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

선택한 ID가 테이블에 표시됩니다. 사용자는 이 테이블에서 항목을 추가하거나 삭제할 수 있습니다.

![마이크로소프트.ManagedIdentity.IdSelector 세 번째 단계](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

## <a name="schema"></a>스키마

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>샘플 출력

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>설명

- **defaultValue.systemAssignedIdentity를** 사용하여 시스템에 할당된 ID 옵션 컨트롤에 대한 초기 값을 설정합니다. 기본값은 **꺼져 있습니다.** 허용되는 값은
  - **On** – ID가 할당된 시스템이 리소스에 할당됩니다.
  - **꺼기** – ID가 할당된 시스템이 리소스에 할당되지 않습니다.
  - **OnOnly** – ID가 할당된 시스템이 리소스에 할당됩니다. 배포 하는 동안이 값을 편집할 수 없습니다.
  - **OffOnly** – ID가 할당된 시스템이 리소스에 할당되지 않습니다. 배포 하는 동안이 값을 편집할 수 없습니다.

- **options.hideSystemAssignedId가** **true로**설정된 경우 시스템에 할당된 ID를 구성하는 UI가 표시되지 않습니다. 이 옵션의 기본값은 **false입니다.**
- **options.hideUserAssignedIdentity가** **true로**설정된 경우 사용자에게 할당된 ID를 구성하는 UI가 표시되지 않습니다. 리소스에 할당된 사용자에게 할당된 ID가 할당되지 않았습니다. 이 옵션의 기본값은 **false입니다.**

## <a name="next-steps"></a>다음 단계

- UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.