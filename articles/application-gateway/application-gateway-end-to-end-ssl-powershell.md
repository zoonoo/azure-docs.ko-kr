<properties
    pageTitle="Application Gateway에서 SSL 정책 및 종단 간 SSL 구성 | Microsoft Azure"
    description="이 문서에서는 Azure Resource Manager PowerShell을 사용하여 Application Gateway로 종단 간 SSL을 구성하는 방법에 대해 설명합니다."
    services="application-gateway"
    documentationCenter="na"
    authors="georgewallace"
    manager="carmonmills"
    editor="tysonn"/>

<tags
    ms.service="application-gateway"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="gwallace"/>

# PowerShell을 사용하여 Application Gateway에서 SSL 정책 및 종단 간 SSL 구성

## 개요

Application Gateway는 트래픽의 종단 간 암호화를 지원합니다. Application Gateway는 이를 위해 응용 프로그램 게이트웨이에서 SSL 연결을 종료합니다. 그러면 게이트웨이에서 트래픽에 라우팅 규칙을 적용하고, 패킷을 다시 암호화하고, 정의된 라우팅 규칙에 따라 적절한 백 엔드에 패킷을 전달합니다. 웹 서버의 모든 응답은 동일한 프로세스를 거쳐 최종 사용자에게 돌아갑니다.

Application Gateway가 지원하는 또 다른 기능은 특정 SSL 프로토콜 버전 비활성화입니다. Application Gateway는 프로토콜 버전 TLSv1.0, TLSv1.1 및 TLSv1.2 비활성화를 지원합니다.

![시나리오 이미지][scenario]

## 시나리오

이 시나리오에서는 PowerShell을 사용하여 종단 간 SSL을 사용하는 Application Gateway를 만드는 방법을 알아봅니다.

이 시나리오에서는 다음을 수행합니다.

- "appgw-rg"라는 리소스 그룹 만들기
- 예약된 CIDR 블록이 10.0.0.0/16이고 이름이 "appgwvnet"인 가상 네트워크를 만듭니다.
- "appgwsubnet" 및 "appsubnet"라는 두 개의 서브넷을 만듭니다.
- 특정 SSL 프로토콜을 비활성화하는 종단 간 SSL 암호화를 지원하는 소형 Application Gateway를 만듭니다.

## 시작하기 전에

Application Gateway를 사용하여 종단 간 SSL을 구성하려면 게이트웨이에 사용할 인증서와 백 엔드 서버에 사용할 인증서가 필요합니다. 게이트웨이 인증서는 SSL을 통해 전송되는 트래픽을 암호화하고 해독하는 데 사용됩니다. 게이트웨이 인증서는 개인 정보 교환(pfx) 형식이어야 합니다. 이 파일 형식을 사용하면 응용 프로그램 게이트웨이에서 트래픽의 암호화 및 암호 해독을 수행하는 데 필요한 개인 키를 내보낼 수 있습니다.

종단 간 ssl 암호화의 경우 백 엔드가 Application Gateway를 통해 허용 목록에 추가되어야 합니다. 이 작업은 백 엔드의 공개 인증서를 Application Gateway에 업로드하여 수행됩니다. 이렇게 하면 Application Gateway가 알려진 백 엔드 인스턴스하고만 통신하므로 종단 간 통신의 보안이 유지됩니다. 이 프로세스는 다음 단계에 설명되어 있습니다.

## 리소스 그룹 만들기

이 섹션에서는 Application Gateway를 포함하는 리소스 그룹을 만드는 과정을 안내합니다.

### 1단계

Azure 계정에 로그인합니다.

    Login-AzureRmAccount

### 2단계

이 시나리오에 사용할 구독을 선택합니다.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### 3단계

리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 가상 네트워크 및 두 개의 서브넷을 만듭니다. 하나의 서브넷은 Application Gateway를 보관하는 데 사용되고 다른 서브넷은 웹 응용 프로그램을 호스트하는 백 엔드 서버에 사용됩니다.

### 1단계

Application Gateway 자체에 사용할 서브넷의 주소 범위를 할당합니다.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

### 2단계

백 엔드 주소 풀에 사용할 주소 범위를 할당합니다.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### 3단계

이전 단계에서 정의한 서브넷을 사용하여 가상 네트워크를 만듭니다.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### 4단계

다음 단계에 사용할 가상 네트워크 리소스와 서브넷 리소스를 검색합니다.

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## 프런트 엔드 구성에 대한 공용 IP 주소 만들기

Application Gateway에 사용할 공용 IP 리소스를 만듭니다. 이 공용 IP 주소는 다음 단계에 사용됩니다.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Application Gateway는 정의된 도메인 레이블로 만든 공용 IP 주소의 사용을 지원하지 않습니다. 도메인 레이블이 동적으로 생성된 공용 IP 주소만 지원됩니다. Application Gateway에 친숙한 dns 이름이 필요한 경우 cname 레코드를 별칭으로 사용하는 것이 좋습니다.

## 응용 프로그램 게이트웨이 구성 개체 만들기

Application Gateway를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### 1단계

Application Gateway IP 구성을 만듭니다. 이 설정은 Application Gateway에서 사용하는 서브넷을 구성합니다. Application Gateway는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### 2단계

프런트 엔드 IP 구성을 만듭니다. 이 설정은 개인 또는 공용 IP 주소를 Application Gateway의 프런트 엔드에 매핑합니다. 다음 단계는 이전 단계의 공용 IP 주소를 프런트 엔드 IP 구성과 연결합니다.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### 3단계

백 엔드 웹 서버의 IP 주소를 사용하여 백 엔드 IP 주소 풀을 구성합니다. 이러한 IP 주소는 프런트 엔드 IP 끝점에서 들어오는 네트워크 트래픽을 수신합니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 다음 IP 주소를 바꿉니다.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] 정규화된 도메인 이름(FQDN) 역시 백 엔드 서버의 IP 주소를 대체할 수 있는 유효한 값입니다.

### 4단계

공용 IP 끝점에 대한 프런트 엔드 IP 포트를 구성합니다. 이 포트는 최종 사용자가 연결하는 포트입니다.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### 5단계

Application Gateway에 대한 인증서를 구성합니다. 이 인증서는 Application Gateway의 트래픽을 암호화하고 해독하는 데 사용됩니다.

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] 이 샘플은 SSL 연결에 사용된 인증서를 구성합니다. 인증서는 .pfx 형식으로 4~12자 사이의 암호이어야 합니다.

### 6단계

Application Gateway에 대한 HTTP 수신기를 만듭니다. 사용할 프런트 엔드 IP 구성, 포트 및 ssl 인증서를 할당합니다.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### 7단계

ssl이 활성화된 백 엔드 풀 리소스에 사용할 인증서를 업로드합니다.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] 이 단계에서 제공하는 인증서는 백 엔드에 있는 pfx 인증서의 공개 키이어야 합니다. 이 단계에서는 Application Gateway를 통해 백 엔드를 허용 목록에 추가합니다.

### 8단계

Application Gateway 백 엔드 http 설정을 구성합니다. 이전 단계에서 업로드한 인증서를 http 설정에 할당합니다.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### 9단계

부하 분산 장치 동작을 구성하는 부하 분산 장치 라우팅 규칙을 만듭니다. 이 예제에서는 기본 라운드 로빈 규칙을 만듭니다.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### 10단계

응용 프로그램 게이트웨이의 인스턴스 크기를 구성합니다. 사용 가능한 크기는 **Standard\_Small**, **Standard\_Medium** 및 **Standard\_Large**입니다. 용량의 경우 사용 가능한 값은 1부터 10까지입니다.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] 테스트 목적으로 인스턴스 수 1을 선택할 수 있습니다. 그러나 두 개 미만의 인스턴스 수는 SLA에서 다루지 않으므로 권장되지 않는다는 점을 알아야 합니다. 작은 게이트웨이는 개발 테스트용이며 프로덕션용으로는 사용되지 않습니다.

### 11단계

Application Gateway에서 사용할 SSL 정책을 구성합니다. Application Gateway는 특정 SSL 프로토콜 버전을 비활성화하는 기능을 지원합니다.

다음 값은 비활성화할 수 있는 프로토콜 버전 목록입니다.

- **TLSv1\_0**
- **TLSv1\_1**
- **TLSv1\_2**

다음 예제에서는 TLSv1\_0 및 TLSv1\_1을 비활성화합니다.

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1

## 응용 프로그램 게이트웨이 만들기

위의 모든 단계를 사용하여 Application Gateway를 만듭니다. 게이트웨이 만들기는 긴 프로세스입니다.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## 기존 Application Gateway의 SSL 프로토콜 버전 비활성화

이전 단계에서는 종단 간 ssl을 사용하여 응용 프로그램을 만들고 특정 SSL 프로토콜 버전을 비활성화했습니다. 다음 예제에서는 기존 Application Gateway의 특정 SSL 정책을 비활성화합니다.

### 1단계

업데이트할 Application Gateway를 검색합니다.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### 2단계

SSL 정책을 정의합니다. 다음 예제에서는 TLSv1.0이 비활성화됩니다.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0 -ApplicationGateway $gw

### 3단계

마지막으로 게이트웨이를 업데이트합니다. 이 마지막 단계는 오래 걸리는 작업입니다. 작업이 완료되면 Application Gateway에 종단 간 ssl이 구성됩니다.

    $gw | Set-AzureRmApplicationGateway

## 다음 단계

[웹 응용 프로그램 방화벽 개요](application-gateway-webapplicationfirewall-overview.md)를 방문하여 Application Gateway를 통해 웹 응용 프로그램 방화벽으로 웹 응용 프로그램의 보안을 강화하는 방법에 대해 자세히 알아보기

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png

<!---HONumber=AcomDC_0928_2016-->