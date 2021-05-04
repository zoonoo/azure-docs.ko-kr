---
title: PowerShell을 통해 Azure Stack Edge Pro GPU 디바이스에서 VM 디스크 백업
description: Azure Stack Edge Pro GPU 디바이스에서 실행되는 가상 머신 디스크의 데이터를 백업하는 방법을 설명합니다
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: 5fad2a9e1789b98ac541e8a0d95c77131905544d
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364733"
---
# <a name="back-up-vm-disks-on-azure-stack-edge-pro-gpu-via-azure-powershell"></a>Azure PowerShell을 통해 Azure Stack Edge Pro GPU에서 VM 디스크 백업

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure PowerShell을 사용하여 Azure Stack Edge Pro GPU 디바이스에서 가상 머신 디스크의 백업을 만드는 방법을 설명합니다.

> [!IMPORTANT]
> 이 절차는 중지된 VM에 적용하기 위한 것입니다. 실행 중인 VM을 백업하려면 타사 백업 도구를 사용하는 것이 좋습니다.

## <a name="workflow"></a>워크플로

다음 단계에는 디바이스에서 VM 디스크를 백업하기 위한 대략적인 워크플로가 요약되어 있습니다.

1. VM을 중지합니다.
1. VM 디스크의 스냅샷을 만듭니다.
1. 스냅샷을 로컬 스토리지 계정에 VHD로 복사합니다.
1. VHD를 외부 대상에 업로드합니다.

## <a name="prerequisites"></a>필수 구성 요소

VM을 백업하기 전에 다음 사항을 확인합니다.

- 디바이스에 연결하는 데 사용할 클라이언트에 액세스할 수 있습니다.
    - 클라이언트가 [지원되는 OS](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 실행합니다.
    - [디바이스의 Azure Resource Manager에 연결](azure-stack-edge-gpu-connect-resource-manager.md) 지침에 따라 디바이스의 로컬 Azure Resource Manager에 연결하도록 클라이언트가 구성되어 있습니다.

## <a name="verify-connection-to-local-azure-resource-manager"></a>로컬 Azure Resource Manager에 대한 연결 확인

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]

## <a name="back-up-a-vm-disk"></a>VM 디스크 백업

1. 디바이스에서 실행 중인 VM 목록을 가져옵니다. 중지할 VM을 식별합니다.

    ```powershell
    Get-AzureRMVM
    ```

    출력의 예제는 다음과 같습니다.

    ```powershell
    PS C:\Users\user> Get-AzureRMVM

    ResourceGroupName    Name         Location  VmSize         OsType   NIC         ProvisioningState Zone
    -----------------    ----         --------  ------         ------   ---         ----------------- ----
    MYASERG           myasewindowsvm1 dbelocal Standard_D1_v2  Linux myasewindowsvm1nic  Succeeded
    MYASERG1            myaselinuxvm1 dbelocal Standard_D1_v2  Linux   myaselinuxvm1nic  Succeeded
    MYASERG2             myasetestvm1 dbelocal Standard_D1_v2  Linux    myasetestvm1nic  Succeeded

    PS C:\Users\user>
    ```
    
1. VM을 중지합니다.

    ```powershell
    Stop-AzureRMVM –ResourceGroupName <Resource group name> -Name <VM name>
    ```

    출력의 예제는 다음과 같습니다.

    ```powershell
    PS C:\Users\user> Stop-AzureRMVM -ResourceGroupName myaserg2 -Name myasetestvm1

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    OperationId :
    Status      : Succeeded
    StartTime   : 4/9/2021 8:43:47 AM
    EndTime     : 4/9/2021 8:44:27 AM
    Error       :
    
    PS C:\Users\user>
    ```
    Azure Portal에서 VM을 중지할 수도 있습니다.
 

2. VM 디스크의 스냅샷을 만들고 로컬 리소스 그룹에 저장합니다. OS 및 데이터 디스크 모두에 대해 이 절차를 사용할 수 있습니다.

   1. 디바이스의 디스크 또는 특정 리소스 그룹에서 목록을 가져옵니다. 백업할 디스크의 이름을 적어둡니다.

        ```powershell
        Get-AzureRMDisk -ResourceGroupName <Resource group name>
        ```   
        출력의 예제는 다음과 같습니다.

        ```powershell
        PS C:\Users\user> $Disk = Get-AzureRMDisk -ResourceGroupName myaserg2
        PS C:\Users\user> $Disk.Name
        myasetestdisk1
        myasetestvm1_disk1_0ed91809927f4023b7aceb6eeca51c05
        PS C:\Users\user>
        ```
   1. VM 스냅샷의 대상으로 사용할 로컬 리소스 그룹을 만듭니다.

        ```powershell
        PS C:\Users\user> New-AzureRmResourceGroup -ResourceGroupName myaserg3 -Location dbelocal
    
        ResourceGroupName : myaserg3
        Location          : dbelocal
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg3
        
        PS C:\Users\user>
        ```

   1. 일부 매개 변수를 설정합니다.

      ```powershell
      $DiskResourceGroup = <Disk resource group>
      $DiskName = <Disk name>
      $SnapshotName = <Snapshot name>
      $DestinationRG = <Snapshot destination resource group>
      ```

   3. 스냅샷 구성을 설정하고 스냅샷을 만듭니다.

        ```powershell
        $Disk = Get-AzureRmDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        $SnapshotConfig = New-AzureRmSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        $Snapshot = New-AzureRmSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        ```
        스냅샷이 대상 리소스 그룹에 만들어졌는지 확인합니다.

        ```powershell
        Get-AzureRMSnapshot -ResourceGroupName $DestinationRG
        ```
        출력의 예제는 다음과 같습니다.

        ```powershell
        PS C:\Users\user> $DiskResourceGroup = "myaserg2"
        PS C:\Users\user> $DiskName = "myasetestdisk1"
        PS C:\Users\user> $SnapshotName = "myasetestdisk1_ss"
        PS C:\Users\user> $DestinationRG = "myaserg3"
        PS C:\Users\user> $Disk = Get-AzureRmDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        PS C:\Users\user> $SnapshotConfig = New-AzureRmSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        PS C:\Users\user> $Snapshot=New-AzureRmSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        PS C:\Users\user> Get-AzureRMSnapshot -ResourceGroupName $DestinationRG
        
        ResourceGroupName  : myaserg3
        ManagedBy          :
        Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
        TimeCreated        : 4/9/2021 4:23:21 PM
        OsType             :
        CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
        DiskSizeGB         : 10
        EncryptionSettings :
        ProvisioningState  : Succeeded
        Id                 : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg3/providers/Microsoft.Compute/snapshots/myasetestdisk1_ss
        Name               : myasetestdisk1_ss
        Type               : Microsoft.Compute/snapshots
        Location           : DBELocal
        Tags               : {}
        
        PS C:\Users\user>
        ```

## <a name="copy-the-snapshot-into-a-local-storage-account"></a>로컬 스토리지 계정에 스냅샷 복사

   디바이스에서 로컬 스토리지 계정에 스냅샷을 복사합니다. 

1. 일부 매개 변수를 설정합니다. 

    ```powershell
    $StorageAccountRG = <Local storage account resource group>
    $StorageAccountName = <Storage account name>
    $StorageEndpointSuffix = <Connection string in format: DeviceName.DnsDomain.com>
    $DestStorageContainer = <Destination storage container>
    $DestFileName = <Blob file name> 
    ```

1. 디바이스에서 로컬 스토리지 계정을 만듭니다. 

    ```powershell
    New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Storage account resource group> -Location DBELocal -SkuName Standard_LRS
    ```

    출력의 예제는 다음과 같습니다. 

    ```powershell
    PS C:\Users\user> New-AzureRmStorageAccount -Name myasesa4 -ResourceGroupName myaserg4 -Location DBELocal -SkuName Standard_LRS
    StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime        ProvisioningState EnableHttpsTrafficOnly
    ------------------ ----------------- -------- -------     ----    ---------- ------------        ----------------- ---------------
    myasesa4           myaserg4          DBELocal StandardLRS Storage            4/9/2021 6:02:56 PM Succeeded         False
    
    PS C:\Users\user>
    ```

1. 만든 로컬 스토리지 계정에 컨테이너를 만듭니다. 

    ```powershell
    $keys = Get-AzureRmStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    $keyValue = $keys[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;
    $container = New-AzureStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;    
    ```
    출력의 예제는 다음과 같습니다.

    ```powershell
    PS C:\Users\user> $StorageAccountName = "myasesa4"                                                              
    PS C:\Users\user> $StorageAccountRG = "myaserg4"
    PS C:\Users\user> $DestStorageContainer = "myasecont2"
    PS C:\Users\user> $StorageEndpointSuffix = "myasegpudev.wdshcsso.com"
    PS C:\Users\user> $DestFileName = "testfile1"

    PS C:\Users\user> $keys = Get-AzureRmStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    PS C:\Users\user> $keyValue = $keys[0].Value
    PS C:\Users\user> $storageContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;                                                                                   
    PS C:\Users\user> $storagecontext 
    StorageAccountName : myasesa4                                                                                      
    BlobEndPoint       : http://myasesa4.blob.myasegpudev.wdshcsso.com/                                                
    TableEndPoint      : http://myasesa4.table.myasegpudev.wdshcsso.com/
    QueueEndPoint      : http://myasesa4.queue.myasegpudev.wdshcsso.com/
    FileEndPoint       : http://myasesa4.file.myasegpudev.wdshcsso.com/
    Context            : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name               :
    StorageAccount     : BlobEndpoint=http://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=http://myasesa4.que
                         ue.myasegpudev.wdshcsso.com/;TableEndpoint=http://myasesa4.table.myasegpudev.wdshcsso.com/;Fi
                         leEndpoint=http://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=[ke
                         y hidden]
    EndPointSuffix     : myasegpudev.wdshcsso.com/
    ConnectionString   : BlobEndpoint=http://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=http://myasesa4.que
                         ue.myasegpudev.wdshcsso.com/;TableEndpoint=http://myasesa4.table.myasegpudev.wdshcsso.com/;Fi
                         leEndpoint=http://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=GSK
                         FuTCJi5Dby6A6C1F4jB4bYS/gBNslb7/FAdlh/0VWUg3Vxd1kHsbwN8sw85pMqdKG1AajoeiwzhievHPnlQ==
    ExtendedProperties : {}
    
    PS C:\Users\user> $container = New-AzureStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;
    PS C:\Users\user> $container
    Blob End Point: http://myasesa4.blob.myasegpudev.wdshcsso.com/
    
    Name                 PublicAccess         LastModified
    ----                 ------------         ------------
    myasecont2           Container            4/12/2021 4:46:03 PM +00:00
    
    PS C:\Users\user>
    ```    

    Azure Storage Explorer를 사용하여 [로컬 스토리지 계정을 만든](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#create-a-storage-account) 다음 디바이스에서 [로컬 스토리지 계정에 컨테이너를 만들 수도](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload) 있습니다. 



1. 로컬 스토리지 계정에 스냅샷을 다운로드합니다.

   ```powershell
   $sassnapshot = Grant-AzureRmSnapshotAccess -ResourceGroupName $DestinationRG -SnapshotName $SnapshotName -Access 'Read' -DurationInSecond 3600
   $destContext = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $keyValue 
   Start-AzureStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName
   ```

    출력의 예제는 다음과 같습니다.

    ```powershell
    PS C:\Users\user> $sassnapshot= Grant-AzureRmSnapshotAccess -ResourceGroupName $DestinationRG -SnapshotName $SnapshotName -Access 'Read' -DurationInSecond 3600
    PS C:\Users\user> $sassnapshot
       
    AccessSAS : https://md-2.blob.myasegpudev.wdshcsso.com/3d95ae10d9924e6fb84de408d071f22d/abcd.vhd?sv=2017-04-17&sr=
    b&si=2535bf98-f87f-4738-9142-594e3c1150fc&sk=system-1&sig=4wrtYzWg6ePWBdrXlodrVgT76q7PIueCbw3bbShKCGs%3D   
    
    PS C:\Users\user> $destContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue
    PS C:\Users\user> $destContext
        
    StorageAccountName : myasesa4
    BlobEndPoint       : https://myasesa4.blob.myasegpudev.wdshcsso.com/
    TableEndPoint      : https://myasesa4.table.myasegpudev.wdshcsso.com/
    QueueEndPoint      : https://myasesa4.queue.myasegpudev.wdshcsso.com/
    FileEndPoint       : https://myasesa4.file.myasegpudev.wdshcsso.com/
    Context            : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name               :
    StorageAccount     : BlobEndpoint=https://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=https://myasesa4.q
                         ueue.myasegpudev.wdshcsso.com/;TableEndpoint=https://myasesa4.table.myasegpudev.wdshcsso.com/;FileEndpoint=https://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=[key hidden]                                  EndPointSuffix     : myasegpudev.wdshcsso.com/                                                                     ConnectionString   : BlobEndpoint=https://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=https://myasesa4.q
                         ueue.myasegpudev.wdshcsso.com/;TableEndpoint=https://myasesa4.table.myasegpudev.wdshcsso.com/
                         ;FileEndpoint=https://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey
                         =GSKFuTCJi5Dby6A6C1F4jB4bYS/gBNslb7/FAdlh/0VWUg3Vxd1kHsbwN8sw85pMqdKG1AajoeiwzhievHPnlQ==
    ExtendedProperties : {}

    PS C:\Users\user> Start-AzureStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName
    
    
       Container Uri: https://myasesa4.blob.myasegpudev.wdshcsso.com/myasecont2
    
    Name                 BlobType  Length          ContentType                    LastModified         AccessTier Snapshot Time
    ----                 --------  ------          -----------                    ------------         ---------- ----
    testfile1            BlockBlob -1                                             2021-04-12 17:01:58Z
 
    PS C:\Users\user>
    ```

    Storage Explorer를 사용하여 스냅샷이 스토리지 계정에 올바르게 복사되었는지 확인할 수도 있습니다.

    ![로컬 스토리지 계정의 컨테이너에 있는 백업을 표시하는 Storage Explorer](media/azure-stack-edge-gpu-back-up-virtual-machine-disks/back-up-virtual-machine-disk-1.png)

## <a name="download-vhd-to-external-target"></a>외부 대상에 VHD 다운로드

백업을 외부 위치로 이동하려면 Azure Storage Explorer 또는 AzCopy를 사용할 수 있습니다.

- 다음 AzCopy 명령을 사용하여 외부 대상에 VHD를 다운로드합니다.

    ```powershell
    azcopy copy "https://<local storage account name>.blob.<device name>.<DNS domain>/<container name>/<filename><SAS query string>" <destination target>
    ```

- Azure Stack Edge를 통해 Azure Storage Explorer를 설정하고 사용하려면 [업로드에 Storage Explorer 사용 ](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)의 지침을 참조합니다.

## <a name="next-steps"></a>다음 단계

[템플릿을 사용하여 Azure Stack Edge Pro GPU 디바이스에 가상 머신을 배포합니다](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).