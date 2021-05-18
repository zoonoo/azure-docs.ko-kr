---
title: 'VPN Gateway: 다른 사용자 그룹에 대한 Azure AD 테넌트: Azure AD 인증'
description: Azure AD 인증을 사용하여 VNet에 연결하기 위해 P2S VPN 사용 가능
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 1305ca603aef63dafcc7b055d55e3f0fe281f4fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91819677"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN 프로토콜 연결을 위한 AD(Active Directory) 테넌트 만들기

VNet에 연결하는 경우 인증서 기반 인증 또는 RADIUS 인증을 사용할 수 있습니다. 그러나 Open VPN 프로토콜을 사용하는 경우 Azure Active Directory 인증을 사용할 수도 있습니다. 다양한 사용자 집합이 서로 다른 VPN Gateway에 연결할 수 있도록 하려면 AD에 여러 앱을 등록하고 다른 VPN Gateway에 연결할 수 있습니다. 이 문서는 P2S OpenVPN 인증을 위해 Azure AD 테넌트를 설정하고 Azure AD에서 여러 앱을 만들고 등록하여 다른 사용자 및 그룹에 대해 서로 다른 액세스를 허용하도록 도와줍니다.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. 게이트웨이에서 인증 사용

이 단계에서는 VPN Gateway에서 Azure AD 인증을 사용하도록 설정합니다.

1. **지점 및 사이트 간 구성** 으로 이동하고 **OpenVPN(SSL)** 을 **터널 유형** 으로 선택하여 VPN Gateway에서 Azure AD 인증을 사용하도록 설정합니다. **Azure Active Directory** 를 **인증 유형** 으로 선택하고, **Azure Active Directory** 섹션 아래에 정보를 입력합니다.

    ![Azure Portal 보기](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Azure VPN Client의 애플리케이션 ID를 사용하지 마세요. 모든 사용자에게 VPN Gateway에 대한 액세스 권한을 부여합니다. 등록한 애플리케이션의 ID를 사용합니다.

2. **VPN 클라이언트 다운로드** 링크를 클릭하여 프로필을 만들고 다운로드합니다.

3. 다운로드한 zip 파일의 압축을 풉니다.

4. 압축 해제된 ‘AzureVPN’ 폴더로 이동합니다.

5. ‘azurevpnconfig.xml’ 파일의 위치를 기록합니다. azurevpnconfig.xml에는 VPN 연결에 대한 설정이 포함되어 있으며, Azure VPN Client 애플리케이션으로 직접 가져올 수 있습니다. 또한 이메일 또는 다른 방법을 통해 연결해야 하는 모든 사용자에게 이 파일을 배포할 수 있습니다. 성공적으로 연결하려면 사용자에게 유효한 Azure AD 자격 증명이 필요합니다.

## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결하려면 VPN 클라이언트 프로필을 만들고 구성해야 합니다. [P2S VPN 연결을 위한 VPN 클라이언트 구성](openvpn-azure-ad-client.md)을 참조하세요.
