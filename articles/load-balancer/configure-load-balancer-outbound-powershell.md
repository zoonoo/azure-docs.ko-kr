---
title: Azure PowerShell을 사용하여 로드 분산 및 아웃바운드 규칙 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure PowerShell을 사용하여 표준 로드 밸런서에서 로드 분산 및 아웃바운드 규칙을 구성하는 방법을 보여 주며 있습니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225434"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 표준 로드 밸런서에서 로드 분산 및 아웃바운드 규칙 구성

이 문서에서는 Azure PowerShell을 사용하여 표준 로드 밸러터에서 아웃바운드 규칙을 구성하는 방법을 보여 주며 있습니다.  

이 문서의 시나리오를 완료하면 로드 밸러버 리소스에는 두 개의 프런트 엔드와 관련 규칙이 포함됩니다. 인바운드 트래픽에 대한 프런트 엔드는 하나, 아웃바운드 트래픽에는 프런트 엔드가 있습니다.  

각 프런트 엔드는 공용 IP 주소를 참조합니다. 이 시나리오에서는 인바운드 트래픽에 대 한 공용 IP 주소 아웃 바운드 트래픽에 대 한 주소와 다릅니다.   로드 분산 규칙은 인바운드 로드 균형만 제공합니다. 아웃바운드 규칙은 VM에 대한 아웃바운드 네트워크 주소 변환(NAT)을 제어합니다.  

시나리오에서는 인바운드 트래픽에 대해 하나, 아웃바운드 트래픽에 대해 두 개의 백 엔드 풀을 사용합니다. 이러한 풀은 기능을 보여 주며 시나리오에 대한 유연성을 제공합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Azure 계정에 연결
[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure 구독에 로그인합니다. 그런 다음 화면에 나타나는 지침에 따릅니다.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup을](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포되는 논리 컨테이너입니다. 그런 다음 리소스가 그룹에서 관리됩니다.

다음 예제에서는 *eastus2* 위치에 *myresourcegroupoutbound*라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>가상 네트워크 만들기
*myvnetoutbound라는*가상 네트워크를 만듭니다. 그 서브넷 *mysubnetoutbound의 이름을 지정합니다.* [new-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) 및 [New-AzVirtualNetworkSubnetConfig를](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)사용하여 *myresourcegroupoutbound에* 배치합니다.

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>인바운드 공용 IP 주소 만들기 

인터넷에서 웹 앱에 액세스하려면 로드 밸런서에 대한 공용 IP 주소가 필요합니다. 표준 로드 밸런서는 표준 공용 IP 주소만 지원합니다. 

[New-AzPublicIpAddress를](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) 사용하여 *myresourcegroupoutbound에서* *mypublicipinbound라는* 표준 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>아웃바운드 공용 IP 주소 만들기 

[New-AzPublicIpAddress를](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)사용하여 로드 밸런서의 프런트 엔드 아웃바운드 구성에 대한 표준 IP 주소를 만듭니다.

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Azure Load Balancer 만들기

이 섹션에서는 로드 밸러블러의 다음 구성 요소를 만들고 구성하는 방법을 설명합니다.
  - 로드 밸러저에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP
  - 프런트 엔드 IP가 로드 균형 네트워크 트래픽을 보내는 백 엔드 풀
  - 아웃바운드 연결을 위한 백 엔드 풀
  - 백 엔드 VM 인스턴스의 상태를 결정하는 상태 프로브
  - 트래픽이 VM에 배포되는 방법을 정의하는 로드 밸러서 인바운드 규칙
  - VM에서 트래픽이 분산되는 방법을 정의하는 로드 밸러서 아웃바운드 규칙

### <a name="create-an-inbound-front-end-ip"></a>인바운드 프런트 엔드 IP 생성
[New-AzLoadBalancerFrontendIpConfig를](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)사용하여 로드 밸러블러에 대한 인바운드 프런트 엔드 IP 구성을 만듭니다. 로드 밸런서에는 *myfrontendinbound*라는 인바운드 프런트 엔드 IP 구성이 포함되어야 합니다. 이 구성을 공용 IP 주소 *mypublicipinbound.*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>아웃바운드 프런트 엔드 IP 생성
[New-AzLoadBalancerFrontendIpConfig를](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)사용하여 로드 밸러블러에 대한 아웃바운드 프런트 엔드 IP 구성을 만듭니다. 이 로드 밸런서에는 *myfrontendoutbound라는*아웃바운드 프런트 엔드 IP 구성이 포함되어야 합니다. 이 구성을 공용 IP 주소 *mypublicipoutbound.*

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>인바운드 백 엔드 풀 만들기
[New-AzLoadBalancerBackendAddressPoolConfig를](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)사용하여 로드 밸러터에 대한 백 엔드 인바운드 풀을 만듭니다. 풀 의 이름을 *bepoolinbound로 지정합니다.*

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>아웃바운드 백 엔드 풀 만들기
다음 명령을 사용하여 다른 백 엔드 주소 풀을 만들어 [New-AzLoadBalancerBackendAddressPoolConfig를](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)사용하여 VM 풀에 대한 아웃바운드 연결을 정의합니다. 이 풀의 이름을 *bepooloutbound.* 

별도의 아웃바운드 풀을 만들면 최대한의 유연성을 제공할 수 있습니다. 그러나 이 단계를 생략하고 원하는 경우 인바운드 *bepoolinbound만* 사용할 수 있습니다.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>상태 프로브 만들기

상태 프로브는 모든 VM 인스턴스를 검사하여 네트워크 트래픽을 보낼 수 있는지 확인합니다. 프로브 검사에 실패한 VM 인스턴스는 로드 밸러워에서 제거되어 다시 온라인 상태가 되고 프로브 검사를 통해 정상 상태가 확인됩니다. 

VM의 상태를 모니터링하려면 [New-AzLoadBalancerProbeConfig를](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0)사용하여 상태 프로브를 만듭니다. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>로드 밸러서 규칙 만들기

로드 밸러서 규칙은 들어오는 트래픽에 대한 프런트 엔드 IP 구성과 트래픽을 수신할 백 엔드 풀을 정의합니다. 또한 필요한 소스 및 대상 포트를 정의합니다. 

[New-AzLoadBalancerRuleConfig를](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0)사용하여 *myinboundlbrule이라는* 로드 밸러터 규칙을 만듭니다. 이 규칙은 프런트 엔드 풀 *myfrontendinbound에서*포트 80을 들을 것입니다. 또한 포트 80을 사용하여 로드 균형 네트워크 트래픽을 백 엔드 주소 풀 *bepoolinbound로*보냅니다. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>이 로드 균형 규칙은 **-DisableOutboundSNAT** 매개 변수로 인해 자동 아웃바운드 보안 NAT(SNAT)를 비활성화합니다. 아웃바운드 NAT는 아웃바운드 규칙에 의해서만 제공됩니다.

### <a name="create-an-outbound-rule"></a>아웃바운드 규칙 만들기

아웃바운드 규칙은 프런트 엔드 *myfrontendoutbound로*표시되는 프런트 엔드 공용 IP를 정의합니다. 이 프런트 엔드는 규칙이 적용되는 백 엔드 풀뿐만 아니라 모든 아웃바운드 NAT 트래픽에 사용됩니다.  

다음 명령을 사용하여 *bepool* 백 엔드 풀의 모든 VM(NIC IP 구성)의 아웃바운드 네트워크 변환에 대한 아웃바운드 규칙 *myoutboundrule을* 만듭니다.  명령은 아웃바운드 유휴 시간 초과시간을 4분에서 15분으로 변경합니다. 1,024개 대신 10,000개의 SNAT 포트를 할당합니다. 자세한 내용은 [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)를 참조하십시오.

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
별도의 아웃바운드 풀을 사용하지 않으려면 이전 명령의 주소 풀 인수를 변경하여 *대신 $bepoolin* 지정할 수 있습니다.  결과 구성을 유연하고 읽을 수 있도록 별도의 풀을 사용하는 것이 좋습니다.

### <a name="create-a-load-balancer"></a>부하 분산 장치 만들기

다음 명령을 사용하여 [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0)를 사용하여 인바운드 IP 주소에 대한 로드 밸러블러를 만듭니다. 로드 밸러블러 *lb의*이름을 지정합니다. 여기에는 인바운드 프런트 엔드 IP 구성이 포함되어야 합니다. 백 엔드 *풀 bepoolinbound* 는 이전 단계에서 만든 공용 IP 주소 *mypublicipinbound와* 연결되어야 합니다.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

이 시점에서 각 NIC 리소스의 IP 구성을 업데이트하여 *bepoolinbound* 및 *bepooloutbound* 백 엔드 풀에 VM을 계속 추가할 수 있습니다. [Add-AzNetworkInterfaceIpConfig를](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)사용하여 리소스 구성을 업데이트합니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, 로드 밸런서 및 관련 리소스가 더 이상 필요하지 않은 경우 [제거-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0)을 사용하여 리소스 그룹을 제거할 수 있습니다.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>다음 단계
이 문서에서는 표준 로드 밸러버를 만들고, 인바운드 및 아웃바운드 로드 밸러터 트래픽 규칙을 모두 구성하고, 백 엔드 풀의 VM에 대한 상태 프로브를 구성했습니다. 

자세한 내용을 보려면 [Azure 로드 밸러터](tutorial-load-balancer-standard-public-zone-redundant-portal.md)에 대한 자습서를 계속 하십시오.
