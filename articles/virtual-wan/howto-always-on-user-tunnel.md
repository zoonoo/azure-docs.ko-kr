---
title: Always On VPN 사용자 터널 구성
titleSuffix: Azure Virtual WAN
description: Virtual WAN에 대한 Always On VPN 사용자 터널을 구성하는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: 65cddc3c850ada4e89e23da4d900afbd7476ce13
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579721"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>가상 WAN에 대한 Always On VPN 사용자 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>필수 구성 요소

지점 및 사이트 간 구성을 생성하고 가상 허브 할당을 편집해야 합니다. 지침은 다음 섹션을 참조하세요.

* [P2S 구성 만들기](virtual-wan-point-to-site-portal.md#p2sconfig)
* [P2S gateway를 통해 허브 만들기](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>사용자 터널 구성

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>프로필 제거 방법

프로필을 제거하려면 다음 단계를 사용합니다.

1. 다음 명령 실행:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 연결을 끊고 **자동으로 연결** 확인란의 선택을 취소합니다.

   ![정리](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
