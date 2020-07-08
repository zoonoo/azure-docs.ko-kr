---
title: Always On VPN 사용자 터널 구성
titleSuffix: Azure VPN Gateway
description: 이 문서에서는 VPN gateway에 대 한 Always On VPN 사용자 터널을 구성 하는 방법을 설명 합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8cfe1d6434c0f5765196776ae9f6712fe14c52a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984133"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Always On VPN 사용자 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>게이트웨이 구성

 [지점 및 사이트 간 vpn 연결 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md) 문서의 지침을 사용 하 여 IKEv2 및 인증서 기반 인증을 사용 하도록 VPN gateway를 구성 합니다.

## <a name="configure-a-user-tunnel"></a>사용자 터널 구성

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>프로필을 제거 하려면

프로필을 제거 하려면 다음 단계를 사용 합니다.

1. 다음 명령을 실행합니다.

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 연결을 끊고 **자동으로 연결** 확인란의 선택을 취소 합니다.

   ![정리](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>다음 단계

발생할 수 있는 연결 문제를 해결 하려면 [Azure 지점 및 사이트 간 연결 문제](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)를 참조 하세요.
