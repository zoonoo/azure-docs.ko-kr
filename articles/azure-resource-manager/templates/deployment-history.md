---
title: 배포 기록
description: 포털, PowerShell, Azure CLI 및 REST API를 사용하여 Azure Resource Manager 배포 작업을 확인하는 방법을 설명합니다.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: b0f196f86bed05094b04bfc20c7cef2248a91c65
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460299"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 배포 기록 보기

Azure Resource Manager를 사용 하 여 배포 기록을 확인 하 고 이전 배포에서 특정 작업을 검토할 수 있습니다. 배포 된 리소스를 확인 하 고 오류에 대 한 정보를 가져올 수 있습니다.

특정 배포 오류에 대한 도움말은 [Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](common-deployment-errors.md)을 참조하세요.

## <a name="get-deployments-and-correlation-id"></a>배포 및 상관 관계 ID 가져오기

Azure Portal, PowerShell, Azure CLI 또는 REST API를 통해 배포에 대 한 세부 정보를 볼 수 있습니다. 각 배포에는 관련 이벤트를 추적 하는 데 사용 되는 상관 관계 ID가 있습니다. 기술 지원 서비스를 사용 하 여 배포 문제를 해결할 때 유용할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 검사 하려는 리소스 그룹을 선택 합니다.

1. **배포**아래에서 링크를 선택 합니다.

   ![배포 기록 선택](./media/deployment-history/select-deployment-history.png)

1. 배포 기록에서 배포 중 하나를 선택 합니다.

   ![배포 선택](./media/deployment-history/select-details.png)

1. 상관 관계 ID를 포함 하 여 배포 요약이 표시 됩니다.

    ![배포 요약](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

리소스 그룹에 대 한 모든 배포를 나열 하려면 [AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) 명령을 사용 합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

리소스 그룹에서 특정 배포를 가져오려면 **Deploymentname** 매개 변수를 추가 합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

상관 관계 ID를 가져오려면 다음 코드를 사용합니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

리소스 그룹에 대 한 배포를 나열 하려면 [az deployment group list](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list)를 사용 합니다.

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

특정 배포를 가져오려면 [az deployment group show](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show)를 사용 합니다.

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

상관 관계 ID를 가져오려면 다음 코드를 사용합니다.

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

리소스 그룹에 대 한 배포를 나열 하려면 다음 작업을 사용 합니다. 요청에서 사용할 최신 API 버전 번호는 [배포-리소스 그룹별 목록](/rest/api/resources/deployments/listbyresourcegroup)을 참조 하세요.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

특정 배포를 가져옵니다. 다음 작업을 사용 합니다. 요청에서 사용할 최신 API 버전 번호는 [배포-가져오기](/rest/api/resources/deployments/get)를 참조 하세요.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

응답에는 상관 관계 ID가 포함 됩니다.

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

각 배포에는 여러 작업이 포함 될 수 있습니다. 배포에 대 한 자세한 정보를 보려면 배포 작업을 확인 하세요. 배포에 실패 하면 배포 작업에 오류 메시지가 포함 됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 배포에 대 한 요약에서 **작업 세부 정보**를 선택 합니다.

    ![배포 작업 선택](./media/deployment-history/get-operation-details.png)

1. 배포 단계에 대 한 세부 정보가 표시 됩니다. 오류가 발생 하면 세부 정보에 오류 메시지가 포함 됩니다.

    ![작업 세부 정보 표시](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

리소스 그룹에 배포 하기 위한 배포 작업을 보려면 [AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) 명령을 사용 합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

실패 한 작업을 보려면 **실패** 상태를 사용 하 여 작업을 필터링 합니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

실패 한 작업의 상태 메시지를 가져오려면 다음 명령을 사용 합니다.

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

리소스 그룹에 배포 하기 위한 배포 작업을 보려면 [az deployment group operation list](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-deployment-group-operation-list) 명령을 사용 합니다.

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeployment
```

실패 한 작업을 보려면 **실패** 상태를 사용 하 여 작업을 필터링 합니다.

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

실패 한 작업의 상태 메시지를 가져오려면 다음 명령을 사용 합니다.

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

배포 작업을 가져오려면 다음 작업을 사용 합니다. 요청에서 사용할 최신 API 버전 번호는 [배포 작업-목록](/rest/api/resources/deploymentoperations/list)을 참조 하세요.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

응답에 오류 메시지가 포함 되어 있습니다.

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
* 활동 로그를 사용하여 다른 유형의 작업을 모니터링하는 방법에 대해 알아보려면 [활동 로그를 보고 Azure 리소스 관리](../management/view-activity-logs.md)를 참조하세요.
* 실행하기 전에 배포의 유효성을 검사하려면 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](deploy-powershell.md)를 참조하세요.

