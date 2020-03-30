---
title: 'Azure VPN 게이트웨이: P2S VPN 클라이언트 프로필 정보'
description: 이렇게 하면 클라이언트 프로필 파일로 작업하는 데 도움이 됩니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528509"
---
# <a name="about-p2s-vpn-client-profiles"></a>P2S VPN 클라이언트 프로필 정보

다운로드한 프로필 파일에는 VPN 연결을 구성하는 데 필요한 정보가 포함되어 있습니다. 이 문서는 VPN 클라이언트 프로필에 필요한 정보를 얻고 이해하는 데 도움이 됩니다.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN 폴더에는** 키와 인증서를 포함하도록 수정해야 하는 *ovpn* 프로필이 포함되어 있습니다. 자세한 내용은 [Azure VPN 게이트웨이에 대한 OpenVPN 클라이언트 구성을](vpn-gateway-howto-openvpn-clients.md#windows)참조하십시오. VPN 게이트웨이에서 Azure AD 인증을 선택한 경우 이 폴더는 zip 파일에 없습니다. 대신 azurevpnconfig.xml은 AzureVPN 폴더에 있습니다.

## <a name="next-steps"></a>다음 단계

지점 간 사이트에 대한 자세한 내용은 [지점 간 정보](point-to-site-about.md)를 참조하십시오.
