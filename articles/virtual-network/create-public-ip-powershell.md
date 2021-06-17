---
title: 공용 IP 만들기 - Azure PowerShell
description: Azure PowerShell을 사용하여 공용 IP를 만드는 방법 알아보기
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 381f0bfcc7ff676754994573c50df6296c14a25e
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110675994"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 공용 IP 주소 만들기

이 문서에서는 Azure PowerShell을 사용하여 공용 IP 주소 리소스를 만드는 방법을 소개합니다. 연결할 수 있는 리소스, 기본 SKU와 표준 SKU 간의 차이점, 기타 관련 정보에 대한 자세한 내용은 [공용 IP 주소](./public-ip-addresses.md)를 참조하세요.  이 예에서는 IPv4 주소만 중점적으로 다룹니다. IPv6 주소에 대한 자세한 내용은 [Azure VNet용 IPv6](./ipv6-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**eastus2** 위치에 **myResourceGroup** 이라는 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>공용 IP 만들기

---
# <a name="standard-sku---using-zones"></a>[**표준 SKU - 영역 사용**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>다음 명령은 Az.Network 모듈 버전 4.5.0 이상에서 작동합니다.  현재 사용 중인 Powershell 모듈에 대한 자세한 내용은 [PowerShellGet 설명서](/powershell/module/powershellget/)를 참조하세요.

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 **myResourceGroup** 에 **myStandardZRPublicIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> 4\.5.0 이전의 Az.Network 모듈의 경우 영역 매개 변수를 지정하지 않고 위의 명령을 실행하여 영역 중복 IP 주소를 만듭니다. 
>

**myResourceGroup** 에서 **myStandardZonalPublicIP** 라는 영역 2에 표준 영역 공용 IP 주소를 만들려면 다음 명령을 사용합니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

위의 영역 옵션은 [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.

# <a name="standard-sku---no-zones"></a>[**표준 SKU - 영역 없음**](#tab/option-create-public-ip-standard)

>[!NOTE]
>다음 명령은 Az.Network 모듈 버전 4.5.0 이상에서 작동합니다.  현재 사용 중인 Powershell 모듈에 대한 자세한 내용은 [PowerShellGet 설명서](/powershell/module/powershellget/)를 참조하세요.

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 **myResourceGroup** 에서 **myStandardPublicIP** 라는 비영역 리소스로 표준 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

이 방법은 모든 지역에서 유효하며, [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 없는 지역에서 표준 공용 IP 주소에 대한 기본 방법입니다.

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-create-public-ip-basic)

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 **myResourceGroup** 에 **myBasicPublicIP** 라는 기본 고정 공용 IP 주소를 만듭니다.  기본 공용 IP에는 가용성 영역의 개념이 없습니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
시간에 따라 IP 주소를 변경할 수 있는 경우 AllocationMethod를 ‘동적’으로 변경하여 **동적** IP 할당을 선택할 수 있습니다.

---

## <a name="additional-information"></a>추가 정보 

위에 나열된 개별 변수에 대한 자세한 내용은 [공용 IP 주소 관리](./virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [공용 IP 주소를 가상 머신에](./associate-public-ip-address-vm.md#azure-portal) 연결
- Azure에서 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
