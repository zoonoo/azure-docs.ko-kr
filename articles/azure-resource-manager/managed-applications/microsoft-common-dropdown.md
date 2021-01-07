---
title: DropDown UI 요소
description: Azure Portal의 Microsoft.Common.DropDown UI 요소에 대해 설명합니다. 관리 되는 응용 프로그램을 배포할 때 사용 가능한 옵션 중에서 선택 하는 데 사용 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: b9b27a432776635290c7e8e796e84d8c1e0e8675
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168344"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 요소

드롭다운 목록을 포함하는 선택 컨트롤입니다. 단일 항목 또는 여러 항목을 선택할 수 있습니다. 항목에 대 한 설명을 선택적으로 포함할 수도 있습니다.

## <a name="ui-sample"></a>UI 샘플

DropDown 요소는 포털에서 모양을 결정 하는 다양 한 옵션을 포함 합니다.

단일 항목만 선택할 수 있는 경우 컨트롤은 다음과 같이 표시 됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft 단일 항목 선택":::

설명이 포함 되 면 컨트롤은 다음과 같이 표시 됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft 단일 항목 선택":::

다중 선택을 사용 하도록 설정 하는 경우 컨트롤은 둘 이상의 항목을 선택할 수 있는 **모두 선택** 옵션과 확인란을 추가 합니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft 단일 항목 선택":::

다중 선택 사용에 대 한 설명을 포함할 수 있습니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Microsoft 단일 항목 선택":::

필터링을 사용 하는 경우 컨트롤에 필터링 값을 추가 하기 위한 입력란이 포함 됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft 단일 항목 선택":::

## <a name="schema"></a>스키마

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
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

- `multiselect`사용자가 둘 이상의 항목을 선택할 수 있는지 여부를 지정 하는 데 사용 합니다.
- `selectAll` `true` 다중 선택을 사용 하도록 설정 하면 기본적으로가 사용 됩니다.
- `filter`사용자는 속성을 사용 하 여 긴 옵션 목록에서 검색할 수 있습니다.
- `constraints.allowedValues`의 레이블은 항목에 대한 표시 텍스트이며, 해당 값은 선택한 요소의 출력 값입니다.
- 이 값을 지정하면 기본값은 `constraints.allowedValues`에 있는 레이블이어야 합니다. 지정하지 않으면 `constraints.allowedValues`의 첫 번째 항목이 선택됩니다. 기본값은 **null**입니다.
- `constraints.allowedValues`에는 적어도 하나의 항목이 합니다.
- 필요하지 않은 값을 에뮬레이트하려면 레이블과 값이 `""`(빈 문자열)인 항목을 `constraints.allowedValues`에 추가합니다.
- `defaultDescription`속성은 설명이 없는 항목에 사용 됩니다.
- `placeholder`속성은 사용자가 편집을 시작할 때 사라지는 도움말 텍스트입니다. 및가 `placeholder` `defaultValue` 둘 다 정의 된 경우 `defaultValue` 가 우선적으로 적용 되 고 표시 됩니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
