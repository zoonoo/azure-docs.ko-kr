---
title: "Azure의 Linux 가상 컴퓨터를 비관리 디스크에서 Managed Disks로 변환 - Azure Managed Disks | Microsoft Docs"
description: "Resource Manager 배포 모델에서 Azure CLI 2.0을 사용하여 Linux VM을 비관리 디스크에서 Managed Disks로 변환하는 방법"
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
ms.devlang: azurecli
ms.topic: article
ms.date: 06/23/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 94f8e3330fb2d6547811315fcfdb8ced338e0247
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---

# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Linux 가상 컴퓨터를 비관리 디스크에서 Managed Disks로 변환

비관리 디스크를 사용하는 기존 Linux VM(가상 컴퓨터)이 있는 경우 [Azure Managed Disks](../windows/managed-disks-overview.md) 서비스를 통해 Managed Disks를 사용하도록 VM을 변환할 수 있습니다. 이 프로세스는 OS 디스크와 연결된 데이터 디스크를 변환합니다.

이 문서에서는 Azure CLI를 사용하여 VM을 변환하는 방법을 보여 줍니다. CLI를 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>단일 인스턴스 VM 변환
이 섹션에서는 단일 인스턴스 Azure VM을 비관리 디스크에서 Managed Disks로 변환하는 방법을 설명합니다. VM이 가용성 집합에 있는 경우 다음 섹션을 참조하세요. 이 프로세스를 사용하여 프리미엄(SSD) 비관리 디스크에서 프리미엄 Managed Disks로 또는 표준(HDD) 비관리 디스크에서 표준 Managed Disks로 변환할 수 있습니다.

1. [az vm deallocate](/cli/azure/vm#deallocate)를 사용하여 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. [az vm convert](/cli/azure/vm#convert)를 사용하여 VM을 Managed Disks로 변환합니다. 다음 프로세스는 OS 디스크 및 데이터 디스크를 포함하는 `myVM`이라는 VM을 변환합니다.

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. [az vm start](/cli/azure/vm#start)를 사용하여 VM을 Managed Disks로 변환한 후 시작합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 시작합니다.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>가용성 집합의 VM 변환

관리되는 디스크로 변환하려는 VM이 가용성 집합에 있는 경우 먼저 가용성 집합을 관리되는 가용성 집합으로 변환해야 합니다.

가용성 집합을 변환하기 전에 가용성 집합에 있는 모든 VM의 할당을 취소해야 합니다. 가용성 집합이 관리되는 가용성 집합으로 변환된 후 모든 VM을 관리되는 디스크로 변환하려고 합니다. 그런 다음 모든 VM을 시작하고 정상적으로 운영을 계속합니다.

1. [az vm availability-set list](/cli/azure/vm/availability-set#list)를 사용하여 가용성 집합의 모든 VM을 나열합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 가용성 집합 `myAvailabilitySet`에 있는 모든 VM을 나열합니다.

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. [az vm deallocate](/cli/azure/vm#deallocate)를 사용하여 모든 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. [az vm availability-set convert](/cli/azure/vm/availability-set#convert)를 사용하여 가용성 집합을 변환합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 가용성 집합 `myAvailabilitySet`을 변환합니다.

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. [az vm convert](/cli/azure/vm#convert)를 사용하여 모든 VM을 Managed Disks로 변환합니다. 다음 프로세스는 OS 디스크 및 데이터 디스크를 포함하는 `myVM`이라는 VM을 변환합니다.

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. [az vm start](/cli/azure/vm#start)를 사용하여 모든 VM을 Managed Disks로 변환한 후 시작합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 시작합니다.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>다음 단계
저장소 옵션에 대한 자세한 내용은 [Azure Managed Disks 개요](../windows/managed-disks-overview.md)를 참조하세요.

