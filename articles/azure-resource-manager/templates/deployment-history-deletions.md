---
title: 배포 기록 삭제
description: Azure Resource Manager 배포 기록에서 배포를 자동으로 삭제 하는 방법을 설명 합니다. 기록은 800 제한을 초과 하는 경우에만 삭제 됩니다.
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 8ec3291dc5e35689d4e2c614949e0328057fbfd3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248987"
---
# <a name="automatic-deletions-from-deployment-history"></a>배포 기록에서 자동 삭제

템플릿을 배포할 때마다 배포에 대 한 정보가 배포 기록에 기록 됩니다. 각 리소스 그룹은 배포 기록에서 800 배포로 제한 됩니다.

Azure Resource Manager은 한도 근처에서 자동으로 기록에서 배포를 삭제 하기 시작 합니다. 자동 삭제는 이전 동작에서 변경 된 내용입니다. 이전에는 오류가 발생 하지 않도록 배포 기록에서 배포를 수동으로 삭제 해야 했습니다. **이 기능은 아직 Azure에 추가 되지 않았습니다. 옵트아웃 (opt out) 하려는 경우이 예정 된 변경 내용을 사용자에 게 알려 드립니다.**

> [!NOTE]
> 기록에서 배포를 삭제 해도 배포 된 리소스에는 영향을 주지 않습니다.
>
> 리소스 그룹에 대해 [Cannotdelete 잠금이](../management/lock-resources.md) 설정 된 경우 해당 리소스 그룹에 대 한 배포를 삭제할 수 없습니다. 배포 기록에서 자동 삭제를 활용 하려면 잠금을 제거 해야 합니다.

## <a name="when-deployments-are-deleted"></a>배포가 삭제 된 경우

775 이상의 배포에 도달 하면 배포는 기록에서 삭제 됩니다. Azure Resource Manager은 기록이 750까지 중단 될 때까지 배포를 삭제 합니다. 가장 오래 된 배포는 항상 먼저 삭제 됩니다.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="배포 기록에서 삭제":::

> [!NOTE]
> 시작 번호 (775)와 끝 번호 (750)는 변경 될 수 있습니다.
>
> 리소스 그룹이 이미 800 제한 상태 이면 다음 배포는 오류로 인해 실패 합니다. 자동 삭제 프로세스가 즉시 시작 됩니다. 잠시 기다리면 배포를 다시 시도할 수 있습니다.

배포 외에도 [가상 작업](template-deploy-what-if.md) 을 실행 하거나 배포의 유효성을 검사할 때 삭제를 트리거합니다.

배포를 기록의 이름과 동일한 이름으로 지정 하면 기록에서 해당 위치가 다시 설정 됩니다. 배포는 최근 기록의 위치로 이동 합니다. 또한 오류가 발생 한 후 [해당 배포로 롤백하](rollback-on-error.md) 는 경우 배포 위치가 다시 설정 됩니다.

## <a name="opt-out-of-automatic-deletions"></a>자동 삭제 옵트아웃 (Opt out)

기록에서 자동 삭제를 옵트아웃 (opt out) 할 수 있습니다. **배포 기록을 직접 관리 하려는 경우에만이 옵션을 사용 합니다.** 기록에서 800 배포의 제한은 여전히 적용 됩니다. 800 배포를 초과 하는 경우 오류가 표시 되 고 배포가 실패 합니다.

자동 삭제를 사용 하지 않도록 설정 하려면 `Microsoft.Resources/DisableDeploymentGrooming` 기능 플래그를 등록 합니다. 기능 플래그를 등록할 때 전체 Azure 구독에 대 한 자동 삭제를 옵트아웃 (opt out) 할 수 있습니다. 특정 리소스 그룹에 대해서만 옵트아웃 (opt out) 할 수 없습니다. 자동 삭제를 다시 활성화 하려면 기능 플래그를 등록 취소 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell의 경우 [AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature)를 사용 합니다.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

구독의 현재 상태를 확인 하려면 다음을 사용 합니다.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

자동 삭제를 다시 활성화 하려면 Azure REST API 또는 Azure CLI를 사용 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI의 경우 [az feature register](/cli/azure/feature#az-feature-register)를 사용 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

구독의 현재 상태를 확인 하려면 다음을 사용 합니다.

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

자동 삭제를 다시 활성화 하려면 [az feature 등록 취소](/cli/azure/feature#az-feature-unregister)를 사용 합니다.

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST (영문)](#tab/rest)

REST API [기능-Register](/rest/api/resources/features/register)를 사용 합니다.

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

구독의 현재 상태를 확인 하려면 다음을 사용 합니다.

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

자동 삭제를 다시 활성화 하려면 [기능 사용-등록 취소](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>다음 단계

* 배포 기록을 보는 방법에 대 한 자세한 내용은 [Azure Resource Manager 사용 하 여 배포 기록 보기](deployment-history.md)를 참조 하세요.
