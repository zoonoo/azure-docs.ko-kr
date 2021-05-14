---
title: IdentitySelector UI 요소
description: Azure Portal의 Microsoft.ManagedIdentity.IdentitySelector UI 요소에 대해 설명합니다. 관리 ID를 리소스에 할당하는 데 사용됩니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063359"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.ManagedIdentity.IdentitySelector UI 요소

배포에서 리소스에 대한 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 할당하는 데 사용되는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

이 컨트롤은 다음 요소로 구성되어 있습니다.

![Microsoft.ManagedIdentity.IdentitySelector 첫 번째 단계](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

사용자가 **추가** 를 선택하면 다음 양식이 열립니다. 사용자는 리소스에 대해 사용자 할당 ID를 하나 이상 선택할 수 있습니다.

![Microsoft.ManagedIdentity.IdentitySelector 두 번째 단계](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

선택한 ID가 표에 표시됩니다. 사용자는 이 표에서 항목을 추가하거나 삭제할 수 있습니다.

![Microsoft.ManagedIdentity.IdentitySelector 세 번째 단계](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

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

- 시스템 할당 ID 옵션 컨트롤의 초기 값을 설정하려면 **defaultValue.systemAssignedIdentity** 를 사용합니다. 기본값은 **Off** 입니다. 허용되는 값은
  - **On** – 시스템 할당 ID가 리소스에 할당됩니다.
  - **Off** – 시스템 할당 ID가 리소스에 할당되지 않습니다.
  - **OnOnly** – 시스템 할당 ID가 리소스에 할당됩니다. 이 값은 사용자가 배포 중에 편집할 수 없습니다.
  - **OffOnly** – 시스템 할당 ID가 리소스에 할당되지 않습니다. 이 값은 사용자가 배포 중에 편집할 수 없습니다.

- **options.hideSystemAssignedIdentity** 가 **true** 로 설정되면 시스템 할당 ID를 구성하는 UI가 표시되지 않습니다. 이 옵션의 기본값은 **false** 입니다.
- **options.hideUserAssignedIdentity** 가 **true** 로 설정되면 사용자 할당 ID를 구성하는 UI가 표시되지 않습니다. 리소스에 사용자 할당 ID가 할당되지 않습니다. 이 옵션의 기본값은 **false** 입니다.

## <a name="next-steps"></a>다음 단계

- UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.