---
title: 템플릿의 사용자 정의 함수
description: Azure Resource Manager 템플릿에서 사용자 정의 함수를 정의 하 고 사용 하는 방법에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943211"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Azure Resource Manager 템플릿의 사용자 정의 함수

템플릿 내에서 함수를 직접 만들 수 있습니다. 이러한 함수는 템플릿에서 사용할 수 있습니다. 사용자 정의 함수는 템플릿 내에서 자동으로 사용할 수 있는 [표준 템플릿 함수와](template-functions.md) 는 별개입니다. 템플릿에서 반복적으로 사용 되는 복잡 한 식이 있는 경우 고유한 함수를 만듭니다.

이 문서에서는 Azure Resource Manager 템플릿에 사용자 정의 함수를 추가 하는 방법을 설명 합니다.

## <a name="define-the-function"></a>함수 정의

함수는 템플릿 함수와 이름 충돌을 피하기 위해 네임스페이스 값이 필요합니다. 다음 예에서는 고유 이름을 반환 하는 함수를 보여 줍니다.

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>함수 사용

다음 예에서는 사용자 정의 함수를 포함 하는 템플릿을 보여 줍니다. 이 함수는 해당 함수를 사용 하 여 저장소 계정에 대 한 고유한 이름을 가져옵니다. 템플릿에는 함수에 매개 변수로 전달 되는 **storageNamePrefix** 라는 매개 변수가 있습니다.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>제한 사항

사용자 함수를 정의할 때는 다음과 같은 몇 가지 제한 사항이 있습니다.

* 함수는 변수에 액세스할 수 없습니다.
* 함수는 함수에 정의된 매개 변수만 사용할 수 있습니다. 사용자 정의 함수 내에서 [parameters](template-functions-deployment.md#parameters) 함수를 사용 하면 해당 함수에 대 한 매개 변수로 제한 됩니다.
* 함수는 다른 사용자 정의 함수를 호출할 수 없습니다.
* 함수는 [reference](template-functions-resource.md#reference) 함수 또는 [list](template-functions-resource.md#list) 함수를 사용할 수 없습니다.
* 함수의 매개 변수는 기본값을 가질 수 없습니다.


## <a name="next-steps"></a>다음 단계

* 사용자 정의 함수에 사용할 수 있는 속성에 대 한 자세한 내용은 [Azure Resource Manager 템플릿 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
* 사용 가능한 템플릿 함수 목록은 [Azure Resource Manager 템플릿 함수](template-functions.md)를 참조 하세요.
