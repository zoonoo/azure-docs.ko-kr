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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680819"
---
이 섹션에서는 직접 피어 링에 대해 다음과 같은 수정 작업을 수행 하는 방법을 설명 합니다.

* 직접 피어 링 연결을 추가 합니다.
* 직접 피어 링 연결을 제거 합니다.
* 활성 연결의 대역폭을 업그레이드 하거나 다운 그레이드 합니다.
* 활성 연결에 IPv4 또는 IPv6 세션을 추가 합니다.
* 활성 연결에서 IPv4 또는 IPv6 세션을 제거 합니다.

### <a name="add-direct-peering-connections"></a>직접 피어 링 연결 추가

이 예에서는 기존 직접 피어 링에 대 한 연결을 추가 하는 방법을 설명 합니다.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>직접 피어 링 연결 제거

현재 PowerShell에서 연결 제거는 지원 되지 않습니다. 자세한 내용은 [Microsoft 피어 링](mailto:peeringexperience@microsoft.com)에 문의 하세요.

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>활성 연결의 대역폭 업그레이드 또는 다운 그레이드

이 예에서는 기존 직접 연결에 10gbps를 추가 하는 방법을 설명 합니다.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>활성 연결에 IPv4 또는 IPv6 세션 추가

이 예에서는 IPv4 세션만 사용 하 여 기존 직접 연결에서 IPv6 세션을 추가 하는 방법을 설명 합니다. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>활성 연결에서 IPv4 또는 IPv6 세션 제거

기존 연결에서 IPv4 또는 IPv6 세션을 제거 하는 것은 현재 PowerShell에서 지원 되지 않습니다. 자세한 내용은 [Microsoft 피어 링](mailto:peeringexperience@microsoft.com)에 문의 하세요.