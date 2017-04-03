---
title: "Azure Network Watcher Next Hop을 사용하여 다음 홉 찾기 - PowerShell | Microsoft Docs"
description: "이 문서에서는 PowerShell에서 Next Hop을 사용하여 다음 홉 유형 및 IP 주소를 찾을 수 있는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 95ecd8d4ef3dd7a956206e7c3e07c793db0111f7
ms.openlocfilehash: 9f53c824b6368dc2a6251fd880f1cabefef884b8
ms.lasthandoff: 03/31/2017


---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>PowerShell을 사용하는 Azure Network Watcher에서 Next Hop 기능을 사용하는 다음 홉이 무엇인지 확인합니다.

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Next Hop은 Network Watcher의 기능으로 지정된 가상 컴퓨터를 기반으로 하는 다음 홉 유형 및 IP 주소를 가져올 수 있습니다. 이 기능은 가상 컴퓨터에서 나가는 트래픽이 게이트웨이, 인터넷 또는 가상 네트워크를 트래버스하여 대상에 도달할지 여부를 결정하는 데 유용합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 Azure Portal을 사용하여 다음 홉 유형 및 IP 주소를 찾습니다.

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다. 또한 시나리오에서는 유효한 가상 컴퓨터를 포함한 리소스 그룹을 사용할 수 있다고 가정합니다.

## <a name="scenario"></a>시나리오

이 문서에서 다루는 시나리오는 리소스에 대한 다음 홉 유형 및 IP 주소를 찾는 Network Watcher의 기능인 Next Hop을 사용합니다. Next Hop에 대한 자세한 내용을 보려면 [Next Hop 개요](network-watcher-next-hop-overview.md)를 방문하세요.

## <a name="retrieve-network-watcher"></a>Network Watcher 검색

첫 번째 단계는 Network Watcher 인스턴스를 검색하는 것입니다. `$networkWatcher` 변수는 다음 홉 확인 cmdlet으로 전달됩니다.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>가상 컴퓨터 가져오기

Next Hop이 가상 컴퓨터에서 다음 홉과 다음 홉의 IP 주소를 반환합니다. 가상 컴퓨터의 ID가 cmdlet에 대해 필요합니다. 사용할 가상 컴퓨터의 ID를 이미 알고 있는 경우 이 단계를 건너뛸 수 있습니다.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> 다음 홉에서는 VM 리소스가 실행되기 위해 할당되어야 합니다.

## <a name="get-the-network-interfaces"></a>네트워크 인터페이스 가져오기

가상 컴퓨터에 있는 NIC의 IP 주소가 필요합니다. 이 예제에서는 가상 컴퓨터의 NIC를 검색합니다. 가상 컴퓨터에서 테스트하려는 IP 주소를 이미 알고 있는 경우 이 단계를 건너뛸 수 있습니다.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkInterfaceIDs.ForEach({$_})}
```

## <a name="get-next-hop"></a>다음 홉 가져오기

이제 `Get-AzureRmNetworkWatcherNextHop` cmdlet을 호출합니다. cmdlet을 Network Watcher, 가상 컴퓨터 ID, 원본 IP 주소 및 대상 IP 주소에 전달합니다. 이 예제에서 대상 IP 주소는 다른 가상 네트워크의 VM입니다. 두 개의 가상 네트워크 간에 가상 네트워크 게이트웨이가 있습니다.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>결과 검토

완료되면 결과가 제공됩니다. 리소스의 유형뿐만 아니라 다음 홉 IP 주소가 반환됩니다. 이 시나리오에서는 가상 네트워크 게이트웨이의 공용 IP 주소입니다.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
```

다음 목록에서는 현재 사용할 수 있는 NextHopType 값을 보여 줍니다.

**다음 홉 유형**

* 인터넷
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* 없음

## <a name="next-steps"></a>다음 단계

[Network Watcher를 사용하여 NSG 감사](network-watcher-nsg-auditing-powershell.md)를 방문하여 네트워크 보안 그룹 설정을 프로그래밍 방식으로 검토하는 방법에 대해 알아봅니다.


















