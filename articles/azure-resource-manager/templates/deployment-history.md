---
title: 배포 기록
description: 포털, PowerShell, Azure CLI 및 REST API를 사용하여 Azure Resource Manager 배포 작업을 확인하는 방법을 설명합니다.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 00e0c51244d5c191d4c9f05f689b90ece81ec5a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91284621"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Azure Resource Manager를 사용한 배포 기록 보기

Azure Resource Manager를 사용 하 여 배포 기록을 볼 수 있습니다. 이전 배포의 특정 작업을 검사하고 어떤 리소스가 배포되었는지 확인할 수 있습니다. 이 기록에는 오류에 대 한 정보가 포함 됩니다.

리소스 그룹에 대 한 배포 기록은 800 배포로 제한 됩니다. 한도에 도달 하면 배포는 기록에서 자동으로 삭제 됩니다. 자세한 내용은 [배포 기록에서 자동 삭제](deployment-history-deletions.md)를 참조하세요.

특정 배포 오류에 대한 도움말은 [Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](common-deployment-errors.md)을 참조하세요.

## <a name="get-deployments-and-correlation-id"></a>배포 및 상관 관계 ID 가져오기

Azure Portal, PowerShell, Azure CLI 또는 REST API를 통해 배포에 대한 세부 정보를 볼 수 있습니다. 각 배포에는 관련 이벤트를 추적하는 데 사용되는 상관 관계 ID가 있습니다. [Azure 지원 요청을 만드는](../../azure-portal/supportability/how-to-create-azure-support-request.md)경우 지원에서 상관 관계 ID를 요청할 수 있습니다. 지원에서 상관 관계 ID를 사용 하 여 실패 한 배포에 대 한 작업을 식별 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 검사하려는 리소스 그룹을 선택합니다.

1. **배포**에서 링크를 선택합니다.

   ![배포 기록 선택](./media/deployment-history/select-deployment-history.png)

1. 배포 기록에서 배포 중 하나를 선택합니다.

   ![배포 선택](./media/deployment-history/select-details.png)

1. 상관 관계 ID를 포함하여 배포 요약이 표시됩니다.

    ![배포 요약](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

리소스 그룹에 대한 모든 배포를 나열하려면 [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) 명령을 사용합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

리소스 그룹에서 특정 배포를 가져오려면 **DeploymentName** 매개 변수를 추가합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

상관 관계 ID를 가져오려면 다음 코드를 사용합니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

리소스 그룹에 대한 배포를 나열하려면 [az deployment group list](/cli/azure/group/deployment#az-deployment-group-list)를 사용합니다.

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

특정 배포를 가져오려면 [az deployment group show](/cli/azure/group/deployment#az-deployment-group-show)를 사용합니다.

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

상관 관계 ID를 가져오려면 다음 코드를 사용합니다.

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

리소스 그룹에 대한 배포를 나열하려면 다음 작업을 사용합니다. 요청에 사용할 최신 API 버전 번호는 [배포 - 리소스 그룹별 목록](/rest/api/resources/deployments/listbyresourcegroup)을 참조하세요.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

특정 배포를 가져오려면 다음 작업을 사용합니다. 요청에 사용할 최신 API 버전 번호는 [배포 - 가져오기](/rest/api/resources/deployments/get)를 참조하세요.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

응답에는 상관 관계 ID가 포함됩니다.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>배포 작업 및 오류 메시지 가져오기

각 배포에는 여러 작업이 포함될 수 있습니다. 배포에 대한 세부 정보를 보려면 배포 작업을 확인하세요. 배포에 실패하면 배포 작업에 오류 메시지가 포함됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 배포 요약에서 **작업 세부 정보**를 선택합니다.

    ![작업 세부 정보 선택](./media/deployment-history/get-operation-details.png)

1. 배포 단계에 대한 세부 정보가 표시됩니다. 오류가 발생하면 세부 정보에 오류 메시지가 포함됩니다.

    ![작업 세부 정보 표시](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

리소스 그룹에 배포하기 위한 배포 작업을 보려면 [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) 명령을 사용합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

실패한 작업을 보려면 **실패한** 상태로 작업을 필터링합니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

실패한 작업에 대한 상태 메시지를 가져오려면 다음 명령을 사용합니다.

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

리소스 그룹에 배포하기 위한 배포 작업을 보려면 [az deployment operation group list](/cli/azure/deployment/operation/group#az-deployment-operation-group-list) 명령을 사용합니다. Azure CLI 2.6.0 이상이 있어야 합니다.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

실패한 작업을 보려면 **실패한** 상태로 작업을 필터링합니다.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

실패한 작업에 대한 상태 메시지를 가져오려면 다음 명령을 사용합니다.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

배포 작업을 가져오려면 다음 작업을 사용합니다. 요청에 사용할 최신 API 버전 번호는 [배포 작업 - 목록](/rest/api/resources/deploymentoperations/list)을 참조하세요.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

응답에 오류 메시지가 포함되어 있습니다.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>다음 단계

* 특정 배포 오류에 대한 도움말은 [Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](common-deployment-errors.md)을 참조하세요.
* 배포 기록에서 배포를 관리 하는 방법에 대 한 자세한 내용은 [배포 기록에서 자동 삭제](deployment-history-deletions.md)를 참조 하세요.
* 실행하기 전에 배포의 유효성을 검사하려면 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](deploy-powershell.md)를 참조하세요.

