---
title: 템플릿의 매개 변수
description: Azure Resource Manager 템플릿 (ARM 템플릿) 및 Bicep 파일에서 매개 변수를 정의 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b23417766524204e490450568bb80b8c49e2d328
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043935"
---
# <a name="parameters-in-arm-templates"></a>ARM 템플릿의 매개 변수

이 문서에서는 Azure Resource Manager 템플릿 (ARM 템플릿) 및 Bicep 파일에서 매개 변수를 정의 하 고 사용 하는 방법을 설명 합니다. 매개 변수에 대해 서로 다른 값을 제공 하 여 다른 환경에 대 한 템플릿을 다시 사용할 수 있습니다.

리소스 관리자는 배포 작업을 시작 하기 전에 매개 변수 값을 확인 합니다. 템플릿에서 매개 변수를 사용 하는 경우에는 리소스 관리자이 매개 변수를 확인 된 값으로 바꿉니다.

각 매개 변수는 [데이터 형식](template-syntax.md#data-types)중 하나로 설정 되어야 합니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="minimal-declaration"></a>최소 선언

모든 매개 변수에는 최소한 이름 및 형식이 필요 합니다. Bicep에서 매개 변수는 동일한 범위에 있는 변수, 리소스, 출력 또는 다른 매개 변수와 동일한 이름을 가질 수 없습니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

---

## <a name="secure-parameters"></a>보안 매개 변수

문자열 또는 개체 매개 변수를 안전한 것으로 표시할 수 있습니다. 보안 매개 변수의 값은 배포 기록에 저장 되지 않으며 기록 되지 않습니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

---

## <a name="allowed-values"></a>허용되는 값

매개 변수에 대해 허용 되는 값을 정의할 수 있습니다. 배열에 허용 되는 값을 제공 합니다. 허용 되는 값 중 하나가 아닌 매개 변수에 대 한 값이 전달 되 면 유효성 검사 중에 배포가 실패 합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

---

## <a name="default-value"></a>기본값

매개 변수의 기본값을 지정할 수 있습니다. 기본값은 배포 중에 값이 제공 되지 않을 때 사용 됩니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoParam string = 'Contoso'
```

---

매개 변수의 다른 속성과 함께 기본값을 지정 하려면 다음 구문을 사용 합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

---

기본값을 포함 하는 식을 사용할 수 있습니다. [Reference](template-functions-resource.md#reference) 함수 또는 [list](template-functions-resource.md#list) 함수는 매개 변수 섹션에서 사용할 수 없습니다. 이러한 함수는 리소스의 런타임 상태를 가져오고 매개 변수를 확인할 때 배포 전에 실행할 수 없습니다.

다른 매개 변수 속성에는 식을 사용할 수 없습니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

다른 매개 변수 값을 사용하여 기본값을 빌드할 수 있습니다. 다음 템플릿은 사이트 이름에서 호스트 계획 이름을 생성 합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

---

## <a name="length-constraints"></a>길이 제약 조건

문자열 및 배열 매개 변수의 최소 및 최대 길이를 지정할 수 있습니다. 제약 조건 중 하나 또는 둘 다를 설정할 수 있습니다. 문자열의 경우 길이는 문자 수를 나타냅니다. 배열의 경우 길이는 배열의 항목 수를 나타냅니다.

다음 예제에서는 두 개의 매개 변수를 선언 합니다. 하나의 매개 변수는 3-24 문자를 포함 해야 하는 저장소 계정 이름에 대 한 매개 변수입니다. 다른 매개 변수는 1-5 항목에서 가져와야 하는 배열입니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

---

## <a name="integer-constraints"></a>정수 제약 조건

정수 매개 변수의 최소값과 최 댓 값을 설정할 수 있습니다. 제약 조건 중 하나 또는 둘 다를 설정할 수 있습니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(1)
@maxValue(12)
param month int
```

---

## <a name="description"></a>설명

매개 변수에 대 한 설명을 추가 하 여 템플릿의 사용자가 제공 하는 값을 이해할 수 있도록 할 수 있습니다. 포털을 통해 템플릿을 배포하는 경우 설명에 제공하는 텍스트가 자동으로 해당 매개 변수에 대한 팁으로 사용됩니다. 텍스트에 매개 변수 이름에서 유추할 수 있는 것 보다 많은 정보가 제공 되는 경우에만 설명을 추가 합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

---

## <a name="use-parameter"></a>매개 변수 사용

JSON 템플릿에서 [parameters](template-functions-deployment.md#parameters) 함수를 사용 하 여 매개 변수의 값을 참조 합니다. Bicep에서 매개 변수 이름을 사용 합니다. 다음 예에서는 Key Vault 이름에 대 한 매개 변수 값을 사용 합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

---

## <a name="objects-as-parameters"></a>개체를 매개 변수로 사용

관련 값을 개체로 전달하면 더 쉽게 구성할 수 있습니다. 이렇게 하면 템플릿의 매개 변수 수도 줄어듭니다.

다음 예제에서는 개체인 매개 변수를 보여 줍니다. 기본값은 개체의 예상 속성을 표시 합니다. 이러한 속성은 배포할 리소스를 정의할 때 사용 됩니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
      "type": "object",
      "defaultValue": {
        "name": "VNet1",
        "location": "eastus",
        "addressPrefixes": [
          {
            "name": "firstPrefix",
            "addressPrefix": "10.0.0.0/22"
          }
        ],
        "subnets": [
          {
            "name": "firstSubnet",
            "addressPrefix": "10.0.0.0/24"
          },
          {
            "name": "secondSubnet",
            "addressPrefix": "10.0.1.0/24"
          }
        ]
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

---

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 매개 변수를 사용 하는 시나리오를 보여 줍니다.

|템플릿  |설명  |
|---------|---------|
|[parameters with functions for default values](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json)(기본값에 대한 함수가 있는 매개 변수) | 매개 변수의 기본값을 정의할 때 템플릿 함수를 사용하는 방법을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |
|[parameter 개체](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | 매개 변수에 대한 개체 사용을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |

## <a name="next-steps"></a>다음 단계

* 매개 변수에 사용할 수 있는 속성에 대 한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
* 매개 변수 값을 파일로 전달 하는 방법에 대 한 자세한 내용은 [리소스 관리자 매개 변수 파일 만들기](parameter-files.md)를 참조 하세요.
* 매개 변수 만들기에 대 한 권장 사항은 [모범 사례-매개 변수](template-best-practices.md#parameters)를 참조 하세요.
