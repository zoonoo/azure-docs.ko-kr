---
title: Azure Stack에서 VM 디스크 관리 | Microsoft Docs
description: Azure Stack에서 virtual machines에 대 한 디스크를 프로 비전 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2018
ms.author: mabrigg
ms.reviewer: jiahan
ms.openlocfilehash: fc17ce0ebd13fb7e89405fcf4d6633551f340a27
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139291"
---
# <a name="provision-virtual-machine-disk-storage-in-azure-stack"></a>Azure Stack에서 virtual machine 디스크 저장소를 프로 비전

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

이 문서에서는 Azure Stack 포털 또는 PowerShell을 사용 하 여 가상 머신 디스크 저장소를 프로 비전 하는 방법을 설명 합니다.

## <a name="overview"></a>개요

Azure Stack의 사용을 지원 [관리 되지 않는 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) 운영 체제 (OS) 및 데이터 디스크를 가상 머신에서 합니다.

만든 관리 되지 않는 디스크를 사용 하는 [저장소 계정](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) 디스크를 저장 합니다. 만든 디스크는 VM 디스크 라고도 하며 저장소 계정의 컨테이너에 저장 됩니다.

### <a name="best-practice-guidelines"></a>모범 사례 지침

성능을 개선 하 고 전체 비용을 절감, 별도 컨테이너에서 각 VM 디스크를 배치 하는 것이 좋습니다. 컨테이너 OS 디스크 또는 데이터 디스크 작업 중 하나만 동시에 하나만 포함 해야 합니다. 그러나 (없는 동일한 컨테이너에 두 가지 유형의 디스크를 모두 배치 하지 않도록 하려면 됩니다.)

VM에 하나 이상의 데이터 디스크를 추가 하는 경우 추가 컨테이너를 사용 하는 위치와 이러한 디스크를 저장 합니다. 추가 Vm의 OS 디스크에서 고유한 컨테이너에 있어야 합니다.

여러 Vm을 만들 때 각 새 가상 머신에 대해 동일한 저장소 계정을 재사용할 수 있습니다. 만든 컨테이너에만 고유 해야 합니다.

### <a name="adding-new-disks"></a>새 디스크 추가

다음 표에서 포털을 사용 하 고 PowerShell을 사용 하 여 디스크를 추가 하는 방법을 보여 줍니다.

| 방법 | 옵션
|-|-|
|[사용자 포털](#use-the-portal-to-add-additional-disks-to-a-vm)|-기존 VM에 새 데이터 디스크를 추가 합니다. 새 디스크는 Azure Stack에서 생성 됩니다. </br> </br>-이전에 프로 비전 된 VM에 기존 디스크 (.vhd) 파일을 추가 합니다. 이렇게 하려면.vhd를 준비한 다음 Azure Stack에 파일을 업로드 합니다. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -OS 디스크를 사용 하 여 새 VM 만들기 및 동시에 해당 VM에 하나 이상의 데이터 디스크를 추가 합니다. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Portal을 사용 하 여 VM에 디스크 추가

기본적으로 대부분의 marketplace 항목에 대 한 VM을 만들려면 포털을 사용 하는 경우 OS 디스크에만 만들어집니다.

VM을 만든 후 포털을 사용할 수 있습니다.
* 새 데이터 디스크를 만들고 VM에 연결 합니다.
* 기존 데이터 디스크를 업로드 하 고 VM에 연결 합니다.

별도 컨테이너에서 추가한 각 관리 되지 않는 디스크를 배치 해야 합니다.

>[!NOTE]
>Azure에서 만들고 관리 디스크 라고 [관리 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)합니다.

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>포털을 사용 하 여 만들고 새 데이터 디스크를 연결 하려면

1.  포털에서 선택 **가상 머신**합니다.    
    ![예: VM 대시보드](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  이전에 프로 비전 된 가상 컴퓨터를 선택 합니다.   
    ![대시보드에서 VM을 선택 하는 다음과 같습니다.](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  가상 컴퓨터를 선택 **Disks** > **새 연결**합니다.       
    ![예: vm에 새 디스크 연결](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  에 **새 디스크 연결** 창 **위치**합니다. 기본적으로 위치는 OS 디스크를 보유 하는 동일한 컨테이너에 설정 됩니다.      
    ![예: 디스크 위치를 설정 합니다.](media/azure-stack-manage-vm-disks/disk-location.png)

5.  선택 된 **저장소 계정** 사용 하도록 합니다. 다음을 선택 합니다 **컨테이너** 데이터 디스크를 배치 하려는 위치. **컨테이너** 페이지를 원하는 경우 새 컨테이너를을 만들 수 있습니다. 그런 다음 고유한 컨테이너에 새 디스크의 위치를 변경할 수 있습니다. 각 디스크에 대 한 별도 컨테이너를 사용 하는 경우 성능을 향상 시킬 수 있는 데이터 디스크의 배치를 배포 합니다. 선택할 **선택** 하 여 선택 내용을 저장 합니다.     
    ![예: 컨테이너를 선택 합니다.](media/azure-stack-manage-vm-disks/select-container.png)

6.  에 **새 디스크 연결** 페이지에서 업데이트를 **이름**를 **형식**를 **크기**, 및 **호스트 캐싱** 설정 디스크. 선택한 **확인** VM에 대 한 새 디스크 구성을 저장 합니다.  
    ![예: 전체 디스크 연결](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Azure Stack 디스크를 만들고이 가상 컴퓨터에 연결을 새 디스크에서 가상 머신의 디스크 설정에 나열 됩니다 **데이터 디스크**합니다.   
    ![예: 디스크 보기](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>VM에 기존 데이터 디스크 추가

1.  [.Vhd 파일을 준비](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) VM에 대 한 데이터 디스크로 사용 합니다. .Vhd 파일을 연결 하려는 VM과 함께 사용 하는 저장소 계정에 해당.vhd 파일을 업로드 합니다.

  OS 디스크를 포함 하는 컨테이너 보다.vhd 파일을 저장할 다른 컨테이너를 사용 하도록 계획 합니다.   
  ![예: VHD 파일 업로드](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  .Vhd 파일을 업로드 한 후 VM에 VHD를 연결할 수 있습니다. 왼쪽 메뉴에서 선택 **가상 머신**합니다.  
 ![대시보드에서 VM을 선택 하는 다음과 같습니다.](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  목록에서 가상 컴퓨터를 선택 합니다.    
  ![대시보드에서 VM을 선택 하는 다음과 같습니다.](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  가상 컴퓨터에 대 한 페이지에서 선택 **Disks** > **기존 연결**합니다.   
  ![예: 기존 디스크 연결](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  에 **기존 디스크 연결** 페이지에서 **VHD 파일**합니다. 합니다 **저장소 계정** 페이지가 열립니다.    
  ![VHD 파일을 선택 하는 다음과 같습니다.](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  아래 **저장소 계정**를 사용 하려면 계정을 선택 하 고 이전에 업로드 한.vhd 파일을 보관 하는 컨테이너를 선택 합니다. .Vhd 파일을 선택 하 고 선택한 **선택** 하 여 선택 내용을 저장 합니다.    
  ![예: 컨테이너를 선택 합니다.](media/azure-stack-manage-vm-disks/select-container2.png)

7.  아래 **기존 디스크 연결**, 선택한 파일 아래에 나열 됩니다 **VHD 파일**합니다. 업데이트를 **호스트 캐싱** 디스크의 설정을 선택한 후 **확인** VM에 대 한 새 디스크 구성을 저장 합니다.    
  ![VHD 파일을 첨부 하는 다음과 같습니다.](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Azure Stack 디스크를 만들고이 가상 컴퓨터에 연결을 새 디스크에서 가상 머신의 디스크 설정에 나열 됩니다 **데이터 디스크**합니다.   
  ![예: 완료 디스크 연결](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>PowerShell을 사용 하 여 VM에 여러 관리 되지 않는 디스크를 추가 하려면
PowerShell을 사용 하 여 VM을 프로 비전 하 고 새 데이터 디스크를 추가 하거나 기존 연결 **.vhd** 파일을 데이터 디스크로 합니다.

합니다 **Add-azurermvmdatadisk** cmdlet은 가상 머신에 데이터 디스크를 추가 합니다. 가상 컴퓨터를 만들거나 기존 가상 컴퓨터에 데이터 디스크를 추가할 수 있습니다 하는 경우 데이터 디스크를 추가할 수 있습니다. 지정 된 **VhdUri** 매개 변수를 다른 컨테이너에 디스크를 배포 합니다.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>새 가상 머신에 데이터 디스크를 추가 합니다.
다음 예제에서는 세 개의 데이터 디스크를 사용 하 여 VM을 만들려면 PowerShell 명령을 사용, 각각 서로 다른 컨테이너에 배치 합니다.

첫 번째 명령은 가상 컴퓨터 개체를 만들고 저장 합니다 *$VirtualMachine* 변수입니다. 명령이 가상 컴퓨터에 이름 및 크기를 할당합니다.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

다음의 세 명령은 세 개의 데이터 디스크의 경로 할당 합니다 *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, 및 *$DataDiskVhdUri03* 변수. 디스크를 다른 컨테이너를 배포 하려면 URL에 다른 경로 이름을 정의 합니다.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

에 저장 된 가상 머신에 데이터 디스크를 추가 하는 마지막 3 개 명령을 *$VirtualMachine*합니다. 각 명령 이름, 위치 및 디스크의 추가 속성을 지정합니다. 각 디스크의 URI에 저장 됩니다 *$DataDiskVhdUri01*를 *$DataDiskVhdUri02*, 및 *$DataDiskVhdUri03*합니다.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

다음 PowerShell 명령을 사용 하 여 VM에 OS 디스크 및 네트워크 구성을 추가 하 고 새 VM을 시작 합니다.
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>기존 가상 머신에 데이터 디스크를 추가 합니다.
다음 예제에서는 기존 VM에 3 개의 데이터 디스크를 추가 하려면 PowerShell 명령을 사용 합니다.
첫 번째 명령은 VirtualMachine를 사용 하 여 명명 된 가상 컴퓨터를 **Get-azurermvm** cmdlet. 이 명령은 가상 머신을 *$VirtualMachine* 변수에 저장합니다.
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
다음의 세 명령은 $DataDiskVhdUri01, $DataDiskVhdUri02, 및 $DataDiskVhdUri03 변수에 세 개의 데이터 디스크의 경로 할당합니다.  vhduri에서 서로 다른 경로 이름을 디스크 배치에 대 한 다른 컨테이너를 나타냅니다.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  다음의 세 명령은에 저장 된 가상 머신에 데이터 디스크를 추가 합니다 *$VirtualMachine* 변수입니다. 각 명령 이름, 위치 및 디스크의 추가 속성을 지정합니다. 각 디스크의 URI에 저장 됩니다 *$DataDiskVhdUri01*를 *$DataDiskVhdUri02*, 및 *$DataDiskVhdUri03*합니다.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  마지막 명령은 가상 컴퓨터에 저장 된 상태를 업데이트 *$VirtualMachine* 에서*ResourceGroupName*합니다.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>다음 단계
Azure Stack virtual machines에 대 한 자세한 내용은 참조 하세요 [Azure Stack에서 Virtual Machines에 대 한 고려 사항](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations)합니다.
