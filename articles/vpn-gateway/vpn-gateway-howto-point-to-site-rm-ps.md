---
title: "지점 및 사이트 간을 사용하여 Azure Virtual Network에 컴퓨터 연결: PowerShell | Microsoft Docs"
description: "지점 및 사이트 간 VPN Gateway 연결을 만들어 Azure Virtual Network에 안전하게 컴퓨터를 연결합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8cbf4e8ec2b8904d16c6a74b40cbf6d2ec6a1330
ms.lasthandoff: 04/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>PowerShell을 사용하여 VNet에 지점 및 사이트 간 연결 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [클래식 - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

지점 및 사이트 간(P2S) 구성을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. P2S는 SSTP를 통한 VPN 연결입니다(보안 소켓 터널링 프로토콜). 지점 및 사이트 간 연결은 집 또는 회의와 같은 원격 위치에서 VNet에 연결하려는 경우 또는 몇 명의 클라이언트만 가상 네트워크에 연결해야 하는 경우 유용합니다. P2S 연결에는 VPN 장치 또는 공용 IP 주소가 필요하지 않습니다. 클라이언트 컴퓨터에서 VPN 연결을 설정합니다.

이 문서에서는 PowerShell을 사용하여 Resource Manager 배포 모델에서 지점 및 사이트 간 연결로 VNet을 만드는 방법을 안내합니다. 지점 및 사이트 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [지점 및 사이트 간 FAQ](#faq)를 참조하세요.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 연결에 대한 배포 모델 및 메서드
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

아래 표에서는 P2S 구성에 대한 두 가지 배포 모델과 사용할 수 있는 배포 메서드를 보여 줍니다. 구성 단계를 포함한 문서를 사용할 수 있는 경우 아래 표에서 관련 링크를 직접 제공합니다.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>기본 워크플로
![Azure VNet-지점 및 사이트 간 연결 다이어그램에 컴퓨터 연결](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

이 시나리오에서는 지점 및 사이트 간 연결로 가상 네트워크를 만듭니다. 또한 이 지침은 해당 구성에 필요한 인증서를 생성하는 데도 도움이 됩니다. P2S 연결은 VPN 게이트웨이가 포함된 VNet, 루트 인증서 .cer 파일(공용 키), 클라이언트 인증서 및 클라이언트 쪽 VPN 구성으로 이루어집니다. 

이 구성에 대해 다음 값을 사용합니다. 문서의 섹션 [1](#declare) 에서 변수를 설정합니다. 단계를 계속 따라가며 값을 변경 없이 사용해도 되고, 사용자 환경을 반영하도록 값을 변경해도 됩니다. 

### <a name="example"></a>예제 값
* **이름: VNet1**
* **주소 공간: 192.168.0.0/16** 및 **10.254.0.0/16**<br>이 예제에서는 두 개의 주소 공간을 사용하여 이 구성이 여러 주소 공간에서 작동하는 것을 보여 줍니다. 하지만 이 구성에 여러 주소 공간이 반드시 필요한 것은 아닙니다.
* **서브넷 이름: FrontEnd**
  * **서브넷 주소 범위: 192.168.1.0/24**
* **서브넷 이름: BackEnd**
  * **서브넷 주소 범위: 10.254.1.0/24**
* **서브넷 이름: GatewaySubnet**<br>서브넷 이름 *GatewaySubnet*은 VPN Gateway가 작동하기 위한 필수 항목입니다.
  * **GatewaySubnet 주소 범위: 192.168.200.0/24** 
* **VPN 클라이언트 주소 풀: 172.16.201.0/24**<br>이 지점 및 사이트 간 연결을 사용하여 VNet에 연결되는 VPN 클라이언트는 VPN 클라이언트 주소 풀에서 IP 주소를 받습니다.
* **구독:** 구독이 2개 이상 있는 경우 올바른 구독을 사용 중인지 확인합니다.
* **리소스 그룹: TestRG**
* **위치: 미국 동부**
* **DNS 서버: 이름 확인에 사용할 DNS 서버의 IP 주소**.
* **GW 이름: Vnet1GW**
* **공용 IP 이름: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="before-beginning"></a>시작하기 전에
* Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 을 참조하세요. 이 구성에 PowerShell을 사용할 때 관리자 권한으로 실행되고 있는지 확인합니다. 

## <a name="declare"></a>1부 - 로그인 및 변수 설정
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

## <a name="ConfigureVNet"></a>2부 - VNet 구성
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


## <a name="Certificates"></a>3부 - 인증서
인증서는 지점 및 사이트 간 VPN에 대한 VPN 클라이언트를 인증하기 위해 Azure에 의해 사용됩니다. 루트 인증서를 만든 후 Base-64 인코딩된 X.509 .cer 파일로 공용 인증서 데이터(개인 키 아님)를 내보냅니다. 그런 다음 루트 인증서에서 Azure로 공용 인증서 데이터를 업로드합니다.

지점 및 사이트 간을 사용하여 VNet에 연결하는 각 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. 클라이언트 인증서는 루트 인증서에서 생성되고 각 클라이언트 컴퓨터에 설치됩니다. 유효한 클라이언트 인증서가 설치되어 있지 않고 클라이언트가 VNet에 연결하려고 하는 경우 인증이 실패합니다.

### <a name="cer"></a>1단계 - 루트 인증서용 .cer 파일 가져오기

####<a name="enterprise-certificate"></a>엔터프라이즈 인증서
 
엔터프라이즈 솔루션을 사용하는 경우 기존 인증서 체인을 사용할 수 있습니다. 사용할 루트 인증서용 .cer 파일을 가져옵니다.

####<a name="self-signed-root-certificate"></a>자체 서명된 루트 인증서

엔터프라이즈 인증서 솔루션을 사용하지 않는 경우 자체 서명된 루트 인증서를 만들어야 합니다. P2S 인증에 필요한 필드가 포함된 자체 서명된 루트 인증서를 만들기 위해 PowerShell을 사용할 수 있습니다. [PowerShell을 사용하여 지점 및 사이트 간 연결을 위해 자체 서명된 루트 인증서 만들기](vpn-gateway-certificates-point-to-site.md)에 자체 서명된 루트 인증서를 만드는 단계가 안내되어 있습니다.

> [!NOTE]
> 이전에 makecert는 자체 서명된 루트 인증서를 만들고 지점 및 사이트 간 연결에 대한 클라이언트 인증서를 생성하는 데 권장된 메서드였습니다. 이제 이러한 인증서를 만드는 데 PowerShell을 사용할 수 있습니다. PowerShell을 사용하는 이점 중 하나는 SHA-2 인증서를 만드는 기능입니다. 필요한 값은 [PowerShell을 사용하여 지점 및 사이트 간 연결에 대한 자체 서명된 루트 인증서 만들기](vpn-gateway-certificates-point-to-site.md)를 참조하세요.
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>자체 서명된 루트 인증서에 대한 공개 키를 내보내려면

지점 및 사이트 간 연결에는 Azure에 업로드될 공개 키(.cer)가 필요합니다. 다음 단계는 자체 서명된 루트 인증서에 대한 .cer 파일을 내보내는 데 도움이 됩니다.

1. 인증서에서 .cer 파일을 가져오려면 **certmgr.msc**를 엽니다. 일반적으로 'Certificates - Current User\Personal\Certificates'에서 자체 서명된 루트 인증서를 찾아 마우스 오른쪽 단추로 클릭합니다. **모든 태스크**를 클릭한 다음 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다.
2. 마법사에서 **다음**을 클릭합니다. **아니요, 개인 키를 내보내지 않습니다.**를 선택한 후 **다음**을 클릭합니다.
3. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)**를 선택한 후 **다음**을 클릭합니다. 
4. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
5. **마침** 을 클릭하여 인증서를 내보냅니다. **내보내기에 성공했습니다**라는 메시지가 표시됩니다. **확인**을 클릭하여 마법사를 닫습니다.

### <a name="generate"></a>2단계 - 클라이언트 인증서 생성
연결할 각 클라이언트의 고유한 인증서를 생성할 수도 있고 여러 클라이언트에서 동일한 인증서를 사용할 수도 있습니다. 고유한 클라이언트 인증서를 생성하면 필요할 경우 단일 인증서를 해지할 수 있는 장점이 있습니다. 그렇지 않은 경우 즉, 모든 구성원이 동일한 클라이언트 인증서를 사용하고 한 클라이언트에 대한 인증서를 해지해야 하는 경우, 인증서를 사용하는 모든 클라이언트에 대해 새 인증서를 생성하고 설치해야 합니다.

####<a name="enterprise-certificate"></a>엔터프라이즈 인증서
- 엔터프라이즈 인증서 솔루션을 사용하는 경우, 'domain name\username' 형식이 아닌 일반 이름 값 형식 'name@yourdomain.com'으로 클라이언트 인증서를 생성합니다.
- 발급한 클라이언트 인증서가 사용 목록의 첫 번째 항목으로 스마트 카드 로그온 등이 아닌 '클라이언트 인증'을 가지는 ‘사용자' 인증서 템플릿을 기준으로 하는지 확인합니다. 클라이언트 인증서를 두 번 클릭하고 **세부 정보 > 확장된 키 사용**을 확인하여 인증서를 확인할 수 있습니다.

####<a name="self-signed-root-certificate"></a>자체 서명된 루트 인증서 
자체 서명된 루트 인증서를 사용하는 경우 지점 및 사이트 간 연결과 호환되는 클라이언트 인증서를 생성하는 단계는 [PowerShell을 사용하여 클라이언트 인증서 생성](vpn-gateway-certificates-point-to-site.md#clientcert)을 참조하세요.


### <a name="exportclientcert"></a>3단계 - 클라이언트 인증서 내보내기

[PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) 지침을 사용하여 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하는 경우 생성하는 데 사용한 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 내보내야 합니다.
 
1. 클라이언트 인증서를 내보내려면 **certmgr.msc**를 엽니다. 내보낼 클라이언트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭한 다음 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다.
2. 마법사에서 **다음**을 클릭하고 **예, 개인 키를 내보냅니다.**를 선택한 후 **다음**을 클릭합니다.
3. **파일 내보내기 형식** 페이지에서 선택된 기본값을 유지합니다. **가능하면 인증 경로에 있는 인증서 모두 포함**을 선택했는지 확인합니다. 그런 후 **Next**를 클릭합니다.
4. **보안** 페이지에서 개인 키를 보호해야 합니다. 암호를 사용하도록 선택하는 경우 이 인증서에 대해 설정한 암호를 기록해두거나 기억합니다. 그런 후 **Next**를 클릭합니다.
5. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
6. **마침** 을 클릭하여 인증서를 내보냅니다.

### <a name="upload"></a>4단계 - 루트 인증서 .cer 파일 업로드

게이트웨이가 생성되었으면 신뢰할 수 있는 루트 인증서에 대한 .cer 파일을 Azure에 업로드할 수 있습니다. 최대 20개의 루트 인증서에 대한 파일을 업로드할 수 있습니다. 루트 인증서에 대한 개인 키를 Azure에 업로드하지 않습니다. .cer 파일이 업로드되었으면 Azure에서는 이 파일을 사용하여 가상 네트워크에 연결할 클라이언트를 인증합니다.

인증서 이름에 대한 변수를 선언하고 변수를 고유한 값으로 바꿉니다.

        $P2SRootCertName = "Mycertificatename.cer"

파일 경로를 고유한 값으로 바꾼 후 cmdlet을 실행합니다.

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>4부 - VPN Gateway 만들기
VNet용 가상 네트워크 게이트웨이를 구성하고 만듭니다. *-GatewayType*은 **Vpn**이어야 하고 *-VpnType*은 **RouteBased**이어야 합니다. 완료하려면 최대 45분이 걸릴 수 있습니다.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>5부 - VPN 클라이언트 구성 패키지 다운로드
P2S를 사용하여 Azure에 연결된 클라이언트에는 클라이언트 인증서와 VPN 클라이언트 구성 패키지가 설치되어야 합니다. VPN 클라이언트 구성 패키지는 Windows 클라이언트에 사용할 수 있습니다.

VPN 클라이언트 패키지는 Windows에 기본 제공되는 VPN 클라이언트 소프트웨어를 구성할 구성 정보를 포함합니다. 패키지는 추가 소프트웨어를 설치하지 않습니다. 설정은 연결하려는 가상 네트워크에 따라 다릅니다. 지원되는 클라이언트 운영 체제의 목록은 이 문서 끝의 [지점 및 사이트 간 연결 FAQ](#faq)를 참조하세요.

1. 게이트웨이를 만든 후에 클라이언트 구성 패키지를 다운로드할 수 있습니다. 이 예제에서는 64비트 클라이언트용 패키지를 다운로드합니다. 32비트 클라이언트를 다운로드하려는 경우 'Amd64'를h 'x86'으로 바꿉니다. Azure 포털을 사용하여 VPN 클라이언트를 다운로드할 수도 있습니다.
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. PowerShell cmdlet에서 URL 링크를 반환합니다. 다음은 반환된 URL 형태의 예입니다.
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. 반환된 링크를 웹 브라우저에 복사 및 붙여넣기하여 패키지를 다운로드합니다. 그런 다음 클라이언트 컴퓨터에 패키지를 설치합니다. SmartScreen 팝업이 나타나면 **자세한 정보**, **실행**을 차례로 클릭하여 패키지를 설치합니다.
4. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 클릭합니다. 나열된 연결이 표시됩니다. 연결된 가상 네트워크의 이름이 다음 예제와 비슷하게 표시됩니다. 
   
    ![VPN 클라이언트](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png)

## <a name="clientcertificate"></a>6부 - 내보낸 클라이언트 인증서 설치

클라이언트 인증서를 생성하는 데 사용한 것 외의 클라이언트 컴퓨터에서 P2S 연결을 만들려는 경우 클라이언트 인증서를 설치해야 합니다. 클라이언트 인증서를 설치할 때는 클라이언트 인증서를 내보낼 때 만든 암호가 필요합니다.

1. *.pfx* 파일을 찾아 클라이언트 컴퓨터에 복사합니다. 클라이언트 컴퓨터에서 *.pfx* 파일을 두 번 클릭하여 설치합니다. **저장소 위치**를 **현재 사용자**로 유지한 후 **다음**을 클릭합니다.
2. 가져올 **파일** 페이지에서 아무 것도 변경하지 않습니다. **다음**을 클릭합니다.
3. **개인 키 보호** 페이지에서 인증서를 사용한 경우 인증서에 대한 암호를 입력하거나 인증서를 설치하는 보안 주체가 올바른지 확인한 후 **다음**을 클릭합니다.
4. **인증서 저장소** 페이지에서 기본 위치를 유지한 후 **다음**을 클릭합니다.
5. **Finish**를 클릭합니다. 인증서 설치에 대한 **보안 경고**에서 **예**를 클릭합니다. 인증서를 생성했으므로 '예'를 클릭하면 됩니다. 이제 인증서를 성공적으로 가져왔습니다.

## <a name="connect"></a>7부 - Azure에 연결
1. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 연결로 이동하고 만든 VPN 연결을 찾습니다. 가상 네트워크와 같은 이름이 지정됩니다. **Connect**를 클릭합니다. 인증서 사용을 안내하는 팝업 메시지가 나타날 수 있습니다. 이 경우 **계속** 을 클릭하여 상승된 권한을 사용합니다. 
2. **연결** 상태 페이지에서 **연결**을 클릭하여 연결을 시작합니다. **인증서 선택** 화면에서 표시되는 클라이언트 인증서가 연결하는 데 사용할 인증서인지 확인합니다. 그렇지 않은 경우 드롭다운 화살표를 사용하여 올바른 인증서를 선택한 다음 **확인**을 클릭합니다.
   
    ![VPN 클라이언트를 Azure에 연결](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. 이제 연결이 설정됩니다.
   
    ![설정된 연결](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

> [!NOTE]
> 엔터프라이즈 CA 솔루션을 사용하여 발급된 인증서를 사용하고 인증에 문제가 발생하는 경우 클라이언트 인증서에서 인증 순서를 확인합니다. 클라이언트 인증서를 두 번 클릭하고 **세부 정보 > 확장된 키 사용**으로 이동하여 인증 목록 순서를 확인할 수 있습니다. 목록의 첫 번째 항목으로 '클라이언트 인증'이 표시되는지 확인합니다. 그렇지 않으면 목록에서 첫 번째 항목으로 클라이언트 인증을 가진 사용자 템플릿을 기반으로 하는 클라이언트 인증서를 발급해야 합니다. 
>
>

## <a name="verify"></a>8부 - 연결 확인
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

## <a name="addremovecert"></a>신뢰할 수 있는 루트 인증서 추가 또는 제거

Azure에서 신뢰할 수 있는 루트 인증서를 추가 및 제거할 수 있습니다. 신뢰할 수 있는 인증서를 제거하면 루트 인증서에서 생성된 클라이언트 인증서는 더 이상 지점 및 사이트 간을 통해 Azure에 연결할 수 없습니다. 클라이언트를 연결하려는 경우 Azure에서 신뢰할 수 있는 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다.


### <a name="to-add-a-trusted-root-certificate"></a>신뢰할 수 있는 루트 인증서를 추가하려면
Azure에 최대 20개의 신뢰할 수 있는 루트 인증서 .cer 파일을 추가할 수 있습니다. 아래 단계를 따라 루트 인증서를 추가합니다.

1. Azure에 추가할 새 루트 인증서를 만들고 준비합니다. Base-64로 인코딩된 X.509(.CER)로 공개 키를 내보내고 텍스트 편집기로 엽니다. 아래에 표시된 섹션만 복사합니다. 
   
    다음 예제와 같이 값을 복사합니다.
   
    ![인증서](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

    > [!NOTE]
    > 인증서 데이터를 복사하는 경우 캐리지 리턴 또는 줄 바꿈 없이 하나의 연속 줄로 텍스트를 복사합니다. 캐리지 리턴 및 줄 바꿈을 보려면 ‘기호 표시/모든 문자 표시'에 대한 텍스트 편집기의 보기를 수정해야 할 수도 있습니다.                                                                                                                                                                            
    >


2. 인증서 이름 및 키 정보를 변수로 지정합니다. 다음 예제에 나와 있는 것처럼 정보를 고유한 정보로 바꿉니다.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. 새 루트 인증서를 추가합니다. 한번에 하나의 인증서만 추가할 수 있습니다.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. 다음 예제를 사용하여 새 인증서가 올바르게 추가되었는지 확인할 수 있습니다.
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>신뢰할 수 있는 루트 인증서를 제거하려면
Azure에서 신뢰할 수 있는 루트 인증서를 제거할 수 있습니다. 신뢰할 수 있는 인증서를 제거하면 루트 인증서에서 생성된 클라이언트 인증서는 더 이상 지점 및 사이트 간을 통해 Azure에 연결할 수 없습니다. 클라이언트를 연결하려는 경우 Azure에서 신뢰할 수 있는 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다.

1. 변수를 선언합니다.
   
        $GWName = "Name_of_virtual_network_gateway"
        $RG = "Name_of_resource_group"
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. 인증서를 제거합니다.
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. 다음 예제를 사용하여 인증서가 성공적으로 제거되었는지 확인합니다. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>클라이언트 인증서 해지
클라이언트 인증서를 해지할 수 있습니다. 인증서 해지 목록에서 개별 클라이언트 인증서를 기반으로 하는 지점 및 사이트 간 연결을 선택적으로 거부할 수 있습니다. 이것은 신뢰할 수 있는 루트 인증서를 제거하는 것과 다릅니다. Azure에서 신뢰할 수 있는 루트 인증서 .cer를 제거하면, 해지된 루트 인증서로 생성/서명된 모든 클라이언트 인증서에 대한 액세스 권한도 해지됩니다. 루트 인증서가 아닌 클라이언트 인증서를 해지해야 루트 인증서로부터 생성된 다른 인증서를 지점 및 사이트 간 연결을 위한 인증에 계속 사용할 수 있습니다.

해지된 클라이언트 인증서를 사용하는 동안 개별 사용자의 세분화된 액세스 제어를 위해 일반적으로 루트 인증서를 사용하여 팀 또는 조직 수준에서 액세스를 관리합니다.

### <a name="to-revoke-a-client-certificate"></a>클라이언트 인증서를 해지하려면

1. 클라이언트 인증서 지문을 검색합니다. 자세한 내용은 [방법: 인증서의 지문 검색](https://msdn.microsoft.com/library/ms734695.aspx)을 참조하세요.
2. 텍스트 편집기에 정보를 복사하고 연속 문자열이 되도록 공백을 모두 제거합니다. 이것을 변수로 선언합니다.
3. 변수를 선언합니다. 이전 단계에서 검색된 지문을 선언해야 합니다.
   
        $RevokedClientCert1 = "NameofCertificate"
        $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
        $GWName = "Name_of_virtual_network_gateway"
        $RG = "Name_of_resource_group"
4. 해지된 인증서 목록에 지문을 추가합니다. 지문이 추가되면 "성공"이 표시됩니다.
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
        -Thumbprint $RevokedThumbprint1
5. 지문이 인증서 해지 목록에 추가되었는지 확인합니다.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
6. 지문이 추가된 후에는 인증서를 더 이상 연결에 사용할 수 없습니다. 이 인증서를 사용하여 연결하려는 클라이언트에게는 인증서가 더 이상 유효하지 않다는 메시지가 표시됩니다.

### <a name="to-reinstate-a-client-certificate"></a>클라이언트 인증서를 복구하려면
해지된 클라이언트 인증서 목록에서 지문을 제거하여 클라이언트 인증서를 복구할 수 있습니다.

1. 변수를 선언합니다. 복구하려는 인증서에 대한 올바른 지문을 선언해야 합니다.
 
        $RevokedClientCert1 = "NameofCertificate"
        $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
        $GWName = "Name_of_virtual_network_gateway"
        $RG = "Name_of_resource_group"

2. 인증서 지문을 인증서 해지 목록에서 제거합니다.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. 해지된 목록에서 지문이 제거되었는지 확인합니다.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
## <a name="faq"></a>지점 및 사이트 간 FAQ

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요. 네트워킹 및 가상 컴퓨터에 대한 자세한 내용은 [Azure 및 Linux VM 네트워크 개요](../virtual-machines/linux/azure-vm-network-overview.md)를 참조하세요.



