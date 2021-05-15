---
title: JSON 및 Bicep의 Azure Resource Manager 템플릿에 대한 구문 비교
description: JSON 및 Bicep으로 개발된 Azure Resource Manager 템플릿들을 비교하고, 언어 간에 변환하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461797"
---
# <a name="comparing-json-and-bicep-for-templates"></a>JSON 및 Bicep의 템플릿 비교

이 문서에서는 Azure Resource Manager 템플릿(ARM 템플릿)에서 Bicep 구문과 JSON 구문을 비교합니다. 대부분의 경우 Bicep는 JSON의 경우 보다 자세한 정보가 적은 구문을 제공합니다.

JSON을 사용하여 ARM 템플릿을 개발하는 방법을 잘 알고 있는 경우, 다음 예제를 사용하여 Bicep의 해당 구문에 대해 알아보세요.

## <a name="expressions"></a>식

식 만들기:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>매개 변수

기본값으로 매개 변수를 선언하기:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

매개 변수 값 가져오기:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>variables

변수를 선언하기:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

변수 값 가져오기:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>문자열

문자열 연결하기:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>논리 연산자

논리적 **AND** 를 반환하기:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

조건에 따라 값을 설정하기:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>배포 범위

배포의 대상 범위를 설정하기:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>리소스

리소스를 선언하기:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

리소스를 조건부로 배포하기:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

리소스 속성을 설정하기:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

템플릿에서 리소스의 리소스 ID를 가져오기:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>루프

배열이나 개수의 항목을 반복하기:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>리소스 종속성

리소스 간 종속성 설정하기:

Bicep의 경우, 종속성의 자동 검색을 사용하거나 수동으로 종속성을 설정합니다.

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>참조 리소스

템플릿의 리소스에서 속성을 가져오기:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

템플릿에 배포되지 않은 기존 리소스에서 속성을 가져오기:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>출력

템플릿의 리소스에서 속성을 출력하기:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>코드 재사용

솔루션을 여러 파일로 분리하기:

* Bicep의 경우, [모듈](bicep-tutorial-add-modules.md)을 사용합니다.
* JSON의 경우, [연결된 템플릿을](linked-templates.md)사용합니다.

## <a name="recommendations"></a>권장 사항

* 가능하면 Bicep 파일에 [참조](template-functions-resource.md#reference) 및 [resourceId](template-functions-resource.md#resourceid) 함수를 사용하지 마십시오. 동일한 Bicep 배포에서 리소스를 참조하는 경우에는 리소스 식별자를 대신 사용합니다. 예를 들어, 리소스 식별자로 `stg`을 사용하여 Bicep 파일에 리소스를 배포한 경우, `stg.id`나 `stg.properties.primaryEndpoints.blob`과 같은 구문을 사용하여 속성 값을 가져옵니다. 리소스 식별자를 사용하여 리소스 간에 암시적 종속성을 만듭니다. DependsOn 속성을 사용하여 종속성을 명시적으로 설정할 필요는 없습니다.
* 리소스가 Bicep 파일에 배포되지 않은 경우, **기존** 키워드를 사용하여 리소스에 대한 기호화된 참조를 가져올 수 있습니다.
* 식별자에 대해 일관된 대/소문자 구분을 사용합니다. 사용할 대/소문자 구분 유형을 잘 모르겠으면 카멜식 대/소문자 구분을 시도합니다. 예: `param myCamelCasedParameter string`.
* 설명에서 사용자에게 중요한 정보를 제공하는 경우에만 매개 변수에 설명을 추가합니다. 일부 정보에는 `//` 주석을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Bicep에 대한 자세한 내용은 [Bicep 자습서](./bicep-tutorial-create-first-bicep.md)를 참조하세요.
* 언어 간 템플릿 변환에 대한 자세한 내용은 [JSON과 Bicep 간에 ARM 템플릿 변환](bicep-decompile.md)을 참조하세요.
