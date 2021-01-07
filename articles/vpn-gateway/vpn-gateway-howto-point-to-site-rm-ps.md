---
title: '컴퓨터에서 VNet에 연결-P2S VPN 및 네이티브 Azure 인증서 인증: PowerShell'
description: P2S 및 자체 서명 된 인증서 또는 CA에서 발급 한 인증서를 사용 하 여 Windows 및 macOS 클라이언트를 Azure 가상 네트워크에 안전 하 게 연결 합니다. 이 문서에서는 PowerShell을 사용합니다.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed0a60c88c33af70b7d780d6c4735c5f8e65b35b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660409"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>네이티브 Azure 인증서 인증을 사용 하 여 VNet에 지점 및 사이트 간 VPN 연결 구성: PowerShell

이 문서는 Windows, Linux 또는 macOS를 실행 하는 개별 클라이언트를 Azure VNet에 안전 하 게 연결 하는 데 도움이 됩니다. 지점 및 사이트 간 VPN 연결은 집 또는 회의에서 원격 통신 하는 경우와 같이 원격 위치에서 VNet에 연결 하려는 경우에 유용 합니다. 또한 VNet에 연결해야 하는 몇 가지 클라이언트만 있는 경우 사이트 간 VPN 대신 P2S를 사용할 수도 있습니다. 지점 및 사이트 간 연결에는 VPN 장치 또는 공용 IP 주소가 필요 하지 않습니다. P2S는 SSTP(Secure Socket Tunneling Protocol) 또는 IKEv2를 통한 VPN 연결을 만듭니다.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="컴퓨터에서 Azure VNet 지점 및 사이트 간 연결 다이어그램에 연결":::

지점 및 사이트 간 VPN에 대 한 자세한 내용은 지점 및 [사이트 간 Vpn 정보](point-to-site-about.md)를 참조 하세요. Azure Portal를 사용 하 여이 구성을 만들려면 [Azure Portal를 사용 하 여 지점 및 사이트 간 VPN 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md)을 참조 하세요.

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> 이 문서의 여러 단계에서 Azure Cloud Shell를 사용할 수 있습니다. 그러나 Cloud Shell를 사용 하 여 인증서를 생성할 수는 없습니다. 또한 루트 인증서 공개 키를 업로드 하려면 Azure PowerShell을 로컬로 사용 하거나 Azure Portal를 사용 해야 합니다.
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. 로그인

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. 변수 선언

이 문서에 대 한 변수를 사용 하 여 예제 자체를 변경할 필요 없이 사용자 환경에 적용할 값을 쉽게 변경할 수 있습니다. 사용할 변수를 선언합니다. 필요한 경우 사용자 고유의 값으로 대체 하 여 다음 샘플을 사용할 수 있습니다. 이 연습을 수행하는 동안 PowerShell/Cloud Shell 세션을 닫게 되는 경우 값을 복사하고 붙여넣어 변수를 다시 선언하세요.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. VNet 구성

1. 리소스 그룹을 만듭니다.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. 가상 네트워크에 대 한 서브넷 구성을 만들고 *프런트 엔드* 및 *게이트웨이 서브넷* 의 이름을 지정 합니다. 이러한 접두사는 선언된 VNet 주소 공간의 일부여야 합니다.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. 가상 네트워크 만들기

   이 예제에서 -DnsServer 서버 매개 변수는 선택 사항입니다. 값을 지정하더라도 새 DNS 서버를 만들지 않습니다. 지정한 DNS 서버 IP 주소는 VNet에서 연결하는 리소스에 대한 이름을 확인할 수 있는 DNS 서버여야 합니다. 이 예에서는 개인 IP 주소를 사용하지만 DNS 서버의 IP 주소가 아닐 가능성이 높습니다. 고유한 값을 사용해야 합니다. 지정한 값은 P2S 연결 또는 VPN 클라이언트가 아니라 VNet에 배포한 리소스에서 사용됩니다.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. 만든 가상 네트워크에 대한 변수를 지정합니다.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. VPN Gateway에는 공용 IP 주소가 있어야 합니다. 먼저 IP 주소 리소스를 요청하고, 가상 네트워크 게이트웨이를 만들 때 참조합니다. VPN Gateway가 생성될 때 IP 주소는 리소스에 동적으로 할당됩니다. 현재 VPN Gateway는 *동적* 공용 IP 주소 할당만 지원합니다. 고정 공용 IP 주소 할당을 요청할 수 없습니다. 하지만 IP 주소가 VPN 게이트웨이에 할당된 후 변경되는 것은 아닙니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 공용 IP 주소가 변경됩니다. VPN Gateway의 크기 조정, 다시 설정 또는 기타 내부 유지 관리/업그레이드 시에는 변경되지 않습니다.

   동적으로 할당된 공용 IP 주소를 요청합니다.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. VPN gateway 만들기

이 단계에서는 VNet에 대 한 가상 네트워크 게이트웨이를 구성 하 고 만듭니다.

* -GatewayType은 **Vpn** 이어야 하고 -VpnType은 **RouteBased** 이어야 합니다.
* -VpnClientProtocol은 사용하려는 터널의 유형을 지정하는 데 사용됩니다. 터널 옵션은 **Openvpn, SSTP** 및 **IKEv2** 입니다. 이러한 항목 중 하나 또는 지원 되는 모든 조합을 사용 하도록 선택할 수 있습니다. 여러 형식을 사용 하도록 설정 하려면 이름을 쉼표로 구분 하 여 지정 합니다. OpenVPN 및 SSTP를 함께 사용 하도록 설정할 수 없습니다. Android 및 Linux의 strongSwan 클라이언트와 iOS 및 OSX의 네이티브 IKEv2 VPN 클라이언트는 IKEv2 터널만 사용하여 연결합니다. Windows 클라이언트는 IKEv2를 먼저 시도하고 연결되지 않는 경우 SSTP로 대체합니다. OpenVPN 클라이언트를 사용 하 여 OpenVPN 터널 형식에 연결할 수 있습니다.
* 가상 네트워크 게이트웨이 ' 기본 ' SKU는 IKEv2, OpenVPN 또는 RADIUS 인증을 지원 하지 않습니다. Mac 클라이언트를 가상 네트워크에 연결하려는 경우 기본 SKU를 사용하지 마세요.
* VPN 게이트웨이는 선택한 [게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md)에 따라 완료하는 데 최대 45분이 걸릴 수 있습니다. 이 예제에서는 IKEv2를 사용합니다.

1. VNet용 가상 네트워크 게이트웨이를 구성하고 만듭니다. 게이트웨이가 생성 되는 데 약 45 분이 소요 됩니다.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. 게이트웨이가 생성 되 면 다음 예제를 사용 하 여 볼 수 있습니다. PowerShell을 닫거나 게이트웨이가 생성 되는 동안 시간이 초과 된 경우 변수를 다시 [선언할](#declare) 수 있습니다.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. VPN 클라이언트 주소 풀 추가

VPN 게이트웨이에서 만들기를 완료한 후에 VPN 클라이언트 주소 풀을 추가할 수 있습니다. VPN 클라이언트 주소 풀은 연결할 때 VPN 클라이언트에서 IP 주소를 받는 범위입니다. 연결 원본이 되는 온-프레미스 위치 또는 연결 대상이 되는 VNet과 겹치지 않는 개인 IP 주소 범위를 사용합니다.

이 예에서 VPN 클라이언트 주소 풀은 이전 단계에서 [변수로](#declare) 선언 됩니다.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. 인증서 생성

>[!IMPORTANT]
> Azure Cloud Shell를 사용 하 여 인증서를 생성할 수 없습니다. 이 섹션에 설명 된 방법 중 하나를 사용 해야 합니다. PowerShell을 사용 하려는 경우 로컬로 설치 해야 합니다.
>

인증서는 Azure에서 지점 및 사이트 간 vpn에 대 한 VPN 클라이언트를 인증 하는 데 사용 됩니다. Azure에 루트 인증서의 공개 키 정보를 업로드합니다. 그러면 해당 공개 키가 '신뢰할 수 있는 키'로 간주됩니다. 클라이언트 인증서는 신뢰할 수 있는 루트 인증서에서 생성한 다음 각 클라이언트 컴퓨터의 [인증서 - 현재 사용자/개인] 인증서 저장소에 설치해야 합니다. 인증서는 VNet에 대한 연결을 시작할 때 해당 클라이언트를 인증하는 데 사용됩니다. 

자체 서명된 인증서를 사용하는 경우 특정 매개 변수를 사용하여 만들어야 합니다. [PowerShell 및 Windows 10](vpn-gateway-certificates-point-to-site.md)에 대한 지침을 사용하여 자체 서명된 인증서를 만들 수 있고 Windows 10을 사용하지 않는 경우 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)를 사용할 수 있습니다. 자체 서명된 루트 인증서 및 클라이언트 인증서를 생성할 때 지침에 나와 있는 단계를 따르는 것이 중요합니다. 그렇지 않으면 생성된 인증서가 P2S 연결과 호환되지 않으며 연결 오류가 발생하게 됩니다.

### <a name="root-certificate"></a><a name="cer"></a>루트 인증서

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. 루트 인증서를 만든 후에는 공용 인증서 데이터 (개인 키 아님)를 b a s e 64로 인코딩된 x.509 파일로 [내보냅니다](vpn-gateway-certificates-point-to-site.md#cer) .

### <a name="client-certificate"></a><a name="generate"></a>클라이언트 인증서

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. 클라이언트 인증서를 만든 후 [내보냅니다](vpn-gateway-certificates-point-to-site.md#clientexport) . 클라이언트 인증서가 연결 되는 클라이언트 컴퓨터에 배포 됩니다.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. 루트 인증서 공개 키 정보를 업로드 합니다.

VPN 게이트웨이에서 만들기가 완료되었는지 확인합니다. 완료되었으면 신뢰할 수 있는 루트 인증서의 .cer 파일(공개 키 정보 포함)을 Azure로 업로드할 수 있습니다. a.cer 파일이 업로드되면 Azure는 이.cer 파일을 사용하여 신뢰할 수 있는 루트 인증서에서 생성된 클라이언트 인증서를 설치한 클라이언트를 인증합니다. 필요한 경우 나중에 신뢰할 수 있는 루트 인증서 파일을 최대 20개까지 추가로 업로드할 수 있습니다.

>[!NOTE]
> Azure Cloud Shell를 사용 하 여 .cer 파일을 업로드할 수 없습니다. 컴퓨터에서 로컬로 PowerShell을 사용 하거나 [Azure Portal 단계](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)를 사용할 수 있습니다.
>

1. 인증서 이름에 대한 변수를 선언하고 변수를 고유한 값으로 바꿉니다.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. 파일 경로를 고유한 값으로 바꾼 후 cmdlet을 실행합니다.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. 공개 키 정보를 Azure에 업로드합니다. 인증서 정보가 업로드되면 Azure는 해당 인증서를 신뢰할 수 있는 루트 인증서로 간주합니다. 업로드할 때 컴퓨터에서 로컬로 PowerShell을 실행 하 고 있는지 확인 하 고, 대신 [Azure Portal 단계](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)를 사용할 수 있습니다. Azure Cloud Shell를 사용 하 여 업로드할 수 없습니다.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. 내보낸 클라이언트 인증서 설치

다음 단계는 Windows 클라이언트에를 설치 하는 데 도움이 됩니다. 추가 클라이언트 및 자세한 내용은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조 하세요.

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

전체 인증서 체인(즉, 기본값)과 함께.pfx로 클라이언트 인증서를 내보냈는지 확인합니다. 그렇지 않은 경우, 루트 인증서 정보가 클라이언트 컴퓨터에 존재하지 않으며 클라이언트를 제대로 인증할 수 없습니다.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. VPN 클라이언트 구성

이 섹션에서는 가상 네트워크 게이트웨이에 연결 하도록 컴퓨터에 대 한 native client를 구성 합니다. 예를 들어 Windows 컴퓨터에서 VPN 설정으로 이동 하면 VPN 연결을 추가할 수 있습니다. 지점 및 사이트 간 연결에는 특정 구성 설정이 필요 합니다. 이러한 단계를 통해 기본 VPN 클라이언트가 지점 및 사이트 간 연결을 통해 가상 네트워크에 연결 해야 하는 특정 설정을 사용 하 여 패키지를 만들 수 있습니다.

다음 빠른 예제를 사용 하 여 클라이언트 구성 패키지를 생성 하 고 설치할 수 있습니다. VPN 클라이언트 구성 파일을 생성 하 고 설치 하는 방법에 대 한 자세한 내용과 패키지 콘텐츠에 대 한 자세한 내용은 [vpn 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md)를 참조 하세요.

변수를 다시 선언 해야 하는 경우 [여기](#declare)에서 찾을 수 있습니다.

### <a name="to-generate-configuration-files"></a>구성 파일을 생성 하려면

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>클라이언트 구성 패키지를 설치 하려면

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Azure에 연결

### <a name="windows-vpn-client"></a>Windows VPN 클라이언트

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Mac VPN 클라이언트

[네트워크] 대화 상자에서 사용하려는 클라이언트 프로필을 찾은 다음 **연결** 을 클릭합니다.
자세한 지침은 [설치 - Mac(OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac)에서 확인하세요. 연결에 문제가 발생하면 가상 네트워크 게이트웨이가 기본 SKU를 사용하지 않는지 확인합니다. 기본 SKU는 Mac 클라이언트에서 지원되지 않습니다.

  ![Mac 연결](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>연결을 확인 하려면

이러한 지침은 Windows 클라이언트에 적용됩니다.

1. VPN 연결이 활성인지를 확인하려면, 관리자 권한 명령 프롬프트를 열고 *ipconfig/all* 을 실행합니다.
2. 결과를 확인합니다. 받은 IP 주소가 구성에서 지정한 지점 및 사이트 간 VPN 클라이언트 주소 풀 내의 주소 중 하나 인지 확인 합니다. 결과는 다음 예제와 비슷합니다.

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>가상 컴퓨터에 연결하려면

이러한 지침은 Windows 클라이언트에 적용됩니다.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* DNS 서버 IP 주소가 VNet에 지정된 후에 VPN 클라이언트 구성 패키지가 생성되었는지 확인합니다. DNS 서버 IP 주소를 업데이트한 경우 새 VPN 클라이언트 구성 패키지를 생성하고 설치합니다.

* 'ipconfig'를 사용하여 연결하는 컴퓨터의 이더넷 어댑터에 할당된 IPv4 주소를 확인합니다. IP 주소가 연결하는 VNet의 주소 범위 또는 VPNClientAddressPool의 주소 범위 내에 있는 경우 이 주소를 겹치는 주소 공간이라고 합니다. 주소 공간이 이러한 방식으로 겹치면 네트워크 트래픽이 Azure에 도달하지 않고 로컬 네트워크에 남아 있습니다.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>루트 인증서 추가 또는 제거하려면

Azure에서 신뢰할 수 있는 루트 인증서를 추가 및 제거할 수 있습니다. 루트 인증서를 제거하면 해당 루트 인증서에서 생성된 인증서가 있는 클라이언트를 인증하지 못하게 되며 연결할 수도 없습니다. 클라이언트를 인증하고 연결하려는 경우 Azure에 (업로드된)신뢰할 수 있는 루트 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다. 이러한 단계를 수행 하려면 Azure PowerShell cmdlet이 컴퓨터에 로컬로 설치 되어 있어야 합니다 (Azure Cloud Shell 하지 않음). Azure Portal를 사용 하 여 루트 인증서를 추가할 수도 있습니다.

**추가 하려면:**

Azure에 최대 20개의 루트 인증서 .cer 파일을 추가할 수 있습니다. 다음 단계는 루트 인증서를 추가 하는 데 도움이 됩니다. 

1. 업로드할 .cer 파일을 준비합니다.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. 파일을 업로드합니다. 한 번에 하나의 파일만 업로드할 수 있습니다.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. 인증서 파일이 업로드되었는지 확인하려면

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**제거 하려면:**

1. 변수를 선언합니다. 제거 하려는 인증서와 일치 하도록 예제의 변수를 수정 합니다.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. 인증서를 제거합니다.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. 다음 예제를 사용하여 인증서가 성공적으로 제거되었는지 확인합니다.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>클라이언트 인증서를 해지 또는 복원 하려면

클라이언트 인증서를 해지할 수 있습니다. 인증서 해지 목록을 사용 하면 개별 클라이언트 인증서를 기준으로 지점 및 사이트 간 연결을 선택적으로 거부할 수 있습니다. 이것은 신뢰할 수 있는 루트 인증서를 제거하는 것과 다릅니다. Azure에서 신뢰할 수 있는 루트 인증서 .cer를 제거하면, 해지된 루트 인증서로 생성/서명된 모든 클라이언트 인증서에 대한 액세스 권한도 해지됩니다. 루트 인증서가 아닌 클라이언트 인증서를 해지하면 루트 인증서에서 생성된 다른 인증서를 인증에 계속 사용할 수 있습니다.

해지된 클라이언트 인증서를 사용하는 동안 개별 사용자의 세분화된 액세스 제어를 위해 일반적으로 루트 인증서를 사용하여 팀 또는 조직 수준에서 액세스를 관리합니다.

**해지하려면:**

1. 클라이언트 인증서 지문을 검색합니다. 자세한 내용은 [인증서의 지문을 검색](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)하는 방법을 참조 하세요.

1. 텍스트 편집기에 정보를 복사하고 연속 문자열이 되도록 공백을 모두 제거합니다. 이 문자열은 다음 단계에서 변수로 선언됩니다.

1. 변수를 선언합니다. 이전 단계에서 검색된 지문을 선언해야 합니다.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. 해지된 인증서 목록에 지문을 추가합니다. 지문이 추가되면 "성공"이라고 표시됩니다.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. 지문이 인증서 해지 목록에 추가되었는지 확인합니다.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. 지문이 추가된 후에는 인증서를 더 이상 연결에 사용할 수 없습니다. 이 인증서를 사용하여 연결하려는 클라이언트에서 인증서가 더 이상 유효하지 않다고 하는 메시지를 받습니다.

**복원 하려면:**

해지된 클라이언트 인증서 목록에서 지문을 제거하여 클라이언트 인증서를 복구할 수 있습니다.

1. 변수를 선언합니다. 복구하려는 인증서에 대한 올바른 지문을 선언해야 합니다.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. 인증서 지문을 인증서 해지 목록에서 제거합니다.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. 해지된 목록에서 지문이 제거되었는지 확인합니다.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>지점 및 사이트 간 FAQ

추가 지점 및 사이트 간 정보는 [VPN Gateway 지점 및 사이트 간 FAQ](vpn-gateway-vpn-faq.md#P2S) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](../index.yml)를 참조하세요. 네트워킹 및 가상 머신에 대한 자세한 내용은 [Azure 및 Linux VM 네트워크 개요](../virtual-machines/network-overview.md)를 참조하세요.

P2S 문제 해결 정보는 [Azure 지점 및 사이트 간 연결 문제 해결](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)을 참조하세요.