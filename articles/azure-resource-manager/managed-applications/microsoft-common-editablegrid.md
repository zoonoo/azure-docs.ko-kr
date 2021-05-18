---
title: EditableGrid UI 요소
description: Azure Portal의 Microsoft.Common.EditableGrid UI 요소에 대해 설명합니다. 사용자가 테이블 형식 입력을 수집할 수 있도록 설정합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88893771"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>Microsoft.Common.EditableGrid UI 요소

테이블 형식 입력을 수집하기 위한 컨트롤입니다. 그리드의 모든 필드를 편집할 수 있으며 행 수는 다를 수 있습니다.

## <a name="ui-sample"></a>UI 샘플

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="Microsoft.Common.EditableGrid":::

## <a name="schema"></a>스키마

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>샘플 출력

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>설명

- 열 배열 내에서 유효한 컨트롤은 [TextBox](microsoft-common-textbox.md), [OptionsGroup](microsoft-common-optionsgroup.md) 및 [DropDown](microsoft-common-dropdown.md)뿐입니다.
- `$rowIndex` 변수는 그리드 열의 자식 요소에 포함된 언어 식에서만 유효합니다. 요소의 상대 행 인덱스를 나타내는 정수이며 개수는 1에서 시작하여 1씩 증가합니다. 스키마의 `"columns":` 섹션에 표시된 대로 `$rowIndex`는 유효성 검사에 사용됩니다.
- `$rowIndex` 변수를 사용하여 유효성 검사를 수행할 때 `last()` 및 `take()` 명령을 결합하여 현재 행의 값을 얻을 수 있습니다.

  예를 들면 다음과 같습니다.

  `last(take(<reference_to_grid>, $rowIndex))`

- `label` 속성은 컨트롤의 일부로 표시되지 않지만 최종 탭 요약에 표시됩니다.
- `ariaLabel` 속성은 그리드의 접근성 레이블입니다. 화면 읽기를 사용하는 사용자에게 유용한 텍스트를 지정합니다.
- `constraints.width` 속성은 그리드의 전체 너비를 설정하는 데 사용됩니다. 옵션은 _전체_, _중간_, _작음_ 입니다. 기본값은 _전체_ 입니다.
- 열의 자식 요소 `width` 속성은 열의 너비를 결정합니다. 너비는 _3fr_ 과 같은 소수 단위를 사용하여 지정되며 총 공간은 단위에 비례하여 열에 할당됩니다. 열 너비를 지정하지 않으면 기본값은 _1fr_ 입니다.

## <a name="next-steps"></a>다음 단계

- UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
