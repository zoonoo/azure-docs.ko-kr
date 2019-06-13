---
title: '빠른 시작: 기본 부하 분산 장치 만들기 - Azure PowerShell'
titlesuffix: Azure Load Balancer
description: 이 빠른 시작은 PowerShell을 사용하여 기본 Load Balancer를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms:custom: seodec18
ms.openlocfilehash: 5b77087557bc4ca1bea3cb4a36a50d787a5edf4d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729839"
---
# <a name="get-started"></a>빠른 시작: Azure PowerShell을 사용하여 공용 부하 분산 장치 만들기

이 빠른 시작은 Azure PowerShell을 사용하여 기본 Load Balancer를 만드는 방법을 보여줍니다. 부하 분산 장치를 테스트하려면 Windows 서버를 실행하는 두 VM(가상 머신)을 배포하고 두 VM 사이에 있는 웹앱의 부하를 분산합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

부하 분산 장치를 만들려면 먼저 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *EastUS* 위치에 *myResourceGroupLB*라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기
인터넷에서 앱에 액세스하려면 부하 분산 장치에 대한 공용 IP 주소가 필요합니다. [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 *myResourceGroupLB* 리소스 그룹에 *myPublicIP*라는 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

## <a name="create-basic-load-balancer"></a>기본 부하 분산 장치 만들기

이 섹션에서는 부하 분산 장치에 대해 프런트 엔드 IP 및 백 엔드 주소 풀을 구성한 다음, 기본 Load Balancer를 만듭니다.

### <a name="create-front-end-ip"></a>프런트 엔드 IP 만들기

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 프런트 엔드 IP를 만듭니다. 다음 예제에서는 *myFrontEnd*라는 프런트 엔드 IP 구성을 만들고 *myPublicIP* 주소를 연결합니다.

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-back-end-address-pool"></a>백 엔드 주소 풀 구성

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀을 만듭니다. VM은 나머지 단계에서 이 백 엔드 풀에 연결됩니다. 다음 예제는 *myBackEndPool*이라는 백 엔드 주소 풀을 만듭니다.

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

### <a name="create-a-health-probe"></a>상태 프로브 만들기
부하 분산 장치가 앱의 상태를 모니터링하도록 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. 기본적으로 VM은 15초 간격으로 두 번의 연속 실패 후에 부하 분산 장치 분산에서 제거됩니다. 앱의 프로토콜 또는 특정 상태 확인 페이지에 따라 상태 프로브를 만듭니다. 

다음 예제에서는 TCP 프로브를 만듭니다. 좀 더 미세 조정된 상태 검사를 위해 사용자 지정 HTTP 프로브를 만들 수도 있습니다. 사용자 지정 HTTP 프로브를 사용할 경우 *healthcheck.aspx*와 같은 상태 확인 페이지를 만들어야 합니다. 부하 분산 상태가 호스트를 순환 상태를 유지하려면 프로브는 **HTTP 200 정상** 응답을 반환해야 합니다.

TCP 상태 프로브를 만들려면 [Add-Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig)를 사용합니다. 다음 예제에서는 *HTTP* 포트 *80*에서 각 VM을 모니터링하는 *myHealthProbe*라는 상태 프로브를 만듭니다.

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 정상 VM만 트래픽을 수신하도록 하려면 사용할 상태 프로브도 정의합니다.

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제는 *myLoadBalancerRule*이라는 부하 분산 장치 규칙을 만들고 *TCP* 포트 *80*에서 트래픽 부하를 분산합니다.

```azurepowershell-interactive
$lbrule = New-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>NAT 규칙 만들기

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig)를 사용하여 NAT 규칙을 만듭니다. 다음 예제에서는 포트 4221 및 4222로 백 엔드 서버에 RDP 연결을 허용하도록 *myLoadBalancerRDP1* 및 *myLoadBalancerRDP2*라는 NAT 규칙을 만듭니다.

```azurepowershell-interactive
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>부하 분산 장치 만들기

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 기본 Load Balancer를 만듭니다. 다음 예제는 이전 단계에서 만든 프런트 엔드 IP 구성, 백 엔드 풀, 상태 프로브, 부하 분산 규칙 및 NAT 규칙을 사용하여 myLoadBalancer라는 공용 기본 Load Balancer를 만듭니다.

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

일부 VM을 배포하고 부하 분산 장치를 테스트하려면 지원하는 네트워크 리소스 - 가상 네트워크 및 가상 NIC를 만들어야 합니다. 

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *myVnet*이라는 가상 네트워크와 *mySubnet*을 만듭니다.

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기

가상 네트워크에 대한 인바운드 연결을 정의하는 네트워크 보안 그룹을 만듭니다. 네트워크 보안 그룹을 만들어 가상 네트워크에 대한 인바운드 연결을 정의합니다.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>포트 3389에 대한 네트워크 보안 그룹 규칙 만들기

포트 3389를 통해 RDP 연결을 허용하도록 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>포트 80에 대한 네트워크 보안 그룹 규칙 만들기

포트 80을 통해 인바운드 연결을 허용하도록 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup' `
-SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>NIC 만들기

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 만든 가상 NIC를 만듭니다. 다음 예제에서는 2개의 가상 NIC를 만듭니다. (다음 단계에서 앱에 대해 만드는 각 VM에 대해 가상 NIC 하나씩) 언제든지 추가 가상 NIC 및 VM을 만든 후 부하 분산 장치에 추가할 수 있습니다.

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

앱의 고가용성을 향상시키려면 VM을 가용성 집합에 배치합니다.

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)을 사용하여 가용성 집합을 만듭니다. 다음 예제는 *myAvailabilitySet*이라는 가용성 집합을 만듭니다.

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 수 있습니다. 다음 예제에서는 2개의 VM 및 아직 없는 경우 필요한 가상 네트워크 구성 요소를 만듭니다. 이 예제에서는 이전 단계에서 생성된 NIC(*VM1* 및 *VM2*)가 동일한 이름으로 동일한 가상 네트워크(*myVnet*)와 서브넷(*mySubnet*)이 할당되므로, 가상 머신 *VM1* 및 *VM2*에 자동으로 할당됩니다. 또한 NIC는 부하 분산 장치의 백 엔드 풀에 연결되어 있으므로 VM 백 엔드 풀에 자동으로 추가됩니다.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

`-AsJob` 매개 변수는 VM을 백그라운드 작업으로 만들므로 PowerShell 프롬프트가 반환됩니다. `Job` cmdlet을 사용하여 백그라운드 작업의 세부 정보를 볼 수 있습니다. 두 VM을 만들고 구성하는 데 몇 분 정도 소요됩니다.

### <a name="install-iis-with-custom-web-page"></a>사용자 지정 웹 페이지로 IIS 설치
 
다음과 같이 두 백 엔드 VM에 사용자 지정 웹 페이지로 IIS를 설치합니다.

1. Load Balancer의 공용 IP 주소를 가져옵니다. `Get-AzPublicIPAddress`를 사용하여 Load Balancer의 공용 IP 주소를 가져옵니다.

   ```azurepowershell-interactive
    Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
   ```
2. 이전 단계에서 가져온 공용 IP 주소를 사용하여 VM1에 대한 원격 데스크톱 연결을 만듭니다. 

   ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
   ```
3. *VM1*에 대한 자격 증명을 입력하여 RDP 세션을 시작합니다.
4. VM1에서 Windows PowerShell을 실행하고, 다음 명령을 사용하여 IIS 서버를 설치하고 기본 htm 파일을 업데이트합니다.
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. *myVM1*과의 RDP 연결을 종료합니다.
6. `mstsc /v:PublicIpAddress:4222` 명령을 실행하여 *myVM2*로 RDP 연결을 만들고, *VM2*에 대해 4단계를 반복합니다.

## <a name="test-load-balancer"></a>부하 분산 장치 테스트
[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 앞서 만든 *myPublicIP*의 IP 주소를 가져옵니다.

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

그런 다음 웹 브라우저에 공용 IP 주소를 입력할 수 있습니다. 다음 예제와 같이 부하 분산 장치가 트래픽을 분산한 VM의 호스트 이름을 포함하여 웹 사이트가 표시됩니다.

![부하 분산 장치 테스트](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

부하 분산 장치가 앱을 실행 중인 두 VM에 트래픽을 분산하고 있는지 확인하려면 웹 브라우저를 강제로 새로 고칩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기본 Load Balancer를 만들고, 거기에 VM을 연결하고, 부하 분산 장치 트래픽 규칙 및 상태 프로브를 구성한 다음, 부하 분산 장치를 테스트했습니다. Azure Load Balancer에 대해 자세히 알아보려면 Azure Load Balancer에 대한 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Load Balancer 자습서](tutorial-load-balancer-basic-internal-portal.md)