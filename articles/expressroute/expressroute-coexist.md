<properties
   pageTitle="공존하도록 Express 경로 및 사이트 간 VPN 구성"
   description="이 자습서에서는 Express 경로와 사이트 간 VPN 연결을 공존하도록 구성하는 과정을 안내합니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/20/2015"
   ms.author="cherylmc"/>

# 공존하는 Express 경로 및 사이트 간 VPN 구성

이제 Express 경로와 사이트 간 VPN을 동일한 가상 네트워크에 연결할 수 있습니다. 두 가지 시나리오와 두 가지 구성 절차 중에서 선택할 수 있습니다.

### 시나리오

두 가지 시나리오가 있으며, 아래 그림에 두 시나리오가 나와 있습니다.

- **시나리오 1** - 하나의 로컬 네트워크가 있는 경우 Express 경로는 활성 링크이고 사이트 간 VPN은 백업으로 사용됩니다. Express 경로 연결에 실패한 경우 사이트 간 VPN 연결이 활성화됩니다. 이 시나리오는 고가용성에 가장 적합합니다.

- **시나리오 2** - 두 개의 로컬 네트워크가 있는 경우 각각 Express 경로와 사이트 간 VPN을 통해 Azure에 연결할 수 있습니다. 이 경우 두 연결 모두 동시에 활성화됩니다.


![공존](media/expressroute-coexist/coexist1.jpg)


### 구성 절차

두 가지 구성 절차 중에서 선택할 수 있습니다. 연결할 기존 가상 네트워크가 있는지, 아니면 새 가상 네트워크를 만들 것인지에 따라 구성 절차를 선택합니다.


- [공존하는 연결을 사용하여 새 VNet 만들기](#create-a-new-vnet-with-coexisting-connections): 가상 네트워크가 아직 없는 경우 이 절차에서는 새 가상 네트워크를 만들고 새 Express 경로 및 사이트 간 VPN 연결을 만드는 과정을 안내합니다.  


- [공존하는 연결에 맞게 기존 VNet 구성](#configure-your-existing-vnet-for-coexisting-connections): 기존 사이트 간 VPN 또는 Express 경로에 연결된 가상 네트워크가 이미 있을 수 있습니다. 공존하는 연결을 만들려면 게이트웨이를 삭제한 다음 공존할 수 있는 새 게이트웨이를 구성해야 합니다. 이 경우 게이트웨이 및 연결을 삭제하고 다시 만드는 동안 크로스-프레미스 연결을 위한 가동 중지 시간이 발생하지만 VM 또는 서비스를 새 가상 네트워크로 마이그레이션할 필요는 없습니다. VM 및 서비스는 게이트웨이를 구성하는 동안 부하 분산 장치를 통해 계속 통신할 수 있습니다(이렇게 구성된 경우).

	이 절차에서는 게이트웨이를 삭제한 다음 새 Express 경로 및 사이트 간 VPN 연결을 만드는 과정을 안내합니다. 새 연결을 만들 때 지정된 순서대로 단계를 완료해야 합니다. 게이트웨이 및 연결을 만들 때 다른 문서의 지침을 사용하지 마세요.

### 참고 사항 및 제한 사항

- 사이트 간 VPN을 통해 연결된 로컬 네트워크와 Express 경로를 통해 연결된 로컬 네트워크 간에는 Azure를 통해 라우팅할 수 없습니다.
- 지점 및 사이트 간 VPN을 Express 경로에 연결된 동일한 VNet에 연결할 수 없습니다. 동일한 VNet에 대해 지점 및 사이트간 VPN과 Express 경로를 함께 사용할 수 없습니다.
- Express 경로 게이트웨이와 사이트 간 VPN 게이트웨이 모두 Standard 또는 HighPerformance 게이트웨이 SKU여야 합니다. 게이트웨이 SKU에 대한 자세한 내용은 [게이트웨이 SKU](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.
- 로컬 네트워크가 Express 경로 및 사이트 간 VPN 둘 다에 연결된 경우(시나리오 1) 로컬 네트워크에서 사이트 간 VPN 연결을 공용 인터넷으로 라우팅하는 정적 경로를 구성해야 합니다. 
- 사이트 간 VPN 게이트웨이를 추가하기 전에 Express 경로 게이트웨이를 먼저 만들어야 합니다.
- 두 절차 모두 Express 경로 회로를 이미 구성한 것으로 가정합니다. 그렇지 않은 경우 다음 문서를 참조하세요. 

	- [NSP(네트워크 서비스 공급자)를 통해 Express 경로 연결 구성](expressroute-configuring-nsps.md) 
	- [EXP(Exchange 공급자)를 통해 Express 경로 연결 구성](expressroute-configuring-exps.md)


## 공존하는 연결을 사용하여 새 VNet 만들기

1. PowerShell cmdlet의 최신 버전이 있는지 확인합니다. [다운로드 페이지](http://azure.microsoft.com/downloads/)의 PowerShell 섹션에서 최신 PowerShell cmdlet을 다운로드하여 설치할 수 있습니다.
2. 가상 네트워크의 스키마를 만듭니다. 네트워크 구성 파일 작업에 대한 자세한 내용은 [네트워크 구성 파일을 사용하여 가상 네트워크 구성](../virtual-network/virtual-networks-using-network-configuration-file.md)을 참조하세요. 구성 스키마에 대한 자세한 내용은 [Azure 가상 네트워크 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 참조하세요.

	스키마를 만들 때 다음 값을 사용해야 합니다.

	- 가상 네트워크의 게이트웨이 서브넷은 /27(또는 더 짧은 접두사)이어야 합니다.
	- 게이트웨이 연결 유형은 "전용"입니다.

		      <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
		        <AddressSpace>
		          <AddressPrefix>10.17.159.192/26</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Subnet-1">
		            <AddressPrefix>10.17.159.192/27</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.17.159.224/27</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>



3. xml 스키마 파일을 만들고 구성한 후 파일을 업로드합니다. 그러면 가상 네트워크가 만들어집니다.

	다음 cmdlet(사용자 고유의 값으로 대체)을 사용하여 파일을 업로드합니다.

	`Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'`
4. Express 경로 게이트웨이를 만듭니다. GatewaySKU를 *Standard* 또는 *HighPerformance*로 지정하고 GatewayType을 *DynamicRouting*으로 지정해야 합니다. 

	아래 샘플(사용자 고유의 값으로 대체)을 사용합니다.

	`New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance`

5. Express 경로 게이트웨이를 Express 경로 회로에 연결합니다. 이 단계를 완료하면 Express 경로를 통해 온-프레미스 네트워크와 Azure 간의 연결이 설정됩니다.

	`New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET`

6. 다음으로, 사이트 간 VPN 게이트웨이를 만듭니다. GatewaySKU는 *Standard* 또는 *HighPerformance*이고, GatewayType은 *DynamicRouting*이어야 합니다.

	`New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance`

	게이트웨이 ID와 공용 IP를 비롯한 가상 네트워크 게이트웨이 설정을 검색하려면 `Get-AzureVirtualNetworkGateway` cmdlet을 사용합니다.

		Get-AzureVirtualNetworkGateway
		
		GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
		GatewayName          : S2SVPN
		LastEventData        :
		GatewayType          : DynamicRouting
		LastEventTimeStamp   : 5/29/2015 4:41:41 PM
		LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
		LastEventID          : 23002
		State                : Provisioned
		VIPAddress           : 104.43.x.y
		DefaultSite          :
		GatewaySKU           : HighPerformance
		Location             :
		VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
		SubnetId             :
		EnableBgp            : False
		OperationDescription : Get-AzureVirtualNetworkGateway
		OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
		OperationStatus      : Succeeded

7. 로컬 사이트 VPN 게이트웨이 엔터티를 만듭니다. 이 명령은 온-프레미스 VPN 게이트웨이를 구성하지 않습니다. 대신, Azure VPN 게이트웨이를 연결할 수 있도록 공용 IP 주소 및 온-프레미스 주소 공간과 같은 로컬 게이트웨이 설정을 제공할 수 있게 해줍니다.

	**중요:** 사이트 간 VPN의 로컬 사이트는 netcfg에 정의되지 않습니다. 대신, 다음 cmdlet을 사용하여 로컬 사이트 매개 변수를 지정해야 합니다. 관리 포털 또는 netcfg 파일을 사용하여 정의할 수 없습니다.

	다음 샘플(사용자 고유의 값으로 대체)을 사용합니다.

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <local-network- gateway-public-IP> -AddressSpace <local-network-address-space>`

	게이트웨이 ID와 공용 IP를 비롯한 가상 네트워크 게이트웨이 설정을 검색하려면 `Get-AzureVirtualNetworkGateway` cmdlet을 사용합니다. 아래 예제를 참조하세요.

		Get-AzureLocalNetworkGateway
		
		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded

	
8. 새 게이트웨이에 연결할 로컬 VPN 장치를 구성합니다. VPN 장치를 구성할 때 6단계에서 검색한 정보를 사용합니다. VPN 장치 구성에 대한 자세한 내용은 [VPN 장치 구성](http://go.microsoft.com/fwlink/p/?linkid=615099)을 참조하세요.
	

9. Azure의 사이트 간 VPN 게이트웨이를 로컬 게이트웨이에 연결합니다.

	이 예제에서 connectedEntityId는 `Get-AzureLocalNetworkGateway`를 실행하여 찾을 수 있는 로컬 게이트웨이 ID입니다. virtualNetworkGatewayId는 `Get-AzureVirtualNetworkGateway` cmdlet을 사용하여 찾을 수 있습니다. 이 단계를 수행하면 사이트 간 VPN 연결을 통해 로컬 네트워크와 Azure 간의 연결이 설정됩니다.


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`


## 공존하는 연결에 맞게 기존 VNet 구성

Express 경로 또는 사이트 간 VPN 연결을 통해 연결된 기존 가상 네트워크가 있는 경우 두 연결 모두를 기존 게이트웨이에 연결하려면 먼저 기존 게이트웨이를 삭제해야 합니다. 이 구성에서 작업하는 동안에는 로컬 프레미스에서 게이트웨이를 통한 가상 네트워크 연결이 손실됩니다.

**구성을 시작하기 전에:** 게이트웨이 서브넷 크기를 늘릴 수 있도록 가상 네트워크에 충분한 IP 주소가 남아 있는지 확인합니다.


1. PowerShell cmdlet의 최신 버전이 있는지 확인합니다. [다운로드 페이지](http://azure.microsoft.com/downloads/)의 PowerShell 섹션에서 최신 PowerShell cmdlet을 다운로드하여 설치할 수 있습니다.
 
2. 기존 사이트 간 VPN 게이트웨이를 삭제합니다. 다음 cmdlet(사용자 고유의 값으로 대체)을 사용합니다.

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. 가상 네트워크 스키마를 내보냅니다. 다음 PowerShell cmdlet(사용자 고유의 값으로 대체)을 사용합니다.

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`
3. 게이트웨이 서브넷이 /27(또는 더 짧은 접두사)이 되도록 네트워크 구성 파일 스키마를 편집합니다. 아래 예제를 참조하세요. 네트워크 구성 파일 작업에 대한 자세한 내용은 [네트워크 구성 파일을 사용하여 가상 네트워크 구성](../virtual-network/virtual-networks-using-network-configuration-file.md)을 참조하세요. 구성 스키마에 대한 자세한 내용은 [Azure 가상 네트워크 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 참조하세요.


          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
4. 이전 게이트웨이가 사이트 간 VPN인 경우 연결 형식을 **전용**으로 변경해야 합니다.

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
5. 이제 게이트웨이가 없는 VNet이 설정됩니다. 이 문서의 **3단계**, [공존하는 연결을 사용하여 새 VNet 만들기](#create-a-new-vnet-with-coexisting-connections)를 계속 진행하여 새 게이트웨이를 만들고 연결을 완료할 수 있습니다.



## 다음 단계
	
Express 경로에 대해 자세히 알아봅니다. [Express 경로 개요](expressroute-introduction.md)를 참조하세요.

VPN 게이트웨이에 대해 자세히 알아봅니다. [VPN 게이트웨이 정보](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.

<!---HONumber=July15_HO4-->