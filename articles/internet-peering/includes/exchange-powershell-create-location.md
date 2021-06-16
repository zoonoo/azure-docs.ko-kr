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
ms.openlocfilehash: da284bd65483392d60442bec62394c008ca38a92
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110721655"
---
PowerShell cmdlet **Get-AzPeeringLocation** 은 필수 매개 변수를 사용하여 이후 단계에서 사용할 필수 매개 변수 `Kind`가 있는 피어링 위치 목록을 반환합니다.

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Exchange 피어링 위치에는 다음 필드가 포함됩니다.
* ExchangeName
* PeeringLocation
* 국가
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Address
* MicrosoftIPv6Address

[PeeringDB](https://www.peeringdb.com)를 참조하여 원하는 피어링 기능에 있는지 확인합니다.

이 예제에서는 피어링 위치로 시애틀을 사용하여 피어링을 만드는 방법을 보여줍니다.

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
