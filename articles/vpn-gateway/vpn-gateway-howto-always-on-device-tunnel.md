---
title: 상시 켜기 VPN 터널 구성
titleSuffix: Azure VPN Gateway
description: VPN 게이트웨이를 위해 항상 VPN 터널을 구성하는 단계
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371759"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Always On VPN 디바이스 터널 구성

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>게이트웨이 구성

[POINT-To-Site VPN 연결](vpn-gateway-howto-point-to-site-resource-manager-portal.md) 구성 문서를 사용하여 IKEv2 및 인증서 기반 인증을 사용하도록 VPN 게이트웨이를 구성합니다.

## <a name="configure-the-device-tunnel"></a>장치 터널 구성

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>프로파일을 제거하려면

프로파일을 제거하려면 다음 명령을 실행합니다.

![정리](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>다음 단계

문제 해결은 [Azure 지점 간 연결 문제를 참조하세요.](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
