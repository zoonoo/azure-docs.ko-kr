---
title: ArmApiControl UI 요소
description: Azure Portal의 Microsoft.Solutions.ArmApiControl UI 요소에 대해 설명합니다. API 작업을 호출하는 데 사용됩니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: fdc299ef1945e3ee0810f1c314fc07edfb4f4873
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313303"
---
# <a name="microsoftsolutionsarmapicontrol-ui-element"></a>Microsoft.Solutions.ArmApiControl UI 요소

ArmApiControl을 사용하면 Azure Resource Manager API 작업에서 결과를 가져올 수 있습니다. 이 결과를 사용하여 다른 컨트롤의 동적 콘텐츠를 채웁니다.

## <a name="ui-sample"></a>UI 샘플

이 컨트롤에는 UI가 없습니다.

## <a name="schema"></a>스키마

다음 예제에서는 이 컨트롤에 대한 스키마를 보여 줍니다.

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>샘플 출력

컨트롤 출력은 사용자에게 표시되지 않습니다. 대신, 작업의 결과가 다른 컨트롤에서 사용됩니다.

## <a name="remarks"></a>설명

- `request.method` 속성은 HTTP 메서드를 지정합니다. `GET` 또는 `POST`만 사용할 수 있습니다.
- `request.path` 속성은 ARM 엔드포인트에 대한 상대 경로여야 하는 URL을 지정합니다. 고정 경로일 수도 있고 다른 컨트롤의 출력 값을 참조하여 동적으로 생성될 수도 있습니다.

  예를 들어 `Microsoft.Network/expressRouteCircuits` 리소스 공급자에 대한 ARM 호출은 다음과 같습니다.

  ```json
  "path": "<subid>/resourceGroup/<resourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<routecircuitName>/?api-version=2020-05-01"
  ```

- `request.body` 속성은 선택 사항입니다. 요청과 함께 전송되는 JSON 본문을 지정하는 데 사용됩니다. 본문은 정적 콘텐츠일 수도 있고 다른 컨트롤의 출력 값을 참조하여 동적으로 생성될 수도 있습니다.

## <a name="example"></a>예제

다음 예제에서는 `providersApi` 요소가 API를 호출하여 공급자 개체의 배열을 가져옵니다.

`providersDropDown` 요소의 `allowedValues` 속성은 공급자 이름을 가져오도록 구성됩니다. 드롭다운 목록으로 표시합니다.

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

ArmApiControl을 사용하여 리소스 이름의 가용성을 확인하는 예제는 [Microsoft.Common.TextBox](microsoft-common-textbox.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
