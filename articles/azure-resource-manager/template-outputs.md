---
title: 템플릿의 출력
description: Azure Resource Manager 템플릿에서 출력 값을 정의 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 61120b9941a6a20812ea046265ecbe13014d769e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689139"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Azure Resource Manager 템플릿의 출력

이 문서에서는 Azure Resource Manager 템플릿에서 출력 값을 정의 하는 방법을 설명 합니다. 배포 된 리소스에서 값을 반환 해야 하는 경우 출력을 사용 합니다.

## <a name="define-output-values"></a>출력 값 정의

다음 예제에서는 공용 IP 주소의 리소스 ID를 반환하는 방법을 보여줍니다.

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>조건부 출력

출력 섹션에서 조건부로 값을 반환할 수 있습니다. 일반적으로 리소스를 [조건부로 배포한](conditional-resource-deployment.md) 경우 출력에서 조건을 사용 합니다. 다음 예제에서는 새 항목을 배포 했는지 여부에 따라 공용 IP 주소에 대 한 리소스 ID를 조건부로 반환 하는 방법을 보여 줍니다.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

조건부 출력의 간단한 예는 [조건부 출력 템플릿](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)을 참조 하세요.

## <a name="linked-templates"></a>연결된 템플릿

연결 된 템플릿에서 출력 값을 검색 하려면 부모 템플릿에서 [reference](resource-group-template-functions-resource.md#reference) 함수를 사용 합니다. 부모 템플릿의 구문은 다음과 같습니다.

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

연결된 템플릿에서 출력 속성을 가져올 때 속성 이름에 대시를 포함할 수 없습니다.

다음 예제에서는 연결 된 템플릿에서 값을 검색 하 여 부하 분산 장치에서 IP 주소를 설정 하는 방법을 보여 줍니다.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

`reference` 함수는 [중첩된 템플릿](resource-group-linked-templates.md#nested-template)의 출력 섹션에 사용할 수 없습니다. 중첩된 템플릿에서 배포된 리소스의 값을 반환하려면 중첩된 템플릿을 연결된 템플릿으로 변환합니다.

## <a name="get-output-values"></a>출력 값 가져오기

배포에 성공 하면 배포 결과에 출력 값이 자동으로 반환 됩니다.

배포 기록에서 출력 값을 가져오려면 스크립트를 사용할 수 있습니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 출력을 사용 하는 시나리오를 보여 줍니다.

|Template  |설명  |
|---------|---------|
|[변수 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 복잡한 변수를 만들고 해당 값을 출력합니다. 여기서는 리소스를 배포하지 않습니다. |
|[공용 IP 주소](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 공용 IP 주소를 만들고 리소스 ID를 출력합니다. |
|[부하 분산 장치](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 이전 템플릿에 연결됩니다. 부하 분산 장치를 만들 때 출력에 리소스 ID를 사용합니다. |

## <a name="next-steps"></a>다음 단계

* 출력에 사용할 수 있는 속성에 대 한 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조 하세요.
