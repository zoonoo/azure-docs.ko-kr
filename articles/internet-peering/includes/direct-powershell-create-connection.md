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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774303"
---
다음 예에서는 시애틀에서 10Gbps Direct 피어 링을 만드는 방법을 보여 줍니다.

PowerShell cmdlet **AzPeeringDirectConnectionObject** 를 사용 하 여 새 피어 링 요청에 사용할 directconnections를 만듭니다.

다음은 DirectConnection을 만드는 예입니다.

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
> 위의 예제에서 [<index>] $peeringLocation에 대 한 <index>은 사용자가 선택한 피어 링 위치와 일치 해야 합니다.

지정 된 피어 링 위치에서 중복성이 필요한 경우 다른 연결을 만듭니다.

```powershell
$connection2 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.33.0/31 `
    -SessionPrefixV6 fe01::3f:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

PowerShell cmdlet **AzPeering** 를 사용 하 여 새 직접 피어 링을 만듭니다. 이 명령에는 아래와 같이 검색할 수 있는 ASN 리소스 ID가 필요 합니다.


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

요청이 성공적으로 처리 된 경우의 예제 응답은 다음과 같습니다.

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
위의 출력에서 **{subscriptionId}** 대신 실제 구독 ID가 표시 됩니다.
