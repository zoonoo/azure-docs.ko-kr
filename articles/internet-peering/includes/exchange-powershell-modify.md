---
title: 포함 파일
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678633"
---
Exchange 피어링에는 다음 수정 작업이 지원됩니다.
* Exchange 피어링 연결을 추가합니다.
* Exchange 피어링 연결을 제거합니다.
* 활성 연결에 IPv4 또는 IPv6 세션을 추가합니다.
* 활성 연결에서 IPv4 또는 IPv6 세션을 제거합니다.


### <a name="add-exchange-peering-connections"></a>Exchange 피어링 연결 추가

이 예제는 기존 Exchange 피어링에 연결을 추가하는 방법을 설명합니다.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Exchange 피어링 연결 제거

이 예제는 기존 Exchange 피어링에 대한 연결을 제거하는 방법을 설명합니다.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

모든 연결을 보고 제거할 연결을 선택합니다. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

다음 명령에서 0 대신 제거할 연결의 인덱스 번호를 입력합니다.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>활성 연결에 IPv4 또는 IPv6 세션 추가

이 예제는 기존 Exchange 연결에 IPv6 세션을 추가하는 방법을 설명합니다.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>활성 연결에서 IPv4 또는 IPv6 세션 제거

기존 연결에서 IPv4 또는 IPv6 세션을 제거하는 것은 현재 PowerShell에서 지원되지 않습니다. 자세한 내용은 [Microsoft 피어링](mailto:peeringexperience@microsoft.com)에 문의하십시오.