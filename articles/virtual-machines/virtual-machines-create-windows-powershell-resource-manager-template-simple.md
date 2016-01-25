<properties
	pageTitle="템플릿을 사용하여 VM 만들기 | Microsoft Azure"
	description="리소스 관리자 템플릿 및 Azure PowerShell을 사용하여 Windows 가상 컴퓨터를 만들 수 있습니다."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# 리소스 관리자 템플릿 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.


Azure PowerShell과 함께 리소스 관리자 템플릿을 사용하여 새 Windows 기반 Azure VM(가상 컴퓨터)을 쉽게 만들 수 있습니다. 이 템플릿은 새 리소스 그룹에 단일 서브넷이 있는 새 가상 네트워크에서 Windows를 실행하는 단일 가상 컴퓨터를 만듭니다.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Windows VM 만들기

Azure PowerShell과 Github 템플릿 리포지토리의 리소스 관리자 템플릿을 사용하여 Windows VM을 만들려면 다음 단계를 수행하세요.

다음 명령에서 배포에 사용하려는 이름, 새 리소스 그룹에 대한 이름 및 리소스를 만들어야 하는 위치를 입력합니다.


		$deployName="<deployment name>"
		$RGName="<resource group name>"
		$locName="<Azure location, such as West US>"
		$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
		New-AzureRmResourceGroup –Name $RGName –Location $locName
		New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


**New-AzureRmResourceGroupDeployment** 명령을 실행하면 JSON 파일의 "parameters" 섹션에 매개 변수 값을 제공하라는 메시지가 표시됩니다. 매개 변수 값을 모두 지정하는 경우 명령을 통해 리소스 그룹과 가상 컴퓨터를 만듭니다.

다음과 유사한 결과가 표시됩니다.


		cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
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
											Name                   Type                       Value
											===============        =========================  ==========
											newStorageAccountName  String                     newsaacct
											adminUsername          String                     WinAdmin1
											adminPassword          SecureString
											dnsNameForPublicIP     String                     contoso
											windowsOSVersion       String                     2012-R2-Datacenter
		Outputs           :


## 다음 단계

[Azure 리소스 관리자 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-deploy-rmtemplates-powershell.md)에서 방금 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0114_2016-->