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
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Azure 리소스 관리자 및 PowerShell을 사용하여 가상 컴퓨터 관리

> [AZURE.SELECTOR]
- [PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)
- [CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

<br>


Azure PowerShell 및 리소스 관리자 템플릿을 사용하여 제공Microsoft Azure에서 리소스를 관리할 때 많은 성능과 유연성을 제공합니다. 이 문서의 작업을 사용하여 가상 컴퓨터를 관리할 수 있습니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

다음 작업에는 PowerShell만 사용합니다.

- [가상 컴퓨터에 대한 정보 표시](#displayvm)
- [가상 컴퓨터 시작](#start)
- [가상 컴퓨터 중지](#stop)
- [가상 컴퓨터 다시 시작](#restart)
- [가상 컴퓨터 삭제](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>가상 컴퓨터에 대한 정보 표시

다음 명령에서는 *리소스 그룹 이름*을 가상 컴퓨터가 포함된 리소스 그룹의 이름으로 바꾸고 *가상 컴퓨터 이름*을 컴퓨터의 이름으로 바꾼 다음 명령을 실행합니다.

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

이때 반환되는 내용은 다음과 같습니다.


	ResourceGroupName        : rg1
	Id                       : /subscriptions/{subscription-id}/resourceGroups/
															rg1/providers/Microsoft.Compute/virtualMachines/vm1
	Name                     : vm1
	Type                     : Microsoft.Azure.Management.Compute.Models.VirtualMachineGetResponse
	Location                 : westus
	Tags                     : {}
	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          :  {
																"VirtualMachineSize": "Standard_D1"
															}
	InstanceView             : null
	Location                 : westus
	Name                     : vm1
	NetworkProfile           :  {
																"NetworkInterfaces": [
																	{
																		"Primary": null,
																		"ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/
																		rg1/providers/Microsoft.Network/networkInterfaces/nc1"
																	}
																]
															}
	OSProfile                :  {
																"AdminPassword": null,
																"AdminUsername": "WinAdmin1",
																"ComputerName": "vm1",
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
	StorageProfile           : 	{
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
																		"Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
																	}
																}
															}
	DataDiskNames            :  {}
	NetworkInterfaceIDs      : { /subscriptions/{subscription-id}/resourceGroups/
																rg1/providers/Microsoft.Network/networkInterfaces/nc1}

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>가상 컴퓨터 시작

다음 명령에서는 *리소스 그룹 이름*을 가상 컴퓨터가 포함된 리소스 그룹의 이름으로 바꾸고 *가상 컴퓨터 이름*을 컴퓨터의 이름으로 바꾼 다음 명령을 실행합니다.

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

이때 반환되는 내용은 다음과 같습니다.

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 06935ddf-6e89-48d2-b46a-229493e3e9d1
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : c1aa0a70-4f4f-4d6c-a8ac-7ea35c004ce0

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>가상 컴퓨터 중지

다음 명령에서는 *리소스 그룹 이름*을 가상 컴퓨터가 포함된 리소스 그룹의 이름으로 바꾸고 *가상 컴퓨터 이름*을 컴퓨터의 이름으로 바꾼 다음 명령을 실행합니다.

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

확인을 요청하는 메시지가 나타납니다.

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

이때 반환되는 내용은 다음과 같습니다.

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : e1705973-d266-467e-8655-920016145347

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>가상 컴퓨터 다시 시작

다음 명령에서는 *리소스 그룹 이름*을 가상 컴퓨터가 포함된 리소스 그룹의 이름으로 바꾸고 *가상 컴퓨터 이름*을 컴퓨터의 이름으로 바꾼 다음 명령을 실행합니다.

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

이때 반환되는 내용은 다음과 같습니다.

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 4b05891c-fdff-4c9a-89ca-e4f1d7691aed
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:06:53 PM -08:00
	EndTime             : 1/5/2016 12:06:54 PM -08:00
	TrackingOperationId : 5aeeab89-45ab-41b9-84ef-9e9a7e732207


수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>가상 컴퓨터 삭제

다음 명령에서는 *리소스 그룹 이름*을 가상 컴퓨터가 포함된 리소스 그룹의 이름으로 바꾸고 *가상 컴퓨터 이름*을 컴퓨터의 이름으로 바꾼 다음 명령을 실행합니다.

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE] **–Force** 매개 변수를 사용하여 확인 프롬프트를 건너뜁니다.

-Force 매개 변수를 사용하지 않은 경우 확인하라는 메시지가 나타납니다.

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

이때 반환되는 내용은 다음과 같습니다.

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 2d723b40-ce1f-4b11-a603-dc659a13b6f0
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:10:28 PM -08:00
	EndTime             : 1/5/2016 12:12:12 PM -08:00
	TrackingOperationId : d138ab29-83bf-4948-9d13-dab87db1a639

수행 중인 이 작업에 대한 비디오를 보려면 다음을 살펴보십시오.

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

<!---HONumber=AcomDC_0128_2016-->