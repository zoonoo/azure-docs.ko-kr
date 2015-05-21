<properties 
	pageTitle="리소스 관리자 템플릿 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기" 
	description="리소스 관리자 템플릿 및 Azure PowerShell을 사용하여 Windows 가상 컴퓨터를 만들 수 있습니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# 리소스 관리자 템플릿 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기

Azure PowerShell과 함께 리소스 관리자 템플릿을 사용하여 새 Windows 기반 Azure VM\(가상 컴퓨터\)을 쉽게 만들 수 있습니다. 이 템플릿은 새 리소스 그룹에 단일 서브넷이 있는 새 가상 네트워크에서 Windows를 실행하는 단일 가상 컴퓨터를 만듭니다.

![](./media/virtual-machines-create-windows-powershell-rm-template-simple/windowsvm.png)
 
시작하기 전에 Azure와 PowerShell이 구성 및 준비되어 있는지 확인합니다.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

## Windows VM 만들기

Azure PowerShell과 Github 템플릿 리포지토리의 리소스 관리자 템플릿을 사용하여 Windows VM을 만들려면 다음 단계를 수행하세요.

Azure 배포 이름, 리소스 그룹 이름 및 Azure 데이터 센터 위치를 입력하고 다음 명령을 실행합니다.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

**New-AzureResourceGroupDeployment** 명령을 실행하면 JSON 파일의 "parameters" 섹션에 매개 변수 값을 제공하라는 메시지가 표시됩니다. 매개 변수 값을 모두 지정하면 명령에서 리소스 그룹과 가상 컴퓨터를 만듭니다.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

다음과 같이 표시됩니다.

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	
	
	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

이제 새 리소스 그룹에 MyWindowsVM이라는 새 Windows 가상 컴퓨터가 생성되었습니다.

## 추가 리소스

[Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)

[Azure 리소스 관리자 개요](resource-group-overview.md)

[Azure 리소스 관리자 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-create-windows-powershell-rm.md)

[PowerShell 및 Azure 서비스 관리자를 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-create-windows-powershell-sm.md)

[가상 컴퓨터 설명서](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure PowerShell을 설치 및 구성하는 방법](install-configure-powershell.md)

<!--HONumber=52-->
