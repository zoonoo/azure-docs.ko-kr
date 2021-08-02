---
title: '가상 허브 유효 경로 보기: Azure Virtual WAN | Microsoft Docs'
description: Azure Virtual WAN에서 가상 허브에 대한 유효 경로를 보는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: 955bf9fb9881fcf5fb37b75352c25d2d639bfb3c
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579841"
---
# <a name="view-virtual-hub-effective-routes"></a>가상 허브 유효 경로 보기

Azure Portal에서 Virtual WAN 허브의 모든 경로를 볼 수 있습니다. 이 문서에서는 효과적인 경로를 확인하는 단계를 안내합니다. 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>연결 또는 경로 테이블 선택

1. 가상 허브로 이동한 다음, **라우팅** 을 선택합니다. [라우팅] 페이지에서 **유효 경로** 를 선택합니다.
1. 드롭다운에서 **경로 테이블** 을 선택할 수 있습니다. 경로 테이블 옵션이 표시되지 않으면 사용자 지정 또는 기본 경로 테이블이 이 가상 허브에 설정되지 않은 것입니다.

## <a name="view-output"></a><a name="output"></a>출력 보기

페이지 출력에는 다음 필드가 표시됩니다.

* **접두사:** 현재 엔터티에 알려진 주소 접두사입니다(가상 허브 라우터에서 학습됨).
* **다음 홉 형식:** 가상 네트워크 연결, VPN_S2S_Gateway, ExpressRouteGateway, 원격 허브 또는 Azure Firewall일 수 있습니다.
* **다음 홉:** 다음 홉의 리소스 ID에 대한 링크이거나 단순히 현재 허브를 암시하는 On-link를 표시합니다.
* **원본:** 라우팅 원본의 리소스 ID입니다.
* **AS 경로:** BGP 특성 AS(자치 시스템) 경로에는 경로가 첨부된 접두사가 보급되는 위치에 도달하기 위해 트래버스해야 하는 모든 AS 번호가 나열됩니다.

### <a name="example"></a><a name="example"></a>예제

다음 예제 표의 값은 가상 허브 연결 또는 경로 테이블이 10.2.0.0/24(분기 접두사)의 경로를 학습했음을 의미합니다. VPN Gateway 리소스 ID **다음 홉** 이 있는 VPN_S2S_Gateway **VPN 다음 홉 형식** 으로 인해 경로가 학습되었습니다. **경로 원본** 은 원래 VPN 게이트웨이/경로 테이블/연결의 리소스 ID를 가리킵니다. **AS 경로** 는 분기에 대한 AS 경로를 나타냅니다.

"AS 경로"는 표의 아래쪽에 있는 스크롤 막대를 사용하여 확인할 수 있습니다.

| **Prefix** |  **다음 홉 유형** | **다음 홉** |  **경로 원본** |**AS 경로** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/subscriptions/`<sub id>`/resourceGroups/`<resource group name>`/providers/Microsoft.Network/vpnGateways/vpngw|/subscriptions/`<sub id>`/resourceGroups/`<resource group name>`/providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**고려 사항:**

* **유효 경로 가져오기** 출력에 0.0.0.0/0이 표시되면 경로가 경로 테이블 중 하나에 있음을 의미합니다. 그러나 이 경로가 인터넷에 대해 설정된 경우 연결에 **"enableInternetSecurity": true** 추가 플래그가 필요합니다. 연결의 "enableInternetSecurity" 플래그가 "false"이면 VM NIC의 유효 경로에 경로가 표시되지 않습니다.

* **기본 경로 전파** 필드는 가상 네트워크 연결, VPN 연결 또는 ExpressRoute 연결을 편집할 때 Azure Virtual WAN 포털에 표시됩니다. 이 필드는 ExpressRoute 및 VPN 연결의 경우 항상 기본적으로 "false"이지만, 가상 네트워크 연결의 경우 "true"인 **enableInternetSecurity** 플래그를 나타냅니다.

* VM NIC에서 유효 경로를 볼 때 다음 홉이 '가상 네트워크 게이트웨이'로 표시되면 VM이 Virtual WAN 허브에 연결된 스포크에 있는 경우 가상 허브 라우터를 의미합니다.

* 가상 허브 경로 테이블에 대한 **유효 경로 보기** 는 하나 이상의 연결 형식(VPN/ER/VNET)이 가상 허브에 연결된 경우에만 채워집니다.

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md)를 참조하세요.
* 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
