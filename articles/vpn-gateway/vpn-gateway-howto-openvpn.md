---
title: 'Azure VPN Gateway에 대해 OpenVPN을 구성하는 방법: PowerShell| Microsoft Docs'
description: Azure VPN Gateway에 대해 OpenVPN을 구성하는 단계
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 609c2ef91fafe0ae955252a594292d861e772f87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002953"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Azure 지점 및 사이트 VPN Gateway에 대 한 OpenVPN 구성

이 문서에서는 설정할 수 있습니다 **OpenVPN® 프로토콜** Azure VPN Gateway에서 합니다. 이 문서에서는 작동하는 지점-사이트 간 환경이 이미 있다고 가정합니다. 환경이 없는 경우 1단계의 지침을 사용하여 지점-사이트 간 VPN을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1. 지점-사이트 간 VPN 만들기

작동하는 지점-사이트 간 환경이 아직 없는 경우 지침에 따라 환경을 만듭니다. 기본 Azure 인증서 인증을 사용하여 지점-사이트 간 VPN 게이트웨이를 만들고 구성하려면 [지점-사이트 간 VPN 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)를 참조하세요. 

> [!IMPORTANT]
> 기본 SKU는 OpenVPN에서 지원되지 않습니다.

## <a name="enable"></a>2. 게이트웨이에서 OpenVPN 사용

게이트웨이에서 OpenVPN을 사용하도록 설정합니다. 다음 명령을 실행하기 전에 게이트웨이가 지점-사이트 간(IKEv2 또는 SSTP)에 대해 이미 구성되어 있는지 확인합니다.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>다음 단계

OpenVPN 클라이언트를 구성하려면 [OpenVPN 클라이언트 구성](vpn-gateway-howto-openvpn-clients.md)을 참조하세요.

**"OpenVPN"는 OpenVPN i n c.의 상표**
