---
title: '가상 허브의 유효 경로 보기: Azure 가상 WAN | Microsoft Docs'
description: Azure 가상 WAN의 가상 허브에 대 한 유효 경로를 보는 방법
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 20cdc55b474034480392f9dfb05b20ad25df6939
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037769"
---
# <a name="view-virtual-hub-effective-routes"></a>가상 허브 유효 경로 보기

Azure Portal에서 가상 WAN 허브의 모든 경로를 볼 수 있습니다. 이 문서에서는 유효 경로를 확인 하는 단계를 안내 합니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

> [!NOTE]
> Azure Portal에서 이러한 기능 중 일부는 계속 출시 될 수 있으며 8 월 1 일까 지는 사용할 수 없습니다. 
>

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>연결 또는 경로 테이블 선택

1. 가상 허브로 이동한 다음 **라우팅**을 선택 합니다. 라우팅 페이지에서 **유효 경로**를 선택 합니다.
1. 드롭다운에서 **연결 유형** 또는 **경로 테이블**을 선택할 수 있습니다. 경로 테이블 옵션이 표시 되지 않으면이 가상 허브에 설정 된 사용자 지정 또는 기본 경로 테이블이 없음을 의미 합니다.
1. **연결/경로 테이블**드롭다운 목록에서 다음 항목 중에서 선택할 수 있습니다.

   * 연결 Virtual Network
   * VPN 사이트 연결
   * ExpressRoute 연결
   * 지점 및 사이트 간 연결
   * 경로 테이블

   :::image type="content" source="./media/effective-routes-virtual-hub/routing.png" alt-text="라우팅":::

## <a name="view-output"></a><a name="output"></a>출력 보기

페이지 출력에는 다음 필드가 표시 됩니다.

* **Prefix**: 현재 엔터티에 알려진 주소 접두사입니다.
* **다음 홉 유형**: 연결, VPN_S2S_Gateway, ExpressRouteGateway, 원격 허브 또는 Azure 방화벽 Virtual Network 수 있습니다.
* **다음 홉**: IP 이거나 단순히 현재 허브를 의미 하는 링크 링크를 표시 합니다.
* **원본**: 라우팅 원본의 리소스 ID입니다.
* **As path**: BGP Attribute as (자치 시스템) 경로는 경로가 연결 된 접두사가에서 보급 되는 위치에 도달 하기 위해 통과 해야 하는 모든 AS 번호를 나열 합니다.

### <a name="example"></a><a name="example"></a>예 들어

다음 예제 테이블의 값은 가상 허브 연결 또는 경로 테이블에서 10.2.0.0/24 (분기 접두사)의 경로를 배웠습니다을 의미 합니다. **다음 홉** VPN GATEWAY 리소스 ID로 VPN_S2S_Gateway **VPN 다음 홉 유형** 으로 인해 경로를 배웠습니다. 원본 VPN 게이트웨이/경로 테이블/연결의 리소스 ID를 가리키는 **경로 원본** 입니다. **As path** 는 분기의 as 경로를 나타냅니다.

테이블의 맨 아래에 있는 스크롤 막대를 사용 하 여 "AS Path"를 볼 수 있습니다.

| **접두사** |  **다음 홉 유형** | **다음 홉** |  **경로 원점** |**AS Path** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |10.1.0.6, 10.1.0.7|/subscriptions/ `<sub id>` /Ssourceg/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**고려 사항**

* **유효 경로 가져오기** 출력에 0.0.0.0/0이 표시 되 면 경로 테이블 중 하나에 경로가 있음을 의미 합니다. 그러나이 경로가 인터넷에 대해 설정 된 경우 추가 플래그 **"Enableinternetsecurity": true** 가 연결에 필요 합니다. 연결의 "enableInternetSecurity" 플래그가 "false" 인 경우 VM NIC의 유효 경로에서 경로를 표시 하지 않습니다.

* **기본 경로 전파** 필드는 가상 네트워크 연결, VPN 연결 또는 express 경로 연결을 편집할 때 AZURE 가상 WAN 포털에 표시 됩니다. 이 필드는 **Enableinternetsecurity** 플래그를 나타냅니다 .이 플래그는 기본적으로 express 경로 및 VPN 연결의 경우 항상 "false"이 고, 가상 네트워크 연결의 경우 "true"입니다.

## <a name="next-steps"></a>다음 단계

* 가상 WAN에 대 한 자세한 내용은 [가상 Wan 개요](virtual-wan-about.md)를 참조 하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
