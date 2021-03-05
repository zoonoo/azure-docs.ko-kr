---
title: TextBox UI 요소
description: Azure Portal의 Microsoft.Common.TextBox UI 요소에 대해 설명합니다. 서식 없는 텍스트를 추가 하는 데 사용 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124332"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI 요소

서식 없는 텍스트를 추가 하는 데 사용할 수 있는 UI (사용자 인터페이스) 요소입니다. `Microsoft.Common.TextBox`요소는 한 줄 입력 필드 이지만 속성을 사용 하 여 여러 줄 입력을 지원 `multiLine` 합니다.

## <a name="ui-sample"></a>UI 샘플

`TextBox`요소는 한 줄 또는 여러 줄 텍스트 상자를 사용 합니다.

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Microsoft. 일반적인 텍스트 상자 요소 한 줄 텍스트 상자입니다.":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Microsoft. 일반 텍스트 상자 요소 여러 줄 텍스트 상자":::

## <a name="schema"></a>스키마

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
  "constraints": {
    "required": true,
    "validations": [
      {
        "regex": "^[a-z0-9A-Z]{1,30}$",
        "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
      },
      {
        "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
        "message": "Must start with 'contoso'."
      }
    ]
  },
  "visible": true
}
```

## <a name="sample-output"></a>샘플 출력

```json
"contoso123"
```

## <a name="remarks"></a>설명

- `toolTip`마우스 커서가 정보 기호를 가리킬 때 요소에 대 한 텍스트를 표시 하려면 속성을 사용 합니다.
- `placeholder`속성은 사용자가 편집을 시작할 때 사라지는 도움말 텍스트입니다. 및가 `placeholder` `defaultValue` 둘 다 정의 된 경우 `defaultValue` 가 우선적으로 적용 되 고 표시 됩니다.
- `multiLine`속성이 부울 `true` 또는 인 `false` 경우 여러 줄 텍스트 상자를 사용 하려면 속성을로 설정 `true` 합니다. 여러 줄 텍스트 상자가 필요 하지 않은 경우 속성을로 설정 `false` 하거나 속성을 제외 합니다. 새 줄의 경우 JSON 출력 `\n` 은 줄 바꿈에 대 한를 표시 합니다. 여러 줄 텍스트 상자는 CR ( `\r` 캐리지 리턴) 및 `\n` 줄 바꿈 (LF)에 대해 허용 됩니다. 예를 들어 기본값에는 CRLF를 지정 하는가 포함 될 수 있습니다 `\r\n` .
- `constraints.required`가로 설정 된 경우 `true` 텍스트 상자에는 유효성을 검사 하기 위한 값이 있어야 합니다. 기본값은 `false`입니다.
- `validations`속성은 텍스트 상자에 제공 된 값을 확인 하는 조건을 추가 하는 배열입니다.
- `regex`속성은 JavaScript 정규식 패턴입니다. 지정 된 경우 텍스트 상자의 값은 유효성을 검사 하기 위해 패턴과 일치 해야 합니다. 기본값은 `null`입니다. Regex 구문에 대 한 자세한 내용은 [정규식 빠른 참조](/dotnet/standard/base-types/regular-expression-language-quick-reference)를 참조 하세요.
- 속성에는 `isValid` 또는로 계산 되는 식이 포함 됩니다 `true` `false` . 식 내에서 텍스트 상자가 유효한 지 여부를 결정 하는 조건을 정의 합니다.
- `message`속성은 텍스트 상자의 값에 대 한 유효성 검사가 실패 하는 경우 표시할 문자열입니다.
- `regex`가로 설정 된 경우에 대 한 값을 지정할 수 `required` `false` 있습니다. 이 시나리오에서는 텍스트 상자에서 유효성을 성공적으로 검사하는 데 값이 필요하지 않습니다. 지정하는 경우 정규식 패턴과 일치해야 합니다.

## <a name="examples"></a>예제

이 예제에서는 한 줄 텍스트 상자와 여러 줄 텍스트 상자를 사용 하는 방법을 보여 줍니다.

### <a name="single-line"></a>한 줄

다음 예제에서는 [ArmApiControl](microsoft-solutions-armapicontrol.md) 컨트롤을 사용 하 여 텍스트 상자를 사용 하 여 리소스 이름의 가용성을 확인 합니다.

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>여러 줄

이 예제에서는 속성을 사용 하 여 `multiLine` 자리 표시자 텍스트가 있는 여러 줄 텍스트 상자를 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

- UI 정의를 만드는 방법에 대 한 소개는 [ Azure 관리 되는 응용 프로그램의 만들기 환경을 위한CreateUiDefinition.js](create-uidefinition-overview.md)를 참조 하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
