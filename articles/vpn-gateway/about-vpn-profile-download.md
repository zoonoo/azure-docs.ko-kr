---
title: 'Azure VPN Gateway: P2S VPN 클라이언트 프로필 정보'
description: 이렇게 하면 클라이언트 프로필 파일을 사용할 수 있습니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528509"
---
# <a name="about-p2s-vpn-client-profiles"></a>P2S VPN 클라이언트 프로필 정보

다운로드 한 프로필 파일에는 VPN 연결을 구성 하는 데 필요한 정보가 포함 되어 있습니다. 이 문서는 VPN 클라이언트 프로필에 필요한 정보를 얻고 이해 하는 데 도움이 됩니다.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Openvpn 폴더** 에는 키와 인증서를 포함 하도록 수정 해야 하는 *ovpn* 프로필이 포함 되어 있습니다. 자세한 내용은 [Azure VPN Gateway에 대 한 OpenVPN 클라이언트 구성](vpn-gateway-howto-openvpn-clients.md#windows)을 참조 하세요. VPN 게이트웨이에서 Azure AD 인증을 선택 하는 경우이 폴더는 zip 파일에 표시 되지 않습니다. 대신, azurevpnconfig는 AzureVPN 폴더에 있게 됩니다.

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 정보에 대 한 자세한 내용은 [지점 및 사이트](point-to-site-about.md)간 정보를 참조 하세요.
