---
title: "Azure CLI 2.0(미리 보기)로 기존 네트워크에 Linux VM 배포 | Microsoft Docs"
description: "Azure CLI 2.0(미리 보기)를 사용하여 기존 가상 네트워크에 Linux 가상 컴퓨터를 배포하는 방법에 알아봅니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 34e9b401444aeec233d846a6b52f4a452c54cdaf
ms.openlocfilehash: 106571bf36454ab20e75cb4ee42b2aca787a9d5a


---

# <a name="deploy-a-linux-vm-into-an-existing-virtual-network-using-the-azure-cli-20-preview"></a>Azure CLI 2.0(미리 보기)를 사용하여 기존 가상 네트워크에 Linux VM을 배포합니다.

이 문서에서는 Azure CLI 2.0(미리 보기)를 사용하여 기존 가상 네트워크에 VM(가상 컴퓨터)을 배포하는 방법을 보여줍니다. 요구 사항은 다음과 같습니다.

- [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)
- [SSH 공용 및 개인 키 파일](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 클래식 및 리소스 관리 배포 모델용 CLI
- [Azure CLI 2.0(미리 보기)](#quick-commands) - 리소스 관리 배포 모델용 차세대 CLI(이 문서)


## <a name="quick-commands"></a>빠른 명령
작업을 빠르게 완료해야 하는 경우 다음 섹션에서 필요한 명령에 대해 자세히 알아보세요. 각 단계에 대한 보다 자세한 내용 및 상황 설명은 [여기서부터](#detailed-walkthrough) 문서 끝까지 참조하세요.

이 사용자 지정 환경을 만들려면 최신 [Azure CLI 2.0(미리 보기)](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/#login)을 사용하여 로그인해야 합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `myVnet` 및 `myVM`이 포함됩니다.

**사전 요구 사항:** Azure 리소스 그룹, 가상 네트워크 및 서브넷, 인바운드 SSH가 있는 네트워크 보안 그룹, 가상 네트워크 인터페이스 카드.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>가상 네트워크 인프라에 VM 배포

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

## <a name="detailed-walkthrough"></a>자세한 연습

가상 네트워크 및 네트워크 보안 그룹과 같은 Azure 자산이 정적이고 거의 배포되지 않은 수명이 긴 리소스인 것이 좋습니다. 가상 네트워크를 배포하면 인프라에 어떤 부정적인 영향을 주지 않고 새 배포에서 다시 사용할 수 있습니다. 가상 네트워크를 기존 하드웨어 네트워크 스위치라고 생각하면 배포할 때마다 새로운 하드웨어 스위치를 구성하지 않아도 됩니다. 올바르게 구성된 가상 네트워크로 가상 네트워크에 반복하여 가상 네트워크의 수명 동안 필요한 변경 사항을 포함하는 새 VM을 계속 배포할 수 있습니다.

이 사용자 지정 환경을 만들려면 최신 [Azure CLI 2.0(미리 보기)](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/#login)을 사용하여 로그인해야 합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `myVnet` 및 `myVM`이 포함됩니다.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

먼저 Azure 리소스 그룹을 만들어서 연습에서 만드는 모든 항목을 구성합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. [az group create](/cli/azure/group#create)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 `westus` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create \
    --name myResourceGroup \
    --location westus
```

## <a name="create-the-virtual-network"></a>가상 네트워크 만들기

VM가 Azure 가상 네트워크 내에서 시작되도록 빌드합니다. Azure 가상 네트워크에 대한 자세한 내용은 [Azure CLI를 사용하여 가상 네트워크 만들기](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. [az network vnet create](/cli/azure/network/vnet#create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 `myVnet`이라는 가상 네트워크와 `mySubnet`이라는 서브넷을 만듭니다.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --location westus \
    --name myVnet \
    --address-prefix 10.10.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.10.1.0/24
```

## <a name="create-the-network-security-group"></a>네트워크 보안 그룹 만들기

Azure 네트워크 보안 그룹은 네트워크 계층에서 방화벽과 동일합니다. 네트워크 보안 그룹에 대한 자세한 내용은 [Azure CLI에서 네트워크 보안 그룹을 만드는 방법](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. [az network nsg create](/cli/azure/network/nsg#create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-ssh-allow-rule"></a>인바운드 SSH 허용 규칙 추가

Linux VM은 인터넷에서 액세스를 해야 하므로 인바운드 포트 22 트래픽이 Linux VM의 포트 22에 대한 네트워크를 통해 전달되도록 허용하는 규칙이 필요합니다. [az network nsg rule create](/cli/azure/network/nsg/rule#create)를 사용하여 네트워크 보안 그룹에 대해 인바운드 규칙을 추가합니다. 다음 예제는 `myNetworkSecurityGroupRuleSSH`이라는 규칙을 만듭니다.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="attach-the-subnet-to-the-network-security-group"></a>네트워크 보안 그룹에 서브넷 연결

네트워크 보안 그룹 규칙을 서브넷 또는 특정 가상 네트워크 인터페이스에 적용할 수 있습니다. 네트워크 보안 그룹을 서브넷에 연결하게 해줍니다. [az 네트워크 vnet 서브넷 업데이트](/cli/azure/network/vnet/subnet#update)로 서브넷을 네트워크 보안 그룹에 연결합니다.

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="add-a-virtual-network-interface-card-to-the-subnet"></a>가상 네트워크 인터페이스 카드를 서브넷에 추가합니다.

가상 네트워크 인터페이스 카드(VNic)는 다른 VM에 연결하여 다시 사용할 수 있기 때문에 중요합니다. 이렇게 다시 사용하면 VM이 임시 리소스가 되는 동안 VNic를 정적 리소스로 유지할 수 있습니다. VNic를 만들고 [az network nic create](/cli/azure/network/nic#create)로 서브넷에 연결합니다. 다음 예제는 `myNic`라는 VNic를 만듭니다.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>가상 네트워크 인프라에 VM 배포

이제 가상 네트워크, 서브넷 및 네트워크 보안 그룹이 SSH에 대한 포트 22를 제외한 모든 인바운드 트래픽을 차단하여 서브넷을 보호하는 방화벽의 역할을 하게 됩니다. 이제 이 기존 네트워크 인프라 내에 VM을 배포할 수 있습니다.

[az vm create](/cli/azure/vm#create)로 VM을 만듭니다. 전체 VM을 배포하기 위해 Azure CLI 2.0(미리 보기)과 함께 사용하는 플래그에 대한 자세한 내용은 [Azure CLI를 사용하여 전체 Linux 환경 만들기](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

다음 예제는 Azure Managed Disks를 사용하여 VM을 만듭니다. 이들 디스크는 Azure 플랫폼을 통해 처리되며 디스크를 저장할 위치나 준비가 필요하지 않습니다. 관리 디스크에 대한 자세한 내용은 [Azure Managed Disks 개요](../storage/storage-managed-disks-overview.md)를 참조하세요. 관리되지 않는 디스크를 사용하려는 경우 아래의 추가 정보를 참조하세요.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

관리 디스크를 사용하는 경우 이 단계를 건너뜁니다. 관리되지 않는 디스크를 사용하려는 경우 다음과 같은 추가 매개 변수를 진행 명령에 추가하여 `mystorageaccount`라는 저장소 계정에 관리되지 않는 디스크를 만들어야 합니다. 

```azurecli
    --use-unmanaged-disk \
    --storage-account mystorageaccount
```

기존 리소스를 호출하기 위해 CLI 플래그를 사용하여 Azure에서 기존 네트워크 내에 VM을 배포하도록 지시합니다. 다시 말해, 가상 네트워크 및 서브넷이 배포되면 Azure 지역 내에서 정적 또는 영구적으로 리소스로 유지할 수 있습니다. 이 예제에서는 VNic에 공용 IP 주소를 만들어 할당하지 않았기 때문에 이 VM은 인터넷을 통해 공개적으로 액세스할 수 없습니다. 자세한 내용은 [Azure CLI을 사용하여 고정 공용 IP가 있는 VM 만들기](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure에서 가상 컴퓨터를 만드는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Resource Manager 템플릿을 사용하여 특정 배포 만들기](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경 만들기](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [템플릿을 사용하여 Azure에서 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO2-->


