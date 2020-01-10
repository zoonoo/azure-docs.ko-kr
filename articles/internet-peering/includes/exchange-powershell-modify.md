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
ms.openlocfilehash: 86e1a9cce1864ce259fe07b6949be2e32be242a8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774420"
---
Exchange 피어 링에 대해 다음 수정 작업이 지원 됩니다.
1. Exchange 피어 링 연결 추가
1. Exchange 피어 링 연결 제거
1. 활성 연결에 IPv4/IPv6 세션을 추가 합니다.
1. 활성 연결에서 IPv4/IPv6 세션을 제거 합니다.


### <a name="add-exchange-peering-connections"></a>Exchange 피어 링 연결 추가

아래 예제에서는 기존 Exchange 피어 링에 대 한 연결을 추가 하는 방법을 설명 합니다.

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

### <a name="remove-exchange-peering-connections"></a>Exchange 피어 링 연결 제거

아래 예제에서는 기존 Exchange 피어 링에 대 한 연결을 제거 하는 방법을 설명 합니다.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

모든 연결을 확인 하 고 제거 하려는 연결을 선택 합니다. 

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

아래 명령에서 0 대신 제거 하려는 연결에 대 한 인덱스 번호를 입력 합니다.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>활성 연결에 IPv4/IPv6 세션 추가

아래 예제에서는 기존 exchange 연결에 IPv6 세션을 추가 하는 방법을 설명 합니다.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>활성 연결에서 IPv4/IPv6 세션 제거

기존 연결에서 IPv4/IPv6 세션을 제거 하는 기능은 현재 PowerShell에서 지원 되지 않습니다. [Microsoft 피어 링](mailto:peeringexperience@microsoft.com)에 문의 하세요.