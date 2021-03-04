---
title: JSON 및 Bicep의 Azure Resource Manager 템플릿에 대 한 구문 비교
description: JSON 및 Bicep를 사용 하 여 개발 된 Azure Resource Manager 템플릿을 비교 하 고 언어 간에 변환 하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 29c2b9948957ebc10a26f22f0fe3daf383dfe5ba
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036217"
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

## <a name="recommendations"></a>권장 사항

* 가능 하면 Bicep 파일에 [참조](template-functions-resource.md#reference) 및 [resourceId](template-functions-resource.md#resourceid) 함수를 사용 하지 마십시오. 동일한 Bicep 배포에서 리소스를 참조 하는 경우에는 리소스 식별자를 대신 사용 합니다. 예를 들어 리소스 식별자로를 사용 하 여 Bicep 파일에 리소스를 배포한 경우 `stg` 또는와 같은 구문을 사용 `stg.id` `stg.properties.primaryEndpoints.blob` 하 여 속성 값을 가져옵니다. 리소스 식별자를 사용 하 여 리소스 간에 암시적 종속성을 만듭니다. DependsOn 속성을 사용 하 여 종속성을 명시적으로 설정할 필요는 없습니다.
* 식별자에 대해 일관 된 대/소문자를 사용 합니다. 사용할 대/소문자 유형을 잘 모르겠으면 카멜식 대/소문자 구분을 시도 합니다. 예들 들어 `param myCamelCasedParameter string`입니다.
* 설명에서 사용자에 게 중요 한 정보를 제공 하는 경우에만 매개 변수에 대 한 설명을 추가 합니다. `//`일부 정보에는 주석을 사용할 수 있습니다.

## <a name="decompile-json-to-bicep"></a>JSON을 Bicep로 디컴파일

Bicep CLI는 기존 ARM 템플릿을 Bicep 파일로 디컴파일 하는 명령을 제공 합니다. JSON 파일을 디컴파일 하려면 다음을 사용 합니다. `bicep decompile "path/to/file.json"`

이 명령은 Bicep 작성을 위한 시작점을 제공 하지만 모든 템플릿에서는 명령이 작동 하지 않습니다. 명령이 실패 하거나 디컴파일을 이후의 문제를 해결 해야 할 수 있습니다. 현재 중첩 된 템플릿은 ' inner ' 식 계산 범위를 사용 하는 경우에만 디컴파일된 수 있습니다.

리소스 그룹에 대 한 템플릿을 내보낸 다음 bicep 디컴파일 명령에 직접 전달할 수 있습니다. 다음 예제에서는 내보낸 템플릿을 디컴파일 하는 방법을 보여 줍니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[포털](#tab/azure-portal)

포털을 통해 [템플릿을 내보냅니다](export-template-portal.md) . `bicep decompile <filename>`다운로드 한 파일에서를 사용 합니다.

---

## <a name="build-json-from-bicep"></a>Bicep에서 JSON 빌드

또한 Bicep CLI는 Bicep를 JSON으로 변환 하는 명령을 제공 합니다. JSON 파일을 작성 하려면 다음을 사용 합니다. `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>Side-by-side 보기

[Bicep 필드](https://aka.ms/bicepdemo) 를 사용 하면 동등한 JSON 및 Bicep 파일을 나란히 볼 수 있습니다. 샘플 템플릿을 선택 하 여 두 버전을 모두 볼 수 있습니다. 또는 `Decompile` 고유한 JSON 템플릿을 업로드 하 고 해당 하는 Bicep 파일을 보려면 선택 합니다.

## <a name="next-steps"></a>다음 단계

Bicep에 대 한 자세한 내용은 [Bicep 자습서](./bicep-tutorial-create-first-bicep.md)를 참조 하십시오.
