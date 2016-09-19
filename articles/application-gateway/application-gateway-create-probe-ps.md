<properties
   pageTitle="리소스 관리자에서 PowerShell을 사용하여 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브 만들기 | Microsoft Azure"
   description="리소스 관리자에서 PowerShell을 사용하여 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브를 만드는 방법에 대해 알아봅니다."
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace" />

# Azure 리소스 관리자에 대해 PowerShell을 사용하여 Azure 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브 만들기

> [AZURE.SELECTOR]
- [Azure 포털](application-gateway-create-probe-portal.md)
- [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
- [Azure 클래식 PowerShell](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### 1단계

Login-AzureRmAccount를 사용하여 인증합니다.

	Login-AzureRmAccount

### 2단계

계정에 대한 구독을 확인합니다.

	Get-AzureRmSubscription

### 3단계

사용할 Azure 구독을 선택합니다. <BR>


	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 4단계

리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

위 예제에서는 "appgw-RG"라는 리소스 그룹과 "West US"라는 위치를 만들었습니다.

## 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 단계는 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷을 만듭니다.

### 1단계


주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### 2단계

접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역에 리소스 그룹 "appgw-rg"에서 "appgwvnet"이라는 가상 네트워크를 만듭니다.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 3단계

응용 프로그램 게이트웨이를 만드는 다음 단계에 서브넷 변수를 할당합니다.

	$subnet = $vnet.Subnets[0]

## 프런트 엔드 구성에 대한 공용 IP 주소 만들기


미국 서부 지역에 리소스 그룹 "appgw-rg"에서 공용 IP 리소스 "publicIP01"을 만듭니다.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## 사용자 지정 프로브를 사용하여 응용 프로그램 게이트웨이 구성 개체 만들기

Application Gateway를 만들기 전에 모든 구성 항목을 설정합니다. 다음 단계 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### 1단계

"gatewayIP01"이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### 2단계


IP 주소가 "134.170.185.46, 134.170.188.221,134.170.185.50"인 "pool01"이라는 백 엔드 IP 주소 풀을 구성합니다. 이러한 값은 프런트 엔드 IP 끝점에서 들어오는 네트워크 트래픽을 수신하는 IP 주소입니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 위의 IP 주소를 바꿉니다.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### 3단계


이 단계에서는 사용자 지정 프로브를 구성합니다.

사용되는 매개 변수:

- **Interval** - 프로브 간격 확인(초)을 구성합니다.
- **Timeout** - HTTP 응답 확인을 위한 프로브 시간 제한을 정의합니다.
- **-Hostname 및 경로** - Application Gateway가 인스턴스의 상태를 확인하기 위해 호출하는 전체 URL 경로입니다. 예: 웹 사이트가 http://contoso.com/인 경우 프로브를 확인하여 성공적으로 HTTP에 응답하도록 "http://contoso.com/path/custompath.htm"에 대해 사용자 지정 프로브를 구성할 수 있습니다.
- **UnhealthyThreshold** - 백 엔드 인스턴스를 *unhealthy*로 표시하는 데 필요한 실패한 HTTP 응답 수입니다.

<BR>

	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### 4단계

백 엔드 풀에서 트래픽에 대해 응용 프로그램 게이트웨이 설정 "poolsetting01"을 구성합니다. 또한 이 단계에서 응용 프로그램 게이트웨이 요청에 대한 백 엔드 풀 응답의 시간 제한을 구성합니다. 백 엔드 응답이 시간 제한에 도달하면 응용 프로그램 게이트웨이 요청을 취소합니다. 이 값은 프로브 확인에 대한 백 엔드 응답만을 위한 프로브 시간 제한과는 다릅니다.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### 5단계

공용 IP 끝점에 대해 "frontendport01"이라는 프런트 엔드 IP 포트를 구성합니다.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### 6단계

"fipconfig01"이라는 프런트 엔드 IP 구성을 만들고 프런트 엔드 IP 구성에 공용 IP 주소를 연결합니다.


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### 7단계

"listener01"이라는 수신기를 만들고 프런트 엔드 IP 구성에 프런트 엔드 포트를 연결합니다.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### 8단계

부하 분산 장치 동작을 구성하는 "rule01"이라는 부하 분산 장치 라우팅 규칙을 만듭니다.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### 9단계

응용 프로그램 게이트웨이의 인스턴스 크기를 구성합니다.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  *InstanceCount*의 기본값은 2이고, 최대값은 10입니다. *GatewaySize*의 기본값은 보통입니다. Standard\_Small, Standard\_Medium 및 Standard\_Large 간에 선택할 수 있습니다.

## New-AzureRmApplicationGateway를 사용하여 응용 프로그램 게이트웨이 만들기

위 단계의 모든 구성 항목으로 응용 프로그램 게이트웨이를 만듭니다. 이 예제에서는 응용 프로그램 게이트웨이를 "appgwtest"라고 합니다.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## 기존 응용 프로그램 게이트웨이에 프로브 추가

다음 네 단계에 따라 기존 응용 프로그램 게이트웨이에 사용자 지정 프로브를 추가합니다.

### 1단계

**Get-AzureRmApplicationGateway**를 사용하여 PowerShell 변수에 응용 프로그램 게이트웨이 리소스를 로드합니다.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 2단계

기존 게이트웨이 구성에 프로브를 추가합니다.

	$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


예제에서 사용자 지정 프로브는 URL 경로 contoso.com/path/custompath.htm에 대해 30초마다 확인하도록 구성되어 있습니다. 시간 제한 임계값 120초가 실패한 프로브 요청 최대 수 8과 함께 구성됩니다.

### 3단계

**-Set-AzureRmApplicationGatewayBackendHttpSettings**를 사용하여 백 엔드 풀 설정 구성 및 제한 시간에 프로브를 추가합니다.


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### 4단계

**Set-AzureRmApplicationGateway**를 사용하여 구성을 응용 프로그램 게이트웨이에 저장합니다.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## 기존 응용 프로그램 게이트웨이에서 프로브 제거

다음은 기존 응용 프로그램 게이트웨이에서 사용자 지정 프로브를 제거하는 단계입니다.

### 1단계

**Get-AzureRmApplicationGateway**를 사용하여 PowerShell 변수에 응용 프로그램 게이트웨이 리소스를 로드합니다.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### 2단계

**Remove-AzureRmApplicationGatewayProbeConfig**를 사용하여 응용 프로그램 게이트웨이에서 프로브 구성을 제거합니다.

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### 3단계

**-Set-AzureRmApplicationGatewayBackendHttpSettings**를 사용하여 프로브 및 제한 시간 설정을 제거하기 위해 백 엔드 풀 설정 구성을 업데이트합니다.


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### 4단계

**Set-AzureRmApplicationGateway**를 사용하여 구성을 응용 프로그램 게이트웨이에 저장합니다.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## 다음 단계

[SSL 오프로드 구성](application-gateway-ssl-arm.md)을 방문하여 SSL 오프로드를 구성하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0907_2016-->