---
title: "Azure의 Linux VM을 관리되지 않는 디스크에서 관리되는 디스크로 변환 | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 VM을 관리되지 않는 디스크에서 Azure Managed Disks로 변환하는 방법"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 53f5eefd9223fecefa184c612633d7a455fe15bf
ms.lasthandoff: 03/18/2017

---

# <a name="how-to-convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Linux VM을 관리되지 않는 디스크에서 Azure Managed Disks로 변환하는 방법

저장소 계정에 관리되지 않는 디스크를 사용하는 기존 Azure의 Linux VM이 있고 관리되는 디스크를 활용하고 싶다면 VM을 변환하면 됩니다. 이 프로세스는 OS 디스크와 연결된 데이터 디스크를 변환합니다. 변환 프로세스는 기존 유지 관리 창 동안 VM의 마이그레이션을 예약하도록 VM의 재시작이 필요합니다. 마이그레이션 프로세스는 되돌릴 수 없습니다. 프로덕션 환경에서 마이그레이션을 수행하기 전에 테스트 가상 컴퓨터를 마이그레이션하여 마이그레이션 프로세스를 테스트해야 합니다.

> [!IMPORTANT]
> 변환 중 VM의 할당을 취소합니다. VM은 변환 후 시작될 때 새 IP 주소를 받습니다. 고정 IP에 종속된 경우 예약된 IP를 사용합니다.

현재 또는 이전에 [Azure SSE(Storage Service Encryption)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 사용하여 암호화된 저장소 계정에 관리되지 않는 디스크가 있는 경우 관리되지 않는 디스크를 관리되는 디스크로 변환할 수 없습니다. 다음은 현재 암호화되었거나 이전에 암호화된 저장소 계정에 있는 관리되지 않는 디스크를 변환하는 자세한 단계입니다.

- [az storage blob copy start](/cli/azure/storage/blob/copy#start)를 사용하여 Azure Storage 서비스 암호화를 사용하도록 설정되지 않은 저장소 계정으로 VHD(가상 하드 디스크)를 복사합니다.
- 관리되는 디스크를 사용하는 VM을 만들고 [az vm create](/cli/azure/vm#create)로 생성되는 동안 해당 VHD 파일을 지정합니다. 또는
- [az vm disk attach](/cli/azure/vm/disk#attach)로 복사된 VHD를 관리되는 디스크가 있는 실행 중인 VM에 연결합니다.

## <a name="convert-vm-to-azure-managed-disks"></a>VM을 Azure Managed Disks로 변환
이 섹션에서는 기존 Azure VM을 관리되는 않는 디스크에서 관리되는 디스크로 변환하는 방법을 설명합니다. 이 프로세스를 사용하여 프리미엄(SSD) 관리되지 않는 디스크에서 프리미엄 관리되는 디스크로 또는 표준(HDD) 관리되지 않는 디스크에서 표준 관리되는 디스크로 변환할 수 있습니다.

> [!IMPORTANT]
> 다음 절차를 수행하면 기본 vhd 컨테이너에 블록 Blob 하나가 남습니다. 이 파일의 이름은 “VMName.xxxxxxx.status”입니다. 남아 있는 이 상태 개체를 삭제하지 마세요. 향후 조치를 통해 이 문제를 해결할 것입니다.

1. [az vm deallocate](/cli/azure/vm#deallocate)로 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. [az vm convert](/cli/azure/vm#convert)를 사용하여 관리되는 디스크로 VM을 변환합니다. 다음 프로세스는 OS 디스크 및 데이터 디스크를 포함하여 `myVM`이라는 VM을 변환합니다.

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. [az vm start](/cli/azure/vm#start)를 사용하여 관리되는 디스크로 변환한 후 VM을 시작합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 시작합니다.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vm-in-an-availability-set-to-managed-disks"></a>가용성 집합의 VM을 관리되는 디스크로 변환

관리되는 디스크로 변환하려는 VM이 가용성 집합에 있는 경우 먼저 가용성 집합을 관리되는 가용성 집합으로 변환해야 합니다.

가용성 집합을 변환하기 전에 가용성 집합에 있는 모든 VM의 할당을 취소해야 합니다. 가용성이 관리되는 가용성 집합으로 변환된 후 모든 VM을 관리되는 디스크로 변환하려고 합니다. 그러면 모든 VM을 시작하고 정상적으로 운영을 지속할 수 있습니다.

1. [az vm availability-set list](/cli/azure/vm/availability-set#list)를 사용하여 가용성 집합의 모든 VM을 나열합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 가용성 집합 `myAvailabilitySet`에 있는 모든 VM을 나열합니다.

    ```azurecli
    az vm availability-set show --resource-group myResourceGroup \
        --name myAvailabilitySet --query [virtualMachines[*].id] --output table
    ```

2. [az vm deallocate](/cli/azure/vm#deallocate)를 사용하여 모든 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. [az vm availability-set convert](/cli/azure/vm/availability-set#convert)를 사용하여 가용성 집합을 변환합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 가용성 집합 `myAvailabilitySet`을 변환합니다.

    ```azurecli
    az vm availability-set convert --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. [az vm convert](/cli/azure/vm#convert)를 사용하여 관리되는 디스크로 모든 VM을 변환합니다. 다음 프로세스는 OS 디스크 및 데이터 디스크를 포함하여 `myVM`이라는 VM을 변환합니다.

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. [az vm start](/cli/azure/vm#start)를 사용하여 관리되는 디스크로 변환한 후 모든 VM을 시작합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 시작합니다.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>다음 단계
저장소 옵션에 대한 자세한 내용은 [Azure Managed Disks 개요](../storage/storage-managed-disks-overview.md)를 참조하세요.

