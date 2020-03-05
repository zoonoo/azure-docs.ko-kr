---
title: Azure 가상 머신 확장 집합을 사용 하 여 자동 인스턴스 복구
description: 확장 집합의 VM 인스턴스에 대 한 자동 복구 정책을 구성 하는 방법에 대해 알아봅니다.
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274815"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>미리 보기: Azure 가상 머신 확장 집합에 대 한 자동 인스턴스 복구

Azure 가상 머신 확장 집합에 대 한 자동 인스턴스 복구를 사용 하도록 설정 하면 정상 인스턴스 집합을 유지 관리 하 여 응용 프로그램에 대 한 고가용성을 달성할 수 있습니다. [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)에서 보고 한 대로 확장 집합의 인스턴스가 비정상으로 확인 되는 경우이 기능은 비정상 인스턴스를 삭제 하 고 새 인스턴스를 만들어 자동으로 인스턴스 복구를 수행 합니다.

> [!NOTE]
> 이 미리 보기 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다.

## <a name="requirements-for-using-automatic-instance-repairs"></a>자동 인스턴스 복구를 사용 하기 위한 요구 사항

**자동 인스턴스 복구 미리 보기를 옵트인 (Opt in)**

REST API 또는 Azure PowerShell를 사용 하 여 자동 인스턴스 복구 미리 보기를 옵트인 (opt in) 합니다. 이러한 단계는 미리 보기 기능에 대 한 구독을 등록 합니다. 이 기능은이 기능을 사용 하는 데 필요한 일회성 설정입니다. 구독이 자동 인스턴스 복구 미리 보기에 이미 등록 된 경우에는 다시 등록할 필요가 없습니다. 

REST API 사용 

1. 기능을 사용 하 여 기능 등록 [-등록](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. *상태가* *등록*됨으로 변경 될 때까지 몇 분 정도 기다립니다. 다음 API를 사용 하 여이를 확인할 수 있습니다.

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. *상태가* *등록*됨으로 변경 되 면 다음을 실행 합니다.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Azure PowerShell 사용

1. Cmdlet [register-azurermresourceprovider](/powershell/module/azurerm.resources/register-azurermresourceprovider) 뒤에 [register-azurermproviderfeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) 를 사용 하 여 기능에 등록 합니다.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. *Registrationstate* 가 *등록*됨으로 변경 될 때까지 몇 분 정도 기다립니다. 다음 cmdlet을 사용 하 여이를 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 응답은 다음과 같아야 합니다.

| FeatureName                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | 등록됨              |

3. *Registrationstate* 가 *등록*됨으로 변경 되 면 다음 cmdlet을 실행 합니다.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**확장 집합에 응용 프로그램 상태 모니터링 사용**

확장 집합에는 사용 하도록 설정 된 인스턴스에 대 한 응용 프로그램 상태 모니터링이 있어야 합니다. [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)를 사용 하 여이 작업을 수행할 수 있습니다. 한 번에 하나만 사용할 수 있습니다. 응용 프로그램 상태 확장 또는 부하 분산 장치 프로브는 가상 머신 인스턴스에 구성 된 응용 프로그램 엔드포인트를 ping 하 여 응용 프로그램 상태를 확인 합니다. 이 상태는 확장 집합 orchestrator에서 인스턴스 상태를 모니터링 하 고 필요한 경우 복구를 수행 하는 데 사용 됩니다.

**상태를 제공 하도록 끝점 구성**

자동 인스턴스 복구 정책을 사용 하도록 설정 하기 전에 확장 집합 인스턴스에 응용 프로그램 상태를 내보내도록 구성 된 응용 프로그램 끝점이 있는지 확인 합니다. 인스턴스가이 응용 프로그램 끝점에 상태 200 (OK)를 반환 하면 인스턴스는 "정상"으로 표시 됩니다. 다른 모든 경우에는 다음 시나리오를 포함 하 여 인스턴스가 "비정상"으로 표시 됩니다.

- 응용 프로그램 상태를 제공 하기 위해 가상 머신 인스턴스 내에 구성 된 응용 프로그램 끝점이 없는 경우
- 응용 프로그램 끝점이 잘못 구성 된 경우
- 응용 프로그램 끝점에 연결할 수 없는 경우

"비정상"으로 표시 된 인스턴스의 경우 자동 복구는 확장 집합에 의해 트리거됩니다. 끝점을 구성 하는 동안 의도 하지 않은 인스턴스 복구를 방지 하기 위해 자동 복구 정책을 사용 하기 전에 응용 프로그램 끝점이 올바르게 구성 되었는지 확인 합니다.

**단일 배치 그룹 사용**

이 미리 보기는 현재 단일 배치 그룹으로 배포 된 확장 집합에 대해서만 사용할 수 있습니다. 자동 인스턴스 복구 기능을 사용 하려면 확장 집합에 대해 *Singleyour Ementgroup* 속성을 *true* 로 설정 해야 합니다. [배치 그룹](./virtual-machine-scale-sets-placement-groups.md#placement-groups)에 대해 자세히 알아보세요.

**API 버전**

자동 복구 정책은 계산 API 버전 2018-10-01 이상에서 지원 됩니다.

**리소스 또는 구독 이동에 대 한 제한**

자동 복구 정책을 사용 하도록 설정한 경우이 미리 보기의 일부로 리소스 또는 구독 이동은 현재 확장 집합에 대해 지원 되지 않습니다.

**Service fabric 확장 집합에 대 한 제한**

이 미리 보기 기능은 현재 service fabric 확장 집합에 대해 지원 되지 않습니다.

## <a name="how-do-automatic-instance-repairs-work"></a>자동 인스턴스 복구는 어떻게 작동 하나요?

자동 인스턴스 복구 기능은 확장 집합에 있는 개별 인스턴스의 상태 모니터링에 의존 합니다. [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)를 사용 하 여 응용 프로그램 상태를 내보내도록 확장 집합의 VM 인스턴스를 구성할 수 있습니다. 인스턴스가 비정상 인 것으로 확인 되 면 확장 집합은 비정상 인스턴스를 삭제 하 고 새 인스턴스를 만들어 대체 하는 방법으로 복구 작업을 수행 합니다. *AutomaticRepairsPolicy* 개체를 사용 하 여 가상 머신 확장 집합 모델에서이 기능을 사용 하도록 설정할 수 있습니다.

### <a name="batching"></a>일괄 처리

자동 인스턴스 복구 작업은 일괄 처리로 수행 됩니다. 지정 된 시간에 확장 집합에 있는 인스턴스의 5%는 자동 복구 정책을 통해 복구 됩니다. 이렇게 하면 동시에 비정상 상태를 발견 한 경우 많은 수의 인스턴스를 동시에 삭제 하 고 다시 만들지 않아도 됩니다.

### <a name="grace-period"></a>유예 기간

확장 집합에서 수행 되는 PUT, PATCH 또는 POST 작업으로 인해 인스턴스가 상태 변경 작업을 수행 하는 경우 (예: 이미지로 다시 설치, 재배포, 업데이트 등) 해당 인스턴스에 대 한 모든 복구 동작은 유예 기간을 기다린 후에만 수행 됩니다. 유예 기간은 인스턴스가 정상 상태로 돌아갈 수 있도록 하는 시간입니다. 유예 기간은 상태 변경이 완료 된 후에 시작 됩니다. 이렇게 하면 중간 또는 실수로 복구 작업을 방지할 수 있습니다. 이 유예 기간은 확장 집합에서 새로 생성 된 모든 인스턴스에 적용 됩니다 (복구 작업의 결과로 만들어진 항목 포함). 유예 기간은 ISO 8601 형식에서 분 단위로 지정 되며 *automaticRepairsPolicy. gracePeriod*속성을 사용 하 여 설정할 수 있습니다. 유예 기간은 30 분에서 90 분 사이로 설정할 수 있으며 기본값은 30 분입니다.

자동 인스턴스 복구 프로세스는 다음과 같이 작동 합니다.

1. [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md) 는 확장 집합의 각 가상 머신 내에서 응용 프로그램 엔드포인트를 ping 하 여 각 인스턴스에 대 한 응용 프로그램 상태를 가져옵니다.
2. 끝점이 상태 200 (OK)를 사용 하 여 응답 하는 경우 인스턴스는 "정상"으로 표시 됩니다. 다른 모든 경우 (끝점에 연결할 수 없는 경우 포함)에는 인스턴스가 "비정상"으로 표시 됩니다.
3. 인스턴스가 비정상 인 것으로 확인 되 면 확장 집합은 비정상 인스턴스를 삭제 하 고 새 인스턴스를 만들어 대체 하는 방법으로 복구 작업을 트리거합니다.
4. 인스턴스 복구는 일괄 처리로 수행 됩니다. 지정 된 시간에 확장 집합의 총 인스턴스 중 5%는 복구 되지 않습니다. 크기 집합의 인스턴스 수가 20 개 미만이 면 한 번에 하나의 비정상 인스턴스에 대 한 복구가 수행 됩니다.
5. 위의 프로세스는 확장 집합의 모든 비정상 인스턴스가 복구 될 때까지 계속 됩니다.

## <a name="instance-protection-and-automatic-repairs"></a>인스턴스 보호 및 자동 복구

크기 집합 *[작업 보호 정책을](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* 적용 하 여 확장 집합의 인스턴스를 보호 하는 경우 해당 인스턴스에서 자동 복구가 수행 되지 않습니다.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>새 확장 집합을 만들 때 자동 복구 정책을 사용 하도록 설정

새 확장 집합을 만드는 동안 자동 복구 정책을 사용 하도록 설정 하려면이 기능을 사용 하기 위한 모든 [요구 사항이](#requirements-for-using-automatic-instance-repairs) 충족 되는지 확인 합니다. 끝점을 구성 하는 동안 의도 하지 않은 복구가 트리거되지 않도록 하려면 확장 집합 인스턴스에 대해 응용 프로그램 끝점을 올바르게 구성 해야 합니다. 새로 만든 크기 집합의 경우 유예 기간 동안 기다린 후에만 인스턴스 복구가 수행 됩니다. 크기 집합에서 자동 인스턴스 복구를 사용 하도록 설정 하려면 가상 머신 확장 집합 모델에서 *automaticRepairsPolicy* 개체를 사용 합니다.

### <a name="rest-api"></a>REST API

다음 예에서는 확장 집합 모델에서 자동 인스턴스 복구를 사용 하도록 설정 하는 방법을 보여 줍니다. API 버전 2018-10-01 이상을 사용 합니다.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

[AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet을 사용 하 여 새 확장 집합을 만드는 동안 자동 인스턴스 복구 기능을 사용 하도록 설정할 수 있습니다. 이 샘플 스크립트는 구성 파일을 사용 하 여 확장 집합 및 연결 된 리소스를 만드는 과정을 안내 합니다. [전체 가상 머신 확장 집합을 만듭니다](./scripts/powershell-sample-create-complete-scale-set.md). 확장 집합을 만들기 위한 구성 개체에 *Enableautomaticrepair* 및 *AutomaticRepairGracePeriod* 매개 변수를 추가 하 여 자동 인스턴스 복구 정책을 구성할 수 있습니다. 다음 예에서는 유예 기간이 30 분인 기능을 사용 하도록 설정 합니다.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>기존 확장 집합을 업데이트할 때 자동 복구 정책을 사용 하도록 설정

기존 확장 집합에서 자동 복구 정책을 사용 하도록 설정 하기 전에이 기능을 옵트인 하기 위한 모든 [요구 사항이](#requirements-for-using-automatic-instance-repairs) 충족 되는지 확인 합니다. 끝점을 구성 하는 동안 의도 하지 않은 복구가 트리거되지 않도록 하려면 확장 집합 인스턴스에 대해 응용 프로그램 끝점을 올바르게 구성 해야 합니다. 크기 집합에서 자동 인스턴스 복구를 사용 하도록 설정 하려면 가상 머신 확장 집합 모델에서 *automaticRepairsPolicy* 개체를 사용 합니다.

### <a name="rest-api"></a>REST API

다음 예에서는 유예 기간이 40 분인 정책을 사용 하도록 설정 합니다. API 버전 2018-10-01 이상을 사용 합니다.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

[AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet을 사용 하 여 기존 확장 집합에서 자동 인스턴스 복구 기능의 구성을 수정할 수 있습니다. 다음 예에서는 유예 기간을 40 분으로 업데이트 합니다.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>문제 해결

**자동 복구 정책을 사용 하도록 설정 하지 못했습니다.**

"' Properties ' 형식의 개체에서 ' automaticRepairsPolicy ' 멤버를 찾을 수 없습니다." 라는 메시지와 함께 ' BadRequest ' 오류가 표시 되 면 가상 머신 확장 집합에 사용 되는 API 버전을 확인 합니다. 이 기능을 수행 하려면 API 버전 2018-10-01 이상이 필요 합니다.

**정책이 설정 된 경우에도 인스턴스가 복구 되지 않습니다.**

인스턴스는 유예 기간 내에 있을 수 있습니다. 복구를 수행 하기 전에 인스턴스의 상태를 변경한 후 대기 하는 시간입니다. 이는 중간에 또는 우발적으로 복구 하는 것을 방지 하기 위한 것입니다. 인스턴스에 대 한 유예 기간이 완료 되 면 복구 작업이 수행 됩니다.

**확장 집합 인스턴스의 응용 프로그램 상태 보기**

가상 머신 확장 집합의 인스턴스에 대 한 [인스턴스 뷰 가져오기 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) 를 사용 하 여 응용 프로그램 상태를 볼 수 있습니다. Azure PowerShell에서 *-instanceview* 플래그와 함께 [AzVmssVM](/powershell/module/az.compute/get-azvmssvm) cmdlet을 사용할 수 있습니다. 응용 프로그램 상태는 *vmhealth*속성에 제공 됩니다.

## <a name="next-steps"></a>다음 단계

확장 집합에 대 한 [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md) 를 구성 하는 방법에 대해 알아봅니다.
