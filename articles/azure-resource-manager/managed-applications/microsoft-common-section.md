---
title: Section UI 요소
description: Azure Portal의 Microsoft.Common.Section UI 요소에 대해 설명합니다. 를 사용 하 여 포털에서 관리 되는 응용 프로그램 배포를 위한 요소를 그룹화 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 924aff8f2ba3d796b65f52494845f3b10018065c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063983"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI 요소

제목 아래에 하나 이상의 요소를 그룹화하는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

![Microsoft.Common.Section](./media/managed-application-elements/microsoft-common-section.png)

## <a name="schema"></a>스키마

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>설명

- `elements`는 하나 이상의 요소를 포함해야 하며, `Microsoft.Common.Section`을 제외한 모든 요소 형식을 포함할 수 있습니다.
- 이 요소는 `toolTip` 속성을 지원하지 않습니다.

## <a name="sample-output"></a>샘플 출력
`elements`에 있는 요소의 출력 값에 액세스하려면 [basics()](create-ui-definition-referencing-functions.md#basics) 또는 [steps()](create-ui-definition-referencing-functions.md#steps) 함수와 점 표기법을 사용합니다.

```json
steps('configuration').section1.text1
```

`Microsoft.Common.Section` 형식의 요소에는 출력 값 자체가 없습니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
