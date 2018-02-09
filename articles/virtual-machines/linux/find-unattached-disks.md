---
title: "연결되지 않은 Azure 관리 및 비관리 디스크 찾기 및 삭제 | Microsoft Docs"
description: "Azure CLI를 사용하여 연결되지 않은 Azure 관리 및 비관리(VHD/페이지 Blob) 디스크를 찾고 삭제하는 방법입니다."
services: virtual-machines-linux
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 9ada768cd4128b9dd6949b5a96c557496c6bb11c
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>연결되지 않은 Azure 관리 및 비관리 디스크 찾기 및 삭제
Azure에서 가상 머신을 삭제하는 경우 연결된 디스크는 기본적으로 삭제되지 않습니다. 실수로 삭제된 가상 머신으로 인해 데이터 손실은 방지되지만 불필요하게 연결되지 않은 디스크에 대한 요금은 계속 지불하게 됩니다. 이 문서를 사용하여 연결되지 않은 모든 디스크를 찾아 삭제하여 비용을 절감합니다. 


## <a name="find-and-delete-unattached-managed-disks"></a>연결되지 않은 관리 디스크 찾기 및 삭제 

다음 스크립트에서는 ManagedBy 속성을 사용하여 연결되지 않은 Managed Disks를 찾는 방법을 보여 줍니다.  구독에 있는 모든 Managed Disks에 대해 반복하고, 연결되지 않은 Managed Disks를 찾기 위해 *ManagedBy* 속성이 null인지 확인합니다. *ManagedBy* 속성은 Managed Disk가 연결된 가상 머신의 리소스 ID를 저장합니다. 

연결되지 않은 모든 디스크를 보려면 먼저 *deleteUnattachedDisks* 변수를 0으로 설정하여 스크립트를 실행하는 것이 좋습니다. 연결되지 않은 디스크를 검토한 후 이러한 디스크를 모두 삭제하려면 *deleteUnattachedDisks*를 1로 설정하여 스크립트를 실행합니다.

 ```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done

```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>연결되지 않은 비관리 디스크 찾기 및 삭제 

Unmanaged Disks는 [Azure Storage 계정](../../storage/common/storage-create-storage-account.md)에 [페이지 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs)으로 저장된 VHD 파일입니다. 다음 스크립트에서는 LeaseStatus 속성을 사용하여 연결되지 않은 비관리 디스크(페이지 Blob)를 찾는 방법을 보여 줍니다. 구독의 모든 저장소 계정에 있는 관리되지 않는 디스크 모두에 대해 반복하고, 연결되지 않은 비관리 디스크를 찾기 위해 *LeaseStatus* 속성의 잠금이 해제되어 있는지 확인합니다. 관리되지 않는 디스크가 가상 머신에 연결된 경우 *LeaseStatus* 속성이 locked(잠금)로 설정됩니다. 

연결되지 않은 모든 디스크를 보려면 먼저 *deleteUnattachedVHDs* 변수를 0으로 설정하여 스크립트를 실행하는 것이 좋습니다. 연결되지 않은 디스크를 검토한 후 이러한 디스크를 모두 삭제하려면 *deleteUnattachedVHDs*를 1로 설정하여 스크립트를 실행합니다.


 ```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 

```

## <a name="next-steps"></a>다음 단계

[저장소 계정 삭제](../../storage/common/storage-create-storage-account.md)



