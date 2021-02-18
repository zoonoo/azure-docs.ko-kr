---
title: 가상 머신 확장 집합에서 사용 하는 기존 부하 분산 장치를 업데이트 하거나 삭제 합니다.
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: 이 방법 문서에서는 Azure 표준 Load Balancer 및 가상 머신 확장 집합을 시작 합니다.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 1228462dc6437ecce7718c4747d2acb9ae7332cb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593033"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>가상 머신 확장 집합에서 사용 하는 부하 분산 장치 업데이트 또는 삭제

Virtual machine scale sets 및 Azure Load Balancer 인스턴스로 작업 하는 경우 다음을 수행할 수 있습니다.

- 규칙을 추가, 업데이트 및 삭제 합니다.
- 구성을 추가 합니다.
- 부하 분산 장치를 삭제 합니다.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>가상 머신 확장 집합을 확장 하기 위한 부하 분산 장치 설정

Azure Load Balancer 인스턴스에 [인바운드 NAT 풀](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) 을 설정 하 고 가상 머신 확장 집합이 부하 분산 장치의 백 엔드 풀에 배치 되어 있는지 확인 합니다. 새 가상 머신 인스턴스가 가상 머신 확장 집합에 추가 되 면 Load Balancer에서 인바운드 NAT 풀에 새 인바운드 NAT 규칙을 자동으로 만듭니다.

인바운드 NAT 풀이 올바르게 설정 되었는지 확인 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다. 그런 다음 리소스 목록에서 **Myloadbalancer** 를 선택 합니다.
1. **설정** 아래에서 **인바운드 NAT 규칙** 을 선택 합니다. 오른쪽 창에는 가상 머신 확장 집합의 각 개별 인스턴스에 대해 생성 된 규칙 목록이 표시 되는 경우 언제 든 지 확장을 위해 모두 go로 설정 됩니다.

## <a name="add-inbound-nat-rules"></a>인바운드 NAT 규칙 추가

개별 인바운드 NAT 규칙을 추가할 수 없습니다. 하지만 가상 머신 확장 집합의 모든 인스턴스에 대해 정의 된 프런트 엔드 포트 범위 및 백 엔드 포트를 사용 하 여 인바운드 NAT 규칙 집합을 추가할 수 있습니다.

가상 머신 확장 집합에 대 한 인바운드 NAT 규칙의 전체 집합을 추가 하려면 먼저 부하 분산 장치에 인바운드 NAT 풀을 만듭니다. 그런 다음 가상 머신 확장 집합의 네트워크 프로필에서 인바운드 NAT 풀을 참조 합니다. CLI를 사용 하는 전체 예제가 나와 있습니다.

새 인바운드 NAT 풀에는 기존 인바운드 NAT 풀과 겹치는 프런트 엔드 포트 범위가 없어야 합니다. 설정 된 기존 인바운드 NAT 풀을 보려면 다음 [CLI 명령을](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list)사용 합니다.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>인바운드 NAT 규칙 업데이트

개별 인바운드 NAT 규칙을 업데이트할 수 없습니다. 하지만 가상 머신 확장 집합의 모든 인스턴스에 대 한 백 엔드 포트 및 정의 된 프런트 엔드 포트 범위를 사용 하 여 인바운드 NAT 규칙 집합을 업데이트할 수 있습니다.

가상 머신 확장 집합에 대 한 인바운드 NAT 규칙의 전체 집합을 업데이트 하려면 부하 분산 장치에서 인바운드 NAT 풀을 업데이트 합니다.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>인바운드 NAT 규칙 삭제

개별 인바운드 NAT 규칙은 삭제할 수 없지만 인바운드 NAT 풀을 삭제 하 여 전체 인바운드 NAT 규칙 집합을 삭제할 수 있습니다.

NAT 풀을 삭제 하려면 먼저 크기 집합에서 NAT 풀을 제거 합니다. CLI를 사용 하는 전체 예제는 다음과 같습니다.

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>여러 IP 구성 추가

여러 IP 구성을 추가 하려면:

1. 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다. 그런 다음 리소스 목록에서 **Myloadbalancer** 를 선택 합니다.
1. **설정** 에서 **프런트 엔드 IP 구성** 을 선택합니다. 그런 다음, **추가** 를 선택합니다.
1. **프런트 엔드 IP 주소 추가** 페이지에서 값을 입력 하 고 **확인** 을 선택 합니다.
1. 새 부하 분산 규칙이 필요한 경우이 자습서의 [5 단계](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) 와 [6 단계](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) 를 수행 합니다.
1. 필요한 경우 새로 만든 프런트 엔드 IP 구성을 사용 하 여 새 인바운드 NAT 규칙 집합을 만듭니다. 예제는 이전 섹션에 나와 있습니다.

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 사용 하는 프런트 엔드 IP 구성을 삭제 합니다.

확장 집합에서 사용 하는 프런트 엔드 IP 구성을 삭제 하려면 다음을 수행 합니다.

 1. 먼저 프런트 엔드 IP 구성을 참조 하는 인바운드 NAT 풀 (인바운드 NAT 규칙 집합)을 삭제 합니다. 인바운드 규칙을 삭제 하는 방법에 대 한 지침은 이전 섹션에서 찾을 수 있습니다.
 1. 프런트 엔드 IP 구성을 참조 하는 부하 분산 규칙을 삭제 합니다.
 1. 프런트 엔드 IP 구성을 삭제 합니다.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 사용 하는 부하 분산 장치 삭제

확장 집합에서 사용 하는 프런트 엔드 IP 구성을 삭제 하려면 다음을 수행 합니다.

 1. 먼저 프런트 엔드 IP 구성을 참조 하는 인바운드 NAT 풀 (인바운드 NAT 규칙 집합)을 삭제 합니다. 인바운드 규칙을 삭제 하는 방법에 대 한 지침은 이전 섹션에서 찾을 수 있습니다.
 1. 가상 머신 확장 집합을 포함 하는 백 엔드 풀을 참조 하는 부하 분산 규칙을 삭제 합니다.
 1. `loadBalancerBackendAddressPool`가상 머신 확장 집합의 네트워크 프로필에서 참조를 제거 합니다.
 
 CLI를 사용 하는 전체 예제는 다음과 같습니다.

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
마지막으로, 부하 분산 장치 리소스를 삭제 합니다.
 
## <a name="next-steps"></a>다음 단계

Azure Load Balancer 및 가상 머신 확장 집합에 대 한 자세한 내용은 개념에 대 한 자세한 내용을 참조 하세요.

> [가상 머신 확장 집합으로 Azure Load Balancer](load-balancer-standard-virtual-machine-scale-sets.md)
