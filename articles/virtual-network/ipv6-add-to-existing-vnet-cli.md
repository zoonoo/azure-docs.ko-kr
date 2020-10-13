---
title: Azure virtual network에서 IPv4 응용 프로그램에 IPv6 추가-Azure CLI
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure CLI를 사용 하 여 Azure 가상 네트워크의 기존 응용 프로그램에 IPv6 주소를 배포 하는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 654924d25a567ed6c63405d27444eb6ff96d480d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90603648"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Azure virtual network에서 IPv4 응용 프로그램에 IPv6 추가-Azure CLI

이 문서에서는 Azure CLI를 사용 하 여 표준 Load Balancer에 대 한 Azure 가상 네트워크에서 IPv4 공용 IP 주소를 사용 하는 응용 프로그램에 IPv6 주소를 추가 하는 방법을 보여 줍니다. 현재 위치의 업그레이드에는 가상 네트워크 및 서브넷, IPv4 + IPV6 프런트 엔드 구성을 사용 하는 표준 Load Balancer, IPv4 + IPv6 구성, 네트워크 보안 그룹 및 공용 Ip가 있는 Nic가 있는 Vm이 포함 됩니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 Azure CLI를 로컬에 설치하고 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 [빠른 시작: 표준 Load Balancer Azure CLI 만들기](../load-balancer/quickstart-load-balancer-standard-public-cli.md)에 설명 된 대로 표준 Load Balancer를 배포 했다고 가정 합니다.

## <a name="create-ipv6-addresses"></a>IPv6 주소 만들기

표준 Load Balancer에 대 한 [az network 공용-ip create](/cli/azure/network/public-ip) 를 사용 하 여 공용 IPv6 주소를 만듭니다. 다음 예제에서는 *Myresourcegroupslb* 리소스 그룹에 *PublicIP_v6* 이라는 IPv6 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>IPv6 부하 분산 장치 프런트 엔드 구성

다음과 같이 [az network lb 프런트 엔드-IP create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) 를 사용 하 여 새 IPv6 IP 주소를 사용 하 여 부하 분산 장치를 구성 합니다.

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>IPv6 부하 분산 장치 백 엔드 풀 구성

다음과 같이 [az network lb address pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) 를 사용 하 여 IPv6 주소를 사용 하 여 nic에 대 한 백 엔드 풀을 만듭니다.

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>IPv6 부하 분산 장치 규칙 구성

[Az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)를 사용 하 여 IPv6 부하 분산 장치 규칙을 만듭니다.

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>IPv6 주소 범위 추가

다음과 같이 부하 분산 장치를 호스트 하는 서브넷 및 가상 네트워크에 IPv6 주소 범위를 추가 합니다.

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Nic에 IPv6 구성 추가

다음과 같이 [az network nic ip config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) 를 사용 하 여 IPv6 주소를 사용 하 여 VM nic를 구성 합니다.

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure Portal에서 IPv6 이중 스택 가상 네트워크 보기

다음과 같이 Azure Portal에서 IPv6 이중 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 창에서 *Myvnet*을 입력 합니다.
2. 검색 결과에 **Myvnet** 이 표시 되 면 선택 합니다. 그러면 *Myvnet*이라는 이중 스택 가상 네트워크의 **개요** 페이지가 시작 됩니다. 이중 스택 가상 네트워크는 *Mysubnet*이라는 이중 스택 서브넷에 있는 IPv4 및 IPv6 구성을 모두 사용 하 여 세 개의 nic를 표시 합니다.

  ![Azure의 IPv6 이중 스택 가상 네트워크](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 IPv4 프런트 엔드 IP 구성을 사용 하 여 기존 표준 Load Balancer를 이중 스택 (IPv4 및 IPv6) 구성으로 업데이트 했습니다. 또한 백 엔드 풀의 Vm Nic에 IPv6 구성을 추가 했습니다. Azure virtual network의 IPv6 지원에 대 한 자세한 내용은 [azure Virtual Network에 대 한](ipv6-overview.md) i p v 6을 참조 하세요.
