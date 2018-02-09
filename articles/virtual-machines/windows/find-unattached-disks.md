---
title: "연결되지 않은 Azure 관리/비관리 디스크를 찾아서 삭제 | Microsoft Docs"
description: "Azure PowerShell을 사용하여 연결되지 않은 Azure 관리/비관리(VHD/페이지 Blob) 디스크를 찾아서 삭제하는 방법입니다."
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>연결되지 않은 Azure 관리/비관리 디스크 찾기 및 삭제
Azure에서 가상 머신을 삭제할 경우 기본적으로 가상 머신에 연결된 디스크는 삭제되지 않습니다. 따라서 실수로 삭제한 가상 머신으로 인한 데이터 손실은 방지할 수 있지만 연결되지 않은 디스크에 대해 불필요하게 비용이 계속 청구됩니다. 이 문서를 사용하여 연결되지 않은 모든 디스크를 찾아 삭제하여 비용을 절감합니다. 


## <a name="find-and-delete-unattached-managed-disks"></a>연결되지 않은 관리 디스크 찾기 및 삭제 

다음 스크립트는 *ManagedBy* 속성을 사용하여 연결되지 않은 [Managed Disks](managed-disks-overview.md)를 찾는 방법을 보여 줍니다. 구독의 모든 Managed Disks를 반복하고 연결되지 않은 Managed Disks를 찾기 위해 *ManagedBy* 속성이 null인지 확인합니다. *ManagedBy* 속성은 관리 디스크가 연결되는 가상 머신의 리소스 ID를 저장합니다.

연결되지 않은 모든 디스크를 보려면 먼저 *deleteUnattachedDisks* 변수를 0으로 설정하여 스크립트를 실행하는 것이 좋습니다. 연결되지 않은 디스크를 검토한 후 연결되지 않은 모든 디스크를 삭제하려면 *deleteUnattachedDisks*를 1로 설정하여 스크립트를 실행합니다.

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>연결되지 않은 비관리 디스크 찾기 및 삭제 

비관리 디스크는 [Azure Storage 계정](../../storage/common/storage-create-storage-account.md)에 [페이지 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs)으로 저장된 VHD 파일입니다. 다음 스크립트에서는 *LeaseStatus* 속성을 사용하여 연결되지 않은 비관리 디스크(페이지 Blob)를 찾는 방법을 보여 줍니다. 구독의 모든 저장소 계정에서 모든 비관리 디스크를 반복하고 연결되지 않은 비관리 디스크를 찾기 위해 *LeaseStatus* 속성의 잠금이 해제되어 있는지 확인합니다. *LeaseStatus* 속성은 비관리 디스크가 가상 머신에 연결된 경우 잠김으로 설정됩니다.

연결되지 않은 모든 디스크를 보려면 먼저 *deleteUnattachedVHDs* 변수를 0으로 설정하여 스크립트를 실행하는 것이 좋습니다. 연결되지 않은 모든 디스크를 삭제하려면 연결되지 않은 디스크를 검토한 후 *deleteUnattachedVHDs*를 1로 설정하여 스크립트를 실행합니다.


```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}

```

## <a name="next-steps"></a>다음 단계

[저장소 계정 삭제](../../storage/common/storage-create-storage-account.md)




