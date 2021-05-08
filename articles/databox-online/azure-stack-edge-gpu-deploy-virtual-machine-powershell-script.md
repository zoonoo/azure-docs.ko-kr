---
title: Azure PowerShell 스크립트를 통해 Azure Stack Edge Pro GPU 디바이스에 VM 배포
description: Azure PowerShell 스크립트를 사용하여 Azure Stack Edge Pro 디바이스에서 VM(가상 머신)을 만들고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 36c7078a79cf8b0b7414c5031acb79b9a2c2453c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630489"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell-script"></a>Azure PowerShell 스크립트를 통해 Azure Stack Edge Pro GPU 디바이스에 VM 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 자습서에서는 Azure PowerShell 스크립트를 사용하여 Azure Stack Edge Pro 디바이스에서 VM을 만들고 관리하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 스크립트를 사용하여 Azure Stack Edge Pro 디바이스에서 VM을 만들고 관리하기 전에, 다음 단계에 나와 있는 사전 요구 사항을 완료했는지 확인해야 합니다.

### <a name="for-azure-stack-edge-pro-device-via-the-local-web-ui"></a>로컬 웹 UI를 통한 Azure Stack Edge Pro 디바이스의 경우

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

### <a name="for-your-windows-client"></a>Windows 클라이언트의 경우

1. 다음을 수정했는지 확인합니다. 

    - 클라이언트의 호스트 파일 또는
    - DNS 서버 구성
    
    > [!IMPORTANT]
    > 엔드포인트 이름 확인을 위해 DNS 서버 구성을 수정하는 것이 좋습니다.

    1. **메모장** 을 관리자(파일을 저장하려면 관리자 권한이 필요함) 권한으로 시작한 다음, `C:\Windows\System32\Drivers\etc`에 있는 **hosts** 파일을 엽니다.
    
        ![Windows 탐색기 호스트 파일](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)
    
    2. 다음 항목을 디바이스에 적합한 값으로 대체하고 **hosts** 파일에 추가합니다.
    
        ```
        <device IP> login.<appliance name>.<DNS domain>
        <device IP> management.<appliance name>.<DNS domain>
        <device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        스토리지 계정의 경우, 스크립트에서 나중에 새 스토리지 계정을 만드는 데 사용할 이름을 제공할 수 있습니다. 스크립트에서는 해당 스토리지 계정이 존재하는지 여부를 확인하지 않습니다.

    3. 다음 이미지를 참조하세요. **호스트** 파일을 저장합니다.

        ![메모장의 hosts 파일](media/azure-stack-edge-gpu-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. 이 프로시저에 사용된 [PowerShell 스크립트를 다운로드](https://aka.ms/ase-vm-powershell)합니다.

3. Windows 클라이언트에서 PowerShell 5.0 이상을 실행하고 있는지 확인합니다.

4. `Azure.Storage Module version 4.5.0`이 시스템에 설치되어 있는지 확인합니다. [PowerShell 갤러리](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0)에서 이 모듈을 가져올 수 있습니다. 이 모듈을 설치하려면 다음을 입력합니다.

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    설치된 모듈 버전을 확인하려면 다음을 입력합니다.

    `Get-InstalledModule -name Azure.Storage`

    다른 버전 모듈을 제거하려면 다음을 입력합니다.

    `Uninstall-Module -Name Azure.Storage`

5. Windows 클라이언트에 [AzCopy 10을 다운로드](../storage/common/storage-use-azcopy-v10.md#download-azcopy)합니다. 스크립트를 실행하는 동안 매개 변수로 이를 전달하므로 이 위치를 기록해 둡니다.

6. Windows 클라이언트에서 TLS 1.2 이상을 실행하고 있는지 확인합니다.


## <a name="create-a-vm"></a>VM 만들기

1. PowerShell을 관리자 권한으로 실행합니다.
1. 클라이언트에 스크립트를 다운로드한 폴더로 이동합니다.
1. 스크립트를 실행하기 전에 디바이스의 로컬 Azure Resource Manager에 연결되어 있으며 연결이 만료되지 않았는지 확인합니다.

    ```powershell
    PS C:\windows\system32> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d

    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1

    PS C:\windows\system32> cd C:\Users\v2
    PS C:\Users\v2>
    ```  
1. 다음 명령을 사용하여 스크립트를 실행합니다.
 
    `.\ArmPowershellClient.ps1 -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <Username to be used to sign in to VM> -VMPassword <Password for the VM> --AzCopy10Path <Absolute Path>`

    IP를 VM에 동적으로 할당하려면 `-NicPrivateIp` 매개 변수를 생략합니다.

    Windows VM 및 Linux VM을 만들기 위해 스크립트를 실행하는 경우의 예는 다음과 같습니다.

    **Windows VM의 경우:**

    다음은 생성된 Windows VM에 대한 샘플 출력입니다.

    ```powershell
    PS C:\Users\v2> .\ArmPowershellClient.ps1 -VHDPath \\asefs\Logs\vmvhd -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName myasesatest -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\Users\AzCopy10\AzCopy.exe
    New-AzureRmResourceGroup -Name rg201221071831 -Location DBELocal -Force
    Successfully created Resource Group:rg201221071831
    Successfully created Resource Group:StorAccRG
    Get-AzureRmStorageAccount -Name myasesatest -ResourceGroupName StorAccRG -ErrorAction SilentlyContinue
    New-AzureRmStorageAccount -Name myasesatest  -ResourceGroupName StorAccRG -SkuName Standard_LRS -Location DBELocal
    
    Created New Storage Account
    Get-AzureRmStorageAccount -name myasesatest -resourcegroupname
    StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
    ------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
    myasesatest        StorAccRG         DBELocal StandardLRS Storage            12/22/2020 3:18:38 AM Succeeded         False
    myasesatest        StorAccRG         DBELocal StandardLRS Storage            12/22/2020 3:18:38 AM Succeeded         False
    
     Uploading Vhd to Storage Account
    
    New-AzureStorageContext -StorageAccountName myasesatest -StorageAccountKey hyibjhbVlOROgTlU1nQJIlxrg94eGDhF+RIQ71Z7UVZIxoOPMlHP274NUhZtA1hMxGBcpk2BVApiFasFPEhY/A== -Endpoint https://myasesatest.blob.myasegpuvm.wdshcsso.com/
    
    New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission
    
    SAS Token : ?sv=2017-07-29&sig=TXaGbjum9tFFaJnu3SFmDuslJuqNiNQwvuHfpPJMYN0%3D&spr=https&se=2020-12-22T04%3A18%3A43Z&srt=sco&ss=bfqt&sp=racwdl
    
     C:\Users\AzCopy10\AzCopy.exe make https://myasesatest.blob.myasegpuvm.wdshcsso.com/vmimages?sv=2017-07-29&sig=TXaGbjum9tFFaJnu3SFmDuslJuqNiNQwvuHfpPJMYN0%3D&spr=https&se=2020-12-22T04%3A18%3A43Z&srt=sco&ss=bfqt&sp=racwdl
    
    Successfully created the resource.
    
     AzCopy cp \\asefs\Logs\vmvhd\WindowsServer2016Datacenter.vhd https://myasesatest.blob.myasegpuvm.wdshcsso.com/vmimages?sv=2017-07-29&sig=TXaGbjum9tFFaJnu3SFmDuslJuqNiNQwvuHfpPJMYN0%3D&spr=https&se=2020-12-22T04%3A18%3A43Z&srt=sco&ss=bfqt&sp=racwdl
    
    INFO: Scanning...
    
    Job b6f54665-93c4-2f47-4770-5f3b7b0de2dc has started
    Log file is located at: C:\Users\Administrator\.azcopy\b6f54665-93c4-2f47-4770-5f3b7b0de2dc.log
    
    INFO: AzCopy.exe: A newer version 10.8.0 is available to download
    
    99.9 %, 0 Done, 0 Failed, 1 Pending, 0 Skipped, 1 Total,  (Disk may be limiting speed)
    
    Job b6f54665-93c4-2f47-4770-5f3b7b0de2dc summary
    Elapsed Time (Minutes): 12.7717
    Total Number Of Transfers: 1
    Number of Transfers Completed: 1
    Number of Transfers Failed: 0
    Number of Transfers Skipped: 0
    TotalBytesTransferred: 13958644224
    Final Job Status: Completed
    
    VHD Upload Done
    
     Creating a new managed disk
    
      = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri
    
     Microsoft.Azure.Commands.Compute.Automation.Models.PSDisk
    
     New-AzureRmDisk -ResourceGroupName rg201221071831 -DiskName ld201221071831 -Disk
    
    ResourceGroupName  : rg201221071831
    ManagedBy          :
    Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
    Zones              :
    TimeCreated        : 12/21/2020 7:31:35 PM
    OsType             :
    CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
    DiskSizeGB         : 13
    EncryptionSettings :
    ProvisioningState  : Succeeded
    Id                 : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Compute/disks/ld201221071831
    Name               : ld201221071831
    Type               : Microsoft.Compute/disks
    Location           : DBELocal
    Tags               : {}
    
     Created a new managed disk
    
     Creating a new Image out of managed disk
    
    ResourceGroupName    :
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    :
    Id                   :
    Name                 :
    Type                 :
    Location             : DBELocal
    Tags                 :
    
     New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig201221071831  -ResourceGroupName rg201221071831
    
    ResourceGroupName    : rg201221071831
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    : Succeeded
    Id                   : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Compute/images/ig201221071831
    Name                 : ig201221071831
    Type                 : Microsoft.Compute/images
    Location             : dbelocal
    Tags                 : {}
    
     Created a new Image
    
     Using Vnet /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET
    
     Creating a new Newtork Interface
    WARNING: The output object type of this cmdlet will be modified in a future release.
    
    VirtualMachine              :
    IpConfigurations            : {ip201221071831}
    DnsSettings                 : Microsoft.Azure.Commands.Network.Models.PSNetworkInterfaceDnsSettings
    MacAddress                  : 001DD87D7216
    Primary                     :
    EnableAcceleratedNetworking : False
    EnableIPForwarding          : False
    NetworkSecurityGroup        :
    ProvisioningState           : Succeeded
    VirtualMachineText          : null
    IpConfigurationsText        : [
                                    {
                                      "Name": "ip201221071831",
                                      "Etag": "W/\"27785dd5-d12a-4d73-9495-ffad7847261a\"",
                                      "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Network/networkInterfaces/nic201221071831/ipConfigurations/ip201221071831",
                                      "PrivateIpAddress": "10.57.51.61",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ResourceNavigationLinks": [],
                                        "ServiceEndpoints": []
                                      },
                                      "ProvisioningState": "Succeeded",
                                      "PrivateIpAddressVersion": "IPv4",
                                      "LoadBalancerBackendAddressPools": [],
                                      "LoadBalancerInboundNatRules": [],
                                      "Primary": true,
                                      "ApplicationGatewayBackendAddressPools": [],
                                      "ApplicationSecurityGroups": []
                                    }
                                  ]
    DnsSettingsText             : {
                                    "DnsServers": [],
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "qgotb4hjdh4efnhn0vz5adtb3f.a--x.internal.cloudapp.net"
                                  }
    NetworkSecurityGroupText    : null
    ResourceGroupName           : rg201221071831
    Location                    : dbelocal
    ResourceGuid                : e6327ab9-0855-4f04-9b36-17bbf31b5bd8
    Type                        : Microsoft.Network/networkInterfaces
    Tag                         :
    TagsTable                   :
    Name                        : nic201221071831
    Etag                        : W/"27785dd5-d12a-4d73-9495-ffad7847261a"
    Id                          : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Network/networkInterfaces/nic201221071831
    
     Created Network Interface
    
     Creating a new VM
    
     New-AzureRmVMConfig -VMName VM201221071831 -VMSize Standard_D1_v2
    
     Set-AzureRmVMOperatingSystem -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Windows -ComputerName COM201221071831 -Credential System.Management.Automation.PSCredential
    
     Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine = Set-AzureRmVMOSDisk -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Name osld201221071831 -Caching ReadWrite -CreateOption FromImage -Windows -StorageAccountType StandardLRS
    
     Add-AzureRmVMNetworkInterface -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Id /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Network/networkInterfaces/nic201221071831.Id
    
     Set-AzureRmVMSourceImage -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Id /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Compute/images/ig201221071831
    
     New-AzureRmVM -ResourceGroupName rg201221071831 -Location DBELocal -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Verbose
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasesa1, is used for boot
    diagnostics.
    VERBOSE: Performing the operation "New" on target "VM201221071831".
    
    Ticks             : 1533424841
    Days              : 0
    Hours             : 0
    Milliseconds      : 342
    Minutes           : 2
    Seconds           : 33
    TotalDays         : 0.00177479726967593
    TotalHours        : 0.0425951344722222
    TotalMilliseconds : 153342.4841
    TotalMinutes      : 2.55570806833333
    TotalSeconds      : 153.3424841
    
    RequestId           :
    IsSuccessStatusCode : True
    StatusCode          : OK
    ReasonPhrase        : OK

    PS C:\Users\v2>
    ```

    **Linux VM의 경우:**

    Linux VM을 만드는 데 사용된 명령 샘플은 다음과 같습니다.

    ```powershell
    .\ArmPowershellClient.ps1 -VHDPath \\asefs\Logs\vmvhd -VHDFile ubuntu13.vhd -StorageAccountName myasesatest -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\Users\AzCopy10\AzCopy.exe
    New-AzureRmResourceGroup -Name rg201221075546 -Location DBELocal -Force    
    ```

  
1. VM이 성공적으로 생성되었으면 해당 VM이 Azure Portal의 가상 머신 목록에 표시되어야 합니다. Azure Portal 내 디바이스의 Azure Stack Edge 리소스에서 VM을 보려면 **에지 서비스 > 가상 머신** 로 이동합니다. 

    ![가상 머신 목록 보기](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell-script/list-virtual-machine-1.png)

    VM 세부 정보를 보려면 VM 이름을 선택합니다. 이 VM에 대한 IP 동적 할당을 확인합니다.

    ![VM 세부 정보 보기](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell-script/view-virtual-machine-details-1.png)

1. 스크립트에서 만든 리소스를 정리하려면 다음 명령을 사용합니다.
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmImage | Remove-AzureRmImage -Force
    Get-AzureRmDisk | Remove-AzureRmDisk -Force
    Get-AzureRmStorageAccount | Remove-AzureRmStorageAccount -Force
    ```


## <a name="next-steps"></a>다음 단계

[Azure PowerShell cmdlets를 사용한 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)