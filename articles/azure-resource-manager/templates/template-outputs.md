---
title: 템플릿의 출력
description: Azure Resource Manager 템플릿 (ARM 템플릿) 및 Bicep 파일에서 출력 값을 정의 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 91feb1a0b653e4b6e96e38df57f87af27e4676f5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703837"
---
# <a name="outputs-in-arm-templates"></a>ARM 템플릿의 출력

이 문서에서는 Azure Resource Manager 템플릿 (ARM 템플릿) 및 Bicep 파일에서 출력 값을 정의 하는 방법을 설명 합니다. 배포 된 리소스에서 값을 반환 해야 하는 경우 출력을 사용 합니다.

각 출력 값의 형식은 [데이터 형식](template-syntax.md#data-types)중 하나로 확인 되어야 합니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>출력 값 정의

다음 예제에서는 배포 된 리소스에서 속성을 반환 하는 방법을 보여 줍니다.

# <a name="json"></a>[JSON](#tab/json)

JSON에 대해 섹션을 `outputs` 템플릿에 추가 합니다. 출력 값은 공용 IP 주소의 정규화 된 도메인 이름을 가져옵니다.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep의 경우 키워드를 사용 `output` 합니다.

다음 예제에서 `publicIP` 은 Bicep 파일에 배포 된 공용 IP 주소의 식별자입니다. 출력 값은 공용 IP 주소의 정규화 된 도메인 이름을 가져옵니다.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

---

이름에 하이픈이 포함 된 속성을 출력 해야 하는 경우 점 표기법 대신 이름 주위에 대괄호를 사용 합니다. 예를 들어  `['property-name']` 대신를 사용 `.property-name` 합니다.

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

조건에 따라 값을 반환할 수 있습니다. 일반적으로 리소스를 [조건부로 배포](conditional-resource-deployment.md) 하는 경우 조건부 출력을 사용 합니다. 다음 예제에서는 새 항목을 배포 했는지 여부에 따라 공용 IP 주소에 대 한 리소스 ID를 조건부로 반환 하는 방법을 보여 줍니다.

# <a name="json"></a>[JSON](#tab/json)

JSON에서 요소를 추가 `condition` 하 여 출력이 반환 되는지 여부를 정의 합니다.

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

Bicep에서 조건부 출력을 지정 하려면 연산자를 사용 `?` 합니다. 다음 예에서는 조건에 따라 끝점 URL 또는 빈 문자열을 반환 합니다.

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

조건부 출력의 간단한 예는 [조건부 출력 템플릿](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)을 참조 하세요.

## <a name="dynamic-number-of-outputs"></a>동적 출력 수

일부 시나리오에서는 템플릿을 만들 때 반환 해야 하는 값의 인스턴스 수를 알 수 없습니다. 반복 출력을 사용 하 여 다양 한 값을 반환할 수 있습니다.

# <a name="json"></a>[JSON](#tab/json)

JSON에서 요소를 추가 `copy` 하 여 출력을 반복 합니다.

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

반복 출력은 현재 Bicep에서 사용할 수 없습니다.

---

자세한 내용은 [ARM 템플릿의 출력 반복](copy-outputs.md)을 참조 하세요.

## <a name="linked-templates"></a>연결된 템플릿

JSON 템플릿에서 [연결 된 템플릿을](linked-templates.md)사용 하 여 관련 템플릿을 배포할 수 있습니다. 연결 된 템플릿에서 출력 값을 검색 하려면 부모 템플릿에서 [reference](template-functions-resource.md#reference) 함수를 사용 합니다. 부모 템플릿의 구문은 다음과 같습니다.

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

다음 예제에서는 연결 된 템플릿에서 값을 검색 하 여 부하 분산 장치에서 IP 주소를 설정 하는 방법을 보여 줍니다.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

속성 이름에 하이픈이 있으면 점 표기법 대신 이름 주위에 대괄호를 사용 합니다.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

`reference` 함수는 [중첩된 템플릿](linked-templates.md#nested-template)의 출력 섹션에 사용할 수 없습니다. 중첩된 템플릿에서 배포된 리소스의 값을 반환하려면 중첩된 템플릿을 연결된 템플릿으로 변환합니다.

[공용 ip 주소 템플릿은](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) 공용 ip 주소를 만들고 리소스 ID를 출력 합니다. [부하 분산 장치 템플릿은](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) 이전 템플릿에 연결 됩니다. 부하 분산 장치를 만들 때 출력에서 리소스 ID를 사용 합니다.

## <a name="modules"></a>모듈

Bicep 파일에서 모듈을 사용 하 여 관련 템플릿을 배포할 수 있습니다. 모듈에서 출력 값을 검색 하려면 다음 구문을 사용 합니다.

```bicep
<module-name>.outputs.<property-name>
```

다음 예제에서는 모듈에서 값을 검색 하 여 부하 분산 장치에서 IP 주소를 설정 하는 방법을 보여 줍니다. 모듈의 이름은 `publicIP` 입니다.

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>예제 템플릿

다음 템플릿은 리소스를 배포 하지 않습니다. 다른 형식의 출력을 반환 하는 몇 가지 방법을 보여 줍니다.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep는 현재 루프를 지원 하지 않습니다.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>출력 값 가져오기

배포에 성공 하면 배포 결과에 출력 값이 자동으로 반환 됩니다.

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

* 출력에 사용할 수 있는 속성에 대 한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
