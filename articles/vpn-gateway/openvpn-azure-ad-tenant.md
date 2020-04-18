---
title: 'VPN 게이트웨이: P2S VPN 연결을 위한 Azure AD 테넌트: Azure AD 인증'
description: Azure AD 인증을 사용하여 P2S VPN을 사용하여 VNet에 연결할 수 있습니다.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: alzam
ms.openlocfilehash: 00db2ed05285a1637414aa1e3adbe3b047ff0568
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641338"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN 프로토콜 연결을 위한 Azure Active Directory 테넌트 만들기

VNet에 연결할 때 인증서 기반 인증 또는 RADIUS 인증을 사용할 수 있습니다. 그러나 Open VPN 프로토콜을 사용하는 경우 Azure Active Directory 인증을 사용할 수도 있습니다. 이 문서에서는 P2S Open VPN 인증을 위한 Azure AD 테넌트를 설정하는 데 도움이 됩니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN® 프로토콜 연결에만 지원됩니다.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Azure AD 테넌트 확인

Azure AD 테넌트가 있는지 확인합니다. Azure AD 테넌트가 없는 경우 [새 테넌트 만들기](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 문서의 단계를 사용하여 테넌트를 만들 수 있습니다.

* 조직 이름
* 초기 도메인 이름

예:

   ![새 Azure AD 테넌트](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD 테넌트 사용자 만들기

Azure AD 테넌트에는 글로벌 관리자 계정과 마스터 사용자 계정과 같은 계정이 필요합니다. 마스터 사용자 계정은 마스터 포함 계정(서비스 계정)으로 사용됩니다. Azure AD 테넌트 사용자 계정을 만들 때 만들려는 사용자 유형에 맞게 디렉터리 역할을 조정합니다.

[이 문서의](../active-directory/fundamentals/add-users-azure-active-directory.md) 단계를 사용하여 Azure AD 테넌트에 대해 두 명 이상의 사용자를 만듭니다. **디렉터리 역할을** 변경하여 계정 유형을 만들어야 합니다.

* 전역 관리자
* 사용자

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. VPN 게이트웨이에서 Azure AD 인증 사용

1. 인증에 사용할 디렉터리 ID를 찾습니다. Active Directory 페이지의 속성 섹션에 나열됩니다.

    ![디렉터리 ID](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. 디렉터리 ID를 복사합니다.

3. **전역 관리자** 역할이 할당된 사용자로 Azure 포털에 로그인합니다.

4. 다음으로 관리자의 동의를 제공합니다. 브라우저의 주소 표시줄에서 배포 위치와 관련된 URL을 복사하여 붙여넣습니다.

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud 독일

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. 메시지가 표시되면 **글로벌 관리자** 계정을 선택합니다.

    ![디렉터리 ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. 메시지가 표시되면 **수락을** 선택합니다.

    ![수락](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Azure AD, 엔터프라이즈 응용 프로그램에서 **Azure VPN이** 나열된 것을 볼 수 **있습니다.**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. 작동하는 지점-사이트 간 환경이 아직 없는 경우 지침에 따라 환경을 만듭니다. 기본 Azure 인증서 인증을 사용하여 지점-사이트 간 VPN 게이트웨이를 만들고 구성하려면 [지점-사이트 간 VPN 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)를 참조하세요. 

    > [!IMPORTANT]
    > 기본 SKU는 OpenVPN에서 지원되지 않습니다.

9. 다음 명령을 실행하여 VPN 게이트웨이에서 Azure AD 인증을 사용하도록 설정하여 사용자 고유의 환경을 반영하도록 명령을 수정해야 합니다.

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>/" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > `AadIssuerUri` 값 끝에 후행 슬래시를 포함해야 합니다. 그렇지 않으면 명령이 실패합니다.

10. 다음 명령을 실행하여 프로필을 만들고 다운로드합니다. -ResourceGroupName 및 -Name 값을 사용자 고유의 값과 일치하도록 변경합니다.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. 명령을 실행한 후 아래와 유사한 결과가 표시됩니다. 결과 URL을 브라우저에 복사하여 프로필 zip 파일을 다운로드합니다.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. 다운로드한 zip 파일을 추출합니다.

13. 압축해제된 "AzureVPN" 폴더를 찾아봅습니다.

14. "azurevpnconfig.xml" 파일의 위치를 기록합니다. azurevpnconfig.xml에는 VPN 연결에 대한 설정이 포함되어 있으며 Azure VPN 클라이언트 응용 프로그램으로 직접 가져올 수 있습니다. 이 파일을 전자 메일 이나 다른 수단을 통해 연결 해야 하는 모든 사용자에 게 배포할 수도 있습니다. 사용자가 성공적으로 연결하려면 유효한 Azure AD 자격 증명이 필요합니다.

## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결하려면 VPN 클라이언트 프로필을 만들고 구성해야 합니다. [P2S VPN 연결에 대한 VPN 클라이언트 구성을](openvpn-azure-ad-client.md)참조하십시오.
