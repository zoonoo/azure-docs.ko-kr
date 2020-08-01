---
title: TextBox UI 요소
description: Azure Portal의 Microsoft.Common.TextBox UI 요소에 대해 설명합니다. 서식 없는 텍스트를 추가 하는 데 사용 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c89bc434d9d67144a95b5c2f23e7664078fe7825
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87474311"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI 요소

서식 없는 텍스트를 편집하는 데 사용할 수 있는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>스키마

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "placeholder": "",
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

- `constraints.required`를 **true**로 설정하면 텍스트 상자에서 유효성을 성공적으로 검사할 수 있는 값을 포함해야 합니다. 기본 값은 **false**입니다.
- `validations`속성은 텍스트 상자에 제공 된 값을 확인 하는 조건을 추가 하는 배열입니다.
- `regex`속성은 JavaScript 정규식 패턴입니다. 지정하면 텍스트 상자의 값이 유효성을 성공적으로 검사하기 위한 패턴과 일치해야 합니다. 기본값은 **null**입니다.
- `isValid`속성은 true 또는 false로 계산 되는 식을 포함 합니다. 식 내에서 텍스트 상자가 유효한 지 여부를 결정 하는 조건을 정의 합니다.
- `message`속성은 텍스트 상자의 값에 대 한 유효성 검사가 실패 하는 경우 표시할 문자열입니다.
- `required`를 **false**로 설정하면 `regex`에 대한 값을 지정할 수 있습니다. 이 시나리오에서는 텍스트 상자에서 유효성을 성공적으로 검사하는 데 값이 필요하지 않습니다. 지정하는 경우 정규식 패턴과 일치해야 합니다.
- `placeholder`속성은 사용자가 편집을 시작할 때 사라지는 도움말 텍스트입니다. 및가 `placeholder` `defaultValue` 둘 다 정의 된 경우 `defaultValue` 가 우선적으로 적용 되 고 표시 됩니다.

## <a name="example"></a>예제

다음 예제에서는 [ArmApiControl](microsoft-solutions-armapicontrol.md) 컨트롤을 사용 하 여 텍스트 상자를 사용 하 여 리소스 이름의 가용성을 확인 합니다.

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
