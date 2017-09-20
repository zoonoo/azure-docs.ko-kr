---
title: "여러 NIC를 사용하여 Azure에서 Linux VM 만들기 | Microsoft Docs"
description: "Azure CLI 2.0 또는 Resource Manager 템플릿을 사용하여 여러 NIC가 있는 Linux VM을 만드는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: ff3e3121102eedaa1f439e517570d0a97cf07c22
ms.contentlocale: ko-kr
ms.lasthandoff: 09/02/2017

---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>여러 네트워크 인터페이스 카드를 사용하여 Azure에서 Linux 가상 컴퓨터를 만드는 방법
Azure에서 여러 가상 NIC(네트워크 인터페이스)가 연결된 VM(가상 컴퓨터)을 만들 수 있습니다. 일반적인 시나리오는 프런트 엔드 및 백 엔드 연결에 다른 서브넷을 사용하거나 모니터링 또는 백업 솔루션 전용 네트워크를 두는 것입니다. 이 문서에서는 여러 NIC가 연결되어 있는 VM을 만드는 방법과 기존 VM에서 NIC를 추가 또는 제거하는 방법을 자세히 설명합니다. 자체 Bash 스크립트 내에서 여러 NIC를 만드는 방법을 비롯한 자세한 내용은 [다중 NIC VM 배포](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md)에 대해 자세히 읽어보세요. [VM 크기](sizes.md) 가 다르면 다양한 NIC가 지원되므로 그에 따라 VM 크기를 지정하도록 합니다.

이 문서에서는 Azure CLI 2.0을 사용하여 여러 NIC가 있는 VM을 만드는 방법을 설명합니다. [Azure CLI 1.0](multiple-nics-nodejs.md)에서 이러한 단계를 수행할 수도 있습니다.


## <a name="create-supporting-resources"></a>지원 리소스 만들기
최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *mystorageaccount* 및 *myVM*이 포함됩니다.

먼저 [az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet#create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *myVnet*이라는 가상 네트워크와 *mySubnetFrontEnd*이라는 서브넷을 만듭니다.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#create)를 사용하여 백 엔드 트래픽에 대한 서브넷을 만듭니다. 다음 예제에서는 *mySubnetBackEnd*이라는 서브넷을 만듭니다.

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

[az network nsg create](/cli/azure/network/nsg#create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNetworkSecurityGroup*이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>여러 NIC 만들기 및 구성
[az network nic create](/cli/azure/network/nic#create)를 사용하여 두 개의 NIC를 만듭니다. 다음 예제는 네트워크 보안 그룹에 연결된 *myNic1* 및 *myNic2*라는 2개의 NIC를 만들며 하나의 NIC가 각 서브넷에 연결됩니다.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>VM 만들기 및 NIC 연결
VM을 만들 때 `--nics`로 만든 NIC를 지정합니다. 또한 VM 크기를 선택할 때도 주의해야 합니다. VM에 추가할 수 있는 NIC 총수가 제한되어 있습니다. [Linux VM 크기](sizes.md)에 대해 자세히 읽어보세요. 

[az vm create](/cli/azure/vm#create)로 VM을 만듭니다. 다음 예제에서는 *myVM*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

## <a name="add-a-nic-to-a-vm"></a>VM에 NIC 추가
이전 단계에서는 여러 NIC가 있는 VM을 만들었습니다. Azure CLI 2.0을 사용해서 기존 VM에 NIC를 추가할 수도 있습니다. 

[az network nic create](/cli/azure/network/nic#create)를 사용하여 다른 NIC를 만듭니다. 다음 예제에서는 이전 단계에서 만든 백 엔드 서브넷 및 네트워크 보안 그룹에 연결된 *myNic3*라는 NIC를 만듭니다.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

기존 VM에 NIC를 추가하려면 먼저 [az vm deallocate](/cli/azure/vm#deallocate)를 사용하여 VM을 할당 취소합니다. 다음 예제에서는 *myVM*이라는 VM을 할당 취소합니다.


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[az vm nic add](/cli/azure/vm/nic#add)를 사용하여 NIC를 추가합니다. 다음 예제에서는 *myNic3*를 *myVM*에 추가합니다.

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

[az vm start](/cli/azure/vm#start)를 사용하여 VM을 시작합니다.

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

## <a name="remove-a-nic-from-a-vm"></a>VM에서 NIC 제거
기존 VM에사 NIC를 제거하려면 먼저 [az vm deallocate](/cli/azure/vm#deallocate)를 사용하여 VM을 할당 취소합니다. 다음 예제에서는 *myVM*이라는 VM을 할당 취소합니다.

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[az vm nic remove](/cli/azure/vm/nic#remove)를 사용하여 NIC를 제거합니다. 다음 예제에서는 *myVM*에서 *myNic3*를 제거합니다.

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM 
    --nics myNic3
```

[az vm start](/cli/azure/vm#start)를 사용하여 VM을 시작합니다.

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 여러 NIC 만들기
Azure Resource Manager 템플릿은 선언적 JSON 파일을 사용하여 환경을 정의합니다. [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)에 대해 읽어볼 수 있습니다. Resource Manager 템플릿은 여러 NIC를 만드는 것과 같이 배포하는 동안 리소스의 여러 인스턴스를 만드는 방법을 제공합니다. *복사* 를 사용하여 만들 인스턴스 수를 지정합니다.

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

[*복사*를 사용하여 여러 인스턴스 만들기](../../resource-group-create-multiple.md)에 대해 자세히 읽어보세요. 

`copyIndex()`를 사용하여 리소스 이름에 번호를 추가할 수도 있습니다. 이와 같이 `myNic1`, `myNic2` 등을 만들 수 있습니다. 다음은 인덱스 값을 추가하는 예를 보여 줍니다.

```json
"name": "[concat('myNic', copyIndex())]", 
```

[Resource Manager 템플릿을 사용하여 여러 NIC 만들기](../../virtual-network/virtual-network-deploy-multinic-arm-template.md)의 전체 예제를 읽어볼 수 있습니다.

## <a name="configure-guest-os-for-multiple-nics"></a>여러 NIC에 대한 게스트 OS 구성

Linux 게스트 OS 기반 VM에 여러 NIC를 만들 경우 특정 NIC에 속하는 트래픽을 보내고 받을 수 있는 추가 라우팅 규칙을 만들어야 합니다. 그렇지 않으면 정의된 기본 경로로 인해 eth1에 속하는 트래픽을 올바르게 처리할 수 없습니다.  


### <a name="solution"></a>해결 방법

먼저 /etc/iproute2/rt_tables 파일에 두 개의 라우팅 테이블을 추가합니다.

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

네트워크 스택이 활성화되어 있는 동안 변경 내용을 영구적으로 적용하려면 */etc/sysconfig/network-scipts/ifcfg-eth0* 및 */etc/sysconfig/network-scipts/ifcfg-eth1* 파일을 바꾸어야 합니다.
*"NM_CONTROLLED=yes"* 행을 *"NM_CONTROLLED=no"*로 바꿉니다.
이 단계 없이는 추가할 추가 규칙/라우팅의 효력이 없습니다.
 
다음 단계는 라우팅 테이블을 확장하는 것입니다. 다음 단계가 잘 보이게 하기 위해 다음과 같은 설정을 적용했다고 가정하겠습니다.

*라우팅*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```
    
*인터페이스*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```
    
    
위의 정보를 사용하여 다음과 같은 추가 파일을 루트로 만들 수 있습니다.

*   /etc/sysconfig/network-scripts/rule-eth0
*   /etc/sysconfig/network-scripts/route-eth0
*   /etc/sysconfig/network-scripts/rule-eth1
*   /etc/sysconfig/network-scripts/route-eth1

각 파일의 콘텐츠는 다음과 같습니다.
```bash
cat /etc/sysconfig/network-scripts/rule-eth0
from 10.0.1.4/32 table eth0-rt
to 10.0.1.4/32 table eth0-rt

cat /etc/sysconfig/network-scripts/route-eth0
10.0.1.0/24 dev eth0 table eth0-rt
default via 10.0.1.1 dev eth0 table eth0-rt

cat /etc/sysconfig/network-scripts/rule-eth1
from 10.0.1.5/32 table eth1-rt
to 10.0.1.5/32 table eth1-rt

cat /etc/sysconfig/network-scripts/route-eth1
10.0.1.0/24 dev eth1 table eth1-rt
default via 10.0.1.1 dev eth1 table eth1-rt
```

파일을 만들고 채운 후에 `systemctl restart network` 네트워크 서비스를 다시 시작해야 합니다.

이제 eth0 또는 eth1에 대한 외부에서 연결합니다.

## <a name="next-steps"></a>다음 단계
여러 NIC를 사용하여 VM을 만들려고 할 때 [Linux VM 크기](sizes.md)를 검토합니다. 각 VM 크기가 지원하는 NIC의 최대 수에 유의합니다. 

