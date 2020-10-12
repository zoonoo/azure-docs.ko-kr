---
title: 테스트 도구 키트에 대 한 테스트 사례
description: ARM 템플릿 테스트 도구 키트에 의해 실행 되는 테스트에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: dda8e92c17029126e7f473a6aee03acfc970e04b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378120"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>ARM 템플릿 테스트 도구 키트에 대 한 기본 테스트 사례

이 문서에서는 [템플릿 테스트 도구 키트](test-toolkit.md)를 사용 하 여 실행 되는 기본 테스트에 대해 설명 합니다. 테스트를 통과 또는 실패 하는 예제를 제공 합니다. 각 테스트의 이름을 포함 합니다.

## <a name="use-correct-schema"></a>올바른 스키마 사용

테스트 이름: **Deploymenttemplate 스키마가 올바릅니다** .

템플릿에서 유효한 스키마 값을 지정 해야 합니다.

다음 예에서는이 테스트를 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

템플릿의 schema 속성은 다음 스키마 중 하나로 설정 되어야 합니다.

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>매개 변수가 있어야 합니다.

테스트 이름: **Parameters 속성이 있어야 합니다** .

템플릿에 매개 변수 요소가 있어야 합니다. 매개 변수는 템플릿을 여러 환경에서 재사용할 수 있도록 하는 데 필수적입니다. 다른 환경에 배포할 때 변경 되는 값에 대 한 매개 변수를 템플릿에 추가 합니다.

다음 예제에서는이 테스트를 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>선언 된 매개 변수를 사용 해야 합니다.

테스트 이름: **매개 변수를 참조 해야 합니다** .

템플릿의 혼동을 줄이려면 정의 되었지만 사용 되지 않는 매개 변수를 삭제 합니다. 이 테스트는 템플릿의 모든 위치에서 사용 되지 않는 매개 변수를 찾습니다. 불필요 한 매개 변수를 제거 하면 불필요 한 값을 제공할 필요가 없기 때문에 템플릿을 쉽게 배포할 수 있습니다.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>보안 매개 변수에는 하드 코드 된 기본값을 사용할 수 없습니다.

테스트 이름: **보안 문자열 매개 변수에는 기본값을 사용할 수 없습니다** .

템플릿의 보안 매개 변수에 하드 코드 된 기본값을 제공 하지 마십시오. 기본값은 빈 문자열입니다.

암호와 같은 중요 한 값을 포함 하는 매개 변수에는 **SecureString** 또는 **secureobject** 유형을 사용 합니다. 매개 변수가 보안 유형을 사용 하는 경우 매개 변수 값이 기록 되지 않거나 배포 기록에 저장 되지 않습니다. 이 작업을 수행 하면 악의적인 사용자가 중요 한 값을 검색 하지 못합니다.

그러나 기본값을 제공 하는 경우 해당 값은 템플릿이나 배포 기록에 액세스할 수 있는 모든 사용자가 검색할 수 있습니다.

다음 예에서는이 테스트에 **실패** 합니다.

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

다음 예제에서는이 테스트를 **통과** 합니다.

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>환경 Url은 하드 코딩 될 수 없습니다.

테스트 이름: **Deploymenttemplate에 하드 코딩 Uri가 포함 되지 않아야 합니다** .

템플릿에서 환경 Url을 하드 코딩 하지 마세요. 대신 [환경 함수](template-functions-deployment.md#environment) 를 사용 하 여 배포 중에 이러한 url을 동적으로 가져옵니다. 차단 된 URL 호스트 목록은 [테스트 사례](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)를 참조 하세요.

URL이 하드 코딩 되었으므로 다음 예제는이 테스트에 **실패** 합니다.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

[Concat](template-functions-string.md#concat) 또는 [uri](template-functions-string.md#uri)와 함께 사용 하는 경우에도 테스트가 **실패** 합니다.

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

다음 예에서는이 테스트를 **통과** 합니다.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>위치에서 매개 변수 사용

테스트 이름: **위치는 하드 코드 되어서는 안 됩니다** .

템플릿의 사용자는 제한 된 영역을 사용할 수 있습니다. 리소스 위치를로 설정 하면 `"[resourceGroup().location]"` 리소스 그룹이 다른 사용자가 액세스할 수 없는 지역에 만들어졌을 수 있습니다. 이러한 사용자는 템플릿 사용을 차단 합니다.

각 리소스의 위치를 정의 하는 경우 리소스 그룹 위치에 대 한 기본값을 사용 하는 매개 변수를 사용 합니다. 사용자는이 매개 변수를 제공 하 여 편의를 위해 기본 값을 사용할 수 있지만 다른 위치를 지정할 수도 있습니다.

다음 예제에서는 리소스의 위치가로 설정 되어 있으므로이 테스트에 **실패** 합니다 `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

다음 예제에서는 위치 매개 변수를 사용 하지만 위치 매개 변수의 기본값은 하드 코드 된 위치 이므로이 테스트에 **실패** 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

대신 리소스 그룹 위치로 기본 설정 되는 매개 변수를 만들지만 사용자가 다른 값을 제공할 수 있도록 합니다. 다음 예에서는이 테스트를 **통과** 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="resources-should-have-location"></a>리소스에 위치가 있어야 합니다.

테스트 이름: **리소스에 위치가 있어야 합니다** .

리소스의 위치는 [템플릿 식](template-expressions.md) 또는로 설정 해야 합니다 `global` . 일반적으로 템플릿 식은 이전 테스트에서 설명한 location 매개 변수를 사용 합니다.

다음 예에서는 위치가 식이나가 아니기 때문에이 테스트에 **실패** 합니다 `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

다음 예에서는이 테스트를 **통과** 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

다음 예제에서는이 테스트도 **통과** 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>VM 크기 사용 매개 변수

테스트 이름: **VM 크기는 매개 변수 여야 합니다** .

가상 컴퓨터 크기를 하드 코딩 하지 마세요. 템플릿의 사용자가 배포 된 가상 머신의 크기를 수정할 수 있도록 매개 변수를 제공 합니다.

다음 예제에서는이 테스트가 **실패** 합니다.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

대신 매개 변수를 제공 합니다.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

그런 다음 VM 크기를 해당 매개 변수로 설정 합니다.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Min 및 max 값은 숫자입니다.

테스트 이름: **Min 및 Max 값은 숫자입니다** .

매개 변수에 대 한 최소값 및 최대값을 정의 하는 경우 해당 값을 숫자로 지정 합니다.

다음 예에서는이 테스트에 **실패** 합니다.

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

대신 숫자 값을 제공 합니다. 다음 예제에서는이 테스트를 **통과** 합니다.

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

최소값 또는 최대값을 제공 하지만 다른 값은 제공 하지 않는 경우에도이 경고가 발생 합니다.

## <a name="artifacts-parameter-defined-correctly"></a>아티팩트 매개 변수가 올바르게 정의 되었습니다.

테스트 이름: **아티팩트 매개 변수**

및에 대 한 매개 변수를 포함 하는 경우 `_artifactsLocation` `_artifactsLocationSasToken` 올바른 기본값과 유형을 사용 합니다. 이 테스트를 통과 하려면 다음 조건을 충족 해야 합니다.

* 하나의 매개 변수를 제공 하는 경우 다른 매개 변수를 제공 해야 합니다.
* `_artifactsLocation`**문자열** 이어야 합니다.
* `_artifactsLocation` 주 템플릿의 기본값이 있어야 합니다.
* `_artifactsLocation` 중첩 된 템플릿에는 기본값을 사용할 수 없습니다. 
* `_artifactsLocation``"[deployment().properties.templateLink.uri]"`기본값에 대해 또는 원시 리포지토리 URL이 있어야 합니다.
* `_artifactsLocationSasToken`**secureString** 이어야 합니다.
* `_artifactsLocationSasToken` 기본값에는 빈 문자열만 사용할 수 있습니다.
* `_artifactsLocationSasToken` 중첩 된 템플릿에는 기본값을 사용할 수 없습니다. 

## <a name="declared-variables-must-be-used"></a>선언 된 변수를 사용 해야 합니다.

테스트 이름: **변수를 참조 해야 합니다** .

템플릿에서 혼동을 줄이려면 정의 되었지만 사용 되지 않는 모든 변수를 삭제 합니다. 이 테스트는 템플릿의 모든 위치에서 사용 되지 않는 변수를 찾습니다.

## <a name="dynamic-variable-should-not-use-concat"></a>동적 변수는 concat을 사용 하면 안 됩니다.

테스트 이름: **동적 변수 참조는 Concat를 사용 하면 안 됩니다** .

다른 변수 또는 매개 변수의 값을 기반으로 변수를 동적으로 생성 해야 하는 경우도 있습니다. 값을 설정할 때 [concat](template-functions-string.md#concat) 함수를 사용 하지 마세요. 대신, 사용 가능한 옵션을 포함 하는 개체를 사용 하 고 배포 하는 동안 개체의 속성 중 하나를 동적으로 가져옵니다.

다음 예에서는이 테스트를 **통과** 합니다. 이러한 **변수는 배포 하는 동안** 동적으로 설정 됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>최근 API 버전 사용

테스트 이름: **Apiversions는 최신 버전 이어야 합니다** .

각 리소스에 대 한 API 버전은 최신 버전을 사용 해야 합니다. 테스트는 사용 하는 버전을 해당 리소스 종류에 사용할 수 있는 버전에 대해 평가 합니다.

## <a name="use-hardcoded-api-version"></a>하드 코딩 API 버전 사용

테스트 이름: **공급자 apiVersions를 사용할 수 없습니다** .

리소스 유형에 대 한 API 버전은 사용할 수 있는 속성을 결정 합니다. 템플릿에 하드 코드 된 API 버전을 제공 합니다. 배포 중에 결정 된 API 버전을 검색 하지 않습니다. 사용할 수 있는 속성을 알 수 없습니다.

다음 예제에서는이 테스트가 **실패** 합니다.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

다음 예에서는이 테스트를 **통과** 합니다.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>속성은 비워 둘 수 없습니다.

테스트 이름: **템플릿에 공백이 포함 되 면 안 됩니다** .

속성을 빈 값으로 하드 코딩 하지 마세요. 빈 값은 null, 빈 문자열, 개체 또는 배열을 포함 합니다. 속성을 빈 값으로 설정한 경우 템플릿에서 해당 속성을 제거 합니다. 그러나 매개 변수를 통해 배포 하는 동안 속성을 빈 값으로 설정 하는 것이 좋습니다.

## <a name="use-resource-id-functions"></a>리소스 ID 함수 사용

테스트 이름: **Id는 Resourceid에서 파생 되어야 합니다** .

리소스 ID를 지정 하는 경우 리소스 ID 함수 중 하나를 사용 합니다. 허용 되는 함수는 다음과 같습니다.

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Concat 함수를 사용 하 여 리소스 ID를 만들지 마세요. 다음 예제에서는이 테스트가 **실패** 합니다.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

다음 예제에서는이 테스트를 **통과** 합니다.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>ResourceId 함수에 올바른 매개 변수가 있습니다.

테스트 이름: **resourceid는 다음을 포함할 수 없습니다** .

리소스 Id를 생성할 때는 선택적 매개 변수에 불필요 한 함수를 사용 하지 마세요. 기본적으로 [resourceId](template-functions-resource.md#resourceid) 함수는 현재 구독 및 리소스 그룹을 사용 합니다. 이러한 값은 제공 하지 않아도 됩니다.  

다음 예에서는 현재 구독 ID와 리소스 그룹 이름을 제공할 필요가 없기 때문에이 테스트에 **실패** 합니다.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

다음 예제에서는이 테스트를 **통과** 합니다.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

이 테스트는 다음에 적용 됩니다.

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [reference](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

및의 경우를 사용 하 여 `reference` `list*` 리소스 ID를 생성할 때 테스트가 **실패** `concat` 합니다.

## <a name="dependson-best-practices"></a>dependsOn 모범 사례

테스트 이름: **DependsOn 모범 사례**

배포 종속성을 설정 하는 경우 [if](template-functions-logical.md#if) 함수를 사용 하 여 조건을 테스트 하지 마세요. 조건에 따라 [배포](conditional-resource-deployment.md)되는 리소스에 종속 된 리소스를 사용 하는 경우 모든 리소스를 사용 하는 것과 마찬가지로 종속성을 설정 합니다. 조건부 리소스가 배포 되지 않은 경우 Azure Resource Manager은 필요한 종속성에서 자동으로 제거 합니다.

다음 예제에서는이 테스트가 **실패** 합니다.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

다음 예제에서는이 테스트를 **통과** 합니다.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>중첩 또는 연결 된 배포는 디버그를 사용할 수 없습니다.

테스트 이름: **배포 리소스는 디버그할 수 없습니다** .

**Microsoft .resources/배포** 리소스 종류를 사용 하 여 [중첩 또는 연결 된 템플릿을](linked-templates.md) 정의 하는 경우 해당 템플릿에 대해 디버깅을 사용 하도록 설정할 수 있습니다. 디버깅은 해당 템플릿을 테스트 해야 하지만 프로덕션에서 템플릿을 사용할 준비가 되었을 때 설정 해야 하는 경우에는 문제가 없습니다.

## <a name="admin-user-names-cant-be-literal-value"></a>관리 사용자 이름은 리터럴 값일 수 없습니다.

테스트 이름: **Adminusername은 리터럴이 아니어야 합니다** .

관리 사용자 이름을 설정할 때는 리터럴 값을 사용 하지 마세요.

다음 예에서는이 테스트에 **실패** 합니다.

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

대신 매개 변수를 사용 합니다. 다음 예제에서는이 테스트를 **통과** 합니다.

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>최신 VM 이미지 사용

테스트 이름: **VM 이미지는 최신 버전을 사용 해야 합니다** .

템플릿에 이미지를 포함 하는 가상 컴퓨터가 포함 된 경우 최신 버전의 이미지를 사용 하 고 있는지 확인 합니다.

## <a name="use-stable-vm-images"></a>안정적인 VM 이미지 사용

테스트 이름: **Virtual Machines 미리 보기로 서는 안 됩니다** .

가상 컴퓨터는 미리 보기 이미지를 사용 하지 않아야 합니다.

다음 예제에서는이 테스트가 **실패** 합니다.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

다음 예에서는이 테스트를 **통과** 합니다.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Microsoft.managedidentity 확장 사용 안 함

테스트 이름: **ManagedIdentityExtension를 사용 하면 안 됩니다** .

가상 컴퓨터에 Microsoft.managedidentity 확장을 적용 하지 마세요. 자세한 내용은 [가상 컴퓨터 관리 id 확장 사용을 중지 하는 방법 및 Azure Instance Metadata Service 사용 시작](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md)을 참조 하세요.

## <a name="outputs-cant-include-secrets"></a>출력에는 비밀을 포함할 수 없습니다.

테스트 이름: **출력은 암호를 포함할 수 없습니다** .

잠재적으로 비밀을 노출 하는 출력 섹션에 값을 포함 하지 마세요. 템플릿의 출력은 배포 기록에 저장 되므로 악의적인 사용자가 해당 정보를 찾을 수 있습니다.

다음 예제에서는 출력 값에 보안 매개 변수를 포함 하기 때문에 테스트에 **실패** 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

다음 예는 출력에서 [list *](template-functions-resource.md#list) 함수를 사용 하기 때문에 **실패** 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

테스트 도구 키트를 실행 하는 방법에 대 한 자세한 내용은 [ARM 템플릿 테스트 도구 키트 사용](test-toolkit.md)을 참조 하세요.