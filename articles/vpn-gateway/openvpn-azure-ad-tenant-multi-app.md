---
title: 'VPN 게이트웨이: 다른 사용자 그룹에 대한 Azure AD 테넌트: Azure AD 인증'
description: Azure AD 인증을 사용하여 P2S VPN을 사용하여 VNet에 연결할 수 있습니다.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485725"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN 프로토콜 연결을 위한 Azure Active Directory 테넌트 만들기

VNet에 연결할 때 인증서 기반 인증 또는 RADIUS 인증을 사용할 수 있습니다. 그러나 Open VPN 프로토콜을 사용하는 경우 Azure Active Directory 인증을 사용할 수도 있습니다. 다른 사용자 집합이 다른 VPN 게이트웨이에 연결할 수 있도록 하려면 AD에 여러 앱을 등록하고 다른 VPN 게이트웨이에 연결할 수 있습니다. 이 문서에서는 P2S OpenVPN 인증에 대한 Azure AD 테넌트를 설정하고 Azure AD에서 여러 앱을 만들고 등록하여 서로 다른 사용자 및 그룹에 대해 서로 다른 액세스를 허용하는 데 도움이 됩니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN® 프로토콜 연결에만 지원됩니다.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. 게이트웨이에서 인증 사용

이 단계에서는 VPN 게이트웨이에서 Azure AD 인증을 사용하도록 설정합니다.

1. 다음 명령을 실행하여 VPN 게이트웨이에서 Azure AD 인증을 활성화합니다. 사용자 고유의 환경을 반영하도록 명령을 수정해야 합니다.

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > 위의 명령에서 Azure VPN 클라이언트의 응용 프로그램 ID를 사용하지 마십시오: 모든 사용자에게 VPN 게이트웨이에 대한 액세스 권한을 부여합니다. 등록한 응용 프로그램의 ID를 사용합니다.

2. 다음 명령을 실행하여 프로필을 만들고 다운로드합니다. -ResourcGroupName 및 -Name 값을 사용자 고유의 값과 일치하도록 변경합니다.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. 명령을 실행한 후 아래와 유사한 결과가 표시됩니다. 결과 URL을 브라우저에 복사하여 프로필 zip 파일을 다운로드합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. 다운로드한 zip 파일을 추출합니다.

5. 압축해제된 "AzureVPN" 폴더를 찾아봅습니다.

6. "azurevpnconfig.xml" 파일의 위치를 기록합니다. azurevpnconfig.xml에는 VPN 연결에 대한 설정이 포함되어 있으며 Azure VPN 클라이언트 응용 프로그램으로 직접 가져올 수 있습니다. 이 파일을 전자 메일 이나 다른 수단을 통해 연결 해야 하는 모든 사용자에 게 배포할 수도 있습니다. 사용자가 성공적으로 연결하려면 유효한 Azure AD 자격 증명이 필요합니다.

## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결하려면 VPN 클라이언트 프로필을 만들고 구성해야 합니다. [P2S VPN 연결에 대한 VPN 클라이언트 구성을](openvpn-azure-ad-client.md)참조하십시오.
