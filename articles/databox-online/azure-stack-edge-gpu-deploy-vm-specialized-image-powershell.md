---
title: Azure Stack Edge Pro GPU 디바이스의 특수화된 Windows VHD 이미지에서 VM 이미지 만들기
description: Windows VHD 또는 VHDX에서 시작하는 특수화된 이미지에서 VM 이미지를 만드는 방법을 설명합니다. 이 특수화된 이미지를 사용하여 Azure Stack Edge Pro GPU 디바이스에 배포된 VM에 사용할 VM 이미지를 만듭니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6bfa42e99f295b429eba40a27eb59becb8aa80a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575960"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Azure PowerShell을 통해 Azure Stack Edge Pro GPU 디바이스의 특수화된 이미지에서 VM 배포 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 특수화된 이미지에서 Azure Stack Edge Pro GPU 디바이스에 VM(가상 머신)을 배포하는 데 필요한 단계를 설명합니다. 

Azure Stack Edge Pro GPU에서 VM을 배포하기 위해 특수화된 이미지를 준비하려면 [Windows VHD에서 일반화된 이미지 준비](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) 또는 [ISO에서 일반화된 이미지 준비](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)를 참조하세요.

## <a name="about-vm-images"></a>VM 이미지 정보

Windows VHD 또는 VHDX를 사용하여 *특수화된* 이미지 또는 *일반화된* 이미지를 만들 수 있습니다. 다음 표에는 *특수화된* 이미지와 *일반화된* 이미지의 주요 차이점이 요약되어 있습니다.

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>워크플로

특수화된 이미지에서 VM을 배포하는 개략적인 워크플로는 다음과 같습니다.

1. Azure Stack Edge Pro GPU 디바이스의 로컬 스토리지 계정에 VHD를 복사합니다.
1. VHD에서 새 관리 디스크를 만듭니다.
1. 관리 디스크에서 새 가상 머신을 만들고 관리 디스크를 연결합니다.

## <a name="prerequisites"></a>필수 요건

PowerShell을 통해 디바이스에 VM을 배포하려면 먼저 다음을 확인합니다.

- 디바이스에 연결하는 데 사용할 클라이언트에 액세스할 수 있습니다.
    - 클라이언트가 [지원되는 OS](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 실행합니다.
    - 클라이언트가 [디바이스의 Azure Resource Manager에 연결](azure-stack-edge-gpu-connect-resource-manager.md) 지침에 따라 디바이스의 로컬 Azure Resource Manager에 연결하도록 구성되어 있습니다.

## <a name="verify-the-local-azure-resource-manager-connection"></a>로컬 Azure Resource Manager 연결 확인

클라이언트가 로컬 Azure Resource Manager에 연결할 수 있는지 확인합니다. 

1. 로컬 디바이스 API를 호출하여 인증합니다.

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Azure Resource Manager를 통해 연결할 사용자 이름 `EdgeArmUser` 및 암호를 제공합니다. 암호가 기억나지 않는 경우 [Azure Resource Manager의 암호를 재설정](azure-stack-edge-gpu-set-azure-resource-manager-password.md)하고 이 암호를 사용하여 로그인합니다.

## <a name="deploy-vm-from-specialized-image"></a>특수화된 이미지에서 VM 배포

다음 섹션에는 특수화된 이미지에서 VM을 배포하는 단계별 지침이 포함되어 있습니다.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>디바이스의 로컬 스토리지 계정에 VHD 복사

로컬 스토리지 계정에 VHD를 복사하려면 다음 단계를 수행합니다.

1. 원본 VHD를 Azure Stack Edge의 로컬 Blob Storage 계정에 복사합니다.

1. 결과 URI를 기록해 둡니다. 이후 단계에서 이 URI를 사용하게 됩니다.

    로컬 스토리지 계정을 만들고 액세스하려면 [Azure PowerShell을 통해 Azure Stack Edge 디바이스에서 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md) 문서에서 [VHD 업로드](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd)를 통해 [스토리지 계정 만들기](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) 섹션을 참조하세요. 

## <a name="create-a-managed-disk-from-vhd"></a>VHD에서 관리 디스크 만들기

이전에 스토리지 계정에 업로드한 VHD에서 관리 디스크를 만들려면 다음 단계를 수행합니다.

1. 일부 매개 변수를 설정합니다.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    다음은 예제 출력입니다.

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. 새 관리 디스크를 만듭니다.

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    다음은 예제 출력입니다. 여기서 위치는 로컬 스토리지 계정의 위치로 설정되며 Azure Stack Edge PRO GPU 디바이스의 모든 로컬 스토리지 계정에 대해 항상 `DBELocal`입니다. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>관리 디스크에서 VM 만들기

관리 디스크에서 VM을 만들려면 다음 단계를 수행합니다.

1. 일부 매개 변수를 설정합니다.

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > `PrivateIP` 매개 변수는 선택 사항입니다. 이 매개 변수를 사용하여 정적 IP를 할당합니다. 그렇지 않으면 기본값은 DHCP를 사용하는 동적 IP입니다.

    다음은 예제 출력입니다. 이 예제에서는 필요한 경우 리소스에 대해 별도의 리소스 그룹을 만들고 지정할 수 있지만 모든 VM 리소스에 대해 동일한 리소스 그룹이 지정됩니다.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. 가상 네트워크 정보를 얻고 새 네트워크 인터페이스를 만듭니다.

    이 샘플은 기본 리소스 그룹 `ASERG`와 연결된 기본 가상 네트워크 `ASEVNET`에 단일 네트워크 인터페이스를 만들고 있다고 가정합니다. 필요한 경우 대체 가상 네트워크를 지정하거나 여러 네트워크 인터페이스를 만들 수 있습니다. 자세한 내용은 [Azure Portal을 통해 VM에 네트워크 인터페이스 추가](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)를 참조하세요.

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    다음은 예제 출력입니다.

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. 새 VM 구성 개체를 만듭니다.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. VM에 네트워크 인터페이스를 추가합니다.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. VM에서 OS 디스크 속성을 설정합니다.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    이 명령의 마지막 플래그는 VM에 사용 중인 OS에 따라 `-Windows` 또는 `-Linux`입니다.

1. VM을 만듭니다.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    다음은 예제 출력입니다. 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>VM 및 리소스 삭제

이 문서에서는 하나의 리소스 그룹만 사용하여 모든 VM 리소스를 만들었습니다. 해당 리소스 그룹을 삭제하면 VM 및 연결된 모든 리소스가 삭제됩니다. 

1. 먼저 리소스 그룹 아래에 생성된 모든 리소스를 봅니다.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    다음은 예제 출력입니다.
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. 리소스 그룹 및 연결된 모든 리소스를 삭제합니다.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    다음은 예제 출력입니다.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. 리소스 그룹이 삭제되었는지 확인합니다. 디바이스에 있는 모든 리소스 그룹을 얻습니다. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    다음은 예제 출력입니다.

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro GPU에 VM을 배포하기 위해 Windows VHD에서 일반화된 이미지 준비](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Azure Stack Edge Pro GPU에 VM을 배포하기 위해 ISO에서 일반화된 이미지 준비](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)