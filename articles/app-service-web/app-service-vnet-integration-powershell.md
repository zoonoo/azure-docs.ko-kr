<properties 
	pageTitle="PowerShell을 사용하여 VNET에 앱 연결" 
	description="PowerShell을 사용하여 V1 또는 V2 VNET으로 연결 및 작업하는 방법에 대한 지침" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor="cephalin"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2016" 
	ms.author="ccompy"/>

# PowerShell을 사용하여 VNET에 앱 연결 #

## 개요 ##

Azure 앱 서비스에서 구독의 VNET에 앱(웹, 모바일 또는 API)을 연결할 수 있습니다. 이 기능은 VNET 통합이라고 합니다. VNET 통합 기능을 VNET에서 Azure 앱 서비스의 인스턴스를 실행할 수 있게 하는 앱 서비스 환경 기능과 혼동해서는 안됩니다.

VNET 통합에는 새 포털에 UI가 있으며 V1 또는 V2 VNET과 통합할 수 있습니다. 기능에 대해 자세히 알아보려면 여기: Azure 가상 네트워크에 앱 통합으로 이동합니다.

이 문서는 UI 사용에 대한 것이 아니며 PowerShell을 사용하여 통합을 활성화하는 방법에 대한 것입니다. V1 VNET에 대한 명령은 V2 VNET용과 다르므로 두 개의 섹션이 있습니다.

이 문서를 진행하기 전에 다음이 있는지 확인합니다.

1. 설치된 최신 Azure PowerShell SDK. 웹 플랫폼 설치 관리자를 사용하여 설치할 수 있습니다.
1. 표준 또는 프리미엄 SKU에서 실행 중인 Azure 앱 서비스의 앱

## V1(클래식) VNET ##

이 문서에서는 V1 VNET에 대한 세 개의 항목을 설명

- 게이트웨이가 있고 지점 및 사이트 간에 대해 구성된 기존 V1 VNET에 앱 연결
- 앱에 대한 VNET 통합 정보 업데이트
- V1 VNET에서 앱 연결 끊기

### V1(클래식) VNET에 앱 연결 ###

앱을 가상 네트워크(VNET)에 연결하는 3단계 프로세스:

1. 특정 VNET에 가입될 웹앱에 대해 선언합니다. 앱은 지점 및 사이트 간 연결에 대한 VNET에 지정될 인증서 생성
1. VNET에 웹앱 인증서를 업로드한 다음 지점 및 사이트 간 VPN 패키지 URI 검색
1. 지점 및 사이트 간 패키지 URI를 사용하여 웹앱 VNET 연결 업데이트

위의 1단계) 및 3단계)는 완전히 스크립트 가능하지만 2단계)는 포털을 통한 일회성 수동 작업 또는 가상 네트워크 ARM 끝점에서 PUT 또는 PATCH 작업 수행에 대한 액세스가 필요합니다(이 옵션을 사용하려면 Azure 지원 팀에 문의). 시작하기 전에 게이트웨이가 생성/배포된 지점 및 사이트 간이 이미 활성화된 클래식 가상 네트워크가 있는지 확인합니다. 게이트웨이를 만들고 지점 및 사이트 간을 활성화하려면 여기 [VPN 게이트웨이 만들기][createvpngateway]에 설명된 대로 포털을 사용해야 합니다.

V1 VNET은 통합하려는 앱을 보유하는 앱 서비스 계획과 동일한 구독에 있어야 합니다.

#####Azure PowerShell SDK 설정#####

PowerShell 창을 열고 다음을 사용하여 Azure 계정 및 구독을 설정합니다.

	Login-AzureRmAccount

해당 명령으로 Azure 자격 증명을 가져올 것인지 묻는 메시지가 열립니다. 로그인한 후 다음 중 하나를 사용

	Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

또는

	Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

사용할 구독을 선택합니다. VNET 및 앱 서비스 계획에 있는 구독을 사용하고 있는지 확인합니다.

#####이 문서에 사용되는 변수#####

아래 명령을 단순화하려면 특정 구성을 사용하여 $Configuration PowerShell 변수를 설정합니다.

다음 매개 변수를 사용하여 PowerShell에서 다음과 같이 변수를 설정합니다.

	$Configuration = @{} 
	$Configuration.WebAppResourceGroup = "[Your web app resource group]"
	$Configuration.WebAppName = "[Your web app name]"
	$Configuration.VnetSubscriptionId = "[Your vnet subscription id]" 
	$Configuration.VnetResourceGroup = "[Your vnet resource group]" 
	$Configuration.VnetName = "[Your vnet name]"
 
앱 위치는 공백 없는 위치이어야 합니다(예: 미국 서부는 westus).

	$Configuration.WebAppLocation = "[Your web app Location]" 

다음 항목은 인증서가 작성되어야 하는 곳입니다. 로컬 시스템에서 쓰기 가능한 경로여야 합니다. 끝에 .cer을 포함해야 합니다.

	$Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]" 

설치할 것을 확인하려면 $Configuration을 입력합니다.

	> $Configuration
	
	Name                           Value
	----                           -----
	GeneratedCertificatePath       C:\vnetcert.cer
	VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
	WebAppResourceGroup            vnetdemo-rg
	VnetResourceGroup              testase1-rg
	VnetName                       TestNetwork
	WebAppName                     vnetintdemoapp
	WebAppLocation                 centralus

문서의 나머지 부분에서는 위에서 설명한 대로 만든 변수가 있다고 가정합니다.

#####앱에 VNET 선언#####

다음 명령을 사용하여 이 특정 VNET이 사용될 것을 앱에 알립니다. 이렇게 하면 앱에서 필요한 인증서를 생성합니다.

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

이 명령이 성공하면 $vnet은 변수 속성이 있어야 합니다. 속성 변수는 인증서 지문뿐만 아니라 인증서 데이터를 포함해야 합니다.

#####가상 네트워크에 웹앱 인증서 업로드#####

각 구독 및 VNET 조합에 필요한 일회성 수동 단계가 있습니다. 즉, VNET A에 구독 A의 앱을 연결하는 경우 구성하는 앱의 수에 관계 없이 이 단계를 한 번 수행해야 합니다. 다른 VNET에 새 앱을 추가하는 경우 이를 다시 수행해야 합니다. 이에 대한 원인은 인증서 집합이 Azure 앱 서비스의 구독 수준에서 생성되고 앱이 연결하는 각 VNET에 대해 한 번 생성되기 때문입니다.

이 단계를 따랐거나 포털을 사용하여 동일한 VNET으로 통합한 경우 인증서는 이미 설정되었습니다.

첫 번째 단계는 .cer 파일을 생성하는 것입니다. 두 번째 단계는 VNET에 .cer 파일을 업로드하는 것입니다. 이전 단계의 API 호출에서 .cer 파일을 생성하려면 다음 명령을 실행합니다.

	$certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
	[System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

인증서는 $Configuration.GeneratedCertificatePath로 지정된 위치에서 찾을 수 있습니다.

인증서를 수동으로 업로드하려면 [Azure 포털][azureportal]에서 새 포털을 사용하고 가상 네트워크(클래식) –> VPN 연결 –> 지점 및 사이트 간 –> 인증서 관리를 찾습니다. 여기에서 인증서를 업로드합니다.

#####지점 및 사이트 간 패키지 가져오기#####

웹앱에서 VNET 연결을 설정하는 다음 단계는 지점 및 사이트 간 패키지를 가져오고 웹앱에 제공하는 것입니다.

다음 템플릿을 컴퓨터의 어딘가에(예: C:\\Azure\\Templates\\GetNetworkPackageUri.json) GetNetworkPackageUri.json이라는 파일로 저장합니다.

	{ 
		"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#", 
		"contentVersion": "1.0.0.0", 
		"parameters": { 
			"certData": { 
				"type": "string" 
			}, 
			"certThumbprint": { 
				"type": "string" 
			}, 
			"networkName": { 
				"type": "string" 
			} 
		}, 
		"variables": { 
			"legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]" 
			}, 
			"resources": [ 
			], 
		"outputs" : { 
			"PackageUri" : 
			{ 
			"value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string" 
			} 
		} 
	} 


입력 매개 변수 설정:

	$parameters = @{"certData" = $vnet.Properties.certBlob ; 
	certThumbprint = $vnet.Properties.certThumbprint ; 
	"networkName" = $Configuration.VnetName } 

스크립트 호출:

	$output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json 


변수 $output.Outputs.packageUri는 이제 웹앱에 제공되는 패키지 URI를 포함합니다.

#####앱에 지점 및 사이트 간 패키지 업로드#####

마지막 단계는 이 패키지와 함께 앱을 제공하는 것입니다. 다음 명령을 실행하면 됩니다.

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation 

기존 리소스를 덮어쓸지 확인하라고 묻는 메시지가 나타날 수 있으며 이를 허용합니다.

이 명령의 성공으로 이제 앱은 VNET에 연결되어야 합니다. 앱 콘솔로 이동하여 이를 확인하고 다음을 입력할 수 있습니다.

	SET WEBSITE_

대상 VNET의 이름과 일치하는 값으로 WEBSITE\_VNETNAME이라는 환경 변수가 있는 경우 모든 구성이 성공되었습니다.

###V1(클래식) VNET 통합 정보 업데이트###

정보를 업데이트 또는 다시 동기화하려면 통합을 만들 때 이어지는 단계를 처음부터 반복해야 합니다. 이러한 단계는 다음과 같습니다.

1. 구성 정보 정의
1. 앱에 VNET 선언
1. 지점 및 사이트 간 패키지 가져오기
1. 앱에 지점 및 사이트 간 패키지 업로드

###V1(클래식) VNET에서 앱 연결 끊기###

연결을 끊으려면 VNET 통합 중 설정된 구성 정보가 필요합니다. 여기에서 해당 정보 사용은 VNET에서 앱을 분리하는 데 필요한 하나의 명령입니다.

	$vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## V2(리소스 관리자) VNET ##

V2 또는 리소스 관리자 VNET에는 ARM API가 있으며 V1 VNET보다 쉽게 구성하도록 수행할 수 있는 몇 가지가 있습니다. 다음이 가능하도록 제공된 스크립트가 있습니다.

- V2 VNET 만들기 및 앱 통합
- 게이트웨이를 만들고 기존 V2 VNET에서 지점 및 사이트 간을 구성한 다음 앱 통합
- 게이트웨이 및 지점 및 사이트 간이 활성화된 기존 V2 VNET과 앱 통합 
- V2 VNET에서 앱 연결 끊기

###V2 VNET 앱 서비스 통합 스크립트###

다음 스크립트를 복사하고 파일에 저장합니다. 스크립트를 그대로 사용하지 않으려는 경우 V2 VNET으로 설정하는 방법을 참조하여 자유롭게 학습할 수 있습니다.


    function ReadHostWithDefault($message, $default)
    {
    	$result = Read-Host "$message [$default]"
    	if($result -eq "")
	    {
			$result = $default
	    }
		    return $result
    	}
    
	function PromptCustom($title, $optionValues, $optionDescriptions)
	{
	    Write-Host $title
	    Write-Host
	    $a = @()
	    for($i = 0; $i -lt $optionValues.Length; $i++){
		    Write-Host "$($i+1))" $optionDescriptions[$i]
	    }
	    Write-Host
    
	    while($true)
	    {
		    Write-Host "Choose an option: "
		    $option = Read-Host
		    $option = $option -as [int]
		    
		    if($option -ge 1 -and $option -le $optionValues.Length)
		    {
			    return $optionValues[$option-1]
		    }
	    }
    }
    
    function PromptYesNo($title, $message, $default = 0)
    {
	    $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
	    $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
	    $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
	    $result = $host.ui.PromptForChoice($title, $message, $options, $default)
	    return $result
    }
    
    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
	    Write-Host "Creating a new VNET"
	    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
	    New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }
    
    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
	    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
	    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	    
	    Write-Host "Creating a public IP address for this VNET"
	    $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
	    $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip
	    
	    Write-Host "Adding a root certificate to this VNET"
	    $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData
	    
	    Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
	    New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }
    
    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
	    Write-Host "Adding a new Vnet"
	    Write-Host
	    $vnetName = Read-Host "Specify a name for this Virtual Network"
	    
	    $vnetGatewayName="$($vnetName)-gateway"
	    $vnetIpName="$($vnetName)-ip"
	    $vnetIpConfigName="$($vnetName)-ipconf"
	    
	    # Virtual Network settings
	    $vnetAddressSpace="10.0.0.0/8"
	    $vnetGatewayAddressSpace="10.5.0.0/16"
	    $vnetPointToSiteAddressSpace="172.16.0.0/16"
	    
	    $changeRequested = 0
	    $resourceGroupName = $webAppResourceGroup
	    
	    while($changeRequested -eq 0)
	    {
		    Write-Host
		    Write-Host "Currently, I will create a VNET with the following settings:"
		    Write-Host
		    Write-Host "Virtual Network Name: $vnetName"
		    Write-Host "Resource Group Name:  $resourceGroupName"
		    Write-Host "Gateway Name: $vnetGatewayName"
		    Write-Host "Vnet IP name: $vnetIpName"
		    Write-Host "Vnet IP config name:  $vnetIpConfigName"
		    Write-Host "Address Space:$vnetAddressSpace"
		    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
		    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
		    Write-Host
		    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1
		    
		    if($changeRequested -eq 0)
		    {
			    $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
			    $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
			    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
			    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
			    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
			    $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
			    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
			    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
		    }
	    }
    
	    $ErrorActionPreference = "Stop";
	    
	    # We create the virtual network and add it here. The way this works is:
	    # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
	    # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
	    # 3) Get the VPN package from the gateway and pass it back to the App.
	    
	    $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
	    $location = $webApp.Location
	    
	    Write-Host "Creating App assocation to VNET"
	    $propertiesObject = @{
	     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
	    }
	    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force
	       
	    CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location
	    
	    CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace
	    
	    Write-Host "Retreiving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64
	    
	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
	    }
	    
	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force
	    
	    Write-Host "Finished!"
    }
    
    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
		$ErrorActionPreference = "Stop";
		
		# At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
		Write-Host "Getting App information"
		$webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$location = $webApp.Location
		   
		$webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$currentVnet = $webAppConfig.Properties.VnetName
		if($currentVnet -ne $null -and $currentVnet -ne "")
		{
			Write-Host "Currently connected to VNET $currentVnet"
		}
		
		# Display existing vnets
		$vnets = Get-AzureRmVirtualNetwork
		$vnetNames = @()
		foreach($vnet in $vnets)
		{
			$vnetNames += $vnet.Name
	    }
	    
	    Write-Host
	    $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames
	    
	    # We need to check if this VNET is able to be joined to a App, based on following criteria
		    # If there is no gateway, we can create one.
		    # If there is a gateway:
			    # It must be of type Vpn
			    # It must be of VpnType RouteBased
			    # If it doesn't have the right certificate, we will need to add it.
			    # If it doesn't have a point-to-site range, we will need to add it.
	       
	    $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }
	       
	    if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
	    {
			$ErrorActionPreference = "Continue";
		    # There is no gateway. We need to create one.
		    Write-Host "This Virtual Network has no gateway. I will need to create one."
		    
		    $vnetName = $vnet.Name
		    $vnetGatewayName="$($vnetName)-gateway"
		    $vnetIpName="$($vnetName)-ip"
		    $vnetIpConfigName="$($vnetName)-ipconf"
		    
		    # Virtual Network settings
		    $vnetAddressSpace="10.0.0.0/8"
		    $vnetGatewayAddressSpace="10.5.0.0/16"
		    $vnetPointToSiteAddressSpace="172.16.0.0/16"
		    
		    $changeRequested = 0
		    
		    Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
		    foreach($subnet in $vnet.Subnets)
		    {
			    Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
		    }
		    
		    $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"
		    
		    while($changeRequested -eq 0)
		    {
			    Write-Host
			    Write-Host "Currently, I will create a VNET gateway with the following settings:"
			    Write-Host
			    Write-Host "Virtual Network Name: $vnetName"
			    Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
			    Write-Host "Gateway Name: $vnetGatewayName"
			    Write-Host "Vnet IP name: $vnetIpName"
			    Write-Host "Vnet IP config name:  $vnetIpConfigName"
			    Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
			    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
			    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
			    Write-Host
			    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1
			    
			    if($changeRequested -eq 0)
			    {
				    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
				    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
				    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
				    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
				    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
			    }
		    }
		    
		    $ErrorActionPreference = "Stop";
		    
		    Write-Host "Creating App assocation to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }
		    
		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
		    
		    # If there is no gateway subnet, we need to create one.
		    if($gatewaySubnet -eq $null)
		    {
			    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
			    $vnet.Subnets.Add($gatewaySubnet);
			    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
		    }
		    
		    CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace
		    
		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
	    }
	    else
	    {
		    $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
		    $gatewayResourceGroup = $uriParts[4]
		    $gatewayName = $uriParts[8]
		       
		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName
		    
		    # validate gateway types, etc.
		    if($gateway.GatewayType -ne "Vpn")
		    {
			    Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
			    return
		    }
		    
		    if($gateway.VpnType -ne "RouteBased")
		    {
			    Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
			    return
		    }
		    
		    if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
		    {
			    Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
			    $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
			    Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
		    }
		    
		    Write-Host "Creating App assocation to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }
		    
		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
		    
		    # We need to check if the certificate here exists in the gateway.
		    $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates
		    
		    $certFound = $false
		    foreach($certificate in $certificates)
		    {
			    if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
			    {
				    $certFound = $true
				    break
			    }
		    }
		    
		    if(-not $certFound)
		    {
			    Write-Host "Adding certificate"
			    Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
		    }
	    }
	    
	    # Now finish joining by getting the VPN package and giving it to the App
	    Write-Host "Retreiving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64
	    
	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
	    }
	    
	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
	    
	    Write-Host "Finished!"
    }
       
    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
	    $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    $currentVnet = $webAppConfig.Properties.VnetName
	    if($currentVnet -ne $null -and $currentVnet -ne "")
	    {
		    Write-Host "Currently connected to VNET $currentVnet"
		    
		    Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    }
	  	  else
	    {
	  	  Write-Host "Not connected to a VNET."
	    }
    }
    
    Write-Host "Please Login"
    Login-AzureRmAccount
    
    # Choose subscription. If there's only one we will choose automatically
    
    $subs = Get-AzureRmSubscription
    $subscriptionId = ""
    
    if($subs.Length -eq 0)
    {
	    Write-Error "No subscriptions bound to this account."
	    return
    }
    
    if($subs.Length -eq 1)
    {
	    $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
	    $subscriptionChoices = @()
	    $subscriptionValues = @()
	       
	    foreach($subscription in $subs){
	    $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
	    $subscriptionValues += ($subscription.SubscriptionId);
	    }
	    
	    $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }
    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    $resourceGroup = Read-Host "Please enter the Resource Group of your App"
    
    $appName = Read-Host "Please enter the Name of your App"
    
    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options
    
    if($option -eq 0)
    {
		AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
	    AddExistingVnet $subscriptionId $resourceGroup $appName
    }
	if($option -eq 2)
    {
	    RemoveVnet $subscriptionId $resourceGroup $appName
    }
    
스크립트의 복사본을 저장합니다. 이 문서에서 V2VnetAllinOne.ps1이라고 하지만 원하는 대로 부를 수 있습니다. 이 스크립트에 대한 인수가 없습니다. 단순히 실행할 수 있습니다. 스크립트가 가장 먼저 수행하는 작업은 로그인하라는 메시지를 표시하는 것입니다. 로그인한 후 스크립트는 계정에 대한 세부 정보를 가져오고 선택할 구독 목록을 반환합니다. 자격 증명을 묻는 팝업은 계산에서 제외되며 초기 스크립트 실행은 다음과 같습니다.

	PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
	Please Login
	
	Environment           : AzureCloud
	Account               : ccompy@microsoft.com
	TenantId              : 722278f-fef1-499f-91ab-2323d011db47
	SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
	CurrentStorageAccount :
	
	Choose a subscription
	
	1) Demo Subscription (af5358e1-acac-2c90-a9eb-722190abf47a)
	2) MS Test (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
	3) Purple Demo Subscription (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)
	
	Choose an option:
	3
	
	Account      : ccompy@microsoft.com
	Environment  : AzureCloud
	Subscription : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d
	Tenant       : 722278f-fef1-499f-91ab-2323d011db47
	
	Please enter the Resource Group of your App: hcdemo-rg
	Please enter the Name of your App: v2vnetpowershell
	What do you want to do?
	
	1) Add a NEW Virtual Network to an App
	2) Add an EXISTING Virtual Network to an App
	3) Remove a Virtual Network from an App

이 문서의 나머지 부분에서는 이러한 각 세 가지 옵션으로 이동합니다.

###V2(리소스 관리자) VNET 만들기 및 통합###

새 V2 VNET을 만들고 앱과 통합하려면 1) 앱에 새 가상 네트워크 추가를 선택합니다. VNET의 이름을 묻는 메시지가 나타납니다. 아래 필자의 경우는 이름 v2pshell을 제공했습니다.

스크립트는 생성되는 VNET에 대한 세부 정보를 제공합니다. 원한다면 값을 변경할 수 있습니다. 이 예제 실행에서는 다음 설정을 사용하여 VNET을 만들었습니다.

	Virtual Network Name:         v2pshell
	Resource Group Name:          hcdemo-rg
	Gateway Name:                 v2pshell-gateway
	Vnet IP name:                 v2pshell-ip
	Vnet IP config name:          v2pshell-ipconf
	Address Space:                10.0.0.0/8
	Gateway Address Space:        10.5.0.0/16
	Point-To-Site Address Space:  172.16.0.0/12
	
	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):

값을 변경하려는 경우 Y를 입력하고 원하는 대로 변경합니다. VNET 설정에 만족하면 N을 입력하거나 설정 변경에 대한 메시지가 표시되면 단순히 반환을 누릅니다. 거기에서 완료까지 스크립트는 VNET 게이트웨이 만들기로 이동할 때까지 수행하는 작업의 일부를 설명합니다. 해당 단계는 최대 1시간이 걸릴 수 있습니다. 이 단계에 있는 동안 진행률 표시기가 없지만 게이트웨이가 만들어지면 알려줍니다.

스크립트가 완료되면 **완료**를 표시합니다. 이 시점에서 선택한 이름 및 설정으로 만들어진 V2 VNET을 가지며 이 새로운 VNET은 앱과 통합됩니다.

###기존 V2 VNET과 앱 통합###

기존 VNET과 통합할 때 게이트웨이 또는 지점 및 사이트 간이 없는 V2 VNET을 제공하는 경우 스크립트는 해당 내용을 설정합니다. VNET이 이미 해당 항목을 설정한 경우 앱 통합으로 바로 이동합니다. 이 프로세스를 시작하려면 단순히 옵션 2) 앱에 기존 가상 네트워크 추가를 선택합니다.

앱과 동일한 구독에 있는 기존 V2 VNET이 있는 경우에만 이 옵션은 작동합니다. 옵션 2를 선택한 후 V2 VNET의 목록이 표시됩니다.

	Select a VNET to integrate with
	
	1) v2demonetwork
	2) v2pshell
	3) v2vnetintdemo
	4) v2asenetwork
	5) v2pshell2
	
	Choose an option:
	5

통합하려는 VNET을 선택하면 됩니다. 지점 및 사이트 간이 활성화된 게이트웨이가 이미 있는 경우 스크립트는 VNET과 앱을 통합합니다. 게이트웨이가 없는 경우 게이트웨이 서브넷을 지정해야 합니다. 게이트웨이 서브넷은 VNET 주소 공간에 있어야 하고 다른 서브넷에 있을 수 없습니다. 따라서 게이트웨이가 없는 VNET이 있고 이 단계를 실행하는 경우 다음과 같습니다.

	This Virtual Network has no gateway. I will need to create one.
	Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
	default: 172.16.0.0/24
	Please choose a GatewaySubnet address space: 172.16.1.0/26

이 예제에서는 다음 설정을 사용하여 VNET 게이트웨이를 만들었습니다.

	Virtual Network Name:         v2pshell2
	Resource Group Name:          vnetdemo-rg
	Gateway Name:                 v2pshell2-gateway
	Vnet IP name:                 v2pshell2-ip
	Vnet IP config name:          v2pshell2-ipconf
	Address Space:                172.16.0.0/16
	Gateway Address Space:        172.16.1.0/26
	Point-To-Site Address Space:  172.16.0.0/12
	
	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):
	Creating App assocation to VNET

이러한 설정을 변경하려는 경우 그렇게 할 수 있으며 그렇지 않으면 반환을 누르고 계속해서 게이트웨이를 만들고 앱을 VNET에 연결합니다. 게이트웨이 생성 시간은 여전히 한 시간이 걸리므로 이를 유의하세요. 모두 완료되면 스크립트는 완료를 표시합니다.

###V2 VNET에서 앱 연결 끊기###

V2 VNET에서 앱 분리는 게이트웨이를 종료하거나 지점 및 사이트 간을 비활성화하지 않습니다. 다른 용도로 모두 사용한 후 수행할 수 있습니다. 또한 제공하는 것 외에 다른 앱에서 연결을 끊지 않습니다. 이 작업을 수행하려면 3) 앱에서 가상 네트워크 제거를 선택합니다. 이를 수행할 때 다음과 같이 표시됩니다.

	Currently connected to VNET v2pshell
	
	Confirm
	Are you sure you want to delete the following resource:
	/subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
	hell/virtualNetworkConnections/v2pshell
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

스크립트는 삭제를 표시하지만 VNET을 삭제하지는 않습니다. 통합만 제거됩니다. 원하는 것인지를 확인한 후에 명령은 매우 신속하게 처리되고 완료되면 True를 표시합니다.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->