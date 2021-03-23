---
title: '자습서: NAT 게이트웨이 만들기 - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Azure CLI를 사용하여 NAT 게이트웨이를 만듭니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5dd431a5a7377c409be0794511c5f402d1c5a3a9
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102636799"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>자습서: Azure CLI를 사용하여 NAT 게이트웨이 만들기

이 자습서에서는 Azure Virtual Network NAT 서비스를 사용하는 방법을 보여줍니다. Azure에서 가상 머신에 대한 아웃바운드 연결을 제공하는 NAT 게이트웨이를 만듭니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크를 만듭니다.
> * 가상 머신을 만듭니다.
> * NAT 게이트웨이를 만들고 가상 네트워크와 연결합니다.
> * 가상 머신에 연결하고 NAT IP 주소를 확인합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 빠른 시작에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 **myResourceGroupNAT** 라는 리소스 그룹을 **eastus2** 위치에 만듭니다.

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>NAT 게이트웨이 만들기

이 섹션에서는 NAT 게이트웨이와 지원 리소스를 만듭니다.

### <a name="create-public-ip-address"></a>공용 IP 주소 만들기

인터넷에 액세스 하려면 NAT 게이트웨이에 대 한 공용 IP 주소가 하나 이상 필요 합니다. [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 **myPublicIP** 라는 공용 IP 주소 리소스를 **myResourceGroupNAT** 에 만듭니다. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>NAT 게이트웨이 리소스 만들기

[az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create)를 사용하여 글로벌 Azure NAT 게이트웨이를 만듭니다. 이 명령의 결과는 공용 IP 주소 **myPublicIP** 를 사용하는 **myNATgateway** 라는 게이트웨이 리소스를 만듭니다. 유휴 시간 제한은 10분으로 설정됩니다.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>가상 네트워크 만들기

**myResourceGroup** 리소스 그룹에 **mySubnet** [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)라는 서브넷이 있는 **myVnet** 이라는 가상 네트워크를 만듭니다. 가상 네트워크의 IP 주소 공간은 **10.1.0.0/16** 입니다. 가상 네트워크 내의 서브넷은 **10.1.0.0/24** 입니다.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>베스천 호스트 만들기

가상 머신에 액세스하려면 **myBastionHost** 라는 Azure Bastion 호스트를 만듭니다. 

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)를 사용하여 Azure Bastion 서브넷을 만듭니다.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 베스천 호스트에 대한 공용 IP 주소를 만듭니다. 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

[az network bastion create](/cli/azure/network/bastion#az-network-bastion-create)를 사용하여 베스천 호스트를 만듭니다. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>원본 서브넷에 대한 NAT 서비스 구성

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용하여 특정 **myNATgateway** NAT 게이트웨이 리소스를 사용하도록 **myVnet** 가상 네트워크의 **mySubnet** 원본 서브넷을 구성합니다. 이 명령은 지정된 서브넷에서 NAT 서비스를 활성화합니다.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

이제 인터넷 대상으로의 모든 아웃바운드 트래픽에서 NAT 게이트웨이를 사용합니다.  UDR을 구성할 필요가 없습니다.


## <a name="virtual-machine"></a>가상 머신

이 섹션에서는 NAT 게이트웨이를 테스트하여 아웃바운드 연결의 공용 IP 주소를 확인하는 가상 머신을 만듭니다.

[az vm create](/cli/azure/vm#az-vm-create)를 사용하여 가상 머신을 만듭니다.

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

다음 섹션으로 이동하기 전에 가상 머신 만들기가 완료될 때까지 기다립니다.

## <a name="test-nat-gateway"></a>NAT 게이트웨이 테스트

이 섹션에서는 NAT 게이트웨이를 테스트합니다. 먼저 NAT 게이트웨이의 공용 IP를 검색합니다. 그런 다음, 테스트 가상 머신에 연결하고 NAT 게이트웨이를 통해 아웃바운드 연결을 확인합니다.
    
1. [Azure 포털](https://portal.azure.com)

1. **개요** 화면에서 NAT 게이트웨이에 대한 공용 IP 주소를 찾습니다. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, **myPublicIP** 를 선택합니다.

2. 공용 IP 주소를 기록해 둡니다.

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="NAT 게이트웨이의 공용 IP 주소 검색" border="true":::

3. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myResourceGroupNAT** 리소스 그룹에 있는 **myVM** 을 선택합니다.

4. **개요** 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

5. 파란색 **Bastion 사용** 단추를 선택합니다.

6. VM을 만드는 동안 입력한 사용자 이름과 암호를 입력합니다.

7. **myTestVM** 에서 **Internet Explorer** 를 엽니다.

8. 주소 표시줄에 **https://whatsmyip.com** 을 입력합니다.

9. 표시된 IP 주소가 이전 단계에서 적어둔 NAT 게이트웨이 주소와 일치하는지 확인합니다.

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="외부 아웃바운드 IP를 표시하는 Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 가상 네트워크, 가상 머신 및 NAT 게이트웨이를 삭제합니다.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>다음 단계

Azure Virtual Network NAT에 대한 자세한 내용은 다음을 참조하세요.
> [!div class="nextstepaction"]
> [Virtual Network NAT 개요](nat-overview.md)
