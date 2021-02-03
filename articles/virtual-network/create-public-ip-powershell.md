---
title: 공용 IP 만들기-Azure PowerShell
description: Azure PowerShell를 사용 하 여 공용 IP를 만드는 방법을 알아봅니다.
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
ms.openlocfilehash: ff768bceaba57c119aa88d5d4d99b11608917695
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492028"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용 하 여 공용 IP 주소 만들기

이 문서에서는 Azure PowerShell를 사용 하 여 공용 IP 주소 리소스를 만드는 방법을 보여 줍니다. 이에 연결할 수 있는 리소스, 기본 및 표준 SKU 간의 차이점 및 기타 관련 정보에 대 한 자세한 내용은 [공용 IP 주소](./public-ip-addresses.md)를 참조 하세요.  이 예에서는 IPv4 주소에만 중점을 둡니다. IPv6 주소에 대 한 자세한 내용은 [Azure VNet 용 ipv6](./ipv6-overview.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**Eastus2** 위치에 **Myresourcegroup** 이라는 [AzResourceGroup 이라는 새](/powershell/module/az.resources/new-azresourcegroup) 리소스 그룹을 만듭니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>공용 IP 만들기

---
# <a name="standard-sku---using-zones"></a>[**표준 SKU-영역 사용**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>다음 명령은 Az. Network module version 4.5.0 이상에 대해 작동 합니다.  현재 사용 중인 Powershell 모듈에 대 한 자세한 내용은 [PowerShellGet 설명서](https://docs.microsoft.com/powershell/module/powershellget/?view=powershell-7.1)를 참조 하세요.

[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 를 사용 하 여 **Myresourcegroup** 에 **mystandardzrpublicip** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.

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
> Az. 4.5.0 보다 오래 된 네트워크 모듈의 경우 영역 중복 IP 주소를 만들기 위한 영역 매개 변수를 지정 하지 않고 위의 명령을 실행 합니다. 
>

**Myresourcegroup** 에서 이름이 **myStandardZonalPublicIP** 인 영역 2에 표준 영역 공용 IP 주소를 만들려면 다음 명령을 사용 합니다.

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

영역에 대 한 위의 옵션은 [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)있는 지역 에서만 유효한 선택입니다.

# <a name="standard-sku---no-zones"></a>[**표준 SKU-영역 없음**](#tab/option-create-public-ip-standard)

>[!NOTE]
>다음 명령은 Az. Network module version 4.5.0 이상에 대해 작동 합니다.  현재 사용 중인 Powershell 모듈에 대 한 자세한 내용은 [PowerShellGet 설명서](https://docs.microsoft.com/powershell/module/powershellget/?view=powershell-7.1)를 참조 하세요.

[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 를 사용 하 여 **Myresourcegroup** 에서 **mystandardpublicip** 라는 비 영역 리소스로 표준 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

이 선택 항목은 모든 지역에서 유효 하며, [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)없는 지역에서 표준 공용 IP 주소에 대 한 기본 선택입니다.

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-create-public-ip-basic)

[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 를 사용 하 여 **Myresourcegroup** 에 **mybasicpublicip** 이라는 기본 고정 공용 IP 주소를 만듭니다.  기본 공용 Ip에는 가용성 영역의 개념이 없습니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
시간에 따라 IP 주소를 변경 하는 것이 허용 되는 경우 AllocationMethod를 ' 동적 '으로 변경 하 여 **동적** ip 할당을 선택할 수 있습니다.

---

## <a name="additional-information"></a>추가 정보 

위에 나열 된 개별 변수에 대 한 자세한 내용은 [공용 IP 주소 관리](./virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
- [가상 컴퓨터에 공용 IP 주소](./associate-public-ip-address-vm.md#azure-portal) 연결
- Azure에서 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
