---
title: JSON 및 Bicep의 Azure Resource Manager 템플릿에 대 한 구문 비교
description: JSON 및 Bicep를 사용 하 여 개발 된 Azure Resource Manager 템플릿을 비교 하 고 언어 간에 변환 하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 85f85e66e69eede68bab847e4bc68514e65115eb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418048"
---
# <a name="comparing-json-and-bicep-for-templates"></a>템플릿에 대 한 JSON 및 Bicep 비교

이 문서에서는 Bicep 구문을 Azure Resource Manager 템플릿에 대 한 JSON 구문과 비교 합니다 (ARM 템플릿). 대부분의 경우 Bicep는 JSON의 경우 보다 자세한 정보를 제공 하는 구문을 제공 합니다.

## <a name="syntax-equivalents"></a>해당 하는 구문

JSON을 사용 하 여 ARM 템플릿을 개발 하는 데 익숙한 경우 다음 표를 사용 하 여 Bicep의 해당 구문에 대해 알아보세요.

| 시나리오 | Bicep | JSON |
| -------- | ------------ | ----- |
| 식 작성 | `func()` | `"[func()]"` |
| 매개 변수 값 가져오기 | `exampleParameter` | `[parameters('exampleParameter'))]` |
| 변수 값 가져오기 | `exampleVar` | `[variables('exampleVar'))]` |
| 문자열 연결 | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| 리소스 속성 설정 | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| 논리적 AND를 반환 합니다. | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| 템플릿에서 리소스의 리소스 ID를 가져옵니다. | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| 템플릿의 리소스에서 속성을 가져옵니다. | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| 조건부로 값 설정 | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| 솔루션을 여러 파일로 분리 | 모듈 사용 | 연결 된 템플릿 사용 |
| 배포의 대상 범위를 설정 합니다. | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| 종속성 설정 | 종속성의 자동 검색을 사용 하거나 수동으로 종속성을 설정 합니다. `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| 리소스 선언 | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>권장 구성

* 가능 하면 Bicep 파일에 [참조](template-functions-resource.md#reference) 및 [resourceId](template-functions-resource.md#resourceid) 함수를 사용 하지 마십시오. 동일한 Bicep 배포에서 리소스를 참조 하는 경우에는 리소스 식별자를 대신 사용 합니다. 예를 들어 리소스 식별자로를 사용 하 여 Bicep 파일에 리소스를 배포한 경우 `stg` 또는와 같은 구문을 사용 `stg.id` `stg.properties.primaryEndpoints.blob` 하 여 속성 값을 가져옵니다. 리소스 식별자를 사용 하 여 리소스 간에 암시적 종속성을 만듭니다. DependsOn 속성을 사용 하 여 종속성을 명시적으로 설정할 필요는 없습니다.
* 식별자에 대해 일관 된 대/소문자를 사용 합니다. 사용할 대/소문자 유형을 잘 모르겠으면 카멜식 대/소문자 구분을 시도 합니다. 예들 들어 `param myCamelCasedParameter string`입니다.
* 설명에서 사용자에 게 중요 한 정보를 제공 하는 경우에만 매개 변수에 대 한 설명을 추가 합니다. `//`일부 정보에는 주석을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Bicep에 대 한 자세한 내용은 [Bicep 자습서](./bicep-tutorial-create-first-bicep.md)를 참조 하십시오.
* 언어 간 템플릿 변환에 대 한 자세한 내용은 [JSON과 Bicep 간에 ARM 템플릿 변환](bicep-decompile.md)을 참조 하세요.
