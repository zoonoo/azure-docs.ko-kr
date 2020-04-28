---
title: 'VPN Gateway: 다른 사용자 그룹에 대 한 Azure AD 테 넌 트: Azure AD 인증'
description: Azure AD 인증을 사용 하 여 VNet에 연결 하는 데 P2S VPN을 사용할 수 있습니다.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485725"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN 프로토콜 연결에 대 한 Azure Active Directory 테 넌 트 만들기

VNet에 연결 하는 경우 인증서 기반 인증 또는 RADIUS 인증을 사용할 수 있습니다. 그러나 오픈 VPN 프로토콜을 사용 하는 경우 Azure Active Directory 인증을 사용할 수도 있습니다. 다른 사용자가 다른 VPN gateway에 연결할 수 있도록 하려면 AD에 여러 앱을 등록 하 고 다른 VPN 게이트웨이에 연결할 수 있습니다. 이 문서는 P2S OpenVPN 인증을 위해 Azure AD 테 넌 트를 설정 하 고 여러 사용자 및 그룹에 대해 서로 다른 액세스를 허용 하기 위해 Azure AD에서 여러 앱을 만들고 등록 하는 데 도움이 됩니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN® 프로토콜 연결에만 지원됩니다.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. 게이트웨이에서 인증 사용

이 단계에서는 VPN 게이트웨이에서 Azure AD 인증을 사용 하도록 설정 합니다.

1. 다음 명령을 실행 하 여 VPN 게이트웨이에서 Azure AD 인증을 사용 하도록 설정 합니다. 사용자 환경에 맞게 명령을 수정 해야 합니다.

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > 위의 명령에서 Azure VPN 클라이언트의 응용 프로그램 ID를 사용 하지 마세요. 모든 사용자에 게 VPN gateway에 대 한 액세스를 부여 합니다. 등록 한 응용 프로그램의 ID를 사용 합니다.

2. 다음 명령을 실행 하 여 프로필을 만들고 다운로드 합니다. -ResourcGroupName 및-Name 값을 사용자가 직접 일치 하도록 변경 합니다.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. 명령을 실행 한 후 아래와 비슷한 결과가 표시 됩니다. 결과 URL을 브라우저에 복사 하 여 프로필 zip 파일을 다운로드 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. 다운로드 한 zip 파일을 추출 합니다.

5. 압축을 푼 "AzureVPN" 폴더로 이동 합니다.

6. "Azurevpnconfig" 파일의 위치를 적어둡니다. Azurevpnconfig에는 VPN 연결에 대 한 설정이 포함 되어 있으며, Azure VPN 클라이언트 응용 프로그램으로 직접 가져올 수 있습니다. 또한 전자 메일 또는 다른 방법을 통해 연결 해야 하는 모든 사용자에 게이 파일을 배포할 수 있습니다. 성공적으로 연결 하려면 사용자에 게 유효한 Azure AD 자격 증명이 필요 합니다.

## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결 하려면 VPN 클라이언트 프로필을 만들고 구성 해야 합니다. [P2S vpn 연결에 대 한 vpn 클라이언트 구성](openvpn-azure-ad-client.md)을 참조 하세요.
