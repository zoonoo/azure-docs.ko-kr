---
title: Azure PowerShell을 통해 Azure Stack Edge 디바이스에 VM 배포
description: Azure PowerShell을 사용하여 Azure Stack Edge 디바이스에서 가상 머신을 만들고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/13/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8dd0d7cf63e736ec4854223bd519573ac6cab4c8
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110698605"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Azure PowerShell을 통해 Azure Stack Edge 디바이스에 VM 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure PowerShell을 사용하여 Azure Stack Edge 디바이스에서 VM(가상 머신)을 만들고 관리하는 방법을 설명합니다. 이 정보는 GPU(그래픽 처리 디바이스)가 있는 Azure Stack Edge Pro, Azure Stack Edge Pro R 및 Azure Stack Edge Mini R 디바이스에 적용됩니다.

## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

다음 다이어그램에 배포 워크플로가 나와 있습니다.

![VM 배포 워크플로의 다이어그램.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>디바이스의 기본 제공 구독 쿼리

Azure Resource Manager의 경우 사용자에게 표시되는 단일 고정 구독만 지원됩니다. 이 구독은 디바이스별로 고유하며 구독 이름과 구독 ID는 변경할 수 없습니다.

구독에는 VM 생성에 필요한 모든 리소스가 포함됩니다. 

> [!IMPORTANT]
> 구독은 Azure Portal에서 VM을 사용하도록 설정할 때 만들어지며 디바이스에서 로컬로 유지됩니다.

구독은 VM을 배포하는 데 사용됩니다.

1.  구독을 나열하려면 다음 명령을 실행합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
    
    다음은 몇 가지 출력 예입니다.

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. 디바이스에서 실행 중인 등록된 리소스 공급자 목록을 가져옵니다. 이 목록에는 일반적으로 컴퓨팅, 네트워크 및 스토리지가 포함됩니다.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > 리소스 공급자는 사전 등록되어 있으며 수정하거나 변경할 수 없습니다.
    
    다음은 몇 가지 출력 예입니다.

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 스토리지 계정, 디스크 및 관리 디스크와 같은 Azure 리소스가 배포되고 관리되는 논리적 컨테이너입니다.

> [!IMPORTANT]
> 모든 리소스는 디바이스와 동일한 위치에 만들어지며 위치는 **DBELocal** 로 설정됩니다.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

다음은 몇 가지 출력 예입니다.

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

이전 단계에서 만든 리소스 그룹을 사용하여 새 스토리지 계정을 만듭니다. VM에 대한 가상 디스크 이미지를 업로드하는 데 사용하는 로컬 스토리지 계정입니다.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Azure Resource Manager를 사용하면 로컬 중복 스토리지(표준 또는 프리미엄)와 같은 로컬 스토리지 계정만 만들 수 있습니다. 계층형 스토리지 계정을 만들려면 [자습서: GPU가 포함된 Azure Stack Edge Pro를 사용하여 스토리지 계정을 통해 데이터 전송](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)을 참조하세요.

다음은 몇 가지 출력 예입니다.

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

스토리지 계정 키를 얻으려면 `Get-AzureRmStorageAccountKey` 명령을 실행합니다. 다음은 몇 가지 출력 예입니다.

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-the-blob-uri-to-the-host-file"></a>호스트 파일에 Blob URI 추가

[Azure PowerShell을 통해 Azure Stack Edge 디바이스에 VM 배포](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)의 "5단계: 엔드포인트 이름 확인을 위한 호스트 파일 수정"에서 Azure Blob Storage에 연결하는 데 사용하는 클라이언트의 호스트 파일에 Blob URI를 이미 추가했습니다. 이 항목은 Blob URI를 추가하는 데 사용되었습니다.

\<Azure consistent network services VIP \> \<storage name\>.blob.\<appliance name\>.\<dnsdomain\>

## <a name="install-certificates"></a>인증서 설치

HTTPS를 사용하는 경우 디바이스에 적절한 인증서를 설치해야 합니다. 여기에서 Blob 엔드포인트 인증서를 설치합니다. 자세한 내용은 [GPU 디바이스가 있는 Azure Stack Edge Pro에서 인증서 사용](azure-stack-edge-gpu-manage-certificates.md)을 참조하세요.

## <a name="upload-a-vhd"></a>VHD 업로드

이전에 만든 로컬 스토리지 계정의 페이지 Blob에 사용할 디스크 이미지를 복사합니다. [AzCopy](../storage/common/storage-use-azcopy-v10.md)와 같은 도구를 사용하여 스토리지 계정에 VHD(가상 하드 디스크)를 업로드할 수 있습니다. 

<!--Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you're using with your Azure Stack Edge Pro device.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Set `BlobType` to `page` for creating a managed disk out of VHD. Set `BlobType` to `block` when you're writing to tiered storage accounts by using AzCopy.

You can download the disk images from Azure Marketplace. For more information, see [Get the virtual disk image from Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Here's some example output that uses AzCopy 7.3. For more information about this command, see [Upload VHD file to storage account by using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->
AzCopy 10에서 다음 명령을 사용합니다.  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

다음은 몇 가지 출력 예입니다. 

```powershell
$ContainerName = <ContainerName>
$ResourceGroupName = <ResourceGroupName>
$StorageAccountName = <StorageAccountName>
$VHDPath = "Full VHD Path"
$VHDFile = <VHDFileName>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```

## <a name="create-a-managed-disk-from-the-vhd"></a>VHD에서 관리 디스크를 만들려면

업로드된 VHD에서 관리 디스크를 만들려면 다음 명령을 실행합니다.

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
다음은 몇 가지 출력 예입니다. 

<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

다음은 몇 가지 출력 예입니다. 이 cmdlet에 대한 자세한 내용은 [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true)를 참조하세요.

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>이미지 관리 디스크에서 VM 이미지 만들기

관리 디스크에서 VM 이미지를 만들려면 다음 명령어를 실행하세요. *\<Disk name>* , *\<OS type>* 및 *\<Disk size>* 을 실제 값으로 바꿉니다.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

다음은 몇 가지 출력 예입니다. 이 cmdlet에 대한 자세한 내용은 [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true)를 참조하세요.

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-your-vm-with-previously-created-resources"></a>이전에 만든 리소스로 VM 만들기

VM을 만들고 배포하기 전에 하나의 가상 네트워크를 만들고 가상 네트워크 인터페이스를 연결해야 합니다.

> [!IMPORTANT]
> 다음 규칙이 적용됩니다.
> - 리소스 그룹 간에도 하나의 가상 네트워크만 만들 수 있습니다. 가상 네트워크에는 논리 네트워크와 정확히 동일한 주소 공간이 있어야 합니다.
> - 가상 네트워크에는 서브넷이 하나만 있을 수 있습니다. 서브넷에는 가상 네트워크와 정확히 동일한 주소 공간이 있어야 합니다.
> - 가상 네트워크 인터페이스 카드를 만들 때 정적 할당 방법만 사용할 수 있습니다. 사용자는 개인 IP 주소를 제공해야 합니다.

### <a name="query-the-automatically-created-virtual-network"></a>자동으로 만들어진 가상 네트워크 쿼리

디바이스의 로컬 UI에서 컴퓨팅을 사용하도록 설정하면 `ASERG` 리소스 그룹 아래에 `ASEVNET`이라는 가상 네트워크가 자동으로 만들어집니다. 

다음 명령을 사용하여 기존 가상 네트워크를 쿼리합니다.

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

### <a name="create-a-virtual-network-interface-card"></a>가상 네트워크 인터페이스 카드 만들기

가상 네트워크 서브넷 ID를 사용하여 가상 네트워크 인터페이스 카드를 만들려면 다음 명령을 실행합니다.

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

다음은 몇 가지 출력 예입니다.

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
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
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

선택적으로 VM에 대한 가상 네트워크 인터페이스 카드를 만드는 동안 공용 IP를 전달할 수 있습니다. 이 경우 공용 IP는 개인 IP를 반환합니다. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="create-a-vm"></a>VM 만들기

이제 VM 이미지를 사용하여 VM을 만들고 이전에 만든 가상 네트워크에 연결할 수 있습니다.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

VM을 만들고 전원을 켠 후에는 다음 사용자 이름과 암호를 사용하여 로그인합니다.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-the-vm"></a>VM에 연결

만들어진 Windows VM 또는 Linux VM에 따라 연결 지침이 다를 수 있습니다.

### <a name="connect-to-a-linux-vm"></a>Linux VM에 연결

Linux VM에 연결하려면 다음을 수행합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

  VM을 만들 때 공용 IP 주소를 사용한 경우 해당 IP를 사용하여 VM에 연결할 수 있습니다. 공용 IP를 가져오려면 다음 명령을 실행합니다. 

  ```powershell
  $publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
  ```
  이 인스턴스에서 공용 IP는 가상 네트워크 인터페이스를 만드는 동안 전달한 개인 IP와 동일합니다.

### <a name="connect-to-a-windows-vm"></a>Windows VM에 연결

Windows VM에 연결하려면 다음을 수행합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM by using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When you're prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command:

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this instance is the same as the private IP that you passed during the virtual network interface creation.-->


## <a name="manage-the-vm"></a>VM 관리

다음 섹션에서는 Azure Stack Edge Pro 디바이스에서 만들 수 있는 몇 가지 일반적인 작업에 대해 설명합니다.

### <a name="list-vms-that-are-running-on-the-device"></a>디바이스에서 실행 중인 VM 나열

Azure Stack Edge 디바이스에서 실행 중인 모든 VM 목록을 반환하려면 다음 명령을 실행합니다.


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>VM 켜기

디바이스에서 실행 중인 가상 머신을 켜려면 다음 cmdlet을 실행합니다.

`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`

이 cmdlet에 대한 자세한 내용은 [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true)을 참조하세요.

### <a name="suspend-or-shut-down-the-vm"></a>VM 일시 중지 또는 종료

디바이스에서 실행 중인 가상 머신을 중지하거나 종료하려면 다음 cmdlet을 실행합니다.


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

이 cmdlet에 대한 자세한 내용은 [Stop-AzureRmVM cmdlet](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true)을 참조하세요.

### <a name="add-a-data-disk"></a>데이터 디스크 추가

VM의 워크로드 요구 사항이 증가하면 데이터 디스크를 추가해야 할 수 있습니다. 이렇게 하려면 다음 명령을 실행합니다.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>VM 삭제

디바이스에서 가상 머신을 제거하려면 다음 cmdlet을 실행합니다.

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

이 cmdlet에 대한 자세한 내용은 [Remove-AzureRmVm cmdlet](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Resource Manager cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
