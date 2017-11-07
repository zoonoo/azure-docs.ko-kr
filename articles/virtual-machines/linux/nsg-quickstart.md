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
ms.date: 08/21/2017
ms.author: iainfou
ms.openlocfilehash: d176187fe465264b5f433260de5178b48ca9dd4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Azure CLI를 사용하여 Linux VM에 대한 끝점 및 포트 열기
서브넷 또는 VM 네트워크 인터페이스에서 네트워크 필터를 만들어, Azure에서 VM(가상 컴퓨터)에 대한 포트를 열거나 끝점을 만듭니다. 인바운드 및 아웃바운드 트래픽을 모두 제어하는 이러한 필터를 트래픽을 수신하는 리소스에 연결된 네트워크 보안 그룹에 배치합니다. 포트 80에서 웹 트래픽의 일반적인 예제를 사용해 보겠습니다. 이 문서에서는 Azure CLI 2.0을 사용하여 VM에 포트를 여는 방법을 보여 줍니다. [Azure CLI 1.0](nsg-quickstart-nodejs.md)에서 이러한 단계를 수행할 수도 있습니다.


## <a name="quick-commands"></a>빠른 명령
네트워크 보안 그룹 및 규칙을 만들려면 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인해야 합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myNetworkSecurityGroup* 및 *myVnet*이 포함됩니다.

[az network nsg create](/cli/azure/network/nsg#create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myNetworkSecurityGroup*이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#create)을 사용하여 웹 서버에 대한 HTTP 트래픽을 허용하는 규칙을 추가하거나 SSH 액세스 또는 데이터베이스 연결 등 사용자 고유의 시나리오에 맞게 조정합니다. 다음 예제에서는 포트 80의 TCP 트래픽을 허용하도록 *myNetworkSecurityGroupRule*이라는 규칙을 만듭니다.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```

[az network nic update](/cli/azure/network/nic#update)를 사용하여 네트워크 보안 그룹을 VM의 NIC(네트워크 인터페이스)에 연결합니다. 다음 예제에서는 *myNic*라는 기존 NIC를 *myNetworkSecurityGroup*이라는 네트워크 보안 그룹에 연결합니다.

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

또는 [az network vnet subnet update](/cli/azure/network/vnet/subnet#update)를 사용하여 네트워크 보안 그룹을 단일 VM의 네트워크 인터페이스가 아니라 가상 네트워크 서브넷에 연결할 수 있습니다. 다음 예제에서는 *myVnet* 가상 네트워크에 있는 *mySubnet*이라는 기존 서브넷을 *myNetworkSecurityGroup*이라는 네트워크 보안 그룹에 연결합니다.

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>네트워크 보안 그룹에 대한 자세한 정보
여기서 빠른 명령을 사용하면 VM으로 트래픽이 이동되도록 할 수 있습니다. 네트워크 보안 그룹은 리소스에 대한 액세스를 제어하는 많은 기능과 세분성을 제공합니다. [여기서 네트워크 보안 그룹 및 ACL 규칙 만들기](tutorial-virtual-network.md#secure-network-traffic)에 대해 자세히 읽어보세요.

고가용성 웹 응용 프로그램인 경우 VM을 Azure Load Balancer 뒤에 배치해야 합니다. 부하 분산 장치는 트래픽 필터링을 제공하는 네트워크 보안 그룹으로 트래픽을 VM에 분산시킵니다. 자세한 내용은 [Azure의 Linux 가상 컴퓨터 부하를 분산하여 고가용성 응용 프로그램을 만드는 방법](tutorial-load-balancer.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 예제에서는 HTTP 트래픽을 허용하는 간단한 규칙을 만들었습니다. 다음 문서에서 보다 자세한 환경을 만들기 위한 정보를 찾을 수 있습니다.

* [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)
* [NSG(네트워크 보안 그룹)란?](../../virtual-network/virtual-networks-nsg.md)
