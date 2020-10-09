---
title: 템플릿 구문 및 식
description: Azure Resource Manager 템플릿에 대 한 선언적 JSON 구문을 설명 합니다.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: baddedae1b918502e579d2ed230e0779960f45e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82203831"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 구문 및 식

템플릿의 기본 구문은 JSON이지만, 그러나 식을 사용 하 여 템플릿 내에서 사용할 수 있는 JSON 값을 확장할 수 있습니다.  식은 `[` 및 `]` 대괄호로 각각 시작 및 종료됩니다. 식의 값은 템플릿을 배포할 때 평가됩니다. 식은 문자열, 정수, 부울, 배열 또는 개체를 반환할 수 있습니다.

템플릿 식은 24576 자를 초과할 수 없습니다.

## <a name="use-functions"></a>함수 사용

Azure Resource Manager에서는 템플릿에서 사용할 수 있는 [함수](template-functions.md) 를 제공 합니다. 다음 예에서는 매개 변수의 기본값에서 함수를 사용 하는 식을 보여 줍니다.

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

식 내에서 구문은 `resourceGroup()` 템플릿 내에서 사용 하기 위해 리소스 관리자 제공 하는 함수 중 하나를 호출 합니다. 이 경우 [resourceGroup](template-functions-resource.md#resourcegroup) 함수입니다. JavaScript에서와 마찬가지로 함수 호출은 `functionName(arg1,arg2,arg3)`과 같이 형식이 지정됩니다. 구문은 `.location` 해당 함수에서 반환 하는 개체에서 속성 하나를 검색 합니다.

템플릿 함수 및 해당 매개 변수는 대/소문자를 구분하지 않습니다. 예를 들어 Resource Manager에서 **variables('var1')** 와 **VARIABLES('VAR1')** 는 동일합니다. 계산될 때 함수는 대/소문자를 명시적으로 수정하지 않는 한(toUpper 또는 toLower 등) 대/소문자를 보존합니다. 특정 리소스 종류에는 함수 평가 방법과 별개의 사례 요구 사항이 있을 수 있습니다.

문자열 값을 매개 변수로 함수에 전달 하려면 작은따옴표를 사용 합니다.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

대부분의 함수는 리소스 그룹, 구독, 관리 그룹 또는 테 넌 트에 배포 되었는지 여부와 동일 하 게 작동 합니다. 다음 함수는 범위에 따라 제한이 있습니다.

* [resourceGroup](template-functions-resource.md#resourcegroup) -리소스 그룹에 대 한 배포에만 사용할 수 있습니다.
* [resourceId](template-functions-resource.md#resourceid) -모든 범위에서 사용할 수 있지만 유효한 매개 변수는 범위에 따라 변경 됩니다.
* [구독](template-functions-resource.md#subscription) -리소스 그룹 또는 구독에 대 한 배포에만 사용할 수 있습니다.

## <a name="escape-characters"></a>이스케이프 문자

리터럴 문자열을 왼쪽 대괄호로 시작 하 `[` 고 오른쪽 괄호를 사용 하 여 종료 `]` 하지만 식으로 해석 되지 않도록 하려면 추가 대괄호를 추가 하 여 문자열을 시작 `[[` 합니다. 예를 들어 변수는 다음과 같습니다.

```json
"demoVar1": "[[test value]"
```

는로 확인 `[test value]` 됩니다.

그러나 리터럴 문자열이 대괄호로 끝나지 않는 경우에는 첫 번째 대괄호가 이스케이프 되지 않습니다. 예를 들어 변수는 다음과 같습니다.

```json
"demoVar2": "[test] value"
```

는로 확인 `[test] value` 됩니다.

템플릿에서 JSON 개체를 추가 하는 것과 같이 식에서 큰따옴표를 이스케이프 하려면 백슬래시를 사용 합니다.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

매개 변수 값을 전달할 때 이스케이프 문자 사용은 매개 변수 값이 지정 된 위치에 따라 달라 집니다. 템플릿에서 기본값을 설정 하는 경우 왼쪽 괄호를 추가로 만들어야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

기본값을 사용 하는 경우 템플릿은를 반환 `[test value]` 합니다.

그러나 명령줄을 통해 매개 변수 값을 전달 하는 경우 문자는 문자 그대로 해석 됩니다. 다음을 사용 하 여 이전 템플릿 배포:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

`[[test value]`를 반환합니다. 대신 다음을 사용 합니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

매개 변수 파일에서 값을 전달할 때 동일한 형식이 적용 됩니다. 문자는 문자 그대로 해석 됩니다. 위의 템플릿과 함께 사용 하는 경우 다음 매개 변수 파일은을 반환 합니다 `[test value]` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Null 값

속성을 null로 설정 하려면 **null** 또는 **[json (' null ')]** 을 사용할 수 있습니다. [Json 함수](template-functions-object.md#json) 는를 매개 변수로 제공할 때 빈 개체를 반환 합니다 `null` . 두 경우 모두 리소스 관리자 템플릿에서는 속성이 없는 것 처럼 처리 합니다.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>다음 단계

* 템플릿 함수의 전체 목록을 보려면 [Azure 리소스 관리자 템플릿 함수](template-functions.md)를 참조하세요.
* 템플릿 파일에 대 한 자세한 내용은 [Azure Resource Manager 템플릿 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
