---
title: 배포 기록 삭제
description: Azure Resource Manager가 배포 기록에서 배포를 자동으로 삭제하는 방법을 설명합니다. 기록이 800개 제한을 초과하는 경우 배포가 삭제됩니다.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: e2920eb1daa626b6a817b2fe3b388e8c531f12e4
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109751621"
---
# <a name="automatic-deletions-from-deployment-history"></a>배포 기록에서 자동 삭제

템플릿을 배포할 때마다 배포에 대한 정보가 배포 기록에 기록됩니다. 각 리소스 그룹의 배포 기록에서 배포는 800개로 제한됩니다.

Azure Resource Manager는 한도에 가까워지면 자동으로 기록에서 배포를 삭제합니다. 자동 삭제는 이전 동작에서 변경된 것입니다. 이전에는 오류가 발생하지 않도록 배포 기록에서 배포를 수동으로 삭제해야 했습니다. 이 변경은 2020년 8월 6일에 구현되었습니다.

**자동 삭제는 리소스 그룹 배포에 대해 지원됩니다. 현재 [구독](deploy-to-subscription.md), [관리 그룹](deploy-to-management-group.md) 및 [테넌트](deploy-to-tenant.md) 배포에 대한 기록에서 배포는 자동으로 삭제되지 않습니다.**

> [!NOTE]
> 기록에서 배포를 삭제해도 배포된 리소스에는 영향을 주지 않습니다.

## <a name="when-deployments-are-deleted"></a>배포가 삭제된 경우

배포가 775개를 초과하는 경우 기록에서 배포가 삭제됩니다. Azure Resource Manager은 기록이 750까지 감소할 때까지 배포를 삭제합니다. 가장 오래된 배포가 항상 먼저 삭제됩니다.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="배포 기록에서 삭제":::

> [!NOTE]
> 시작 번호(775)와 끝 번호(750)는 변경될 수 있습니다.
>
> 리소스 그룹이 이미 800개 제한에 도달하면 다음 배포는 오류로 인해 실패합니다. 자동 삭제 프로세스가 즉시 시작됩니다. 잠시 기다리면 배포를 다시 시도할 수 있습니다.

배포 외에도 [가상 작업](template-deploy-what-if.md)을 실행하거나 배포의 유효성을 검사할 때 삭제를 트리거합니다.

배포의 이름을 기록에 있는 이름과 동일한 이름으로 지정하면 기록에서 해당 위치가 재설정됩니다. 배포는 기록에서 가장 최근 위치로 이동합니다. 또한 오류가 발생한 후 [해당 배포로 롤백](rollback-on-error.md)하는 경우 배포 위치가 재설정됩니다.

## <a name="remove-locks-that-block-deletions"></a>삭제를 차단하는 잠금 제거

리소스 그룹에 대해 [CanNotDelete 잠금](../management/lock-resources.md)이 설정된 경우 해당 리소스 그룹에 대한 배포를 삭제할 수 없습니다. 배포 기록에서 자동 삭제를 활용하려면 잠금을 제거해야 합니다.

PowerShell을 사용하여 잠금을 삭제하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Azure CLI를 사용하여 잠금을 삭제하려면 다음 명령을 실행합니다.

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>필요한 사용 권한

템플릿을 배포한 사용자의 ID로 삭제를 요청합니다. 배포를 삭제하려면 사용자에게 **Microsoft.Resources/deployments/delete** 작업에 대한 액세스 권한이 있어야 합니다. 사용자에게 필요한 권한이 없는 경우에는 기록에서 배포가 삭제되지 않습니다.

현재 사용자에게 필요한 권한이 없는 경우 다음 배포 중에 자동 삭제가 다시 시도됩니다.

## <a name="opt-out-of-automatic-deletions"></a>자동 삭제 옵트아웃

기록에서 자동 삭제를 옵트아웃(opt out)할 수 있습니다. **배포 기록을 직접 관리하려는 경우에만 이 옵션을 사용합니다.** 기록에서 800개 배포 제한은 여전히 적용됩니다. 800개 배포를 초과하는 경우 오류가 표시되고 배포가 실패합니다.

자동 삭제를 사용하지 않도록 설정하려면 `Microsoft.Resources/DisableDeploymentGrooming` 기능 플래그를 등록합니다. 기능 플래그를 등록할 때 전체 Azure 구독에 대한 자동 삭제를 옵트아웃(opt out)할 수 있습니다. 특정 리소스 그룹에 대해서만 옵트아웃(opt out)할 수 없습니다. 자동 삭제를 다시 사용하도록 설정하려면 기능 플래그를 등록 해제합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell의 경우 [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature)를 사용합니다.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

구독의 현재 상태를 확인하려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

자동 삭제를 다시 사용하도록 설정하려면 Azure REST API 또는 Azure CLI를 사용합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI의 경우 [az feature register](/cli/azure/feature#az_feature_register)를 사용합니다.

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

구독의 현재 상태를 확인하려면 다음을 사용합니다.

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

자동 삭제를 다시 사용하도록 설정하려면 [az feature unregister](/cli/azure/feature#az_feature_unregister)를 사용합니다.

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST (영문)](#tab/rest)

REST API의 경우 [Features - Register](/rest/api/resources/features/register)를 사용합니다.

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

구독의 현재 상태를 확인하려면 다음을 사용합니다.

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

자동 삭제를 다시 사용하도록 설정하려면 [Features - Unregister](/rest/api/resources/features/unregister)를 사용합니다.

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>다음 단계

* 배포 기록을 보는 방법에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 배포 기록 보기](deployment-history.md)를 참조하세요.
