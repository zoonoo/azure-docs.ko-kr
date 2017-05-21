---
title: "Azure Managed Application TextBox UI 요소 | Microsoft Docs"
description: "Azure Managed Applications의 Microsoft.Common.TextBox UI 요소에 대해 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: aa161d2f6611cd7f1ef8fac8eebfde12eedd9fcf
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---

# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI 요소
서식 없는 텍스트를 편집하는 데 사용할 수 있는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>스키마
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Halp!",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>설명
- `constraints.required`를 **true**로 설정하면 텍스트 상자에서 유효성을 성공적으로 검사할 수 있는 값을 포함해야 합니다. 기본값은 **false**입니다.
- `constraints.regex`는 JavaScript 정규식 패턴입니다. 지정하면 텍스트 상자의 값이 유효성을 성공적으로 검사하기 위한 패턴과 일치해야 합니다. 기본값은 **null**입니다.
- `constraints.validationMessage`는 텍스트 상자의 값이 유효성 검사에 실패할 때 표시할 문자열입니다. 지정하지 않으면 텍스트 상자의 기본 제공 유효성 검사 메시지가 사용됩니다. 기본값은 **null**입니다.
- `constraints.required`를 **false**로 설정하면 `constraints.regex`에 대한 값을 지정할 수 있습니다. 이 시나리오에서는 텍스트 상자에서 유효성을 성공적으로 검사하는 데 값이 필요하지 않습니다. 지정하는 경우 정규식 패턴과 일치해야 합니다.

## <a name="sample-output"></a>샘플 출력

```json
"foobar"
```

## <a name="next-steps"></a>다음 단계
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](managed-application-createuidefinition-elements.md)를 참조하세요.

