---
title: 배포 할당량초과
description: 리소스 그룹 기록에 800개 이상의 배포가 있는 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245092"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>배포 수가 800을 초과하면 오류 해결

각 리소스 그룹은 배포 기록에서 800개의 배포로 제한됩니다. 이 문서에서는 허용된 800개의 배포를 초과하기 때문에 배포가 실패할 때 받는 오류에 대해 설명합니다. 이 오류를 해결하려면 리소스 그룹 기록에서 배포를 삭제합니다. 기록에서 배포를 삭제해도 배포된 리소스에는 영향을 주지 않습니다.

## <a name="symptom"></a>증상

배포 하는 동안 현재 배포 800 배포의 할당량을 초과 한다는 오류가 발생 합니다.

## <a name="solution"></a>해결 방법

### <a name="azure-cli"></a>Azure CLI

az [배포 그룹 삭제](/cli/azure/group/deployment) 명령을 사용하여 기록에서 배포를 삭제합니다.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

5일이 지난 모든 배포를 삭제하려면 다음을 사용하십시오.

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

다음 명령을 통해 배포 기록의 현재 수를 얻을 수 있습니다.

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

[제거-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) 명령을 사용 하 여 기록에서 배포를 삭제 합니다.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

5일이 지난 모든 배포를 삭제하려면 다음을 사용하십시오.

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

다음 명령을 통해 배포 기록의 현재 수를 얻을 수 있습니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>타사 솔루션

다음 외부 솔루션은 특정 시나리오를 다룹니다.

* [Azure 논리 앱 및 PowerShell 솔루션](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [아즈데브옵스 확장](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
