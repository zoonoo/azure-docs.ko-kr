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
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774290"
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

다음은 시애틀을 피어링 위치로 사용하여 직접 피어링을 만드는 방법을 보여 주는 예제입니다.

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