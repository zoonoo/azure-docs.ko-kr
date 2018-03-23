---
title: 네트워크 트래픽 라우팅 - Azure CLI | Microsoft Docs
description: Azure CLI를 사용하여 경로 테이블이 포함된 네트워크 트래픽을 라우팅하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Azure CLI를 사용하여 경로 테이블이 포함된 네트워크 트래픽 라우팅

기본적으로 Azure에서는 가상 네트워크 내의 모든 서브넷 간에 트래픽을 자동으로 라우팅합니다. 고유의 라우팅을 만들어 Azure의 기본 라우팅을 재정의할 수 있습니다. 사용자 지정 경로를 만드는 기능은, 예를 들어 방화벽을 통해 서브넷 간 트래픽을 라우팅하려는 경우에 유용합니다. 이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 경로 테이블 만들기
> * 경로 만들기
> * 가상 네트워크 서브넷에 경로 테이블 연결
> * 테스트 라우팅
> * 라우팅 문제 해결

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-a-route-table"></a>경로 테이블 만들기

기본적으로 Azure에서는 가상 네트워크 내의 모든 서브넷 간에 트래픽을 라우팅합니다. Azure의 기본 경로를 자세히 알아보려면 [시스템 경로](virtual-networks-udr-overview.md)를 참조하세요. Azure의 기본 라우팅을 재정의하려면 경로를 포함하는 경로 테이블을 만들고 가상 네트워크 서브넷에 경로 테이블을 연결합니다.

이 문서에서 생성된 모든 리소스에 대해 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만들어야 경로 테이블을 만들 수 있습니다. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

[az network route-table create](/cli/azure/network/route#az_network_route_table_create)를 사용하여 경로 테이블을 만듭니다. 다음 예제에서는 *myRouteTablePublic*이라는 경로 테이블을 만듭니다. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>경로 만들기

경로 테이블에는 0개 또는 여러 개의 경로가 포함됩니다. [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create)를 사용하여 경로 테이블에 경로를 만듭니다. 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

경로는 10.0.2.4 IP 주소를 사용하는 네트워크 가상 어플라이언스를 통해 10.0.1.0/24 주소 접두사를 향하는 모든 트래픽을 보냅니다. 지정된 주소 접두사가 있는 네트워크 가상 어플라이언스 및 서브넷은 이후 단계에서 만들어집니다. 경로는 직접 서브넷 간 트래픽을 라우팅하는 Azure의 기본 라우팅을 재정의합니다. 각 경로는 다음 홉 형식을 지정합니다. 다음 홉 형식은 Azure가 트래픽을 라우팅하는 방법을 지정합니다. 이 예제에서 다음 홉 형식은 *VirtualAppliance*입니다. Azure에서 사용 가능한 모든 다음 홉 형식 및 사용 시기에 대해 자세히 알아보려면 [다음 홉 형식](virtual-networks-udr-overview.md#custom-routes)을 참조하세요.

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

서브넷에 경로 테이블을 연결하려면 먼저 가상 네트워크 및 서브넷을 만들어야 합니다. [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 하나의 서브넷이 있는 가상 네트워크를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 2개의 추가 서브넷을 만듭니다.

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

경로 테이블을 0개 또는 여러 개의 서브넷에 연결할 수 있습니다. 서브넷에는 0개 또는 1개의 경로 테이블이 연결될 수 있습니다. 서브넷의 아웃바운드 트래픽은 Azure의 기본 경로 및 서브넷에 연결한 경로 테이블에 추가한 모든 사용자 지정 경로에 따라 라우팅됩니다. [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용하여 *myRouteTablePublic* 경로 테이블을 *공용* 서브넷에 연결합니다.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

프로덕션 사용을 위해 경로 테이블을 배포하기 전에 [Azure에서 라우팅](virtual-networks-udr-overview.md) 및 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)에 대해 자세히 알아두는 것이 좋습니다.

## <a name="test-routing"></a>테스트 라우팅

라우팅을 테스트하려면 이전 단계에서 만든 경로를 통해 라우팅하는 네트워크 가상 어플라이언스로 사용되는 가상 머신을 만들어야 합니다. 네트워크 가상 어플라이언스를 만든 후에 가상 머신을 *공용* 및 *개인* 서브넷에 배포합니다. 그런 다음, *공용* 서브넷의 트래픽을 네트워크 가상 어플라이언스를 통해 *개인* 서브넷에 라우팅하게 됩니다.

### <a name="create-a-network-virtual-appliance"></a>네트워크 가상 어플라이언스 만들기

이전 단계에서 다음 홉 형식으로 네트워크 가상 어플라이언스를 지정하는 경로를 만들었습니다. 네트워크 응용 프로그램을 실행하는 가상 머신을 종종 네트워크 가상 어플라이언스라고 합니다. 프로덕션 환경에서 배포한 네트워크 가상 어플라이언스는 종종 미리 구성된 가상 머신입니다. 몇 가지 네트워크 가상 어플라이언스는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1)에서 확인할 수 있습니다. 이 문서에서는 기본 가상 머신이 생성됩니다. 

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 *DMZ* 서브넷에 네트워크 가상 어플라이언스를 만듭니다. 가상 머신을 만들 때 Azure는 기본적으로 가상 머신에 공용 IP 주소를 만들고 할당합니다. `--public-ip-address ""` 매개 변수는 Azure가 가상 머신에 공용 IP 주소를 만들고 할당하지 못하도록 지시합니다. 가상 머신을 인터넷에서 연결할 필요가 없기 때문입니다. 또한 기본 키 위치에 SSH 키가 없는 경우 해당 명령이 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

가상 머신을 만드는 데 몇 분 정도 걸립니다. Azure가 가상 머신 만들기를 완료하고 가상 머신에 대한 출력을 반환할 때까지 다음 단계로 이동하지 마십시오. 프로덕션 환경에서 배포한 네트워크 가상 어플라이언스는 종종 미리 구성된 가상 머신입니다. 몇 가지 네트워크 가상 어플라이언스는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1)에서 확인할 수 있습니다.

가상 머신에 연결된 각 Azure [네트워크 인터페이스](virtual-network-network-interface.md)에 대해 IP 전달을 사용하도록 설정해야 합니다. 여기서 가상 머신은 네트워크 인터페이스에 할당되지 않은 모든 IP 주소를 대상으로 하는 트래픽을 전달합니다. *DMZ* 서브넷에서 네트워크 가상 어플라이언스를 만들면 Azure에서 자동으로 *myVmNvaVMNic*이라는 네트워크 인터페이스를 만들고, 네트워크 인터페이스를 가상 머신에 연결하고, 개인 IP 주소 *10.0.2.4*를 네트워크 인터페이스에 할당합니다. [az network nic update](/cli/azure/network/nic#az_network_nic_update)를 사용하여 네트워크 인터페이스에 대한 IP 전달을 사용하도록 설정합니다.

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

가상 머신 내 운영 체제 또는 가상 머신 내에서 실행 중인 응용 프로그램도 네트워크 트래픽을 전달할 수 있어야 합니다. 프로덕션 환경에서 네트워크 가상 어플라이언스를 배포할 때 어플라이언스는 일반적으로 트래픽을 전달하기 전에 몇 가지 다른 기능을 필터링, 로그 또는 수행합니다. 하지만 이 문서에서는 운영 체제가 수신한 모든 트래픽을 전달하기만 합니다. 운영 체제 내에서 IP 전달을 사용하도록 설정하는 명령을 실행하는 [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set)을 사용하여 가상 머신의 운영 체제 내에서 IP 전달을 사용하도록 설정합니다.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
명령은 실행되기까지 최대 1분이 걸릴 수 있습니다.

### <a name="create-virtual-machines"></a>가상 머신 만들기

이후 단계에서 *공용* 서브넷의 해당 트래픽이 네트워크 가상 어플라이언스를 통해 *개인* 서브넷에 라우팅되는지 유효성을 검사할 수 있도록 가상 네트워크에 두 개의 가상 머신을 만듭니다. 

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 *공용* 서브넷에 가상 머신을 만듭니다. `--no-wait` 매개 변수를 사용하면 Azure가 백그라운드에서 명령을 실행할 수 있으므로 다음 명령을 계속 진행할 수 있습니다. 이 문서를 간소화하기 위해 암호가 사용됩니다. 일반적으로 키는 프로덕션 배포에 사용됩니다. 키를 사용하는 경우 SSH 에이전트 전달도 구성해야 합니다. 자세한 내용은 SSH 클라이언트를 위한 설명서를 참조하세요. 다음 명령에서 `<replace-with-your-password>`를 원하는 암호로 바꿉니다.

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

가상 머신을 만드는 데 몇 분 정도 걸립니다. 가상 머신을 만든 후 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. 

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
**publicIpAddress**를 기록해 둡니다. 이 주소는 이후 단계에서 인터넷을 통해 가상 컴퓨터에 액세스하는 데 사용됩니다.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>네트워크 가상 어플라이언스를 통한 트래픽 라우팅

다음 명령을 사용하여 *myVmMgmt* 가상 머신과의 SSH 세션을 만듭니다. *<publicIpAddress>*를 가상 머신의 공용 IP 주소로 바꿉니다. 위의 예제에서 IP 주소는 *13.90.242.231*입니다.

```bash 
ssh azureuser@<publicIpAddress>
```

암호를 입력하라는 메시지가 표시되면 [가상 머신 만들기](#create-virtual-machines)에서 선택한 암호를 입력합니다.

다음 명령을 사용하여 *myVmMgmt* 가상 머신에서 경로 추적을 설치합니다.

```bash 
sudo apt-get install traceroute
```

*myVmMgmt* 가상 머신에서 *myVmWeb* 가상 머신에 대한 네트워크 트래픽의 라우팅을 테스트하려면 다음 명령을 사용합니다.

```bash
traceroute myvmweb
```

응답은 다음 예제와 유사합니다.

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

트래픽이 *myVmMgmt* 가상 머신에서 *myVmWeb* 가상 머신으로 직접 라우팅되는지 볼 수 있습니다. Azure의 기본 경로는 트래픽을 서브넷 간에 직접 라우팅합니다. 

*myVmMgmt* 가상 머신에서 *myVmWeb* 가상 머신에 SSH를 설정하려면 다음 명령을 사용합니다.

```bash 
ssh azureuser@myVmWeb
```

*myVmWeb* 가상 머신에서 경로 추적을 설치하려면 다음 명령을 사용합니다.

```bash 
sudo apt-get install traceroute
```

*myVmWeb* 가상 머신에서 *myVmMgmt* 가상 머신에 대한 네트워크 트래픽의 라우팅을 테스트하려면 다음 명령을 사용합니다.

```bash
traceroute myvmmgmt
```

응답은 다음 예제와 유사합니다.

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
첫 번째 홉이 네트워크 가상 어플라이언스의 개인 IP 주소인 10.0.2.4인 것을 확인할 수 있습니다. 두 번째 홉은 *myVmMgmt* 가상 머신의 개인 IP 주소인 10.0.1.4입니다. 경로가 *myRouteTablePublic* 경로 테이블에 추가되고 *공용* 서브넷에 연결되었으므로 Azure는 트래픽을 직접 *개인* 서브넷에 라우팅하는 대신 NVA를 통해 트래픽을 라우팅합니다.

*myVmWeb* 및 *myVmMgmt* 가상 머신 모두에 대해 SSH 세션을 닫습니다.

## <a name="troubleshoot-routing"></a>라우팅 문제 해결

이전 단계에서 설명한 대로 Azure는 사용자가 필요에 따라 자신 고유의 경로로 재정의할 수 있는 기본 경로를 적용합니다. 경우에 따라 트래픽이 예상 대로 라우팅하지 못할 수 있습니다. 트래픽이 두 가상 머신 간에 라우팅되는 방법을 결정하려면 [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop)을 사용합니다. 예를 들어 다음 명령은 *myVmWeb*(10.0.0.4) 가상 머신에서 *myVmMgmt*(10.0.1.4) 가상 머신으로의 트래픽 라우팅을 테스트합니다.

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
잠시 후 다음과 같은 출력이 반환됩니다.

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

출력에는 *myVmWeb*에서 *myVmMgmt*의 트래픽에 대한 다음 홉 IP 주소가 10.0.2.4(*myVmNva* 가상 머신)이며, 다음 홉 형식은 *VirtualAppliance*이고, 라우팅을 유발하는 경로 테이블은 *myRouteTablePublic*이라는 정보가 포함됩니다.

각 네트워크 인터페이스에 대한 유효한 경로는 Azure의 기본 경로와 사용자가 정의한 경로의 조합입니다. [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table)을 사용하여 가상 머신의 네트워크 인터페이스에 대해 유효한 모든 경로를 참조하세요. 예를 들어 *MyVmWebVMNic* 네트워크 인터페이스에 대한 유효한 경로를 *myVmWeb* 가상 머신에 표시하려면 다음 명령을 입력합니다.

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

모든 기본 경로 및 이전 단계에서 추가한 경로가 반환됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹 및 그룹에 포함된 모든 리소스를 제거합니다.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 경로 테이블을 만들고 서브넷에 연결했습니다. 공용 서브넷에서 개인 서브넷으로 트래픽을 라우팅하는 네트워크 가상 어플라이언스를 만들었습니다. 가상 네트워크 내에서 여러 Azure 리소스를 배포할 수 있는 반면, 일부 Azure PaaS 서비스에 대한 리소스는 가상 네트워크에 배포할 수 없습니다. 하지만 일부 Azure PaaS 서비스의 리소스에 대한 액세스를 가상 네트워크 서브넷의 트래픽만으로 제한할 수 있습니다. 다음 자습서에서 Azure PaaS 리소스에 대한 네트워크 액세스를 제한하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [PaaS 리소스에 대한 네트워크 액세스 제한](virtual-network-service-endpoints-configure.md#azure-cli)
