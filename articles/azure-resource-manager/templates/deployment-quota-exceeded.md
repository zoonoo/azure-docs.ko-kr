---
title: 배포 할당량 초과
description: 리소스 그룹 기록에 배포 수가 800 개를 초과 하는 오류를 해결 하는 방법을 설명 합니다.
ms.topic: troubleshooting
ms.date: 06/25/2020
ms.openlocfilehash: 1b0c3de6007964b487a13e71cd43bd984cd970f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391182"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>배포 수가 800를 초과 하는 경우 오류 해결

각 리소스 그룹은 배포 기록에서 800 배포로 제한 됩니다. 이 문서에서는 허용 되는 800 배포를 초과 하기 때문에 배포가 실패할 때 수신 하는 오류를 설명 합니다. 이 오류를 해결 하려면 리소스 그룹 기록에서 배포를 삭제 합니다. 기록에서 배포를 삭제 해도 배포 된 리소스에는 영향을 주지 않습니다.

> [!NOTE]
> Azure Resource Manager은 한도 근처에서 자동으로 기록에서 배포를 삭제 하기 시작 합니다. 자동 삭제를 옵트아웃 한 경우에도이 오류가 표시 될 수 있습니다. 자세한 내용은 [배포 기록에서 자동 삭제](deployment-history-deletions.md)를 참조 하세요.

## <a name="symptom"></a>증상

배포 하는 동안 현재 배포가 800 배포의 할당량을 초과 한다는 오류가 표시 됩니다.

## <a name="solution"></a>솔루션

### <a name="azure-cli"></a>Azure CLI

[Az deployment group delete](/cli/azure/group/deployment) 명령을 사용 하 여 기록에서 배포를 삭제 합니다.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

5 일이 지난 모든 배포를 삭제 하려면 다음을 사용 합니다.

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

다음 명령을 사용 하 여 배포 기록에서 현재 개수를 가져올 수 있습니다.

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

[AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) 명령을 사용 하 여 기록에서 배포를 삭제 합니다.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

5 일이 지난 모든 배포를 삭제 하려면 다음을 사용 합니다.

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

다음 명령을 사용 하 여 배포 기록에서 현재 개수를 가져올 수 있습니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>타사 솔루션

다음 외부 솔루션은 특정 시나리오를 처리 합니다.

* [Azure Logic Apps 및 PowerShell 솔루션](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps 확장](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
