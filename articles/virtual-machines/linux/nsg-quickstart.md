---
title: "Azure CLI 2.0을 사용하여 Linux VM에 포트 열기 | Microsoft Docs"
description: "Azure Resource Manager 배포 모델 및 Azure CLI 2.0을 사용하여 Linux VM에 대한 포트를 열고 끝점을 만드는 방법 알아보기"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8001d5ca1224e170081c53c22ad251f972002f7a
ms.lasthandoff: 04/03/2017


---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Azure CLI를 사용하여 Linux VM에 대한 끝점 및 포트 열기
서브넷 또는 VM 네트워크 인터페이스에서 네트워크 필터를 만들어, Azure에서 VM(가상 컴퓨터)에 대한 포트를 열거나 끝점을 만듭니다. 인바운드 및 아웃바운드 트래픽을 모두 제어하는 이러한 필터를 트래픽을 수신하는 리소스에 연결된 네트워크 보안 그룹에 배치합니다. 포트 80에서 웹 트래픽의 일반적인 예제를 사용해 보겠습니다. 이 문서에서는 Azure CLI 2.0을 사용하여 VM에 포트를 여는 방법을 보여 줍니다. [Azure CLI 1.0](nsg-quickstart-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 이러한 단계를 수행할 수도 있습니다.


## <a name="quick-commands"></a>빠른 명령
네트워크 보안 그룹 및 규칙을 만들려면 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인해야 합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `myNetworkSecurityGroup` 및 `myVnet`이 포함됩니다.

[az network nsg create](/cli/azure/network/nsg#create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 `westus` 위치에 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#create)을 사용하여 웹 서버에 대한 HTTP 트래픽을 허용하는 규칙을 추가하거나 SSH 액세스 또는 데이터베이스 연결 등 사용자 고유의 시나리오에 맞게 조정합니다. 다음 예제에서는 포트 80의 TCP 트래픽을 허용하도록 `myNetworkSecurityGroupRule`이라는 규칙을 만듭니다.

```azurecli
az network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 80 --access allow
```

[az network nic update](/cli/azure/network/nic#update)를 사용하여 네트워크 보안 그룹을 VM의 NIC(네트워크 인터페이스)에 연결합니다. 다음 예제에서는 `myNic`라는 기존 NIC를 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹에 연결합니다.

```azurecli
az network nic update --resource-group myResourceGroup --name myNic \
    --network-security-group myNetworkSecurityGroup
```

또는 [az network vnet subnet update](/cli/azure/network/vnet/subnet#update)를 사용하여 네트워크 보안 그룹을 단일 VM의 네트워크 인터페이스가 아니라 가상 네트워크 서브넷에 연결할 수 있습니다. 다음 예제에서는 `myVnet` 가상 네트워크에서 `mySubnet`이라는 기존 서브넷을 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹에 연결합니다.

```azurecli
az network vnet subnet update --resource-group myResourceGroup \
    --vnet-name myVnet --name mySubnet --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>네트워크 보안 그룹에 대한 자세한 정보
여기서 빠른 명령을 사용하면 VM으로 트래픽이 이동되도록 할 수 있습니다. 네트워크 보안 그룹은 리소스에 대한 액세스를 제어하는 많은 기능과 세분성을 제공합니다. [여기서 네트워크 보안 그룹 및 ACL 규칙 만들기](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)에 대해 자세히 읽어보세요.

네트워크 보안 그룹 및 ACL 규칙을 Azure Resource Manager 템플릿의 일부로 정의할 수도 있습니다. [템플릿을 사용하여 네트워크 보안 그룹 만들기](../../virtual-network/virtual-networks-create-nsg-arm-template.md)에 대해 자세히 읽어보세요.

포트 전달을 사용하여 고유한 외부 포트를 VM의 내부 포트에 매핑해야 하는 경우 부하 분산 장치 및 NAT(네트워크 주소 변환) 규칙을 사용합니다. 예를 들어 TCP 포트 8080을 외부에 노출하고 트래픽이 VM의 TCP 포트 80으로 전달되도록 할 수 있습니다. [인터넷 연결 부하 분산 장치 만들기](../../load-balancer/load-balancer-get-started-internet-arm-cli.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 예제에서는 HTTP 트래픽을 허용하는 간단한 규칙을 만들었습니다. 다음 문서에서 보다 자세한 환경을 만들기 위한 정보를 찾을 수 있습니다.

* [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)
* [NSG(네트워크 보안 그룹)란?](../../virtual-network/virtual-networks-nsg.md)
* [부하 분산 장치에 대한 Azure Resource Manager 개요](../../load-balancer/load-balancer-arm.md)


