---
title: 여러 서브넷이 있는 Azure Virtual Network 만들기 - Azure CLI | Microsoft Docs
description: Azure CLI를 사용하여 여러 서브넷이 있는 가상 네트워크를 만드는 방법을 알아봅니다.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 0b0bfae02147910d98231d7c93f5ab260f26364f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880533"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>Azure CLI를 사용하여 여러 서브넷이 있는 가상 네트워크 만들기

가상 네트워크를 사용하면 여러 유형의 Azure 리소스가 인터넷과 통신할 수 있고 서로 비공개적으로 통신할 수 있습니다. 가상 네트워크에 여러 서브넷을 만들면 네트워크를 분할하여 서브넷 간의 트래픽 흐름을 필터링하거나 제어할 수 있습니다. 이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * 서브넷 만들기
> * 가상 머신 간의 네트워크 통신 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli.md)를 참조하세요. 

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제는 주소 접두사 *10.0.0.0/16*을 사용하는 *myVirtualNetwork*라는 가상 네트워크를 만듭니다. 명령을 통해 주소 접두사가 *10.0.0.0/24*인 *Public*이라는 서브넷이 하나 만들어집니다.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

이전 명령에서 위치가 지정되지 않았기 때문에 Azure는 *myResourceGroup* 리소스 그룹과 동일한 위치에 가상 네트워크를 만듭니다. **address-prefixes**와 **subnet-prefix**는 CIDR 표기법으로 지정됩니다. 지정된 주소 접두사는 IP 주소 10.0.0.0-10.0.255.254를 포함합니다. 서브넷에 지정된 접두사는 가상 네트워크에 대해 정의된 주소 접두사 내에 있어야 합니다. Azure DHCP는 서브넷 주소 접두사의 IP 주소를 서브넷에 배포된 리소스에 할당합니다. Azure는 **Public** 서브넷에 배포된 리소스에 10.0.0.4-10.0.0.254 주소만 할당합니다. Azure에서 각 서브넷에 앞쪽 주소 4개(이 예제의 경우 서브넷에 10.0.0.0-10.0.0.3)와 마지막 주소(이 예제의 경우 서브넷에 10.0.0.255)를 예약하기 때문입니다.

## <a name="create-a-subnet"></a>서브넷 만들기

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 서브넷을 만듭니다. 다음 예제는 주소 접두사가 *10.0.1.0/24*인 *myVirtualNetwork* 가상 네트워크 내에 *Private*이라는 서브넷을 만듭니다. 주소 접두사는 가상 네트워크에 대해 정의된 주소 접두사 내에 있어야 하며, 가상 네트워크에 있는 다른 서브넷의 주소 접두사와 겹칠 수 없습니다.

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

프로덕션용으로 Azure 가상 네트워크와 서브넷을 배포하기 전에 주소 공간 [고려 사항](manage-virtual-network.md#create-a-virtual-network) 및 [가상 네트워크 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 철저히 숙지하는 것이 좋습니다. 리소스가 서브넷에 배포된 후에, 일부 가상 네트워크 및 서브넷이 변경되어(예: 주소 범위 변경) 서브넷에 배포된 기존 Azure 리소스를 다시 배포하는 것이 필요할 수 있습니다.

## <a name="test-network-communication"></a>네트워크 통신 테스트

가상 네트워크를 사용하면 여러 유형의 Azure 리소스가 인터넷과 통신할 수 있고 서로 비공개적으로 통신할 수 있습니다. 가상 네트워크에 배포할 수 있는 한 가지 유형의 리소스는 가상 머신입니다. 가상 네트워크에 가상 머신을 두 개 만들어서 나중에 인터넷 및 가상 머신 사이의 네트워크 통신을 테스트 할 수 있습니다.

### <a name="create-virtual-machines"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)로 가상 머신을 만듭니다. 다음 예제에서는 *Public* 서브넷에 *myVmWeb*이라는 가상 머신을 만듭니다. `--no-wait` 매개 변수를 사용하면 Azure가 백그라운드에서 명령을 실행할 수 있으므로 다음 명령을 계속 진행할 수 있습니다. 자습서를 간소화하기 위해 암호가 사용됩니다. 일반적으로 키는 프로덕션 배포에 사용됩니다. 키를 사용하는 경우 나머지 단계를 완료하도록 SSH 에이전트 전달도 구성해야 합니다. SSH 에이전트 전달에 대한 자세한 내용은 SSH 클라이언트에 대한 설명서를 참조하세요. 다음 명령에서 `<replace-with-your-password>`를 원하는 암호로 바꿉니다.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Azure는 10.0.0.4를 가상 머신의 개인 IP 주소로 자동 할당했습니다. *Public* 서브넷에서 사용 가능한 첫 번째 IP 주소가 10.0.0.4이기 때문입니다. 

*Private* 서브넷에 가상 머신을 만듭니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```
가상 머신을 만드는 데 몇 분 정도 걸립니다. 가상 머신을 만든 후 Azure CLI는 다음 예제와 비슷한 출력을 반환합니다. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

이 예제의 출력에는 **privateIpAddress**가 *10.0.1.4*로 표시됩니다. Azure에서 [네트워크 인터페이스](virtual-network-network-interface.md)를 만들어서 가상 머신에 연결하고 네트워크 인터페이스에 개인 IP 주소와 **macAddress**를 할당했습니다. Azure DHCP는 네트워크에 10.0.1.4를 자동으로 할당했습니다. 이것이 *Private* 서브넷에서 첫 번째로 사용 가능한 IP 주소이기 때문입니다. 개인 IP와 MAC 주소는 네트워크 인터페이스가 삭제될 때까지 네트워크 인터페이스에 할당된 상태로 유지됩니다. 

**publicIpAddress**를 기록해 둡니다. 이 주소는 이후 단계에서 인터넷을 통해 가상 컴퓨터에 액세스하는 데 사용됩니다. 가상 머신에 공용 IP 주소가 할당되어 있어야 하는 것은 아니지만 Azure는 만들어진 가상 머신마다 공용 IP 주소를 기본적으로 할당합니다. 인터넷에서 가상 머신으로 통신하려면 가상 머신에 공용 IP 주소를 할당해야 합니다. 가상 머신에 공용 IP 주소가 할당되어 있는지 여부와 상관없이 모든 가상 머신은 인터넷으로 아웃 바운드 통신이 가능합니다. Azure의 아웃바운드 인터넷 연결에 대해 자세히 알아보려면 [Azure에서 아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

이 문서에서 만든 가상 머신에는 네트워크 인터페이스에 동적으로 할당된 하나의 IP 주소가 있는 [네트워크 인터페이스](virtual-network-network-interface.md)가 하나 있습니다. VM을 배포한 후에는 [공용 및 비공개 IP 주소를 여러 개 추가하거나 IP 주소 할당 방법을 정적으로 변경](virtual-network-network-interface-addresses.md#add-ip-addresses)할 수 있습니다. 가상 머신을 만들 때 선택한 [VM 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)로 지원되는 한도까지 [네트워크 인테페이스를 추가](virtual-network-network-interface-vm.md#vm-add-nic)할 수 있습니다. VM에 대해 [SR-IOV(단일 루트 I/O 가상화)를 사용하도록 설정](create-vm-accelerated-networking-cli.md)할 수 있지만 용량을 지원하는 VM 크기로 VM을 만들 때만 가능합니다.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>가상 머신 간 통신 및 인터넷 통신

다음 명령을 사용하여 *myVmMgmt* 가상 머신과의 SSH 세션을 만듭니다. `<publicIpAddress>`를 가상 머신의 공용 IP 주소로 바꿉니다. 이전 예제의 공용 IP 주소는*13.90.242.231*입니다. 암호를 입력하라는 메시지가 표시되면 [가상 머신 만들기](#create-virtual-machines)에서 입력한 암호를 입력합니다.

```bash 
ssh azureuser@<publicIpAddress>
```

보안상의 이유로 가상 네트워크에서 원격으로 연결할 수 있는 가상 머신의 수를 제한하는 것이 일반적입니다. 이 자습서에서는 가상 네트워크의 *myVmWeb* 가상 머신을 관리하는 데 *myVmMgmt* 가상 머신이 사용됩니다. *myVmMgmt* 가상 머신에서 *myVmWeb* 가상 머신에 SSH를 설정하려면 다음 명령을 사용합니다.

```bash 
ssh azureuser@myVmWeb
```

*myVmWeb* 가상 머신에서 *myVmMgmt* 가상 머신으로 통신하려면 명령 프롬프트에 다음 명령을 입력합니다.

```
ping -c 4 myvmmgmt
```

다음 예제 출력과 유사한 출력이 표시됩니다.
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
*myVmMgmt* 가상 머신의 주소가 10.0.1.4로 표시됩니다. 10.0.1.4는 이전 단계에서 *myVmMgmt* 가상 머신을 배포한 *Private* 서브넷의 주소 범위에서 첫 번째로 사용 가능한 IP 주소입니다.  가상 머신의 정규화된 도메인 이름이 *myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net*으로 표시됩니다. 도메인 이름의 *hxehizax3z1udjnrx1r4gr30pg* 부분은 가상 머신과 다르지만 도메인 이름의 나머지 부분은 동일합니다. 기본적으로 모든 Azure 가상 머신은 기본 Azure DNS 서비스를 사용합니다. 가상 네트워크 내의 모든 가상 머신은 Azure의 기본 DNS 서비스를 사용하여 동일한 가상 네트워크에 있는 다른 모든 가상 머신의 이름을 확인할 수 있습니다. Azure의 기본 DNS 서비스를 사용하는 대신 자신의 DNS 서버 또는 Azure DNS 서비스의 비공개 도메인 기능을 사용할 수 있습니다. 자세한 내용은 [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)이나 [개인 도메인에 Azure DNS 사용](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

다음 명령을 사용하여 *myVmWeb* 가상 머신에 nginx 웹 서버를 설치합니다.

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

nginx가 설치되면 *myVmWeb* SSH 세션을 닫습니다. 그러면 *myVmMgmt* 가상 머신에 대한 프롬프트가 표시됩니다. 다음 명령을 실행하여 *myVmWeb* 가상 머신의 nginx 시작 화면을 검색합니다.

```bash
curl myVmWeb
```

nginx 시작 화면이 반환됩니다.

*myVmMgmt* 가상 머신으로 SSH 세션을 닫습니다.

Azure에서 *myVmWeb* 가상 머신이 만들어질 때 *myVmWebPublicIP*라는 공용 IP 주소도 만들어지고 가상 머신에 할당됩니다. [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용하여 Azure가 할당한 공용 주소를 가져옵니다.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

자신의 컴퓨터에서 다음 명령의 `<publicIpAddress>`를 이전 명령에서 반환된 주소로 바꿔서 입력합니다.

```bash
curl <publicIpAddress>
```

자신의 컴퓨터에서 nginx 환영 화면을 curl하려는 시도가 실패합니다. 가상 머신이 배포될 때 Azure에서 기본적으로 가상 머신마다 네트워크 보안 그룹을 만들었기 때문에 시도가 실패합니다. 

네트워크 보안 그룹에는 포트 및 IP 주소별로 인바운드 및 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 보안 규칙이 포함됩니다. Azure에서 생성되는 기본 네트워크 보안 그룹은 동일한 가상 네트워크에 있는 리소스 사이의 모든 포트를 거치는 통신을 허용합니다. Linux 가상 머신의 경우 기본 네트워크 보안 그룹은 모든 포트를 거치는 인터넷의 모든 인바운드 트래픽을 거부하고 TCP 포트 22(SSH)를 통한 트래픽은 수락합니다. 따라서 포트 22를 웹 서버에 개방하지 않으려는 경우에도 기본적으로 인터넷에서 *myVmWeb* 가상 머신에 직접 SSH 세션을 생성할 수도 있습니다. `curl` 명령은 포트 80을 통해 통신하기 때문에 인터넷 통신은 실패합니다. 이것은 기본 네트워크 보안 그룹에 포트 80을 거치는 트래픽을 허용하는 규칙이 없기 때문입니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹 및 그룹에 포함된 모든 리소스를 제거합니다.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 여러 서브넷이 있는 가상 네트워크를 배포하는 방법을 알아보았습니다. 또한 Linux 가상 머신을 만들 때 Azure는 가상 머신에 연결되는 네트워크 인터페이스를 만들고 인터넷에서 포트 22를 거치는 트래픽만 허용하는 네트워크 보안 그룹을 만드는 것을 알아보았습니다. 개별 가상 머신이 아닌 서브넷에 대한 네트워크 트래픽을 필터링하는 방법을 알아보려면 다음 자습서로 이동하세요.

> [!div class="nextstepaction"]
> [서브넷에 대한 네트워크 트래픽 필터링](./virtual-networks-create-nsg-arm-cli.md)
