---
title: '빠른 시작: NAT 게이트웨이 만들기 - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: 이 빠른 시작에서는 Azure CLI를 사용하여 NAT 게이트웨이를 만드는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: df1e363f31aa8c88be54454c9dc060f4ed6b7ca1
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588895"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 NAT 게이트웨이 만들기

이 빠른 시작에서는 Azure Virtual Network NAT 서비스를 사용하는 방법을 보여 줍니다. Azure에서 가상 머신에 대한 아웃바운드 연결을 제공하는 NAT 게이트웨이를 만듭니다. 

>[!NOTE] 
>Azure Virtual Network NAT는 현재 공개 미리 보기로 제공되며, 제한된 [지역](https://azure.microsoft.com/global-infrastructure/regions/) 세트에서 사용할 수 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms)을 참조하세요.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell을 사용하여 이 자습서를 완료하거나 각 명령을 로컬로 실행할 수 있습니다.  Azure Cloud Shell을 사용한 적이 없는 경우 [지금 로그인](https://shell.azure.com)하여 초기 설정을 진행합니다.
이러한 명령을 로컬로 실행하도록 선택하는 경우 CLI를 설치해야 합니다.  이 자습서를 수행하려면 Azure CLI 버전 2.0.71 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](https://docs.microsoft.com/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 **myResourceGroupNAT**라는 리소스 그룹을 **eastus2** 위치에 만듭니다.

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>NAT Gateway 만들기

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

공용 인터넷에 액세스 하려면 NAT 게이트웨이에 대 한 공용 IP 주소가 하나 이상 필요 합니다. [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)를 사용하여 **myPublicIP**라는 공용 IP 주소 리소스를 **myResourceGroupNAT**에 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>공용 IP 접두사 만들기

하나 이상의 공용 IP 주소 리소스, 공용 IP 접두사 또는 둘 모두는 NAT 게이트웨이에서 사용할 수 있습니다. 공용 IP 접두사 리소스를 이 시나리오에 추가하여 보여 줍니다.   [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix)를 사용하여 **myPublicIPprefix**라는 공용 IP 접두사를 **myResourceGroupNAT**에 만듭니다.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT 게이트웨이 리소스 만들기

이 섹션에서는 NAT 게이트웨이 리소스를 사용하여 NAT 서비스의 다음 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.
  - NAT 게이트웨이 리소스에서 변환하는 아웃바운드 흐름에 사용할 공용 IP 풀 및 공용 IP 접두사입니다.
  - 유휴 시간 제한을 4분(기본값)에서 10분으로 변경합니다.

[az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest)를 사용하여 **myNATgateway**라는 글로벌 Azure NAT 게이트웨이를 만듭니다. 이 명령은 **myPublicIP** 공용 IP 주소와 **myPublicIPprefix**공용 IP 접두사를 모두 사용합니다. 유휴 시간 제한을 **10**분으로 변경합니다.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

이 시점에서 NAT 게이트웨이가 작동하며, 이제 가상 네트워크의 서브넷을 사용해야 하는 서브넷만 구성하면 됩니다.

## <a name="configure-virtual-network"></a>가상 네트워크 구성

VM을 배포하기 전에 NAT 게이트웨이를 사용하려면 먼저 가상 네트워크를 만들어야 합니다.

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet)를 사용하여 **mySubnet**이라는 서브넷이 있는 **myVnet** 가상 네트워크를 **myResourceGroupNAT**에 만듭니다.  가상 네트워크의 IP 주소 공간은 **192.168.0.0/16**입니다. 가상 네트워크 내의 서브넷은 **192.168.0.0/24**입니다.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>원본 서브넷에 대한 NAT 서비스 구성

[az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet)를 사용하여 특정 **myNATgateway** NAT 게이트웨이 리소스를 사용하도록 **myVnet** 가상 네트워크의 **mySubnet** 원본 서브넷을 구성합니다.  이 명령은 지정된 서브넷에서 NAT 서비스를 활성화합니다.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

이제 인터넷 대상으로의 모든 아웃바운드 트래픽에서 NAT 게이트웨이를 사용합니다.  UDR을 구성할 필요가 없습니다.

## <a name="create-a-vm-to-use-the-nat-service"></a>NAT 서비스를 사용하는 VM 만들기

이제 NAT 서비스를 사용할 VM을 만듭니다.  이 VM에는 VM에 액세스할 수 있도록 인스턴스 수준 공용 IP로 사용할 공용 IP가 있습니다.  NAT 서비스는 흐름 방향을 인식하며 서브넷의 기본 인터넷 대상을 대체합니다. VM의 공용 IP 주소는 아웃바운드 연결에 사용되지 않습니다.

### <a name="create-public-ip-for-source-vm"></a>원본 VM에 대한 공용 IP 만들기

VM에 액세스하는 데 사용할 공용 IP를 만듭니다.  [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)를 사용하여 **myPublicIPVM**이라는 공용 IP 주소 리소스를 **myResourceGroupNAT**에 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>VM에 대한 NSG 만들기

표준 공용 IP 주소는 '기본적으로 보안'되므로 ssh 액세스에 대한 인바운드 액세스를 허용하는 NSG를 만들어야 합니다. [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)를 사용하여 **myNSG**라는 NSG 리소스를 **myResourceGroupNAT**에 만듭니다.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>원본 VM에서 SSH 엔드포인트 공개

NSG에서 원본 VM에 대한 SSH 액세스 규칙을 만듭니다. [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)를 사용하여 **ssh**라는 NSG 규칙을 **myResourceGroupNAT**의 **myNSG**라는 NSG에 만듭니다.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>VM에 대한 NIC 만들기

[az network nic create](/cli/azure/network/nic#az-network-nic-create)를 사용하여 네트워크 인터페이스를 만들고 공용 IP 주소 및 네트워크 보안 그룹에 연결합니다. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>VM 만들기

[az vm create](/cli/azure/vm#az-vm-create)를 사용하여 가상 머신을 만듭니다.  이 VM에 대한 ssh 키를 생성하고 나중에 사용할 프라이빗 키를 저장합니다.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

VM이 배포될 때까지 기다린 후 나머지 단계를 계속 진행합니다.

## <a name="discover-the-ip-address-of-the-vm"></a>VM의 IP 주소 검색

먼저 만든 VM의 IP 주소를 검색해야 합니다. VM의 공용 IP 주소를 검색하려면 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) 명령을 사용합니다. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>공용 IP 주소를 복사한 다음, 이를 사용하여 VM에 액세스할 수 있도록 메모장에 붙여넣습니다.

### <a name="sign-in-to-vm"></a>VM에 로그인

SSH 자격 증명은 이전 작업의 Cloud Shell에 저장해야 합니다.  브라우저에서 [Azure Cloud Shell](https://shell.azure.com)을 엽니다. 이전 단계에서 검색된 IP 주소를 사용하여 가상 머신에 SSH로 연결합니다.

```bash
ssh <ip-address-destination>
```

이제 NAT 서비스를 사용할 준비가 되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 NAT 게이트웨이 및 이를 사용하는 VM을 만들었습니다. 

Azure Monitor에서 메트릭을 검토하여 NAT 서비스의 작동 상태를 확인합니다. 사용 가능한 SNAT 포트의 리소스 소모와 같은 문제를 진단합니다.  이러한 SNAT 포트의 리소스 소모는 공용 IP 주소 리소스, 공용 IP 접두사 리소스 또는 둘 모두를 추가하여 해결할 수 있습니다.


- [Azure Virtual Network NAT](./nat-overview.md)에 대한 자세한 정보
- [NAT 게이트웨이 리소스](./nat-gateway-resource.md)에 대해 알아봅니다.
- [Azure CLI를 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-cli.md)를 배포하기 위한 빠른 시작.
- [Azure PowerShell을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-powershell.md)를 배포하기 위한 빠른 시작.
- [Azure Portal을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-portal.md)를 배포하기 위한 빠른 시작.
- [공개 미리 보기에 대한 피드백을 제공합니다](https://aka.ms/natfeedback).
> [!div class="nextstepaction"]

