---
title: Azure PowerShell를 통해 Azure Stack Edge 장치에 Vm 배포
description: Azure PowerShell를 사용 하 여 Azure Stack Edge 장치에서 가상 컴퓨터를 만들고 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 28988af0c1b3b5e4e5ce359abb617a66af816d69
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439819"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Azure PowerShell를 통해 Azure Stack Edge 장치에 Vm 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure PowerShell를 사용 하 여 Azure Stack Edge 장치에서 VM (가상 머신)을 만들고 관리 하는 방법을 설명 합니다. 이 정보는 GPU (그래픽 처리 장치)를 사용 하는 Azure Stack Edge Pro, Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R 장치에 적용 됩니다.

## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

배포 워크플로는 다음 다이어그램에 표시 됩니다.

![VM 배포 워크플로의 다이어그램입니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>장치에서 기본 제공 구독 쿼리

Azure Resource Manager의 경우 사용자가 볼 수 있는 단일 고정 구독만 지원 됩니다. 이 구독은 장치 마다 고유 하며 구독 이름 및 구독 ID를 변경할 수 없습니다.

구독에는 VM을 만드는 데 필요한 모든 리소스가 포함 되어 있습니다. 

> [!IMPORTANT]
> 구독은 Azure Portal에서 Vm을 사용 하도록 설정 하 고 장치에 로컬로 상주 하는 경우 생성 됩니다.

구독은 Vm을 배포 하는 데 사용 됩니다.

1.  구독을 나열 하려면 다음 명령을 실행 합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
    
    다음은 몇 가지 예제 출력입니다.

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. 장치에서 실행 되는 등록 된 리소스 공급자의 목록을 가져옵니다. 일반적으로이 목록에는 compute, network 및 storage가 포함 됩니다.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > 리소스 공급자는 미리 등록 되어 있으며 수정 하거나 변경할 수 없습니다.
    
    다음은 몇 가지 예제 출력입니다.

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

[New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 저장소 계정, 디스크 및 관리 디스크와 같은 Azure 리소스가 배포 및 관리 되는 논리적 컨테이너입니다.

> [!IMPORTANT]
> 모든 리소스는 장치와 동일한 위치에 만들어지고 위치는 **Dbelocal** 로 설정 됩니다.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

다음은 몇 가지 예제 출력입니다.

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

이전 단계에서 만든 리소스 그룹을 사용 하 여 새 저장소 계정을 만듭니다. VM에 대 한 가상 디스크 이미지를 업로드 하는 데 사용 하는 로컬 저장소 계정입니다.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Azure Resource Manager를 사용 하 여 로컬 저장소 계정 (예: 로컬 중복 저장소) (standard 또는 premium)만 만들 수 있습니다. 계층화 된 저장소 계정을 만들려면 [자습서: GPU를 사용 하 여 Azure Stack Edge Pro로 저장소 계정을 통해 데이터 전송](azure-stack-edge-j-series-deploy-add-storage-accounts.md)을 참조 하세요.

다음은 몇 가지 예제 출력입니다.

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

저장소 계정 키를 가져오려면 `Get-AzureRmStorageAccountKey` 명령을 실행 합니다. 다음은 몇 가지 예제 출력입니다.

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

## <a name="add-the-blob-uri-to-the-host-file"></a>호스트 파일에 blob URI를 추가 합니다.

[Azure PowerShell를 통해 Azure Stack에 지 장치에 Vm 배포](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)의 "5 단계: 끝점 이름 확인을 위한 호스트 파일 수정"의 Azure Blob Storage에 연결 하는 데 사용 하는 클라이언트의 호스트 파일에 blob URI를 이미 추가 했습니다. 이 항목은 blob URI를 추가 하는 데 사용 되었습니다.

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> .\<dnsdomain\>

## <a name="install-certificates"></a>인증서 설치

HTTPS를 사용 하는 경우 장치에 적절 한 인증서를 설치 해야 합니다. 여기에서 blob 끝점 인증서를 설치 합니다. 자세한 내용은 [GPU 장치를 사용 하 여 Azure Stack Edge Pro에서 인증서 사용](azure-stack-edge-gpu-manage-certificates.md)을 참조 하세요.

## <a name="upload-a-vhd"></a>VHD 업로드

이전에 만든 로컬 저장소 계정의 페이지 blob에 사용할 디스크 이미지를 복사 합니다. [AzCopy](../storage/common/storage-use-azcopy-v10.md) 와 같은 도구를 사용 하 여 VHD (가상 하드 디스크)를 저장소 계정에 업로드할 수 있습니다. 

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
AzCopy 10에서 다음 명령을 사용 합니다.  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

다음은 몇 가지 예제 출력입니다. 

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

업로드 된 VHD에서 관리 디스크를 만들려면 다음 명령을 실행 합니다.

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
다음은 몇 가지 예제 출력입니다. 

<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

다음은 몇 가지 예제 출력입니다. 이 cmdlet에 대 한 자세한 내용은 [new-azurermdisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true)를 참조 하세요.

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

관리 디스크에서 VM 이미지를 만들려면 다음 명령을 실행 합니다. *\<Disk name>*, *\<OS type>* 및을 *\<Disk size>* 실제 값으로 바꿉니다.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

다음은 몇 가지 예제 출력입니다. 이 cmdlet에 대 한 자세한 내용은 [new-azurermimage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true)를 참조 하세요.

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

## <a name="create-your-vm-with-previously-created-resources"></a>이전에 만든 리소스를 사용 하 여 VM 만들기

VM을 만들고 배포 하기 전에 가상 네트워크를 하나 만들고 가상 네트워크 인터페이스를 연결 해야 합니다.

> [!IMPORTANT]
> 다음 규칙이 적용됩니다.
> - 리소스 그룹 간에도 가상 네트워크를 하나만 만들 수 있습니다. 가상 네트워크에는 논리 네트워크와 정확히 동일한 주소 공간이 있어야 합니다.
> - 가상 네트워크에는 서브넷이 하나만 있을 수 있습니다. 서브넷에는 가상 네트워크와 정확히 동일한 주소 공간이 있어야 합니다.
> - 가상 네트워크 인터페이스 카드를 만들 때 정적 할당 메서드만 사용할 수 있습니다. 사용자는 개인 IP 주소를 제공 해야 합니다.

### <a name="query-the-automatically-created-virtual-network"></a>자동으로 만들어진 가상 네트워크 쿼리

장치의 로컬 UI에서 계산을 사용 하도록 설정 하면 이라는 가상 네트워크가 `ASEVNET` 리소스 그룹 아래에 자동으로 만들어집니다 `ASERG` . 

다음 명령을 사용 하 여 기존 가상 네트워크를 쿼리 합니다.

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

### <a name="create-a-virtual-network-interface-card"></a>가상 네트워크 인터페이스 카드 만들기

가상 네트워크 서브넷 ID를 사용 하 여 가상 네트워크 인터페이스 카드를 만들려면 다음 명령을 실행 합니다.

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

다음은 몇 가지 예제 출력입니다.

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

VM에 대 한 가상 네트워크 인터페이스 카드를 만드는 동안 필요에 따라 공용 IP를 전달할 수 있습니다. 이 인스턴스에서 공용 IP는 개인 IP를 반환 합니다. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="create-a-vm"></a>VM 만들기

이제 VM 이미지를 사용 하 여 VM을 만들고 이전에 만든 가상 네트워크에 연결할 수 있습니다.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

VM을 만들고 켠 후에는 다음 사용자 이름 및 암호를 사용 하 여 로그인 합니다.

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

Windows VM을 만들었는지 아니면 Linux VM을 만들었는지에 따라 연결 지침이 달라질 수 있습니다.

### <a name="connect-to-a-linux-vm"></a>Linux VM에 연결

Linux VM에 연결 하려면 다음을 수행 합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Windows VM에 연결

Windows VM에 연결 하려면 다음을 수행 합니다.

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

다음 섹션에서는 Azure Stack Edge Pro 장치에서 만들 수 있는 몇 가지 일반적인 작업에 대해 설명 합니다.

### <a name="list-vms-that-are-running-on-the-device"></a>장치에서 실행 되는 Vm 나열

Azure Stack Edge 장치에서 실행 되는 모든 Vm의 목록을 반환 하려면 다음 명령을 실행 합니다.


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>VM 켜기

장치에서 실행 되는 가상 컴퓨터를 켜려면 다음 cmdlet을 실행 합니다.

`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`

이 cmdlet에 대 한 자세한 내용은 [new-azurermvm](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true)를 참조 하세요.

### <a name="suspend-or-shut-down-the-vm"></a>VM 일시 중단 또는 종료

장치에서 실행 되는 가상 컴퓨터를 중지 하거나 종료 하려면 다음 cmdlet을 실행 합니다.


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

이 cmdlet에 대 한 자세한 내용은 [new-azurermvm cmdlet](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true)을 참조 하세요.

### <a name="add-a-data-disk"></a>데이터 디스크 추가

VM에 대 한 워크 로드 요구 사항이 증가 하는 경우 데이터 디스크를 추가 해야 할 수 있습니다. 이렇게 하려면 다음 명령을 실행합니다.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>VM 삭제

장치에서 가상 컴퓨터를 제거 하려면 다음 cmdlet을 실행 합니다.

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

이 cmdlet에 대 한 자세한 내용은 [new-azurermvm cmdlet](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Resource Manager cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
