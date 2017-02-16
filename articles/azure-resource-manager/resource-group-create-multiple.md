---
title: "리소스의 여러 인스턴스를 배포 | Microsoft Docs"
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
ms.date: 11/02/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 28bda592a685de4b2b938da21c3f3aa0a60e632d
ms.openlocfilehash: 241a22429e8e0fbd2625292890baf4b2c3d77d81


---
# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기
이 항목에서는 Azure 리소스 관리자 템플릿을 반복하여 리소스의 여러 인스턴스를 만드는 방법을 보여 줍니다.

## <a name="copy-copyindex-and-length"></a>copy, copyIndex 및 length
여러 번 만들려는 리소스 내에서 반복 횟수를 지정하는 **copy** 개체를 정의할 수 있습니다. copy는 다음 형식을 갖습니다.

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

**copyIndex()** 함수로 현재 반복 값에 액세스할 수 있습니다. 다음 예제에서는 concat 함수에 copyIndex를 사용하여 이름을 생성합니다.

    [concat('examplecopy-', copyIndex())]

값의 배열에서 여러 리소스를 만들 때 **lenth** 함수를 사용하여 개수를 지정할 수 있습니다. length 함수에 대한 매개 변수로 배열을 제공합니다.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

최상위 리소스에만 복사본 개체를 적용할 수 있습니다. 리소스 유형의 속성이나 자식 리소스에는 적용할 수 없습니다. 그러나 이 항목에서는 속성에 대해 여러 항목을 지정하고 자식 리소스의 여러 인스턴스를 만드는 방법을 보여 줍니다. 다음 의사 코드 예제는 복사본을 적용할 수 있는 위치를 보여 줍니다.

    "resources": [
      {
        "type": "{provider-namespace-and-type}",
        "name": "parentResource",
        "copy": {  
          /* yes, copy can be applied here */
        },
        "properties": {
          "exampleProperty": {
            /* no, copy cannot be applied here */
          }
        },
        "resources": [
          {
            "type": "{provider-type}",
            "name": "childResource",
            /* copy can be applied if resource is promoted to top level */ 
          }
        ]
      }
    ] 

속성에 **복사본**을 적용할 수 없더라도 속성은 여전히 이 속성이 포함된 리소스의 반복 중 일부입니다. 따라서 값을 지정하는 속성 내에 **copyIndex()**를 사용할 수 있습니다.

리소스의 속성에서 반복을 수행하려는 여러 시나리오가 있습니다. 예를 들어, 가상 컴퓨터에 대해 여러 데이터 디스크를 지정하려고 할 수 있습니다. 속성에서 반복하는 방법을 보려면 [복사되지 않은 경우 여러 인스턴스 만들기](#create-multiple-instances-when-copy-wont-work)를 참조하세요. 

자식 리소스를 사용하려면 [자식 리소스의 여러 인스턴스 만들기](#create-multiple-instances-of-a-child-resource)를 참조하세요.

## <a name="use-index-value-in-name"></a>이름에 인덱스 값 사용
복사 작업을 사용하여 증분 인덱스를 기준으로 고유하게 명명되는 리소스의 여러 인스턴스를 만들 수 있습니다. 예를 들어 배포되는 각 리소스 이름의 끝에 고유 번호를 추가할 수 있습니다. 다음 이름의 웹 사이트 3개를 배포하려면

* examplecopy-0
* examplecopy-1
* examplecopy-2.

다음 템플릿을 사용합니다.

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>인덱스 값 오프셋
앞의 예제에서 인덱스 값은 0에서 2로 이동합니다. 인덱스 값을 오프셋하여 값을 **copyIndex(1)**와 같은 **copyIndex()** 함수를 전달할 수 있습니다. 수행할 반복 수는 복사 요소에서 지정되지만 copyIndex의 값이 지정된 값 만큼 오프셋됩니다. 따라서 이전 예제와 같은 서식 파일을 사용하지만 **copyIndex(1)** 를 지정하여 다음과 같이 명명된 3개의 웹 사이트를 배포합니다.

* examplecopy-1
* examplecopy-2
* examplecopy-3

## <a name="use-copy-with-array"></a>배열을 사용하여 복사
복사 작업은 배열의 각 요소를 반복할 수 있으므로 배열을 사용할 때 유용합니다. 다음 이름의 웹 사이트 3개를 배포하려면

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

다음 템플릿을 사용합니다.

    "parameters": { 
      "org": { 
         "type": "array", 
         "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

물론, 복사본 개수를 배열 길이가 아닌 다른 값으로 설정할 수 있습니다. 예를 들어 많은 값이 포함된 배열을 만든 다음 배포할 배열 요소 수를 지정하는 매개 변수 값을 전달할 수 있습니다. 이 경우 첫 번째 예제와 같이 복사본 개수를 설정합니다. 

## <a name="depend-on-resources-in-a-loop"></a>루프의 리소스에 따라 달라짐
**dependsOn** 요소를 사용하여 어떤 리소스를 다른 리소스 다음에 배포하도록 지정할 수 있습니다. 루프의 리소스 컬렉션에 따라 달라지는 리소스를 배포하려면 **dependsOn** 요소에 copy 루프의 이름을 제공합니다. 다음 예제에서는 가상 컴퓨터를 배포하기 전에 저장소 계정 3개를 배포하는 방법을 보여줍니다. 전체 가상 컴퓨터 정의는 표시되지 않습니다. 참고로 copy 요소의 **name**은 **storagecopy**로 설정되고 가상 컴퓨터에 대한 **dependsOn** 요소도 **storagecopy**로 설정되었습니다.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
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

## <a name="create-multiple-instances-of-a-child-resource"></a>자식 리소스의 여러 인스턴스 만들기
자식 리소스에 대해 복사 반복을 사용할 수 없습니다. 일반적으로 다른 리소스 내에 중첩된 것으로 정의된 여러 인스턴스를 만들려면 대신 해당 리소스를 최상위 리소스로 만들어야 합니다. **형식** 및 **이름** 속성을 통해 부모 리소스와의 관계를 정의합니다.

예를 들어, 데이터 집합을 데이터 팩터리 내에 자식 리소스로 정의한다고 가정합니다.

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

데이터 집합의 여러 인스턴스를 만들려면 데이터 팩터리의 외부에서 이동합니다. 데이터 집합은 데이터 팩터리와 같은 수준에 있어야 하지만 여전히 데이터 팩터리의 자식 리소스입니다. **형식** 및 **이름** 속성을 통해 데이터 집합과 데이터 팩터리 간의 관계를 유지합니다. 템플릿의 해당 위치에서 형식을 더 이상 유추할 수 없으므로 다음과 같은 형식으로 정규화된 형식을 제공해야 합니다.

 **{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}** 

데이터 팩터리 인스턴스로 부모/자식 관계를 설정하려면 부모 리소스 이름을 포함하는 데이터 집합에 대해 이름을 제공합니다. 이때 다음 형식을 사용합니다.

**{parent-resource-name}/{child-resource-name}**.  

다음 예제에서는 구현을 보여줍니다.

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

## <a name="create-multiple-instances-when-copy-wont-work"></a>복사되지 않은 경우 여러 인스턴스 만들기
리소스 형식 내의 속성이 아니라 리소스 형식에서 **복사** 를 사용할 수 있습니다. 리소스의 일부인 항목에 여러 인스턴스를 만드는 경우 이 요구 사항은 문제를 일으킬 수 있습니다. 일반적인 시나리오는 가상 컴퓨터에 대해 여러 데이터 디스크를 만드는 것입니다. **dataDisks**는 자체 리소스 형식이 아니라 가상 컴퓨터에 대한 속성이므로 데이터 디스크에 대해 **복사**를 사용할 수 없습니다. 대신 필요한 만큼 많은 데이터 디스크를 포함한 배열을 만들고 만들 데이터 디스크의 실제 수를 전달합니다. 가상 컴퓨터 정의에서 **take** 함수를 사용하여 배열에서 실제로 원하는 요소의 수를 알아냅니다.

이 패턴의 전체 예제는 [동적 데이터 디스크를 선택하여 VM 만들기](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) 템플릿에 나와 있습니다.

배포 템플릿의 관련 섹션은 다음 예제에 나와 있습니다. 많은 수의 데이터 디스크를 동적으로 만드는 작업과 관련된 섹션을 중점적으로 제공하기 위해 많은 템플릿을 제거했습니다. **numDataDisks** 매개 변수는 만들려는 디스크 수를 제공하는 데 사용합니다. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

속성에 대해 가변적인 항목 수로 리소스의 여러 인스턴스를 만들어야 할 때 **take** 함수와 **copy** 요소를 함께 사용할 수 있습니다. 예를 들어, 여러 가상 컴퓨터를 만들어야 하지만 각 가상 컴퓨터에 서로 다른 수의 데이터 디스크가 있다고 가정합니다. 각 데이터 디스크에 연결된 가상 컴퓨터를 식별하는 이름을 지정하려면 데이터 디스크 배열을 별도의 템플릿에 넣습니다. 가상 컴퓨터 이름 및 반환할 데이터 디스크 수에 대한 매개 변수를 포함합니다. 출력 섹션에서 지정된 항목 수를 반환합니다.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

부모 템플릿에서는 가상 컴퓨터의 수에 대한 매개 변수 및 각 가상 컴퓨터에 대 한 데이터 디스크 수에 대한 배열을 포함합니다.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

리소스 섹션에서 데이터 디스크를 정의하는 템플릿의 여러 인스턴스를 배포합니다. 

```
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

리소스 섹션에서 가상 컴퓨터의 여러 인스턴스를 배포합니다. 데이터 디스크의 경우 데이터 디스크에 대한 올바른 수와 데이터 디스크에 대한 올바른 이름을 포함하는 중첩된 배포를 참조합니다.

```
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="return-values-from-a-loop"></a>루프에서 값 반환
리소스 종류의 여러 인스턴스를 만드는 방법은 편리하지만 루프에서 값을 반환하는 것은 어려울 수 있습니다. 값을 유지 및 반환하는 한 가지 방법은 중첩된 템플릿으로 **복사**를 사용하고 반환할 모든 값을 포함하는 배열을 왕복하는 것입니다. 예를 들어 여러 저장소 계정을 만들고 각각에 대한 기본 끝점을 반환하려고 한다고 가정합니다. 

먼저 저장소 계정을 만드는 중첩된 템플릿을 만듭니다. Blob URI에 대한 배열 매개 변수를 허용하는지 확인합니다. 이 매개 변수를 사용하여 이전 배포에서 모든 값을 왕복합니다. 템플릿의 출력은 이전 URI에 새 Blob URI를 연결하는 배열입니다.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "indexValue": {
      "type":"int"
    },
    "blobURIs": {
        "type": "array",
      "defaultValue": []
    }
  },
    "variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id), parameters('indexValue'))]"
  },
    "resources": [
    {
        "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {  
      }
    }
    ],
    "outputs": {
      "result": {
        "type": "array",
      "value": "[concat(parameters('blobURIs'),split(reference(variables('storageName')).primaryEndpoints.blob, ','))]"
    }
  }
}
```

이제 중첩된 템플릿의 정적 인스턴스 하나와 중첩된 템플릿의 나머지 인스턴스에 대한 루프를 포함하는 부모 템플릿을 만듭니다. 반복된 배포의 각 인스턴스에 대해 이전 배포의 출력인 배열을 전달합니다.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberofStorage": { "type": "int", "minValue": 2 }
  },
  "resources": [
    {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate0",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": 0}
        }
      }
    },
    {
      "apiVersion": "2016-09-01",
      "name": "[concat('nestedTemplate', copyIndex(1))]",
      "type": "Microsoft.Resources/deployments",
      "copy": {
        "name": "storagecopy",
        "count": "[sub(parameters('numberofStorage'), 1)]"
      },
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": "[copyIndex(1)]"},
          "blobURIs": {"value": "[reference(concat('nestedTemplate', copyIndex())).outputs.result.value]"}
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "object",
      "value": "[reference(concat('nestedTemplate', sub(parameters('numberofStorage'), 1))).outputs.result]"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계
* 템플릿 섹션에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 템플릿에서 사용할 수 있는 모든 함수는 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
* 템플릿 배포 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.




<!--HONumber=Dec16_HO3-->


