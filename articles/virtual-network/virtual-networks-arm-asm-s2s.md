<properties 
   pageTitle="Azure에서 클래식 VNet을 ARM VNet에 연결하는 방법 - 솔루션 가이드"
   description="클래식 VNet과 새 VNet 간의 VPN 연결을 만드는 방법에 대해 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2015"
   ms.author="telmos" />

# 새 VNet에 클래식 VNet 연결

Azure에는 현재 Azure 서비스 관리자(클래식이라고 함)와 Azure 리소스 관리자(ARM)라는 두 가지 관리 모드가 있습니다. 일정 기간 동안 Azure를 사용한 경우 Azure VM 및 인스턴스 역할이 클래식 VNet에서 실행되고 있을 것입니다. 또한 이후의 VM 및 역할 인스턴스는 ARM에서 만들 VNet에서 실행되고 있을 것입니다.

이러한 경우 새 인프라에서 클래식 리소스와 통신할 수 있는지 확인할 수 있습니다. 두 VNet 간의 VPN 연결을 만들면 됩니다. 아래 그림에서는 두 VNet(클래식 및 ARM) 간에 보안 터널 연결이 구성된 샘플 환경을 보여 줍니다.

![](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure01.png)

>[AZURE.NOTE]이 문서에서는 테스트 목적으로 종단 간 솔루션을 안내합니다. VNet을 이미 설정하고 Azure의 사이트 간 연결 및 VPN 게이트웨이에 익숙한 경우 [ARM VNet과 클래식 VNet 간의 S2S VPN 구성](../virtual-networks-arm-asm-s2s-howto.md)을 참조하세요.

이 시나리오를 테스트하려면 다음이 필요합니다.

1. [클래식 VNet 환경 만들기](#Create-a-classic-VNet-environment)
2. [새 VNet 환경 만들기](#Create-a-new-VNet-environment)
3. [두 VNet 연결](#Connect-the-two-VNets)

먼저 클래식 포털, 네트워크 구성 파일, Azure 서비스 관리자 PowerShell cmdlet 등 클래식 Azure 관리 도구를 사용하여 위 단계를 실행한 후 새 Azure 포털, ARM 템플릿, ARM PowerShell cmdlet 등 새 관리 도구로 이동합니다.

>[AZURE.IMPORTANT]VNet을 서로 연결하려면 CIDR 블록이 충돌해서는 안 됩니다. 각 VNet에 고유한 CIDR 블록이 있는지 확인하세요.

## 클래식 VNet 환경 만들기

기존 클래식 VNet을 사용하여 새 ARM VNet에 연결할 수 있습니다. 이 예제에서는 테스트용으로 서브넷 두 개, 게이트웨이 하나 및 VM 하나로 구성된 새 클래식 VNet를 만드는 방법을 보여 줍니다.

### 1단계: 클래식 VNet 만들기

위 그림 1에 매핑되는 새 VNet을 만들려면 아래 지침을 따르세요.

1. PowerShell 콘솔에서 아래 명령을 실행하여 Azure 계정을 추가합니다.

		Add-AzureAccount

2. 로그인 대화 상자 지침에 따라 Azure 계정으로 로그온합니다.

3. 아래 명령을 실행하여 Azure 서비스 관리 PowerShell cmdlet를 사용하고 있는지 확인합니다.

		Switch-AzureMode AzureServiceManagement

4. 아래 명령을 실행하여 Azure 네트워크 구성 파일을 다운로드합니다.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

		XMLConfiguration                                OperationDescription                            OperationId                                     OperationStatus                                
		----------------                                --------------------                            -----------                                     ---------------                                
		<?xml version="1.0" encoding="utf-8"?>...       Get-AzureVNetConfig                             04ab3224-7e1c-cc1a-8b75-96c6c300ddb8            Succeeded       

5. 방금 다운로드한 파일을 열고 *10.2.0.0/16*을 사용하는 *vnet02*라는 로컬 네트워크 사이트를 주소 접두사로 추가하고, 유효한 공용 IP 주소를 VPN 게이트웨이 주소로 추가합니다. 이렇게 하려면 **LocalNetworkSite** 요소를 구성 파일의 **LocalNetworkSites** 요소에 추가합니다. 아래 파일 조각은 샘플 **LocalNetworksSites**요소를 보여 줍니다.

	    <LocalNetworkSites>
	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>2.0.0.2</VPNGatewayAddress>
	      </LocalNetworkSite>
	    </LocalNetworkSites>		

6. **VirtualNetworkSites** 요소에서 위 시나리오 그림에 따라 주소 접두사가 *10.1.0.0/16*인 새 가상 네트워크와 두 개의 서브넷을 추가합니다. 아래 파일 조각은 샘플 **VirtualNetworkSites** 요소를 보여 줍니다.
	
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="vnet01" Location="East US">
	        <AddressSpace>
	          <AddressPrefix>10.1.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="Subnet1">
	            <AddressPrefix>10.1.0.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="GatewaySubnet">
	            <AddressPrefix>10.1.200.0/29</AddressPrefix>
	          </Subnet>
	        </Subnets>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>

7. 파일을 저장한 다음 아래 명령을 실행하여 Azure에 업로드합니다. 사용자 환경에 필요한 대로 파일 경로를 변경해야 합니다.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

		OperationDescription                                            OperationId                                                     OperationStatus                                                
		--------------------                                            -----------                                                     ---------------                                                
		Set-AzureVNetConfig                                             e0ee6e66-9167-cfa7-a746-7cab93c22013                            Succeeded 

### 2단계: 클래식 VNet에 VM 만들기

Azure 서비스 관리자 PowerShell cmdlet를 사용하여 클래식 VNet에 VM을 만들려면 아래 지침을 따르세요.

1. Azure에서 VM 이미지를 검색합니다. 아래 PowerShell 명령은 최신 Windows Server 2012 R2 이미지를 검색합니다.

		$WinImage = (Get-AzureVMImage `
		    | ?{$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"} `
		    | sort PublishedDate -Descending)[0]		

2. 아래 명령을 실행하여 VM에 대한 VHD(가상 하드 드라이브)를 저장할 저장소 계정을 만듭니다.

		$storage1 = New-AzureStorageAccount `
			-StorageAccountName v1v2teststorage1 `
		    -Location "East US"	

3.  아래 명령을 실행하여 VM을 만듭니다. 사용자 이름 및 암호 값을 바꿔야 합니다.

		$vm1 = New-AzureVMConfig -Name "VM01" -InstanceSize "ExtraLarge" `
		    -Image $WinImage.ImageName –AvailabilitySetName "MyAVSet1" `
		    -MediaLocation "https://v1v2teststorage1.blob.core.windows.net/vhd/vm01.vhd"
		Add-AzureProvisioningConfig –VM $vm1 -Windows `
		    -AdminUserName "user" -Password "P@ssw0rd" 

4.  아래 명령을 실행하여 VM을 *Subnet1*에 연결합니다.

		Set-AzureSubnet -SubnetNames "Subnet1" -VM $vm1
		Set-AzureStaticVNetIP  -IPAddress "10.1.0.101" -VM $vm1

5. 아래 명령을 실행하여 VM을 호스트할 새 클라우드 서비스를 만듭니다.

		New-AzureService -ServiceName "v1v2svc01" -Location "East US"
 		New-AzureVM -ServiceName "v1v2svc01" –VNetName "vnet01" –VMs $vm1

### 3단계: 클래식 VNet에 대한 VPN 게이트웨이 만들기 

클래식 Azure 포털을 사용하여 vnet01에 대한 VPN 게이트웨이를 만들려면 아래 지침을 따르세요.

1. https://manage.windowsazure.com에서 클래식 Azure 포털을 엽니다. 필요한 경우 자격 증명을 지정합니다.
2. **모든 항목** 목록에서 아래로 스크롤하여 **네트워크**를 클릭합니다.
3. 가상 네트워크 목록에서 **vnet01**을 클릭한 다음 **구성**을 클릭합니다.
4. **사이트 간 연결**에서 **로컬 네트워크에 연결** 확인란을 선택합니다.
5. **로컬 네트워크** 목록에서 **vnet02**를 선택하고 **저장**을 클릭한 다음 **예**를 클릭합니다.
6. **대시보드**를 클릭하고 아래 그림 2와 같이 게이트웨이를 아직 만들지 않았음을 알리는 메시지를 확인합니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure02.png)

7. 아래 그림 3과 같이 **게이트웨이 만들기**를 클릭하여 vnet01에 대한 VPN 게이트웨이 만듭니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure03.png)

8. 게이트웨이 형식 목록에서 **동적**을 클릭한 다음 **예**를 클릭합니다. 게이트웨이를 만드는 동안 해당 게이트웨이가 노란색으로 표시되도록 대시보드 이미지가 변경됩니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure04.png)

	>[AZURE.NOTE]이 작업에는 몇 분 정도 걸릴 수 있습니다.

9. 생성되면 아래와 같이 게이트웨이의 공용 IP 주소를 적어 둡니다. 나중에 ARM VNet에 대한 로컬 네트워크를 만드는 데 이 주소가 필요합니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure05.png)

## 새 VNet 환경 만들기

클래식 VNet이 VM 및 게이트웨이를 사용하여 실행되므로 이제 ARM VNet을 만들 차례입니다.

### 1단계: ARM에서 새 VNet 만들기

ARM 템플릿을 사용하여 클래식 VNet에 대한 로컬 네트워크 및 두 개의 서브넷과 함께 ARM VNet을 만들려면 아래 지침을 따르세요.

1. [git hub](https://github.com/Azure/azure-quickstart-templates/tree/master/arm-asm-s2s)에서 azuredeploy.json 및 azuredeploy parameters.json 파일을 다운로드합니다.
2. Visual Studio에서 azuredeploy.json 파일을 열고 템플릿에서 다음 4개의 리소스를 만들었는지 확인합니다. 

	- **로컬 게이트웨이**: 이 리소스는 연결하려는 VNet에 대해 생성된 게이트웨이를 나타냅니다. 이 시나리오에서는 vnet01에 대한 게이트웨이입니다.
	- **가상 네트워크**: 이 리소스는 만들려는 ARM VNet을 나타냅니다. 이 시나리오에서는 vnet02입니다.
	- **게이트웨이 공용 IP**: 이 리소스는 ARM VNet에 대해 만들려는 게이트웨이의 공용 IP 주소를 나타냅니다. 
	- **게이트웨이**: 이 리소스는 ARM VNet(vnet02)에 대해 만들려는 게이트웨이를 나타냅니다.

3. 이 파일에 사용된 매개 변수를 확인합니다. 대부분 기본값으로 설정됩니다. 필요에 따라 이러한 값을 변경할 수 있습니다.

4. Visual Studio에서 azuredeploy-parameters.json 파일을 엽니다. 이 파일에는 템플릿 파일의 매개 변수에 사용할 값이 포함되어 있습니다. 필요한 경우 다음 값을 편집합니다.

	- **subscriptionId**: 구독 ID를 편집하여 붙여 넣습니다. 구독 ID를 모르는 경우 **Get-AzureSubscription** PowerShell cmdlet를 실행하여 ID를 검색합니다.
	- **location**: VNet을 만들 Azure 위치를 지정합니다. 이 시나리오에서는 **Central US**입니다.
	- **virtualNetworkName**: 만들려는 ARM VNet의 이름입니다. 이 시나리오에서는 **vnet02**입니다.
	- **localGatewayName**: 새 ARM VNet과 연결할 로컬 네트워크입니다. 이 시나리오에서는 **vnet01**입니다.
	- **localGatewayIpAddress**: 연결하려는 네트워크에 대해 만든 게이트웨이의 공용 IP 주소입니다. 이 시나리오에서는 **vnet01**에 대한 VPN 게이트웨이를 만들 때 위 9단계에서 적어 둔 IP 주소입니다.
	- **localGatewayAddressPrefix**: VNet에서 연결할 로컬 네트워크의 CIDR 블록입니다. 이 시나리오에서 연결하려는 VNet은 **vnet01**이고, 해당 CIDR 블록은 **10.1.0.0/16**입니다.
	- **gatewayPublicIPName**: ARM VNet에 대해 만들려는 게이트웨이의 공용 IP에 대한 IP 개체의 이름입니다.
	- **gatewayName**: ARM VNet에 대해 만들려는 게이트웨이 개체의 이름입니다.
	- **addressPrefix**: ARM VNet에 대한 CIDR 블록입니다. 이 시나리오에서는 **10.2.0.0/16**입니다.
	- **subnet1Prefix**: ARM VNet의 서브넷에 대한 CIDR 블록입니다. 이 시나리오에서는 **10.2.0.0/24**입니다.
	- **gatewaySubnetPrefix**: ARM VNet의 서브넷에 대한 게이트웨이의 CIDR 블록입니다. 이 시나리오에서는 **10.2.200.0/29**입니다.
	- **connectionName**: 만들려는 연결 개체의 이름입니다.
	- **sharedKey**: 연결을 위한 IPSec 공유 키입니다. 이 시나리오에서는 **abc123**입니다.

5. ARM VNet 및 해당 관련 개체를 만들려면 **RG1**이라는 새 리소스 그룹에서 다음 PowerShell 명령을 실행합니다. 템플릿 파일 및 매개 변수 파일에 대한 경로를 변경해야 합니다.

		Switch-AzureMode AzureResourceManager
		New-AzureResourceGroup -Name RG1 -Location "Central US" `
		    -TemplateFile C:\Azure\azuredeploy.json `
		    -TemplateParameterFile C:\Azure\azuredeploy-parameters.json		

	>[AZURE.NOTE]이 작업에는 몇 분 정도 걸릴 수 있습니다.

7. 필요한 경우 브라우저에서 https://portal.azure.com/으로 이동하여 자격 증명을 입력합니다.
8. 다음과 같이 Azure 포털에서 **RG1** 리소스 그룹 타일을 클릭합니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure06.png)

9. ARM 템플릿을 사용하여 그룹에 리소스가 추가되었는지 확인합니다.

### 2단계: ARM에서 새 VM 만들기

Azure 포털에서 새 VNet에 VM을 만들려면 아래 지침을 따르세요.

1. 포털에서 **새로 만들기** 단추를 클릭하고 **계산**을 클릭한 다음 **Windows Server 2012 R2 Datacenter**를 클릭합니다.
2. 오른쪽 창 아래쪽의 **계산 스택 선택**에서 아래와 같이 ARM에서 VM을 만들 방법으로 **리소스 관리자 스택 사용**을 선택하고 **만들기**를 클릭합니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure07.png)

3. **기본 사항** 블레이드에서 VM 이름, 사용자 이름, 암호, 구독, 리소스 그룹 및 VM의 위치를 입력한 다음 **확인**을 클릭합니다. 아래 그림에서는 이 시나리오에 대한 설정을 보여 줍니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure08.png)

4. **크기 선택** 블레이드에서 크기를 선택하고 **선택**을 클릭합니다. 이 시나리오에서는 **D2**를 선택합니다.
5. **설정** 블레이드에서 **가상 네트워크**를 클릭한 다음 **vnet02**를 클릭합니다.
6. **서브넷**, **Subnet1**, **확인**을 차례로 클릭합니다. **요약** 블레이드가 아래 그림과 같이 표시됩니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure09.png)

7. **확인**을 클릭한 다음 **만들기**을 클릭하여 VM을 만듭니다. 아래와 같이 프로비전되는 VM을 보여 주는 새 타일이 포털에 표시됩니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure10.png)

	>[AZURE.NOTE]이 작업에는 몇 분 정도 걸릴 수 있습니다. 이 문서의 다음 부분으로 이동할 수 있습니다.

## 두 VNet 연결

VM이 연결된 VNet 두 개가 있으므로 이제 이전에 설정한 게이트웨이를 통해 VNet을 연결하고 연결을 테스트할 차례입니다.

### 1단계: 클래식 VNet에 대한 게이트웨이 구성

ARM VNet(vnet02)에 대해 만든 게이트웨이의 IP 주소를 사용하도록 클래식 VNet을 구성한 다음 각 VNet에서 연결을 설정해야 합니다. 이렇게 하려면 아래의 지침을 따르세요.

1. ARM VNet에서 게이트웨이에 사용되는 IP 주소를 검색하려면 아래 명령을 실행하고 출력을 확인합니다. 나중에 클래식 VNet에 대한 로컬 네트워크 설정을 수정하는 데 필요하므로 주소를 적어 둡니다.

		Get-AzurePublicIpAddress | ?{$_.Name -eq "ArmAsmS2sGatewayIp"}

		Name                     : ArmAsmS2sGatewayIp
		ResourceGroupName        : RG1
		Location                 : centralus
		Id                       : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/publicIPAddresses/ArmAsmS2sGatewayIp
		Etag                     : W/"1ee6c1bd-8be1-488e-a571-77b05b49e33a"
		ProvisioningState        : Succeeded
		Tags                     : 
		PublicIpAllocationMethod : Dynamic
		IpAddress                : 23.99.213.28
		IdleTimeoutInMinutes     : 4
		IpConfiguration          : {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworkGateways/ArmAsmGateway/ipConfigurations/vn
		                           etGatewayConfig"
		                           }
		DnsSettings              : null

2. 아래 명령을 실행하여 PowerShell 명령에 Azure 서비스 관리 API를 사용합니다.

		Switch-AzureMode AzureServiceManagement

3. 아래 명령을 실행하여 Azure 네트워크 구성 파일을 다운로드합니다.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

4. 방금 다운로드한 파일을 열고 **vnet02**에 대한 **LocalNetworkSite** 요소를 편집하여 위의 1단계에서 얻은 새 VNet에 대한 게이트웨이의 IP 주소를 추가합니다. 요소는 아래 샘플과 유사해야 합니다.

	      <LocalNetworkSite name="vnet03">
	        <AddressSpace>
	          <AddressPrefix>10.3.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>23.99.213.28</VPNGatewayAddress>
	      </LocalNetworkSite>

5. 파일을 저장한 다음 아래 명령을 실행하여 클래식 vnet을 구성합니다. 위의 4단계에서 저장한 파일을 가리키도록 경로를 변경해야 합니다.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

6. 아래 명령을 실행하여 클래식 VNet 게이트웨이에 대한 IPSec 공유 키를 설정합니다. 아래 게시된 것과 유사한 출력이 표시됩니다. 사용자 고유의 기존 VNet을 사용하는 경우 VNet 이름을 변경해야 합니다.

		Set-AzureVNetGatewayKey -VNetName vnet01 -LocalNetworkSiteName vnet02 -SharedKey abc123 

		Error          : 
		HttpStatusCode : OK
		Id             : b2154475-bf00-480e-ad1e-aed893014979
		Status         : Successful
		RequestId      : 08257a09d723cb8982c47b85edb0e08a
		StatusCode     : OK

### 2단계: ARM VNet에 대한 게이트웨이 구성

클래식 VNet 게이트웨이를 구성했으므로 이제 연결을 설정해야 합니다. 이렇게 하려면 아래의 지침을 따르세요.

1. PowerShell 콘솔에서 아래 명령을 실행하여 ARM 모드로 전환합니다. 

		Switch-AzureMode AzureResourceManager

2. 다음 명령을 실행하여 게이트웨이 간의 연결을 만듭니다.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name vnet01 -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name ArmAsmGateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "Central US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

3. https://manage.windowsazure.com에서 Azure 포털을 열고 필요한 경우 자격 증명을 입력합니다.
4. **모든 항목**에서 아래로 스크롤하여 **네트워크**를 클릭한 다음 **vnet01**, **대시보드**를 차례로 클릭합니다. 이제 아래와 같이 **vnet01**과 **vnet02** 간의 연결이 설정됩니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure11.png)

5. 클래식 포털에서 클래식 VNet 및 해당 연결을 관리할 수 있지만 새 Azure 포털을 사용하는 것이 좋습니다. 새 포털을 열려면 https://portal.azure.com으로 이동합니다.
6. 새 포털에서 **모두 찾아보기**를 클릭한 다음 **가상 네트워크(클래식)**, **vnet01**을 차례로 클릭합니다. 아래와 같이 **VPN 연결** 창이 표시됩니다.

	![VNet 대시보드](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure12.png)

### 3단계: 연결 테스트

두 개의 VNet을 연결했으므로 이제 다른 VM에서 하나의 VM을 ping하여 연결을 테스트해야 합니다. VM 중 하나의 방화벽 설정을 ICMP를 허용하도록 변경한 다음 다른 VM에서 해당 VM을 ping합니다. 이렇게 하려면 아래의 지침을 따르세요.

1. Azure 포털에서 **모두 찾아보기**, **가상 컴퓨터**, **VM02**를 차례로 클릭합니다.
2. **VM02** 블레이드에서 **연결**을 클릭합니다. 필요한 경우 브라우저 보안 배너에서 **열기**를 클릭하여 RDP 파일을 엽니다.
3. **원격 데스크톱 연결** 대화 상자에서 **연결**을 클릭합니다.
4. **Windows 보안** 대화 상자에서 VM 사용자 이름 및 암호를 입력합니다. 
5. **원격 데스크톱 연결** 대화 상자에서 **예**를 클릭합니다.
6. VM에 연결되면 **서버 관리자**에서 **도구**를 클릭한 다음 **고급 보안이 포함된 Windows 방화벽**을 클릭합니다.
7. **고급 보안이 포함된 Windows 방화벽** 창에서 **아웃바운드 규칙**을 클릭합니다.
8. **인바운드 규칙** 목록에서 **파일 및 프린터 공유(에코 요청 - ICMPv4-In)**를 마우스 오른쪽 단추로 클릭한 다음 **규칙 사용**을 클릭합니다.
9. 작업 표시줄에서 **Windows PowerShell** 단추를 클릭하고 다음 명령을 실행합니다.

		ipconfig

		Connection-specific DNS Suffix  . : 4oxp4ce0c5rulb1iey4cdqhasg.gx.internal.cloudapp.net
		Link-local IPv6 Address . . . . . : fe80::8cea:a98a:5c48:4c58%12
		IPv4 Address. . . . . . . . . . . : 10.2.0.101
		Subnet Mask . . . . . . . . . . . : 255.255.255.0
		Default Gateway . . . . . . . . . : 10.2.0.101

10. VM의 IP 주소를 적어 둡니다. 이 시나리오에서는 **10.2.0.101**입니다. 다른 VM에서 해당 주소를 ping하여 VNet 간의 연결을 테스트합니다.
11. Azure 포털의 왼쪽 창에서 **가상 컴퓨터(클래식)**, **VM01**, **연결**을 클릭합니다. 필요한 경우 브라우저 보안 배너에서 **열기**를 클릭하여 RDP 파일을 엽니다.
12. **원격 데스크톱 연결** 대화 상자에서 **연결**을 클릭합니다.
13. **Windows 보안** 대화 상자에서 VM 사용자 이름 및 암호를 입력합니다. 
14. **원격 데스크톱 연결** 대화 상자에서 **예**를 클릭합니다.
15. 원격 VM의 작업 표시줄에서 **Windows PowerShell** 단추를 클릭하고 다음 명령을 실행합니다.

		ping 10.2.0.101

		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126

## 다음 단계

- [ARM에 대한 NRP(네트워크 리소스 공급자)](../resource-groups-networking.md)에 대해 자세히 알아봅니다.
- [클래식 VNet과 ARM VNet 간의 S2S VPN 연결을 만드는](../virtual-networks-arm-asm-s2s-howto.md) 방법에 대한 일반적인 지침을 살펴봅니다.

<!---HONumber=August15_HO7-->