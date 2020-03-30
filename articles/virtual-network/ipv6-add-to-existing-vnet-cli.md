---
title: Azure 가상 네트워크의 IPv4 응용 프로그램에 IPv6 추가 - Azure CLI
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure CLI를 사용하여 Azure 가상 네트워크의 기존 응용 프로그램에 IPv6 주소를 배포하는 방법을 보여 주며 있습니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2019
ms.author: kumud
ms.openlocfilehash: 5dc231febc2e9b605b9e7f603f5d036b8a2c62eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240752"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli-preview"></a>Azure 가상 네트워크의 IPv4 응용 프로그램에 IPv6 추가 - Azure CLI(미리 보기)

이 문서에서는 Azure CLI를 사용하는 표준 로드 밸런서에 대해 Azure 가상 네트워크에서 IPv4 공용 IP 주소를 사용하는 응용 프로그램에 IPv6 주소를 추가하는 방법을 보여 주십니다. 현재 업그레이드에는 가상 네트워크 및 서브넷, IPv4 + IPv6 프런트 엔드 구성이 있는 표준 로드 밸런서, IPv4 + IPv6 구성이 있는 NIC가 있는 VM, 네트워크 보안 그룹 및 공용 IP가 포함됩니다.

> [!Important]
> Azure 가상 네트워크에 대한 IPv6 지원은 현재 공개 미리 보기 상태입니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 Azure CLI를 로컬에 설치하고 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="register-the-service"></a>서비스 등록

Azure에서 이중 스택 응용 프로그램을 배포하기 전에 다음 Azure CLI를 사용하여 이 미리 보기 기능에 대한 구독을 구성해야 합니다.

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

기능 등록이 완료될 때까지 최대 30분이 걸립니다. 다음 Azure CLI 명령을 실행하여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

등록이 완료되면 다음 명령을 실행합니다.

```azurecli
az provider register --namespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>표준 Load Balancer 만들기
이 문서에서는 [빠른 시작: 표준 로드 밸러커 만들기 - Azure CLI에](../load-balancer/quickstart-load-balancer-standard-public-cli.md)설명된 대로 표준 로드 밸러서를 배포했다고 가정합니다.

## <a name="create-ipv6-addresses"></a>IPv6 주소 만들기

표준 로드 밸런서에 대한 [az 네트워크 공용 IP 만들기를](/cli/azure/network/public-ip) 사용하여 공용 IPv6 주소를 만듭니다. 다음 예제에서는 *myResourceGroupSLB* 리소스 그룹에 *PublicIP_v6* 라는 IPv6 공용 IP 주소를 만듭니다.

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>IPv6 로드 밸론자 프런트 엔드 구성

다음과 같이 [az 네트워크 lb 프런트 엔드 IP create를](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) 사용하여 새로운 IPv6 IP 주소로 로드 밸러블러를 COnfigure:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>IPv6 로드 밸러서 백엔드 풀 구성

az 네트워크 lb 주소 풀을 사용하여 IPv6 주소가 있는 NIC용 백 엔드 풀을 다음과 같이 [만듭니다.](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>IPv6 로드 밸러블러 규칙 구성

az 네트워크 lb 규칙을 사용하여 IPv6 로드 밸러터 [규칙을 만듭니다.](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)

```azurecli
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

다음과 같이 로드 밸러블러를 호스팅하는 가상 네트워크 및 서브넷에 IPv6 주소 범위를 추가합니다.

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>NIC에 IPv6 구성 추가

다음과 같이 [az 네트워크 nic ip-config create를](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) 사용하여 IPv6 주소로 VM NIC를 구성합니다.

```azurecli
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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure 포털에서 IPv6 듀얼 스택 가상 네트워크 보기
다음과 같이 Azure 포털에서 IPv6 듀얼 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 표시줄에서 *myVnet을*입력합니다.
2. **myVnet이** 검색 결과에 나타나면 선택합니다. 그러면 *myVNet이라는*듀얼 스택 가상 네트워크의 **개요** 페이지가 시작됩니다. 듀얼 스택 가상 네트워크는 *mySubnet라는*듀얼 스택 서브넷에 있는 IPv4 및 IPv6 구성을 모두 갖춘 3개의 NIC를 보여줍니다.

  ![Azure의 IPv6 듀얼 스택 가상 네트워크](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Azure 가상 네트워크에 대한 IPv6는 이 미리 보기 릴리스에 대해 읽기 전용으로 Azure 포털에서 사용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 IPv4 프런트 엔드 IP 구성을 사용하여 기존 표준 로드 밸런서를 이중 스택(IPv4 및 IPv6) 구성으로 업데이트했습니다. 또한 백 엔드 풀에 있는 VM의 NIC에 IPv6 구성을 추가했습니다. Azure 가상 네트워크에서 IPv6 지원에 대해 자세히 알아보려면 [Azure 가상 네트워크에 대한 IPv6이란 무엇입니까?](ipv6-overview.md)
