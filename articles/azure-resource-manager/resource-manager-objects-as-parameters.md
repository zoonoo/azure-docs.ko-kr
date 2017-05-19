---
title: "Azure Resource Manager 템플릿 - 개체를 매개 변수로 사용 | Microsoft Docs"
description: "Azure Resource Manager 템플릿의 기능을 확장하여 개체를 매개 변수로 사용하는 방법을 설명합니다."
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/01/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 617c24ea999aef78696ff08add4b9616e3dac589
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---objects-as-parameters"></a>Azure Resource Manager 템플릿의 기능 확장 패턴 - 개체를 매개 변수로 사용

Azure Resource Manager 템플릿은 리소스 배포를 사용자 지정하기 위한 값을 지정하는 매개 변수를 지원합니다. 이 기능은 유용하고 복잡한 배포를 만들 수 있도록 하지만 단일 템플릿은 255개 매개 변수로 제한됩니다. 리소스의 각 속성에 대해 매개 변수를 사용하며 대규모 배포를 유지하는 경우 매개 변수가 부족할 수 있습니다.

## <a name="create-object-as-parameter"></a>개체를 매개 변수로 만들기

이 문제를 해결하는 한 가지 방법은 개체를 매개 변수로 사용하는 것입니다. 이 패턴에서는 템플릿으로 개체를 매개 변수로 지정한 다음 해당 개체를 값 또는 값 배열로 제공합니다. `parameter()` 함수 및 점 연산자를 사용하여 해당 하위 속성을 참조합니다. 예:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "VNetSettings":{"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[parameters('VNetSettings').name]",
      "location":"[resourceGroup().location]",
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
  ],          
  "outputs": {}
}

```

해당 매개 변수 파일은 다음과 같습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "VNetSettings":{
          "value":{
              "name":"VNet1",
              "addressPrefixes": [
                  { 
                      "name": "firstPrefix",
                      "addressPrefix": "10.0.0.0/22"
                  }
              ],
              "subnets":[
                  {
                      "name": "firstSubnet",
                      "addressPrefix": "10.0.0.0/24"
                  },
                  {
                      "name":"secondSubnet",
                      "addressPrefix":"10.0.1.0/24"
                  }
              ]
          }
      }
  }
}
```

이 예제에서 VNet에 대해 지정된 모든 값은 단일 매개 변수 `VNetSettings`에서 제공됩니다. 이 패턴은 특정 리소스에 대한 모든 값을 단일 개체에 유지하므로 속성 값 관리에 유용합니다. 또한 이 예제에서는 개체를 매개 변수로 사용하는 동안 개체 배열도 매개 변수로 사용할 수 있습니다. 배열에 대한 인덱스를 사용하여 개체를 참조합니다.

## <a name="use-object-instead-of-multiple-arrays"></a>여러 배열 대신 개체 사용

여러 속성 값 배열을 만들고 각 배열을 반복하여 값을 선택하는 방식으로 비슷한 패턴을 사용하여 리소스의 여러 인스턴스를 만들었을 수 있습니다. 이 패턴은 같은 형식의 여러 리소스를 만들 때 적합하지만 자식 리소스를 만들 때 사용하면 문제가 있을 수 있습니다. 

이 문제에는 두 가지 이유가 있습니다. 먼저 Resource Manager는 2개의 자식 리소스는 동시에 배포하려고 하지만 2개의 자식 리소스가 부모를 동시에 업데이트하면 배포가 실패합니다. 

둘째, 각 속성 값이 동시에 반복되며, 각 배열의 모양이 같지 않으면 오류가 발생합니다. 예를 들어 다음과 같은 속성 배열을 고려해 보세요.

```json
"variables": {
    "firstProperty": [
        {
            "name": "A",
            "type": "typeA"
        },
        {
            "name": "B",
            "type": "typeB"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ],
    "secondProperty": [
        "one","two"
    ]
}
```

복사 루프 내에서 속성에 이러한 값을 할당하는 일반적인 패턴은 `variables()` 함수를 사용하여 속성에 액세스하고 `copyIndex()`를 배열에 대한 인덱스로 사용하는 것입니다. 예:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    ...
    "copy": {
        "name": "copyLoop1",
        "count": "[length(variables('firstProperty'))]"
    },
    ...
    "properties": {
        "name": { "value": "[variables('firstProperty')[copyIndex()].name]" },
        "type": { "value": "[variables('firstProperty')[copyIndex()].type]" },
        "number": { "value": "[variables('secondProperty')[copyIndex()]]" }
    }
}
```
복사 루프의 `count`는 `firstProperty` 배열의 속성 수를 기준으로 합니다. 그러나 `secondProperty` 배열에 동일한 속성 수가 없습니다. `secondProperty` 배열의 길이가 다르므로 이 템플릿에 대한 유효성 검사가 실패합니다.

그러나 단일 개체에 모든 속성을 포함하는 경우 값이 누락되었는지 확인하기가 훨씬 더 쉽습니다. 예:

```json
"variables": {
    "propertyObject": [
        {
            "name": "A",
            "type": "typeA",
            "number": "one"
        },
        {
            "name": "B",
            "type": "typeB",
            "number": "two"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ]
}
```

## <a name="try-the-template"></a>템플릿 시도

이러한 템플릿으로 실험하려면 다음 단계를 따릅니다.

1.    Azure Portal로 이동하여 "+" 아이콘을 선택하고 "템플릿 배포" 리소스 종류를 검색합니다. 해당 리소스 종류를 검색 결과에서 찾으면 선택합니다.
2.    "템플릿 배포" 페이지에서 **만들기** 단추를 선택합니다. 그러면 “사용자 지정 배포” 블레이드가 열립니다.
3.    **템플릿 편집** 단추를 선택합니다.
4.    오른쪽 창에서 빈 템플릿을 삭제합니다.
5.    샘플 템플릿을 복사하여 오른쪽 창에 붙여 넣습니다.
6.    **저장** 단추를 선택합니다.
7.    “사용자 지정 배포” 창으로 돌아가면 **매개 변수 편집** 단추를 선택합니다.
8.  “매개 변수 편집” 블레이드에서 기존 템플릿을 삭제합니다.
9.  위의 샘플 매개 변수 템플릿을 복사한 후 붙여 넣습니다.
10. **저장** 단추를 선택합니다. 그러면 “사용자 지정 배포” 블레이드로 돌아갑니다.
11. “사용자 지정 배포" 블레이드에서 구독을 선택하고, 새로 만들거나 기존 리소스 그룹을 사용하고, 위치를 선택합니다. 사용 약관을 검토하고 “동의함” 확인란을 선택합니다.
12.    **구입** 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계

배포당 허용되는 최대 255개의 매개 변수보다 더 많은 매개 변수가 필요한 경우이 패턴을 사용하여 템플릿에 더 적은 수의 매개 변수를 지정합니다. 이 패턴을 사용하여 리소스의 속성을 좀 더 쉽게 관리한 다음 순차 루프 패턴을 사용하여 충돌 없이 배포할 수 있습니다.

* `parameter()` 함수 및 배열 사용에 대한 소개 내용을 보려면 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
* 이 패턴은 [템플릿 구성 요소 프로젝트](https://github.com/mspnp/template-building-blocks) 및 [Azure 참조 아키텍처](/azure/architecture/reference-architectures/)에서도 구현됩니다.
