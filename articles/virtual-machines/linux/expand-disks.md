---
title: "Azure의 Linux VM에서 가상 하드 디스크 확장 | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 Linux VM에서 가상 하드 디스크를 확장하는 방법 알아보기"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 4a0b8254ec80576576afde7af34828025d1d2f0a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017

---

# Azure CLI를 사용하여 Linux VM에서 가상 하드 디스크를 확장하는 방법
<a id="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli" class="xliff"></a>
Azure에서 Linux VM(가상 컴퓨터)에서 OS(운영 체제)에 대한 기본 가상 하드 디스크 크기는 일반적으로 30GB입니다. [데이터 디스크를 추가](add-disk.md)하여 추가 저장소 공간을 제공하고 OS 디스크 또는 기존 데이터 디스크를 확장할 수 있습니다. 이 문서에서는 Azure CLI 2.0을 사용하여 Linux VM에서 Managed Disks를 확장하는 방법을 설명합니다. [Azure CLI 1.0](expand-disks-nodejs.md)을 사용하여 관리되지 않는 OS 디스크를 확장할 수도 있습니다.

## 디스크 확장
<a id="expand-disk" class="xliff"></a>
최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치했고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인했는지 확인합니다.

다음 샘플에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup* 및 *myVM*이 포함됩니다.

1. VM 실행 중에 가상 하드 디스크에 대한 작업을 수행할 수 없습니다. [az vm deallocate](/cli/azure/vm#deallocate)를 사용하여 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM의 할당을 취소합니다.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `az vm stop`은 계산 리소스를 릴리스하지 않습니다. 계산 리소스를 릴리스하려면 `az vm deallocate`을 사용합니다. VM 할당을 취소하여 가상 하드 디스크를 확장해야 합니다.

2. 이제 [az disk list](/cli/azure/disk#list)를 사용하여 리소스 그룹에서 Managed Disks 목록을 봅니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*의 Managed Disks 목록을 표시합니다.

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    [az disk update](/cli/azure/disk#update)를 사용하여 필요한 디스크를 확장합니다. 다음 예제에서는 *myDataDisk*이라는 Managed Disk를 *200*GB 크기로 확장합니다.

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Managed Disk를 확장하면 업데이트된 크기는 가장 가까운 Managed Disk 크기에 매핑됩니다. 사용 가능한 Managed Disk 크기 및 계층의 테이블은 [Azure Managed Disks 개요 - 가격 책정 및 청구](../../storage/storage-managed-disks-overview.md#pricing-and-billing)를 참조하세요.

3. [az vm start](/cli/azure/vm#start)를 사용하여 VM을 시작합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM을 시작합니다.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. 적절한 자격 증명을 사용하여 VM에 SSH합니다. OS 디스크 크기를 조정했는지 확인하려면 `df -h`을 사용합니다. 다음 예제 출력에서는 데이터 드라이브(*/dev/sdc1*)가 200GB임을 보여 줍니다.

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## 다음 단계
<a id="next-steps" class="xliff"></a>
또한 추가 저장소가 필요한 경우 [Linux VM에 데이터 디스크를 추가](add-disk.md)합니다. 디스크 암호화에 대한 자세한 내용은 [Azure CLI를 사용하여 Linux VM에서 디스크 암호화](encrypt-disks.md)를 참조하세요.

