---
title: 파일 포함
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: fa8d8ccef7d6ad6e1b5d9f19de61e45ee8c439fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82587828"
---
PowerShell cmdlet **AzPeeringLocation** 는 필수 매개 변수를 사용 하 여 피어 링 위치 목록을 반환 합니다 `Kind` .이 위치는 이후 단계에서 사용할 수 있습니다.

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Exchange 피어 링 위치에는 다음 필드가 포함 됩니다.
* ExchangeName
* PeeringLocation
* 국가
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Address
* MicrosoftIPv6Address

[Peeringdb](https://www.peeringdb.com)를 참조 하 여 원하는 피어 링 기능에 있는지 확인 합니다.

이 예에서는 피어 링 위치로 시애틀을 사용 하 여 피어 링을 만드는 방법을 보여 줍니다.

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```
