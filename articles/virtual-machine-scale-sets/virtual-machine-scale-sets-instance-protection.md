---
title: Azure 가상 머신 확장 집합 인스턴스에 대 한 인스턴스 보호
description: 규모 확장 및 확장 집합 작업에서 Azure 가상 머신 확장 집합 인스턴스를 보호 하는 방법에 대해 알아봅니다.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919858"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Azure 가상 머신 확장 집합 인스턴스에 대 한 인스턴스 보호

Azure 가상 머신 확장 집합을 사용 하면 [자동 크기 조정을](virtual-machine-scale-sets-autoscale-overview.md)통해 워크 로드에 대해 더 나은 탄력성를 사용할 수 있으므로 인프라가 확장 되 고 확장이 확장 될 때를 구성할 수 있습니다. 또한 크기 집합을 사용 하면 여러 [업그레이드 정책](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 설정을 통해 중앙에서 다 수의 vm을 관리, 구성 및 업데이트할 수 있습니다. 업그레이드 정책을 자동 또는 롤링으로 설정한 경우 확장 집합 모델에 대 한 업데이트를 구성할 수 있으며 새 구성은 모든 확장 집합 인스턴스에 자동으로 적용 됩니다.

응용 프로그램에서 트래픽을 처리할 때 특정 인스턴스를 확장 집합 인스턴스의 나머지 부분과 다르게 처리 하려는 상황이 있을 수 있습니다. 예를 들어, 확장 집합의 특정 인스턴스는 장기 실행 작업을 수행할 수 있으며, 작업이 완료 될 때까지 이러한 인스턴스를 확장 하지 않으려고 합니다. 확장 집합의 일부 인스턴스는 확장 집합의 다른 멤버와 다른 작업을 수행 하기 위해 확장 집합에서 몇 가지 인스턴스를 특수화 했을 수도 있습니다. 이러한 ' 특별 ' Vm은 확장 집합의 다른 인스턴스로 수정 하지 않아도 됩니다. 인스턴스 보호는 응용 프로그램에 대해 이러한 시나리오와 기타 시나리오를 사용할 수 있도록 하는 추가 컨트롤을 제공 합니다.

이 문서에서는 확장 집합 인스턴스를 사용 하 여 다양 한 인스턴스 보호 기능을 적용 하 고 사용 하는 방법을 설명 합니다.

## <a name="types-of-instance-protection"></a>인스턴스 보호 유형
크기 집합은 다음과 같은 두 가지 유형의 인스턴스 보호 기능을 제공 합니다.

-   **규모에서 보호**
    - 확장 집합 인스턴스에서 **protectFromScaleIn** 속성을 통해 사용
    - 자동 크기 조정 시작 된 확장에서 인스턴스를 보호 합니다.
    - 사용자가 시작한 인스턴스 작업 (인스턴스 삭제 포함)이 **차단 되지 않습니다** .
    - 확장 집합에서 시작 된 작업 (업그레이드, 이미지로 다시 설치, 할당 취소 등)이 **차단 되지 않습니다** .

-   **크기 집합 작업에서 보호**
    - 확장 집합 인스턴스에서 **protectFromScaleSetActions** 속성을 통해 사용
    - 자동 크기 조정 시작 된 확장에서 인스턴스를 보호 합니다.
    - 확장 집합에서 시작 된 작업 (예: 업그레이드, 이미지로 다시 설치, 할당 취소 등)의 인스턴스를 보호 합니다.
    - 사용자가 시작한 인스턴스 작업 (인스턴스 삭제 포함)이 **차단 되지 않습니다** .
    - 전체 확장 집합의 삭제가 **차단 되지 않습니다** .

## <a name="protect-from-scale-in"></a>규모에서 보호
인스턴스를 만든 후에 확장 집합 인스턴스에 인스턴스 보호를 적용할 수 있습니다. 보호는 [인스턴스 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) 에서만 적용 되 고 수정 되며 [확장 집합 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)에는 적용 되지 않습니다.

아래 예제에 설명 된 대로 확장 집합 인스턴스에 스케일 인 보호를 적용 하는 여러 가지 방법이 있습니다.

### <a name="azure-portal"></a>Azure 포털

Azure Portal를 통해 확장 집합의 인스턴스에 확장 보호를 적용할 수 있습니다. 한 번에 두 개 이상의 인스턴스를 조정할 수 없습니다. 보호 하려는 각 인스턴스에 대해이 단계를 반복 합니다.
 
1. 기존 가상 머신 확장 집합으로 이동 합니다.
1. 왼쪽의 **설정**에서 메뉴의 **인스턴스** 를 선택 합니다.
1. 보호 하려는 인스턴스의 이름을 선택 합니다.
1. **보호 정책** 탭을 선택 합니다.
1. **보호 정책** 블레이드에서 **확장에서 보호** 옵션을 선택 합니다.
1. **저장**을 선택합니다. 

### <a name="rest-api"></a>REST API

다음 예에서는 확장 집합의 인스턴스에 확장 보호를 적용 합니다.

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
>인스턴스 보호는 API 버전 2019-03-01 이상 에서만 지원 됩니다.

### <a name="azure-powershell"></a>Azure PowerShell

[AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet을 사용 하 여 확장 집합 인스턴스에 스케일 인 보호를 적용 합니다.

다음 예에서는 인스턴스 ID가 0 인 확장 집합의 인스턴스에 확장 보호를 적용 합니다.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

[Az vmss update](/cli/azure/vmss#az-vmss-update) 를 사용 하 여 확장 집합 인스턴스에 스케일 인 보호를 적용 합니다.

다음 예에서는 인스턴스 ID가 0 인 확장 집합의 인스턴스에 확장 보호를 적용 합니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>크기 집합 작업에서 보호
인스턴스를 만든 후에 확장 집합 인스턴스에 인스턴스 보호를 적용할 수 있습니다. 보호는 [인스턴스 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) 에서만 적용 되 고 수정 되며 [확장 집합 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)에는 적용 되지 않습니다.

크기 집합 작업에서 인스턴스를 보호 하면 자동 크기 조정 시작 된 규모에서 인스턴스를 보호할 수도 있습니다.

아래 예제에 설명 된 대로 확장 집합 인스턴스에 확장 집합 작업 보호를 적용 하는 방법에는 여러 가지가 있습니다.

### <a name="azure-portal"></a>Azure 포털

Azure Portal를 통해 확장 집합 작업의 보호를 확장 집합의 인스턴스에 적용할 수 있습니다. 한 번에 두 개 이상의 인스턴스를 조정할 수 없습니다. 보호 하려는 각 인스턴스에 대해이 단계를 반복 합니다.
 
1. 기존 가상 머신 확장 집합으로 이동 합니다.
1. 왼쪽의 **설정**에서 메뉴의 **인스턴스** 를 선택 합니다.
1. 보호 하려는 인스턴스의 이름을 선택 합니다.
1. **보호 정책** 탭을 선택 합니다.
1. **보호 정책** 블레이드에서 **크기 집합 작업에서 보호** 옵션을 선택 합니다.
1. **저장**을 선택합니다. 

### <a name="rest-api"></a>REST API

다음 예에서는 확장 집합 작업에서 확장 집합의 인스턴스로 보호를 적용 합니다.

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
>인스턴스 보호는 API 버전 2019-03-01 이상 에서만 지원 됩니다.</br>
크기 집합 작업에서 인스턴스를 보호 하면 자동 크기 조정 시작 된 규모에서 인스턴스를 보호할 수도 있습니다. "ProtectFromScaleSetActions": true를 설정 하는 경우 "protectFromScaleIn": false를 지정할 수 없습니다.

### <a name="azure-powershell"></a>Azure PowerShell

[AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet을 사용 하 여 확장 집합 작업의 보호를 확장 집합 인스턴스에 적용할 수 있습니다.

다음 예에서는 인스턴스 ID가 0 인 확장 집합의 인스턴스에 확장 집합 작업의 보호를 적용 합니다.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

[Az vmss update](/cli/azure/vmss#az-vmss-update) 를 사용 하 여 확장 집합 작업의 보호를 확장 집합 인스턴스에 적용 합니다.

다음 예에서는 인스턴스 ID가 0 인 확장 집합의 인스턴스에 확장 집합 작업의 보호를 적용 합니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>문제 해결
### <a name="no-protectionpolicy-on-scale-set-model"></a>확장 집합 모델에 protectionPolicy 없음
인스턴스 보호는 확장 집합에만 적용 되 고 확장 집합 모델에는 적용 되지 않습니다.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>확장 집합 인스턴스 모델에 protectionPolicy 없음
기본적으로 보호 정책이 생성 될 때 인스턴스에 적용 되지 않습니다.

인스턴스를 만든 후 인스턴스 보호를 확장 집합 인스턴스에 적용할 수 있습니다.

### <a name="not-able-to-apply-instance-protection"></a>인스턴스 보호를 적용할 수 없습니다.
인스턴스 보호는 API 버전 2019-03-01 이상 에서만 지원 됩니다. 사용 중인 API 버전을 확인 하 고 필요에 따라 업데이트 합니다. PowerShell 또는 CLI를 최신 버전으로 업데이트 해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
