---
title: Azure Virtual WAN에 대해 OpenVPN 클라이언트 구성
description: Azure Virtual WAN에 대해 OpenVPN 클라이언트를 구성하는 방법에 대해 알아봅니다. 이 문서에는 Windows, Mac, iOS 및 Linux 클라이언트 구성 단계가 포함되어 있습니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 83c70aca81eaa888186807d43fff5a7bbaccb700
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579624"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Azure Virtual WAN에 대해 OpenVPN 클라이언트 구성

이 문서는 **OpenVPN &reg; 프로토콜** 클라이언트를 구성하는 데 도움이 됩니다. Windows 10용 Azure VPN Client를 사용하여 OpenVPN 프로토콜을 통해 연결할 수도 있습니다. 자세한 내용은 [P2S OpenVPN 연결을 위한 VPN 클라이언트 구성](openvpn-azure-ad-client.md)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

사용자 VPN(지점 및 사이트 간) 구성 만들기 터널 유형으로 "OpenVPN"을 선택했는지 확인합니다. 단계는 [Azure Virtual WAN용 P2S 구성 만들기](virtual-wan-point-to-site-portal.md#p2sconfig)를 참조하세요.

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>다음 단계

사용자 VPN(지점 간)에 대한 자세한 내용은 [사용자 VPN 연결 만들기](virtual-wan-point-to-site-portal.md)를 참조하세요.

**"OpenVPN"은 OpenVPN Inc.의 상표입니다.**
