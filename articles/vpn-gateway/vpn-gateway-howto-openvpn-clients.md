---
title: Azure VPN Gateway에 대해 OpenVPN 클라이언트를 구성하는 방법 | Microsoft Docs
description: Windows, Linux 및 Mac 운영 체제 클라이언트용 Azure VPN Gateway에 대해 OpenVPN을 구성 하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 92447a541726c1c11b7b10d6d52cf91cfc07f945
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036864"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Azure VPN Gateway에 대 한 OpenVPN 클라이언트 구성

이 문서는 **Openvpn &reg; 프로토콜** 클라이언트를 구성 하는 데 도움이 됩니다.

## <a name="before-you-begin"></a>시작하기 전에

VPN 게이트웨이에 대해 OpenVPN을 구성하는 단계를 완료했는지 확인합니다. 자세한 내용은 [Azure VPN Gateway에 대해 OpenVPN 구성](vpn-gateway-howto-openvpn.md)을 참조하세요.

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>다음 단계

VPN 클라이언트가 다른 VNet의 리소스에 액세스할 수 있도록 [하려면 vnet 간](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 연결에 대 한 지침에 따라 vnet 간 연결을 설정 합니다. 게이트웨이 및 연결에서 BGP를 사용하도록 설정해야 합니다. 그렇지 않으면 트래픽이 흐르지 않습니다.

**"OpenVPN"은 OpenVPN i n c .의 상표입니다.**
