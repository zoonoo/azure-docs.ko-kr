---
title: 템플릿의 변수
description: Azure Resource Manager 템플릿 (ARM 템플릿) 및 Bicep 파일에서 변수를 정의 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: e00a9e8e1801725707bac2abdc67512477e2cf07
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700340"
---
# <a name="variables-in-arm-templates"></a>ARM 템플릿의 변수

이 문서에서는 Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일에서 변수를 정의 하 고 사용 하는 방법을 설명 합니다. 변수를 사용 하 여 템플릿을 단순화 합니다. 템플릿 전체에서 복잡 한 식을 반복 하는 대신 복잡 한 식을 포함 하는 변수를 정의 합니다. 그런 다음 템플릿 전체에서 필요에 따라 해당 변수를 사용 합니다.

리소스 관리자는 배포 작업을 시작 하기 전에 변수를 확인 합니다. 템플릿에서 변수를 사용하는 모든 경우에는 Resource Manager는 변수를 확인된 값으로 바꿉니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-variable"></a>변수 정의

변수를 정의할 때 변수에 대 한 [데이터 형식을](template-syntax.md#data-types) 지정 하지 않습니다. 대신 값 또는 템플릿 식을 제공 합니다. 변수 형식은 확인 된 값에서 유추 됩니다. 다음 예에서는 변수를 문자열로 설정 합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "stringVar": "example value"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var stringVar = 'example value'
```

---

변수를 생성할 때 매개 변수 또는 다른 변수에서 값을 사용할 수 있습니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}-addtovar'
var concatToParam = '${inputValue}-addtoparam'
```

---

[템플릿 함수](template-functions.md) 를 사용 하 여 변수 값을 생성할 수 있습니다.

다음 예에서는 저장소 계정 이름에 대 한 문자열 값을 만듭니다. 여러 템플릿 함수를 사용 하 여 매개 변수 값을 가져오고이를 고유한 문자열에 연결 합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

---

JSON 템플릿에서는 [reference](template-functions-resource.md#reference) 함수 또는 [list](template-functions-resource.md#list) 함수를 변수 선언에 사용할 수 없습니다. 이러한 함수는 리소스의 런타임 상태를 가져오며, 변수가 확인 될 때 배포 전에 실행할 수 없습니다.

Bicep 파일에서 참조 및 목록 함수는 변수를 선언할 때 유효 합니다.

## <a name="use-variable"></a>변수 사용

다음 예제에서는 리소스 속성에 대 한 변수를 사용 하는 방법을 보여 줍니다.

# <a name="json"></a>[JSON](#tab/json)

JSON 템플릿에서 [variables](template-functions-deployment.md#variables) 함수를 사용 하 여 변수 값을 참조 합니다.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep 파일에서 변수 이름을 제공 하 여 변수의 값을 참조 합니다.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
```

---

## <a name="example-template"></a>예제 템플릿

다음 템플릿은 리소스를 배포 하지 않습니다. 다양 한 형식의 변수를 선언 하는 몇 가지 방법을 보여 줍니다.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep는 현재 루프를 지원 하지 않습니다.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

---

## <a name="configuration-variables"></a>구성 변수

환경을 구성 하는 데 관련 된 값을 포함 하는 변수를 정의할 수 있습니다. 변수를 값이 포함 된 개체로 정의 합니다. 다음 예제에서는 **테스트** 와 **prod** 의 두 환경에 대 한 값을 보유 하는 개체를 보여 줍니다. 배포 하는 동안 이러한 값 중 하나를 전달 합니다.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

---

## <a name="next-steps"></a>다음 단계

* 변수에 사용할 수 있는 속성에 대 한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
* 변수를 만드는 방법에 대 한 권장 사항은 [모범 사례-변수](template-best-practices.md#variables)를 참조 하세요.
* 네트워크 보안 그룹에 보안 규칙을 할당 하는 예제 템플릿은 [네트워크 보안 규칙](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) 및 [매개 변수 파일](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)을 참조 하세요.
