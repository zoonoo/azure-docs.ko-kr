---
title: Azure Resource Manager 템플릿의 매개 변수
description: Azure Resource Manager 템플릿에서 매개 변수를 정의 하는 방법을 설명 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4947b00d6fad5007751cd97d43ad6aca8d775330
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383282"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 매개 변수

이 문서에서는 Azure Resource Manager 템플릿에서 매개 변수를 정의 하 고 사용 하는 방법을 설명 합니다. 매개 변수에 대해 서로 다른 값을 제공 하 여 다른 환경에 대 한 템플릿을 다시 사용할 수 있습니다.

리소스 관리자는 배포 작업을 시작 하기 전에 매개 변수 값을 확인 합니다. 템플릿에서 매개 변수를 사용 하는 경우에는 리소스 관리자이 매개 변수를 확인 된 값으로 바꿉니다.

## <a name="define-parameter"></a>매개 변수 정의

다음 예제에서는 간단한 매개 변수 정의를 보여 줍니다. **StorageSKU**라는 매개 변수를 정의 합니다. 매개 변수는 문자열 값 이며 원래 용도에 적합 한 값만 허용 합니다. 배포 중에 값이 제공 되지 않는 경우 매개 변수는 기본값을 사용 합니다.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>매개 변수 사용

템플릿에서 [parameters](resource-group-template-functions-deployment.md#parameters) 함수를 사용 하 여 매개 변수의 값을 참조 합니다. 다음 예에서는 매개 변수 값을 사용 하 여 저장소 계정에 대 한 SKU를 설정 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>템플릿 함수

매개 변수에 기본값을 지정하는 경우 대부분의 템플릿 함수를 사용할 수 있습니다. 다른 매개 변수 값을 사용하여 기본값을 빌드할 수 있습니다. 다음 템플릿에서는 기본값에서 함수를 사용 하는 방법을 보여 줍니다. 사이트에 대해 이름을 제공 하지 않으면 고유한 문자열 값을 만들어 **사이트**에 추가 합니다. 호스트 계획에 대 한 이름을 제공 하지 않으면 사이트의 값과 추가 **계획**을 사용 합니다.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

[Reference](resource-group-template-functions-resource.md#reference) 함수 또는 [list](resource-group-template-functions-resource.md#list) 함수는 매개 변수 섹션에서 사용할 수 없습니다. 이러한 함수는 리소스의 런타임 상태를 가져오고 매개 변수를 확인할 때 배포 전에 실행할 수 없습니다.

## <a name="objects-as-parameters"></a>개체를 매개 변수로 사용

관련 값을 개체로 전달하면 더 쉽게 구성할 수 있습니다. 이렇게 하면 템플릿의 매개 변수 수도 줄어듭니다.

다음 예제에서는 개체인 매개 변수를 보여 줍니다. 기본값은 개체의 예상 속성을 표시 합니다.

```json
"parameters": {
  "VNetSettings": {
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
```

점 연산자를 사용 하 여 개체의 속성을 참조 합니다.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 매개 변수를 사용 하는 시나리오를 보여 줍니다.

|템플릿  |Description  |
|---------|---------|
|[parameters with functions for default values](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json)(기본값에 대한 함수가 있는 매개 변수) | 매개 변수의 기본값을 정의할 때 템플릿 함수를 사용하는 방법을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |
|[parameter object](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json)(매개 변수 개체) | 매개 변수에 대한 개체 사용을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |


## <a name="next-steps"></a>다음 단계

* 매개 변수에 사용할 수 있는 속성에 대 한 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조 하세요.
* 매개 변수 값을 파일로 전달 하는 방법에 대 한 자세한 내용은 [리소스 관리자 매개 변수 파일 만들기](resource-manager-parameter-files.md)를 참조 하세요.
* 매개 변수 만들기에 대 한 권장 사항은 [모범 사례-매개 변수](template-best-practices.md#parameters)를 참조 하세요.
