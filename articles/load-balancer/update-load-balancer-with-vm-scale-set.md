---
title: 가상 컴퓨터 확장 집합에 사용 되는 기존 Azure Load Balancer 업데이트 또는 삭제
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: 이 방법 문서에서는 Azure 표준 Load Balancer 및 Virtual Machine Scale Sets를 시작 합니다.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: f8f664375e53a1cef28b0c7b95207770434f67fa
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97893269"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets에서 사용 하는 Azure Load Balancer을 업데이트/삭제 하는 방법

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>규모 확장 Virtual Machine Scale Sets에 대 한 Azure Load Balancer를 설정 하는 방법
  * Load Balancer [인바운드 NAT 풀](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) 을 설정 하 고 가상 머신 확장 집합이 Load Balancer의 백 엔드 풀에 배치 되어 있는지 확인 합니다. 새 가상 머신 인스턴스가 가상 머신 확장 집합에 추가 되 면 Azure Load Balancer에서 인바운드 NAT 풀에 새 인바운드 NAT 규칙을 자동으로 만듭니다. 
  * 인바운드 NAT 풀이 올바르게 설정 되었는지 확인 하려면 
  1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
  
  1. 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **MyLoadBalancer** 를 선택합니다.
  
  1. **설정** 아래에서 **인바운드 NAT 규칙** 을 선택 합니다.
오른쪽 창에는 가상 머신 확장 집합의 각 개별 인스턴스에 대해 생성 된 규칙 목록이 표시 되 면 언제 든 지 확장 하기 위해 축 하 합니다 설정 됩니다.

## <a name="how-to-add-inbound-nat-rules"></a>인바운드 NAT 규칙을 추가 하는 방법 
  * 개별 인바운드 NAT 규칙을 추가할 수 없습니다. 그러나 가상 머신 확장 집합의 모든 인스턴스에 대해 정의 된 프론트 엔드 포트 범위와 백 엔드 포트를 사용 하 여 인바운드 NAT 규칙 집합을 추가할 수 있습니다.
  * Virtual Machine Scale Sets에 대 한 인바운드 NAT 규칙의 전체 집합을 추가 하려면 먼저 Load Balancer에서 인바운드 NAT 풀을 만든 다음, 가상 머신 확장 집합의 네트워크 프로필에서 인바운드 NAT 풀을 참조 해야 합니다. CLI를 사용 하는 전체 예제는 다음과 같습니다.
  * 새 인바운드 NAT 풀에는 기존 인바운드 NAT 풀과 겹치는 프런트 엔드 포트 범위가 없어야 합니다. 기존 인바운드 NAT 풀 설정을 보려면이 [CLI 명령을](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list) 사용할 수 있습니다.
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
## <a name="how-to-update-inbound-nat-rules"></a>인바운드 NAT 규칙을 업데이트 하는 방법 
  * 개별 인바운드 NAT 규칙을 업데이트할 수 없습니다. 그러나 가상 머신 확장 집합의 모든 인스턴스에 대해 정의 된 프론트 엔드 포트 범위와 백 엔드 포트를 사용 하 여 인바운드 NAT 규칙 집합을 업데이트할 수 있습니다.
  * Virtual Machine Scale Sets에 대 한 인바운드 NAT 규칙의 전체 집합을 업데이트 하려면 Load Balancer에서 인바운드 NAT 풀을 업데이트 해야 합니다. 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>인바운드 NAT 규칙을 삭제 하는 방법 
* 개별 인바운드 NAT 규칙을 삭제할 수 없습니다. 그러나 인바운드 NAT 규칙의 전체 집합을 삭제할 수 있습니다.
* 확장 집합에서 사용 하는 인바운드 NAT 규칙의 전체 집합을 삭제 하려면 먼저 확장 집합에서 NAT 풀을 제거 해야 합니다. CLI를 사용 하는 전체 예제는 다음과 같습니다.
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>여러 IP 구성을 추가 하는 방법:
1. 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **MyLoadBalancer** 를 선택합니다.
   
1. **설정** 아래에서 **프런트 엔드 IP 구성** 을 선택 하 고 **추가** 를 선택 합니다.
   
1. **프런트 엔드 IP 주소 추가** 페이지에서 값을 입력 하 고 **확인** 을 선택 합니다.

1. 새 부하 분산 규칙이 필요한 경우이 자습서의 [5 단계](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) 와 [6 단계](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) 를 수행 합니다.

1. 필요한 경우 새로 만든 프런트 엔드 IP 구성을 사용 하 여 새 인바운드 NAT 규칙 집합을 만듭니다. 예제는 [이전 섹션]에서 찾을 수 있습니다.

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>가상 머신 확장 집합에 사용 되는 프런트 엔드 IP 구성을 삭제 하는 방법: 
 1. 확장 집합에서 사용 하는 프런트 엔드 IP 구성을 삭제 하려면 먼저 프런트 엔드 IP 구성을 참조 하는 인바운드 nat 풀 (인바운드 NAT 규칙 집합)을 삭제 해야 합니다. 인바운드 규칙을 삭제 하는 방법에 대 한 지침은 이전 섹션에서 찾을 수 있습니다.
 1. 프런트 엔드 IP 구성을 참조 하는 부하 분산 규칙을 삭제 합니다. 
 1. 프런트 엔드 IP 구성을 삭제 합니다.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>가상 컴퓨터 확장 집합에서 사용 하는 Azure Load Balancer를 삭제 하는 방법: 
 1. 확장 집합에서 사용 하는 프런트 엔드 IP 구성을 삭제 하려면 먼저 프런트 엔드 IP 구성을 참조 하는 인바운드 nat 풀 (인바운드 NAT 규칙 집합)을 삭제 해야 합니다. 인바운드 규칙을 삭제 하는 방법에 대 한 지침은 이전 섹션에서 찾을 수 있습니다.
 
 1. 가상 머신 확장 집합을 포함 하는 백 엔드 풀을 참조 하는 부하 분산 규칙을 삭제 합니다.
 
 1. 가상 머신 확장 집합의 네트워크 프로필에서 loadBalancerBackendAddressPool 참조를 제거 합니다. CLI를 사용 하는 전체 예제는 다음과 같습니다.
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
마지막으로 Load Balancer 리소스를 삭제 합니다.
 
## <a name="next-steps"></a>다음 단계

Azure Load Balancer 및 가상 머신 확장 집합에 대 한 자세한 내용은 개념에 대 한 자세한 내용을 참조 하세요.

> [Azure 가상 머신 확장 집합을 사용 하 여 Azure Load Balancer](load-balancer-standard-virtual-machine-scale-sets.md)
