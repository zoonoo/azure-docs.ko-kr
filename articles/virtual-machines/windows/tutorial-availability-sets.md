---
title: 자습서 - Azure에서 Windows VM을 위한 고가용성 | Microsoft Docs
description: 이 자습서에서는 Azure PowerShell을 사용하여 가용성 집합에서 고가용성 가상 머신을 배포하는 방법을 알아봅니다.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f71bfa39e4ded0ea300cc2d329c442fdc6ddec37
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309080"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 고가용성 가상 머신 만들기 및 배포

이 자습서에서는 가용성 집합을 사용하여 VM(Virtual Machine)의 가용성과 안정성을 향상시키는 방법에 대해 알아봅니다. 가용성 집합은 Azure에 배포한 VM이 클러스터의 격리된 여러 하드웨어 노드에 분산되어 있는지 확인합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가용성 집합 만들기
> * 가용성 집합에서 VM 만들기
> * 사용 가능한 VM 크기 확인
> * Azure Advisor 확인


## <a name="availability-set-overview"></a>가용성 집합 개요

가용성 집합은 VM 리소스가 배포될 때 서로를 격리하는 논리적 그룹화 기능입니다. Azure는 가용성 집합 내에 배치한 VM을 여러 물리적 서버, 컴퓨팅 랙, 스토리지 단위 및 네트워크 스위치에서 실행되도록 합니다. 하드웨어 또는 소프트웨어 장애가 발생하면, VM의 하위 집합만 영향을 받고 전체 솔루션은 작동 상태를 유지합니다. 가용성 집합은 안정적인 클라우드 솔루션을 구축하는 데 필수적입니다.

4개의 프런트 엔드 웹 서버 및 2개의 백 엔드 VM이 있는 일반적인 VM 기반 솔루션을 가정하겠습니다. Azure를 사용하여 VM을 배포하기 전에 두 개의 가용성 집합(웹 계층에 하나, 백 계층에 하나)을 정의하려고 합니다. 새 VM을 만들 때 가용성 집합을 매개 변수로 지정합니다. Azure는 VM이 다수의 실제 하드웨어 리소스에서 격리되도록 합니다. 서버 중 하나가 실행 중인 실제 하드웨어에 문제가 발생해도, 서버의 다른 인스턴스는 계속 실행됩니다. 해당 인스턴스는 다른 하드웨어에 있기 때문입니다.

Azure 내에서 신뢰할 수 있는 VM 기반 솔루션을 배포하려면 항상 가용성 집합을 사용합니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="create-an-availability-set"></a>가용성 집합 만들기

한 위치의 하드웨어는 여러 개의 업데이트 도메인 및 장애 도메인으로 구분됩니다. **업데이트 도메인**은 동시에 다시 부팅할 수 있는 VM 그룹과 기본 물리적 하드웨어입니다. 동일한 **장애 도메인**의 VM은 공통의 저장소뿐만 아니라 공통의 전원 및 네트워크 스위치를 공유합니다.  

[New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset)을 사용하여 가용성 집합을 만들 수 있습니다. 이 예제에서 업데이트 및 장애 도메인의 수는 모두 *2*이며 가용성 집합의 이름은 *myAvailabilitySet*입니다.

리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

`-sku aligned` 매개 변수가 있는 [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset)을 사용하여 관리형 가용성 집합을 만듭니다.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>가용성 집합에 포함된 VM 만들기
VM이 하드웨어 전체에 올바르게 배포되도록 하려면 VM을 가용성 집합 내에 만들어야 합니다. VM을 만든 후에는 가용성 집합에 기존 VM을 추가할 수 없습니다. 


[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 때 `-AvailabilitySetName` 매개 변수를 사용하여 가용성 집합의 이름을 지정합니다.

먼저 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM에 대한 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

이제 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)을 사용하여 가용성 집합에 2개의 VM을 만듭니다.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

두 VM을 만들고 구성하는 데 몇 분 정도 소요됩니다. 완료되면 기본 하드웨어에 2개의 가상 머신이 배포되어 있습니다. 

**리소스 그룹** > **myResourceGroupAvailability** > **myAvailabilitySet**으로 차례로 이동하여 포털에서 가용성 집합을 살펴보면 VM이 2개의 장애 및 업데이트 도메인에 배포되는 방식을 확인할 수 있습니다.

![포털의 가용성 집합](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>사용 가능한 VM 크기 확인 

나중에 더 많은 VM을 가용성 집합에 추가할 수 있지만 하드웨어에서 사용 가능한 VM 크기를 알아야 합니다. [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize)를 사용하여 하드웨어 클러스터에서 사용 가능한 모든 가용성 집합 크기를 나열합니다.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Azure Advisor 확인 

또한 Azure Advisor를 사용하여 VM의 가용성을 개선하는 방법에 대한 자세한 정보를 볼 수 있습니다. Azure Advisor는 구성 및 사용량 원격 분석을 수행한 다음, Azure 리소스의 비용 효율성, 성능, 가용성 및 보안을 향상시키는 데 도움이 되는 해결 방법을 권장합니다.

[Azure Portal](https://portal.azure.com)에 로그인하고, **모든 서비스**를 선택하고, **Advisor**를 입력합니다. Advisor 대시보드에 선택한 구독에 대해 맞춤형 추천이 표시됩니다. 자세한 내용은 [Azure Advisor 시작](../../advisor/advisor-get-started.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가용성 집합 만들기
> * 가용성 집합에서 VM 만들기
> * 사용 가능한 VM 크기 확인
> * Azure Advisor 확인

가상 머신 확장 집합에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [VM Scale Set 만들기](tutorial-create-vmss.md)


