---
title: "리소스 관리자 및 PowerShell을 사용하여 VM 관리 | Microsoft Docs"
description: "Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 48930854-7888-4e4c-9efb-7d1971d4cc14
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: b54a95c4f81d9a981912e1b596a817dc6ad56334
ms.openlocfilehash: 52684fe3212454abbfb0cf9d1c67759fce9a1549


---
# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>리소스 관리자 및 PowerShell을 사용하여 Azure 가상 컴퓨터 관리
## <a name="install-azure-powershell"></a>Azure Powershell 설치
최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

## <a name="set-variables"></a>변수 설정
이 문서의 모든 명령은 가상 컴퓨터가 있는 리소스 그룹 이름 및 관리할 가상 컴퓨터 이름이 필요합니다. **$rgName** 값을 가상 컴퓨터를 포함하는 리소스 그룹 이름으로 바꿉니다. **$vmName** 값을 VM 이름으로 바꿉니다. 변수를 만듭니다.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>가상 컴퓨터에 대한 정보 표시
가상 컴퓨터 정보를 가져옵니다.

    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

이때 반환되는 내용은 다음 예제와 같습니다.

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

## <a name="stop-a-virtual-machine"></a>가상 컴퓨터 중지
실행 중인 가상 컴퓨터를 중지합니다.

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

확인을 요청하는 메시지가 나타납니다.

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

가상 컴퓨터를 중지하려면 **Y** 를 입력합니다.

몇 분 후 다음 예제와 같이 반환됩니다.

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>가상 컴퓨터 시작
중지된 경우 가상 컴퓨터를 시작합니다.

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

몇 분 후 다음 예제와 같이 반환됩니다.

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

이미 실행 중인 가상 컴퓨터를 다시 시작하려는 경우 다음에 설명된 **Restart-AzureRmVM**을 사용합니다.

## <a name="restart-a-virtual-machine"></a>가상 컴퓨터 다시 시작
실행 중인 가상 컴퓨터를 다시 시작합니다.

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

이때 반환되는 내용은 다음 예제와 같습니다.

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>가상 컴퓨터 삭제
가상 컴퓨터를 삭제합니다.  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [!NOTE]
> **–Force** 매개 변수를 사용하여 확인 프롬프트를 건너뜁니다.
> 
> 

-Force 매개 변수를 사용하지 않은 경우 확인하라는 메시지가 나타납니다.

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

이때 반환되는 내용은 다음 예제와 같습니다.

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>가상 컴퓨터 업데이트
이 예제에서는 가상 컴퓨터 크기를 업데이트하는 방법을 보여 줍니다.

    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

이때 반환되는 내용은 다음 예제와 같습니다.

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

가상 컴퓨터에 사용 가능한 크기 목록은 [Azure에서 가상 컴퓨터에 대한 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 를 참조하세요.


## <a name="next-steps"></a>다음 단계
배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)을 살펴봅니다.




<!--HONumber=Feb17_HO2-->


