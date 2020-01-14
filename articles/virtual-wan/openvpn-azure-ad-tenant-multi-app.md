---
title: 'VPN Gateway: 다른 사용자 그룹에 대 한 Azure AD 테 넌 트: Azure AD 인증'
description: Azure AD 인증을 사용 하 여 VNet에 연결 하는 데 P2S VPN을 사용할 수 있습니다.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: b390b5f8b00f61994db820a3af7bce26a3e0a30d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934974"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN 프로토콜 연결에 대 한 Azure Active Directory 테 넌 트 만들기

VNet에 연결 하는 경우 인증서 기반 인증 또는 RADIUS 인증을 사용할 수 있습니다. 그러나 오픈 VPN 프로토콜을 사용 하는 경우 Azure Active Directory 인증을 사용할 수도 있습니다. 다른 사용자가 다른 VPN gateway에 연결할 수 있도록 하려면 AD에 여러 앱을 등록 하 고 다른 VPN 게이트웨이에 연결할 수 있습니다. 이 문서는 P2S OpenVPN 인증을 위해 Azure AD 테 넌 트를 설정 하 고 여러 사용자 및 그룹에 대해 서로 다른 액세스를 허용 하기 위해 Azure AD에서 여러 앱을 만들고 등록 하는 데 도움이 됩니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN® 프로토콜 연결에만 지원됩니다.
>

## <a name="tenant"></a>1. Azure AD 테 넌 트 만들기

[새 테 넌 트 만들기](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 문서의 단계를 사용 하 여 Azure AD 테 넌 트를 만듭니다.

* 조직 이름
* 초기 도메인 이름

예:

   ![새 Azure AD 테 넌 트](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. Azure AD 테 넌 트 사용자 만들기

다음으로 두 개의 사용자 계정을 만듭니다. 전역 관리자 계정과 마스터 사용자 계정 하나를 만듭니다. 마스터 사용자 계정은 마스터 포함 계정 (서비스 계정)으로 사용 됩니다. Azure AD 테 넌 트 사용자 계정을 만들 때 만들려는 사용자의 유형에 대 한 디렉터리 역할을 조정 합니다.

[이 문서의](../active-directory/fundamentals/add-users-azure-active-directory.md) 단계를 사용 하 여 Azure AD 테 넌 트에 대해 두 명 이상의 사용자를 만듭니다. 계정 유형을 만들려면 **디렉터리 역할** 을 변경 해야 합니다.

* 전역 관리자
* 사용자

## <a name="enable-authentication"></a>3. azure AD 테 넌 트에서 Azure VPN 클라이언트 등록

1. 인증에 사용할 디렉터리의 디렉터리 ID를 찾습니다. Active Directory 페이지의 속성 섹션에 나열 됩니다.

    ![디렉터리 ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. 디렉터리 ID를 복사 합니다.

3. **전역 관리자** 역할이 할당 된 사용자로 Azure Portal에 로그인 합니다.

4. 다음으로, 관리자 동의를 제공 합니다. 브라우저의 주소 표시줄에서 배포 위치와 관련 된 URL을 복사 하 여 붙여 넣습니다.

    공용

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud 독일

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. 메시지가 표시 되 면 **전역 관리자** 계정을 선택 합니다.

    ![디렉터리 ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 메시지가 표시 되 면 **동의** 를 선택 합니다.

    ![수락](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Azure AD의 **엔터프라이즈 응용 프로그램**에는 **azure VPN** 나열이 표시 됩니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. 다양 한 사용자/그룹에 대 한 추가 응용 프로그램 등록

1. Azure Active Directory에서 **앱 등록** 를 클릭 하 고 **+ 새로 등록** 을 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. **응용 프로그램 등록** 블레이드에서 **이름을** 입력 하 고 원하는 **지원 계정 유형을** 선택한 후 **등록** 을 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 새 앱이 등록 되 면 앱 블레이드에서 **API 노출** 을 클릭 합니다.

4. **+ 범위 추가** 를 클릭 합니다.
5. 기본 **응용 프로그램 ID URI** 를 그대로 두고 **저장 후 계속을** 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. 필수 필드를 입력 하 고 **상태가** **사용으로 설정**되어 있는지 확인 합니다. **범위 추가** 클릭

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. **API 노출** 를 클릭 한 다음 **클라이언트 응용 프로그램 추가**를 클릭 합니다.  **클라이언트 ID**에 대해 클라우드에 따라 다음 값을 입력 합니다.
    -   **41b23e61-6c1e-4545-b367-cd054e0ed4b4** For Azure **Public** 을 입력 합니다.
    -   Azure **정부** 에 **51bb15d4-3a4f-4ebf-9dca-40096fe32426** 입력
    -   Azure **독일** 에 **538ee9e6-310a-468d-afef-ea97365856a9** 입력
    -   Azure **중국 21vianet** 에 대해 **49f817b6-84ae-4cc0-928c-73f27289b3aa** 입력


8. **응용 프로그램 추가** 를 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. **개요** 페이지에서 **응용 프로그램 (클라이언트) ID** 를 복사 합니다. VPN gateway를 구성 하는 데 필요 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 이 섹션 (4)의 단계를 반복 하 여 보안 요구 사항에 필요한 만큼의 응용 프로그램을 만듭니다. 각 응용 프로그램은 VPN gateway에 연결 되며 다른 사용자 집합을 가질 수 있습니다. 하나의 응용 프로그램만 게이트웨이에 연결할 수 있습니다.

## <a name="enable-authentication"></a>5. 응용 프로그램에 사용자 할당

1. Azure AD, **엔터프라이즈 응용 프로그램**에서 새로 등록 된 응용 프로그램을 선택 하 고 **속성**을 클릭 합니다. **사용자 할당 필요 여부** 를 **예**로 설정 합니다. 페이지 맨 아래에 있는 **저장**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 앱 페이지에서 **사용자 및 그룹** 을 클릭 한 다음 **사용자 추가** 를 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. **할당 추가**에서 **사용자 및 그룹**을 클릭 합니다. 이 VPN 응용 프로그램에 액세스 하려는 사용자를 선택 합니다. **선택** 클릭

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="site"></a>6. 새 P2S 구성 만들기

P2S 구성은 원격 클라이언트 연결에 대한 매개 변수를 정의합니다.

1. 환경에 필요한 대로 값을 대체하여 다음 변수를 설정합니다.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. 다음 명령을 실행하여 구성을 만듭니다.

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
> [!NOTE]
> Do not use the Azure VPN client's application ID in the commands above as it will grant all users access to the VPN gateway. Use the ID of the application(s) you registered.

## <a name="hub"></a>7. Edit hub assignment

1. Navigate to the **Hubs** blade under the virtual WAN.
2. Select the hub that you want to associate the vpn server configuration to and click the ellipsis (...).

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Click **Edit virtual hub**.
4. Check the **Include point-to-site gateway** check box and pick the **Gateway scale unit** that you want.

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Enter the **Address pool** from which the VPN clients will be assigned IP addresses.
6. Click **Confirm**.
7. The operation will can take up to 30 minutes to complete.

## <a name="device"></a>8. Download VPN profile

Use the VPN profile to configure your clients.

1. On the page for your virtual WAN, click **User VPN configurations**.
2. At the top of the  page, click **Download user VPN config**.
3. Once the file has finished creating, you can click the link to download it.
4. Use the profile file to configure the VPN clients.

4. Extract the downloaded zip file.

5. Browse to the unzipped “AzureVPN” folder.

6. Make a note of the location of the “azurevpnconfig.xml” file. The azurevpnconfig.xml contains the setting for the VPN connection and can be imported directly into the Azure VPN Client application. You can also distribute this file to all the users that need to connect via e-mail or other means. The user will need valid Azure AD credentials to connect successfully.
## Configure user VPN clients

To connect, you need to download the Azure VPN Client (Preview) and import the VPN client profile that was downloaded in the previous steps on every computer that wants to connect to the VNet.

> [!NOTE]
> Azure AD authentication is supported only for OpenVPN® protocol connections.
>

#### To download the Azure VPN client

Use this [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) to download the Azure VPN Client (Preview).

#### <a name="import"></a>To import a client profile

1. On the page, select **Import**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Browse to the profile xml file and select it. With the file selected, select **Open**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Specify the name of the profile and select **Save**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Select **Connect** to connect to the VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Once connected, the icon will turn green and say **Connected**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>To delete a client profile

1. Select the ellipsis (...) next to the client profile that you want to delete. Then, select **Remove**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Select **Remove** to delete.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnose connection issues

1. To diagnose connection issues, you can use the **Diagnose** tool. Select the ellipsis (...) next to the VPN connection that you want to diagnose to reveal the menu. Then select **Diagnose**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. On the **Connection Properties** page, select **Run Diagnosis**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Sign in with your credentials.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. View the diagnosis results.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>View your virtual WAN

1. Navigate to the virtual WAN.
2. On the Overview page, each point on the map represents a hub. Hover over any point to view the hub health summary.
3. In the Hubs and connections section, you can view hub status, site, region, VPN connection status, and bytes in and out.

## <a name="viewhealth"></a>View your resource health

1. Navigate to your WAN.
2. On your WAN page, in the **SUPPORT + Troubleshooting** section, click **Health** and view your resource.


## <a name="cleanup"></a>Clean up resources

When you no longer need these resources, you can use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) to remove the resource group and all of the resources it contains. Replace "myResourceGroup" with the name of your resource group and run the following PowerShell command:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.