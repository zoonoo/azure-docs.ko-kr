---
title: "웹 응용 프로그램 방화벽 구성: Azure Application Gateway | Microsoft Docs"
description: "이 문서에서는 기존 또는 새 응용 프로그램 게이트웨이에 웹 응용 프로그램 방화벽을 사용하는 방법을 안내합니다."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: e8106805d21b325e33fb3ab376db75cd783b9042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>새 또는 기존 응용 프로그램 게이트웨이에 웹 응용 프로그램 방화벽 구성

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

WAF(웹 응용 프로그램 방화벽)를 사용하도록 설정된 응용 프로그램 게이트웨이를 만드는 방법에 대해 알아봅니다. 또한 WAF를 기존 응용 프로그램 게이트웨이에 추가하는 방법을 알아봅니다.

Azure Application Gateway의 WAF는 SQL 삽입 공격, 사이트 간 스크립팅 공격, 세션 하이재킹 등의 일반적인 웹 기반 공격으로부터 웹 응용 프로그램을 보호합니다.

 Application Gateway는 계층 7 부하 분산 장치입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다. Application Gateway는 많은 ADC(응용 프로그램 배달 컨트롤러) 기능을 제공합니다.

 * HTTP 부하 분산
 * 쿠키 기반 세션 선호도
 * SSL(Secure Sockets Layer) 오프로드
 * 사용자 지정 상태 프로브
 * 멀티 사이트 기능에 대한 지원
 
 지원되는 기능의 전체 목록을 찾으려면 [Application Gateway에 대한 개요](application-gateway-introduction.md)를 참조하세요.

이 문서에서는 [WAF를 기존 응용 프로그램 게이트웨이에 추가](#add-web-application-firewall-to-an-existing-application-gateway)하는 방법을 보여 줍니다. 또한 [WAF를 사용하여 응용 프로그램 게이트웨이를 만드는](#create-an-application-gateway-with-web-application-firewall) 방법을 보여 줍니다.

![시나리오 이미지][scenario]

## <a name="waf-configuration-differences"></a>WAF 구성 차이

[PowerShell을 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway-arm.md)를 읽어 보셨다면 응용 프로그램 게이트웨이를 만들 때 구성하는 SKU 설정에 대해 알고 계실 것입니다. WAF는 응용 프로그램 게이트웨이에 SKU를 구성할 때 정의하는 추가 설정을 제공합니다. Application Gateway 자체에서 추가로 변경해야 하는 사항은 없습니다.

| **설정** | **세부 정보**
|---|---|
|**SKU** |WAF가 없는 일반 응용 프로그램 게이트웨이는 **Standard\_Small**, **Standard\_Medium** 및 **Standard\_Large** 크기를 지원합니다. WAF의 도입으로 두 개의 SKU, 즉 **WAF\_Medium** 및 **WAF\_Large** SKU가 추가되었습니다. 소형 응용 프로그램 게이트웨이에는 WAF가 지원되지 않습니다.|
|**계층** | 사용 가능한 값은 **표준** 또는 **WAF**입니다. WAF를 사용하는 경우 **WAF**를 선택해야 합니다.|
|**모드** | 이 설정은 WAF 모드입니다. 허용되는 값은 **검색** 및 **방지**입니다. WAF를 **검색** 모드로 설정하면 모든 위협이 로그 파일에 저장됩니다. **방지** 모드에서는 이벤트를 여전히 기록하지만 공격자가 응용 프로그램 게이트웨이로부터 403 권한 없음 응답을 받습니다.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이에 웹 응용 프로그램 방화벽 추가

Azure PowerShell의 최신 버전을 사용하는지 확인합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

1. Azure 계정에 로그인합니다.

    ```powershell
    Login-AzureRmAccount
    ```

2. 이 시나리오에 사용할 구독을 선택합니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. WAF를 추가하려는 게이트웨이를 검색합니다.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. WAF SKU를 구성합니다. 사용 가능한 크기는 **WAF\_Large** 및 **WAF\_Medium**입니다. WAF를 사용하는 경우 계층은 **WAF**여야 합니다. SKU를 설정하는 경우 용량을 확인합니다.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. 다음 예제에 정의된 대로 WAF 설정을 구성합니다. **FirewallMode**의 경우 사용 가능한 값은 **방지** 및 **검색**입니다.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. 이전 단계에서 정의한 설정을 사용하여 응용 프로그램 게이트웨이를 업데이트합니다.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

이 명령은 WAF가 있는 응용 프로그램 게이트웨이를 업데이트합니다. 응용 프로그램 게이트웨이에 대한 로그를 보는 방법을 알아보려면 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요. WAF의 보안 특성상, 주기적으로 로그를 검토하여 웹 응용 프로그램의 보안 상태를 파악해야 합니다.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>웹 응용 프로그램 방화벽이 있는 응용 프로그램 게이트웨이 만들기

다음 단계에서는 WAF가 있는 응용 프로그램 게이트웨이를 만드는 전체 과정을 안내합니다.

Azure PowerShell의 최신 버전을 사용하는지 확인합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

1. `Login-AzureRmAccount`를 실행하여 Azure에 로그인합니다. 자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.

2. `Get-AzureRmSubscription`을 실행하여 계정에 대한 구독을 확인합니다.

3. 사용할 Azure 구독을 선택합니다.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Application Gateway에 대한 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

이전 예제에서는 "미국 서부"라는 위치로 "appgw-RG"라는 리소스 그룹을 만들었습니다.

> [!NOTE]
> 응용 프로그램 게이트웨이에 사용자 지정 프로브를 구성해야 하는 경우 [PowerShell을 사용하여 사용자 지정 프로브로 응용 프로그램 게이트웨이 만들기](application-gateway-create-probe-ps.md)를 참조하세요. 자세한 내용은 [사용자 지정 프로브 및 상태 모니터링](application-gateway-probe-overview.md)을 참조하세요.

### <a name="configure-a-virtual-network"></a>가상 네트워크 구성

응용 프로그램 게이트웨이에는 자체 서브넷이 필요합니다. 이 단계에서는 10.0.0.0/16 주소 공간과 두 서브넷(응용 프로그램 게이트웨이에 하나 및 백 엔드 풀 멤버에 하나)을 사용하여 가상 네트워크를 만듭니다.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>공용 IP 주소 구성

외부 요청을 처리하려면 응용 프로그램 게이트웨이에는 공용 IP 주소가 필요합니다. 이 공용 IP 주소에는 Application Gateway에서 사용되도록 정의된 `DomainNameLabel`이 있어서는 안됩니다.

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>응용 프로그램 게이트웨이 구성

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> 기본 WAF 구성을 사용하여 만든 응용 프로그램 게이트웨이는 보호를 위해 CRS 3.0으로 구성됩니다.

## <a name="get-an-application-gateway-dns-name"></a>응용 프로그램 게이트웨이 DNS 이름 가져오기

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 응용 프로그램 게이트웨이는 친근한 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 최종 사용자가 응용 프로그램 게이트웨이를 누를 수 있도록 하려면 CNAME 레코드를 사용하여 응용 프로그램 게이트웨이의 공용 끝점을 가리키도록 합니다. 자세한 내용은 [Azure 클라우드 서비스에서 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요. 

별칭을 구성하려면 응용 프로그램 게이트웨이에 연결된 PublicIPAddress 요소를 사용하여 응용 프로그램 게이트웨이 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름을 사용하여 두 개의 웹 응용 프로그램을 이 DNS 이름으로 가리키는 CNAME 레코드를 만듭니다. VIP가 응용 프로그램 게이트웨이를 다시 시작할 때 변경될 수 있으므로 A 레코드 사용은 권장하지 않습니다.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
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

진단 로깅을 구성하여 WAF를 통해 검색 또는 방지되는 이벤트를 기록하는 방법을 알아보려면 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요.

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png
