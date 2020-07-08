---
title: Always On VPN 터널 구성
titleSuffix: Azure Virtual WAN
description: 가상 WAN에 대 한 Always On VPN 장치 터널을 구성 하는 단계
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/22/2020
ms.author: cherylmc
ms.openlocfilehash: 7adeb9682336b19cc87d8c73a7f9ad8bda5e7828
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564063"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>가상 WAN에 대 한 Always On VPN 장치 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>사전 요구 사항

지점 및 사이트 간 구성을 만들고 가상 허브 할당을 편집 해야 합니다. 지침은 다음 섹션을 참조 하세요.

* [P2S 구성 만들기](virtual-wan-point-to-site-portal.md#p2sconfig)
* [P2S gateway를 사용 하 여 허브 만들기](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>장치 터널 구성

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>프로필을 제거 하려면

프로필을 제거 하려면 다음 명령을 실행 합니다.

![정리](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.