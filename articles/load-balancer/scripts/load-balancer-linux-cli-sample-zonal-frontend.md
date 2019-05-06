---
title: CLI 예제 - 영역 내부의 Load Balancer VM - Azure | Microsoft Docs
description: 이 Azure CLI 스크립트 예제는 특정 가용성 영역 내의 VM에 트래픽을 부하 분산하는 방법을 보여줍니다.
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: 121d3f941f3d00373bd2821534c3e601107e97e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506963"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Azure CLI 스크립트 예제: 고가용성을 위해 VM에 트래픽 부하 분산

이 Azure CLI 스크립트 예제에서는 특정 가용성 영역 내에서 항상 사용 가능하고 부하 분산된 구성에서 구성된 여러 Ubuntu 가상 머신을 실행하는 데 필요한 모든 항목을 만듭니다. 스크립트를 실행하면 Azure 표준 Load Balancer를 통해 액세스할 수 있는 지역 내의 단일 가용성 집합에 3개의 가상 머신이 생성됩니다. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

```azurecli-interactive
  #!/bin/bash

  # Create a resource group.
   az group create \
    --name myResourceGroup \
    --location westeurope

  # Create a virtual network.
   az network vnet create \
    --resource-group myResourceGroup \
    --location westeurope \
    --name myVnet \
    --subnet-name mySubnet

  # Create a zonal Standard public IP address.
   az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --sku Standard
    --zone 1

  # Create an Azure Load Balancer.
   az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --sku Standard

  # Creates an LB probe on port 80.
   az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80

  # Creates an LB rule for port 80.
   az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRuleWeb \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe

  # Create three NAT rules for port 22.
   for i in `seq 1 3`; do
    az network lb inbound-nat-rule create \
     --resource-group myResourceGroup \
     --lb-name myLoadBalancer \
     --name myLoadBalancerRuleSSH$i \
     --protocol tcp \
     --frontend-port 422$i \
     --backend-port 22 \
     --frontend-ip-name myFrontEndPool
   done

  # Create a network security group
   az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup

  # Create a network security group rule for port 22.
   az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*'  \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 1000

  # Create a network security group rule for port 80.
    az network nsg rule create \
     --resource-group myResourceGroup \
     --nsg-name myNetworkSecurityGroup \
     --name myNetworkSecurityGroupRuleHTTP \
     --protocol tcp \
     --direction inbound \
     --source-address-prefix '*' \
     --source-port-range '*' \
     --destination-address-prefix '*' \
     --destination-port-range 80 \
     --access allow \
     --priority 2000

  # Create three virtual network cards and associate with public IP address and NSG.
  for i in `seq 1 3`; do
   az network nic create \
     --resource-group myResourceGroup \
     --name myNic$i \
     --vnet-name myVnet \
     --subnet mySubnet \
     --network-security-group myNetworkSecurityGroup \
     --lb-name myLoadBalancer \
     --lb-address-pools myBackEndPool \
     --lb-inbound-nat-rules myLoadBalancerRuleSSH$i
  done

# Create three virtual machines, this creates SSH keys if not present.
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroup \
    --name myVM$i \
    --zone 1 \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
done

```

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 머신, 가용성 집합, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-network-vnet-create) | Azure Virtual Network 및 서브넷을 만듭니다. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-create) | 고정 IP 주소 및 연결된 DNS 이름을 사용하여 공용 IP 주소를 만듭니다. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az-network-lb-create) | Azure Load Balancer를 만듭니다. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az-network-lb-probe-create) | 부하 분산 장치 프로브를 만듭니다. 부하 분산 장치 프로브는 부하 분산 장치 집합의 각 VM을 모니터링하는 데 사용됩니다. 모든 VM이 액세스할 수 없게 되면 트래픽은 VM에 라우팅되지 않습니다. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | 부하 분산 장치 규칙을 만듭니다. 이 샘플에서는 포트 80에 대한 규칙을 만듭니다. HTTP 트래픽이 부하 분산 장치에 도착하면 LB 집합에서 VM 중 하나인 포트 80에 라우팅됩니다. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | 부하 분산 장치 NAT(네트워크 주소 변환) 규칙을 만듭니다.  NAT 규칙은 VM에 있는 포트에 부하 분산 장치의 포트를 매핑합니다. 이 샘플에서는 부하 분산 장치 집합의 각 VM에 SSH 트래픽에 대한 NAT 규칙을 만듭니다.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#az-network-nsg-create) | 인터넷과 가상 컴퓨터 간에 보안 경계인 NSG(네트워크 보안 그룹)을 만듭니다. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | 인바운드 트래픽을 허용하도록 NSG 규칙을 만듭니다. 이 샘플에서 SSH 트래픽에 대해 포트 22가 열립니다. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az-network-nic-create) | 가상 네트워크 카드를 만들고 가상 네트워크, 서브넷 및 NSG에 연결합니다. |
| [az vm create](/cli/azure/vm#az-vm-create) | 가상 머신을 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 NSG에 연결합니다. 또한 이 명령은 사용할 가상 머신 이미지와 관리 자격 증명을 지정합니다.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 Azure 네트워킹 CLI 스크립트 샘플은 [Azure 네트워킹 설명서](../cli-samples.md)에서 찾을 수 있습니다.