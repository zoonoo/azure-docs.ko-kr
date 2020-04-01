---
title: Azure 가상 네트워크에서 공용 IPv6 주소 및 주소 범위 예약
titlesuffix: Azure Virtual Network
description: Azure 가상 네트워크에서 공용 IPv6 주소 및 주소 범위를 예약하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a0dd56842174d89688c862397c373326ef50d1f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420537"
---
# <a name="reserve-public-ipv6-address-prefix"></a>공용 IPv6 주소 접두사 예약
VNet(Azure 가상 네트워크용 IPv6)을 사용하면 가상 네트워크 내에서 와 인터넷에서 IPv6 및 IPv4 연결을 통해 Azure에서 응용 프로그램을 호스팅할 수 있습니다. 개별 IPv6 주소를 예약하는 것 외에도 사용할 수 있도록 연속범위의 Azure IPv6 주소(IP 접두사라고 함)를 예약할 수 있습니다. 이 문서에서는 Azure PowerShell 및 CLI를 사용하여 IPv6 공용 IP 주소 및 주소 범위를 만드는 방법에 대해 설명합니다.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>예약된 단일 IPv6 공용 IP 만들기

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

다음과 같이 [New-AzPublicIpAddress를](/powershell/module/az.network/new-azpublicipaddress) 사용하여 Azure PowerShell을 사용하여 단일 예약(정적) IPv6 공용 IP 주소를 만들 수 있습니다.

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Azure CLI 사용

 [az 네트워크 공용 IP를](/cli/azure/network/public-ip) 사용하여 단일 예약(정적) IPv6 공용 IP 주소 Azure CLI를 다음과 같이 만들 수 있습니다.
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>예약된 IPv6 접두사 만들기(범위)

IPv6 접두사를 예약하려면 IPv4 접두사를 만드는 데 사용되는 것과 동일한 명령에 IPv6의 IP 주소 패밀리를 추가합니다. 다음 명령은 크기 /125 (8 IPv6 주소)의 접두사를 만듭니다.  

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

다음과 같이 [az 네트워크 공용 IP create를](/powershell/module/az.network/new-azpublicipprefix) 사용하여 Azure CLI를 사용하여 공용 IPv6 주소를 만들 수 있습니다.
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Azure CLI 사용

다음과 같이 Azure CLI를 사용하여 공용 IPv6 주소를 만들 수 있습니다.

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>예약된 IPv6 접두사에서 공용 IP 주소 할당

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

 Azure PowerShell을 사용하여 공용 IP를 만들 때 `-PublicIpPrefix` 인수를 추가하여 예약된 접두사에서 정적 IPv6 공용 IP를 만듭니다. 다음 예제에서는 접두사가 만들어지고 powerShell 변수인 *$myOwnIPv6Prefix에*저장되었다고 가정합니다.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Azure CLI 사용
 
다음 예제에서는 접두사가 만들어지고 *IPv6PrefixWestUS라는*CLI 변수에 저장되었다고 가정합니다.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>다음 단계
- [IPv6 주소 접두사에](ipv6-public-ip-address-prefix.md)대해 자세히 알아보십시오.
- [IPv6 주소에](ipv6-overview.md)대해 자세히 알아보십시오.
