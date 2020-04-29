---
title: Always On VPN 사용자 터널 구성
titleSuffix: Azure Virtual WAN
description: 이 문서에서는 가상 WAN에 대 한 Always On VPN 사용자 터널을 구성 하는 방법을 설명 합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502868"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>가상 WAN에 대 한 Always On VPN 사용자 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>전제 조건

지점 및 사이트 간 구성을 만들고 가상 허브 할당을 편집 해야 합니다. 지침은 다음 섹션을 참조 하세요.

* [P2S 구성 만들기](virtual-wan-point-to-site-portal.md#p2sconfig)
* [허브 할당 편집](virtual-wan-point-to-site-portal.md#edit)

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

가상 WAN에 대 한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조 하세요.
