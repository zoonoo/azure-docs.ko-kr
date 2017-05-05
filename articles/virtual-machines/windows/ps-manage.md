---
title: "PowerShell을 사용하여 Azure VM 관리 | Microsoft 문서"
description: "PowerShell을 사용하여 Azure Virtual Machines를 관리합니다."
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
ms.date: 03/07/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: c8ed83c6c0219995030408f276d4ef7d83703c7c
ms.lasthandoff: 04/27/2017


---
# <a name="manage-azure-virtual-machines-using-powershell"></a>PowerShell을 사용하여 Azure Virtual Machines 관리

이 문서에서는 Azure 가상 컴퓨터에서 수행할 수 있는 일반적인 관리 작업을 보여 줍니다.

최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

> [!NOTE]
> 이 문서에서 기능을 사용하려면 Azure PowerShell을 다시 설치해야 합니다. Managed Disks 기능은 버전 3.5 이상에 있습니다.
> 
> 

명령을 더 쉽게 실행할 수 있도록 이러한 변수를 만들고 환경에 맞게 값을 변경합니다.
    
```powershell
$myResourceGroup = "myResourceGroup"
$myVM = "myVM"
$location = "centralus"
```

## <a name="display-information-about-a-virtual-machine"></a>가상 컴퓨터에 대한 정보 표시

가상 컴퓨터에 대한 정보를 가져옵니다.

```powershell
Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM -DisplayHint Expand
```

이때 반환되는 내용은 다음 예제와 같습니다.

    ResourceGroupName             : myResourceGroup
    Id                            : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
    VmId                          : #########-####-####-####-############
    Name                          : myVM
    Type                          : Microsoft.Compute/virtualMachines
    Location                      : centralus
    Tags                          : {}
    DiagnosticsProfile            :
      BootDiagnostics             :
      Enabled                     : True
      StorageUri                  : https://mystorage1.blob.core.windows.net/
    AvailabilitySetReference      : 
      Id                          : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAV1
    Extensions[0]                 :
      Id                          : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Compute/
                                    virtualMachines/myVM/extensions/BGInfo
      Name                        : BGInfo
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Compute
      VirtualMachineExtensionType : BGInfo
      TypeHandlerVersion          : 2.1
      AutoUpgradeMinorVersion     : True
      ProvisioningState           : Succeeded
    HardwareProfile               : 
      VmSize                      : Standard_DS1_v2
    NetworkProfile          
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
    OSProfile                     : 
      ComputerName                : myVM
      AdminUsername               : myaccount1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
         EnableAutomaticUpdates   : True
    ProvisioningState             : Succeeded
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : latest   
      OsDisk                      :
        OsType                    : Windows
        Name                      : myosdisk
        Vhd                       : 
          Uri                     : http://mystore1.blob.core.windows.net/vhds/myosdisk.vhd
        Caching                   : ReadWrite
        CreateOption              : FromImage
    NetworkInterfaceIDs[0]        : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC

## <a name="stop-a-virtual-machine"></a>가상 컴퓨터 중지

두 가지 방법으로 가상 컴퓨터를 중지할 수 있습니다. 가상 컴퓨터를 중지하고 해당 설정을 모두 그대로 유지하면 계속 요금이 청구될 수 있습니다. 그렇지 않으려면 가상 컴퓨터를 중지하고 할당을 해제합니다. 가상 컴퓨터를 할당을 해제하면 연결된 모든 리소스의 할당이 취소되고 대금 청구가 끝납니다.

### <a name="stop-and-deallocate"></a>중지 및 할당 취소
    
```powershell
Stop-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
```

확인을 요청하는 메시지가 나타납니다.

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"):
 
    Enter **Y** to stop the virtual machine.

몇 분 후 다음 예제와 같이 반환됩니다.

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:11:57 PM
    EndTime     : 9/13/2016 12:14:40 PM
    Error       :

### <a name="stop-and-stay-provisioned"></a>중지 및 프로비전된 상태 유지

```powershell
Stop-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM -StayProvisioned
```

확인을 요청하는 메시지가 나타납니다.

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"):
 
가상 컴퓨터를 중지하려면 **Y** 를 입력합니다.

몇 분 후 다음 예제와 같이 반환됩니다.

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:11:57 PM
    EndTime     : 9/13/2016 12:14:40 PM
    Error       :

## <a name="start-a-virtual-machine"></a>가상 컴퓨터 시작
 
중지된 경우 가상 컴퓨터를 시작합니다.

```powershell
Start-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
```

몇 분 후 다음 예제와 같이 반환됩니다.

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:32:55 PM
    EndTime     : 9/13/2016 12:35:09 PM
    Error       :

이미 실행 중인 가상 컴퓨터를 다시 시작하려는 경우 다음에 설명된 **Restart-AzureRmVM**을 사용합니다.

## <a name="restart-a-virtual-machine"></a>가상 컴퓨터 다시 시작

실행 중인 가상 컴퓨터를 다시 시작합니다.

```powershell
Restart-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
```

이때 반환되는 내용은 다음 예제와 같습니다.

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:54:40 PM
    EndTime     : 9/13/2016 12:55:54 PM
    Error       :    

## <a name="add-a-data-disk-to-a-virtual-machine"></a>데이터 디스크를 가상 컴퓨터에 추가
    
### <a name="managed-data-disk"></a>관리되는 데이터 디스크

```powershell
$diskConfig = New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk = New-AzureRmDisk -DiskName "myDataDisk1" -Disk $diskConfig -ResourceGroupName $myResourceGroup
$vm = Get-AzureRmVM -Name $myVM -ResourceGroupName $myResourceGroup
Add-AzureRmVMDataDisk -VM $vm -Name "myDataDisk1" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
Update-AzureRmVM -ResourceGroupName $myResourceGroup -VM $vm
```

Add-AzureRmVMDataDisk를 실행한 후 다음 예제와 같이 표시됩니다.

    ResourceGroupName        : myResourceGroup
    Id                       : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Compute/virtualMachines/myVM
    VmId                     : ########-####-####-####-############
    Name                     : myVM
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {Id}
    DiagnosticsProfile       : {BootDiagnostics}
    HardwareProfile          : {VmSize}
    NetworkProfile           : {NetworkInterfaces}
    OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
    ProvisioningState        : Succeeded
    StorageProfile           : {ImageReference, OsDisk, DataDisks}
    DataDiskNames            : {myDataDisk1}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Network/networkInterfaces/myNIC}

Update-AzureRmVM을 실행한 후 다음 예제와 같이 표시됩니다.

    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK

### <a name="unmanaged-data-disk"></a>관리되지 않는 데이터 디스크

```powershell
$vm = Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
Add-AzureRmVMDataDisk -VM $vm -Name "myDataDisk1" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
Update-AzureRmVM -ResourceGroupName $myResourceGroup -VM $vm
```

Add-AzureRmVMDataDisk를 실행한 후 다음 예제와 같이 표시됩니다.

    ResourceGroupName        : myResourceGroup
    Id                       : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Compute/virtualMachines/myVM
    VmId                     : ########-####-####-####-############
    Name                     : myVM
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {Id}
    DiagnosticsProfile       : {BootDiagnostics}
    HardwareProfile          : {VmSize}
    NetworkProfile           : {NetworkInterfaces}
    OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
    ProvisioningState        : Succeeded
    StorageProfile           : {ImageReference, OsDisk, DataDisks}
    DataDiskNames            : {myDataDisk1}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Network/networkInterfaces/myNIC}

Update-AzureRmVM을 실행한 후 다음 예제와 같이 표시됩니다.

    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK

## <a name="update-a-virtual-machine"></a>가상 컴퓨터 업데이트

이 예제에서는 가상 컴퓨터 크기를 업데이트하는 방법을 보여 줍니다.

```powershell
$vm = Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
$vm.HardwareProfile.vmSize = "Standard_DS2_v2"
Update-AzureRmVM -ResourceGroupName $myResourceGroup -VM $vm
```

이때 반환되는 내용은 다음 예제와 같습니다.

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

가상 컴퓨터에 사용 가능한 크기 목록은 [Azure에서 가상 컴퓨터에 대한 크기](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 를 참조하세요.

## <a name="delete-a-virtual-machine"></a>가상 컴퓨터 삭제

가상 컴퓨터를 삭제합니다.  

```powershell
Remove-AzureRmVM -ResourceGroupName $myResourceGroup –Name $myVM
```

> [!NOTE]
> **–Force** 매개 변수를 사용하여 확인 프롬프트를 건너뜁니다.
> 
> 

-Force 매개 변수를 사용하지 않은 경우 확인하라는 메시지가 나타납니다.

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"):

이때 반환되는 내용은 다음 예제와 같습니다.

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="next-steps"></a>다음 단계

- 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../../resource-manager-common-deployment-errors.md)을 살펴봅니다.
- Azure PowerShell을 사용하여 [Resource Manager 및 PowerShell을 사용하여 Windows VM 만들기](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 가상 컴퓨터를 만드는 방법을 알아봅니다.
- [Resource Manager 템플릿을 사용하여 Windows 가상 컴퓨터 만들기](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

