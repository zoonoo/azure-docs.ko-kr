---
title: Azure PowerShell을 사용하여 Azure 개인 링크 서비스 만들기| 마이크로 소프트 문서
description: Azure PowerShell을 사용하여 Azure 개인 링크 서비스를 만드는 방법에 대해 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932073"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Azure PowerShell을 사용하여 개인 링크 서비스 만들기
이 문서에서는 Azure PowerShell을 사용하여 Azure에서 개인 링크 서비스를 만드는 방법을 보여 주며 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

로컬에서 PowerShell을 설치하고 사용하도록 선택한 경우 이 문서에는 최신 Azure PowerShell 모듈 버전이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

개인 링크를 만들려면 먼저 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *WestCentralUS* 위치에 *myResourceGroup이라는* 리소스 그룹을 만듭니다.

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[New-AzVirtualNetwork를](/powershell/module/az.network/new-azvirtualnetwork)사용하여 개인 링크에 대한 가상 네트워크를 만듭니다. 다음 예제에서는 프런트 엔드 *(frontendSubnet),* 백 엔드 *(백 엔드 서브넷),* 개인 링크 *(기타 Subnet)에*대한 서브넷이있는 *myvnet이라는* 가상 네트워크를 만듭니다.

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>내부 부하 밸러블러 생성
[New-AzLoadBalancer를](/powershell/module/az.network/new-azloadbalancer)사용하여 내부 표준 로드 밸런서를 만듭니다. 다음 예제에서는 앞의 단계에서 만든 프런트 엔드 IP 구성, 프로브, 규칙 및 백 엔드 풀을 사용하여 내부 표준 로드 밸런서를 만듭니다.

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Private Link 서비스 만들기
[New-AzPrivateLinkService를](/powershell/module/az.network/new-azloadbalancer)사용하여 개인 링크 서비스를 만듭니다.  이 예제에서는 *myResourceGroup이라는*리소스 그룹의 표준 로드 밸러서를 사용하여 *myPLS라는* 개인 링크 서비스를 만듭니다. 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>개인 링크 서비스 받기
[Get-AzPrivateLinkService를](/powershell/module/az.network/get-azprivatelinkservice) 통해 개인 링크 서비스에 대한 자세한 내용을 다음과 같이 가져옵니다.

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

이 단계에서 개인 링크 서비스가 성공적으로 만들어지고 트래픽을 받을 준비가 되었습니다. 위의 예제는 PowerShell을 사용하여 개인 링크 서비스를 만드는 것을 보여 주기 위한 것입니다.  트래픽을 수신하기 위해 로드 밸러서 백 엔드 풀 또는 백 엔드 풀의 응용 프로그램을 구성하지 않았습니다. 끝 간 트래픽 흐름을 보려면 표준 로드 밸러터 뒤에 응용 프로그램을 구성하는 것이 좋습니다. 

다음으로 PowerShell을 사용하여 다른 VNet의 개인 끝점에 이 서비스를 매핑하는 방법을 설명합니다. 다시 말하지만, 이 예제는 개인 끝점을 만들고 위에서 만든 개인 링크 서비스에 연결하는 것으로 제한됩니다. 가상 네트워크에서 가상 컴퓨터를 만들어 시나리오를 빌드하기 위해 개인 끝점으로 트래픽을 보내/받을 수 있습니다. 

## <a name="create-a-private-endpoint"></a>Private Endpoint 만들기
### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[New-AzVirtualNetwork를](/powershell/module/az.network/new-azvirtualnetwork)사용하여 개인 엔드포인트에 대한 가상 네트워크를 만듭니다. 이 예제에서는 *myResourceGroup이라는*리소스 그룹에서 *vnetPE라는* 가상 네트워크를 만듭니다.
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기
가상 네트워크에서 위에서 만든 개인 링크 서비스를 사용할 수 있는 개인 끝점을 만듭니다.
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>프라이빗 엔드포인트 받기
다음과 `Get-AzPrivateEndpoint` 같이 개인 끝점의 IP 주소를 가져옵니다.

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>개인 끝점 연결 승인
'승인-AzPrivateEndpointConnection'를 사용하여 개인 링크 서비스에 대한 개인 끝점 연결을 승인합니다.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>다음 단계
- [Azure 개인 링크에](private-link-overview.md) 대해 자세히 알아보기
 
