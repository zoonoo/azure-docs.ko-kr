---
title: 'Azure VPN Gateway: P2S VPN 클라이언트 프로필 정보'
description: 이 문서를 사용 하 여 VPN 클라이언트 프로필에 필요한 정보를 찾을 수 있습니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985936"
---
# <a name="about-p2s-vpn-client-profiles"></a>P2S VPN 클라이언트 프로필 정보

다운로드한 프로필 파일에는 VPN 연결을 구성하는 데 필요한 정보가 포함되어 있습니다. 이 문서는 VPN 클라이언트 프로필에 필요한 정보를 얻고 이해하는 데 도움이 됩니다.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN 폴더**에는 *ovpn* 프로필이 포함되어 있으며, 키 및 인증서를 포함하도록 이 프로필을 수정해야 합니다. 자세한 내용은 [Azure VPN Gateway에서 OpenVPN 클라이언트 구성](vpn-gateway-howto-openvpn-clients.md#windows)을 참조하세요. VPN 게이트웨이에서 Azure AD 인증을 선택하는 경우 이 폴더는 zip 파일에 표시되지 않습니다. 대신 AzureVPN 폴더로 이동하여 azurevpnconfig.xml을 찾습니다.

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 VPN에 대한 자세한 내용은 [지점 및 사이트 간 정보](point-to-site-about.md)를 참조하세요.
