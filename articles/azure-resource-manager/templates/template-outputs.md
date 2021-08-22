---
title: 템플릿의 출력
description: Azure Resource Manager 템플릿(ARM 템플릿) 및 Bicep 파일에서 출력 값을 정의하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 05/18/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 369c335027e4e7b2105f1f6eb10fda3225746333
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353468"
---
# <a name="outputs-in-arm-templates"></a>ARM 템플릿의 출력

이 문서에서는 Azure Resource Manager 템플릿(ARM 템플릿) 및 Bicep 파일에서 출력 값을 정의하는 방법을 설명합니다. 배포된 리소스에서 값을 반환해야 하는 경우 출력을 사용합니다.

각 출력 값의 형식은 [데이터 유형](data-types.md) 중 하나로 해석되어야 합니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>출력 값 정의

다음 예제에서는 배포된 리소스에서 속성을 반환하는 방법을 보여 줍니다.

# <a name="json"></a>[JSON](#tab/json)

JSON의 경우 `outputs` 섹션을 템플릿에 추가합니다. 출력 값은 공용 IP 주소의 정규화된 도메인 이름을 가져옵니다.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep의 경우 `output` 키워드를 사용합니다.

다음 예에서 `publicIP`는 Bicep 파일에 배포된 공용 IP 주소의 식별자입니다. 출력 값은 공용 IP 주소의 정규화된 도메인 이름을 가져옵니다.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

---

이름에 하이픈이 포함된 속성을 출력해야 하는 경우 점 표기법 대신 이름 주위에 대괄호를 사용합니다. 예를 들어, `.property-name` 대신 `['property-name']`을 사용합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>조건부 출력

조건에 따라 값을 반환할 수 있습니다. 일반적으로 리소스를 [조건부로 배포](conditional-resource-deployment.md)한 경우 조건부 출력을 사용합니다. 다음 예에서는 새 항목이 배포되었는지 여부에 따라 공용 IP 주소의 리소스 ID를 조건부로 반환하는 방법을 보여 줍니다.

# <a name="json"></a>[JSON](#tab/json)

JSON에서 `condition` 요소를 추가하여 출력이 반환되는지 여부를 정의합니다.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep에서 조건부 출력을 지정하려면 `?` 연산자를 사용합니다. 다음 예에서는 조건에 따라 엔드포인트 URL 또는 빈 문자열을 반환합니다.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

조건부 출력의 간단한 예는 [조건부 출력 템플릿](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)을 참조하세요.

## <a name="dynamic-number-of-outputs"></a>출력의 동적 수

일부 시나리오에서는 템플릿을 만들 때 반환해야 하는 값의 인스턴스 수를 알 수 없습니다. 반복 출력을 사용하여 가변 개수의 값을 반환할 수 있습니다.

# <a name="json"></a>[JSON](#tab/json)

JSON에서 `copy` 요소를 추가하여 출력을 반복합니다.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep에서 동적 출력에 대한 조건을 정의하는 `for` 표현식을 추가합니다. 다음 예에서는 정수 범위를 반복합니다. 배열을 반복할 수도 있습니다.

```bicep
output storageEndpoints array = [for i in range(0, storageCount): reference(${i}${baseName_var}).primaryEndpoints.blob]
```

---

자세한 내용은 [ARM 템플릿에서 출력 반복](copy-outputs.md)을 참조하세요.

## <a name="linked-templates"></a>연결된 템플릿

JSON 템플릿에서 [링크된 템플릿](linked-templates.md)을 사용하여 관련 템플릿을 배포할 수 있습니다. 링크된 템플릿에서 출력 값을 검색하려면 상위 템플릿에서 [reference](template-functions-resource.md#reference) 함수를 사용하세요. 부모 템플릿의 구문은 다음과 같습니다.

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

다음 예제에서는 연결된 템플릿에서 값을 검색하여 부하 분산 장치에서 IP 주소를 설정하는 방법을 보여 줍니다.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

속성 이름에 하이픈이 있는 경우 점 표기법 대신 이름 주위에 대괄호를 사용합니다.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

`reference` 함수는 [중첩된 템플릿](linked-templates.md#nested-template)의 출력 섹션에 사용할 수 없습니다. 중첩된 템플릿에서 배포된 리소스의 값을 반환하려면 중첩된 템플릿을 연결된 템플릿으로 변환합니다.

[공개 IP 주소 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json)은 공개 IP 주소를 만들고 리소스 ID를 출력합니다. [부하 분산기 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json)은 이전 템플릿에 연결됩니다. 부하 분산 장치를 만들 때 출력에 리소스 ID를 사용합니다.

## <a name="modules"></a>모듈

Bicep 파일에서 모듈을 사용하여 관련 템플릿을 배포할 수 있습니다. 모듈에서 출력 값을 검색하려면 다음 구문을 사용합니다.

```bicep
<module-name>.outputs.<property-name>
```

다음 예제에서는 모듈에서 값을 검색하여 부하 분산 장치에서 IP 주소를 설정하는 방법을 보여 줍니다. 모듈의 이름은 `publicIP`입니다.

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>예제 템플릿

다음 템플릿은 리소스를 배포하지 않습니다. 다양한 형식의 출력을 반환하는 몇가지 방법을 보여 줍니다.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep는 현재 루프를 지원하지 않습니다.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>출력 값 가져오기

배포에 성공하면 배포 결과에 출력 값이 자동으로 반환됩니다.

배포 기록에서 출력 값을 가져오려면 스크립트를 사용할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>다음 단계

* 출력에 사용할 수 있는 속성에 대한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
