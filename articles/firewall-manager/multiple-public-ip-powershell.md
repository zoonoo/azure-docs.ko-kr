---
title: Azure PowerShell를 사용 하 여 여러 공용 IP 주소를 사용 하 여 Azure 방화벽 배포
description: 여러 공용 IP 주소를 사용 하 여 방화벽을 배포 하 여 가상 허브 보호
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 652c7cbfbe63ef2ae9a0d54e05407152ea300f1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87007000"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>여러 공용 IP 주소를 사용 하 여 Azure 방화벽 배포

Azure 방화벽을 사용 하 여 가상 허브를 보호 하려는 경우 Azure PowerShell를 사용 하 여 여러 공용 IP 주소를 사용 하 여 방화벽을 배포할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>방화벽 배포

다음 Azure PowerShell 예제를 사용 하 여 가상 허브를 보호 하기 위해 여러 공용 IP 주소를 사용 하 여 Azure 방화벽을 배포 합니다.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="update-a-public-ip-address"></a>공용 IP 주소를 업데이트 합니다.

Azure PowerShell를 사용 하 여 Azure 방화벽에 대 한 공용 IP 주소를 업데이트할 수 있습니다. 다음 예에서는 방화벽에서 하나의 공용 IP 주소를 삭제 합니다. 3 개의 공용 IP 주소로 시작 합니다.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>다음 단계

[보안 가상 허브란?](secured-virtual-hub.md)