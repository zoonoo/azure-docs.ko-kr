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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774290"
---
PowerShell cmdlet **AzPeeringLocation** 는 필수 매개 변수 `Kind`를 사용 하 여 피어 링 위치 목록을 반환 합니다 .이 위치는 이후 단계에서 사용할 수 있습니다.

```powershell
Get-AzPeeringLocation -Kind Direct
```

직접 피어 링 위치에는 다음 필드가 포함 됩니다.
* PeeringLocation 
* 국가
* PeeringDBFacilityId
* PeeringDBFacilityLink
* 밴드 (와이드)

[Peeringdb](https://w www.peeringdb.com)를 참조 하 여 원하는 피어 링 기능에 있는지 확인 합니다.

다음은 피어 링 위치로 시애틀을 사용 하 여 직접 피어 링을 만드는 방법을 보여 주는 예입니다.

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