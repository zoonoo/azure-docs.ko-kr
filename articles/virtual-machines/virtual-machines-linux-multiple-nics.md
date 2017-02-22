---
title: "Azure CLI 2.0(미리 보기)을 사용하여 다중 NIC가 있는 Linux VM 만들기 | Microsoft Docs"
description: "Azure CLI 2.0(미리 보기) 또는 Resource Manager 템플릿을 사용하여 여러 NIC가 연결된 Linux VM을 만드는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 368c79b001495e0fb000a4b280023b2299256435
ms.openlocfilehash: a854a15a9119f289344a75638d1042ee6779bb46


---
# <a name="create-a-linux-vm-with-multiple-nics-using-the-azure-cli-20-preview"></a>Azure CLI 2.0(미리 보기)을 사용하여 여러 NIC가 있는 Linux VM 만들기
Azure에서 여러 가상 NIC(네트워크 인터페이스)가 연결된 VM(가상 컴퓨터)을 만들 수 있습니다. 일반적인 시나리오는 프런트 엔드 및 백 엔드 연결에 다른 서브넷을 사용하거나 모니터링 또는 백업 솔루션 전용 네트워크를 두는 것입니다. 이 문서에서는 여러 NIC가 연결된 VM을 만드는 빠른 명령을 제공합니다. 자체 Bash 스크립트 내에서 여러 NIC를 만드는 방법을 비롯한 자세한 내용은 [다중 NIC VM 배포](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)에 대해 자세히 읽어보세요. [VM 크기](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 가 다르면 다양한 NIC가 지원되므로 그에 따라 VM 크기를 지정하도록 합니다.

> [!WARNING]
> VM을 만들 때 여러 NIC를 연결해야 합니다. 기존 VM에 NIC를 추가할 수 없습니다. [가상 디스크에 따라 VM을 만들고](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM을 배포할 때 여러 NIC를 만들 수 있습니다.


## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](virtual-machines-linux-multiple-nics-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 클래식 및 리소스 관리 배포 모델용 CLI
- [Azure CLI 2.0(미리 보기)](#create-supporting-resources) - 리소스 관리 배포 모델용 차세대 CLI(이 문서)


## <a name="create-supporting-resources"></a>지원 리소스 만들기
최신 [Azure CLI 2.0(미리 보기)](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `mystorageaccount` 및 `myVM`가 포함됩니다.

먼저 [az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 `WestUS` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westus
```

[az network vnet create](/cli/azure/network/vnet#create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 `myVnet`이라는 가상 네트워크와 `mySubnetFrontEnd`이라는 서브넷을 만듭니다.

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnetFrontEnd --subnet-prefix 192.168.1.0/24
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#create)를 사용하여 백 엔드 트래픽에 대한 서브넷을 만듭니다. 다음 예제는 `mySubnetBackEnd`이라는 서브넷을 만듭니다.

```azurecli
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVnet \
    --name mySubnetBackEnd --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>여러 NIC 만들기 및 구성
모든 NIC 생성을 반복하는 프로세스 스크립팅을 포함하여 [Azure CLI를 사용하여 여러 NIC 배포](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)에 대한 자세한 정보를 읽을 수 있습니다.

일반적으로 [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md) 또는 [부하 분산 장치](../load-balancer/load-balancer-overview.md)를 만들어 VM에서 트래픽을 관리하고 분산합니다. [az network nsg create](/cli/azure/network/nsg#create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create --resource-group myResourceGroup \
  --name myNetworkSecurityGroup
```

[az network nic create](/cli/azure/network/nic#create)를 사용하여 두 개의 NIC를 만듭니다. 다음 예제는 네트워크 보안 그룹에 연결된 `myNic1` 및 `myNic2`라는&2;개의 NIC를 만들며 하나의 NIC가 각 서브넷에 연결됩니다.

```azurecli
az network nic create --resource-group myResourceGroup --name myNic1 \
  --vnet-name myVnet --subnet mySubnetFrontEnd \
  --network-security-group myNetworkSecurityGroup
az network nic create --resource-group myResourceGroup --name myNic2 \
  --vnet-name myVnet --subnet mySubnetBackEnd \
  --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>VM 만들기 및 NIC 연결
VM을 만들 때 `--nics`로 만든 NIC를 지정합니다. 또한 VM 크기를 선택할 때도 주의해야 합니다. VM에 추가할 수 있는 NIC 총수가 제한되어 있습니다. [Linux VM 크기](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 대해 자세히 읽어보세요. 

[az vm create](/cli/azure/vm#create)로 VM을 만듭니다. 다음 예제는 [Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 사용하여 `myVM`이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic1 myNic2
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 여러 NIC 만들기
Azure Resource Manager 템플릿은 선언적 JSON 파일을 사용하여 환경을 정의합니다. [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)에 대해 읽어볼 수 있습니다. Resource Manager 템플릿은 여러 NIC를 만드는 것과 같이 배포하는 동안 리소스의 여러 인스턴스를 만드는 방법을 제공합니다. *복사* 를 사용하여 만들 인스턴스 수를 지정합니다.

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

[*복사*를 사용하여 여러 인스턴스 만들기](../azure-resource-manager/resource-group-create-multiple.md)에 대해 자세히 읽어보세요. 

`copyIndex()`를 사용하여 리소스 이름에 번호를 추가할 수도 있습니다. 이와 같이 `myNic1`, `myNic2` 등을 만들 수 있습니다. 다음은 인덱스 값을 추가하는 예를 보여 줍니다.

```json
"name": "[concat('myNic', copyIndex())]", 
```

[Resource Manager 템플릿을 사용하여 여러 NIC 만들기](../virtual-network/virtual-network-deploy-multinic-arm-template.md)의 전체 예제를 읽어볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
여러 NIC를 사용하여 VM을 만들려고 할 때 [Linux VM 크기](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 를 검토해야 합니다. 각 VM 크기가 지원하는 NIC의 최대 수에 유의합니다. 

기존 VM에 NIC를 더 추가할 수 없으므로 VM을 배포할 때 모든 NIC를 만들어야 합니다. 배포를 계획할 때 처음부터 필요한 모든 네트워크 연결이 있는지 확인합니다.




<!--HONumber=Feb17_HO2-->


