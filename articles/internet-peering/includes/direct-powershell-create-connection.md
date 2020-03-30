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
ms.openlocfilehash: e1cb1bec6273fa79315b9439bec1412622ebfe28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774303"
---
다음 예제에서는 시애틀에서 10Gbps 직접 피어링을 만드는 방법을 보여 주며 있습니다.

PowerShell cmdlet **New-AzPeeringDirectConnectionObject를** 사용하여 새 피어링 요청에 사용할 직접 연결을 만듭니다.

다음은 직접 연결을 만드는 예제입니다.

```powershell
$connection1 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.31.0/31 `
    -SessionPrefixV6 fe01::3e:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

> [!NOTE]
> <index> $peeringLocation[]에<index>대한 용은 위의 예에서 선택한 피어링 위치에 해당해야 합니다.

지정된 피어링 위치에서 중복성이 필요한 경우 다른 연결을 만듭니다.

```powershell
$connection2 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.33.0/31 `
    -SessionPrefixV6 fe01::3f:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

PowerShell cmdlet **New-AzPeering을** 사용하여 새 직접 피어링을 만듭니다. 이 명령에는 아래와 같이 검색할 수 있는 ASN 리소스 ID가 필요합니다.


```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeeringLocation  $peeringLocation[0].PeeringLocation `
    -PeerAsnResourceId $asn.Id `
    -DirectConnection $connection1 [, $connection2]
```
&nbsp;

다음은 요청이 성공적으로 처리되었을 때의 예제 응답입니다.

```powershell

    Name                 : SeattleDirectPeering
    Sku.Name             : Basic_Direct_Free
    Kind                 : Direct
    Connections          : 71
    PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
    UseForPeeringService : False
    PeeringLocation      : Seattle
    ProvisioningState    : Succeeded
    Location             : centralus
    Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
    Type                 : Microsoft.Peering/peerings
    Tags                 : {}

```
위의 출력에 **{subscriptionId}** 대신 실제 구독 ID가 표시됩니다.
