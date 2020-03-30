---
title: 템플릿의 매개 변수
description: Azure 리소스 관리자 템플릿에서 매개 변수를 정의하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122426"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿의 매개 변수

이 문서에서는 Azure 리소스 관리자 템플릿에서 매개 변수를 정의하고 사용하는 방법을 설명합니다. 매개 변수에 대해 서로 다른 값을 제공하면 다른 환경에 대해 템플릿을 다시 사용할 수 있습니다.

리소스 관리자는 배포 작업을 시작하기 전에 매개 변수 값을 확인합니다. 템플릿에서 매개 변수가 사용되는 모든 위치에서 리소스 관리자는 매개 변수를 해결된 값으로 바꿉습니다.

## <a name="define-parameter"></a>매개 변수 정의

다음 예제에서는 간단한 매개 변수 정의를 보여 줍니다. **storageSKU라는**매개 변수를 정의합니다. 매개 변수는 문자열 값이며 의도된 용도에 유효한 값만 허용합니다. 매개 변수는 배포 중에 값이 제공되지 않는 경우 기본값을 사용합니다.

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

템플릿에서 매개 변수 함수를 사용하여 매개 [변수의 값을 참조합니다.](template-functions-deployment.md#parameters) 다음 예제에서는 매개 변수 값을 사용하여 저장소 계정에 대한 SKU를 설정합니다.

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

매개 변수에 기본값을 지정하는 경우 대부분의 템플릿 함수를 사용할 수 있습니다. 다른 매개 변수 값을 사용하여 기본값을 빌드할 수 있습니다. 다음 템플릿은 기본값에 함수의 사용을 보여 줍니다. 사이트에 대한 이름이 제공되지 않는 경우 고유한 문자열 값을 만들고 **사이트에**부가합니다. 호스트 계획에 대한 이름이 제공되지 않은 경우 사이트의 값을 적용하고 **-plan을**가산합니다.

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

매개 변수 섹션에서 [참조](template-functions-resource.md#reference) 함수 또는 [목록](template-functions-resource.md#list) 함수를 사용할 수 없습니다. 이러한 함수는 리소스의 런타임 상태를 가지며 매개 변수가 해결될 때 배포 전에 실행할 수 없습니다.

## <a name="objects-as-parameters"></a>개체를 매개 변수로 사용

관련 값을 개체로 전달하면 더 쉽게 구성할 수 있습니다. 이렇게 하면 템플릿의 매개 변수 수도 줄어듭니다.

다음 예제에서는 개체인 매개 변수를 보여 주며 있습니다. 기본값은 개체에 대해 예상되는 속성을 표시합니다.

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

점 연산자를 사용하여 개체의 속성을 참조합니다.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
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

다음 예제에서는 매개 변수를 사용하는 시나리오를 보여 줍니다.

|템플릿  |설명  |
|---------|---------|
|[parameters with functions for default values](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json)(기본값에 대한 함수가 있는 매개 변수) | 매개 변수의 기본값을 정의할 때 템플릿 함수를 사용하는 방법을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |
|[매개 변수 개체](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | 매개 변수에 대한 개체 사용을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |


## <a name="next-steps"></a>다음 단계

* 매개 변수에 사용할 수 있는 속성에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해를](template-syntax.md)참조하십시오.
* 매개 변수 값을 파일로 전달하는 방법에 대해 알아보려면 [리소스 관리자 매개 변수 파일 만들기](parameter-files.md)를 참조하십시오.
* 매개 변수 를 만드는 것에 대한 권장 사항은 [모범 사례 - 매개 변수를](template-best-practices.md#parameters)참조하십시오.
