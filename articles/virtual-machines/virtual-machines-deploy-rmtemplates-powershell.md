<properties
	pageTitle="Azure 리소스 관리자 VM 관리 | Microsoft Azure"
	description="Azure 리소스 관리자 템플릿 및 PowerShell을 사용하여 가상 컴퓨터 관리"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Azure 리소스 관리자 및 PowerShell을 사용하여 가상 컴퓨터 관리

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [PowerShell - Windows](virtual-machines-deploy-rmtemplates-powershell.md)
- [Azure CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

Azure PowerShell 및 리소스 관리자 템플릿을 사용하여 제공Microsoft Azure에서 리소스를 관리할 때 많은 성능과 유연성을 제공합니다. 이 문서의 작업을 사용하여 가상 컴퓨터를 만들고 관리할 수 있습니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-tutorial-classic-portal.md).

다음 작업에는 리소스 관리자 템플릿 및 PowerShell을 사용합니다.

- [가상 컴퓨터 만들기](#windowsvm)
- [전문화된 디스크를 사용하여 가상 컴퓨터 만들기](#customvm)
- [외부 부하 분산 장치를 사용하여 가상 네트워크에 여러 가상 컴퓨터 만들기](#multivm)

다음 작업에는 PowerShell만 사용합니다.

- [리소스 그룹 제거](#removerg)
- [가상 컴퓨터에 대한 정보 표시](#displayvm)
- [가상 컴퓨터 시작](#start)
- [가상 컴퓨터 중지](#stop)
- [가상 컴퓨터 다시 시작](#restart)
- [가상 컴퓨터 삭제](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Azure 리소스 관리자 템플릿 및 리소스 그룹

이 문서의 일부 작업에서는 Azure 리소스 관리자 템플릿 및 Powershell을 사용하여 Azure 가상 컴퓨터를 자동으로 배포하고 관리하는 방법을 보여 줍니다.

Microsoft Azure에서 실행 중인 대부분의 응용 프로그램은 다양한 클라우드 리소스 유형(예: 하나 이상의 가상 컴퓨터 및 저장소 계정, SQL 데이터베이스 또는 가상 네트워크)의 조합으로 구축됩니다. Azure 리소스 관리자 템플릿을 사용하면 리소스와 관련 구성 및 배포 매개 변수에 대한 JSON 설명을 사용하여 이처럼 다양한 리소스를 함께 관리할 수 있습니다.

JSON 기반 리소스 템플릿을 정의한 후 해당 템플릿을 PowerShell 명령과 함께 사용하여 Azure에서 정의된 리소스를 배포할 수 있습니다. 이러한 명령은 PowerShell 명령 셸 내에서 별도로 실행하거나, 추가 자동화 논리를 포함하는 스크립트 안에 통합할 수 있습니다.

Azure 리소스 관리자 템플릿을 사용하여 만드는 리소스를 새 또는 기존 *Azure 리소스 그룹*에 배포합니다. 리소스 그룹을 사용하면 논리 그룹으로 함께 배포된 여러 리소스를 관리할 수 있습니다. 즉, 그룹 응용 프로그램의 전체 수명 주기를 관리할 수 있습니다.

템플릿 작성에 관심이 있다면 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.

### 리소스 그룹 만들기

리소스를 만드는 작업의 경우 리소스 그룹(아직 없다면)이 필요합니다.

다음 명령에서 *리소스 그룹 이름*을 새 리소스 그룹의 이름으로 바꾸고 *Azure 위치*를 리소스를 찾을 Azure 데이터 센터 위치로 바꾼 다음 명령을 실행합니다.

	New-AzureRmResourceGroup -Name "resource group name" -Location "Azure location"

## <a id="windowsvm"></a>작업: 가상 컴퓨터 만들기

이 작업에서는 템플릿 갤러리의 템플릿을 사용합니다. 템플릿에 대한 자세한 내용을 배우려면 [미국 서부에서 Windows 가상 컴퓨터 배포](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)를 참조하세요.

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)

다음 명령에서는 *배포 이름*을 배포에 사용할 이름으로 바꾸고 *리소스 그룹 이름*을 기존 리소스 그룹의 이름으로 바꾼 다음 명령을 실행합니다.

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

예를 들면 다음과 같습니다.

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

JSON 파일의 **parameters** 섹션에 매개 변수의 값을 제공하라는 프롬프트가 표시됩니다.

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso

이때 반환되는 내용은 다음과 같습니다.

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
	                    newStorageAccountName  String                     saacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO deploy-a-windows-virtual-machine-with-azure-resource-manager-templates-and-powershell]

## <a id="customvm"></a>작업: 전문화된 디스크를 사용하여 가상 컴퓨터 만들기

이 작업에서는 템플릿 갤러리의 템플릿을 사용합니다. 템플릿에 대해 더 자세히 배우려면 [전문화된 VHD 디스크를 사용하여 가상 컴퓨터 만들기](https://azure.microsoft.com/documentation/templates/201-vm-from-specialized-vhd/)를 참조하세요.

다음 명령에서는 *배포 이름*을 배포에 사용할 이름으로 바꾸고 *리소스 그룹 이름*을 기존 리소스 그룹의 이름으로 바꾼 다음 명령을 실행합니다.

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

예를 들면 다음과 같습니다.

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

JSON 파일의 **parameters** 섹션에 매개 변수의 값을 제공하라는 프롬프트가 표시됩니다.

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

> [AZURE.NOTE]위와 같은 예제에서는 saacct 저장소 계정에 존재하는 vhd 파일을 사용합니다. 디스크의 이름은 템플릿에 대한 매개 변수로 제공됩니다.

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell]

## <a id="multivm"></a>작업: 외부 부하 분산 장치를 사용하여 가상 네트워크에 여러 가상 컴퓨터 만들기

이 작업에서는 템플릿 갤러리의 템플릿을 사용합니다. 템플릿에 대해 더 자세히 배우려면 [전문화된 VHD 디스크를 사용하여 가상 컴퓨터 만들기](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/)를 참조하세요.

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

다음 명령에서는 *배포 이름*을 배포에 사용할 이름으로 바꾸고 *리소스 그룹 이름*을 기존 리소스 그룹의 이름으로 바꾼 다음 명령을 실행합니다.

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"

JSON 파일의 **parameters** 섹션에 매개 변수의 값을 제공하라는 프롬프트가 표시됩니다.

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO deploy-multi-vm-app-with-a-virtual-network-and-load-balancer-in-azure-resource-manager]

## <a id="removerg"></a>작업: 리소스 그룹 제거

다음 명령에서는 *리소스 그룹 이름*을 제거할 리소스 그룹의 이름으로 바꾼 다음 명령을 실행합니다.

	Remove-AzureRmResourceGroup  -Name "resource group name"

> [AZURE.NOTE]**–Force** 매개 변수를 사용하여 확인 프롬프트를 건너뜁니다.

-Force 매개 변수를 사용하지 않은 경우 확인하라는 메시지가 나타납니다.

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO removing-a-resource-group-in-azure]

## <a id="displayvm"></a>작업: 가상 컴퓨터에 대한 정보 표시

다음 명령에서는 *리소스 그룹 이름*을 가상 컴퓨터가 포함된 리소스 그룹의 이름으로 바꾸고 *가상 컴퓨터 이름*을 컴퓨터의 이름으로 바꾼 다음 명령을 실행합니다.

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

이때 반환되는 내용은 다음과 같습니다.

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/{subscription-id}/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://saacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>작업: 가상 컴퓨터 시작

다음 명령에서는 *리소스 그룹 이름*을 가상 컴퓨터가 포함된 리소스 그룹의 이름으로 바꾸고 *가상 컴퓨터 이름*을 컴퓨터의 이름으로 바꾼 다음 명령을 실행합니다.

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

이때 반환되는 내용은 다음과 같습니다.

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>작업: 가상 컴퓨터 중지

다음 명령에서는 *리소스 그룹 이름*을 가상 컴퓨터가 포함된 리소스 그룹의 이름으로 바꾸고 *가상 컴퓨터 이름*을 컴퓨터의 이름으로 바꾼 다음 명령을 실행합니다.

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

확인을 요청하는 메시지가 나타납니다.

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

이때 반환되는 내용은 다음과 같습니다.

	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>작업: 가상 컴퓨터 다시 시작

다음 명령에서는 *리소스 그룹 이름*을 가상 컴퓨터가 포함된 리소스 그룹의 이름으로 바꾸고 *가상 컴퓨터 이름*을 컴퓨터의 이름으로 바꾼 다음 명령을 실행합니다.

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

이때 반환되는 내용은 다음과 같습니다.

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>작업: 가상 컴퓨터 삭제

다음 명령에서는 *리소스 그룹 이름*을 가상 컴퓨터가 포함된 리소스 그룹의 이름으로 바꾸고 *가상 컴퓨터 이름*을 컴퓨터의 이름으로 바꾼 다음 명령을 실행합니다.

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE]**–Force** 매개 변수를 사용하여 확인 프롬프트를 건너뜁니다.

-Force 매개 변수를 사용하지 않은 경우 확인하라는 메시지가 나타납니다.

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

이때 반환되는 내용은 다음과 같습니다.

	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## 추가 리소스
[Azure 빠른 시작 템플릿](http://azure.microsoft.com/documentation/templates/) 및 [앱 프레임워크](virtual-machines-app-frameworks.md)

[Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)

[Azure 리소스 관리자 개요](resource-group-overview.md)

[가상 컴퓨터 설명서](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=AcomDC_1203_2015-->