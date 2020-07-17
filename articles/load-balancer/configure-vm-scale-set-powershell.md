---
title: 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합 구성-Azure PowerShell
description: 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성 하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 858315008434f511e3adc0a91d591d924634fc39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809495"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합 구성

이 문서에서는 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성 하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독
- 가상 머신 확장 집합이 배포 될 구독의 기존 표준 sku 부하 분산 장치입니다.
- 가상 머신 확장 집합에 대 한 Azure Virtual Network입니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Azure CLI에 로그인

Azure에 로그인 합니다.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>기존 부하 분산 장치를 사용 하 여 가상 머신 확장 집합 배포

괄호 안의 값을 구성에 있는 리소스의 이름으로 바꿉니다.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

아래 예제에서는를 사용 하 여 가상 머신 확장 집합을 배포 합니다.

- **Myvmss** 라는 가상 머신 확장 집합
- **Myloadbalancer** 라는 Azure Load Balancer
- **MyBackendPool** 라는 부하 분산 장치 백 엔드 풀
- **Myvnet** 이라는 Azure Virtual Network
- **Mysubnet** 이라는 서브넷
- **Myresourcegroup** 이라는 리소스 그룹

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> 확장 집합을 만든 후에는 부하 분산 장치의 상태 프로브에서 사용 하는 부하 분산 규칙에 대해 백 엔드 포트를 수정할 수 없습니다. 포트를 변경 하려면 Azure 가상 머신 확장 집합을 업데이트 하 고, 포트를 업데이트 한 후 상태 프로브를 다시 구성 하 여 상태 프로브를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 배포 했습니다.  가상 머신 확장 집합 및 부하 분산 장치에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Load Balancer란?](load-balancer-overview.md)
- [가상 머신 크기 집합이란?](../virtual-machine-scale-sets/overview.md)
                                