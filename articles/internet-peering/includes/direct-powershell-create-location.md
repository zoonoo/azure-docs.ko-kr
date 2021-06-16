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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65f282a63e95530549bd0db35c2f42fe5c25993d
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720061"
---
PowerShell cmdlet **Get-AzPeeringLocation** 은 필수 매개 변수를 사용하여 이후 단계에서 사용할 필수 매개 변수 `Kind`가 있는 피어링 위치 목록을 반환합니다.

```powershell
Get-AzPeeringLocation -Kind Direct
```

직접 피어링 위치에는 다음 필드가 포함됩니다.
* PeeringLocation 
* 국가
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

[PeeringDB](https://wwww.peeringdb.com)를 참조하여 원하는 피어링 기능에 있는지 확인합니다.

이 예제에서는 피어링 위치로 시애틀을 사용하여 직접 피어링을 만드는 방법을 보여줍니다.

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
