---
title: "가상 네트워크에서 Application Gateway와 함께 Azure API Management를 사용하는 방법 | Microsoft Docs"
description: "내부 가상 네트워크에서 프론트 엔드로 Application Gateway(WAF)와 함께 Azure API Management를 설정하고 구성하는 방법 알아보기"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2017
ms.author: sasolank
translationtype: Human Translation
ms.sourcegitcommit: a87349518f7494dda49e52ed160509a4ffeb7775
ms.openlocfilehash: a58ec5f6d62d2b48d6cf85d997b2deac95310505


---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>내부 VNET에서 Application Gateway와 API Management 통합 

##<a name="overview"> </a> 개요
 
가상 네트워크 내에서만 액세스할 수 있도록 내부 모드의 가상 네트워크에서 API Management 서비스를 구성할 수 있습니다. Azure Application Gateway는 계층&7; 부하 분산 장치를 제공하는 PAAS 서비스입니다. 역방향 프록시 서비스 역할을 하고 제품에 WAF(웹 응용 프로그램 방화벽)를 제공합니다.

내부 VNET에서 프로비전된 API Management와 Application Gateway 프런트 엔드를 결합하면 다음 시나리오가 가능합니다.

* 내부 소비자 및 외부 소비자의 소비에 대해 동일한 API Management 리소스를 사용합니다.
* 단일 API Management 리소스를 사용하며 외부 소비자가 사용할 수 있는 API Management에서 정의된 API의 하위 집합을 갖습니다.
* 공용 인터넷에서 API Management에 대한 액세스를 켜기 및 끄기로 전환하는 턴키 방법을 제공합니다. 

##<a name="scenario"> </a> 시나리오
이 게시물에서는 내부 및 외부 소비자에 대해 단일 API Management 서비스를 사용하고 온-프레미스 및 클라우드 API에 대해 단일 프런트 엔드 역할을 하도록 만드는 방법을 다룹니다. Application Gateway에서 사용 가능한 PathBasedRouting 기능을 사용하여 외부 소비에 대해 해당 API(녹색으로 강조 표시됨)의 하위 집합만을 노출하도록 선택할 수 있습니다.

이 설치에서 모든 API는 가상 네트워크 내에서만 관리됩니다. 내부 소비자(주황색으로 강조 표시됨)는 모든 내부 및 외부 API에 액세스할 수 있습니다. 내부 소비자는 Express 경로 회로를 통해 제공되는 고속으로 인터넷으로 나가지 않는 트래픽을 활용합니다.

![url 경로](./media/api-management-using-with-vnet/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> 시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하는 Azure PowerShell cmdlet의 최신 버전을 설치합니다. **다운로드 페이지** 의 [Windows PowerShell](https://azure.microsoft.com/downloads/)섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. 가상 네트워크를 만들고 API Management 및 Application Gateway에 대한 별도 서브넷을 만듭니다. 
3. 가상 네트워크에 대한 사용자 지정 DNS 서버를 만들려는 경우 배포를 시작하기 전에 만들고 가상 네트워크의 새 서브넷에서 만든 가상 컴퓨터가 모든 Azure 서비스 끝점을 확인할 수 있도록 합니다.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>API 관리 및 응용 프로그램 게이트웨이 간에 통합을 만드는 데 무엇이 필요합니까?

* **백 엔드 서버 풀:** API Management 서비스의 내부 가상 IP 주소입니다.
* **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
* **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백 엔드 서버 중의 하나로 리디렉트됩니다.
* **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 값은 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
* **규칙:** 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽이 전달되어야 하는 백 엔드 서버 풀을 정의합니다.
* **사용자 정의 상태 프로브:** 기본적으로 Application Gateway는 IP 주소 기반 프로브를 사용하여 BackendAddressPool의 어떤 서버가 활성 상태인지 파악합니다. API Management 서비스는 올바른 호스트 헤더가 있는 요청에만 응답하므로 기본 프로브는 실패합니다. 서비스는 활성 상태이고 요청을 전달해야 한다는 것을 응용 프로그램 게이트웨이에 알리도록 사용자 지정 상태 프로브를 정의해야 합니다.
* **사용자 지정 도메인 인증서:** 인터넷에서 API Management에 액세스하려면 Application Gateway 프런트 엔드 DNS 이름에 대한 호스트 이름의 CNAME 매핑을 수행해야 합니다. Application Gateway를 통해 요청이 오는 경우 API Management에서 이를 유효한 것으로 인식하고 응답하도록 동일한 호스트 이름 헤더와 인증서가 API Management에 적용됩니다.
## <a name="overview-steps"> </a> API Management 및 Application Gateway 통합에 필요한 단계 

1. 리소스 관리자에 대한 리소스 그룹을 만듭니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크, 서브넷 및 공용 IP를 만듭니다. API 관리에 대한 다른 서브넷을 만듭니다.
3. 위에서 만든 VNET 서브넷 내의 내부 VNET 모드에서 API 관리 서비스를 만듭니다.
4. 사용자 지정 도메인 이름으로 API 관리 서비스를 업데이트합니다.
5. 응용 프로그램 게이트웨이 구성 개체를 만듭니다.
6. 응용 프로그램 게이트웨이 리소스를 만듭니다.
7. API 관리 프록시 호스트 이름을 응용 프로그램 게이트웨이 리소스의 공용 DNS 이름으로 Cname합니다.

## <a name="create-a-resource-group-for-resource-manager"></a>리소스 관리자에 대한 리소스 그룹 만들기

Azure PowerShell의 최신 버전을 사용하고 있는지 확인합니다. 자세한 내용은 [Resource Manager에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

### <a name="step-1"></a>1단계:

Azure에 로그인

```powershell
Login-AzureRmAccount
```

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.<BR>

### <a name="step-2"></a>2단계

계정에 대한 구독을 확인하고 선택합니다.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>3단계

리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

```powershell
New-AzureRmResourceGroup -Name apim-appGw-RG -Location "West US"
```
Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 리소스 관리자를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다.

### <a name="step-1"></a>1단계

주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 동안 응용 프로그램 게이트웨이에 사용할 서브넷 변수에 할당합니다.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgateway01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>2단계

가상 네트워크를 만드는 동안 주소 범위 10.0.1.0/24를 API 관리에 사용할 서브넷 변수에 할당합니다.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name apim01 -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>3단계

접두사 10.0.0.0/16과 서브넷 10.0.0.0/24 및 10.0.1.0/24를 사용하여 미국 서부 지역에 리소스 그룹 **apim-appGw-RG**에서 **appgwvnet**이라는 가상 네트워크를 만듭니다.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName apim-appGw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>4단계

다음 단계에 대한 서브넷 변수 할당

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-in-internal-vnet-mode"></a>내부 VNET 모드에서 API Management 서비스 만들기

다음 예제에서는 내부 VNET에서 API Management 서비스를 만드는 방법을 보여 줍니다.

### <a name="step-1"></a>1단계
위에서 만든 $apimsubnetdata 서브넷을 사용하여 API 관리 가상 네트워크 개체를 만듭니다.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>2단계
가상 네트워크 내부에 API 관리 서비스 만들기

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization Contoso -AdminEmail admin@contoso.com -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Premium"
```
위의 명령이 성공한 후 액세스하려면 [내부 VNET API Management 서비스에 액세스하는 데 필요한 DNS 구성][api-management-using-with-internal-vnet.md#apim-dns-configuration]을 참조하세요.

## <a name="update-api-management-service-with-custom-domain-name"></a>사용자 지정 도메인 이름으로 API Management 서비스 업데이트

이제 인터넷에서 액세스하려는 API Management 서비스 끝점 프록시에 사용자 지정 도메인 이름을 적용합니다.

### <a name="step-1"></a>1단계
사용자의 권한을 보여 주는 개인 키로 인증서를 업로드하여 도메인에 사용자 지정 도메인을 설치합니다. `*.contoso.net`이라고 가정합니다. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file>
```

### <a name="step-2"></a>2단계
인증서를 업로드하면 인증서는 `*.contoso.net` 도메인에 권한을 표시하므로 호스트 이름 `api.contoso.net`으로 프록시에 대한 호스트 이름 구성 개체를 만들 수 있습니다. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" –PortalHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>프런트 엔드 구성에 대한 공용 IP 주소 만들기

미국 서부 지역에 리소스 그룹 **apim-appGw-RG**에서 공용 IP 리소스 **publicIP01**을 만듭니다.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

서비스를 시작할 때 응용 프로그램 게이트웨이에 IP 주소가 할당됩니다.

## <a name="create-application-gateway-configuration"></a>응용 프로그램 게이트웨이 구성 만들기

응용 프로그램 게이트웨이를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### <a name="step-1"></a>1단계:

**gatewayIP01**이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>2단계

공용 IP 끝점에 대한 프런트 엔드 IP 포트를 구성합니다. 이 포트는 최종 사용자가 연결하는 포트입니다.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```
### <a name="step-3"></a>3단계

공용 IP 끝점으로 프런트 엔드 IP를 구성합니다.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4단계

Application Gateway에 대한 인증서를 구성합니다. 이 인증서는 Application Gateway의 트래픽을 암호화하고 해독하는 데 사용됩니다.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>5단계

Application Gateway에 대한 HTTP 수신기를 만듭니다. 사용할 프런트 엔드 IP 구성, 포트 및 ssl 인증서를 할당합니다.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>6단계

여기서 API Management 서비스 `ContosoApi` 프록시 도메인 끝점에 사용자 지정 프로브를 만듭니다. 경로 `/status-0123456789abcdef`는 모든 API Management 서비스에서 호스트되는 기본 상태 끝점입니다. 호스트 이름 `contosoapi.azure-api.net`은 서비스 `contosoapi`가 공용 Azure에서 생성될 때 구성된 기본 프록시 호스트 이름입니다.

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name apimproxyprobe -Protocol Https -HostName "contosoapi.azure-api.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7단계

ssl이 활성화된 백 엔드 풀 리소스에 사용할 인증서를 업로드합니다.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>8단계

백 엔드 풀에서 트래픽에 대해 응용 프로그램 게이트웨이 설정 **apimPoolSetting**을 구성합니다. 또한 이 단계에서 응용 프로그램 게이트웨이 요청에 대한 백 엔드 풀 응답의 시간 제한을 구성합니다. 백 엔드 응답이 시간 제한에 도달하면 응용 프로그램 게이트웨이 요청을 취소합니다. 이 값은 프로브 확인에 대한 백 엔드 응답만을 위한 프로브 시간 제한과는 다릅니다.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name apimPoolSetting -Port 443 -Protocol Https -CookieBasedAffinity Disabled -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>9단계

위에서 만든 API Management 서비스에 대한 내부 가상 IP 주소로 **apimbackend**라는 백 엔드 IP 주소 풀을 구성합니다.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name apimbackend -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>10단계
백 엔드 풀에 대한 URL 규칙 경로를 구성합니다. `Echo API (/echo/), Calculator API (/calc/) etc.`과 같이 API Management에서 구성된 여러 API를 가질 수 있으며 인터넷에서 `Echo API`에 대한 액세스만을 허용하도록 선택할 수 있습니다. 

다음 예제에서는 백 엔드 "apimProxyBackendPool"에 대한 트래픽을 라우팅하는 "/echo/" 경로에 간단한 규칙을 만듭니다.

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting

$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule -DefaultBackendAddressPool $apimProxyBackendPool -DefaultBackendHttpSettings $apimPoolSetting
```

위의 단계를 수행하면 경로 "/echo"에 대한 요청만이 응용 프로그램 게이트웨이를 통해서 허용됩니다. API Management에서 구성된 다른 API에 대한 요청은 인터넷에서 액세스될 때 Application Gateway에서 404를 throw합니다. 

### <a name="step-11"></a>11단계

규칙 설정을 만듭니다. 이 단계에서는 URL 경로 기반 라우팅을 사용하도록 응용 프로그램 게이트웨이를 구성합니다.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-12"></a>12단계

응용 프로그램 게이트웨이에 대한 크기 및 인스턴스 수를 구성합니다. 여기에서 API Management 리소스의 향상된 보안에 [WAF Sku][../application-gateway/application-gateway-webapplicationfirewall-overview.md]를 사용하고 있습니다.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

### <a name="step-13"></a>13단계

WAF에 대한 모드를 "방지" 모드가 되도록 구성합니다.
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>응용 프로그램 게이트웨이 만들기

이전 단계의 모든 구성 개체로 응용 프로그램 게이트웨이를 만듭니다.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName apim-appGw-RG -Location "West US" -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-public-dns-name-of-application-gateway-resource"></a>API 관리 프록시 호스트 이름을 응용 프로그램 게이트웨이 리소스의 공용 DNS 이름으로 Cname

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 Application Gateway는 식별 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 프런트 엔드 IP CNAME 기록을 구성하려면 Application Gateway에 연결된 PublicIPAddress 요소를 사용하여 Application Gateway 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름은 위의 `api.contoso.net`에서 구성한 프록시 호스트 이름을 이 DNS 이름으로 가리키는 CNAME 레코드를 만드는 데 사용되어야 합니다. A 레코드를 사용할 경우 응용 프로그램 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 이는 권장되지 않습니다.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -Name publicIP01
```

##<a name="summary"> </a> 요약
VNET에서 Azure API Management 서비스는 온-프레미스 또는 클라우드에서 호스트되었는지 여부에 상관 없이 모든 API에 대한 액세스를 관리하는 단일 게이트웨이 인터페이스를 제공합니다. API 사용자 및 사용되고 있는 방법에 대한 깊은 통찰력을 얻습니다. Application Gateway와 API Management의 통합을 통해 인터넷에서 액세스를 부여하려는 API에 유연성을 향상시키고 API Management 인스턴스에 대한 프런트 엔드로 웹 응용 프로그램 방화벽을 제공합니다.

##<a name="next-steps"> </a> 다음 단계
* Azure Application Gateway에 대한 자세한 정보
  * [응용 프로그램 게이트웨이 개요](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway 웹 응용 프로그램 방화벽](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
* VNET의 API Management에 대한 자세한 정보
  * [VNET에서 API Management 사용](api-management-using-with-vnet.md)





<!--HONumber=Feb17_HO1-->


