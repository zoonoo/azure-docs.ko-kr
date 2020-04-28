---
title: Azure PowerShell를 사용 하 여 부하 분산 및 아웃 바운드 규칙 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure PowerShell를 사용 하 여 표준 Load Balancer에서 부하 분산 및 아웃 바운드 규칙을 구성 하는 방법을 보여 줍니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74225434"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 표준 Load Balancer에서 부하 분산 및 아웃 바운드 규칙 구성

이 문서에서는 Azure PowerShell를 사용 하 여 표준 Load Balancer에서 아웃 바운드 규칙을 구성 하는 방법을 보여 줍니다.  

이 문서의 시나리오를 완료 하는 경우 부하 분산 장치 리소스에는 프런트 엔드 두 개와 연결 된 규칙이 포함 됩니다. 인바운드 트래픽에 대해 프런트 엔드가 하나 있고 아웃 바운드 트래픽에 대 한 다른 프런트 엔드가 있습니다.  

각 프런트 엔드는 공용 IP 주소를 참조 합니다. 이 시나리오에서 인바운드 트래픽에 대 한 공용 IP 주소는 아웃 바운드 트래픽의 주소와 다릅니다.   부하 분산 규칙은 인바운드 부하 분산만 제공 합니다. 아웃 바운드 규칙은 VM에 대 한 아웃 바운드 NAT (네트워크 주소 변환)를 제어 합니다.  

이 시나리오에서는 두 개의 백 엔드 풀을 사용 합니다. 하나는 인바운드 트래픽과 아웃 바운드 트래픽용으로 하나입니다. 이러한 풀은 기능을 설명 하 고 시나리오에 대 한 유연성을 제공 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Azure 계정에 연결
[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure 구독에 로그인합니다. 그런 다음 화면에 나타나는 지침에 따릅니다.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)를 사용 하 여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 되는 논리적 컨테이너입니다. 그런 다음 리소스는 그룹에서 관리 됩니다.

다음 예제에서는 *eastus2* 위치에 *myresourcegroupoutbound*라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>가상 네트워크 만들기
*Myvnetoutbound*이라는 가상 네트워크를 만듭니다. 해당 서브넷의 이름을 *mysubnetoutbound*로 합니다. [AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) 및 [AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)를 사용 하 여 *myresourcegroupoutbound 바운드* 에 저장 합니다.

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>인바운드 공용 IP 주소 만들기 

인터넷에서 웹 앱에 액세스 하려면 부하 분산 장치에 대 한 공용 IP 주소가 필요 합니다. 표준 Load Balancer는 표준 공용 IP 주소만 지원 합니다. 

[AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) 를 사용 하 여 *myresourcegroupoutbound*에 *mypublicipinbound* 이라는 표준 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>아웃 바운드 공용 IP 주소 만들기 

[AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)를 사용 하 여 부하 분산 장치의 프런트 엔드 아웃 바운드 구성에 대 한 표준 IP 주소를 만듭니다.

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Azure Load Balancer 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성 하는 방법을 설명 합니다.
  - 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신 하는 프런트 엔드 IP
  - 프런트 엔드 IP가 부하 분산 된 네트워크 트래픽을 전송 하는 백 엔드 풀
  - 아웃 바운드 연결용 백 엔드 풀
  - 백 엔드 VM 인스턴스의 상태를 결정 하는 상태 프로브
  - 트래픽을 Vm에 분산 하는 방법을 정의 하는 부하 분산 장치 인바운드 규칙
  - Vm에서 트래픽이 분산 되는 방법을 정의 하는 부하 분산 장치 아웃 바운드 규칙

### <a name="create-an-inbound-front-end-ip"></a>인바운드 프런트 엔드 IP 만들기
[AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)를 사용 하 여 부하 분산 장치에 대 한 인바운드 프런트 엔드 IP 구성을 만듭니다. 부하 분산 장치는 *myfrontendinbound*라는 인바운드 프런트 엔드 IP 구성을 포함 해야 합니다. 이 구성을 공용 IP 주소 *mypublicipinbound*연결 합니다.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>아웃 바운드 프런트 엔드 IP 만들기
[AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)를 사용 하 여 부하 분산 장치에 대 한 아웃 바운드 프런트 엔드 IP 구성을 만듭니다. 이 부하 분산 장치는 *myfrontendoutbound*라는 아웃 바운드 프런트 엔드 IP 구성을 포함 해야 합니다. 이 구성을 공용 IP 주소 *mypublicipoutbound*연결 합니다.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>인바운드 백 엔드 풀 만들기
[AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)를 사용 하 여 부하 분산 장치에 대 한 백 엔드 인바운드 풀을 만듭니다. 풀 이름을 *bepoolinbound*로 합니다.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>아웃 바운드 백 엔드 풀 만들기
다음 명령을 사용 하 여 [AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)를 사용 하 여 vm의 풀에 대 한 아웃 바운드 연결을 정의 하는 다른 백 엔드 주소 풀을 만듭니다. 이 풀의 이름을 *bepooloutbound*로 합니다. 

별도의 아웃 바운드 풀을 만들면 최대한의 유연성을 제공 합니다. 하지만 원하는 경우이 단계를 생략 하 고 인바운드 *bepoolinbound* 만 사용할 수 있습니다.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>상태 프로브 만들기

상태 프로브는 모든 VM 인스턴스를 검사 하 여 네트워크 트래픽을 보낼 수 있는지 확인 합니다. 프로브 검사에 실패 한 VM 인스턴스는 다시 온라인 상태가 되 고 프로브 검사에서 정상 상태가 되는 것으로 확인 될 때까지 부하 분산 장치에서 제거 됩니다. 

Vm의 상태를 모니터링 하려면 [AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0)를 사용 하 여 상태 프로브를 만듭니다. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 들어오는 트래픽에 대 한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 풀을 정의 합니다. 또한 필요한 원본 및 대상 포트를 정의 합니다. 

[AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0)를 사용 하 여 *myinboundlbrule* 이라는 부하 분산 장치 규칙을 만듭니다. 이 규칙은 프런트 엔드 풀 *myfrontendinbound*에서 80 포트를 수신 대기 합니다. 또한 포트 80을 사용 하 여 부하 분산 된 네트워크 트래픽을 백 엔드 주소 풀 *bepoolinbound*에 전송 합니다. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>이 부하 분산 규칙은 **-disableoutboundsnat 추가 됨** 매개 변수로 인해 자동 아웃 바운드 SNAT (secure NAT)를 사용 하지 않도록 설정 합니다. 아웃 바운드 NAT는 아웃 바운드 규칙에 의해서만 제공 됩니다.

### <a name="create-an-outbound-rule"></a>아웃바운드 규칙 만들기

아웃 바운드 규칙은 프런트 엔드 *myfrontendoutbound*으로 표현 되는 프런트 엔드 공용 IP를 정의 합니다. 이 프런트 엔드는 규칙이 적용 되는 백 엔드 풀 뿐만 아니라 모든 아웃 바운드 NAT 트래픽에 사용 됩니다.  

다음 명령을 사용 하 여 *bepool* 백 엔드 풀에 있는 모든 VM (NIC IP 구성)의 아웃 바운드 네트워크 변환에 대 한 아웃 바운드 규칙 *myoutboundrule* 을 만듭니다.  이 명령은 아웃 바운드 유휴 시간 제한 시간을 4 분에서 15 분으로 변경 합니다. 1024 대신 1만 SNAT 포트를 할당 합니다. 자세한 내용은 [AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)를 참조 하세요.

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
별도의 아웃 바운드 풀을 사용 하지 않으려면 앞의 명령에서 주소 풀 인수를 변경 하 여 *$bepoolin* 를 대신 지정할 수 있습니다.  별도의 풀을 사용 하 여 결과 구성을 유연 하 고 읽을 수 있도록 하는 것이 좋습니다.

### <a name="create-a-load-balancer"></a>부하 분산 장치 만들기

다음 명령을 사용 하 여 [AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0)를 사용 하 여 인바운드 IP 주소에 대 한 부하 분산 장치를 만듭니다. 부하 분산 장치에 이름을 *lb*로 합니다. 인바운드 프런트 엔드 IP 구성을 포함 해야 합니다. 백 엔드 풀 *bepoolinbound* 은 이전 단계에서 만든 공용 IP 주소 *mypublicipinbound* 연결 해야 합니다.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

이 시점에서 각 NIC 리소스의 IP 구성을 업데이트 하 여 *bepoolinbound* 및 *bepoolinbound* 백 엔드 풀 모두에 vm을 계속 추가할 수 있습니다. [AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)를 사용 하 여 리소스 구성을 업데이트 합니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, 부하 분산 장치 및 관련 리소스가 더 이상 필요 하지 않은 경우 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0)를 사용 하 여 제거할 수 있습니다.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>다음 단계
이 문서에서는 표준 부하 분산 장치를 만들고, 인바운드 및 아웃 바운드 부하 분산 장치 트래픽 규칙을 모두 구성 하 고, 백 엔드 풀의 Vm에 대 한 상태 프로브를 구성 했습니다. 

자세히 알아보려면 [Azure Load Balancer의 자습서](tutorial-load-balancer-standard-public-zone-redundant-portal.md)를 계속 진행 하세요.
