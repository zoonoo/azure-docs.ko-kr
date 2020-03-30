---
title: 상시 켜져 있는 VPN 사용자 터널 구성
titleSuffix: Azure VPN Gateway
description: 이 문서에서는 VPN 게이트웨이에 대해 Always On VPN 사용자 터널을 구성하는 방법에 대해 설명합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502265"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Always On VPN 사용자 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>게이트웨이 구성

 [POINT-To-Site VPN 연결](vpn-gateway-howto-point-to-site-resource-manager-portal.md) 문서의 지침을 사용하여 IKEv2 및 인증서 기반 인증을 사용하도록 VPN 게이트웨이를 구성합니다.

## <a name="configure-a-user-tunnel"></a>사용자 터널 구성

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>프로파일을 제거하려면

프로파일을 제거하려면 다음 단계를 사용합니다.

1. 다음 명령 실행:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 연결을 끊고 연결 **확인란을 자동으로** 선택 취소합니다.

   ![정리](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>다음 단계

발생할 수 있는 연결 문제를 해결하려면 [Azure 지점 간 연결 문제를](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)참조하십시오.
