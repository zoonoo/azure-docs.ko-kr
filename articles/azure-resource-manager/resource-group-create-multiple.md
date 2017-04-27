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
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8ecf7c058b90fd18e41fd4e1cbc29e22dfeb0883
ms.lasthandoff: 04/18/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿에서 리소스의 여러 인스턴스 배포
이 항목에서는 Azure 리소스 관리자 템플릿을 반복하여 리소스의 여러 인스턴스를 만드는 방법을 보여 줍니다.

## <a name="copy-and-copyindex"></a>copy 및 copyIndex
다음 형식으로 리소스를 여러 번 만듭니다.

```json
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())", 
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
```

반복할 횟수를 copy 개체에 지정합니다.

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

count 값은 양의 정수여야 하며 800을 초과할 수 없습니다.

각 리소스의 이름에는 `copyIndex()` 함수가 포함되어 있으며 이 함수는 루프에서 현재 반복을 반환합니다.

```json
"name": "[concat('examplecopy-', copyIndex())]",
```

3개의 웹 사이트를 배포하는 경우 다음과 같은 이름이 지정됩니다.

* examplecopy-0
* examplecopy-1
* examplecopy-2.

인덱스 값을 오프셋하여 값을 `copyIndex(1)`와 같은 copyIndex() 함수를 전달할 수 있습니다. 수행할 반복 수는 복사 요소에서 지정되지만 copyIndex의 값이 지정된 값 만큼 오프셋됩니다. 따라서 이전 예제와 같은 템플릿을 사용하지만 copyIndex(1)를 지정하여 다음과 같이 명명된 3개의 웹 사이트를 배포합니다.

* examplecopy-1
* examplecopy-2
* examplecopy-3

Resource Manager는 병렬로 리소스를 만듭니다. 따라서 생성되는 순서는 정해져 있지 않습니다. 순서대로 반복된 리소스를 만들려면 [Azure Resource Manager 템플릿에 대한 순차적 루핑](resource-manager-sequential-loop.md)을 참조하세요. 

최상위 리소스에만 복사본 개체를 적용할 수 있습니다. 리소스 유형의 속성이나 자식 리소스에는 적용할 수 없습니다. 다음 의사 코드 예제는 복사본을 적용할 수 있는 위치를 보여 줍니다.

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* Yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* No, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* No, copy cannot be applied here. The resource must be promoted to top-level. */ 
      }
    ]
  }
] 
```

자식 리소스를 반복하려면 [자식 리소스의 여러 인스턴스 만들기](#create-multiple-instances-of-a-child-resource)를 참조하세요.

속성에 복사본을 적용할 수 없더라도 속성은 여전히 이 속성이 포함된 리소스의 반복 중 일부입니다. 따라서 값을 지정하는 속성 내에 copyIndex()를 사용할 수 있습니다. 속성에 대한 여러 값을 만들려면 [리소스 종류에서 속성의 여러 인스턴스 만들기](resource-manager-property-copy.md)를 참조하세요.

## <a name="use-copy-with-array"></a>배열을 사용하여 복사
복사 작업은 배열의 각 요소를 반복할 수 있으므로 배열을 사용할 때 유용합니다. 다음 이름의 웹 사이트 3개를 배포하려면

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

다음 템플릿을 사용합니다.

```json
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
```

`length` 함수는 횟수를 지정하는데 사용됩니다. length 함수에 대한 매개 변수로 배열을 제공합니다.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
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
* 순서대로 반복된 리소스를 만들려면 [Azure Resource Manager 템플릿에 대한 순차적 루핑](resource-manager-sequential-loop.md)을 참조하세요.
* 템플릿 배포 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.


