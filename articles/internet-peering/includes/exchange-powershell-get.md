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
ms.openlocfilehash: 34a23ce76ed0e9285a686073e1cbeb95347f7b7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678642"
---
피어 링 목록을 가져오려면 **AzPeering** 명령을 실행 합니다.

```powershell
Get-AzPeering ResourceGroupName "PeeringResourceGroup" -Name "SeattleExchangePeering"
```

이 예제 응답에서는 종단 간 프로 비전이 성공적으로 완료 된 경우를 보여 줍니다.

```powershell
    Name                     : SeattleExchangePeering
    Sku                      : Basic_Exchange_Free
    Kind                     : Exchange
    PeeringLocation          : Seattle
    ProvisioningState        : Succeeded
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.101
    MicrosoftIPv4Address     : 10.21.31.51
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:101
    MicrosoftIPv6Address     : fe01::3e:51
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```