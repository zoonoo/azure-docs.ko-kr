---
title: Azure 가상 머신 확장 집합을 사용 하 여 자동 인스턴스 복구
description: 확장 집합의 VM 인스턴스에 대 한 자동 복구 정책을 구성 하는 방법에 대해 알아봅니다.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ae508754775d4eb622d8e91ef58eb0d6e1c45692
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94889017"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합에 대한 자동 인스턴스 복구

Azure 가상 머신 확장 집합에 대 한 자동 인스턴스 복구를 사용 하도록 설정 하면 정상 인스턴스 집합을 유지 관리 하 여 응용 프로그램에 대 한 고가용성을 달성할 수 있습니다. [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)에서 보고 한 대로 확장 집합의 인스턴스가 비정상으로 확인 되는 경우이 기능은 비정상 인스턴스를 삭제 하 고 새 인스턴스를 만들어 자동으로 인스턴스 복구를 수행 합니다.

## <a name="requirements-for-using-automatic-instance-repairs"></a>자동 인스턴스 복구를 사용 하기 위한 요구 사항

**확장 집합에 응용 프로그램 상태 모니터링 사용**

확장 집합에는 사용 하도록 설정 된 인스턴스에 대 한 응용 프로그램 상태 모니터링이 있어야 합니다. [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)를 사용 하 여이 작업을 수행할 수 있습니다. 한 번에 하나만 사용할 수 있습니다. 응용 프로그램 상태 확장 또는 부하 분산 장치 프로브는 가상 머신 인스턴스에 구성 된 응용 프로그램 엔드포인트를 ping 하 여 응용 프로그램 상태를 확인 합니다. 이 상태는 확장 집합 orchestrator에서 인스턴스 상태를 모니터링 하 고 필요한 경우 복구를 수행 하는 데 사용 됩니다.

**상태를 제공 하도록 끝점 구성**

자동 인스턴스 복구 정책을 사용 하도록 설정 하기 전에 확장 집합 인스턴스에 응용 프로그램 상태를 내보내도록 구성 된 응용 프로그램 끝점이 있는지 확인 합니다. 인스턴스가이 응용 프로그램 끝점에 상태 200 (OK)를 반환 하면 인스턴스는 "정상"으로 표시 됩니다. 다른 모든 경우에는 다음 시나리오를 포함 하 여 인스턴스가 "비정상"으로 표시 됩니다.

- 응용 프로그램 상태를 제공 하기 위해 가상 머신 인스턴스 내에 구성 된 응용 프로그램 끝점이 없는 경우
- 응용 프로그램 끝점이 잘못 구성 된 경우
- 응용 프로그램 끝점에 연결할 수 없는 경우

"비정상"으로 표시 된 인스턴스의 경우 자동 복구는 확장 집합에 의해 트리거됩니다. 끝점을 구성 하는 동안 의도 하지 않은 인스턴스 복구를 방지 하기 위해 자동 복구 정책을 사용 하기 전에 응용 프로그램 끝점이 올바르게 구성 되었는지 확인 합니다.

**확장 집합의 최대 인스턴스 수**

이 기능은 현재 최대 200 인스턴스를 포함 하는 확장 집합에 대해서만 사용할 수 있습니다. 확장 집합은 단일 배치 그룹 또는 다중 배치 그룹으로 배포할 수 있지만 확장 집합에 대해 자동 인스턴스 복구가 사용 되는 경우에는 인스턴스 수가 200을 초과 하면 안 됩니다.

**API 버전**

자동 복구 정책은 계산 API 버전 2018-10-01 이상에서 지원 됩니다.

**리소스 또는 구독 이동에 대 한 제한**

자동 복구 기능을 사용 하도록 설정한 경우에는 현재 리소스 또는 구독 이동이 확장 집합에 대해 지원 되지 않습니다.

**Service fabric 확장 집합에 대 한 제한**

이 기능은 현재 service fabric 확장 집합에 대해 지원 되지 않습니다.

## <a name="how-do-automatic-instance-repairs-work"></a>자동 인스턴스 복구는 어떻게 작동 하나요?

자동 인스턴스 복구 기능은 확장 집합에 있는 개별 인스턴스의 상태 모니터링에 의존 합니다. [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)를 사용 하 여 응용 프로그램 상태를 내보내도록 확장 집합의 VM 인스턴스를 구성할 수 있습니다. 인스턴스가 비정상 인 것으로 확인 되 면 확장 집합은 비정상 인스턴스를 삭제 하 고 새 인스턴스를 만들어 대체 하는 방법으로 복구 작업을 수행 합니다. 최신 가상 머신 확장 집합 모델을 사용 하 여 새 인스턴스를 만듭니다. *AutomaticRepairsPolicy* 개체를 사용 하 여 가상 머신 확장 집합 모델에서이 기능을 사용 하도록 설정할 수 있습니다.

### <a name="batching"></a>일괄 처리

자동 인스턴스 복구 작업은 일괄 처리로 수행 됩니다. 지정 된 시간에 확장 집합에 있는 인스턴스의 5%는 자동 복구 정책을 통해 복구 됩니다. 이렇게 하면 동시에 비정상 상태를 발견 한 경우 많은 수의 인스턴스를 동시에 삭제 하 고 다시 만들지 않아도 됩니다.

### <a name="grace-period"></a>유예 기간

확장 집합에서 수행 되는 PUT, PATCH 또는 POST 작업으로 인해 인스턴스가 상태 변경 작업을 수행 하는 경우 (예: 이미지로 다시 설치, 재배포, 업데이트 등) 해당 인스턴스에 대 한 모든 복구 동작은 유예 기간을 기다린 후에만 수행 됩니다. 유예 기간은 인스턴스가 정상 상태로 돌아갈 수 있도록 하는 시간입니다. 유예 기간은 상태 변경이 완료 된 후에 시작 됩니다. 이렇게 하면 중간 또는 실수로 복구 작업을 방지할 수 있습니다. 이 유예 기간은 확장 집합에서 새로 생성 된 모든 인스턴스에 적용 됩니다 (복구 작업의 결과로 만들어진 항목 포함). 유예 기간은 ISO 8601 형식에서 분 단위로 지정 되며 *automaticRepairsPolicy. gracePeriod* 속성을 사용 하 여 설정할 수 있습니다. 유예 기간은 30 분에서 90 분 사이로 설정할 수 있으며 기본값은 30 분입니다.

### <a name="suspension-of-repairs"></a>복구 일시 중단 

가상 머신 확장 집합은 필요한 경우 자동 인스턴스 복구를 일시적으로 일시 중단 하는 기능을 제공 합니다. 가상 머신 확장 집합 인스턴스 보기의 *orchestrationServices* 속성에서 자동 복구의 *servicestate* 는 자동 복구의 현재 상태를 표시 합니다. 확장 집합이 자동 복구로 옵트인 되 면 매개 변수 *Servicestate* 의 값이 *실행 중* 으로 설정 됩니다. 크기 집합에 대 한 자동 복구가 일시 중단 되 면 *Servicestate* 매개 변수가 *suspended* 로 설정 됩니다. *AutomaticRepairsPolicy* 가 확장 집합에 정의 되어 있지만 자동 복구 기능이 사용 하도록 설정 되지 않은 경우 *Servicestate* 매개 변수가 *실행 중이 아닌* 것으로 설정 됩니다.

확장 집합에서 비정상 상태를 바꾸기 위해 새로 만든 인스턴스가 계속 해 서 복구 작업을 수행한 후에도 비정상 상태를 유지 하는 경우, 보안 조치로 플랫폼에서 자동 복구가 *일시 중단* 되도록 *servicestate* 를 업데이트 합니다. 자동 복구를 *실행* 하는 데 필요한 *servicestate* 값을 설정 하 여 자동 복구를 다시 시작할 수 있습니다. 자세한 지침은 확장 집합에 대 한 [자동 복구 정책의 서비스 상태를 보고 업데이트 하](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) 는 방법 섹션에 나와 있습니다. 

자동 인스턴스 복구 프로세스는 다음과 같이 작동 합니다.

1. [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md) 는 확장 집합의 각 가상 머신 내에서 응용 프로그램 엔드포인트를 ping 하 여 각 인스턴스에 대 한 응용 프로그램 상태를 가져옵니다.
2. 끝점이 상태 200 (OK)를 사용 하 여 응답 하는 경우 인스턴스는 "정상"으로 표시 됩니다. 다른 모든 경우 (끝점에 연결할 수 없는 경우 포함)에는 인스턴스가 "비정상"으로 표시 됩니다.
3. 인스턴스가 비정상 인 것으로 확인 되 면 확장 집합은 비정상 인스턴스를 삭제 하 고 새 인스턴스를 만들어 대체 하는 방법으로 복구 작업을 트리거합니다.
4. 인스턴스 복구는 일괄 처리로 수행 됩니다. 지정 된 시간에 확장 집합의 총 인스턴스 중 5%는 복구 되지 않습니다. 크기 집합의 인스턴스 수가 20 개 미만이 면 한 번에 하나의 비정상 인스턴스에 대 한 복구가 수행 됩니다.
5. 위의 프로세스는 확장 집합의 모든 비정상 인스턴스가 복구 될 때까지 계속 됩니다.

## <a name="instance-protection-and-automatic-repairs"></a>인스턴스 보호 및 자동 복구

[보호 정책](./virtual-machine-scale-sets-instance-protection.md)중 하나를 적용 하 여 확장 집합의 인스턴스를 보호 하는 경우 해당 인스턴스에서 자동 복구가 수행 되지 않습니다. 이는 *규모 확장 으로부터 보호* 하 고 *확장 집합 작업에서* 보호 하는 보호 정책 모두에 적용 됩니다. 

## <a name="terminatenotificationandautomaticrepairs"></a>종료 알림 및 자동 복구

확장 집합에 대해 [종료 알림](./virtual-machine-scale-sets-terminate-notification.md) 기능을 사용 하는 경우 자동 복구 작업 중에 비정상 인스턴스 삭제는 종료 알림 구성을 따릅니다. 종료 알림은 Azure metadata service-예약 된 이벤트를 통해 전송 되며, 구성 된 지연 시간 제한 기간 동안 인스턴스 삭제가 지연 됩니다. 그러나 비정상 항목을 대체 하기 위해 새 인스턴스를 만드는 경우 지연 시간 제한이 완료 될 때까지 기다리지 않습니다.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>새 확장 집합을 만들 때 자동 복구 정책을 사용 하도록 설정

새 확장 집합을 만드는 동안 자동 복구 정책을 사용 하도록 설정 하려면이 기능을 사용 하기 위한 모든 [요구 사항이](#requirements-for-using-automatic-instance-repairs) 충족 되는지 확인 합니다. 끝점을 구성 하는 동안 의도 하지 않은 복구가 트리거되지 않도록 하려면 확장 집합 인스턴스에 대해 응용 프로그램 끝점을 올바르게 구성 해야 합니다. 새로 만든 크기 집합의 경우 유예 기간 동안 기다린 후에만 인스턴스 복구가 수행 됩니다. 크기 집합에서 자동 인스턴스 복구를 사용 하도록 설정 하려면 가상 머신 확장 집합 모델에서 *automaticRepairsPolicy* 개체를 사용 합니다.

이 [빠른 시작 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) 사용 하 여 가상 머신 확장 집합을 배포 하 고, 부하 분산 장치 상태 프로브와 자동 인스턴스 복구가 30 분의 유예 기간으로 활성화 되도록 할 수도 있습니다.

### <a name="azure-portal"></a>Azure portal
 
새 확장 집합을 만들 때 자동 복구 정책을 설정 하는 단계는 다음과 같습니다.
 
1. **가상 머신 확장 집합** 으로 이동 합니다.
1. **+ 추가** 를 선택 하 여 새 확장 집합을 만듭니다.
1. **상태** 탭으로 이동 합니다. 
1. **상태** 섹션을 찾습니다.
1. **응용 프로그램 상태 모니터링** 옵션을 사용 하도록 설정 합니다.
1. **자동 복구 정책** 섹션을 찾습니다.
1. **자동 복구** 옵션 **을 설정 합니다** .
1. **유예 기간 (분)** 에서 유예 기간 (분)을 지정 합니다. 허용 되는 값은 30 분에서 90 분 사이입니다. 
1. 새 확장 집합 만들기를 완료 한 후 **검토 + 만들기** 단추를 선택 합니다.

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

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음 예에서는 *[az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* 를 사용 하 여 새 크기 집합을 만드는 동안 자동 복구 정책을 사용 하도록 설정 합니다. 먼저 리소스 그룹을 만든 다음 자동 복구 정책 유예 기간이 30 분으로 설정 된 새 확장 집합을 만듭니다.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

위의 예에서는 기존 부하 분산 장치 및 상태 프로브를 사용 하 여 인스턴스의 응용 프로그램 상태를 모니터링 합니다. 대신 응용 프로그램 상태 확장을 모니터링에 사용 하려는 경우에는 확장 집합을 만들고, 응용 프로그램 상태 확장을 구성한 다음, 다음 섹션에 설명 된 대로 *az vmss 업데이트* 를 사용 하 여 자동 인스턴스 복구 정책을 사용 하도록 설정할 수 있습니다.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>기존 확장 집합을 업데이트할 때 자동 복구 정책을 사용 하도록 설정

기존 확장 집합에서 자동 복구 정책을 사용 하도록 설정 하기 전에이 기능을 옵트인 하기 위한 모든 [요구 사항이](#requirements-for-using-automatic-instance-repairs) 충족 되는지 확인 합니다. 끝점을 구성 하는 동안 의도 하지 않은 복구가 트리거되지 않도록 하려면 확장 집합 인스턴스에 대해 응용 프로그램 끝점을 올바르게 구성 해야 합니다. 크기 집합에서 자동 인스턴스 복구를 사용 하도록 설정 하려면 가상 머신 확장 집합 모델에서 *automaticRepairsPolicy* 개체를 사용 합니다.

기존 확장 집합의 모델을 업데이트 한 후에는 최신 모델이 모든 규모의 인스턴스에 적용 되는지 확인 합니다. [최신 확장 집합 모델을 사용 하 여 vm을 최신 상태로 전환 하는 방법](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)에 대 한 지침을 참조 하세요.

### <a name="azure-portal"></a>Azure portal

Azure Portal를 통해 기존 확장 집합의 자동 복구 정책을 수정할 수 있습니다. 
 
1. 기존 가상 머신 확장 집합으로 이동합니다.
1. 왼쪽 메뉴의 **설정** 에서 **상태 및 복구** 를 선택 합니다.
1. **응용 프로그램 상태 모니터링** 옵션을 사용 하도록 설정 합니다.
1. **자동 복구 정책** 섹션을 찾습니다.
1. **자동 복구** 옵션 **을 설정 합니다** .
1. **유예 기간 (분)** 에서 유예 기간 (분)을 지정 합니다. 허용 되는 값은 30 분에서 90 분 사이입니다. 
1. 작업이 완료되면 **저장** 을 선택합니다. 

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

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음은 *[az vmss update](/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)* 를 사용 하 여 기존 확장 집합의 자동 인스턴스 복구 정책을 업데이트 하는 예제입니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>자동 인스턴스 복구 정책의 서비스 상태 보기 및 업데이트

### <a name="rest-api"></a>REST API 

*OrchestrationServices* 속성을 사용 하 여 가상 머신 확장 집합에 대 한 API 버전 2019-12-01 이상으로 [인스턴스 보기 가져오기](/rest/api/compute/virtualmachinescalesets/getinstanceview) 를 사용 하 여 자동 복구의 *servicestate* 를 확인 합니다. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

가상 머신 확장 집합에서 API 버전 2019-12-01 이상인 *setOrchestrationServiceState* api를 사용 하 여 자동 복구 상태를 설정 합니다. 크기 집합을 자동 복구 기능에 옵트인 하면이 API를 사용 하 여 확장 집합에 대 한 자동 복구를 일시 중단 하거나 다시 시작할 수 있습니다. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

[Get instance-view](/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) cmdlet을 사용 하 여 자동 인스턴스 복구를 위한 *servicestate* 를 확인 합니다. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

자동 인스턴스 복구를 위해 *Servicestate* 를 업데이트 하려면 [설정-오케스트레이션-service-state](/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) cmdlet을 사용 합니다. 크기 집합을 자동 복구 기능에 옵트인 한 후에는이 cmdlet을 사용 하 여 확장 집합에 대 한 자동 복구를 일시 중단 하거나 다시 시작할 수 있습니다. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

매개 변수 *instanceview* 와 함께 [AzVmss](/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) cmdlet을 사용 하 여 자동 인스턴스 복구를 위한 *servicestate* 를 확인 합니다.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

자동 인스턴스 복구를 위해 Set-AzVmssOrchestrationServiceState cmdlet을 사용 하 여 *Servicestate* 를 업데이트 합니다. 크기 집합을 자동 복구 기능에 옵트인 한 후에는이 cmdlet을 사용 하 여 확장 집합에 대 한 자동 복구를 일시 중단 하거나 다시 시작할 수 있습니다.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>문제 해결

**자동 복구 정책을 사용 하도록 설정 하지 못했습니다.**

"' Properties ' 형식의 개체에서 ' automaticRepairsPolicy ' 멤버를 찾을 수 없습니다." 라는 메시지와 함께 ' BadRequest ' 오류가 표시 되 면 가상 머신 확장 집합에 사용 되는 API 버전을 확인 합니다. 이 기능을 수행 하려면 API 버전 2018-10-01 이상이 필요 합니다.

**정책이 설정 된 경우에도 인스턴스가 복구 되지 않습니다.**

인스턴스는 유예 기간 내에 있을 수 있습니다. 복구를 수행 하기 전에 인스턴스의 상태를 변경한 후 대기 하는 시간입니다. 이는 중간에 또는 우발적으로 복구 하는 것을 방지 하기 위한 것입니다. 인스턴스에 대 한 유예 기간이 완료 되 면 복구 작업이 수행 됩니다.

**확장 집합 인스턴스의 응용 프로그램 상태 보기**

가상 머신 확장 집합의 인스턴스에 대 한 [인스턴스 뷰 가져오기 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) 를 사용 하 여 응용 프로그램 상태를 볼 수 있습니다. Azure PowerShell에서 *-instanceview* 플래그와 함께 [AzVmssVM](/powershell/module/az.compute/get-azvmssvm) cmdlet을 사용할 수 있습니다. 응용 프로그램 상태는 *vmhealth* 속성에 제공 됩니다.

Azure Portal 에서도 상태를 볼 수 있습니다. 기존 확장 집합으로 이동 하 고, 왼쪽 메뉴에서 **인스턴스** 를 선택 하 고, 각 확장 집합 인스턴스의 상태에 대 한 **상태 열을 확인 합니다.** 

## <a name="next-steps"></a>다음 단계

확장 집합에 대 한 [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md) 를 구성 하는 방법에 대해 알아봅니다.
