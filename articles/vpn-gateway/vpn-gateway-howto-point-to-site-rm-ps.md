---
title: "Resource Manager 배포 모델을 사용하여 가상 네트워크에 지점 및 사이트 간 VPN Gateway 연결 구성 | Microsoft Docs"
description: "지점 및 사이트 간 VPN Gateway 연결을 만들어 Azure Virtual Network에 안전하게 연결합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 43bb33c1a4386108056b909bef9608087167a30a


---
# <a name="configure-a-pointtosite-connection-to-a-vnet-using-powershell"></a>PowerShell을 사용하여 VNet에 지점 및 사이트 간 연결 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [클래식 - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

지점 및 사이트 간(P2S) 구성을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. P2S 연결은 집 또는 회의와 같은 원격 위치에서 VNet에 연결하려는 경우 또는 몇 명의 클라이언트만 가상 네트워크에 연결해야 하는 경우 유용합니다. 

지점 및 사이트 간 연결을 작동하는 데는 VPN 장치 또는 공용 IP 주소가 필요하지 않습니다. 클라이언트 컴퓨터에서 연결을 시작하여 VPN 연결을 설정합니다. 지점 및 사이트 간 연결에 대한 자세한 내용은 [VPN Gateway FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections) 및 [계획 및 설계](vpn-gateway-plan-design.md)를 참조하세요. 

이 문서에서는 PowerShell을 사용하여 Resource Manager 배포 모델에서 지점 및 사이트 간 연결로 VNet을 만드는 방법을 안내합니다.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 연결에 대한 배포 모델 및 메서드
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

아래 표에서는 P2S 구성에 대한 두 가지 배포 모델과 사용할 수 있는 배포 메서드를 보여 줍니다. 구성 단계를 포함한 문서를 사용할 수 있는 경우 아래 표에서 관련 링크를 직접 제공합니다.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>기본 워크플로
![지점 및 사이트 간 다이어그램](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

이 시나리오에서는 지점 및 사이트 간 연결로 가상 네트워크를 만듭니다. 또한 이 지침은 해당 구성에 필요한 인증서를 생성하는 데도 도움이 됩니다. P2S 연결은 VPN 게이트웨이가 포함된 VNet, 루트 인증서 .cer 파일(공용 키), 클라이언트 인증서 및 클라이언트 쪽 VPN 구성으로 이루어집니다. 

이 구성에 대해 다음 값을 사용합니다. 문서의 섹션 [1](#declare) 에서 변수를 설정합니다. 단계를 계속 따라가며 값을 변경 없이 사용해도 되고, 사용자 환경을 반영하도록 값을 변경해도 됩니다. 

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>예제 값
* **이름: VNet1**
* **주소 공간: 192.168.0.0/16** 및 **10.254.0.0/16**<br>이 예제에서는 두 개의 주소 공간을 사용하여 이 구성이 여러 주소 공간에서 작동하는 것을 보여 줍니다. 하지만 이 구성에 여러 주소 공간이 반드시 필요한 것은 아닙니다.
* **서브넷 이름: FrontEnd**
  * **서브넷 주소 범위: 192.168.1.0/24**
* **서브넷 이름: BackEnd**
  * **서브넷 주소 범위: 10.254.1.0/24**
* **서브넷 이름: GatewaySubnet**<br>서브넷 이름 *GatewaySubnet*은 VPN Gateway가 작동하기 위한 필수 항목입니다.
  * **서브넷 주소 범위: 192.168.200.0/24** 
* **VPN 클라이언트 주소 풀: 172.16.201.0/24**<br>이 지점 및 사이트 간 연결을 사용하여 VNet에 연결되는 VPN 클라이언트는 VPN 클라이언트 주소 풀에서 IP 주소를 받습니다.
* **구독:** 구독이 2개 이상 있는 경우 올바른 구독을 사용 중인지 확인합니다.
* **리소스 그룹: TestRG**
* **위치: 미국 동부**
* **DNS 서버: 이름 확인에 사용할 DNS 서버의 IP 주소**.
* **GW 이름: Vnet1GW**
* **공용 IP 이름: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="before-beginning"></a>시작하기 전에
* Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 을 참조하세요. 이 구성에 PowerShell을 사용할 때 관리자 권한으로 실행되고 있는지 확인합니다. 

## <a name="a-namedeclareapart-1-log-in-and-set-variables"></a><a name="declare"></a>1부 - 로그인 및 변수 설정
이 섹션에서는 로그인하고 이 구성에 사용되는 값을 선언합니다. 선언된 값은 예제 스크립트에 사용됩니다. 값을 변경하여 고유한 환경을 반영합니다. 또는 선언된 값을 사용하고 단계를 연습 삼아 살펴볼 수 있습니다.

1. PowerShell 콘솔에서 Azure 계정에 로그인합니다. 이 cmdlet은 Azure 계정에 대한 로그인 자격 증명을 입력하라는 메시지를 표시합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.
   
        Login-AzureRmAccount 
2. Azure 구독 목록을 가져옵니다.
   
        Get-AzureRmSubscription
3. 사용할 구독을 지정합니다. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
4. 사용할 변수를 선언합니다. 다음 샘플을 사용하여 필요할 때 고유한 값으로 대체합니다.
   
        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"

## <a name="a-nameconfigurevnetapart-2-configure-a-vnet"></a><a name="ConfigureVNet"></a>2부 - VNet 구성
1. 리소스 그룹을 만듭니다.
   
        New-AzureRmResourceGroup -Name $RG -Location $Location
2. 가상 네트워크에 대한 서브넷 구성을 만들고 *FrontEnd*, *BackEnd* 및 *GatewaySubnet*으로 이름을 지정합니다. 이러한 접두사는 선언된 VNet 주소 공간의 일부여야 합니다.
   
        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
3. 가상 네트워크 만들기 지정된 DNS 서버는 연결 중인 리소스에 대한 이름을 확인할 수 있는 DNS 서버여야 합니다. 이 예제에서는 공용 IP 주소를 사용했습니다. 고유한 값을 사용해야 합니다.
   
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
4. 만든 가상 네트워크에 대한 변수를 지정합니다.
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
5. 동적으로 할당된 공용 IP 주소를 요청합니다. 이 IP 주소는 게이트웨이가 제대로 작동하는 데 필요합니다. 나중에 게이트웨이를 게이트웨이 IP 구성에 연결합니다.
   
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="a-namecertificatesapart-3-certificates"></a><a name="Certificates"></a>3부 - 인증서
인증서는 지점 및 사이트 간 VPN에 대한 VPN 클라이언트를 인증하기 위해 Azure에 의해 사용됩니다. 엔터프라이즈 인증서 솔루션에 의해 생성된 루트 인증서 또는 자체 서명된 루트 인증서에서 Base-64로 인코딩된 X.509 .cer 파일로 공용 인증서 데이터(개인 키 아님)를 내보냅니다. 그런 다음 루트 인증서에서 Azure로 공용 인증서 데이터를 가져옵니다. 또한 클라이언트에 대한 루트 인증서에서 클라이언트 인증서를 생성해야 합니다. P2S 연결을 사용하여 가상 네트워크에 연결하려는 각 클라이언트에 루트 인증서에서 생성된 클라이언트 인증서가 설치되어 있어야 합니다.

### <a name="a-namecera1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>1. 루트 인증서용 .cer 파일 가져오기
사용할 루트 인증서에 대한 공용 인증서 데이터를 가져와야 합니다.

* 엔터프라이즈 인증서 시스템을 사용하는 경우, 사용하려는 루트 인증서용 .cer 파일을 가져옵니다. 
* 엔터프라이즈 인증서 솔루션을 사용하지 않는 경우 자체 서명된 루트 인증서를 생성해야 합니다. Windows 10 단계는 [지점 및 사이트 간 구성에 대한 자체 서명된 루트 인증서로 작업](vpn-gateway-certificates-point-to-site.md)을 참조할 수 있습니다.

1. 인증서에서 .cer 파일을 가져오려면 **certmgr.msc**를 열고 루트 인증서를 찾습니다. 자체 서명된 루트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭한 후 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다.
2. 마법사에서 **다음**을 클릭하고 **아니요, 개인 키를 내보내지 않습니다.**를 선택한 후 **다음**을 클릭합니다.
3. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)**를 선택합니다. 그런 후 **다음**을 클릭합니다. 
4. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
5. **마침** 을 클릭하여 인증서를 내보냅니다.

### <a name="a-namegeneratea2-generate-the-client-certificate"></a><a name="generate"></a>2. 클라이언트 인증서 생성
그 다음 클라이언트 인증서를 생성합니다. 연결할 각 클라이언트의 고유한 인증서를 생성할 수도 있고 여러 클라이언트에서 동일한 인증서를 사용할 수도 있습니다. 고유한 클라이언트 인증서를 생성하면 필요할 경우 단일 인증서를 해지할 수 있는 장점이 있습니다. 그렇지 않은 경우 즉, 모든 구성원이 동일한 클라이언트 인증서를 사용하고 한 클라이언트에 대한 인증서를 해지해야 하는 경우, 인증서를 사용하는 모든 클라이언트에 대해 새 인증서를 생성하고 설치해야 합니다. 클라이언트 인증서는 이 연습 뒷부분에서 각 클라이언트 컴퓨터에 설치됩니다.

* 엔터프라이즈 인증서 솔루션을 사용하는 경우, NetBIOS '도메인\사용자 이름' 형식이 아닌 일반 이름 값 형식 'name@yourdomain.com',으로 클라이언트 인증서를 생성합니다. 
* 자체 서명된 인증서를 사용하는 경우 [지점 및 사이트 간 구성에 대한 자체 서명된 루트 인증서로 작업](vpn-gateway-certificates-point-to-site.md) 을 참조하여 클라이언트 인증서를 생성합니다.

### <a name="a-nameexportclientcerta3-export-the-client-certificate"></a><a name="exportclientcert"></a>3. 클라이언트 인증서 내보내기
인증하려면 클라이언트 인증서가 필요합니다. 클라이언트 인증서를 생성한 후 내보냅니다. 내보내는 클라이언트 인증서는 각 클라이언트 컴퓨터에 나중에 설치됩니다.

1. 클라이언트 인증서를 내보내려면 *certmgr.msc*를 사용할 수 있습니다. 내보낼 클라이언트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭한 다음 **내보내기**를 클릭합니다.
2. 개인 키로 클라이언트 인증서를 내보냅니다. 이는 *.pfx* 파일입니다. 이 인증서에 대해 설정한 암호(키)를 기억 하거나 기록해야 합니다.

### <a name="a-nameuploada4-upload-the-root-certificate-cer-file"></a><a name="upload"></a>4. 루트 인증서 .cer 파일 업로드
인증서 이름에 대한 변수를 선언하고 변수를 고유한 값으로 바꿉니다.

        $P2SRootCertName = "Mycertificatename.cer"

루트 인증서에 대한 공용 인증서 데이터를 Azure에 추가합니다. 최대 20개의 루트 인증서에 대한 파일을 업로드할 수 있습니다. 루트 인증서에 대한 개인 키를 Azure에 업로드하지 않습니다. .cer 파일이 업로드되었으면 Azure에서는 이 파일을 사용하여 가상 네트워크에 연결할 클라이언트를 인증합니다. 

파일 경로를 고유한 값으로 바꾼 후 cmdlet을 실행합니다.

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="a-namecreategatewayapart-4-create-the-vpn-gateway"></a><a name="creategateway"></a>4부 - VPN Gateway 만들기
VNet용 가상 네트워크 게이트웨이를 구성하고 만듭니다. *-GatewayType*은 **Vpn**이어야 하고 *-VpnType*은 **RouteBased**이어야 합니다. 완료하려면 최대 45분이 걸릴 수 있습니다.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="a-nameclientconfigapart-5-download-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>5부 - VPN 클라이언트 구성 패키지 다운로드
P2S를 사용하여 Azure에 연결된 클라이언트에는 클라이언트 인증서와 VPN 클라이언트 구성 패키지가 설치되어야 합니다. VPN 클라이언트 구성 패키지는 Windows 클라이언트에 사용할 수 있습니다. VPN 클라이언트 패키지는 Windows에 기본 제공되고 연결하려는 VPN에 관련된 VPN 클라이언트 소프트웨어를 구성하는 정보를 포함합니다. 패키지는 추가 소프트웨어를 설치하지 않습니다. 자세한 내용은 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections) 를 참조하세요.

1. 게이트웨이를 만든 후에 클라이언트 구성 패키지를 다운로드할 수 있습니다. 이 예제에서는 64비트 클라이언트용 패키지를 다운로드합니다. 32비트 클라이언트를 다운로드하려는 경우 'Amd64'를h 'x86'으로 바꿉니다. Azure 포털을 사용하여 VPN 클라이언트를 다운로드할 수도 있습니다.
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. PowerShell cmdlet에서 URL 링크를 반환합니다. 다음은 반환된 URL 형태의 예입니다.
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. 반환된 링크를 웹 브라우저에 복사 및 붙여넣기 하여 패키지를 다운로드합니다. 그런 다음 클라이언트 컴퓨터에 패키지를 설치합니다. SmartScreen 팝업이 나타나면 **자세한 정보**, **실행**을 차례로 클릭하여 패키지를 설치합니다.
4. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 클릭합니다. 나열된 연결이 표시됩니다. 연결된 가상 네트워크의 이름이 다음 예제와 비슷하게 표시됩니다. 
   
    ![VPN 클라이언트](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN client")

## <a name="a-nameclientcertificateapart-6-install-the-client-certificate"></a><a name="clientcertificate"></a>6부 - 클라이언트 인증서 설치
인증을 위해 각 클라이언트 컴퓨터에 클라이언트 인증서가 있어야 합니다. 클라이언트 인증서를 설치할 때는 클라이언트 인증서를 내보낼 때 만든 암호가 필요합니다.

1. .pfx 파일을 클라이언트 컴퓨터에 복사합니다.
2. .pfx 파일을 두 번 클릭하여 설치합니다. 설치 위치를 수정하지 마세요.

## <a name="a-nameconnectapart-7-connect-to-azure"></a><a name="connect"></a>7부 - Azure에 연결
1. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 연결로 이동하고 만든 VPN 연결을 찾습니다. 가상 네트워크와 같은 이름이 지정됩니다. **Connect**를 클릭합니다. 인증서 사용을 안내하는 팝업 메시지가 나타날 수 있습니다. 이 경우 **계속** 을 클릭하여 상승된 권한을 사용합니다. 
2. **연결** 상태 페이지에서 **연결**을 클릭하여 연결을 시작합니다. **인증서 선택** 화면에서 표시되는 클라이언트 인증서가 연결하는 데 사용할 인증서인지 확인합니다. 그렇지 않은 경우 드롭다운 화살표를 사용하여 올바른 인증서를 선택한 다음 **확인**을 클릭합니다.
   
    ![VPN 클라이언트 연결](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")
3. 이제 연결이 설정됩니다.
   
    ![설정된 연결](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Connection established")

## <a name="a-nameverifyapart-8-verify-your-connection"></a><a name="verify"></a>8부 - 연결 확인
1. VPN 연결이 활성인지를 확인하려면, 관리자 권한 명령 프롬프트를 열고 *ipconfig/all*을 실행합니다.
2. 결과를 확인합니다. 받은 IP 주소가 구성에 지정한 지점 및 사이트 VPN 클라이언트 주소 풀 내의 주소 중 하나인지 확인합니다. 결과는 다음과 같아야 합니다.
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="a-nameaddremovecertato-add-or-remove-a-trusted-root-certificate"></a><a name="addremovecert"></a>신뢰할 수 있는 루트 인증서를 추가 또는 제거하려면
인증서는 지점 및 사이트 간 VPN에 대한 VPN 클라이언트를 인증하는 데 사용됩니다. 다음 단계는 루트 인증서를 추가하거나 제거하는 과정을 안내합니다. Azure에 Base64 인코딩 X.509(.cer) 파일을 추가할 때 파일이 나타내는 루트 인증서를 신뢰하도록 Azure에 지시합니다. 

PowerShell을 사용하거나 Azure 포털에서 신뢰할 수 있는 루트 인증서를 추가하거나 제거할 수 있습니다. Azure Portal을 사용하여 수행하려면 **가상 네트워크 게이트웨이 > 설정 > 지점 및 사이트 구성 > 루트 인증서**로 이동합니다. 다음 단계에서는 PowerShell을 사용하여 이러한 작업을 살펴봅니다. 

### <a name="add-a-trusted-root-certificate"></a>신뢰할 수 있는 루트 인증서 추가
Azure에 최대 20개의 신뢰할 수 있는 루트 인증서 .cer 파일을 추가할 수 있습니다. 아래 단계를 따라 루트 인증서를 추가합니다.

1. Azure에 추가할 새 루트 인증서를 만들고 준비합니다. Base-64로 인코딩된 X.509(.CER)로 공개 키를 내보내고 텍스트 편집기로 엽니다. 아래에 표시된 섹션만 복사합니다. 
   
    다음 예제와 같이 값을 복사합니다.
   
    ![인증서](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificate")
2. 인증서 이름 및 키 정보를 변수로 지정합니다. 다음 예제에 나와 있는 것처럼 정보를 고유한 정보로 바꿉니다.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. 새 루트 인증서를 추가합니다. 한번에 하나의 인증서만 추가할 수 있습니다.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. 다음 cmdlet을 사용하여 새 인증서가 올바르게 추가되었는지 확인할 수 있습니다.
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>신뢰할 수 있는 루트 인증서를 제거하려면
Azure에서 신뢰할 수 있는 루트 인증서를 제거할 수 있습니다. 신뢰할 수 있는 인증서를 제거하면 루트 인증서에서 생성된 클라이언트 인증서는 더 이상 지점 및 사이트 간을 통해 Azure에 연결할 수 없습니다. 클라이언트를 연결하려는 경우 Azure에서 신뢰할 수 있는 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다.

1. 신뢰할 수 있는 루트 인증서를 제거하려면 다음 샘플을 수정합니다.
   
    변수를 선언합니다.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. 인증서를 제거합니다.
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. 다음 cmdlet을 사용하여 인증서가 성공적으로 제거되었는지 확인합니다. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="a-namerevokeato-manage-the-list-of-revoked-client-certificates"></a><a name="revoke"></a>해지된 클라이언트 인증서 목록을 관리하려면
클라이언트 인증서를 해지할 수 있습니다. 인증서 해지 목록에서 개별 클라이언트 인증서를 기반으로 하는 지점 및 사이트 간 연결을 선택적으로 거부할 수 있습니다. Azure에서 루트 인증서 .cer를 제거하면, 해지된 루트 인증서에 의해 생성/서명된 모든 클라이언트 인증서에 대한 액세스 권한도 해지됩니다. 루트가 아닌 특정 클라이언트 인증서를 해지해도 이를 수행할 수 있습니다. 이런 방식은 루트 인증서에서 생성된 다른 인증서에도 여전히 유효합니다.

해지된 클라이언트 인증서를 사용하는 동안 개별 사용자의 세분화된 액세스 제어를 위해 일반적으로 루트 인증서를 사용하여 팀 또는 조직 수준에서 액세스를 관리합니다.

### <a name="revoke-a-client-certificate"></a>클라이언트 인증서 해지
1. 해지할 클라이언트 인증서의 지문을 가져옵니다.
   
        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"
2. 해지된 지문 목록에 지문을 추가합니다.
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. 지문이 인증서 해지 목록에 추가되었는지 확인합니다. 한번에 하나의 지문을 추가해야 합니다.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>클라이언트 인증서 복구
해지된 클라이언트 인증서 목록에서 지문을 제거하여 클라이언트 인증서를 복구할 수 있습니다.

1. 해지된 클라이언트 인증서 지문 목록에서 지문을 제거합니다.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
2. 해지된 목록에서 지문이 제거되었는지 확인합니다.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>다음 단계
가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 단계는 [가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 를 참조하세요.




<!--HONumber=Nov16_HO2-->


