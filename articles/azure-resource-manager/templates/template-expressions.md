---
title: 템플릿 구문 및 식
description: Azure 리소스 관리자 템플릿에 대 한 선언적 JSON 구문을 설명 합니다.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460112"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿의 구문 및 식

템플릿의 기본 구문은 JSON이지만, 그러나 식을 사용하여 템플릿 내에서 사용할 수 있는 JSON 값을 확장할 수 있습니다.  식은 `[` 및 `]` 대괄호로 각각 시작 및 종료됩니다. 식의 값은 템플릿을 배포할 때 평가됩니다. 식은 문자열, 정수, 부울, 배열 또는 개체를 반환할 수 있습니다.

템플릿 표현식은 24,576자를 초과할 수 없습니다.

## <a name="use-functions"></a>함수 사용

Azure 리소스 관리자는 템플릿에서 사용할 수 있는 [함수를](template-functions.md) 제공합니다. 다음 예제에서는 매개 변수의 기본값에 함수를 사용하는 식을 보여 주며 있습니다.

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

식 내에서 구문은 `resourceGroup()` Resource Manager가 템플릿 내에서 사용하기 위해 제공하는 함수 중 하나를 호출합니다. 이 경우 [리소스그룹](template-functions-resource.md#resourcegroup) 함수입니다. JavaScript에서와 마찬가지로 함수 호출은 `functionName(arg1,arg2,arg3)`과 같이 형식이 지정됩니다. 구문은 `.location` 해당 함수에서 반환되는 개체에서 하나의 속성을 검색합니다.

템플릿 함수 및 해당 매개 변수는 대/소문자를 구분하지 않습니다. 예를 들어 Resource Manager에서 **variables('var1')** 와 **VARIABLES('VAR1')** 는 동일합니다. 계산될 때 함수는 대/소문자를 명시적으로 수정하지 않는 한(toUpper 또는 toLower 등) 대/소문자를 보존합니다. 특정 리소스 유형에는 함수를 평가하는 방법과 별개의 대/소문자 요구 사항이 있을 수 있습니다.

함수에 매개 변수로 문자열 값을 전달하려면 single 따옴표를 사용합니다.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

대부분의 함수는 리소스 그룹, 구독, 관리 그룹 또는 테넌트에 배포하든 동일하게 작동합니다. 다음 함수에는 범위에 따라 제한이 있습니다.

* [리소스 그룹](template-functions-resource.md#resourcegroup) - 리소스 그룹에 배포에서만 사용할 수 있습니다.
* [resourceId](template-functions-resource.md#resourceid) - 모든 범위에서 사용할 수 있지만 유효한 매개 변수는 범위에 따라 변경됩니다.
* [구독](template-functions-resource.md#subscription) - 리소스 그룹 또는 구독에 대한 배포에서만 사용할 수 있습니다.

## <a name="escape-characters"></a>이스케이프 문자

리터럴 문자열이 왼쪽 대괄호로 시작하고 오른쪽 `[` `]`대괄호로 끝을 갖도록 하지만 식으로 해석되지 않은 `[[`경우 를 사용하여 문자열을 시작하는 추가 대괄호를 추가합니다. 예를 들어 변수는 다음과 같은 것입니다.

```json
"demoVar1": "[[test value]"
```

`[test value]`로 확인합니다.

그러나 리터럴 문자열이 괄호로 끝나지 않으면 첫 번째 대괄호를 이스케이프하지 마십시오. 예를 들어 변수는 다음과 같은 것입니다.

```json
"demoVar2": "[test] value"
```

`[test] value`로 확인합니다.

템플릿에 JSON 개체를 추가하는 것과 같이 식에서 큰따옴표를 이스케이프하려면 백슬래시를 사용합니다.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

매개 변수 값을 전달할 때 이스케이프 문자의 사용은 매개 변수 값이 지정된 위치에 따라 달라집니다. 템플릿에서 기본값을 설정하면 왼쪽 대괄호가 추가로 필요합니다.

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

기본값을 사용하는 경우 템플릿이 `[test value]`반환됩니다.

그러나 명령줄을 통해 매개 변수 값을 전달하면 문자가 문자 문자로 문자로 해석됩니다. 다음을 통해 이전 템플릿 배포:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

`[[test value]`를 반환합니다. 대신 다음을 사용합니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

매개 변수 파일에서 값을 전달할 때도 동일한 서식이 적용됩니다. 문자는 문자 그대로 해석됩니다. 앞의 템플릿과 함께 사용하면 다음 매개 `[test value]`변수 파일이 반환됩니다.

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

속성을 null로 설정하려면 **null** 또는 **[json('null')]을**사용할 수 있습니다. [json 함수는](template-functions-array.md#json) 매개 변수로 `null` 제공할 때 빈 개체를 반환합니다. 두 경우 모두 리소스 관리자 템플릿은 속성이 없는 것처럼 처리합니다.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>다음 단계

* 템플릿 함수의 전체 목록을 보려면 [Azure 리소스 관리자 템플릿 함수](template-functions.md)를 참조하세요.
* 템플릿 파일에 대한 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해하기를](template-syntax.md)참조하십시오.
