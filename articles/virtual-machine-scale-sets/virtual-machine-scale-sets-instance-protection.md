---
title: Azure 가상 시스템 규모 집합 인스턴스에 대한 인스턴스 보호
description: 확장 및 확장 집합 작업에서 Azure 가상 시스템 집합 인스턴스를 보호하는 방법을 알아봅니다.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254119"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Azure 가상 시스템 규모 집합 인스턴스에 대한 인스턴스 보호

Azure 가상 시스템 크기 집합을 사용하면 [자동 확장을](virtual-machine-scale-sets-autoscale-overview.md)통해 워크로드에 대한 탄력성이 향상되므로 인프라가 확장될 때와 확장 시기를 구성할 수 있습니다. 또한 확장 집합을 사용하면 다양한 [업그레이드 정책](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 설정을 통해 많은 수의 VM을 중앙에서 관리, 구성 및 업데이트할 수 있습니다. 확장 집합 모델에서 업데이트를 구성할 수 있으며 업그레이드 정책을 자동 또는 롤링으로 설정한 경우 새 구성이 모든 축척 집합 인스턴스에 자동으로 적용됩니다.

응용 프로그램이 트래픽을 처리함에 따라 특정 인스턴스를 다른 규모 집합 인스턴스와 다르게 처리하려는 경우가 있을 수 있습니다. 예를 들어 규모 집합의 특정 인스턴스는 장기 실행 작업을 수행할 수 있으며 작업이 완료될 때까지 이러한 인스턴스의 크기를 조정하지 않도록 할 수 있습니다. 축척 집합에 확장 집합의 다른 멤버보다 추가 또는 다른 작업을 수행하기 위해 몇 가지 인스턴스를 특수화할 수도 있습니다. 이러한 '특수' VM은 배율 집합의 다른 인스턴스와 함께 수정할 수 없습니다. 인스턴스 보호는 응용 프로그램에 대해 이러한 시나리오 및 기타 시나리오를 사용하도록 설정하는 추가 컨트롤을 제공합니다.

이 문서에서는 확장 집합 인스턴스를 사용하여 다양한 인스턴스 보호 기능을 적용하고 사용하는 방법에 대해 설명합니다.

## <a name="types-of-instance-protection"></a>인스턴스 보호 유형
크기 조정 집합은 두 가지 유형의 인스턴스 보호 기능을 제공합니다.

-   **스케일 인으로부터 보호**
    - 확장 집합 인스턴스에서 **protectFromScaleIn** 속성을 통해 사용 가능
    - 자동 크기 조정이 시작된 스케일 인으로부터 인스턴스 보호
    - 사용자가 시작한 인스턴스 작업(인스턴스 삭제 포함)이 **차단되지 않습니다.**
    - 스케일 세트에서 시작된 작업(업그레이드, 이미지 이미지, 거래 할당 등)은 **차단되지 않습니다.**

-   **스케일 세트 작업으로부터 보호**
    - 확장 집합 인스턴스에서 **보호로부터스케일셋작업** 속성을 통해 사용 가능
    - 자동 크기 조정이 시작된 스케일 인으로부터 인스턴스 보호
    - 스케일 세트에서 시작된 작업(예: 업그레이드, 이미지 재이미지, 할당 해제 등)에서 인스턴스를 보호합니다.
    - 사용자가 시작한 인스턴스 작업(인스턴스 삭제 포함)이 **차단되지 않습니다.**
    - 전체 축척 세트 삭제가 **차단되지 않음**

## <a name="protect-from-scale-in"></a>스케일 인으로부터 보호
인스턴스 보호를 인스턴스를 만든 후 집합 인스턴스를 배율 조정에 적용할 수 있습니다. 보호는 [배율 집합](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)모델이 아닌 [인스턴스 모델에서만](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) 적용되고 수정됩니다.

아래 예제에서 자세히 설명한 대로 스케일 집합 인스턴스에 확장 인 보호를 적용하는 방법에는 여러 가지가 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure 포털을 통해 확장 집합의 인스턴스에 확장 인 보호를 적용할 수 있습니다. 한 번에 두 개 이상의 인스턴스를 조정할 수 없습니다. 보호하려는 각 인스턴스에 대한 단계를 반복합니다.
 
1. 기존 가상 시스템 규모 집합으로 이동합니다.
1. **설정**에서 왼쪽 메뉴에서 **인스턴스를** 선택합니다.
1. 보호할 인스턴스의 이름을 선택합니다.
1. 보호 **정책** 탭을 선택합니다.
1. 보호 **정책** 블레이드에서 확장 해제 옵션을 **선택합니다.**
1. **저장**을 선택합니다. 

### <a name="rest-api"></a>REST API

다음 예제에서는 배율 집합의 인스턴스에 확장 보호가 적용됩니다.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>인스턴스 보호는 API 버전 2019-03-01 이상에서만 지원됩니다.

### <a name="azure-powershell"></a>Azure PowerShell

[Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet을 사용하여 스케일 세트 인스턴스에 배율 보호를 적용합니다.

다음 예제에서는 인스턴스 ID 0을 갖는 축척 집합의 인스턴스에 확장 보호가 적용됩니다.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

[az vmss 업데이트를](/cli/azure/vmss#az-vmss-update) 사용하여 스케일 세트 인스턴스에 확장 인 보호를 적용합니다.

다음 예제에서는 인스턴스 ID 0을 갖는 축척 집합의 인스턴스에 확장 보호가 적용됩니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>스케일 세트 작업으로부터 보호
인스턴스 보호를 인스턴스를 만든 후 집합 인스턴스를 배율 조정에 적용할 수 있습니다. 보호는 [배율 집합](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)모델이 아닌 [인스턴스 모델에서만](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) 적용되고 수정됩니다.

스케일 집합 작업으로부터 인스턴스를 보호하여 자동 크기 조정이 시작된 스케일 인으로부터 인스턴스를 보호합니다.

아래 예제에서 자세히 설명한 대로 스케일 집합 인스턴스에 축척 집합 작업 보호를 적용하는 방법에는 여러 가지가 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure 포털을 통해 확장 집합 작업에서 확장 집합의 인스턴스에 대한 보호를 적용할 수 있습니다. 한 번에 두 개 이상의 인스턴스를 조정할 수 없습니다. 보호하려는 각 인스턴스에 대한 단계를 반복합니다.
 
1. 기존 가상 시스템 규모 집합으로 이동합니다.
1. **설정**에서 왼쪽 메뉴에서 **인스턴스를** 선택합니다.
1. 보호할 인스턴스의 이름을 선택합니다.
1. 보호 **정책** 탭을 선택합니다.
1. 보호 **정책** 블레이드에서 **축척 설정 작업에서 보호 옵션을 선택합니다.**
1. **저장**을 선택합니다. 

### <a name="rest-api"></a>REST API

다음 예제에서는 배율 집합 작업에서 확장 집합 집합의 인스턴스에 대한 보호를 적용합니다.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>인스턴스 보호는 API 버전 2019-03-01 이상에서만 지원됩니다.</br>
스케일 집합 작업으로부터 인스턴스를 보호하여 자동 크기 조정이 시작된 스케일 인으로부터 인스턴스를 보호합니다. "protectFromScaleIn"을 지정할 수 없습니다.

### <a name="azure-powershell"></a>Azure PowerShell

[Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet을 사용하여 배율 설정 작업에서 확장 집합 인스턴스에 대한 보호를 적용합니다.

다음 예제에서는 배율 집합 작업에서 인스턴스 ID 0을 갖는 축척 집합의 인스턴스에 대한 보호를 적용합니다.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

[az vmss 업데이트를](/cli/azure/vmss#az-vmss-update) 사용하여 배율 설정 작업에서 확장 집합 인스턴스에 대한 보호를 적용합니다.

다음 예제에서는 배율 집합 작업에서 인스턴스 ID 0을 갖는 축척 집합의 인스턴스에 대한 보호를 적용합니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>문제 해결
### <a name="no-protectionpolicy-on-scale-set-model"></a>보호 없음스케일 세트 모델의 정책
인스턴스 보호는 축척 집합 모델이 아닌 축척 집합 인스턴스에만 적용됩니다.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>확장 집합 인스턴스 모델의 보호 없음정책
기본적으로 보호 정책이 생성될 때 인스턴스에 적용되지 않습니다.

인스턴스를 만든 후 집합 인스턴스의 배율 조정에 인스턴스 보호를 적용할 수 있습니다.

### <a name="not-able-to-apply-instance-protection"></a>인스턴스 보호를 적용할 수 없습니다.
인스턴스 보호는 API 버전 2019-03-01 이상에서만 지원됩니다. 사용 중인 API 버전을 확인하고 필요에 따라 업데이트합니다. PowerShell 또는 CLI를 최신 버전으로 업데이트해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
