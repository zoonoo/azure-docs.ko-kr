---
title: 사용자 지정 프로브 만들기 - Azure Application Gateway - PowerShell | Microsoft Docs
description: 리소스 관리자에서 PowerShell을 사용하여 Application Gateway에 대한 사용자 지정 프로브를 만드는 방법에 대해 알아봅니다.
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: acd70bacd23755cd764bc782a297d80db3622424
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66135239"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Azure Resource Manager에 대해 PowerShell을 사용하여 Azure Application Gateway에 대한 사용자 지정 프로브 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 클래식 PowerShell](application-gateway-create-probe-classic-ps.md)

이 문서에서는 PowerShell을 사용하여 기존 애플리케이션 게이트웨이에 사용자 지정 프로브를 추가합니다. 사용자 지정 프로브는 특정 상태 확인 페이지를 사용하는 애플리케이션이나 기본 웹 애플리케이션에서 성공적으로 응답을 제공하지 않는 애플리케이션에 유용합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>사용자 지정 프로브를 사용하여 애플리케이션 게이트웨이 만들기

### <a name="sign-in-and-create-resource-group"></a>로그인하여 리소스 그룹 만들기

1. `Connect-AzAccount`을 사용하여 인증합니다.

   ```powershell
   Connect-AzAccount
   ```

1. 계정에 대한 구독을 가져옵니다.

   ```powershell
   Get-AzSubscription
   ```

1. 사용할 Azure 구독을 선택합니다.

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 애플리케이션 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

이전 예제에서는 **West US** 위치에 **appgw-RG**라는 리소스 그룹을 만들었습니다.

### <a name="create-a-virtual-network-and-a-subnet"></a>가상 네트워크 및 서브넷 만들기

다음 예제에서는 애플리케이션 게이트웨이에 대한 가상 네트워크와 서브넷을 만듭니다. 애플리케이션 게이트웨이에는 자체 서브넷이 필요합니다. 이러한 이유로 애플리케이션 게이트웨이를 위해 만드는 서브넷은 VNET의 주소 공간보다 작아야 다른 서브넷을 만들고 사용할 수 있습니다.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>프런트 엔드 구성에 대한 공용 IP 주소 만들기

미국 서부 지역의 리소스 그룹 **appgw-rg**에 공용 IP 리소스 **publicIP01**을 만듭니다. 이 예제에서는 애플리케이션 게이트웨이의 프런트 엔드 IP 주소에 공용 IP 주소를 사용합니다.  애플리케이션 게이트웨이를 사용하려면 공용 IP 주소에 동적으로 만들어진 DNS 이름이 있어야 하므로 공용 IP 주소를 만드는 동안에는 `-DomainNameLabel`을 지정할 수 없습니다.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

Application Gateway를 만들기 전에 모든 구성 항목을 설정합니다. 다음 예제에서는 애플리케이션 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

| **구성 요소** | **설명** |
|---|---|
| **게이트웨이 IP 구성** | 애플리케이션 게이트웨이에 대한 IP 구성입니다.|
| **백 엔드 풀** | 웹 애플리케이션을 호스팅하는 애플리케이션 서버에 대한 IP 주소, FQDN 또는 NIC의 풀입니다.|
| **상태 프로브** | 백 엔드 풀 멤버의 상태를 모니터링하는 데 사용되는 사용자 지정 프로브입니다|
| **HTTP 설정** | 포트, 프로토콜, 쿠키 기반 선호도, 프로브 및 시간 제한을 포함한 설정의 모음입니다.  이러한 설정은 트래픽이 백 엔드 풀 멤버로 라우팅되는 방법을 결정합니다.|
| **프런트 엔드 포트** | 애플리케이션 게이트웨이에서 트래픽을 수신 대기하는 포트입니다.|
| **수신기** | 프로토콜, 프론트 엔드 IP 구성 및 프론트 엔드 포트의 조합이며, 들어오는 요청을 수신 대기하는 것입니다.
|**규칙**| HTTP 설정에 따라 트래픽을 적절한 백 엔드로 라우팅합니다.|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>기존 애플리케이션 게이트웨이에 프로브 추가

다음 코드 조각에서는 기존 애플리케이션 게이트웨이에 프로브를 추가합니다.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>기존 애플리케이션 게이트웨이에서 프로브 제거

다음 코드 조각에서는 기존 애플리케이션 게이트웨이에서 프로브를 제거합니다.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>애플리케이션 게이트웨이 DNS 이름 가져오기

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 Application Gateway는 식별 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 최종 사용자가 애플리케이션 게이트웨이를 누를 수 있도록 하려면 CNAME 레코드를 사용하여 애플리케이션 게이트웨이의 공용 엔드포인트를 가리키도록 합니다. [Azure에서 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md). 이 작업을 수행하려면 Application Gateway에 연결된 PublicIPAddress 요소를 사용하여 Application Gateway 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 애플리케이션 게이트웨이의 DNS 이름은 두 개의 웹 애플리케이션을 이 DNS 이름으로 가리키는 CNAME 레코드를 만드는 데 사용됩니다. A 레코드를 사용할 경우 애플리케이션 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 이는 권장되지 않습니다.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>다음 단계

방문 하 여 SSL 오프 로드를 구성 하는 방법을 알아봅니다. [SSL 오프 로드 구성](application-gateway-ssl-arm.md)

