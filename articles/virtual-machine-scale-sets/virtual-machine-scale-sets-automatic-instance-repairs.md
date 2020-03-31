---
title: Azure 가상 시스템 규모 집합을 통한 자동 인스턴스 복구
description: 스케일 세트에서 VM 인스턴스에 대한 자동 복구 정책을 구성하는 방법 알아보기
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274815"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>미리 보기: Azure 가상 시스템 규모 집합에 대한 자동 인스턴스 복구

Azure 가상 시스템 크기 집합에 대한 자동 인스턴스 복구를 사용하면 정상 인스턴스 집합을 유지 관리하여 응용 프로그램의 가용성을 높은 가용성으로 얻을 수 있습니다. 배율 집합의 인스턴스가 [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 로드 [밸런서 상태 프로브에서](../load-balancer/load-balancer-custom-probe-overview.md)보고한 대로 비정상으로 판명되면 이 기능은 비정상 인스턴스를 삭제하고 이를 대체할 새 인스턴스를 만들어 인스턴스 복구를 자동으로 수행합니다.

> [!NOTE]
> 이 미리 보기 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다.

## <a name="requirements-for-using-automatic-instance-repairs"></a>자동 인스턴스 복구 사용에 대한 요구 사항

**자동 인스턴스 복구 미리 보기 선택**

REST API 또는 Azure PowerShell을 사용하여 자동 인스턴스 복구 미리 보기를 옵트인합니다. 이 단계는 미리 보기 기능에 대한 구독을 등록합니다. 이 기능은 이 기능을 사용하는 데 필요한 일회성 설정일 뿐입니다. 구독이 자동 인스턴스 복구 미리 보기에 이미 등록된 경우 다시 등록할 필요가 없습니다. 

REST API 사용 

1. 피처를 사용하여 피처 등록 [- 등록](/rest/api/resources/features/register) 

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

2. *상태가* *등록 된*으로 변경 될 때까지 몇 분 동안 기다립니다. 다음 API를 사용하여 이를 확인할 수 있습니다.

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

3. *상태가* *등록으로*변경되면 다음을 실행합니다.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Azure PowerShell 사용

1. cmdlet [레지스터를](/powershell/module/azurerm.resources/register-azurermresourceprovider) 사용하여 기능에 등록한 다음 [레지스터-AzureRmProvider기능](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. *등록상태가* 등록된 상태로 변경될 때까지 몇 분 동안 *기다립니다.* 다음 cmdlet을 사용하여 이를 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 응답은 다음과 같이해야 합니다.

| 기능 이름                           | ProviderName            | 등록 상태       |
|---------------------------------------|-------------------------|-------------------------|
| 수리VM스케일세트인스턴스미리보기      | Microsoft.Compute       | 등록됨              |

3. *등록상태가* *등록된*상태로 변경되면 다음 cmdlet을 실행합니다.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**스케일 세트에 대한 애플리케이션 상태 모니터링 지원**

규모 집합에는 인스턴스에 대한 응용 프로그램 상태 모니터링이 활성화되어 있어야 합니다. 이 작업은 응용 [프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [로드 밸러서 상태 프로브를](../load-balancer/load-balancer-custom-probe-overview.md)사용하여 수행할 수 있습니다. 이 중 하나만 한 번에 활성화할 수 있습니다. 응용 프로그램 상태 확장 또는 로드 밸러버 프로브는 가상 시스템 인스턴스에서 구성된 응용 프로그램 끝점을 ping하여 응용 프로그램 상태 상태를 확인합니다. 이 상태는 규모 집합 오케스트레이터에서 인스턴스 상태를 모니터링하고 필요한 경우 복구를 수행하는 데 사용됩니다.

**상태 상태를 제공하도록 끝점 구성**

자동 인스턴스 복구 정책을 사용하도록 설정하기 전에 확장 집합 인스턴스에 응용 프로그램 상태 상태를 내보도록 구성된 응용 프로그램 끝점이 있는지 확인합니다. 인스턴스가 이 응용 프로그램 끝점에서 상태 200(확인)을 반환하면 인스턴스가 "정상"으로 표시됩니다. 다른 모든 경우 인스턴스는 다음 시나리오를 포함하여 "비정상"으로 표시됩니다.

- 가상 시스템 인스턴스 내에 응용 프로그램 상태 상태를 제공하도록 구성된 응용 프로그램 끝점이 없는 경우
- 응용 프로그램 끝점이 잘못 구성된 경우
- 응용 프로그램 끝점에 연결할 수 없는 경우

"비정상"으로 표시된 인스턴스의 경우 배율 집합에 의해 자동 복구가 트리거됩니다. 끝점이 구성되는 동안 의도하지 않은 인스턴스 복구를 방지하기 위해 자동 복구 정책을 사용하도록 설정하기 전에 응용 프로그램 끝점이 올바르게 구성되었는지 확인합니다.

**단일 배치 그룹 사용**

이 미리 보기는 현재 단일 배치 그룹으로 배포된 축척 집합에만 사용할 수 있습니다. 속성 *singlePlacementGroup* 자동 인스턴스 복구 기능을 사용 하 여 축척 집합에 대 한 *true로* 설정 해야 합니다. [게재위치 그룹에](./virtual-machine-scale-sets-placement-groups.md#placement-groups)대해 자세히 알아보세요.

**API 버전**

자동 복구 정책은 2018-10-01 이상의 계산 API 버전에 대해 지원됩니다.

**리소스 또는 구독 이동 제한**

이 미리 보기의 일부로 자동 복구 정책이 활성화된 경우 현재 확장 집합에 대해 리소스 또는 구독 이동이 지원되지 않습니다.

**서비스 패브릭 스케일 세트 제한**

이 미리 보기 기능은 현재 서비스 패브릭 스케일 집합에 대해 지원되지 않습니다.

## <a name="how-do-automatic-instance-repairs-work"></a>자동 인스턴스 복구는 어떻게 작동합니까?

자동 인스턴스 복구 기능은 축척 집합의 개별 인스턴스의 상태 모니터링에 의존합니다. 확장 집합의 VM 인스턴스는 응용 프로그램 [상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [로드 밸러버 상태 프로브를](../load-balancer/load-balancer-custom-probe-overview.md)사용하여 응용 프로그램 상태 상태를 내보도록 구성할 수 있습니다. 인스턴스가 비정상으로 판명되면 배율 집합은 비정상 인스턴스를 삭제하고 이를 대체할 새 인스턴스를 만들어 복구 작업을 수행합니다. 이 기능은 *자동RepairsPolicy 개체를* 사용하여 가상 시스템 스케일 집합 모델에서 사용할 수 있습니다.

### <a name="batching"></a>일괄 처리

자동 인스턴스 복구 작업은 일괄 처리로 수행됩니다. 지정된 시간에 스케일 집합의 인스턴스 중 5% 이하는 자동 수리 정책을 통해 복구됩니다. 이렇게 하면 동시에 비정상으로 발견되는 경우 많은 수의 인스턴스를 동시에 삭제하고 다시 만드는 것을 방지할 수 있습니다.

### <a name="grace-period"></a>유예 기간

인스턴스가 SCALE 집합에서 수행된 PUT, PATCH 또는 POST 작업(예: 이미지 다시 이미지, 재배포, 업데이트 등)으로 인해 상태 변경 작업을 거치면 해당 인스턴스의 모든 복구 작업은 유예 기간을 기다린 후에만 수행됩니다. 유예 기간은 인스턴스가 정상 상태로 돌아갈 수 있도록 하는 시간입니다. 유예 기간은 상태 변경이 완료된 후 시작됩니다. 이렇게 하면 조기 또는 우발적인 수리 작업을 방지할 수 있습니다. 유예 기간은 축척 집합에서 새로 생성된 인스턴스(수리 작업의 결과로 생성된 인스턴스 포함)에 대해 적용됩니다. 유예 기간은 ISO 8601 형식으로 분 단위로 지정되며 속성 *자동RepairsPolicy.gracePeriod를*사용하여 설정할 수 있습니다. 유예 기간은 30분에서 90분 사이이며 기본값은 30분입니다.

자동 인스턴스 복구 프로세스는 다음과 같이 작동합니다.

1. [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [로드 밸런서 상태 프로브는](../load-balancer/load-balancer-custom-probe-overview.md) 스케일 세트의 각 가상 시스템 내부의 응용 프로그램 끝점을 ping하여 각 인스턴스에 대한 응용 프로그램 상태 상태를 가져옵니다.
2. 끝점이 상태 200(OK)으로 응답하면 인스턴스가 "정상"으로 표시됩니다. 다른 모든 경우(끝점에 연결할 수 없는 경우 포함)에서 인스턴스는 "비정상"으로 표시됩니다.
3. 인스턴스가 비정상으로 판명되면 크기 조정 집합은 비정상 인스턴스를 삭제하고 새 인스턴스를 만들어 복구 작업을 트리거합니다.
4. 인스턴스 복구는 일괄 처리로 수행됩니다. 지정된 시간에 스케일 집합의 총 인스턴스 중 5% 이하가 복구됩니다. 규모 집합에 인스턴스가 20개 미만인 경우 한 번에 하나의 비정상 인스턴스에 대해 복구가 수행됩니다.
5. 위의 프로세스는 스케일 집합의 모든 비정상 인스턴스가 복구될 때까지 계속됩니다.

## <a name="instance-protection-and-automatic-repairs"></a>인스턴스 보호 및 자동 수리

축척 집합의 인스턴스가 확장 집합 *[작업 보호 정책으로부터 보호를](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* 적용하여 보호되는 경우 해당 인스턴스에서 자동 복구가 수행되지 않습니다.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>새 축척 세트를 만들 때 자동 복구 정책 사용

새 축척 집합을 만드는 동안 자동 수리 정책을 사용하도록 설정하려면 이 기능에 옵트인하기 위한 모든 [요구 사항이](#requirements-for-using-automatic-instance-repairs) 충족되었는지 확인합니다. 끝점이 구성되는 동안 의도하지 않은 복구가 트리거되지 않도록 확장 집합 인스턴스에 대해 응용 프로그램 끝점을 올바르게 구성해야 합니다. 새로 생성된 축척 집합의 경우 모든 인스턴스 복구는 유예 기간 동안 기다린 후에만 수행됩니다. 축척 집합에서 자동 인스턴스 복구를 사용하려면 가상 시스템 축척 집합 모델에서 *자동수리정책* 개체를 사용합니다.

### <a name="rest-api"></a>REST API

다음 예제에서는 축척 집합 모델에서 자동 인스턴스 복구를 활성화하는 방법을 보여 주습니다. API 버전 2018-10-01 이상에서 사용합니다.

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

[새 AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet을 사용하여 새 축척 세트를 만드는 동안 자동 인스턴스 복구 기능을 사용할 수 있습니다. 이 샘플 스크립트는 구성 파일: [전체 가상 시스템 축척 집합 만들기를](./scripts/powershell-sample-create-complete-scale-set.md)사용하여 축척 집합 및 관련 리소스를 만드는 데 대해 살펴봅니다. [자동 *복구]* 및 *자동 수리GracePeriod* 매개 변수를 구성 개체에 추가하여 자동 인스턴스 복구 정책을 구성할 수 있습니다. 다음 예제에서는 30분의 유예 기간을 가진 기능을 활성화합니다.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>기존 축척 세트를 업데이트할 때 자동 복구 정책 사용

기존 축척 집합에서 자동 수리 정책을 사용하도록 설정하기 전에 이 기능에 옵트인하기 위한 모든 [요구 사항이](#requirements-for-using-automatic-instance-repairs) 충족되었는지 확인합니다. 끝점이 구성되는 동안 의도하지 않은 복구가 트리거되지 않도록 확장 집합 인스턴스에 대해 응용 프로그램 끝점을 올바르게 구성해야 합니다. 축척 집합에서 자동 인스턴스 복구를 사용하려면 가상 시스템 축척 집합 모델에서 *자동수리정책* 개체를 사용합니다.

### <a name="rest-api"></a>REST API

다음 예제는 40분의 유예 기간을 가진 정책을 활성화합니다. API 버전 2018-10-01 이상에서 사용합니다.

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

[Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet을 사용하여 기존 축척 집합에서 자동 인스턴스 복구 피쳐의 구성을 수정합니다. 다음 예제는 유예 기간을 40분으로 업데이트합니다.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>문제 해결

**자동 복구 정책을 사용하도록 설정하지 못했습니다.**

'속성' 형식의 개체에서 '자동 수리 정책'이라는 메시지가 있는 'BadRequest' 오류가 발생하면 가상 시스템 규모 집합에 사용되는 API 버전을 확인합니다. 이 기능에는 API 버전 2018-10-01 이상의 기능이 필요합니다.

**정책이 활성화된 경우에도 인스턴스가 복구되지 않음**

인스턴스는 유예 기간에 있을 수 있습니다. 복구를 수행하기 전에 인스턴스의 상태가 변경된 후 대기하는 시간입니다. 이는 조기 또는 우발적인 수리를 피하기 위한 것입니다. 인스턴스에 대한 유예 기간이 완료되면 복구 작업이 수행되어야 합니다.

**스케일 세트 인스턴스에 대한 응용 프로그램 상태 보기**

가상 시스템 규모 집합의 인스턴스에 [대해 인스턴스 보기 받기 API를](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) 사용하여 응용 프로그램 상태 상태를 볼 수 있습니다. Azure PowerShell을 사용하면 *-InstanceView* 플래그를 사용하여 cmdlet [Get-AzVmssVM을](/powershell/module/az.compute/get-azvmssvm) 사용할 수 있습니다. 응용 프로그램 상태는 속성 *vmHealth*에서 제공됩니다.

## <a name="next-steps"></a>다음 단계

스케일 세트에 대한 [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 로드 [밸러저 상태 프로브를](../load-balancer/load-balancer-custom-probe-overview.md) 구성하는 방법에 대해 알아봅니다.
