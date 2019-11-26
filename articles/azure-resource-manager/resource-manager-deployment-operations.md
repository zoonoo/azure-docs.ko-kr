---
title: 배포 기록
description: 포털, PowerShell, Azure CLI 및 REST API를 사용하여 Azure Resource Manager 배포 작업을 확인하는 방법을 설명합니다.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 23687ddcfb7911a999ee06ac8df8badf341b41d9
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484201"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>View deployment history with Azure Resource Manager

Azure Resource Manager enables you to view your deployment history and examine specific operations in past deployments. You can see the resources that were deployed, and get information about any errors.

특정 배포 오류에 대한 도움말은 [Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.

## <a name="portal"></a>포털

To get details about a deployment from the deployment history.

1. Select the resource group you want to examine.

1. Select the link under **Deployments**.

   ![Select deployment history](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Select one of the deployments from the deployment history.

   ![배포 선택](./media/resource-manager-deployment-operations/select-details.png)

1. A summary of the deployment is displayed, including a list of the resources that were deployed.

    ![배포 요약](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. To view the template used for the deployment, select **Template**. You can download the template to reuse it.

    ![템플릿 표시](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. If your deployment failed, you see an error message. Select the error message for more details.

    ![View failed deployment](./media/resource-manager-deployment-operations/show-error.png)

1. The detailed error message is displayed.

    ![View error details](./media/resource-manager-deployment-operations/show-details.png)

1. The correlation ID is used to track related events, and can be helpful when working with technical support to troubleshoot a deployment.

    ![Get correlation ID](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. To learn more about the step that failed, select **Operation details**.

    ![Select deployment operations](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. You see the details for that step of the deployment.

    ![Show operation details](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

배포의 전반적인 상태를 가져오려면 **Get-AzResourceGroupDeployment** 명령을 사용합니다.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

또는 실패한 배포에 대해서만 결과를 필터링할 수 있습니다.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

The correlation ID is used to track related events, and can be helpful when working with technical support to troubleshoot a deployment. 상관 관계 ID를 가져오려면 다음 코드를 사용합니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

각 배포에는 여러 작업이 포함되어 있습니다. 각 작업은 배포 프로세스의 단계를 나타냅니다. 배포에서 무엇이 잘못 되었는지 검색하려면 일반적으로 배포 작업에 대한 세부 정보를 확인해야 합니다. **Get-AzResourceGroupDeploymentOperation**을 사용하여 작업의 상태를 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

여러 작업이 각각 다음과 같은 형식으로 반환됩니다.

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

실패한 작업에 대한 자세한 정보를 얻으려면 상태가 **Failed** 인 작업에 대한 속성을 검색합니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

모든 실패한 작업이 각각 다음과 같은 형식으로 반환됩니다.

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

작업의 serviceRequestId 및 trackingId를 확인하세요. serviceRequestId는 기술 지원과 함께 배포 문제를 해결할 때 유용할 수 있습니다. You'll use the trackingId in the next step to focus on a particular operation.

특정 실패한 작업에 대한 상태 메시지를 얻으려면 다음 명령을 사용합니다.

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

반환하는 내용은 다음과 같습니다.

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Azure의 모든 배포 작업에는 요청 및 응답 콘텐츠가 포함됩니다. During deployment, you can use **DeploymentDebugLogLevel** parameter to specify that the request and/or response are logged.

로그에서 해당 정보를 가져오고 다음 PowerShell 명령을 사용하여 로컬에 저장합니다.

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

To get the overall status of a deployment, use the **azure group deployment show** command.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
The correlation ID is used to track related events, and can be helpful when working with technical support to troubleshoot a deployment.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

배포의 작업을 보려면 다음을 사용합니다.

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST (영문)

The following example shows how to get information about a deployment. For documentation about the latest API version, see the [Deployments - Get](/rest/api/resources/deployments/get) operation.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

응답에서 **provisioningState** , **correlationId** 및 **error** 요소에 특히 유의합니다. **correlationId** 는 관련 이벤트를 추적하는 데 사용되며 기술 지원과 함께 배포 문제를 해결할 때 유용할 수 있습니다.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

The following example shows how to get deployment operations. For documentation about the latest API version, see the [Deployment Operations - List](/rest/api/resources/deploymentoperations/list) operation.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
응답에는 배포 중에 **debugSetting** 속성에 지정하는 설정을 기준으로 요청 및/또는 응답 정보가 포함됩니다.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>다음 단계
* 특정 배포 오류에 대한 도움말은 [Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
* 활동 로그를 사용하여 다른 유형의 작업을 모니터링하는 방법에 대해 알아보려면 [활동 로그를 보고 Azure 리소스 관리](resource-group-audit.md)를 참조하세요.
* 실행하기 전에 배포의 유효성을 검사하려면 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](resource-group-template-deploy.md)를 참조하세요.

