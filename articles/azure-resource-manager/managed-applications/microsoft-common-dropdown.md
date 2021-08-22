---
title: DropDown UI 요소
description: Azure Portal의 Microsoft.Common.DropDown UI 요소에 대해 설명합니다. 관리형 애플리케이션을 배포할 때 사용 가능한 옵션 중에서 선택하는 데 사용합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 57befe2d0a2ff985dc1da90119e3eedd5da85040
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135071"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 요소

드롭다운 목록을 포함하는 선택 컨트롤입니다. 단일 항목 또는 여러 항목을 선택할 수 있습니다. 항목에 대한 설명을 선택적으로 포함할 수도 있습니다.

## <a name="ui-sample"></a>UI 샘플

DropDown 요소는 포털에서 요소 모양을 결정하는 다양한 옵션을 포함합니다.

단일 항목만 선택할 수 있는 경우 컨트롤은 다음과 같이 표시됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft.Common.DropDown 단일 선택":::

설명을 포함하면 컨트롤은 다음과 같이 표시됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft.Common.DropDown 단일 선택(설명 포함)":::

다중 선택을 사용할 수 있는 경우 컨트롤은 둘 이상의 항목을 선택할 수 있는 **모두 선택** 옵션과 확인란을 추가합니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft.Common.DropDown 다중 선택":::

다중 선택을 사용하도록 설정하여 설명을 포함할 수 있습니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="다중 선택을 사용하도록 설정하여 설명을 포함할 수 있는 방법 스크린샷":::

필터링을 사용할 수 있는 경우 컨트롤은 필터링 값을 추가할 수 있는 텍스트 상자를 포함합니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft.Common.DropDown 다중 선택(설명 포함)":::

## <a name="schema"></a>스키마

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,
    "selectAll": true,
    "filter": true,
    "filterPlaceholder": "Filter items ...",
    "multiLine": true,
    "defaultDescription": "A value for selection",
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
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

- 사용자가 둘 이상의 항목을 선택할 수 있는지 여부를 지정하려면 `multiselect`를 사용합니다.
- 다중 선택을 사용하도록 설정하면 `selectAll`은 기본적으로 `true`가 됩니다.
- `filter` 속성을 사용하면 사용자가 긴 옵션 목록에서 검색할 수 있습니다.
- `constraints.allowedValues`의 레이블은 항목에 대한 표시 텍스트이며, 해당 값은 선택한 요소의 출력 값입니다.
- 이 값을 지정하면 기본값은 `constraints.allowedValues`에 있는 레이블이어야 합니다. 지정하지 않으면 `constraints.allowedValues`의 첫 번째 항목이 선택됩니다. 기본값은 **null** 입니다.
- `constraints.allowedValues`에는 적어도 하나의 항목이 합니다.
- 필요하지 않은 값을 에뮬레이트하려면 레이블과 값이 `""`(빈 문자열)인 항목을 `constraints.allowedValues`에 추가합니다.
- `defaultDescription` 속성은 설명이 없는 항목에 사용됩니다.
- `placeholder` 속성은 사용자가 편집을 시작할 때 사라지는 도움말 텍스트입니다. `placeholder` 및 `defaultValue`를 둘 다 정의하면 `defaultValue`가 우선 적용되고 표시됩니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
