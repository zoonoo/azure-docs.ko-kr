<properties 
   pageTitle="Azure 가상 네트워크에 지점 및 사이트 간 VPN 게이트웨이 연결 구성 | Microsoft Azure"
   description="지점 및 사이트 간 VPN 연결을 만들어 Azure 가상 네트워크에 안전하게 연결합니다. 이 구성은 VPN 장치를 사용하지 않는 원격 위치에서 프레미스 간 연결이 필요한 경우 도움이 되며 하이브리드 네트워크 구성과 함께 사용할 수 있습니다. 이 문서는 리소스 관리자 배포 모델을 사용하여 만든 VNet에 대한 PowerShell 지침을 포함합니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/11/2016"
   ms.author="cherylmc" />

# PowerShell을 사용하여 가상 네트워크에 지점 및 사이트 간 연결 구성

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [PowerShell - Classic](vpn-gateway-point-to-site-create.md)

지점 및 사이트 간 구성을 사용하면 개별적으로 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. 클라이언트 컴퓨터에서 연결을 시작하여 VPN 연결을 설정합니다. 지점 및 사이트 간 연결은 집 또는 회의와 같은 원격 위치에서 VNet에 연결하려는 경우 또는 몇 명의 클라이언트만 가상 네트워크에 연결해야 하는 경우에 사용할 수 있는 뛰어난 솔루션입니다. 지점 및 사이트 간 연결을 작동하는 데는 VPN 장치 또는 공용 IP 주소가 필요하지 않습니다. 지점 및 사이트 간 연결에 대한 자세한 내용은 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections) 및 [프레미스 간 연결 정보](vpn-gateway-cross-premises-options.md)를 참조하세요.

이 문서는 **Azure 리소스 관리자** 배포 모델을 사용하여 만든 VNet 및 VPN 게이트웨이에 적용됩니다. 서비스 관리(클래식 배포 모델이라고도 함)를 사용하여 만든 VNet에 대한 지점 및 사이트 간 연결을 구성하려는 경우 [이 문서](vpn-gateway-point-to-site-create.md)를 참조하세요.

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 이 구성에 대한 정보

이 시나리오에서는 지점 및 사이트 간 연결로 가상 네트워크를 만듭니다. 지점 및 사이트 간 연결은 게이트웨이가 포함된 VNet, 업로드한 루트 인증서 .cer 파일, 클라이언트 인증서 및 클라이언트 쪽 VPN 구성으로 이루어집니다.

이 구성에 대해 다음 값을 사용합니다.

- 이름 = **TestVNet**, 주소 공간 192.168.0.0/16 및 10.254.0.0/16 사용. VNet에는 하나 이상의 주소 공간을 사용할 수 있습니다.
- 서브넷 이름 = **FrontEnd**, 192.168.1.0/24 사용
- 서브넷 이름 = **BackEnd**, 10.254.1.0/24 사용
- 서브넷 이름 = **GatewaySubnet**, 192.168.200.0/24 사용. 서브넷 이름 *GatewaySubnet*은 게이트웨이가 작동하기 위한 필수 항목입니다. 
- VPN 클라이언트 주소 풀: 172.16.201.0/24. 이 지점 및 사이트 간 연결을 사용 하여 VNet에 연결되는 VPN 클라이언트는 이 풀에서 IP 주소를 받습니다.
- 구독 = 하나 이상 있는 경우 올바른 구독인지 확인합니다.
- 리소스 그룹 = **TestRG**
- 위치 = **미국 동부**
- DNS 서버 = 이름 확인에 사용할 DNS 서버의 IP 주소.
- GW 이름 = **GW**
- 공용 IP 이름 = **GWIP**
- VpnType = **RouteBased**


## 시작하기 전에

Azure 구독이 있고 이 구성에 필요한 Azure PowerShell cmdlet을 설치했는지 확인합니다(1.0.2 이상). Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
	
**PowerShell cmdlet 모듈 설치에 대한 정보**

	[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

## Azure에 대한 지점 및 사이트 간 연결 구성

1. PowerShell 콘솔에서 Azure 계정에 로그인합니다. 이 cmdlet은 Azure 계정에 대한 로그인 자격 증명을 입력하라는 메시지를 표시합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.

		Login-AzureRmAccount 

2. Azure 구독 목록을 가져옵니다.

		Get-AzureRmSubscription

3. 사용할 구독을 지정합니다.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. 이 구성에서는 사용할 값과 함께 다음 PowerShell 변수가 선언됩니다. 선언된 값은 예제 스크립트에 사용됩니다. 사용할 값을 선언합니다. 필요할 경우 아래 샘플(사용자 고유의 값으로 대체)을 사용합니다.

		$VNetName  = "TestVNet"
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
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"

5. 새 리소스 그룹을 만듭니다.

		New-AzureRmResourceGroup -Name $RG -Location $Location

6. 가상 네트워크에 대한 서브넷 구성을 만들고 *FrontEnd*, *BackEnd* 및 *GatewaySubnet*으로 이름을 지정합니다. 이러한 접두사는 위에서 선언된 VNet 주소 공간의 일부여야 합니다.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. 가상 네트워크 만들기 지정된 DNS 서버는 연결 중인 리소스에 대한 이름을 확인할 수 있는 DNS 서버여야 합니다. 이 예제에서는 공용 IP 주소를 사용했지만 여기에 고유한 값을 입력하려고 합니다.

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. 방금 만든 가상 네트워크에 대한 변수를 지정합니다.

		$vnet   = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. 동적으로 할당된 공용 IP 주소를 요청합니다. 이 IP 주소는 게이트웨이가 제대로 작동하는 데 필요합니다. 나중에 게이트웨이를 게이트웨이 IP 구성에 연결합니다.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Azure에 루트 인증서 .cer 파일을 업로드합니다. 엔터프라이즈 인증서 환경에서 루트 인증서를 사용하거나 자체 서명된 루트 인증서를 사용할 수 있습니다. 최대 20개의 루트 인증서를 업로드할 수 있습니다. *makecert*를 사용하여 자체 서명된 루트 인증서를 만드는 방법은 이 문서의 끝에 있는 섹션을 참조하세요. .cer 파일은 루트 인증서의 개인 키를 포함해서는 안 됩니다.
	
	다음은 이 구조에 대한 샘플입니다. 공용 인증서 데이터를 업로드하는 과정 중 어려운 부분은 전체 문자열을 공백없이 복사 및 붙여넣기 해야 한다는 점입니다. 그렇지 않으면 업로드가 동작하지 않습니다. 이 단계에 고유한 인증서.cer 파일을 사용해야 합니다. 아래에서 샘플을 복사 및 붙여넣기 하지 마세요.

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. VNet용 가상 네트워크 게이트웨이를 만듭니다. GatewayType은 Vpn이어야 하고 VpnType은 RouteBased여야 합니다.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## 클라이언트 구성

지점 및 사이트를 사용하여 Azure에 연결하는 각 클라이언트에는 두 가지 조건이 필요합니다. VPN 클라이언트가 연결되도록 구성되어야 하고 클라이언트에 클라이언트 인증서가 설치되어 있어야 합니다. VPN 클라이언트 구성 패키지는 Windows 클라이언트에 사용할 수 있습니다. 자세한 내용은 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections)를 참조하세요.

1. VPN 클라이언트 구성 패키지를 다운로드합니다. 이 단계에서는 다음 예제를 사용하여 클라이언트 구성 패키지를 다운로드합니다.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	PowerShell cmdlet에서 URL 링크를 반환합니다. 반환된 링크를 웹 브라우저에 복사 및 붙여넣기 하여 패키지를 컴퓨터에 다운로드합니다. 반환된 URL의 예는 다음과 같습니다.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. 클라이언트 컴퓨터의 루트 인증서에서 생성된 클라이언트 인증서(*.pfx)를 생성하고 설치합니다. 익숙한 설치 방법을 어떤 것이든 사용할 수 있습니다. 자체 서명된 루트 인증서를 사용 중이고 이 작업에 익숙하지 않은 경우 이 문서의 끝에 있는 지침을 참조하면 됩니다.

3. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 연결로 이동하고 방금 만든 VPN 연결을 찾습니다. 가상 네트워크와 같은 이름입니다. **Connect**를 클릭합니다. 인증서 사용을 안내하는 팝업 메시지가 나타날 수 있습니다. 이 경우 **계속**을 클릭하여 상승된 권한을 사용합니다.

4. **연결** 상태 페이지에서 연결을 시작하도록 **연결**을 클릭합니다. **인증서 선택** 화면에서 표시되는 클라이언트 인증서가 연결하는 데 사용할 인증서인지 확인합니다. 그렇지 않은 경우 드롭다운 화살표를 사용하여 올바른 인증서를 선택한 다음 **확인**을 클릭합니다.

5. 이제 연결이 설정됩니다. 다음 절차를 사용하여 연결을 확인할 수 있습니다.

## 연결 확인

1. VPN 연결이 활성인지를 확인하려면, 관리자 권한 명령 프롬프트를 열고 *ipconfig/all*을 실행합니다.

2. 결과를 확인합니다. 받은 IP 주소가 구성에 지정한 지점 및 사이트 VPN 클라이언트 주소 풀 내의 주소 중 하나인지 확인합니다. 결과는 다음과 같아야 합니다.
    
		PPP adapter VNetEast:
			Connection-specific DNS Suffix .:
			Description.....................: VNetEast
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## makecert를 사용하여 자체 서명된 루트 인증서를 만들려면

Makecert는 자체 서명된 루트 인증서를 만드는 한 가지 방법입니다. 아래 단계는 이에 대한 절차를 안내합니다.

1. Windows 10을 실행하는 컴퓨터에서 [이 링크](https://dev.windows.com/en-us/downloads/windows-10-sdk)를 통해 Windows 10 SDK를 다운로드하여 설치합니다.

2. 설치가 끝나면 C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch> 경로 아래에서 makecert.exe 유틸리티를 찾을 수 있습니다.
		
	예제:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. 다음으로 컴퓨터의 개인 인증서 저장소에 루트 인증서를 만들고 설치합니다. 아래 예제에서는 나중에 업로드할 해당 *.cer* 파일을 만듭니다. 관리자 권한으로 다음 명령을 실행합니다. 여기서 *RootCertificateName*은 인증서에 사용할 이름입니다.

	참고: 다음 예제를 변경하지 않고 실행하면 결과는 루트 인증서 및 해당 파일 *RootCertificateName.cer*입니다. 명령을 실행한 디렉터리에서 .cer 파일을 찾을 수 있습니다. 인증서는 사용자 인증서 위치인 Current User\\Personal\\Certificates에 있습니다.

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]클라이언트 인증서를 생성할 루트 인증서를 만들었기 때문에 이 인증서를 해당 개인 키와 함께 내보내고 복구 가능한 안전한 위치에 저장할 수 있습니다.

## 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하려면

다음 단계를 사용하면 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성한 후 클라이언트 인증서를 내보내고 설치할 수 있습니다.

### 클라이언트 인증서 생성

다음 단계는 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하는 한 가지 방법을 안내합니다. 동일한 루트 인증서에서 여러 클라이언트 인증서를 생성할 수 있습니다. 그런 다음 각 클라이언트 인증서를 내보내고 클라이언트 컴퓨터에 설치할 수 있습니다.

1. 자체 서명된 루트 인증서를 만드는 데 사용한 동일한 컴퓨터에서 관리자로 명령 프롬프트를 엽니다.
2. 클라이언트 인증서 파일을 저장하려는 위치로 디렉터리를 변경합니다. *RootCertificateName*은 생성된 자체 서명된 루트 인증서를 말합니다. RootCertificateName을 루트 인증서 이름으로 변경하여 다음 예제를 실행하면 결과는 개인 인증서 저장소의 "ClientCertificateName"이라는 클라이언트 인증서입니다.
3. 다음 명령을 입력합니다.

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. 모든 인증서는 컴퓨터의 사용자 인증서 위치인 Current User\\Personal\\Certificates 저장소에 저장됩니다. 이 절차를 기반으로 필요에 따라 많은 수의 클라이언트 인증서를 생성할 수 있습니다.

### 클라이언트 인증서 내보내기 및 설치

가상 네트워크에 연결하려는 각 컴퓨터에 클라이언트 인증서를 설치하는 작업은 필수 단계입니다. 다음 단계에서는 클라이언트 인증서의 수동 설치를 안내합니다.

1. 클라이언트 인증서를 내보내려면 *certmgr.msc*를 사용합니다. 내보낼 클라이언트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭 한 다음 **내보내기**를 클릭합니다. 그러면 인증서 내보내기 마법사가 열립니다.
2. 마법사에서 **다음**을 클릭하고 **예, 개인 키를 내보냅니다.**를 선택한 후 **다음**을 클릭합니다.
3. **파일 내보내기 형식** 페이지에서 선택된 기본값을 유지할 수 있습니다. 그런 후 **Next**를 클릭합니다.  
4. **보안** 페이지에서 개인 키를 보호해야 합니다. 암호를 사용하도록 선택하는 경우 이 인증서에 대해 설정한 암호를 기록해두거나 기억한 후 **다음**을 클릭합니다.
5. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
6. **마침**을 클릭하여 인증서를 내보냅니다.	
3. *.pfx* 파일을 찾아 클라이언트 컴퓨터에 복사합니다. 클라이언트 컴퓨터에서 *.pfx* 파일을 두 번 클릭하여 설치합니다. **저장소 위치**를 **현재 사용자**로 유지한 후 **다음**을 클릭합니다.
4. 가져올 **파일** 페이지에서 아무 것도 변경하지 않습니다. **다음**을 클릭합니다.
5. **개인 키 보호** 페이지에서 인증서를 사용한 경우 인증서에 대한 암호를 입력하거나 인증서를 설치하는 보안 주체가 올바른지 확인한 후 **다음**을 클릭합니다.
6. **인증서 저장소** 페이지에서 기본 위치를 유지한 후 **다음**을 클릭합니다.
7. **마침**을 클릭합니다. 인증서 설치에 대한 **보안 경고**에서 **예**를 클릭합니다. 이제 인증서를 성공적으로 가져왔습니다.

## 다음 단계

가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 단계는 [가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-tutorial.md)를 참조하세요.

<!---HONumber=AcomDC_0114_2016-->