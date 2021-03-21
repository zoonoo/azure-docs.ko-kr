---
title: 'Azure 가상 WAN: 사용자 VPN 클라이언트 프로필'
description: 클라이언트 프로필 파일을 사용하는 데 도움이 됩니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980918"
---
# <a name="working-with-user-vpn-client-profile-files"></a>사용자 VPN 클라이언트 프로필 파일 작업

프로필 파일에는 VPN 연결을 구성 하는 데 필요한 정보가 포함 되어 있습니다. 이 문서는 사용자 VPN 클라이언트 프로필에 필요한 정보를 얻고 이해 하는 데 도움이 됩니다.

## <a name="download-the-profile"></a>프로필 다운로드

[프로필 다운로드](global-hub-profile.md) 문서의 단계를 사용 하 여 클라이언트 프로필 zip 파일을 다운로드할 수 있습니다.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN 폴더** 에는 *ovpn* 프로필이 포함되어 있으며, 키 및 인증서를 포함하도록 이 프로필을 수정해야 합니다. 자세한 내용은 [OpenVPN 클라이언트 구성](../virtual-wan/howto-openvpn-clients.md#windows)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

가상 WAN 사용자 VPN에 대 한 자세한 내용은 [사용자 vpn 연결 만들기](virtual-wan-point-to-site-portal.md)를 참조 하세요.
