---
title: 가상 머신 확장 집합에 대 한 근접 배치 그룹 미리 보기 | Microsoft Docs
description: Azure에서 Windows 가상 머신 확장 집합에 대 한 근접 배치 그룹을 만들고 사용 하는 방법에 대해 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 6a4f145c6431e98bbe9575f128ace30a23a1b972
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850354"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>미리 보기: PowerShell을 사용 하 여 근접 배치 그룹 만들기 및 사용

가장 낮은 대기 시간을 달성 하 여 가능한 한 가까운 Vm을 얻으려면 [근접 배치 그룹](co-location.md#preview-proximity-placement-groups)내에 확장 집합을 배포 해야 합니다.

근접 배치 그룹은 Azure 계산 리소스가 물리적으로 서로 가까운 위치에 있는지 확인 하는 데 사용 되는 논리적 그룹화입니다. 근접 배치 그룹은 낮은 대기 시간을 요구 하는 작업에 유용 합니다.

> [!IMPORTANT]
> 근접 배치 그룹은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 이러한 지역에서는 미리 보기 중에 근접 배치 그룹을 사용할 수 없습니다. **일본 동부**, **오스트레일리아 동부** 및 **인도 중부**.


## <a name="create-a-proximity-placement-group"></a>근접 배치 그룹 만들기
[AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet을 사용 하 여 근접 배치 그룹을 만듭니다. 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>근접 배치 그룹 나열

[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet을 사용 하 여 모든 근접 배치 그룹을 나열할 수 있습니다.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>확장 집합 만들기

[AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss)를 사용 하 여 확장 집합을 만들 때 근접 배치 그룹 ID를 참조 하려면를 `-ProximityPlacementGroup $ppg.Id`사용하  여 근접 배치 그룹에 소수 자릿수를 만듭니다.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)를 사용 하 여 배치 그룹에서 인스턴스를 볼 수 있습니다.

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>다음 단계

[Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) 를 사용 하 여 근접 배치 그룹을 만들 수도 있습니다.