---
title: "Azure CLI 2.0을 사용하여 Linux VM 캡처 | Microsoft Docs"
description: "Azure CLI 2.0으로 만든 Managed Disks를 사용하여 Linux 기반 Azure VM(가상 컴퓨터)의 이미지를 캡처하고 일반화하는 방법"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8433f326f04d0585e538695c419e451323c7869b
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-generalize-and-capture-a-linux-virtual-machine"></a>Linux 가상 컴퓨터를 일반화하고 캡처하는 방법
Azure에서 배포되고 구성된 VM(가상 컴퓨터)을 다시 사용하려면 VM의 이미지를 캡처합니다. 프로세스는 또한 이미지에서 새 VM을 배포하기 전에 개인 계정 정보를 제거하도록 VM 일반화를 포함합니다. 이 문서에서는 Azure Managed Disks를 사용하여 VM에 대한 Azure CLI 2.0에서 VM 이미지를 캡처하는 방법을 자세히 설명합니다. 이들 디스크는 Azure 플랫폼을 통해 처리되며 디스크를 저장할 위치나 준비가 필요하지 않습니다. 자세한 내용은 [Azure Managed Disks 개요](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 이 문서에서는 Azure CLI 2.0에서 Linux VM을 캡처하는 방법을 자세히 설명합니다. [Azure CLI 1.0](capture-image-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 이러한 단계를 수행할 수도 있습니다.

> [!TIP]
> 백업 또는 디버깅용의 특수화된 상태로 기존 Linux VM의 복사본을 만들려면 [Azure에서 실행되는 Linux 가상 컴퓨터의 복사본 만들기](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 온-프레미스 VM으로부터 Linux VHD를 업로드하려면 [사용자 지정 디스크 이미지에서 Linux VM 업로드 및 만들기](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.  


## <a name="before-you-begin"></a>시작하기 전에
다음 필수 조건을 충족하는지 확인합니다.

* **Azure VM이 Resource Manager 배포 모델에 생성됨** - Linux VM을 만들지 않은 경우, [포털](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [Resource Manager 템플릿](cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 사용할 수 있습니다. 필요에 따라 VM을 구성합니다. 예를 들어 [데이터 디스크를 추가하고](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), 업데이트를 적용하고, 응용 프로그램을 설치합니다. 

또한 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인해야 합니다.

## <a name="quick-commands"></a>빠른 명령
작업을 빠르게 완료해야 하는 경우 다음 섹션에서 Azure에서 Linux VM의 이미지를 캡처하는 기본 명령에 대해 자세히 알아보세요. 각 단계에 대한 보다 자세한 내용 및 상황 설명은 [여기](#detailed-steps)서부터 문서 끝까지 참조하세요. 다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `myVM` 및 `myImage`가 포함됩니다.

1. 원본 VM 프로비전을 해제합니다.

    ```bash
    ssh ops@myvm.westus.cloudapp.azure.com
    sudo waagent -deprovision+user -force
    exit
    ```

2. [az vm deallocate](/cli/azure/vm#deallocate)로 VM의 할당을 취소합니다.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. [az vm generalize](/cli/azure/vm#generalize)로 VM을 일반화합니다.
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

4. [az image create](/cli/azure/image#create)로 VM 리소스에서 이미지를 만듭니다.
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```

5. [az vm create](/cli/azure/vm#create)로 이미지 리소스에서 VM을 만듭니다.

    ```azurecli
    az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="detailed-steps"></a>자세한 단계
다음 단계에서는 기존 VM을 프로비전 해제하고 할당 취소하고 VM 리소스를 일반화한 다음 이미지를 만듭니다. 이 이미지를 사용하여 구독 내의 모든 리소스 그룹에서 VM을 만들 수 있습니다. 이 프로세스를 통해 [Azure Managed Disks](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)는 관리되지 않는 디스크보다 유리하게 됩니다. 관리되지 않는 디스크의 경우 기본 VHD(가상 하드 디스크)의 Blob 복사본을 만든 다음 복사한 VHD Blob과 동일한 저장소 계정에 VM 만들기로 제한합니다. 관리되는 디스크의 경우 전체 구독에서 배포될 수 있는 이미지 리소스를 만듭니다.

## <a name="step-1-remove-the-azure-linux-agent"></a>1단계: Azure Linux 에이전트를 제거합니다.
VM 일반화를 준비하려면 Azure VM 에이전트를 사용하여 파일 및 데이터를 삭제하도록 VM을 프로비전 해제합니다. 대상 Linux VM에서 **deprovision** 매개 변수와 함께 **waagent** 명령을 사용합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

1. SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
2. SSH 창에서 다음 명령을 입력합니다.
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > 이미지로 캡처하려는 VM에서만 이 명령을 실행합니다. 이 과정이 이미지에서 중요한 정보가 모두 지워졌다거나 재배포에 적합하다는 것을 보장하지는 않습니다.
 
3. 계속하려면 **y** 를 입력합니다. 이 확인 단계를 피하려면 **-force** 매개 변수를 추가합니다.
4. 명령이 완료된 후 **exit**를 입력합니다. 이 단계는 SSH 클라이언트를 닫습니다.

## <a name="step-2-capture-the-vm"></a>2단계: VM 캡처
Azure CLI 2.0을 사용하여 VM을 일반화하고 캡처합니다. 다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 **myResourceGroup**, **myVnet**, **myVM**이 포함됩니다.

1. [az vm deallocate](/cli//azure/vm#deallocate)로 프로비전 해제한 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다.
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. [az vm generalize](/cli//azure/vm#generalize)로 VM을 일반화합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 일반화합니다.
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

3. 이제 [az image create](/cli//azure/image#create)로 VM 리소스의 이미지를 만듭니다. 다음 예제에서는 `myVM`이라는 VM 리소스를 사용하여 `myResourceGroup`이라는 리소스 그룹에서 `myImage`라는 이미지를 만듭니다.
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```
   
   > [!NOTE]
   > 이미지는 원본 VM과 동일한 리소스 그룹에 만들어집니다. 이 이미지에서 구독 내의 모든 리소스 그룹에 VM을 만들 수 있습니다. 관리 측면에서 VM 리소스 및 이미지에 대한 특정 리소스 그룹을 만들 수도 있습니다.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3단계: 캡처한 이미지로부터 새 VM 만들기
[az vm create](/cli/azure/vm#create)로 만든 이미지를 사용하여 VM을 만듭니다. 다음 예제에서는 이미지 `myImage`에서 `myVMDeployed`라는 VM을 만듭니다.

```azurecli
az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```

관리되는 디스크의 경우 구독 내 모든 리소스 그룹의 이미지에서 VM을 만들 수 있습니다. 이 동작은 원본 VHD와 동일한 저장소 계정에만 VM을 만들 수 있는 관리되지 않는 디스크에서 변경된 내용입니다. 이미지와 다른 리소스 그룹에 VM을 만들려면 이미지에 완전한 리소스 ID를 지정합니다. [az image list](/cli/azure/image#list)를 사용하여 이미지 목록을 봅니다. 다음 예제와 유사하게 출력됩니다.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

다음 예제에서는 이미지 리소스 ID를 지정하여 원본 이미지와 다른 리소스 그룹에 VM을 만드는 **az vm create**를 사용합니다.

```azurecli
az vm create --resource-group myOtherResourceGroup --name myOtherVMDeployed 
    --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage"
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```


### <a name="verify-the-deployment"></a>배포 확인
생성한 가상 컴퓨터에 SSH를 실행하여 배포를 확인하고 새 VM 사용을 시작합니다. SSH를 통해 연결하려면 [az vm show](/cli/azure/vm#show)로 VM의 IP 주소 또는 FQDN을 찾습니다.

```azurecli
az vm show --resource-group myResourceGroup --name myVM --show-details
```

## <a name="next-steps"></a>다음 단계
원본 VM 이미지에서 여러 VM을 만들 수 있습니다. 이미지를 변경해야 하는 경우: 

- 사용자 이미지에서 VM을 만듭니다.
- 모든 업데이트 또는 구성 변경 내용을 확인합니다.
- 단계에 따라 이미지를 다시 프로비전 해제, 할당 취소, 일반화하고 만듭니다.
- 향후 배포에서 이 새로운 이미지를 사용합니다. 원하는 경우에 원본 이미지를 삭제합니다.

CLI를 사용하여 VM을 관리하는 방법에 대한 자세한 내용은 [Azure CLI 2.0](/cli/azure/overview)을 참조하세요.

