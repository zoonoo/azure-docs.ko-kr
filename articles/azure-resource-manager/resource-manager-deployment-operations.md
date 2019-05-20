---
title: Azure Resource Manager를 사용 하 여 배포 기록 | Microsoft Docs
description: 포털, PowerShell, Azure CLI 및 REST API를 사용하여 Azure Resource Manager 배포 작업을 확인하는 방법을 설명합니다.
tags: top-support-issue
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 58d22e3fcae5c30e5d7dcc39b317afeef4a693ee
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605948"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 배포 기록 보기

Azure Resource Manager를 사용 하면 배포 기록 보기 및 과거 배포에서 특정 작업으로 검토할 수 있습니다. 배포 된 리소스를 참조 하 고 모든 오류에 대 한 정보를 얻을 수 있습니다.

특정 배포 오류에 대한 도움말은 [Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.

## <a name="portal"></a>포털

배포 기록에서 배포에 대 한 정보를 가져오려면.

1. 검사 하려는 리소스 그룹을 선택 합니다.

1. 아래의 링크를 선택 **배포**합니다.

   ![배포 기록 선택](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. 배포 기록에서 배포 중 하나를 선택 합니다.

   ![배포를 선택 합니다.](./media/resource-manager-deployment-operations/select-details.png)

1. 배포 된 리소스 목록을 포함 하 여 배포의 요약이 표시 됩니다.

    ![배포 요약](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. 배포에 사용 된 템플릿을 보려면 **템플릿**합니다. 다시 사용 하도록 템플릿을 다운로드할 수 있습니다.

    ![템플릿 표시](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. 배포가 실패 하는 경우 오류 메시지가 표시 됩니다. 자세한 오류 메시지를 선택 합니다.

    ![실패 한 배포 보기](./media/resource-manager-deployment-operations/show-error.png)

1. 자세한 오류 메시지가 표시 됩니다.

    ![오류 세부 정보 보기](./media/resource-manager-deployment-operations/show-details.png)

1. 상관 관계 ID 관련된 이벤트를 추적 하는 데 사용 되 고 기술 지원을 받을 배포 문제를 해결할 때 유용할 수 있습니다.

    ![상관 관계 ID 가져오기](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. 선택 실패 한 단계에 대 한 자세한 내용은 **작업 세부 정보**합니다.

    ![배포 작업 선택](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. 배포의 해당 단계에 대 한 세부 정보 표시 됩니다.

    ![작업 세부 정보를 표시 합니다.](./media/resource-manager-deployment-operations/show-operation-details.png)

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

상관 관계 ID 관련된 이벤트를 추적 하는 데 사용 되 고 기술 지원을 받을 배포 문제를 해결할 때 유용할 수 있습니다. 상관 관계 ID를 가져오려면 다음 코드를 사용합니다.

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

작업의 serviceRequestId 및 trackingId를 확인하세요. serviceRequestId는 기술 지원과 함께 배포 문제를 해결할 때 유용할 수 있습니다. 특정 작업에 집중할 수 trackingId는 다음 단계에서 사용 합니다.

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

Azure의 모든 배포 작업에는 요청 및 응답 콘텐츠가 포함됩니다. 배포 하는 동안 사용할 수 있습니다 **DeploymentDebugLogLevel** 매개 변수를 요청 및/또는 응답 기록 됩니다 지정 합니다.

로그에서 해당 정보를 가져오고 다음 PowerShell 명령을 사용하여 로컬에 저장합니다.

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

배포의 전반적인 상태를 사용 합니다 **azure 그룹 배포 표시** 명령입니다.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
상관 관계 ID 관련된 이벤트를 추적 하는 데 사용 되 고 기술 지원을 받을 배포 문제를 해결할 때 유용할 수 있습니다.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

배포의 작업을 보려면 다음을 사용합니다.

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST (영문)

배포에 대 한 정보를 사용 합니다 [템플릿 배포에 대 한 정보를 가져올](https://docs.microsoft.com/rest/api/resources/deployments) 작업 합니다.

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

배포에 대 한 정보를 가져오려면 [모든 템플릿 배포 작업 나열](https://docs.microsoft.com/rest/api/resources/deployments)합니다. 

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

