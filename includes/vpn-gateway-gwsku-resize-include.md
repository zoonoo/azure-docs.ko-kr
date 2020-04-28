---
title: 파일 포함
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "73495735"
---
PowerShell cmdlet을 사용 `Resize-AzVirtualNetworkGateway` 하 여 Generation1.xml 또는 Generation2 SKU를 업그레이드 하거나 다운 그레이드할 수 있습니다. 기본 sku를 제외 하 고 모든 VpnGw sku의 크기를 조정할 수 있습니다. 기본 게이트웨이 SKU를 사용하는 경우 [대신 이러한 지침을 사용하여](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) 게이트웨이의 크기를 조정합니다.

다음 PowerShell 예제에서는 VpnGw2로 크기가 조정되는 게이트웨이 SKU를 보여 줍니다.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

가상 네트워크 게이트웨이에 대한 **구성** 페이지로 이동하고 드롭다운 목록에서 다른 SKU를 선택하여 Azure Portal에서 게이트웨이의 크기를 조정할 수도 있습니다.
