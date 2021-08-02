---
title: 템플릿의 사용자 정의 함수
description: ARM 템플릿(Azure Resource Manager 템플릿)에서 사용자 정의 함수를 정의하고 사용하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 802ac6e1c70db8caa58aecde3efe814430c79682
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353456"
---
# <a name="user-defined-functions-in-arm-template"></a>ARM 템플릿의 사용자 정의 함수

템플릿 내에서 함수를 직접 만들 수 있습니다. 이러한 함수는 템플릿에서 사용할 수 있습니다. 사용자 정의 함수는 템플릿 내에서 자동으로 사용할 수 있는 [표준 템플릿 함수](template-functions.md)와는 별개입니다. 템플릿에서 반복적으로 사용되는 복잡한 식이 있는 경우 고유한 함수를 만들 수 있습니다.

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)에서 사용자 정의 함수를 추가하는 방법을 설명합니다.

## <a name="define-the-function"></a>함수의 정의

함수는 템플릿 함수와 이름 충돌을 피하기 위해 네임스페이스 값이 필요합니다. 다음 예제에서는 고유한 이름을 반환하는 함수를 보여 줍니다.

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

다음 예제에서는 사용자 정의 함수를 포함하여 스토리지 계정에 고유한 이름을 붙이는 템플릿을 보여 줍니다. 이 템플릿에는 함수에 매개 변수로 전달되는 `storageNamePrefix`라는 매개 변수가 있습니다.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

배포하는 동안 `storageNamePrefix` 매개 변수는 함수에 전달됩니다.

* 이 템플릿은 `storageNamePrefix`라는 매개 변수를 정의합니다.
* 함수에 정의된 매개 변수만 사용할 수 있기에 함수는 `namePrefix`를 사용합니다. 자세한 내용은 [제한 사항](#limitations)을 참조하세요.
* 템플릿의 `resources` 섹션에서 `name` 요소는 함수를 사용하고 `storageNamePrefix` 값을 함수의 `namePrefix`에 전달합니다.

## <a name="limitations"></a>제한 사항

사용자 함수를 정의할 때는 다음과 같은 몇 가지 제한 사항이 있습니다.

* 함수는 변수에 액세스할 수 없습니다.
* 함수는 함수에 정의된 매개 변수만 사용할 수 있습니다. 사용자 정의 함수 내에서 [parameters](template-functions-deployment.md#parameters) 함수를 사용하면 해당 함수에 대한 매개 변수로 제한됩니다.
* 함수는 다른 사용자 정의 함수를 호출할 수 없습니다.
* 함수는 [reference](template-functions-resource.md#reference) 함수 또는 [list](template-functions-resource.md#list) 함수를 사용할 수 없습니다.
* 함수는 [dateTimeAdd](template-functions-date.md#datetimeadd)를 사용할 수 없습니다.
* 함수의 매개 변수는 기본값을 가질 수 없습니다.

## <a name="next-steps"></a>다음 단계

* 사용자 정의 함수에 사용할 수 있는 속성에 대한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
* 템플릿에서 사용할 수 있는 함수 목록은 [ARM 템플릿 함수](template-functions.md)를 참조하세요.
