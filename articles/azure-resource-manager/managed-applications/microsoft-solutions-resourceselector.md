---
title: ResourceSelector UI 요소
description: Azure Portal의 Microsoft.Solutions.ResourceSelector UI 요소에 대해 설명합니다. 기존 리소스 목록을 가져오는 데 사용됩니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87099056"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>Microsoft.Solutions.ResourceSelector UI 요소

ResourceSelector를 사용하면 사용자가 구독에서 기존 리소스를 선택할 수 있습니다.

## <a name="ui-sample"></a>UI 샘플

![Microsoft.Solutions.ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>스키마

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>샘플 출력

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>설명

`resourceType` 속성에서는 목록에 표시할 리소스의 리소스 공급자 네임스페이스 및 리소스 종류 이름을 제공합니다.

`filter` 속성은 리소스에 사용할 수 있는 옵션을 제한합니다. 위치 또는 구독을 기준으로 결과를 제한할 수 있습니다. 기본 사항의 선택 사항과 일치하는 리소스만 표시하려면 `onBasics`를 사용합니다. 모든 리소스를 표시하려면 `all`을 사용합니다. 기본값은 `all`입니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
