---
title: Azure PowerShell을 사용하여 Azure Stack Edge Mini R 디바이스에서 VPN 구성
description: Azure 리소스를 만들기 위해 Azure PowerShell 스크립트를 사용하여 Azure Stack Edge Mini R 디바이스에서 VPN을 구성하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 487d30c350fc6871122c66bbb3faa6b656ea96f9
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110698639"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Azure PowerShell을 통해 Azure Stack Edge Mini R 디바이스에서 VPN 구성

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN 옵션은 Azure Stack Edge Mini R 또는 Azure Stack Edge Pro R 디바이스에서 Azure로 *TLS* 를 통해 이동 중인 데이터에 대한 두 번째 암호화 계층을 제공합니다. Azure Portal 또는 Azure PowerShell을 통해 Azure Stack Edge Mini R 디바이스에 VPN을 구성할 수 있습니다. 

이 문서에서는 Azure PowerShell 스크립트를 사용하여 클라우드에 구성을 생성하는 Azure Stack Edge Mini R 디바이스에서 P2S(Point-to-Site) VPN을 구성하는 데 필요한 단계를 설명합니다. Azure Stack Edge 디바이스의 구성은 로컬 UI를 통해 수행됩니다.

## <a name="about-vpn-setup"></a>VPN 설정 정보

P2S VPN 게이트웨이 연결을 사용하면 개별 클라이언트 컴퓨터 또는 Azure Stack Edge Mini R 디바이스에서 가상 네트워크에 대한 보안 연결을 만들 수 있습니다. 클라이언트 컴퓨터 또는 디바이스에서 P2S 연결을 시작합니다. 이 경우 P2S 연결은 표준 기반 IPsec VPN 솔루션인 IKEv2 VPN을 사용합니다.

일반적인 워크플로에는 다음 단계가 포함됩니다.

1. 필수 조건을 구성합니다.
2. Azure에서 필요한 리소스를 설정합니다.
    1. 가상 네트워크 및 필수 서브넷을 만들어 구성합니다. 
    2. Azure VPN Gateway(Virtual Network Gateway)를 만들어 구성합니다.
    3. Azure Firewall을 설정하고 네트워크 및 앱 규칙을 추가합니다.
    4. Azure 경로 테이블을 만들고 경로를 추가합니다.
    5. VPN 게이트웨이에서 지점 및 사이트 간을 사용하도록 설정합니다.
        1. 클라이언트 주소 풀을 추가합니다.
        2. 터널 종류를 구성합니다.
        3. 인증 유형을 구성합니다.
        4. 인증서를 만듭니다.
        5. 인증서를 업로드합니다.
    6. 전화 번호부를 다운로드합니다.
3. 디바이스의 로컬 웹 UI에서 VPN을 설정합니다. 
    1. 전화 번호부를 제공합니다.
    2. 서비스 태그(json) 파일을 제공합니다.


자세한 내용은 다음 섹션에 나와 있습니다.

## <a name="configure-prerequisites"></a>필수 조건 구성

- [Azure Stack Edge Mini R 디바이스 설치](azure-stack-edge-mini-r-deploy-install.md)의 지침에 따라 설치된 Azure Stack Edge Mini R 디바이스에 대한 액세스 권한이 있어야 합니다. 이 디바이스는 Azure와 P2S 연결을 설정합니다. 

- Azure에서 Azure Stack Edge 서비스에 대해 활성화된 유효한 Azure 구독에 대한 액세스 권한이 있어야 합니다. 이 구독을 사용하여 Azure에서 해당 리소스를 만들어 Azure Stack Edge Mini R 디바이스를 관리합니다.  

- Azure Stack Edge Mini R 디바이스에 액세스하는 데 사용할 Windows 클라이언트에 액세스할 수 있습니다. 이 클라이언트를 사용하여 클라우드에서 프로그래밍 방식으로 구성합니다.

    1. Windows 클라이언트에 필요한 PowerShell 버전을 설치하려면 다음 명령을 실행합니다.

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Azure 계정 및 구독에 연결하려면 다음 명령을 실행합니다.

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        VPN을 구성하기 위해 Azure Stack Edge Mini R 디바이스와 함께 사용하는 Azure 구독 이름을 제공합니다.

    3. 클라우드에서 구성하는 데 필요한 [스크립트를 다운로드](https://aka.ms/ase-vpn-deployment)합니다. 스크립트는 다음을 수행합니다.
        
        - Azure 가상 네트워크와 *GatewaySubnet* 및 *AzureFirewallSubnet* 서브넷을 만듭니다.
        - Azure VPN 게이트웨이를 만들어 구성합니다.
        - Azure 로컬 네트워크 게이트웨이를 만들어 구성합니다.
        - Azure VPN 게이트웨이와 로컬 네트워크 게이트웨이 간에 Azure VPN 연결을 만들어 구성합니다.
        - Azure Firewall을 만들고 네트워크 규칙, 앱 규칙을 추가합니다.
        - Azure 경로 테이블을 만들고 경로를 추가합니다.

    4. Azure 리소스를 만들려는 Azure Portal에서 리소스 그룹을 만듭니다. Azure Portal의 서비스 목록으로 이동하여 **리소스 그룹** 을 선택한 다음, **+ 추가** 를 선택합니다. 리소스 그룹의 구독 정보와 이름을 제공한 다음, **만들기** 를 선택합니다. 이 리소스 그룹으로 이동하면 현재 해당 그룹에 리소스가 없어야 합니다.

        ![Azure 리소스 그룹](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. Azure Stack Edge Mini R 디바이스에 대해 `.cer` 형식의 Base 64 인코딩 인증서가 있어야 합니다. 이 인증서는 프라이빗 키를 사용하여 `pfx`로 Azure Stack Edge 디바이스에 업로드해야 합니다. 이 인증서는 P2S 연결을 설정하려는 클라이언트의 저장소의 신뢰할 수 있는 루트에도 설치되어야 합니다.

## <a name="use-the-script"></a>스크립트 사용

먼저 `parameters-p2s.json` 매개 변수를 입력하도록 파일을 수정합니다. 그런 다음, 수정된 json 파일을 사용하여 스크립트를 실행합니다.

이러한 각 단계는 다음 섹션에서 설명합니다.

### <a name="download-service-tags-file"></a>서비스 태그 파일 다운로드

스크립트를 다운로드한 폴더에 `ServiceTags.json` 파일이 이미 있을 수 있습니다. 그렇지 않은 경우 서비스 태그 파일을 다운로드할 수 있습니다.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>매개 변수 파일 수정

첫 번째 단계는 `parameters-p2s.json` 파일을 수정하고 변경 내용을 저장하는 것입니다. 

만든 Azure 리소스의 경우 다음 이름을 제공합니다.

|매개 변수 이름  |Description  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure Virtual Network 이름        |
|azureFirewalls_firewall_name     | Azure Firewall 이름        |
|routeTables_routetable_name     | Azure 경로 테이블 이름        |
|publicIPAddresses_VNGW_public_ip_name     | 가상 네트워크 게이트웨이의 공용 IP 이름입니다.       |
|virtualNetworkGateways_VNGW_name    | Azure VPN 게이트웨이(가상 네트워크 게이트웨이) 이름        |
|publicIPAddresses_firewall_public_ip_name     | Azure Firewall의 공용 IP 주소 이름         |
|위치     |가상 네트워크를 만들려는 지역입니다. 디바이스와 연결된 지역을 선택합니다.         |
|RouteTables_routetable_onprem_name| 방화벽이 패킷을 Azure Stack Edge 디바이스로 다시 라우팅하는 데 도움이 되는 추가 경로 테이블의 이름입니다. 스크립트는 두 개의 추가 경로를 만들고 *default* 및 *FirewallSubnet* 을 이 경로 테이블과 연결합니다.|

가상 네트워크 및 관련 서브넷(*default*, *firewall*, *GatewaySubnet*)을 포함하여 생성한 Azure 리소스에 대해 다음 IP 주소 및 주소 공간을 제공합니다.

|매개 변수 이름  |Description  |
|---------|---------|
|VnetIPv4AddressSpace    | 가상 네트워크와 연결된 주소 공간입니다. Vnet IP 범위를 개인 IP 범위(https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) )로 제공합니다.     |
|DefaultSubnetIPv4AddressSpace    |가상 네트워크의 `Default` 서브넷과 연결된 주소 공간입니다.         |
|FirewallSubnetIPv4AddressSpace    |가상 네트워크의 `Firewall` 서브넷과 연결된 주소 공간입니다.          |
|GatewaySubnetIPv4AddressSpace    |가상 네트워크의 `GatewaySubnet`과 연결된 주소 공간입니다.          |
|GatewaySubnetIPv4bgpPeeringAddress    | BGP 통신용으로 예약된 IP 주소이며 가상 네트워크의 `GatewaySubnet`와 연결된 주소 공간을 기반으로 합니다.          |
|ClientAddressPool    | 이 IP 주소는 Azure Portal P2S 구성의 주소 풀에 사용됩니다.         |
|PublicCertData     | 공용 인증서 데이터는 VPN Gateway에서 연결된 P2S 클라이언트를 인증하는 데 사용됩니다. 인증서 데이터를 가져오려면 루트 인증서를 설치합니다. 인증서가 .cer 확장자로 인코딩된 Base-64인지 확인합니다. 이 인증서를 열고 ==BEGIN CERTIFICATE== 및 ==END CERTIFICATE== 사이에 있는 인증서의 텍스트를 한 줄로 복사합니다.     |



### <a name="run-the-script"></a>스크립트 실행

다음 단계에 따라 수정된 `parameters-p2s.json`을 사용하고 스크립트를 실행하여 Azure 리소스를 만듭니다.

1. PowerShell을 실행합니다. 스크립트가 있는 디렉터리로 전환합니다.

3. 스크립트를 실행합니다.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > 이 릴리스에서 스크립트는 미국 동부 위치에서만 작동합니다.

    스크립트를 실행할 때 다음 정보를 입력해야 합니다.

    
    |매개 변수  |Description  |
    |---------|---------|
    |위치     |Azure 리소스를 만들어야 하는 지역입니다.         |
    |AzureAppRuleFilePath     | `appRule.json`의 파일 경로입니다.       |
    |AzureIPRangesFilePath     |이전 단계에서 다운로드한 서비스 태그 json 파일입니다.         |
    |ResourceGroupName     | 모든 Azure 리소스를 만드는 리소스 그룹의 이름입니다.        |
    |AzureDeploymentName    |Azure 배포의 이름입니다.         |
    |NetworkRuleCollectionName            | Azure Firewall에 생성되어 추가되는 모든 네트워크 규칙 모음의 이름입니다.             |
    |우선 순위            | 생성된 모든 네트워크 및 애플리케이션 규칙에 할당된 우선 순위입니다.              |
    |AppRuleCollectionName            |Azure Firewall에 생성되어 추가되는 모든 애플리케이션 규칙 컬렉션의 이름입니다.                |


    샘플 출력은 다음과 같습니다.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - 이 스크립트를 실행하는 데 약 90분이 걸립니다. 스크립트가 시작되기 직전에 네트워크에 로그인해야 합니다.
    > - 어떤 이유로든 스크립트에 실패한 세션이 있는 경우 리소스 그룹을 삭제하여 그 아래에 생성된 모든 리소스를 삭제해야 합니다.
  
    
    스크립트가 완료되면 스크립트가 있는 동일한 폴더에 배포 로그가 생성됩니다.


## <a name="verify-the-azure-resources"></a>Azure 리소스 확인

스크립트를 성공적으로 실행한 후 모든 리소스가 Azure에서 생성되었는지 확인합니다. 만든 리소스 그룹으로 이동합니다. 다음과 같은 리소스가 표시됩니다.

![Azure 리소스](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>VPN 프로필에 대한 전화 번호부 다운로드

이 단계에서는 디바이스에 대한 VPN 프로필을 다운로드합니다.

1. Azure Portal에서 리소스 그룹으로 이동한 후 이전 단계에서 만든 가상 네트워크 게이트웨이를 선택합니다.

    ![Azure Virtual Network 게이트웨이](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. **설정 > 지점 및 사이트 간 구성** 을 선택합니다. **VPN 클라이언트 다운로드** 를 선택합니다.

    ![P2S 구성 1 사용](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. 압축된 프로필을 저장하고 Windows 클라이언트에서 압축을 풉니다.

    ![P2S 구성 2 사용](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. *WindowsAmd64* 폴더로 이동한 다음, `.exe`: *VpnClientSetupAmd64.exe* 를 추출합니다.

    ![P2S 구성 3 사용](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. 임시 경로를 만듭니다. 예:

    `C:\NewTemp\vnet\tmp`

4. PowerShell을 실행하고 `.exe`가 있는 디렉터리로 이동합니다. `.exe`를 실행하려면 다음을 입력합니다.

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. 임시 경로에 새 파일이 포함됩니다. 샘플 출력은 다음과 같습니다.

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. *.pbk* 파일은 VPN 프로필의 전화 번호부입니다. 이는 로컬 UI에서 사용합니다.


## <a name="vpn-configuration-on-the-device"></a>디바이스의 VPN 구성

Azure Stack Edge 디바이스의 로컬 UI에서 이러한 단계를 따릅니다.

1. 로컬 UI에서 **VPN** 페이지롤 이동합니다. VPN 상태에서 **구성** 을 선택합니다.

    ![VPN 1 구성](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. **VPN 구성** 블레이드에서:
    
    1. 전화 번호부 파일 업로드에서, 이전 단계에서 만든 .pbk 파일을 가리킵니다.
    2. 공용 IP 목록 구성 파일 업로드에서 Azure 데이터 센터 IP 범위 JSON 파일을 입력으로 제공합니다. 이전 단계에서 이 파일을 다운로드했습니다. [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519)
    3. 지역으로 **eastus** 를 선택하고 **적용** 을 선택합니다.

    ![VPN 2 구성](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. **VPN 만 사용하여 액세스할 IP 주소 범위** 섹션에서 Azure Virtual Network에 대해 선택한 Vnet IPv4 범위를 입력합니다.

    ![VPN 3 구성](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>클라이언트 연결 확인

1. Azure Portal에서 VPN 게이트웨이로 이동합니다.
2. **설정 > 지점 및 사이트 간 구성** 을 선택합니다. **할당된 IP 주소** 아래에 Azure Stack Edge 디바이스의 IP 주소가 표시되어야 합니다.

## <a name="validate-data-transfer-through-vpn"></a>VPN을 통한 데이터 전송 유효성 검사

VPN이 작동하는지 확인하려면 데이터를 SMB 공유로 복사합니다. Azure Stack Edge 디바이스에서 [공유 추가](azure-stack-edge-gpu-manage-shares.md#add-a-share)의 단계를 따릅니다. 

1. 파일(예: \data\pictures\waterfall.jpg)을 클라이언트 시스템에 탑재한 SMB 공유에 복사합니다. 
2. 데이터가 복사되는 동안 VPN을 통해 데이터가 이동하는지 확인하려면 다음을 수행합니다.

    1. Azure Portal에서 VPN 게이트웨이로 이동합니다. 

    2. **모니터링 > 메트릭** 으로 이동합니다.

    3. 오른쪽 창에서 VPN 게이트웨이로 **범위**, 게이트웨이 P2S 대역폭으로 **메트릭**, 평균으로 **집계** 를 선택합니다.

    4. 데이터가 복사되는 동안 대역폭 사용률이 증가하고 데이터 복사가 완료되면 대역폭 사용률이 감소합니다.

        ![Azure vpn 메트릭](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. 이 파일이 클라우드의 스토리지 계정에 표시되는지 확인합니다.
 
## <a name="debug-issues"></a>문제 디버그

문제를 디버그하려면 다음 명령을 사용합니다.

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

샘플 출력은 다음과 같습니다.


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge 디바이스의 로컬 UI를 통해 VPN을 구성합니다](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn).
