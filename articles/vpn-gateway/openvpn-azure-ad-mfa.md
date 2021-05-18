---
title: 'VPN 사용자에 대해 MFA 사용: Azure AD 인증'
description: VPN 사용자에 대해 다단계 인증을 사용하도록 설정
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 9ab595d3eb81cc7d1bdd65d1e4c3f4273a2460ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019109"
---
# <a name="enable-azure-ad-multi-factor-authentication-mfa-for-vpn-users"></a>VPN 사용자에 대해 Azure AD 다단계 인증(MFA)을 사용하도록 설정

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>인증 사용

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>로그인 설정 구성

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>옵션 1 - 사용자당 액세스

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>옵션 2 - 조건부 액세스

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결하려면 VPN 클라이언트 프로필을 만들고 구성해야 합니다. [P2S VPN 연결을 위한 VPN 클라이언트 구성](openvpn-azure-ad-client.md)을 참조하세요.