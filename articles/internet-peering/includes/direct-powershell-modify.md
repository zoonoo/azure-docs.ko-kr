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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774264"
---
이 섹션에서는 직접 피어링에 대해 다음과 같은 수정 작업을 수행하는 방법에 대해 설명합니다.

* 직접 피어링 연결 추가
* 직접 피어링 연결 제거
* 활성 연결에서 대역폭을 업그레이드하거나 다운그레이드합니다.
* 활성 연결에 IPv4/IPv6 세션을 추가합니다.
* 활성 연결에서 IPv4/IPv6 세션을 제거합니다.

### <a name="add-direct-peering-connections"></a>직접 피어링 연결 추가

아래 예제는 기존 직접 피어링에 연결을 추가하는 방법을 설명합니다.

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

### <a name="remove-direct-peering-connections"></a>직접 피어링 연결 제거

연결 제거는 현재 PowerShell에서 지원되지 않습니다. [연락처 마이크로 소프트 피어링](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>활성 연결에서 대역폭 업그레이드 또는 다운그레이드

아래 예제는 기존 직접 연결에 10Gbps를 추가하는 방법을 설명합니다.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>활성 연결에 IPv4/IPv6 세션을 추가합니다.

아래 예제는 IPv4 세션만 있는 기존 직접 연결에 IPv6 세션을 추가하는 방법을 설명합니다. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>활성 연결에서 IPv4/IPv6 세션을 제거합니다.

기존 연결에서 IPv4/IPv6 세션을 제거하는 것은 현재 PowerShell에서 지원되지 않습니다. [연락처 마이크로 소프트 피어링](mailto:peeringexperience@microsoft.com).