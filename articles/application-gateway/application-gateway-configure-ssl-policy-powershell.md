---
title: PowerShell을 사용하여 TLS 정책 구성
titleSuffix: Azure Application Gateway
description: 이 문서에서는 Azure 응용 프로그램 게이트웨이에서 TLS 정책을 구성하는 방법에 대한 지침을 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 3804059fdd818f10663d14bde72da2c6773fa53f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312685"
---
# <a name="configure-tls-policy-versions-and-cipher-suites-on-application-gateway"></a>응용 프로그램 게이트웨이에서 TLS 정책 버전 및 암호 제품군 구성

응용 프로그램 게이트웨이에서 TLS/SSL 정책 버전 및 암호 제품군을 구성하는 방법에 대해 알아봅니다. TLS 정책 버전 및 사용 가능한 암호 제품군의 다양한 구성을 포함하는 미리 정의된 정책 목록에서 선택할 수 있습니다. 또한 요구 사항에 따라 [사용자 지정 TLS 정책을](#configure-a-custom-tls-policy) 정의할 수도 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-available-tls-options"></a>사용 가능한 TLS 옵션 받기

`Get-AzApplicationGatewayAvailableSslOptions` cmdlet은 사용 가능한 미리 정의된 정책, 사용 가능한 암호 그룹 및 구성 가능한 프로토콜 버전의 목록을 제공합니다. 다음 예제는 cmdlet을 실행하는 작업의 예제 출력을 보여줍니다.

```
DefaultPolicy: AppGwSslPolicy20150501
PredefinedPolicies:
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20150501
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401S

AvailableCipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
    TLS_RSA_WITH_AES_128_GCM_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA256
    TLS_RSA_WITH_AES_128_CBC_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA
    TLS_RSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_3DES_EDE_CBC_SHA
    TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

AvailableProtocols:
    TLSv1_0
    TLSv1_1
    TLSv1_2
```

## <a name="list-pre-defined-tls-policies"></a>미리 정의된 TLS 정책 목록

Application Gateway는 사용할 수 있는 미리 정의된 3가지 정책을 함께 제공합니다. `Get-AzApplicationGatewaySslPredefinedPolicy` cmdlet은 이러한 정책을 검색합니다. 각 정책에는 다른 프로토콜 버전 및 암호 그룹을 사용 하도록 설정했습니다. 이러한 미리 정의된 정책을 사용하여 응용 프로그램 게이트웨이에서 TLS 정책을 신속하게 구성할 수 있습니다. 기본적으로 **AppGwSlPolicy20150501은** 특정 TLS 정책이 정의되지 않은 경우 선택됩니다.

다음 출력은 `Get-AzApplicationGatewaySslPredefinedPolicy`을 실행하는 예제입니다.

```
Name: AppGwSslPolicy20150501
MinProtocolVersion: TLSv1_0
CipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
 ...
Name: AppGwSslPolicy20170401
MinProtocolVersion: TLSv1_1
CipherSuites:
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
...
```

## <a name="configure-a-custom-tls-policy"></a>사용자 지정 TLS 정책 구성

사용자 지정 TLS 정책을 구성할 때 정책 유형, MinProtocolVersion, CipherSuite 및 응용 프로그램 게이트웨이라는 매개 변수를 전달합니다. 다른 매개 변수를 전달하려고 하면 Application Gateway를 만들거나 업데이트할 때 오류가 발생합니다. 

다음 예제는 응용 프로그램 게이트웨이에서 사용자 지정 TLS 정책을 설정합니다. 최소 프로토콜 버전을 `TLSv1_1`로 설정하고 다음 암호 그룹을 사용하도록 설정합니다.

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

> [!IMPORTANT]
> 사용자 지정 TLS 정책을 구성할 때 TLS_RSA_WITH_AES_256_CBC_SHA256 선택해야 합니다. 응용 프로그램 게이트웨이는 백 엔드 관리를 위해 이 암호 제품군을 사용합니다. 다른 스위트와 함께 사용할 수 있지만 이 스위트도 선택해야 합니다. 

```powershell
# get an application gateway resource
$gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroup AdatumAppGatewayRG

# set the TLS policy on the application gateway
Set-AzApplicationGatewaySslPolicy -ApplicationGateway $gw -PolicyType Custom -MinProtocolVersion TLSv1_1 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"

# validate the TLS policy locally
Get-AzApplicationGatewaySslPolicy -ApplicationGateway $gw

# update the gateway with validated TLS policy
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="create-an-application-gateway-with-a-pre-defined-tls-policy"></a>미리 정의된 TLS 정책을 사용하여 응용 프로그램 게이트웨이 만들기

미리 정의된 TLS 정책을 구성할 때 정책 유형, 정책 이름 및 응용 프로그램 게이트웨이와 같은 매개 변수를 전달합니다. 다른 매개 변수를 전달하려고 하면 Application Gateway를 만들거나 업데이트할 때 오류가 발생합니다.

다음 예제는 미리 정의된 TLS 정책을 사용 하 고 새 응용 프로그램 게이트웨이를 만듭니다.

```powershell
# Create a resource group
$rg = New-AzResourceGroup -Name ContosoRG -Location "East US"

# Create a subnet for the application gateway
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network with a 10.0.0.0/16 address space
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for later use
$subnet = $vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location "East US" -AllocationMethod Dynamic

# Create an ip configuration object
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Create a backend pool for backend web servers
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

# Define the backend http settings to be used.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

# Create a new port for TLS
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 443

# Upload an existing pfx certificate for TLS offload
$password = ConvertTo-SecureString -String "P@ssw0rd" -AsPlainText -Force
$cert = New-AzApplicationGatewaySslCertificate -Name cert01 -CertificateFile C:\folder\contoso.pfx -Password $password

# Create a frontend IP configuration for the public IP address
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Create a new listener with the certificate, port, and frontend ip.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a new rule for backend traffic routing
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Define the size of the application gateway
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Configure the TLS policy to use a different pre-defined policy
$policy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Create the application gateway.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName $rg.ResourceGroupName -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

## <a name="update-an-existing-application-gateway-with-a-pre-defined-tls-policy"></a>미리 정의된 TLS 정책으로 기존 애플리케이션 게이트웨이 업데이트

사용자 지정 TLS 정책을 설정하려면 정책 **유형,** **MinProtocolVersion,** **CipherSuite**및 **응용 프로그램 게이트웨이**의 매개 변수를 전달합니다. 미리 정의된 TLS 정책을 설정하려면 **policyType,** **PolicyName**및 **ApplicationGateway**의 다음 매개 변수를 전달합니다. 다른 매개 변수를 전달하려고 하면 Application Gateway를 만들거나 업데이트할 때 오류가 발생합니다.

다음 예제에는 사용자 지정 정책과 미리 정의된 정책 모두에 대한 코드 샘플이 나옵니다. 사용하려는 정책의 주석 처리를 제거하세요.

```powershell
# You have to change these parameters to match your environment.
$AppGWname = "YourAppGwName"
$RG = "YourResourceGroupName"

$AppGw = get-Azapplicationgateway -Name $AppGWname -ResourceGroupName $RG

# Choose either custom policy or predefined policy and uncomment the one you want to use.

# TLS Custom Policy
# Set-AzApplicationGatewaySslPolicy -PolicyType Custom -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_RSA_WITH_AES_128_CBC_SHA256" -ApplicationGateway $AppGw

# TLS Predefined Policy
# Set-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S" -ApplicationGateway $AppGW

# Update AppGW
# The TLS policy options are not validated or updated on the Application Gateway until this cmdlet is executed.
$SetGW = Set-AzApplicationGateway -ApplicationGateway $AppGW
```

## <a name="next-steps"></a>다음 단계

[Application Gateway 리디렉션 개요](application-gateway-redirect-overview.md)를 방문하여 HTTPS 엔드포인트에 HTTP 트래픽을 리디렉션하는 방법에 대해 알아봅니다.
