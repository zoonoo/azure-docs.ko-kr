---
title: JSON 및 Bicep의 Azure Resource Manager 템플릿에 대한 구문 비교
description: JSON 및 Bicep으로 개발된 Azure Resource Manager 템플릿들을 비교하고, 언어 간에 변환하는 방법을 보여 줍니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a3df1189b264b092a7575cd976c23d89aa12bf18
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537121"
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

리소스 속성 설정하기:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

템플릿에서 리소스의 리소스 ID 가져오기:

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

Bicep의 경우 명시적 종속성을 설정할 수 있지만 이 방법은 권장되지 않습니다. 대신 암시적 종속성을 사용하세요. 암시적 종속성은 한 리소스 선언이 다른 리소스 선언의 식별자를 참조할 때 만들어집니다.

다음은 네트워크 보안 그룹에 대한 암시적 종속성이 있는 네트워크 인터페이스를 보여 줍니다. `nsg.id`를 사용하여 네트워크 보안 그룹을 참조합니다.

```bicep
resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = {
  ...
}

resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
  name: nic1Name
  location: location
  properties: {
    ...
    networkSecurityGroup: {
      id: nsg.id
    }
  }
}
```

명시적 의존성을 설정해야 하는 경우 다음을 사용합니다.

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

* Bicep의 경우, [모듈](modules.md)을 사용합니다.
* ARM 템플릿의 경우 [연결된 템플릿](../templates/linked-templates.md)을 사용합니다.

## <a name="next-steps"></a>다음 단계

* Bicep에 대한 자세한 내용은 [Bicep 빠른 시작](./quickstart-create-bicep-use-visual-studio-code.md)을 참조하세요.
* 언어 간 템플릿 변환에 대한 자세한 내용은 [JSON과 Bicep 간에 ARM 템플릿 변환](decompile.md)을 참조하세요.
