---
title: 기타 그룹 UI 요소
description: Azure Portal의 Microsoft.Common.OptionsGroup UI 요소에 대해 설명합니다. 사용자가 관리 되는 응용 프로그램을 배포할 때 사용 가능한 옵션 중에서 선택할 수 있습니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: aa73b4cbded98291a14792a7151df9fdfb885b53
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004196"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup UI 요소

옵션 그룹 컨트롤을 사용 하면 사용자가 둘 이상의 선택 항목에서 옵션을 선택할 수 있습니다. 사용자는 옵션을 하나만 선택할 수 있습니다.

> [!NOTE]
> 이전에이 컨트롤은 옵션을 가로로 렌더링 했습니다. 이제 컨트롤은 옵션을 라디오 단추로 세로로 표시 합니다.

## <a name="ui-sample"></a>UI 샘플

:::image type="content" source="./media/managed-application-elements/microsoft-common-optionsgroup-2.png" alt-text="Microsoft.Common.OptionsGroup":::

## <a name="schema"></a>스키마

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>샘플 출력

```json
"two"
```

## <a name="remarks"></a>설명

- `constraints.allowedValues`의 레이블은 항목에 대한 표시 텍스트이며, 해당 값은 선택한 요소의 출력 값입니다.
- 이 값을 지정하면 기본값은 `constraints.allowedValues`에 있는 레이블이어야 합니다. 지정하지 않으면 `constraints.allowedValues`의 첫 번째 항목이 기본적으로 선택됩니다. 기본값은 **null**입니다.
- `constraints.allowedValues`에는 적어도 하나의 항목이 합니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
