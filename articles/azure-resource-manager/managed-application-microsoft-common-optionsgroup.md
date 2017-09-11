---
title: "Azure Managed Application OptionsGroup UI 요소 | Microsoft Docs"
description: "Azure Managed Applications의 Microsoft.Common.OptionsGroup UI 요소에 대해 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 6e147ed28c8248f7f17cb36fd7ae13468141dced
ms.contentlocale: ko-kr
ms.lasthandoff: 05/13/2017

---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup UI 요소
사용 가능한 옵션 행을 포함하는 선택 컨트롤입니다. [Azure 관리되는 응용 프로그램을 만드는](managed-application-publishing.md) 경우 이 요소를 사용합니다.

## <a name="ui-sample"></a>UI 샘플
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>스키마
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Foo",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Foo",
        "value": "Bar"
      },
      {
        "label": "Baz",
        "value": "Qux"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>설명
- `constraints.allowedValues`의 레이블은 항목에 대한 표시 텍스트이며, 해당 값은 선택한 요소의 출력 값입니다.
- 이 값을 지정하면 기본값은 `constraints.allowedValues`에 있는 레이블이어야 합니다. 지정하지 않으면 `constraints.allowedValues`의 첫 번째 항목이 기본적으로 선택됩니다. 기본값은 **null**입니다.
- `constraints.allowedValues`는 하나 이상의 항목을 포함해야 합니다.
- 이 요소는 `constraints.required` 속성을 지원하지 않습니다. 유효성을 성공적으로 검사하기 위한 항목을 선택해야 합니다.

## <a name="sample-output"></a>샘플 출력
```json
"Bar"
```

## <a name="next-steps"></a>다음 단계
* 관리되는 응용 프로그램에 대한 소개는 [Azure Managed Application 개요](managed-application-overview.md)를 참조하세요.
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](managed-application-createuidefinition-elements.md)를 참조하세요.

