---
title: "여러 NIC를 사용하여 Linux VM 만들기 | Microsoft Docs"
description: "Azure CLI 또는 Resource Manager 템플릿을 사용하여 여러 NIC가 연결된 Linux VM을 만드는 방법을 알아봅니다."
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
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: d4fa4187b25dcbb7cf3b75cb9186b5d245c89227
ms.openlocfilehash: 12da49e49782869153dcecbf6e4ca0ec24fa5960


---
# <a name="creating-a-linux-vm-with-multiple-nics"></a>여러 NIC가 연결된 Linux VM 만들기
Azure에서 여러 가상 NIC(네트워크 인터페이스)가 연결된 VM(가상 컴퓨터)을 만들 수 있습니다. 일반적인 시나리오는 프런트 엔드 및 백 엔드 연결에 다른 서브넷을 사용하거나 모니터링 또는 백업 솔루션 전용 네트워크를 두는 것입니다. 이 문서에서는 여러 NIC가 연결된 VM을 만드는 빠른 명령을 제공합니다. 자체 Bash 스크립트 내에서 여러 NIC를 만드는 방법을 비롯한 자세한 내용은 [다중 NIC VM 배포](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)에 대해 자세히 읽어보세요. [VM 크기](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 가 다르면 다양한 NIC가 지원되므로 그에 따라 VM 크기를 지정하도록 합니다.

> [!WARNING]
> VM을 만들 때 여러 NIC를 연결해야 합니다. 기존 VM에 NIC를 추가할 수 없습니다. [원래 가상 디스크를 기반으로 VM을 만들고](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM을 배포할 때 여러 NIC를 만들 수 있습니다.
> 
> 

## <a name="quick-commands"></a>빠른 명령
[Azure CLI](../xplat-cli-install.md)에 로그인되어 있고 리소스 관리자 모드를 사용 중인지 확인합니다.

```azurecli
azure config mode arm
```

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `mystorageaccount` 및 `myVM`가 포함됩니다.

먼저 리소스 그룹을 만듭니다. 다음 예제에서는 `WestUS` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
azure group create myResourceGroup -l WestUS
```

VM을 보유할 저장소 계정을 만듭니다. 다음 예제에서는 `mystorageaccount`라는 저장소 계정을 만듭니다.

```azurecli
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

VM을 연결할 가상 네트워크를 만듭니다. 다음 예제는 주소 접두사 `192.168.0.0/16`으로 `myVnet`이라는 가상 네트워크를 만듭니다.

```azurecli
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

두 개의 가상 네트워크 서브넷(프런트 엔드 트래픽용 및 백 엔드 트래픽용)을 만듭니다. 다음 예제는 `mySubnetFrontEnd` 및 `mySubnetBackEnd`라는 두 개의 서브넷을 만듭니다.

```azurecli
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

2개의 NIC를 만들고 한 NIC를 프런트 엔드 서브넷에, 다른 NIC를 백 엔드 서브넷에 연결합니다. 다음 예제는 `myNic1` 및 `myNic2`라는 두 개의 NIC를 만들어서 서브넷에 연결합니다.

```azurecli
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic1 -m myVnet -k mySubnetFrontEnd
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic2 -m myVnet -k mySubnetBackEnd
```

마지막으로 VM을 만들고 이전에 만든 두 개의 NIC를 연결합니다. 다음 예제는 `myVM`이라는 VM을 만듭니다.

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-azure-cli"></a>Azure CLI를 사용하여 여러 NIC 만들기
이전에 Azure CLI를 사용하여 VM을 만든 경우 빠른 명령을 잘 알고 있어야 합니다. 이 프로세스는 NIC를 1개 만들 경우와 여러 개 만들 경우에 동일합니다. 모든 NIC 생성을 반복하는 프로세스 스크립팅을 포함하여 [Azure CLI를 사용하여 여러 NIC 배포](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)에 대한 자세한 정보를 읽을 수 있습니다.

다음 예제는 `myNic1` 및 `myNic2`라는 2개의 NIC를 만들며 하나의 NIC가 각 서브넷에 연결됩니다.

```azurecli
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

또한 일반적으로 [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md) 또는 [부하 분산 장치](../load-balancer/load-balancer-overview.md)를 만들어 VM에서 트래픽을 관리하고 분산합니다. 이 명령 또한 여러 NIC로 작업할 때와 같습니다. 다음 예제는 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

`azure network nic set`을 사용하여 NIC를 네트워크 보안 그룹에 바인딩합니다. 다음 예제는 `myNic1` 및 `myNic2`를 `myNetworkSecurityGroup`과 바인딩합니다.

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

VM을 만들 때 이제 여러 NIC를 지정합니다. `--nic-name`을 사용하여 단일 NIC를 제공하는 대신, `--nic-names`를 사용하고 쉼표로 구분된 NIC 목록을 제공합니다. 또한 VM 크기를 선택할 때도 주의해야 합니다. VM에 추가할 수 있는 NIC 총수가 제한되어 있습니다. [Linux VM 크기](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 대해 자세히 읽어보세요. 다음 예제에서는 여러 NIC를 지정하는 방법과 여러 NIC의 사용을 지원하는 VM 크기를 보설명합니다(`Standard_DS2_v2`).

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 여러 NIC 만들기
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




<!--HONumber=Jan17_HO1-->


