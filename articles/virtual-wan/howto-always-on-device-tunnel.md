---
title: Always-On VPN 터널 구성
titleSuffix: Azure Virtual WAN
description: 가상 WAN에 대 한 Always On VPN 장치 터널을 구성 하는 단계
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983678"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>가상 WAN에 대 한 Always On VPN 장치 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>필수 구성 요소

지점 및 사이트 간 구성을 만들고 가상 허브 할당을 편집 해야 합니다. 지침은 다음 섹션을 참조 하세요.

* [P2S 구성 만들기](virtual-wan-point-to-site-portal.md#p2sconfig)
* [P2S gateway를 사용 하 여 허브 만들기](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>장치 터널 구성

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>프로필을 제거 하려면

프로필을 제거 하려면 다음 명령을 실행 합니다.

![스크린샷 Remove-VpnConnection-Name MachineCertTest 명령을 실행 하는 PowerShell 창을 보여 줍니다.](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.