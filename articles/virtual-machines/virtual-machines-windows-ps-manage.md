<properties
	pageTitle="리소스 관리자 및 PowerShell을 사용하여 VM 관리 | Microsoft Azure"
	description="Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# 리소스 관리자 및 PowerShell을 사용하여 Azure 가상 컴퓨터 관리

## Azure PowerShell 설치
 
최신 버전의 Azure PowerShell을 설치하고 사용하려는 구독을 선택하며 Azure 계정에 로그인하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## 변수 설정

이 문서의 모든 명령은 가상 컴퓨터가 있는 리소스 그룹 이름 및 관리할 가상 컴퓨터 이름이 필요합니다. **$rgName** 값을 가상 컴퓨터를 포함하는 리소스 그룹 이름으로 바꿉니다. **$vmName** 값을 VM 이름으로 바꿉니다. 변수를 만듭니다.

        $rgName = "resource group name"
        $vmName = "VM name"

## 가상 컴퓨터에 대한 정보 표시

가상 컴퓨터 정보를 가져옵니다.
  
        Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

이때 반환되는 내용은 다음과 같습니다.

        ResourceGroupName        : rg1
        Id                       : /subscriptions/{subscription-id}/resourceGroups/
                                    rg1/providers/Microsoft.Compute/virtualMachines/vm1
        Name                     : vm1
        Type                     : Microsoft.Compute/virtualMachines
        Location                 : centralus
        Tags                     : {}
        AvailabilitySetReference : {
                                     "id": "/subscriptions/{subscription-id}/resourceGroups/
                                       rg1/providers/Microsoft.Compute/availabilitySets/av1"
                                   }
        Extensions               : []
        HardwareProfile          : {
                                     "vmSize": "Standard_A0"
                                   }
        InstanceView             : null
        NetworkProfile           : {
                                     "networkInterfaces": [
                                       {
                                         "properties.primary": null,
                                         "id": "/subscriptions/{subscription-id}/resourceGroups/
                                           rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                       }
                                     ]
                                   }
        OSProfile                : {
                                     "computerName": "vm1",
                                     "adminUsername": "myaccount1",
                                     "adminPassword": null,
                                     "customData": null,
                                     "windowsConfiguration": {
                                       "provisionVMAgent": true,
                                       "enableAutomaticUpdates": true,
                                       "timeZone": null,
                                       "additionalUnattendContents": [],
                                       "winRM": null
                                     },
                                     "linuxConfiguration": null,
                                     "secrets": []
                                   }
        Plan                     : null
        ProvisioningState        : Succeeded
        StorageProfile           : {
                                     "imageReference": {
                                       "publisher": "MicrosoftWindowsServer",
                                       "offer": "WindowsServer",
                                       "sku": "2012-R2-Datacenter",
                                       "version": "latest"
                                     },
                                     "osDisk": {
                                       "osType": "Windows",
                                       "encryptionSettings": null,
                                       "name": "osdisk",
                                       "vhd": {
                                         "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                       }
                                       "image": null,
                                       "caching": "ReadWrite",
                                       "createOption": "FromImage"
                                     }
                                     "dataDisks": [],
                                   }
        DataDiskNames            : {}
        NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                     rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## 가상 컴퓨터 시작

가상 컴퓨터를 시작합니다.

        Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

몇 분 후 다음과 같이 반환됩니다.

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## 가상 컴퓨터 중지

가상 컴퓨터를 중지합니다.

	    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

확인을 요청하는 메시지가 나타납니다.

        Virtual machine stopping operation
        This cmdlet will stop the specified virtual machine. Do you want to continue?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
        
가상 컴퓨터를 중지하려면 **Y**를 입력합니다.

몇 분 후 다음과 같이 반환됩니다.

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## 가상 컴퓨터 다시 시작

가상 컴퓨터를 다시 시작합니다.

        $rgName = "resource group name"
        $vmName = "VM name"
        Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

이때 반환되는 내용은 다음과 같습니다.

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## 가상 컴퓨터 삭제

가상 컴퓨터를 삭제합니다.

        $rgName = "resource group name"
        $vmName = "VM name"
	    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [AZURE.NOTE] **–Force** 매개 변수를 사용하여 확인 프롬프트를 건너뜁니다.

-Force 매개 변수를 사용하지 않은 경우 확인하라는 메시지가 나타납니다.

	    Virtual machine removal operation
	    This cmdlet will remove the specified virtual machine. Do you want to continue?
	    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

이때 반환되는 내용은 다음과 같습니다.

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## 가상 컴퓨터 업데이트

이 예제에서는 가상 컴퓨터 크기를 업데이트하는 방법을 보여 줍니다.
        
        $vmSize = "Standard_A1"
        $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
        $vm.HardwareProfile.vmSize = $vmSize
        Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
    See [Sizes for virtual machines in Azure](virtual-machines-windows-sizes.md) for a list of available sizes for a virtual machine.

이때 반환되는 내용은 다음과 같습니다.

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## 다음 단계

배포에 문제가 있는 경우 [Azure 포털을 사용하여 리소스 그룹 배포 문제 해결](../resource-manager-troubleshoot-deployments-portal.md)을 살펴보세요.

<!---HONumber=AcomDC_0420_2016-->