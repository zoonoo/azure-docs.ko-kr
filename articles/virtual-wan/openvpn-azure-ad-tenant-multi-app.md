---
title: '가상 WAN: 다른 사용자 그룹에 대한 Azure AD 테넌트: Azure AD 인증'
description: P2S OpenVPN 인증을 위해 Azure AD 테넌트를 설정하고 여러 사용자 및 그룹에 대해 서로 다른 액세스를 허용하도록 Azure AD에서 여러 앱을 만들어 등록합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 63dff3df15f0e87a67c786d5c0e8b9a5ec1b8a3f
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110700095"
---
# <a name="create-an-azure-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN 프로토콜 연결을 위한 Azure AD(Active Directory) 테넌트 만들기

VNet에 연결하는 경우 인증서 기반 인증 또는 RADIUS 인증을 사용할 수 있습니다. 그러나 오픈 VPN 프로토콜을 사용하는 경우 Azure Active Directory 인증을 사용할 수도 있습니다. 다양한 사용자 집합이 서로 다른 게이트웨이에 연결할 수 있도록 하려면 AD에 여러 앱을 등록하고 다른 게이트웨이에 연결할 수 있습니다.

이 문서는 P2S OpenVPN 인증을 위해 Azure AD 테넌트를 설정하고 여러 사용자 및 그룹에 대해 서로 다른 액세스를 허용하도록 Azure AD에서 여러 앱을 만들어 등록합니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN&reg; 프로토콜 연결에만 지원됩니다.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. 새 P2S 구성 만들기

P2S 구성은 원격 클라이언트 연결에 대한 매개 변수를 정의합니다.

1. 환경에 필요한 대로 값을 대체하여 다음 변수를 설정합니다.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. 다음 명령을 실행하여 구성을 만듭니다.

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > 위의 명령에서 Azure VPN Client의 애플리케이션 ID를 사용하지 마세요. 모든 사용자에게 게이트웨이에 대한 액세스 권한을 부여합니다. 등록한 애플리케이션의 ID를 사용합니다.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7.허브 할당 편집

1. 가상 WAN 아래의 **허브** 블레이드로 이동합니다.

2. vpn 서버 구성을 연결할 허브를 선택하고, 줄임표(...)를 클릭합니다.

    ![메뉴에서 선택한 가상 허브 편집을 보여 주는 스크린샷.](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. **가상 허브 편집** 을 클릭합니다.

4. **지점 및 사이트 간 게이트웨이 포함** 확인란을 선택하고 원하는 **게이트웨이 배율 단위** 를 선택합니다.

    ![게이트웨이 확장 단위를 선택할 수 있는 가상 허브 편집 대화 상자를 보여 주는 스크린샷.](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. VPN 클라이언트에 IP 주소를 할당할 **주소 풀** 를 입력합니다.

6. **확인** 을 클릭합니다.

7. 이 작업을 완료하려면 최대 30분이 걸릴 수 있습니다.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. VPN 프로필 다운로드

VPN 프로필을 사용하여 클라이언트를 구성합니다.

1. 가상 WAN에 대한 페이지에서 **사용자 VPN 구성** 을 클릭합니다.

2. 페이지 맨 위에서 **사용자 VPN 구성 다운로드** 를 클릭합니다.

3. 파일 만들기가 끝나면 링크를 클릭하여 다운로드할 수 있습니다.

4. 프로필 파일을 사용하여 VPN 클라이언트를 구성합니다.

5. 다운로드한 zip 파일을 추출합니다.

6. 압축 해제된 "AzureVPN" 폴더로 이동합니다.

7. “azurevpnconfig.xml” 파일의 위치를 기록합니다. azurevpnconfig.xml에는 VPN 연결에 대한 설정이 포함되어 있으며, 이 파일은 Azure VPN Client 애플리케이션으로 직접 가져올 수 있습니다. 또한 이메일 또는 다른 방법을 통해 연결해야 하는 모든 사용자에게 이 파일을 배포할 수 있습니다. 성공적으로 연결하려면 사용자에게 유효한 Azure AD 자격 증명이 필요합니다.

## <a name="9-configure-user-vpn-clients"></a>9. 사용자 VPN 클라이언트 구성

연결하려면 Azure VPN Client를 다운로드하고, 이전 단계에서 VNet에 연결하려는 모든 컴퓨터에 다운로드한 VPN 클라이언트 프로필을 가져와야 합니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN&reg; 프로토콜 연결에만 지원됩니다.
>

#### <a name="to-download-the-azure-vpn-client"></a>Azure VPN 클라이언트를 다운로드하려면,

이 [링크](https://go.microsoft.com/fwlink/?linkid=2117554)를 사용하여 Azure VPN Client를 다운로드합니다.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>클라이언트 프로필을 가져오려면,

1. 페이지에서 **가져오기** 를 선택합니다.

    ![\+ 메뉴에서 선택한 가져오기 작업을 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. 프로필 xml 파일을 찾아서 선택합니다. 파일이 선택된 상태에서 **열기** 를 선택합니다.

    ![파일을 선택할 수 있는 열기 대화 상자를 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. 프로필 이름을 지정하고, **저장** 을 선택합니다.

    ![추가된 연결 이름과 선택한 저장 단추를 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. **연결** 을 선택하여 VPN에 연결합니다.

    ![방금 만든 연결에 대한 연결 단추를 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. 연결되면 아이콘이 녹색으로 바뀌고 **연결됨** 으로 표시됩니다.

    ![연결된 상태의 연결과 연결 해제 옵션을 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>클라이언트 프로필을 삭제하려면,

1. 삭제하려는 클라이언트 프로필 옆의 줄임표(...)를 선택합니다. 그런 다음, **제거** 를 선택합니다.

    ![메뉴에서 선택한 제거 작업을 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. **제거** 를 선택하여 삭제합니다.

    ![제거 또는 취소 옵션이 있는 확인 대화 상자를 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>연결 문제를 진단하려면

1. 연결 문제를 진단하려면 **진단** 도구를 사용할 수 있습니다. 진단하려는 VPN 연결 옆에 있는 줄임표(...)를 선택하여 메뉴를 표시합니다. 그런 다음, **진단** 을 선택합니다.

    ![메뉴에서 선택한 진단 작업을 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. **연결 속성** 페이지에서 **진단 실행** 을 선택합니다.

    ![연결에 대한 진단 실행 단추를 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. 자격 증명을 사용하여 로그인합니다.

    ![진단 3](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. 진단 결과를 살펴봅니다.

    ![연결에 대한 진단 실행 버튼을 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. 자격 증명을 사용하여 로그인합니다.

    ![이 작업에 대한 로그인 대화 상자를 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. 진단 결과를 살펴봅니다.

    ![진단의 결과를 보여 주는 스크린샷.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. 가상 WAN 보기

1. 가상 WAN 탭으로 이동합니다.

2. 개요 페이지의 맵에 있는 각 점은 허브를 나타냅니다.

3. 허브 및 연결 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.

## <a name="clean-up-resources"></a><a name="cleanup"></a>리소스 정리

리소스가 더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 여기에 포함된 모든 리소스를 제거할 수 있습니다. "myResourceGroup"을 리소스 그룹의 이름으로 바꾸고 다음 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
