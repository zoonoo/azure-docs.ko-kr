---
title: Always On VPN 사용자 터널 구성
titleSuffix: Azure Virtual WAN
description: 이 문서에서는 가상 WAN에 대한 Always On VPN 사용자 터널을 구성하는 방법을 설명합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e83ca64d2b0e50ec02007a3cd878e6bf034d0961
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313589"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>가상 WAN에 대한 Always On VPN 사용자 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>필수 구성 요소

지점 및 사이트 간 구성을 생성하고 가상 허브 할당을 편집해야 합니다. 지침은 다음 섹션을 참조하세요.

* [P2S 구성 만들기](virtual-wan-point-to-site-portal.md#p2sconfig)
* [P2S gateway를 통해 허브 만들기](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>사용자 터널 구성

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>프로필을 제거하려면

프로필을 제거하려면 다음 단계를 사용합니다.

1. 다음 명령 실행:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 연결을 끊고 **자동으로 연결** 확인란의 선택을 취소합니다.

   ![정리](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
