---
title: P2S VPN 게이트웨이용 OpenVPN 클라이언트를 구성하는 방법
titleSuffix: Azure VPN Gateway
description: Azure VPN Gateway용 OpenVPN 클라이언트를 구성하는 방법을 알아봅니다. 이 문서는 Windows, Linux, iOS 및 Mac 클라이언트를 구성하는 데 도움을 줍니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/29/2021
ms.author: cherylmc
ms.openlocfilehash: 5a7b125ef89d5d598e22fc4458b5b1c7f8c4a41c
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108289357"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Azure VPN Gateway에 대해 OpenVPN 클라이언트 구성

이 문서는 **OpenVPN &reg; 프로토콜** 클라이언트를 구성하는 데 도움이 됩니다.

## <a name="before-you-begin"></a>시작하기 전에

VPN 게이트웨이에 대해 OpenVPN을 구성하는 단계를 완료했는지 확인합니다. 자세한 내용은 [Azure VPN Gateway에 대해 OpenVPN 구성](vpn-gateway-howto-openvpn.md)을 참조하세요.

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>다음 단계

VPN 클라이언트가 다른 VNet의 리소스에 액세스할 수 있게 하려면 [VNet 간](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 문서의 지침에 따라 vnet 간 연결을 설정합니다. 게이트웨이 및 연결에서 BGP를 사용하도록 설정해야 합니다. 그렇지 않으면 트래픽이 흐르지 않습니다.

**"OpenVPN"은 OpenVPN Inc.의 상표입니다.**
