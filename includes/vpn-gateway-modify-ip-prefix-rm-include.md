---
title: 파일 포함
description: 파일 포함
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7dd255e9767309c7b273dccfa0ee5675eed18568
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380497"
---
추가 주소 접두사를 추가하려면:

1. LocalNetworkGateway에 대한 변수를 설정합니다.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
1. 접두사를 수정합니다.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
   ```

주소 접두사를 제거하려면:

  더 이상 필요하지 않은 접두사는 생략합니다. 이 예제에서는 이전 예제의 10.101.2.0/24 접두사가 더 이상 필요하지 않으므로 해당 접두사를 제외하고 로컬 네트워크 게이트웨이를 업데이트합니다.

1. LocalNetworkGateway에 대한 변수를 설정합니다.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
1. 업데이트된 접두사를 사용하는 게이트웨이를 설정합니다.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```
