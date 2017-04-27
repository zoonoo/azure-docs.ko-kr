---
title: "Azure 템플릿의 순차적 루핑 | Microsoft Docs"
description: "Azure Resource Manager 템플릿 기능을 확장하여 리소스 종류의 여러 인스턴스를 배포할 때 순차적 루핑을 구현하는 방법에 대해 설명합니다."
services: guidance, azure-resource-manager
documentationcenter: na
author: petertaylor9999
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 04/18/2017
ms.author: petertay
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 44d59488600e05d6ae80a169b5d682e8fa94ccc5
ms.lasthandoff: 04/18/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---sequential-looping"></a>Azure Resource Manager 템플릿 기능을 확장하는 패턴 - 순차적 루핑

Azure Resource Manager 템플릿은 복사 루프를 통해 유사한 리소스 그룹의 배포를 지원합니다. 리소스 개체의 복사 루프는 리소스의 고유한 이름 생성에 사용되는 문자열 배열을 반복하는 데 사용할 수 있습니다. 리소스를 설명하는 변수 배열에 리소스 개체의 복사 루프를 반복하여 사용할 수도 있습니다.

이러한 패턴은 모두 잘 작동하지만 각 그룹 구성원 간의 종속성이 없는 경우에서만 잘 작동합니다. 반복 루프 동안 Resource Manager에서는 리소스를 병렬로 배포하려고 합니다. 첫 번째 리소스가 두 번째 리소스에 종속되는 경우 Resource Manager가 두 번째 리소스를 첫 번째 리소스보다 먼저 배포하면 실패할 수 있습니다.

실제 문제는 Resource Manager가 현재 반복 루프 내에서 `dependsOn`을 지원하지 않는다는 것입니다. 하지만 기존 Resource Manager 기능을 사용하고 일부 창의적인 리소스 이름을 지정하여 이 기능을 구현할 수 있습니다. 

## <a name="sequential-looping-pattern"></a>순차적 루핑 패턴

패턴은 다음과 같습니다. 첫 번째 리소스의 이름은 루프의 첫 번째 인덱스가 무엇이든 이름 접두사와 `0`의 연결을 사용하여 지정됩니다. 두 번째 리소스에는 복사 루프가 있으며 복사 루프에서 다음 리소스 이름은 이름 접두사와 현재 `copyIndex()`에 1을 더한 `copyIndex(1)` 함수의 결과를 연결한 것입니다. 또한 두 번째 리소스에는 이름 접두사와 `copyIndex()` 함수 결과의 연결을 참조하는 `dependsOn` 요소가 있습니다. 이 방법을 통해 다음 리소스에서 이전 리소스로 다시 `dependsOn` 관계를 만듭니다. Resource Manager는 이전 리소스가 배포될 때까지 다음 리소스 배포를 기다립니다.

다음 템플릿은 이 패턴을 보여 줍니다. Microsoft.Resources/배포 리소스 종류는 실제 아무것도 배포하지 않는 중첩된 템플릿입니다.

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
  "variables": {
    "count": "[sub(parameters('numberToDeploy'), 1)]"
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "loop-0",
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "loop-0 "
            }
          }
        }
      }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex(1))]",
      "dependsOn": [
        "[concat('loop-', copyIndex())]"
      ],
      "copy": {
        "name": "iterator",
        "count": "[variables('count')]"
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
            "collection": {
              "type": "string",
              "value": "[concat(reference(concat('loop-',copyIndex())).outputs.collection.value,'loop-',copyIndex(1), ' ')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference(concat('loop-',variables('count'))).outputs.collection.value]"
    }
  }
}

```
이 템플릿에서 첫 번째 리소스 개체 이름은 `loop-0`입니다. 그러면 두 번째 리소스 개체에서 다음 리소스 이름은 단어 `loop-`와 `copyIndex(1)` 함수의 결과를 연결한 (`loop-1`)입니다. `dependsOn` 요소는 단어 `loop-`와 `copyIndex()` 함수의 결과를 연결한 `loop-0`이므로 이전 리소스를 참조합니다. 두 번째 리소스 개체의 패턴은 `count`에 도달할 때까지 반복하고 `loop-4`의 리소스 이름이 `dependsOn` `loop-3`이면 끝납니다. `count`는 `numberToDeploy` 매개 변수에서 `1`을 빼는 변수이며 0부터 시작하여 올바른 계산을 유지합니다.

## <a name="try-the-template"></a>템플릿 시도

이 템플릿으로 실험하려면 다음 단계를 따릅니다.

1.    Azure Portal로 이동하여 "+" 아이콘을 선택하고 "템플릿 배포" 리소스 종류를 검색합니다. 해당 리소스 종류를 검색 결과에서 찾으면 선택합니다.
2.    "템플릿 배포" 페이지에서 "만들기" 단추를 선택합니다. "사용자 지정 배포" 블레이드로 이동하여 템플릿에 리소스가 없음을 확인합니다.
3.    "편집" 아이콘을 선택합니다.
4.    오른쪽 창에서 빈 템플릿을 삭제합니다.
5.    이전 샘플 템플릿을 복사하여 오른쪽 창에 붙여넣습니다.
6.    "저장" 단추를 선택합니다.
7.    "사용자 지정 배포" 창으로 돌아왔지만 이번에는 일부 드롭다운 상자가 있습니다. 구독을 선택하고, 새로 만들거나 기존 리소스 그룹을 사용하고, 위치를 선택합니다. 계약조건을 검토하고 "동의함" 단추를 클릭합니다.
8.    "구입" 단추를 선택합니다.

리소스가 순차적으로 배포되고 있는지를 확인하려면 "리소스 그룹"을 선택한 다음 이전에 선택한 리소스 그룹을 선택합니다. 리소스 그룹 블레이드에서 "배포" 단추를 선택하면 해당하는 타임스탬프와 함께 순차적으로 배포된 리소스가 표시됩니다.

![Azure Resource Manager에서 순차 루프 템플릿 배포](./media/resource-manager-sequential-loop/deployments.png)

## <a name="next-steps"></a>다음 단계

중첩된 템플릿에 리소스를 추가하여 이 패턴을 템플릿에서 사용합니다. 해당 리소스를 `Microsoft.Resources/deployments` 리소스의 템플릿 요소에서 직접 작성하거나 `templateLink` 요소를 사용하여 연결할 수 있습니다. 예제의 리소스 종류는 중첩된 템플릿이지만 모든 리소스 종류를 배포할 수 있습니다. 유일한 예외는 반복 루프 내에서 자식 리소스를 참조할 수 없는 경우입니다.

* 리소스의 여러 인스턴스를 만드는 방법은 [Azure Resource Manager 템플릿에서 리소스의 여러 인스턴스 배포](resource-group-create-multiple.md)를 참조하세요.
* 이 패턴은 [템플릿 구성 요소 프로젝트](https://github.com/mspnp/template-building-blocks) 및 [Azure 참조 아키텍처](/azure/architecture/reference-architectures/)에서도 구현됩니다.
