---
title: IPv6 듀얼 스택 애플리케이션 배포 - 표준 로드 밸러저 - CLI
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure CLI를 사용하여 Azure 가상 네트워크에서 IPv6 듀얼 스택 응용 프로그램을 배포하는 방법을 보여 주며 있습니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: bb90858f7e87e31b8b6028a30a6000bbed4d3e4b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421087"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli"></a>Azure 가상 네트워크에 IPv6 듀얼 스택 응용 프로그램 배포 - CLI

이 문서에서는 듀얼 스택 서브넷이 있는 듀얼 스택 가상 네트워크, 듀얼(IPv4 + IPv6) 프런트 엔드 구성이 있는 표준 로드 밸런서, 듀얼 IP 구성이 있는 NIC가 있는 VM, 이중 IP 구성이 있는 VM, 이중 네트워크 보안 그룹 규칙 및 이중 공용 IP가 포함된 Azure의 표준 로드 밸런서를 사용하여 이중 스택(IPv4 + IPv6) 응용 프로그램을 배포하는 방법을 보여 주며, 이중 스택 을 사용하는 응용 프로그램을 배포하는 방법을 보여 주십니다.

Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 지금 만드세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 로컬로 Azure CLI를 설치하고 사용하기로 결정한 경우 이 빠른 시작을 위해 Azure CLI 버전 2.0.49 이상을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

듀얼 스택 가상 네트워크를 만들려면 먼저 [az 그룹이 만들 수](/cli/azure/group)있는 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *동쪽* 위치에 *DsResourceGroup01이라는* 리소스 그룹을 만듭니다.

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>로드 밸런서용 IPv4 및 IPv6 공용 IP 주소 만들기
인터넷에서 IPv4 및 IPv6 끝점에 액세스하려면 로드 밸런서에 대한 IPv4 및 IPv6 공용 IP 주소가 필요합니다. [az network public-ip create](/cli/azure/network/public-ip)를 사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 *DsResourceGroup01* 리소스 그룹에서 *dsPublicIP_v4* 및 *dsPublicIP_v6라는* IPv4 및 IPv6 공용 IP 주소를 만듭니다.

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku STANDARD  \
--allocation-method static  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku STANDARD  \
--allocation-method static  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>VM용 공용 IP 주소 만들기

인터넷에서 VM에 원격으로 액세스하려면 VM에 대한 IPv4 공용 IP 주소가 필요합니다. [az network public-ip create](/cli/azure/network/public-ip)를 사용하여 공용 IP 주소를 만듭니다.

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4
```

## <a name="create-standard-load-balancer"></a>표준 Load Balancer 만들기

이 섹션에서는 부하 분산에 대한 이중 프런트 엔드 IP(IPv4 및 IPv6) 및 백 엔드 주소 풀을 구성한 다음 표준 로드 밸러블러를 만듭니다.

### <a name="create-load-balancer"></a>부하 분산 장치 만들기

[az 네트워크 lb를](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) 사용하여 표준 로드 밸런서 **만들기는 dsLbFrontEnd_v4**라는 프런트 엔드 풀, 이전 단계에서 만든 IPv4 공용 IP 주소 **dsPublicIP_v4** 연결된 **dsLbBackEndPool_v4** 라는 백 엔드 풀을 포함하는 **dsLB라는 이름의 dsLB를** 만듭니다. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Standard \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>IPv6 프런트 엔드 만들기

[az 네트워크 lb 프런트 엔드](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)IP 만들기를 사용하여 IPV6 프런트 엔드 IP 만들기 . 다음 예제는 *dsLbFrontEnd_v6* 라는 프런트 엔드 IP 구성을 만들고 *dsPublicIP_v6* 주소를 연결 합니다.

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>IPv6 백 엔드 주소 풀 구성

az 네트워크 lb 주소 풀을 만드는 IPv6 백 엔드 [주소 풀 만들기.](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) 다음 예제는 IPv6 NIC 구성이 있는 VM을 포함하도록 *dsLbBackEndPool_v6* 백 엔드 주소 풀을 만듭니다.

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>상태 프로브 만들기
[az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest)를 사용하여 가상 머신의 상태를 모니터링하는 상태 프로브를 만듭니다. 

```azurecli
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 

[az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제는 *dsLBrule_v4* 및 *dsLBrule_v6* 라는 로드 밸러버 규칙을 만들고 *TCP* 포트 *80의* 트래픽을 IPv4 및 IPv6 프런트 엔드 IP 구성으로 균형을 조정합니다.

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>네트워크 리소스 만들기
일부 VM을 배포하기 전에 가용성 집합, 네트워크 보안 그룹, 가상 네트워크 및 가상 NIC와 같은 지원 네트워크 리소스를 만들어야 합니다. 
### <a name="create-an-availability-set"></a>가용성 집합 만들기
앱의 가용성을 개선하려면 VM을 가용성 집합에 배치합니다.

[az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest)를 사용하여 가용성 집합을 만듭니다. 다음 예제는 *dsAVset라는*가용성 집합을 만듭니다.

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기

VNet에서 인바운드 및 아웃바운드 통신을 제어하는 규칙에 대한 네트워크 보안 그룹을 만듭니다.

#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[az 네트워크 nsg 생성을](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) 사용하여 네트워크 보안 그룹 만들기


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>인바운드 및 아웃바운드 연결에 대한 네트워크 보안 그룹 규칙 만들기

포트 3389를 통한 RDP 연결, 포트 80을 통한 인터넷 연결 및 [az 네트워크 nsg 규칙의](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)아웃바운드 연결을 허용하는 네트워크 보안 그룹 규칙을 만듭니다.

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az 네트워크 vnet을 사용하여](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create)가상 네트워크를 만듭니다. 다음 예제는 서브넷이 *dsSubNET_v4* *dsVNET이라는* 가상 네트워크를 만들고 *dsSubNET_v6.*

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>NIC 만들기

az 네트워크 nic create를 사용하여 각 VM에 대한 가상 [NIC를 만듭니다.](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) 다음 예제는 각 VM에 대한 가상 NIC를 만듭니다. 각 NIC에는 두 개의 IP 구성(1개의 IPv4 구성, 1개의 IPv6 구성)이 있습니다. [az 네트워크 nic ip-config를 사용하여](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)IPV6 구성을 만듭니다.
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)로 VM을 만듭니다. 다음 예제에서는 2개의 VM 및 아직 없는 경우 필요한 가상 네트워크 구성 요소를 만듭니다. 

다음과 같이 가상 컴퓨터 *dsVM0을* 만듭니다.

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

다음과 같이 가상 컴퓨터 *dsVM1을* 만듭니다.

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure 포털에서 IPv6 듀얼 스택 가상 네트워크 보기
다음과 같이 Azure 포털에서 IPv6 듀얼 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 표시줄에서 *dsVnet*을 입력합니다.
2. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다. 그러면 *dsVnet이라는*듀얼 스택 가상 네트워크의 **개요** 페이지가 시작됩니다. 듀얼 스택 가상 네트워크는 *dsSubnet라는*듀얼 스택 서브넷에 있는 IPv4 및 IPv6 구성이 있는 두 NIC를 보여줍니다.

  ![Azure의 IPv6 듀얼 스택 가상 네트워크](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 az 그룹 삭제 명령을 사용하여 리소스 [그룹,](/cli/azure/group#az-group-delete) VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 이중 프런트 엔드 IP 구성(IPv4 및 IPv6)을 갖춘 표준 로드 밸러커를 만들었습니다. 또한 부하 분산기의 백 엔드 풀에 추가된 듀얼 IP 구성(IPV4 + IPv6)이 포함된 두 개의 가상 컴퓨터를 만들었습니다. Azure 가상 네트워크에서 IPv6 지원에 대해 자세히 알아보려면 [Azure 가상 네트워크에 대한 IPv6이란 무엇입니까?](ipv6-overview.md)
