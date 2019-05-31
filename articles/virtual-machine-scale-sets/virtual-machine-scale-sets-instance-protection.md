---
title: 인스턴스 보호에 대 한 Azure 가상 머신 확장 집합 인스턴스 | Microsoft Docs
description: 규모 감축 및 확장 집합 작업에서 Azure 가상 머신 확장 집합 인스턴스를 보호 하는 방법에 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 61430f5a43a04fa0e5b2f0c79ff03419c73aaf28
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416559"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>인스턴스 보호에 대 한 Azure 가상 머신 확장 집합 인스턴스 (미리 보기)
Azure 가상 머신 확장 집합 사용을 통해 워크 로드에 대 한 더 나은 탄력성 [자동 크기 조정](virtual-machine-scale-sets-autoscale-overview.md)때 인프라 확장을 구성할 수 있도록 및 해당 눈금 체크 합니다. 확장 집합 사용 하면 수도 중앙에서 관리, 구성 및 업데이트를 통해 다른 많은 수의 Vm [업그레이드 정책을](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 설정 합니다. 확장 집합 모델에 대 한 업데이트를 구성할 수 있습니다 하 고 새 구성을 자동으로 적용 됩니다 모든 확장 집합 인스턴스에 자동 또는 롤링 업그레이드 정책을 설정한 경우.

응용 프로그램 트래픽을 처리를 수 있습니다 특정 인스턴스를 눈금의 나머지 부분에서 다르게 처리 하려는 경우 인스턴스를 설정 합니다. 예를 들어, 확장 집합의 특정 인스턴스 장기 실행 작업을 수행할 수 없습니다 및 수 확장 기능에 작업이 완료 될 때까지 이러한 인스턴스를 표시 하지 않으려면입니다. 확장 집합의 다른 구성원이 추가 하거나 다른 작업을 수행 하도록 규모 집합에서 일부 인스턴스를을 특수화도 될 수 있습니다. 이러한 '특수' Vm 확장 집합의 다른 인스턴스를 사용 하 여 수정할 필요가 필요 합니다. 인스턴스 보호에는 이러한 및 응용 프로그램에 대 한 기타 시나리오를 사용 하도록 설정 하려면 추가 컨트롤을 제공 합니다.

이 문서 적용 확장 집합 인스턴스를 사용 하 여 다른 인스턴스 보호 기능을 사용 하는 방법에 대해 설명 합니다.

> [!NOTE]
>인스턴스 보호는 현재 공개 미리 보기로 제공 됩니다. 아래에 설명 된 공개 미리 보기 기능을 사용 하려면 옵트인 프로시저가 필요 합니다. 인스턴스 protection 미리 보기 API-VERSION 2019-03-01와 managed disks를 사용 하는 확장 집합에만 지원 됩니다.

## <a name="types-of-instance-protection"></a>유형의 인스턴스 보호
확장 집합 두 가지 유형의 인스턴스 보호 기능을 제공합니다.

-   **확장 기능에서 보호**
    - 통해 사용 하도록 설정 **protectFromScaleIn** 눈금의 속성 집합 인스턴스
    - 시작 하는 자동 크기 조정 규모 감축에서 인스턴스를 보호합니다.
    - 사용자가 시작한 인스턴스 (인스턴스 삭제 포함) 연산은 **차단 되지 않음**
    - 확장 집합에서 시작 된 작업 (업그레이드, 이미지로 다시 설치, 할당 취소 등)는 **차단 되지 않음**

-   **확장 집합 작업에서 보호**
    - 통해 사용 하도록 설정 **protectFromScaleSetActions** 눈금의 속성 집합 인스턴스
    - 시작 하는 자동 크기 조정 규모 감축에서 인스턴스를 보호합니다.
    - 확장 집합에서 시작 하는 작업의 인스턴스를 보호 (업그레이드와 같은 이미지로 다시 설치, 할당 취소 등.)
    - 사용자가 시작한 인스턴스 (인스턴스 삭제 포함) 연산은 **차단 되지 않음**
    - 전체 확장 집합의 삭제는 **차단 되지 않음**

## <a name="protect-from-scale-in"></a>확장 기능에서 보호
인스턴스가 만들어진 후에 확장 집합 인스턴스에 인스턴스 보호를 적용할 수 있습니다. 보호를 적용 하 고의 경우에 수정 합니다 [인스턴스 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) 아닌 합니다 [확장 집합 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)합니다.

아래 예제에 설명 된 대로 확장 집합 인스턴스에서 확장 보호를 적용 하는 방법은 여러 가지입니다.

### <a name="rest-api"></a>REST API

다음 예제에서는 확장 집합의 인스턴스를 스케일 인 보호를 적용합니다.

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
>인스턴스 보호에는 API 버전 2019-03-01을 사용 하 여 이상 에서만 지원 됩니다.

### <a name="azure-powershell"></a>Azure PowerShell

사용 된 [업데이트 AzVmssVM](/powershell/module/az.compute/update-azvmssvm) 스케일 인 보호 확장을 적용 하는 cmdlet 집합 인스턴스.

다음 예에서는 0 인스턴스 ID를 가진 확장 집합의 인스턴스를 스케일 인 보호를 적용 합니다.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

사용 하 여 [az vmss 업데이트](/cli/azure/vmss#az-vmss-update) 스케일 인 보호에 확장 집합 인스턴스를 적용 하도록 합니다.

다음 예에서는 0 인스턴스 ID를 가진 확장 집합의 인스턴스를 스케일 인 보호를 적용 합니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>확장 집합 작업에서 보호
인스턴스가 만들어진 후에 확장 집합 인스턴스에 인스턴스 보호를 적용할 수 있습니다. 보호를 적용 하 고의 경우에 수정 합니다 [인스턴스 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) 아닌 합니다 [확장 집합 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)합니다.

시작 하는 자동 크기 조정 규모 감축에서 인스턴스를 보호도 확장 집합 작업의 인스턴스를 보호 합니다.

확장을 적용 하는 여러 가지 작업에 보호를 설정 하 여 확장 집합 인스턴스 아래 예제에 설명 된 대로 가지 있습니다.

### <a name="rest-api"></a>REST API

다음 예제에서는 확장 집합의 인스턴스를 확장 집합 작업에서 보호를 적용합니다.

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
>인스턴스 보호 API 2019-03-01 버전 이상에 지원 됩니다.</br>
시작 하는 자동 크기 조정 규모 감축에서 인스턴스를 보호도 확장 집합 작업의 인스턴스를 보호 합니다. "ProtectFromScaleIn"를 지정할 수 없습니다: "protectFromScaleSetActions"를 설정 하는 경우 false: true

### <a name="azure-powershell"></a>Azure PowerShell

사용 합니다 [업데이트 AzVmssVM](/powershell/module/az.compute/update-azvmssvm) 눈금에서 보호를 적용 하는 cmdlet에 확장 집합 인스턴스에 작업을 설정 합니다.

다음 예에서는 0 인스턴스 ID를 가진 확장 집합의 인스턴스를 확장 집합 작업에서 보호를 적용 합니다.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

사용 하 여 [az vmss 업데이트](/cli/azure/vmss#az-vmss-update) 규모 집합 인스턴스로 크기 조정 집합 작업에서 보호를 적용 하도록 합니다.

다음 예에서는 0 인스턴스 ID를 가진 확장 집합의 인스턴스를 확장 집합 작업에서 보호를 적용 합니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>문제 해결
### <a name="no-protectionpolicy-on-scale-set-model"></a>확장 집합 모델에 없는 protectionPolicy
인스턴스 보호만 확장 집합 모델이 아닌 확장 집합 인스턴스에 적용 됩니다.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>확장 집합 인스턴스 모델에 없는 protectionPolicy
보호 정책이 기본적으로 없습니다 만들어질 때 인스턴스에 적용 됩니다.

인스턴스가 만들어진 후에 확장 집합 인스턴스에 인스턴스 보호를 적용할 수 있습니다.

### <a name="not-able-to-apply-instance-protection"></a>인스턴스 보호를 적용할 수 없습니다.
인스턴스 보호 API 2019-03-01 버전 이상에 지원 됩니다. 사용 중인 API 버전을 확인 하 고 필요에 따라 업데이트 합니다. PowerShell 또는 CLI 최신 버전으로 업데이트 해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계
가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
