---
title: 상시 켜기 VPN 터널 구성
titleSuffix: Azure Virtual WAN
description: 가상 WAN에 대한 항상 VPN 장치 터널을 구성하는 단계
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502855"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>가상 WAN을 위한 항상 켜져 있는 VPN 장치 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>사전 요구 사항

사이트 간 구성을 작성하고 가상 허브 할당을 편집해야 합니다. 지침은 다음 섹션을 참조하십시오.

* [P2S 구성 만들기](virtual-wan-point-to-site-portal.md#p2sconfig)
* [허브 할당 편집](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>장치 터널 구성

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>프로파일을 제거하려면

프로파일을 제거하려면 다음 명령을 실행합니다.

![정리](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>다음 단계

가상 WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하십시오.