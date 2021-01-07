---
title: 배포 할당량 초과
description: 리소스 그룹 기록에 배포 수가 800 개를 초과 하는 오류를 해결 하는 방법을 설명 합니다.
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 8996d7817eea2f8daf44fbc9b4416c884b05940f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87987055"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>배포 수가 800를 초과 하는 경우 오류 해결

각 리소스 그룹은 배포 기록에서 800 배포로 제한 됩니다. 이 문서에서는 허용 되는 800 배포를 초과 하기 때문에 배포가 실패할 때 수신 하는 오류를 설명 합니다. 이 오류를 해결 하려면 리소스 그룹 기록에서 배포를 삭제 합니다. 기록에서 배포를 삭제 해도 배포 된 리소스에는 영향을 주지 않습니다.

Azure Resource Manager은 한도 근처에서 자동으로 기록에서 배포를 삭제 합니다. 다음 이유 중 하나에 해당 하는 경우에도이 오류가 표시 될 수 있습니다.

1. 배포 기록에서 삭제를 방지 하는 CanNotDelete 잠금이 리소스 그룹에 있습니다.
1. 자동 삭제를 옵트아웃 했습니다.
1. 여러 배포를 동시에 실행 하 고 있으며, 자동 삭제는 총 수를 줄이기 위해 신속 하 게 처리 되지 않습니다.

잠금을 제거 하거나 자동 삭제를 옵트인 하는 방법에 대 한 자세한 내용은 [배포 기록에서 자동 삭제](deployment-history-deletions.md)를 참조 하세요.

이 문서에서는 기록에서 배포를 수동으로 삭제 하는 방법을 설명 합니다.

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
