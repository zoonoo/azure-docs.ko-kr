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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680815"
---
PowerShell cmdlet **Get-AzPeeringLocation은** 이후 단계에서 사용할 필수 `Kind`매개 변수를 사용하여 피어링 위치 목록을 반환합니다.

```powershell
Get-AzPeeringLocation -Kind Direct
```

직접 피어링 위치에는 다음 필드가 포함됩니다.
* 피어링위치 
* 국가
* 피어링DB시설ID
* 피어링DB시설링크
* 대역폭제공

[PeeringDB](https://wwww.peeringdb.com)를 참조하여 원하는 피어링 시설에 있는지 확인합니다.

이 예제에서는 시애틀을 피어링 위치로 사용하여 직접 피어링을 만드는 방법을 보여 주며 있습니다.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```