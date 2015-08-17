<properties 
   pageTitle="가상 네트워크에 여러 온-프레미스 사이트 연결"
   description="이 문서에서는 여러 로컬 온-프레미스 사이트를 가상 네트워크에 연결하는 과정을 설명합니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/04/2015"
   ms.author="cherylmc" />

# 가상 네트워크에 여러 온-프레미스 사이트 연결

단일 가상 네트워크에 여러 온-프레미스 사이트를 연결할 수 있습니다. 이 방식은 하이브리드 클라우드 솔루션을 구축하는 경우 특히 유용합니다. Azure 가상 네트워크 게이트웨이에 대한 다중 사이트 연결을 만드는 작업은 다른 사이트 간 연결을 만드는 작업과 매우 비슷합니다. 사실, 경로 기반(또는 동적 라우팅) VPN 게이트웨이가 가상 네트워크에 대해 구성된 경우 기존 Azure VPN 게이트웨이를 사용할 수 있습니다.

게이트웨이가 정책 기반(또는 정적 라우팅)인 경우, 온-프레미스 VPN 게이트웨이가 경로 기반 VPN 구성을 지원하는지 확인해야 할 수도 있지만, 다중 사이트를 수용하기 위해 가상 네트워크를 다시 구축하지 않고도 언제든 게이트웨이 유형을 변경할 수 있습니다. 그런 다음, 네트워크 구성 파일에 구성 설정을 추가하고 가상 네트워크에서 추가 사이트로 여러 VPN 연결을 만듭니다.

![다중 사이트 VPN](./media/vpn-gateway-multi-site/IC727363.png)

## 고려할 항목

**관리 포털을 사용하여 이 가상 네트워크를 변경할 수 없습니다.** 이 릴리스에서는 관리 포털을 사용하는 대신 네트워크 구성 파일을 변경해야 합니다. 관리 포털에서 변경하면 이 가상 네트워크의 다중 사이트 참조 설정을 덮어쓰게 됩니다. 따라서 다중 사이트 절차를 완료할 때까지는 네트워크 구성 파일을 사용하는 데 익숙해져야 합니다. 그러나 네트워크 구성을 여러 사용자가 사용하는 경우에는 이 제한에 대해 모든 사람이 알 수 있도록 해야 합니다. 이는 관리 포털을 전혀 사용할 수 없다는 의미가 아닙니다. 이 특정 가상 네트워크에 대한 구성 변경을 제외한 모든 작업에 관리 포털을 사용할 수 있습니다.

## 시작하기 전에

구성을 시작하기 전에 다음이 있는지 확인합니다.

- Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

- 각 온-프레미스 위치에서 호환되는 VPN 하드웨어. 사용하려는 장치가 호환 가능한 것인지 확인하려면 [가상 네트워크 연결을 위한 VPN 장치 정보](http://go.microsoft.com/fwlink/p/?linkid=615099)를 참조하세요.

- 각 VPN 장치에 대한 외부 연결 공용 IPv4 IP 주소. IP 주소는 NAT 뒤에 배치할 수 없습니다. 이는 필수 요구 사항입니다.

-   최신 버전의 Azure PowerShell cmdlet. [다운로드 페이지](http://azure.microsoft.com/downloads/)의 Windows PowerShell 섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.

- VPN 하드웨어 구성에 능숙한 사용자. 관리 포털에서 자동 생성된 VPN 스크립트를 사용하여 VPN 장치를 구성할 수 없습니다. 따라서 사용자가 VPN 장치 구성 방법에 대해 매우 잘 알고 있거나 이와 관련된 지식이 있는 사람과 작업해야 합니다.

- 가상 네트워크에 사용할 IP 주소 범위(아직 만들지 않은 경우).

- 연결할 각 로컬 네트워크 사이트에 대한 IP 주소 범위. 연결하려는 각 로컬 네트워크 사이트의 IP 주소 범위가 겹치지 않도록 해야 합니다. 범위가 겹치면 업로드 중인 구성을 관리 포털이나 REST API에서 거부합니다. 예를 들어 두 로컬 네트워크 사이트에 IP 주소 범위 10.2.3.0/24가 모두 포함되어 있고 대상 주소가 10.2.3.3인 패키지가 있는 경우, 주소 범위가 겹치기 때문에 Azure는 어떤 사이트에 패키지를 전송할지 알지 못하게 됩니다. 라우팅 문제를 방지하기 위해 Azure에서는 범위가 겹치는 구성 파일의 업로드를 허용하지 않습니다.

## 가상 네트워크 및 게이트웨이 만들기

1. **동적 라우팅 게이트웨이를 사용하여 사이트 간 VPN을 만듭니다.** 이미 있는 경우는 그대로 진행하면 됩니다. [가상 네트워크 구성 설정 내보내기](#export)로 진행할 수 있습니다. 아직 만들지 않은 경우는 아래 작업을 수행합니다.

	**사이트 간 가상 네트워크가 이미 있지만 정적 라우팅 게이트웨이가 있는 경우:** **1.** 게이트웨이 유형을 동적 라우팅으로 변경합니다. 다중 사이트 VPN에는 동적 라우팅 게이트웨이가 필요합니다. 게이트웨이 유형을 변경하려면 먼저 기존 게이트웨이를 삭제한 다음 새로 만들어야 합니다. 자세한 지침은 [VPN 게이트웨이 라우팅 유형 변경](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type)을 참조하세요. **2.** 새 게이트웨이를 구성하고 VPN 터널을 만듭니다. 자세한 지침은 [관리 포털에서 VPN 게이트웨이 구성](vpn-gateway-configure-vpn-gateway-mp.md)을 참조하세요.
	
	**사이트 간 가상 네트워크 사이트가 없는 경우:** **1.** [관리 포털에서 사이트 간 VPN 연결로 가상 네트워크 만들기](vpn-gateway-site-to-site-create.md)의 지침에 따라 사이트 간 가상 네트워크를 만듭니다. **2.** [관리 포털에서 VPN 게이트웨이 구성](vpn-gateway-configure-vpn-gateway-mp.md)의 지침에 따라 동적 라우팅 게이트웨이를 구성합니다. 사용 중인 게이트웨이 유형에 맞는 **동적 라우팅**을 선택해야 합니다.



1. **<a name="export"></a>가상 네트워크 구성 설정을 내보냅니다.** 네트워크 구성 파일을 내보내려면 [네트워크 설정을 내보내려면](../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal)을 참조하세요. 내보내는 파일은 새로 다중 사이트 설정을 구성하는 데 사용됩니다.

1. **네트워크 구성 파일을 엽니다.** 마지막 단계에서 다운로드한 네트워크 구성 파일을 엽니다. 원하는 xml 편집기를 사용합니다. 파일은 다음과 유사하게 나타납니다.

		<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <LocalNetworkSites>
		      <LocalNetworkSite name="Site1">
		        <AddressSpace>
		          <AddressPrefix>10.0.0.0/16</AddressPrefix>
		          <AddressPrefix>10.1.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
		      </LocalNetworkSite>
		      <LocalNetworkSite name="Site2">
		        <AddressSpace>
		          <AddressPrefix>10.2.0.0/16</AddressPrefix>
		          <AddressPrefix>10.3.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
		      </LocalNetworkSite>
		    </LocalNetworkSites>
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
		        <AddressSpace>
		          <AddressPrefix>10.20.0.0/16</AddressPrefix>
		          <AddressPrefix>10.21.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="FE">
		            <AddressPrefix>10.20.0.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="BE">
		            <AddressPrefix>10.20.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.20.2.0/29</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="Site1">
		              <Connection type="IPsec" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

1. 네트워크 구성 파일에 여러 사이트 참조를 추가합니다. 사이트 참조 정보를 추가 또는 제거하면 ConnectionsToLocalNetwork/LocalNetworkSiteRef의 구성이 변경됩니다. 새 로컬 사이트 참조를 추가하면 Azure에서 새 터널을 만듭니다. 아래 예제에서 네트워크 구성은 단일 사이트 연결용입니다.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	사이트 참조를 더 추가하려면(다중 사이트 구성 만들기) 아래 예제에 나온 것처럼 "LocalNetworkSiteRef" 줄을 더 추가하면 됩니다.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

1. **네트워크 구성 파일을 저장하고 가져옵니다.** 네트워크 구성 파일을 가져오려면 [네트워크 설정을 가져오려면](../virtual-network/../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal)을 참조하세요. 변경 내용과 함께 이 파일을 가져오면 새 터널이 추가됩니다. 터널은 이전에 만든 동적 게이트웨이를 사용합니다.



1. **VPN 터널에 대한 사전 공유 키를 다운로드합니다.** 새 터널을 추가한 후 PowerShell cmdlet Get-AzureVNetGatewayKey를 사용하여 각 터널의 IPsec/IKE 사전 공유 키를 가져옵니다.

	예:

		PS C:\> Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

		PS C:\> Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

	원하는 경우 *가상 네트워크 게이트웨이 공유 키 가져오기* REST API를 사용하여 사전 공유 키를 가져올 수도 있습니다.

## 연결 확인

**다중 사이트 터널 상태를 확인합니다.** 각 터널에 대한 키를 다운로드한 후 연결을 확인할 수 있습니다. 아래 예제에 나온 것처럼 *Get-AzureVnetConnection*을 사용하여 가상 네트워크 터널 목록을 가져옵니다. VNet1이 VNet의 이름입니다.

		PS C:\Users\yushwang\Azure> Get-AzureVnetConnection -VNetName VNET1
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 661530
		IngressBytesTransferred   : 519207
		LastConnectionEstablished : 5/2/2014 2:51:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site1
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 789398
		IngressBytesTransferred   : 143908
		LastConnectionEstablished : 5/2/2014 3:20:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site2
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded

## 다음 단계

VPN 게이트웨이에 대한 자세한 내용은 [VPN 게이트웨이 정보](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.

<!---HONumber=August15_HO6-->