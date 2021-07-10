---
title: 테스트 도구 키트에 대한 테스트 사례
description: ARM 템플릿 테스트 도구 키트를 통해 실행되는 테스트에 대해 설명합니다.
ms.topic: conceptual
ms.date: 05/17/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 8e771d8c15e26367ab205ea77a451fae443ac981
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110064399"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>ARM 템플릿 테스트 도구 키트의 기본 테스트 사례

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)에 대한 [템플릿 테스트 툴킷](test-toolkit.md)으로 실행되는 기본 테스트에 대해 설명합니다. 테스트에 합격하거나 불합격하는 예를 제공합니다. 여기에는 각 테스트의 이름이 포함됩니다. 특정 테스트를 실행하려면 [매개 변수 테스트](test-toolkit.md#test-parameters)를 참조하세요.

## <a name="use-correct-schema"></a>올바른 스키마 사용

테스트 이름: **Deploymenttemplate 스키마가 올바름**

템플릿에서 유효한 스키마 값을 지정해야 합니다.

다음 예에서는 이 테스트를 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

템플릿의 schema 속성은 다음 스키마 중 하나로 설정되어야 합니다.

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="declared-parameters-must-be-used"></a>선언된 매개 변수를 사용해야 함

테스트 이름: **매개 변수를 참조 해야함**

템플릿의 혼동을 줄이려면 정의되었지만 사용되지 않는 매개 변수를 모두 삭제합니다. 이 테스트는 템플릿의 모든 위치에서 사용되지 않는 매개 변수를 찾습니다. 사용하지 않는 매개 변수를 제거하면 불필요한 값을 제공하지 않아도 되므로 템플릿을 더 쉽게 배포할 수 있습니다.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>보안 매개 변수에는 하드 코드된 기본값을 사용할 수 없음

테스트 이름: **보안 문자열 매개 변수에는 기본값을 사용할 수 없음**

템플릿의 보안 매개 변수에 하드 코드된 기본값을 제공하지 마세요. 기본값은 빈 문자열입니다.

암호와 같은 중요한 값을 포함하는 매개 변수에는 **SecureString** 또는 **SecureObject** 유형을 사용합니다. 매개 변수가 보안 유형을 사용하는 경우 매개 변수 값이 기록되지 않거나 배포 기록에 저장되지 않습니다. 이 작업을 수행하면 악의적인 사용자가 중요한 값을 검색하지 못합니다.

하지만 기본값을 제공하는 경우 해당 값은 템플릿이나 배포 기록에 액세스할 수 있는 모든 사용자가 검색할 수 있습니다.

다음 예에서는 이 테스트에 **실패** 합니다.

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

다음 예에서는 이 테스트를 **통과** 합니다.

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>환경 URL은 하드 코드될 수 없음

테스트 이름: **DeploymentTemplate에 하드 코드된 URI가 포함되지 않아야 함**

템플릿에서 환경 URL을 하드 코드하지 마세요. 대신 [환경 함수](template-functions-deployment.md#environment)를 사용하여 배포 중에 이러한 URL을 동적으로 가져옵니다. 차단된 URL 호스트 목록은 [테스트 사례](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)를 참조하세요.

URL이 하드 코드되었으므로 다음 예제는 이 테스트에 **실패** 합니다.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

[concat](template-functions-string.md#concat) 또는 [uri](template-functions-string.md#uri)와 함께 사용하는 경우에도 테스트에 **실패** 합니다.

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

다음 예에서는 이 테스트를 **통과** 합니다.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>위치에서 매개 변수 사용

테스트 이름: **위치는 하드 코드되어서는 안 됨**

템플릿에 location이라는 매개 변수를 포함해야 합니다. 이 매개 변수를 사용하여 템플릿에서 리소스의 위치를 설정합니다. 기본 템플릿(_azuredeploy.json_ 또는 _mainTemplate.json_)에서 이 매개 변수는 기본적으로 리소스 그룹 위치로 지정할 수 있습니다. 연결되거나 중첩된 템플릿에서 위치 매개 변수에는 기본 위치를 사용할 수 없습니다.

템플릿의 사용자는 제한된 영역을 사용할 수 있습니다. 리소스 위치를 하드 코드하는 경우 사용자가 해당 지역에서 리소스를 만들지 못하도록 차단될 수 있습니다. 리소스 위치를 `"[resourceGroup().location]"`으로 설정한 경우에도 사용자가 차단될 수 있습니다. 다른 사용자가 액세스할 수 없는 지역에 리소스 그룹이 만들어졌을 수 있습니다. 이러한 사용자는 템플릿 사용이 차단됩니다.

리소스 그룹 위치를 기본값으로 하는 위치 매개 변수를 제공하여 사용자는 편할 때 기본값을 사용할 수 있지만 다른 위치를 지정할 수도 있습니다.

다음 예제에서는 리소스의 위치가 **으로 설정되어 있기 때문에 이 테스트에** 실패`resourceGroup().location`합니다.

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

다음 예제에서는 위치 매개 변수를 사용하지만 위치 매개 변수가 하드 코드된 위치를 기본값으로 하기 때문에 이 테스트에 **실패** 합니다.

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

대신 리소스 그룹 위치를 기본값으로 하는 매개 변수를 만들지만 사용자가 다른 값을 제공할 수 있도록 합니다. 다음 예제에서는 템플릿이 기본 템플릿으로 사용되는 경우 이 테스트에 **통과** 합니다.

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

하지만 앞의 예제를 연결된 템플릿으로 사용하는 경우 테스트에 **실패** 합니다. 연결된 템플릿으로 사용하는 경우 기본값을 제거합니다.

## <a name="resources-should-have-location"></a>리소스에 위치가 있어야 함

테스트 이름: **리소스에 위치가 있어야 함**

리소스의 위치는 [템플릿 식](template-expressions.md) 또는 `global`으로 설정해야 합니다. 일반적으로 템플릿 식은 이전 테스트에서 설명한 location 매개 변수를 사용합니다.

다음 예제에서는 위치가 식 또는 `global`이 아니기 때문에 이 테스트에 **실패** 합니다.

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

다음 예에서는 이 테스트를 **통과** 합니다.

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

다음 예제에서도 이 테스트에 **통과** 합니다.

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

## <a name="vm-size-uses-parameter"></a>VM 크기에서 매개 변수 사용

테스트 이름: **VM 크기는 매개 변수여야 함**

가상 컴퓨터 크기를 하드 코드하지 마세요. 템플릿의 사용자가 배포된 가상 머신의 크기를 수정할 수 있도록 매개 변수를 제공합니다.

다음 예제에서는 이 테스트에 **실패** 합니다.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

대신 매개 변수를 입력합니다.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

그런 다음 VM 크기를 해당 매개 변수로 설정합니다.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>최소 및 최대 값은 숫자

테스트 이름: **최소 및 최대 값은 숫자**

매개 변수에 대한 최솟값 및 최댓값을 정의하는 경우 숫자로 지정합니다.

다음 예에서는 이 테스트에 **실패** 합니다.

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

대신 숫자 값을 제공합니다. 다음 예제에서는 이 테스트를 **통과** 합니다.

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

최솟값 또는 최댓값을 제공하지만 다른 값은 제공하지 않는 경우에도 이 경고가 표시됩니다.

## <a name="artifacts-parameter-defined-correctly"></a>올바르게 정의된 아티팩트 매개 변수

테스트 이름: **아티팩트 매개 변수**

`_artifactsLocation` 및 `_artifactsLocationSasToken`에 대한 매개 변수를 포함하는 경우 올바른 기본값 및 형식을 사용하세요. 이 테스트를 통과하려면 다음 조건을 충족해야 합니다.

* 하나의 매개 변수를 제공하는 경우 다른 매개 변수를 제공해야 함
* `_artifactsLocation`은 **문자열** 이어야 함
* `_artifactsLocation`은 기본 템플릿에 기본값이 있어야 함
* `_artifactsLocation`은 중첩된 템플릿에 기본값을 사용할 수 없음
* `_artifactsLocation`은 해당 기본값으로 `"[deployment().properties.templateLink.uri]"` 또는 원시 리포지토리 URL이 있어야 함
* `_artifactsLocationSasToken`은 **secureString** 이어야 함
* `_artifactsLocationSasToken`은 해당 기본값으로 빈 문자열만 사용할 수 있음
* `_artifactsLocationSasToken`은 중첩된 템플릿에 기본값을 사용할 수 없음

## <a name="declared-variables-must-be-used"></a>선언된 변수를 사용해야 함

테스트 이름: **변수를 참조해야 함**

템플릿에서 혼동을 줄이려면 정의되었지만 사용되지 않는 모든 변수를 삭제합니다. 이 테스트는 템플릿의 모든 위치에서 사용되지 않는 변수를 찾습니다.

## <a name="dynamic-variable-should-not-use-concat"></a>동적 변수는 concat을 사용해서는 안 됨

테스트 이름: **동적 변수 참조는 concat을 사용해서는 안 됨**

다른 변수 또는 매개 변수의 값을 기반으로 변수를 동적으로 구성해야 하는 경우도 있습니다. 값을 설정할 때 [concat](template-functions-string.md#concat) 함수를 사용하지 마세요. 대신, 사용 가능한 옵션이 포함된 개체를 사용하고 배포 중에 개체에서 속성 중 하나를 동적으로 가져옵니다.

다음 예에서는 이 테스트를 **통과** 합니다. **currentImage** 변수는 배포 중에 동적으로 설정됩니다.

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

테스트 이름: **apiVersions는 최신 버전이어야 함**

각 리소스에 대한 API 버전은 최신 버전을 사용해야 합니다. 이 테스트는 사용하는 버전을 해당 리소스 종류에 사용할 수 있는 버전과 비교하여 평가합니다.

## <a name="use-hardcoded-api-version"></a>하드 코드된 API 버전 사용

테스트 이름: **공급자 apiVersions는 허용되지 않음**

리소스 종류에 대한 API 버전은 사용할 수 있는 속성을 결정합니다. 템플릿에 하드 코드된 API 버전을 제공합니다. 배포 중에 결정된 API 버전을 검색하지 마세요. 사용할 수 있는 속성을 알 수 없습니다.

다음 예제에서는 이 테스트에 **실패** 합니다.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

다음 예에서는 이 테스트를 **통과** 합니다.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>속성은 비워 둘 수 없음

테스트 이름: **템플릿에 공백을 포함할 수 없음**

속성을 빈 값으로 하드 코드하지 마세요. 빈 값은 null 및 빈 문자열, 개체 또는 배열을 포함합니다. 속성을 빈 값으로 설정한 경우 템플릿에서 해당 속성을 제거합니다. 하지만 매개 변수를 통해 배포 중에 속성을 빈 값으로 설정할 수 있습니다.

## <a name="use-resource-id-functions"></a>리소스 ID 함수 사용

테스트 이름: **ID는 ResourceID에서 파생되어야 함**

리소스 ID를 지정하는 경우 리소스 ID 함수 중 하나를 사용합니다. 허용되는 함수는 다음과 같습니다.

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

concat 함수를 사용하여 리소스 ID를 만들지 마세요. 다음 예제에서는 이 테스트에 **실패** 합니다.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

다음 예제에서는 이 테스트에 **통과** 합니다.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>ResourceId 함수에 올바른 매개 변수 포함

테스트 이름: **ResourceId에 포함할 수 없음**

리소스 ID를 생성할 때는 선택적 매개 변수에 불필요한 함수를 사용하지 마세요. 기본적으로 [resourceId](template-functions-resource.md#resourceid) 함수는 현재 구독 및 리소스 그룹을 사용합니다. 이러한 값은 제공하지 않아도 됩니다.

다음 예에서는 현재 구독 ID와 리소스 그룹 이름을 제공할 필요가 없기 때문에 이 테스트에 **실패** 합니다.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

다음 예제에서는 이 테스트에 **통과** 합니다.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

이 테스트는 다음과 같이 적용됩니다.

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [reference](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

`reference` 및 `list*`의 경우 리소스 ID 구성에 `concat`을 사용하는 경우 이 텍스트에 **실패** 합니다.

## <a name="dependson-best-practices"></a>dependsOn 모범 사례

테스트 이름: **DependsOn 모범 사례**

배포 종속성을 설정하는 경우 [if](template-functions-logical.md#if) 함수를 사용하여 조건을 테스트하지 마세요. 하나의 리소스가 [조건부 배포](conditional-resource-deployment.md)되는 리소스에 종속되는 경우 다른 리소스와 마찬가지로 종속성을 설정합니다. 조건부 리소스가 배포되지 않으면 Azure Resource Manager가 필요한 종속성에서 해당 리소스를 자동으로 제거합니다.

다음 예제에서는 이 테스트에 **실패** 합니다.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

다음 예제에서는 이 테스트에 **통과** 합니다.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>중첩 또는 연결된 배포는 디버그를 사용할 수 없음

테스트 이름: **배포 리소스는 디버그할 수 없음**

**Microsoft.Resources/deployments** 리소스 종류를 사용하여 [중첩 또는 연결된 템플릿](linked-templates.md)을 정의하는 경우 해당 템플릿에 대해 디버깅을 사용하도록 설정할 수 있습니다. 디버깅은 해당 템플릿을 테스트해야 하지만 프로덕션에서 템플릿을 사용할 준비가 되었을 때 전환해야 하는 경우 사용할 수 있습니다.

## <a name="admin-user-names-cant-be-literal-value"></a>관리 사용자 이름은 리터럴 값일 수 없음

테스트 이름: **adminUsername은 리터럴이 아니어야 함**

관리 사용자 이름을 설정할 때는 리터럴 값을 사용하지 마세요.

다음 예에서는 이 테스트에 **실패** 합니다.

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

대신 매개 변수를 사용합니다. 다음 예제에서는 이 테스트를 **통과** 합니다.

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>최신 VM 이미지 사용

테스트 이름: **VM 이미지는 최신 버전을 사용해야 함**

템플릿에 이미지가 있는 가상 컴퓨터가 포함된 경우 최신 버전의 이미지를 사용하고 있는지 확인합니다.

## <a name="use-stable-vm-images"></a>안정적인 VM 이미지 사용

테스트 이름: **가상 머신은 미리 보기를 사용하지 않아야 함**

가상 컴퓨터는 미리 보기 이미지를 사용하지 않아야 합니다.

다음 예제에서는 이 테스트에 **실패** 합니다.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

다음 예에서는 이 테스트를 **통과** 합니다.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>ManagedIdentity 확장 사용 안 함

테스트 이름: **ManagedIdentityExtension를 사용하지 않아야 함**

가상 머신에 ManagedIdentity 확장을 적용하지 마세요. 확장은 2019에서 사용되지 않으므로 더 이상 사용할 수 없습니다.

## <a name="outputs-cant-include-secrets"></a>출력에는 비밀을 포함할 수 없음

테스트 이름: **출력은 비밀을 포함할 수 없음**

잠재적으로 비밀을 노출하는 출력 섹션에 값을 포함하지 마세요. 템플릿의 출력은 배포 기록에 저장되므로 악의적인 사용자가 해당 정보를 찾을 수 있습니다.

다음 예제에서는 출력 값에 보안 매개 변수를 포함하기 때문에 테스트에 **실패** 합니다.

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

다음 예제에서는 출력에서 [list *](template-functions-resource.md#list) 함수를 사용하기 때문에 **실패** 합니다.

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

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>commandToExecute 비밀에 protectedSettings 사용

테스트 이름: **commandToExecute는 비밀에 protectedSettings를 사용해야 함**

사용자 지정 스크립트 확장에서 `commandToExecute`에 암호와 같은 비밀 데이터가 포함되는 경우 암호화된 속성 `protectedSettings`를 사용합니다. 비밀 데이터 형식의 예는 `secureString`, `secureObject`, `list()` 함수 또는 스크립트입니다.

가상 머신의 사용자 지정 스크립트 확장에 대한 자세한 내용은 [Windows](
/azure/virtual-machines/extensions/custom-script-windows), [Linux](../../virtual-machines/extensions/custom-script-linux.md) 및 스키마 [Microsoft.Compute virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions)를 참조하세요.

이 예제에서는 암호화된 속성 `protectedSettings`에 `commandToExecute`가 포함되기 때문에 `adminPassword`라는 매개 변수 및 `secureString` 종류의 매개 변수가 있는 템플릿이 테스트를 **통과** 합니다.

```json
"properties": [
  {
    "protectedSettings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

암호화되지 않은 속성 `settings`에 `commandToExecute`를 포함하는 경우 테스트에 **실패** 합니다.

```json
"properties": [
  {
    "settings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

## <a name="next-steps"></a>다음 단계

* 테스트 도구 키트를 실행하는 방법에 대한 자세한 내용은 [ARM 템플릿 테스트 도구 키트 사용](test-toolkit.md)을 참조하세요.
* 테스트 도구 키트를 사용하는 Microsoft Learn 모듈은 [변경 내용 미리 보기 및 ARM 템플릿 테스트 도구 키트와 가상 분석을 사용한 Azure 리소스 유효성 검사](/learn/modules/arm-template-test/)를 참조하세요.