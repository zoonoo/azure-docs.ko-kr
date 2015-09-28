## PowerShell에서 네트워크 구성 파일을 사용하여 VNet을 만드는 방법

Azure에서는 xml 파일을 사용하여 구독에 사용할 수 있는 모든 VNet을 정의합니다. 이 파일을 다운로드하고, 편집하여 기존 VNet을 수정 또는 삭제하고 새 VNet을 만들 수 있습니다. 이 문서에서는 네트워크 구성(또는 netcgf) 파일이라고 하는 이 파일을 다운로드하고 편집하여 새 VNet을 만드는 방법을 알아봅니다. 네트워크 구성 파일에 대한 자세한 내용은 [Azure 가상 네트워크 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 참조하세요.

PowerShell과 netcfg 파일을 사용하여 VNet을 만들려면 다음 단계를 수행합니다.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.
2. Azure PowerShell 콘솔에서 **Get-AzureVnetConfig** cmdlet을 사용하고 명령을 실행하여 네트워크 구성 파일을 다운로드합니다. 

		Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

	예상 출력:

		XMLConfiguration                                                                                                     
		----------------                                                                                                     
		<?xml version="1.0" encoding="utf-8"?>...  

3. XML 또는 텍스트 편집기 응용 프로그램을 사용하여 위의 2단계에서 저장한 파일을 열고 **<VirtualNetworkSites>** 요소를 찾아봅니다. 이미 만들어진 네트워크가 있으면 각 네트워크는 자체의 **<VirtualNetworkSite>** 요소로 표시됩니다.
4. 이 시나리오에서 설명하는 가상 네트워크를 만들려면 **<VirtualNetworkSites>** 요소 바로 아래에 다음 XML을 추가합니다.

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

9.  네트워크 구성 파일을 저장합니다.
10. Azure PowerShell 콘솔에서 **Set-AzureVnetConfig** cmdlet을 사용하고 아래 명령을 실행하여 네트워크 구성 파일을 업로드합니다. 명령 아래의 출력을 확인하면 **OperationStatus** 아래에 **Succeeded**가 표시됩니다. 그렇지 않으면 xml 파일에 오류가 있는지 확인합니다.

		Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

	다음은 위의 명령에 대해 예상된 출력입니다.

		OperationDescription OperationId                          OperationStatus
		-------------------- -----------                          ---------------
		Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
	
11. Azure PowerShell 콘솔에서 **Get-AzureVnetSite** cmdlet을 사용하고 아래 명령을 실행하여 새 네트워크가 추가되었는지 확인합니다.

		Get-AzureVNetSite -VNetName TestVNet

	다음은 위의 명령에 대해 예상된 출력입니다.

		AddressSpacePrefixes : {192.168.0.0/16}
		Location             : Central US
		AffinityGroup        : 
		DnsServers           : {}
		GatewayProfile       : 
		GatewaySites         : 
		Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
		InUse                : False
		Label                : 
		Name                 : TestVNet
		State                : Created
		Subnets              : {FrontEnd, BackEnd}
		OperationDescription : Get-AzureVNetSite
		OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
		OperationStatus      : Succeeded

<!---HONumber=Sept15_HO3-->