---
title: IdentitySelector UI 요소
description: Azure Portal에 대 한 Microsoft.managedidentity IdentitySelector UI 요소에 대해 설명 합니다. 를 사용 하 여 관리 되는 id를 리소스에 할당 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77087545"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.managedidentity IdentitySelector UI 요소

배포에서 리소스에 대 한 [관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md) 를 할당 하기 위한 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

컨트롤은 다음 요소로 구성 됩니다.

![Microsoft.managedidentity. IdentitySelector first step](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

사용자가 **추가**를 선택 하면 다음 양식이 열립니다. 사용자는 리소스에 대해 사용자 할당 id를 하나 이상 선택할 수 있습니다.

![Microsoft.managedidentity. IdentitySelector second 단계](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

선택한 id가 테이블에 표시 됩니다. 사용자는이 테이블에서 항목을 추가 하거나 삭제할 수 있습니다.

![Microsoft.managedidentity. IdentitySelector 3 단계](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

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

- **defaultValue.systemAssignedIdentity** 를 사용 하 여 시스템 할당 id 옵션 컨트롤의 초기 값을 설정 합니다. 기본값은 **Off**입니다. 허용되는 값은
  - **On** – 시스템 할당 id가 리소스에 할당 됩니다.
  - **Off** – 시스템 할당 id가 리소스에 할당 되지 않습니다.
  - **Ononly** – 시스템 할당 id가 리소스에 할당 됩니다. 사용자는 배포 중에이 값을 편집할 수 없습니다.
  - **Offonly** – 시스템 할당 id는 리소스에 할당 되지 않습니다. 사용자는 배포 중에이 값을 편집할 수 없습니다.

- **HideSystemAssignedIdentity** 가 **true**로 설정 된 경우 시스템 할당 id를 구성 하는 UI가 표시 되지 않습니다. 이 옵션의 기본값은 **false**입니다.
- **HideUserAssignedIdentity** 가 **true**로 설정 된 경우 사용자 할당 id를 구성 하는 UI가 표시 되지 않습니다. 리소스에 사용자 할당 id가 할당 되지 않았습니다. 이 옵션의 기본값은 **false**입니다.

## <a name="next-steps"></a>다음 단계

- UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.