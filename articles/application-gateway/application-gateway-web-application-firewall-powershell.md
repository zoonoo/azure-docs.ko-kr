---
title: "웹 응용 프로그램 방화벽 구성 - Azure Application Gateway | Microsoft Docs"
description: "이 문서에서는 기존 또는 새 Application Gateway에 웹 응용 프로그램 방화벽을 사용하는 방법을 안내합니다."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 52b7728c3fc702e37f5c5fe3d6544117a11464e8
ms.lasthandoff: 03/30/2017


---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>새 또는 기존 Application Gateway에 웹 응용 프로그램 방화벽 구성

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-web-application-firewall-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-web-application-firewall-powershell.md)

Azure Application Gateway의 웹 응용 프로그램 방화벽(WAF)은 SQL 삽입 공격, 사이트 간 스크립팅 공격, 세션 하이재킹 등의 일반적인 웹 기반 공격으로부터 웹 응용 프로그램을 보호합니다.

Azure 응용 프로그램 게이트웨이는 계층 7 부하 분산 장치입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다. 응용 프로그램은 HTTP 부하 분산, 쿠키 기반 세션 선호도, SSL(Secure Sockets Layer) 오프로드, 사용자 지정 상태 프로브, 다중 사이트 지원 및 기타를 포함하여 많은 ADC(Application Delivery Controller)를 제공합니다. 지원되는 기능의 전체 목록을 찾으려면 Application Gateway 개요를 방문하세요.

다음 문서에서는 [기존 Application Gateway에 웹 응용 프로그램 방화벽 추가](#add-web-application-firewall-to-an-existing-application-gateway) 및 [웹 응용 프로그램 방화벽을 사용하는 Application Gateway 만들기](#create-an-application-gateway-with-web-application-firewall)에 대해 보여줍니다.

![시나리오 이미지][scenario]

## <a name="waf-configuration-differences"></a>WAF 구성 차이

[PowerShell을 사용하여 Application Gateway 만들기](application-gateway-create-gateway-arm.md)를 읽어 보셨다면 Application Gateway를 만들 때 구성하는 SKU 설정에 대해 알고 계실 것입니다. WAF는 Application Gateway에 SKU를 구성할 때 정의하는 추가 설정을 제공합니다. Application Gateway 자체에서 추가로 변경해야 하는 사항은 없습니다.

**SKU** - WAF가 없는 일반 Application Gateway는 **Standard\_Small**, **Standard\_Medium** 및 **Standard\_Large** 크기를 지원합니다. WAF의 도입으로 두 개의 SKU, 즉 **WAF\_Medium** 및 **WAF\_Large** SKU가 추가되었습니다. 소형 Application Gateway에는 WAF가 지원되지 않습니다.

**계층** - 사용 가능한 값은 **표준** 또는 **WAF**입니다. 웹 응용 프로그램 방화벽을 사용하는 경우 **WAF**를 선택해야 합니다.

**모드** - 이 설정은 WAF 모드입니다. 허용되는 값은 **검색** 및 **방지**입니다. WAF를 검색 모드로 설정하면 모든 위협이 로그 파일에 저장됩니다. 방지 모드에서는 이벤트를 여전히 기록하지만 공격자가 Application Gateway로부터 403 권한 없음 응답을 받습니다.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>기존 Application Gateway에 웹 응용 프로그램 방화벽 추가

Azure PowerShell의 최신 버전을 사용하고 있는지 확인합니다. 자세한 내용은 [Resource Manager에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

### <a name="step-1"></a>1단계

Azure 계정에 로그인합니다.

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>2단계

이 시나리오에 사용할 구독을 선택합니다.

```powershell
Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>3단계

웹 응용 프로그램 방화벽을 추가하려는 게이트웨이를 검색합니다.

```powershell
$gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
```

### <a name="step-4"></a>4단계

웹 응용 프로그램 방화벽 sku를 구성합니다. 사용 가능한 크기는 **WAF\_Large** 및 **WAF\_Medium**입니다. 웹 응용 프로그램 방화벽을 사용하는 경우 계층은 **WAF**여야 하며, sku를 설정하는 경우 용량을 확인해야 합니다.

```powershell
$gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
```

### <a name="step-5"></a>5단계

다음 예제에 정의된 대로 WAF 설정을 구성합니다.

**WafMode** 설정의 경우 사용 가능한 값은 방지 및 검색입니다.

```powershell
$gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
```

### <a name="step-6"></a>6단계

이전 단계에서 정의한 설정을 사용하여 Application Gateway를 업데이트합니다.

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

이 명령은 웹 응용 프로그램 방화벽이 있는 Application Gateway를 업데이트합니다. [Application Gateway 진단](application-gateway-diagnostics.md)을 살펴보고 Application Gateway에 대한 로그를 보는 방법에 대해 알아볼 것을 권장합니다. WAF의 보안 특성상, 주기적으로 로그를 검토하여 웹 응용 프로그램의 보안 상태를 파악해야 합니다.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>웹 응용 프로그램 방화벽이 있는 Application Gateway 만들기

다음 단계에서는 웹 응용 프로그램 방화벽이 있는 Application Gateway를 만드는 전체 과정을 처음부터 끝까지 안내합니다.

Azure PowerShell의 최신 버전을 사용하고 있는지 확인합니다. 자세한 내용은 [Resource Manager에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

### <a name="step-1"></a>1단계:

Azure에 로그인

```powershell
Login-AzureRmAccount
```

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.

### <a name="step-2"></a>2단계:

계정에 대한 구독을 확인합니다.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>3단계:

사용할 Azure 구독을 선택합니다.

```powershell
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-4"></a>4단계:

리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

이전 예제에서는 "appgw-RG"라는 리소스 그룹과 "미국 서부"라는 위치를 만들었습니다.

> [!NOTE]
> 응용 프로그램 게이트웨이에 사용자 지정 프로브를 구성해야 하는 경우 [PowerShell을 사용하여 사용자 지정 프로브로 응용 프로그램 게이트웨이 만들기](application-gateway-create-probe-ps.md)를 참조하세요. 자세한 내용은 [사용자 지정 프로브 및 상태 모니터링](application-gateway-probe-overview.md)을 확인합니다.

### <a name="step-5"></a>5단계

Application Gateway 자체에 사용할 서브넷의 주소 범위를 할당합니다.

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> 응용 프로그램에 대한 서브넷의 마스크 비트가 적어도 28 이상이어야 합니다. 이 값은 Application Gateway 인스턴스의 서브넷에 10개 주소를 남겨 둡니다. 서브넷 마스크가 그보다 작으면 나중에 Application Gateway의 인스턴스를 추가할 수 없습니다.


### <a name="step-6"></a>6단계

백 엔드 주소 풀에 사용할 주소 범위를 할당합니다.

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-7"></a>7단계

[리소스 그룹 만들기](#create-the-resource-group)

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-8"></a>8단계

다음 단계에 사용할 가상 네트워크 리소스와 서브넷 리소스를 검색합니다.

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

### <a name="step-9"></a>9단계

Application Gateway에 사용할 공용 IP 리소스를 만듭니다. 이 공용 IP 주소는 다음 단계 중 하나에 사용됩니다.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway는 정의된 도메인 레이블로 만든 공용 IP 주소의 사용을 지원하지 않습니다. 도메인 레이블이 동적으로 생성된 공용 IP 주소만 지원됩니다. Application Gateway에 친숙한 dns 이름이 필요한 경우 cname 레코드를 별칭으로 사용하는 것이 좋습니다.


### <a name="step-10"></a>10단계

Application Gateway를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

Application Gateway IP 구성을 만듭니다. 이 설정은 Application Gateway에서 사용하는 서브넷을 구성합니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-11"></a>11단계

백 엔드 웹 서버의 IP 주소를 사용하여 백 엔드 IP 주소 풀을 구성합니다. 이러한 IP 주소는 프런트 엔드 IP 끝점에서 들어오는 네트워크 트래픽을 수신합니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 다음 IP 주소를 바꿉니다.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

### <a name="step-12"></a>12단계

ssl이 활성화된 백 엔드 풀 리소스에 사용할 인증서를 업로드합니다.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-13"></a>13단계

Application Gateway 백 엔드 http 설정을 구성합니다. 이전 단계에서 업로드한 인증서를 http 설정에 할당합니다.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-14"></a>14단계

공용 IP 끝점에 대한 프런트 엔드 IP 포트를 구성합니다. 이 포트는 최종 사용자가 연결하는 포트입니다.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-15"></a>15단계

프런트 엔드 IP 구성을 만듭니다. 이 설정은 개인 또는 공용 IP 주소를 Application Gateway의 프런트 엔드에 매핑합니다. 다음 단계는 이전 단계의 공용 IP 주소를 프런트 엔드 IP 구성과 연결합니다.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-16"></a>16단계

Application Gateway에 대한 인증서를 구성합니다. 이 인증서는 Application Gateway의 트래픽을 암호화하고 해독하는 데 사용됩니다.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-17"></a>17단계

Application Gateway에 대한 HTTP 수신기를 만듭니다. 사용할 프런트 엔드 IP 구성, 포트 및 ssl 인증서를 할당합니다.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

### <a name="step-18"></a>18단계

부하 분산 장치 동작을 구성하는 부하 분산 장치 라우팅 규칙을 만듭니다. 이 예제에서는 기본 라운드 로빈 규칙을 만듭니다.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-19"></a>19단계

응용 프로그램 게이트웨이의 인스턴스 크기를 구성합니다.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

> [!NOTE]
> **WAF\_Medium**과 **WAF\_Large** 중에 선택할 수 있으며, WAF를 사용할 때의 계층은 언제나 **WAF**입니다. 용량은 1부터 10 사이의 숫자입니다.

### <a name="step-20"></a>20단계

WAF의 모드를 구성합니다. 허용되는 값은 **방지** 및 **검색**입니다.

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="step-21"></a>21단계

이전 단계의 모든 구성 항목으로 응용 프로그램 게이트웨이를 만듭니다. 이 예제에서는 응용 프로그램 게이트웨이를 "appgwtest"라고 합니다.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> 기본 웹 응용 프로그램 방화벽 구성을 사용하여 만든 응용 프로그램 게이트웨이는 보호를 위해 CRS 3.0으로 구성됩니다.

## <a name="get-application-gateway-dns-name"></a>응용 프로그램 게이트웨이 DNS 이름 가져오기

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 Application Gateway는 식별 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 최종 사용자가 Application Gateway를 누를 수 있도록 하려면 CNAME 레코드를 사용하여 Application Gateway의 공용 끝점을 가리키도록 합니다. [Azure에서 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md). 이 작업을 수행하려면 Application Gateway에 연결된 PublicIPAddress 요소를 사용하여 Application Gateway 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름은 두 개의 웹 응용 프로그램을 이 DNS 이름으로 가리키는 CNAME 레코드를 만드는 데 사용됩니다. A 레코드를 사용할 경우 응용 프로그램 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 이는 권장되지 않습니다.

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

[Application Gateway 진단](application-gateway-diagnostics.md)을 방문하여 진단 로깅을 구성하는 방법 및 웹 응용 프로그램 방화벽을 통해 검색 또는 방지되는 이벤트를 기록하는 방법에 대해 알아보기

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

