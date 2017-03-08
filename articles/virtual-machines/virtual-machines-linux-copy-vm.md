---
title: "Azure CLI 2.0을 사용하여 Linux VM 복사 | Microsoft Docs"
description: "Azure CLI 2.0(미리 보기)을 사용하여 Resource Manager 배포 모델에서 Azure Linux VM의 복사본을 만드는 방법에 대해 알아보기"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1beaca707bfc5058083213b564b3898545f57556
ms.openlocfilehash: edf98951d4ed6709a9894e36ec5ae80b589dd639
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-preview"></a>Azure CLI 2.0(미리 보기)을 사용하여 Linux VM 복사본 만들기
이 문서에서는 Azure Resource Manager 배포 모델에서 Linux를 실행하는 Azure VM(가상 컴퓨터)의 복사본을 만드는 방법에 대해 설명합니다.

운영 체제와 데이터 디스크를 새 컨테이너로 복사한 다음 네트워크 리소스를 설정하고 VM을 만듭니다.

[사용자 지정 디스크 이미지에서 VM 업로드 및 만들](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)수도 있습니다.

## <a name="cli-versions"></a>CLI 버전
다음 CLI(명령줄 인터페이스) 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

* [Azure CLI 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): 클래식 및 리소스 관리 배포 모델의 경우입니다.
* Azure CLI 2.0(미리 보기): 이 문서에서 다루는 리소스 관리 배포 모델용 차세대 CLI입니다.

## <a name="prerequisites"></a>필수 조건
* [Azure CLI 2.0(미리 보기)](/cli/azure/install-az-cli2)는 [az login](/cli/azure/#login)을 사용하여 설치되고 Azure 계정에 로그인됩니다.
* 복사본에 대한 원본으로 사용할 Azure VM입니다.

## <a name="step-1-stop-the-source-vm"></a>1단계: 원본 VM 중지
[az vm deallocate](/cli/azure/vm#deallocate)를 사용하여 원본 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다.

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>2단계: 원본 VM 복사
VM을 복사하려면 기본 가상 하드 디스크의 복사본을 만듭니다. 이 프로세스를 통해 원본 VM과 동일한 구성 및 설정을 포함하는 특수한 VM을 만들 수 있습니다.

가상 디스크를 복사하는 프로세스는 Azure Managed Disks와 관리되지 않는 디스크가 다릅니다. Managed Disks는 Azure 플랫폼을 통해 처리되며 디스크를 저장할 위치나 준비가 필요하지 않습니다. Managed Disks가 최상위 리소스이기 때문에 쉽게 작업할 수 없습니다. 즉, 디스크 리소스의 직접 복사본을 만들 수 있습니다.

Azure Managed Disks에 대한 자세한 내용은 [Azure Managed Disks 개요](../storage/storage-managed-disks-overview.md)를 참조하세요.

원본 VM에 저장소 유형에 따라 다음 두 섹션 중 한 가지의 지침을 수행한 다음 "3단계: 가상 네트워크 설정"으로 이동합니다.

### <a name="managed-disks"></a>Managed Disks

1. [az vm list](/cli/azure/vm#list)를 사용하여 각 VM 및 해당 OS Managed Disks의 이름을 나열합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 모든 VM을 나열합니다.

    ```azurecli
    az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
    ```

    다음 예제와 유사하게 출력됩니다.

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
```

2. 디스크를 복사하려면 [az disk create](/cli/azure/disk#create)를 사용하여 새로운 Managed Disk를 만듭니다. 다음 예제에서는 Managed Disks `myDisk`에서 디스크 `myCopiedDisk`를 만듭니다.

    ```azurecli
    az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
    ```

3. 이제 [az disk list](/cli/azure/disk#list)를 사용하여 리소스 그룹의 Managed Disks를 확인합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 Managed Disks를 나열합니다.

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```

4. ["3단계: 가상 네트워크 설정"](#set-up-the-virtual-network)으로 건너뜁니다.


### <a name="unmanaged-disks"></a>관리되지 않는 디스크

1. 가상 하드 디스크의 복사본을 만들려면 Azure Storage 계정 키 및 디스크의 URI가 필요합니다. 저장소 계정 키를 보려면 [az storage account keys list](/cli/azure/storage/account/keys#list)를 사용합니다.

 다음 예제에서는 리소스 그룹 `myResourceGroup`에 저장소 계정 `mystorageaccount`에 대한 키를 나열합니다.

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --name mystorageaccount --output table
    ```

 다음 예제와 유사하게 출력됩니다.

    ```azurecli
    KeyName    Permissions    Value
    ---------  -------------  ----------------------------------------------------------------------------------------
    key1       Full           gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug==
    key2       Full           UMoyQjWuKCBUAVDr1ANRe/IWTE2o0ZdmQH2JSZzXKNmDKq83368Fw9nyTBcTEkSKI9cm5tlTL8J15ArbPMo8eA==
    ```

2. VM 및 해당 URI의 목록을 보려면 [az vm list](/cli/azure/vm#list)를 사용합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM을 나열합니다.

    ```azurecli
    az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
    ```

    다음 예제와 유사하게 출력됩니다.

    ```azurecli
    Name    URI
    ------  -------------------------------------------------------------
    myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
    ```

3. [az storage blob copy start](/cli/azure/storage/blob/copy#start)를 사용하여 가상 하드 디스크를 복사합니다. 필수 저장소 계정 키 및 가상 하드 디스크 URI를 제공하려면 `az storage account keys` 및 `az vm` 목록의 정보를 사용합니다.

    ```azurecli
    az storage blob copy start \
        --account-name mystorageaccount \
        --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
        --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
        --destination-container vhds --destination-blob myCopiedVHD.vhd
    ```

## <a name="step-3-set-up-a-virtual-network"></a>3단계: 가상 네트워크 설정
다음 선택적 단계는 새 가상 네트워크, 서브넷, 공용 IP 주소 및 가상 NIC(네트워크 인터페이스 카드)를 만듭니다.

문제 해결 목적 또는 추가 배포를 위해 VM을 복사하는 경우 기존 가상 네트워크의 VM을 사용하지 않으려 할 수 있습니다.

복사된 VM에 대한 가상 네트워크 인프라를 만들려는 경우 다음 몇 단계를 수행합니다. 가상 네트워크를 만들지 않으려면 ["4단계: VM 만들기](#create-a-vm)"로 건너뜁니다.

1. [az network vnet create](/cli/azure/network/vnet#create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 가상 네트워크 `myVnet` 및 두 개의 서브넷 `mySubnet`을 만듭니다.

    ```azurecli
    az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
        --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
    ```

2. [az network public-ip create](/cli/azure/network/public-ip#create)를 사용하여 공용 IP를 만듭니다. 다음 예제에서는 `mypublicdns`라는 DNS 이름으로 `myPublicIP`라는 공용 IP를 만듭니다. DNS 이름은 고유해야 하므로 고유한 이름을 제공하세요.

    ```azurecli
    az network public-ip create --resource-group myResourceGroup --location westus \
        --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
    ```

3. [az network nic create](/cli/azure/network/nic#create)를 사용하여 NIC를 만듭니다. 다음 예제에서는 `mySubnet` 서브넷에 연결된 `myNic`라는 NIC를 만듭니다.

    ```azurecli
    az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
        --vnet-name myVnet --subnet mySubnet
    ```

## <a name="step-4-create-a-vm"></a>4단계: VM 만들기
이제 [az vm create](/cli/azure/vm#create)를 사용하여 VM을 만들 수 있습니다. 디스크를 복사하는 경우 Managed Disks와 관리되지 않는 디스크 간에 프로세스는 약간 다릅니다. 원본 VM에 저장소 유형에 따라 다음 두 섹션 중 하나의 지침을 수행합니다.

### <a name="managed-disks"></a>Managed Disks
1. [az vm create](/cli/azure/vm#create)를 사용하여 VM을 만듭니다.
2. 다음과 같이 복사된 관리되는 디스크를 OS 디스크(`--attach-os-disk`)로 사용하도록 지정합니다.

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image UbuntuLTS
        --attach-os-disk myCopiedDisk
    ```

### <a name="unmanaged-disks"></a>관리되지 않는 디스크
1. [az vm create](/cli/azure/vm#create)로 VM을 만듭니다.
2. 다음과 같이 `az storage blob copy start`(`--image`)을 사용하여 복사한 디스크를 만들 때 사용한 저장소 계정, 컨테이너 이름 및 가상 하드 디스크를 지정합니다.

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM  \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
        --use-unmanaged-disk
    ```

## <a name="next-steps"></a>다음 단계
Azure CLI를 사용하여 새 VM을 관리하는 방법을 알아보려면 [Azure Resource Manager의 Azure CLI 명령](azure-cli-arm-commands.md)을 참조하세요.

