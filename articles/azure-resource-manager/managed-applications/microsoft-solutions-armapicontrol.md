---
title: ArmApiControl UI 요소
description: Azure Portal에 대 한 ArmApiControl UI 요소에 대해 설명 합니다. API 작업을 호출 하는 데 사용 됩니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: bbe36e072d10b81c421331b2212d8b161afd2693
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098156"
---
# <a name="microsoftcommonarmapicontrol-ui-element"></a>ArmApiControl UI 요소

ArmApiControl를 사용 하면 Azure Resource Manager API 작업에서 결과를 가져올 수 있습니다. 결과를 사용 하 여 다른 컨트롤의 동적 콘텐츠를 채웁니다.

## <a name="ui-sample"></a>UI 샘플

이 컨트롤에 대 한 UI가 없습니다.

## <a name="schema"></a>스키마

다음 예제에서는이 컨트롤에 대 한 스키마를 보여 줍니다.

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

컨트롤의 출력이 사용자에 게 표시 되지 않습니다. 대신 작업의 결과가 다른 컨트롤에서 사용 됩니다.

## <a name="remarks"></a>설명

- `request.method`속성은 HTTP 메서드를 지정 합니다. `GET`또는만 `POST` 사용할 수 있습니다.
- `request.path`속성은 URL의 상대 경로를 지정 합니다. 정적 경로 이거나 다른 컨트롤의 출력 값을 참조 하 여 동적으로 생성 될 수 있습니다.
- `request.body` 속성은 선택 사항입니다. 이를 사용 하 여 요청과 함께 전송 되는 JSON 본문을 지정 합니다. 본문은 정적 콘텐츠 이거나 다른 컨트롤의 출력 값을 참조 하 여 동적으로 생성 될 수 있습니다.

## <a name="example"></a>예제

다음 예제에서 `providersApi` 요소는 API를 호출 하 여 공급자 개체의 배열을 가져옵니다.

`allowedValues` `providersDropDown` 요소의 속성은 공급자의 이름을 가져오도록 구성 됩니다. 드롭다운 목록에 표시 됩니다.

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

ArmApiControl를 사용 하 여 리소스 이름의 가용성을 확인 하는 예제는 [Microsoft. 일반 텍스트 상자](microsoft-common-textbox.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
