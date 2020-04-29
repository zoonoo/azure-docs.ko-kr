---
title: Always On VPN 사용자 터널 구성
titleSuffix: Azure VPN Gateway
description: 이 문서에서는 VPN gateway에 대 한 Always On VPN 사용자 터널을 구성 하는 방법을 설명 합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502265"
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
