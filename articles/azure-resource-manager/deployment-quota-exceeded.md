---
title: Azure 배포 할당량 초과
description: 리소스 그룹 기록에 배포 수가 800 개를 초과 하는 오류를 해결 하는 방법을 설명 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/01/2019
ms.author: tomfitz
ms.openlocfilehash: f06aff74e2cf800d44115f34921825122b09a9e7
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719436"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>배포 수가 800를 초과 하는 경우 오류 해결

각 리소스 그룹은 배포 기록에서 800 배포로 제한 됩니다. 이 문서에서는 허용 되는 800 배포를 초과 하기 때문에 배포가 실패할 때 수신 하는 오류를 설명 합니다. 이 오류를 해결 하려면 리소스 그룹 기록에서 배포를 삭제 합니다. 기록에서 배포를 삭제 해도 배포 된 리소스에는 영향을 주지 않습니다.

## <a name="symptom"></a>증상

배포 하는 동안 현재 배포가 800 배포의 할당량을 초과 한다는 오류가 표시 됩니다.

## <a name="solution"></a>솔루션

### <a name="azure-cli"></a>Azure CLI

[Az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete) 명령을 사용 하 여 기록에서 배포를 삭제 합니다.

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

다음 명령을 사용 하 여 배포 기록에서 현재 개수를 가져올 수 있습니다.

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

[AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) 명령을 사용 하 여 기록에서 배포를 삭제 합니다.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

다음 명령을 사용 하 여 배포 기록에서 현재 개수를 가져올 수 있습니다.

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

