---
title: Always On VPN 사용자 터널 구성
titleSuffix: Azure Virtual WAN
description: 이 문서에서는 가상 WAN에 대 한 Always On VPN 사용자 터널을 구성 하는 방법을 설명 합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/22/2020
ms.author: cherylmc
ms.openlocfilehash: 03f67053a5a199c8c64efb05d2b6a65ad6707650
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564051"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>가상 WAN에 대 한 Always On VPN 사용자 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>사전 요구 사항

지점 및 사이트 간 구성을 만들고 가상 허브 할당을 편집 해야 합니다. 지침은 다음 섹션을 참조 하세요.

* [P2S 구성 만들기](virtual-wan-point-to-site-portal.md#p2sconfig)
* [P2S gateway를 사용 하 여 허브 만들기](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>사용자 터널 구성

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>프로필을 제거 하려면

프로필을 제거 하려면 다음 단계를 사용 합니다.

1. 다음 명령을 실행합니다.

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 연결을 끊고 **자동으로 연결** 확인란의 선택을 취소 합니다.

   ![정리](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
