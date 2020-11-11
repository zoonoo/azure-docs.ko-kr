---
title: Azure 가상 WAN에 대 한 OpenVPN 클라이언트 구성
description: Azure 가상 WAN에 대해 OpenVPN 클라이언트를 구성 하는 단계
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491004"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Azure 가상 WAN에 대 한 OpenVPN 클라이언트 구성

이 문서는 **Openvpn &reg; 프로토콜** 클라이언트를 구성 하는 데 도움이 됩니다. Windows 10 용 Azure VPN 클라이언트를 사용 하 여 OpenVPN 프로토콜을 통해 연결할 수도 있습니다. 자세한 지침은 여기를 참조 [하세요](openvpn-azure-ad-client.md) .

## <a name="before-you-begin"></a>시작하기 전에

사용자 VPN (지점 및 사이트 간) 구성을 만듭니다. 터널 형식으로 "OpenVPN"을 선택 했는지 확인 합니다. 단계는 [Azure 가상 WAN에 대 한 P2S 구성 만들기](virtual-wan-point-to-site-portal.md#p2sconfig)를 참조 하세요.

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>다음 단계

사용자 VPN (지점-사이트)에 대 한 자세한 내용은 [사용자 vpn 연결 만들기](virtual-wan-point-to-site-portal.md)를 참조 하세요.

**"OpenVPN"은 OpenVPN i n c .의 상표입니다.**
