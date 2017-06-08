---
title: "Azure 리소스의 여러 인스턴스 배포 | Microsoft Docs"
description: "Azure 리소스 관리자 템플릿에서 복사 작업 및 배열을 사용하여 여러 번 반복하는 방법을 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: a8e35456af8c9f2cf1bf9e9c364e33641f29a477
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017


---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿에서 리소스 또는 속성의 여러 인스턴스 배포
이 항목에서는 Azure 리소스 관리자 템플릿을 반복하여 리소스의 여러 인스턴스를 만드는 방법을 보여 줍니다.

## <a name="resource-iteration"></a>리소스 반복
리소스 종류의 여러 인스턴스를 만들려면 리소스 종류에 `copy` 요소를 추가합니다. copy 요소에서 이 루프의 반복 횟수와 이름을 지정합니다. count 값은 양의 정수여야 하며 800을 초과할 수 없습니다. Resource Manager는 병렬로 리소스를 만듭니다. 따라서 생성되는 순서는 정해져 있지 않습니다. 시퀀스에서 반복된 리소스를 만들려면 [직렬 복사](#serial-copy)를 참조하세요. 

다음 형식으로 리소스를 여러 번 만듭니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

각 리소스의 이름에는 `copyIndex()` 함수가 포함되어 있으며 이 함수는 루프에서 현재 반복을 반환합니다. `copyIndex()`는 0부터 시작합니다. 따라서 예제는 다음과 같습니다.

```json
"name": "[concat('storage', copyIndex())]",
```

다음과 같은 이름을 만듭니다.

* storage0
* storage1
* storage2

인덱스 값을 오프셋하려면 copyIndex() 함수에 값을 전달하면 됩니다. 수행할 반복 수는 복사 요소에서 지정되지만 copyIndex의 값이 지정된 값 만큼 오프셋됩니다. 따라서 예제는 다음과 같습니다.

```json
"name": "[concat('storage', copyIndex(1))]",
```

다음과 같은 이름을 만듭니다.

* storage1
* storage2
* storage3

복사 작업은 배열의 각 요소를 반복할 수 있으므로 배열을 사용할 때 유용합니다. 배열의 `length` 함수를 사용하여 반복 횟수를 지정하고, `copyIndex`를 사용하여 배열의 현재 인덱스를 검색합니다. 따라서 예제는 다음과 같습니다.

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

다음과 같은 이름을 만듭니다.

* storagecontoso
* storagefabrikam
* storagecoho

## <a name="serial-copy"></a>직렬 복사

복사 요소를 사용하여 리소스 형식의 여러 인스턴스를 만드는 경우 Resource Manager는 기본적으로 동시에 해당 인스턴스를 배포합니다. 그러나 그 결과로 리소스가 배포되도록 지정하려고 합니다. 예를 들어 프로덕션 환경을 업데이트할 때 특정 수를 한 번에 업데이트하도록 업데이트를 늦추려고 할 수 있습니다.

Resource Manager는 순차적으로 여러 인스턴스를 배포할 수 있는 복사 요소의 속성을 제공합니다. 복사 요소에서 `mode`를 **직렬**로 설정하고 `batchSize`를 한 번에 배포할 인스턴스 수로 설정합니다. Resource Manager는 직렬 모드에서 루프에 이전 인스턴스의 종속성을 만듭니다. 따라서 이전 일괄 처리가 완료될 때까지 하나의 일괄 처리를 시작하지 않습니다.

```json
"copy": {
    "name": "iterator",
    "count": "[parameters('numberToDeploy')]",
    "mode": "serial",
    "batchSize": 2
},
```

모드 속성은 기본 값인 **병렬**을 수용합니다.

실제 리소스를 만들지 않고 직렬 복사본을 테스트하려면 다음 템플릿을 사용하여 비어 있는 중첩된 템플릿을 배포합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex())]",
      "copy": {
        "name": "iterator",
        "count": "[parameters('numberToDeploy')]",
        "mode": "serial",
        "batchSize": 1
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

배포 기록에서 중첩된 배포가 순서대로 처리되는지를 확인합니다.

![직렬 배포](./media/resource-group-create-multiple/serial-copy.png)

보다 현실적인 시나리오의 경우 다음 예제에서는 중첩된 템플릿을 사용하는 Linux VM 하나당 두 개의 인스턴스를 배포합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "User name for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "dnsLabelPrefix": {
            "type": "string",
            "metadata": {
                "description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "ubuntuOSVersion": {
            "type": "string",
            "defaultValue": "16.04.0-LTS",
            "allowedValues": [
                "12.04.5-LTS",
                "14.04.5-LTS",
                "15.10",
                "16.04.0-LTS"
            ],
            "metadata": {
                "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version."
            }
        }
    },
    "variables": {
        "templatelink": "https://raw.githubusercontent.com/rjmax/Build2017/master/Act1.TemplateEnhancements/Chapter03.LinuxVM.json"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "[concat('nestedDeployment',copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "copy": {
                "name": "myCopySet",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            },
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "adminUsername": {
                        "value": "[parameters('adminUsername')]"
                    },
                    "adminPassword": {
                        "value": "[parameters('adminPassword')]"
                    },
                    "dnsLabelPrefix": {
                        "value": "[parameters('dnsLabelPrefix')]"
                    },
                    "ubuntuOSVersion": {
                        "value": "[parameters('ubuntuOSVersion')]"
                    },
                    "index":{
                        "value": "[copyIndex()]"
                    }
                }
            }
        }
    ]
}
```

## <a name="depend-on-resources-in-a-loop"></a>루프의 리소스에 따라 달라짐
`dependsOn` 요소를 사용하여 어떤 리소스를 다른 리소스 다음에 배포하도록 지정합니다. 루프의 리소스 컬렉션에 따라 달라지는 리소스를 배포하려면 dependsOn 요소에 복사 루프의 이름을 제공합니다. 다음 예제에서는 가상 컴퓨터를 배포하기 전에 저장소 계정 3개를 배포하는 방법을 보여줍니다. 전체 가상 컴퓨터 정의는 표시되지 않습니다. 참고로 copy 요소의 name은 `storagecopy`로 설정되고 가상 컴퓨터에 대한 dependsOn 요소도 `storagecopy`로 설정되었습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>자식 리소스의 여러 인스턴스 만들기
자식 리소스에 대해 복사 반복을 사용할 수 없습니다. 일반적으로 다른 리소스 내에 중첩된 것으로 정의된 여러 인스턴스를 만들려면 대신 해당 리소스를 최상위 리소스로 만들어야 합니다. 형식 및 이름 속성을 통해 부모 리소스와의 관계를 정의합니다.

예를 들어, 데이터 집합을 데이터 팩터리 내에 자식 리소스로 정의한다고 가정합니다.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

데이터 집합의 여러 인스턴스를 만들려면 데이터 팩터리의 외부에서 이동합니다. 데이터 집합은 데이터 팩터리와 같은 수준에 있어야 하지만 여전히 데이터 팩터리의 자식 리소스입니다. 형식 및 이름 속성을 통해 데이터 집합과 데이터 팩터리 간의 관계를 유지합니다. 템플릿의 해당 위치에서 형식을 더 이상 유추할 수 없으므로 `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` 형식으로 정규화된 형식을 제공해야 합니다.

데이터 팩터리 인스턴스로 부모/자식 관계를 설정하려면 부모 리소스 이름을 포함하는 데이터 집합에 대해 이름을 제공합니다. 사용할 형식: `{parent-resource-name}/{child-resource-name}`.  

다음 예제에서는 구현을 보여줍니다.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="next-steps"></a>다음 단계
* 템플릿 섹션에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 템플릿 배포 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.


