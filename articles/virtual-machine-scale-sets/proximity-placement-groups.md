---
title: 가상 머신 스케일 세트에 대한 근접 배치 그룹 미리 보기
description: Azure에서 Windows 가상 시스템 크기 집합에 대한 근접 배치 그룹을 만들고 사용하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273613"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>미리 보기: PowerShell을 사용하여 근접 배치 그룹 생성 및 사용

VM을 가능한 한 가깝게 설정하여 가능한 가장 낮은 대기 시간을 달성하려면 [근접 배치 그룹](co-location.md#preview-proximity-placement-groups)내에 배율 집합을 배포해야 합니다.

근접 배치 그룹은 Azure 계산 리소스가 물리적으로 서로 가까이 있는지 확인하는 데 사용되는 논리적 그룹입니다. 근접 배치 그룹은 대기 시간이 짧은 워크로드에 유용합니다.

> [!IMPORTANT]
> 근접 배치 그룹은 현재 공개 미리 보기상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 미리보기 기간 동안 이러한 지역에서는 근접 배치 그룹을 사용할 수 없습니다: **일본 동부,** **오스트레일리아 동부** 및 **인도 중앙.**


## <a name="create-a-proximity-placement-group"></a>근접 배치 그룹 만들기
[새 Az근접배치그룹](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet을 사용하여 근접 배치 그룹을 작성합니다. 

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

## <a name="list-proximity-placement-groups"></a>근접 배치 그룹 목록

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet을 사용하여 모든 근접 배치 그룹을 나열할 수 있습니다.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>확장 집합 만들기

[New-AzVMSS를](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) 사용하여 `-ProximityPlacementGroup $ppg.Id` 축척 집합을 만들 때 근접 배치 그룹 ID를 참조하여 근접 배치 그룹에서 배율을 작성합니다.

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

[Get-Az근접배치Group을](/powershell/module/az.compute/get-azproximityplacementgroup)사용하여 배치 그룹의 인스턴스를 볼 수 있습니다.

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>다음 단계

[Azure CLI를](../virtual-machines/linux/proximity-placement-groups.md) 사용하여 근접 배치 그룹을 만들 수도 있습니다.
