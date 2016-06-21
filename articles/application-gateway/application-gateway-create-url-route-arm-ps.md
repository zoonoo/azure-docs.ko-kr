<properties
   pageTitle="URL 라우팅 규칙을 사용하여 응용 프로그램 게이트웨이 만들기 | Microsoft Azure"
   description="이 페이지에서는 URL 라우팅 규칙을 사용하여 Azure 응용 프로그램 게이트웨이를 만들고, 구성하기 위한 지침을 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="joaoma"/>


# URL 기반 라우팅을 사용하여 응용 프로그램 게이트웨이 만들기 

URL 경로 기반 라우팅을 사용하여 Http 요청의 URL 경로에 따라 경로를 연결할 수 있습니다. 응용 프로그램 게이트웨이의 URL 목록에 대해 구성된 백 엔드 풀에 대한 경로가 있는지 확인하고 정의된 백 엔드 풀로 네트워크 트래픽을 전송합니다. URL 기반 라우팅의 일반적인 용도는 여러 콘텐츠 형식에 대한 요청의 부하를 여러 백 엔드 서버 풀에 분산하는 것입니다.

URL 기반 라우팅에서는 응용 프로그램 게이트웨이에 새로운 규칙 형식을 제공합니다. 응용 프로그램 게이트웨이에는 두 가지 규칙 형식(기본 및 PathBasedRouting)이 있습니다. 기본 규칙 형식은 라운드 로빈 배포 외에도 PathBasedRouting 동안 백 엔드 풀에 대한 라운드 로빈 서비스를 제공하며 백 엔드 풀을 선택한 상태에서는 요청 URL의 경로 패턴도 적용합니다.

>[AZURE.IMPORTANT] PathPattern: 일치하는 경로 패턴의 목록입니다. 각각은 /로 시작해야 하고 *는 끝에만 올 수 있습니다. 사용 가능한 예는 /xyz, /xyz* 또는 /xyz/*입니다. 경로 검사기에 제공하는 문자열은 ? 또는 #으로 시작하는 텍스트는 포함하지 않습니다. 이러한 문자는 허용되지 않습니다.

## 시나리오
다음 예제에서 응용 프로그램 게이트웨이는 두 개의 백 엔드 서버 풀(video 서버 풀 및 image 서버 풀)과 함께 contoso.com에 대한 트래픽을 제공합니다.

http://contoso.com/image*에 대한 요청은 image 서버 풀(pool1)로 라우트되고 http://contoso.com/video*에 대한 요청은 video 서버 풀(pool2)로 라우트됩니다. 경로 패턴과 일치하는 항목이 없는 경우 기본 서버 풀(pool1)이 선택됩니다.

![url 경로](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## 시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하는 Azure PowerShell cmdlet의 최신 버전을 설치합니다. [다운로드 페이지](https://azure.microsoft.com/downloads/)의 **Windows PowerShell** 섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷을 만듭니다. 서브넷을 사용 중인 가상 컴퓨터 또는 클라우드 배포가 없는지 확인합니다. 응용 프로그램 게이트웨이는 가상 네트워크 서브넷에서 단독이어야 합니다.
3. 응용 프로그램 게이트웨이를 사용하도록 백 엔드 풀에 추가된 서버가 존재하거나 가상 네트워크 또는 공용 IP/VIP가 할당된 해당 끝점이 만들어져야 합니다.



## 응용 프로그램 게이트웨이를 만드는 데 필요한 것은 무엇입니까?


- **백 엔드 서버 풀:** 백 엔드 서버의 IP 주소 목록입니다. 나열된 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP/VIP이어야 합니다.
- **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
- **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백 엔드 서버 중의 하나로 리디렉트됩니다.
- **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 경우 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
- **규칙:** 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽이 이동되는 백 엔드 서버 풀을 정의합니다.

## 새 응용 프로그램 게이트웨이 만들기

Azure Classic 및 Azure 리소스 관리자 간의 차이점은 응용 프로그램 게이트웨이를 만드는 순서와 구성할 항목입니다.

리소스 관리자를 사용하면 응용 프로그램 게이트웨이를 만드는 모든 항목이 개별적으로 구성된 후 응용 프로그램 게이트웨이 리소스를 만드는 데 사용됩니다.


다음은 응용 프로그램 게이트웨이를 만드는 데 필요한 단계입니다.

1. 리소스 관리자에 대한 리소스 그룹을 만듭니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크, 서브넷 및 공용 IP를 만듭니다.
3. 응용 프로그램 게이트웨이 구성 개체를 만듭니다.
4. 응용 프로그램 게이트웨이 리소스를 만듭니다.

## 리소스 관리자에 대한 리소스 그룹 만들기

Azure PowerShell의 최신 버전을 사용하고 있는지 확인합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

### 1단계
Azure에 로그인 Login-AzureRmAccount

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.<BR>

### 2단계
계정에 대한 구독을 확인합니다.

		Get-AzureRmSubscription

### 3단계
사용할 Azure 구독을 선택합니다. <BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### 4단계
새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"
또는 응용 프로그램 게이트웨이의 리소스 그룹에 대한 태그를 만들 수도 있습니다.
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

위 예제에서는 "appgw-RG"라는 리소스 그룹과 "West US"라는 위치를 만들었습니다.

>[AZURE.NOTE] 응용 프로그램 게이트웨이에 사용자 지정 프로브를 구성해야 하는 경우 [PowerShell을 사용하여 사용자 지정 프로브로 응용 프로그램 게이트웨이 만들기](application-gateway-create-probe-ps.md)를 참조하세요. 자세한 내용은 [사용자 지정 프로브 및 상태 모니터링](application-gateway-probe-overview.md)을 확인합니다.

## 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 리소스 관리자를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다.

### 1단계
주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### 2단계
접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역에 리소스 그룹 "appgw-rg"에서 "appgwvnet"이라는 가상 네트워크를 만듭니다.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 3단계
응용 프로그램 게이트웨이를 만드는 다음 단계에 서브넷 변수를 할당합니다.

	$subnet=$vnet.Subnets[0]

## 프런트 엔드 구성에 대한 공용 IP 주소 만들기
미국 서부 지역에 리소스 그룹 "appgw-rg"에서 공용 IP 리소스 "publicIP01"을 만듭니다.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

서비스를 시작할 때 응용 프로그램 게이트웨이에 IP 주소가 할당됩니다.

## 응용 프로그램 게이트웨이 구성 만들기

응용 프로그램 게이트웨이를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### 1단계
"gatewayIP01"이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### 2단계
IP 주소가 "134.170.185.46, 134.170.188.221,134.170.185.50"인 "pool1"과 IP 주소가 "134.170.186.46, 134.170.189.221,134.170.186.50"인 "pool2"라는 백 엔드 IP 주소 풀을 구성합니다.


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

이 예제에서는 URL 경로를 기반으로 네트워크 트래픽을 라우트하는 두 개의 백 엔드 풀이 있습니다. 하나의 풀은 URL 경로 "/video"에서 트래픽을 수신하고 또 다른 풀은 경로 "/image"에서 트래픽을 수신합니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 위의 IP 주소를 바꿔야 합니다.

### 3단계
백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 응용 프로그램 게이트웨이 설정 "poolsetting01" 및 "poolsetting02"를 구성합니다. 이 예제에서는 백 엔드 풀에 대한 다른 백 엔드 풀 설정을 구성합니다. 각 백 엔드 풀에는 고유한 백 엔드 풀 설정이 있을 수 있습니다.

	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### 4단계
공용 IP 끝점으로 프런트 엔드 IP를 구성합니다.

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### 5단계 
응용 프로그램 게이트웨이에 대한 프런트 엔드 포트를 구성합니다.

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### 6단계
수신기를 구성합니다. 이 단계에서는 들어오는 네트워크 트래픽을 수신하는 데 사용되는 공용 IP 주소 및 포트에 대한 수신기를 구성합니다.
 
	$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### 7단계 
백 엔드 풀에 대한 URL 규칙 경로를 구성합니다. 이 단계에서는 응용 프로그램 게이트웨이가 URL 경로와 들어오는 트래픽을 처리하는 데 할당되는 백 엔드 풀 간의 매핑을 정의하는 데 사용하는 상대 경로를 구성합니다.

아래 예제에서는 두 개의 규칙을 만듭니다. 백 엔드 "pool1"로 트래픽을 라우트하는 "/image/" 경로에 대한 규칙과 백 엔드 "pool2"로 트래픽을 라우트하는 "/video/" 경로에 대한 규칙입니다.
    
	$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

	$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02

또한 경로가 미리 정의된 경로 규칙과 일치하지 않으면 규칙 경로 맵 구성은 기본 백 엔드 주소 풀을 구성합니다.

	$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### 8단계 
규칙 설정을 만듭니다. 이 단계에서는 URL 경로 기반 라우팅을 사용하도록 응용 프로그램 게이트웨이를 구성합니다.

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
### 9단계 
응용 프로그램 게이트웨이에 대한 크기 및 인스턴스 수를 구성합니다.

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## 응용 프로그램 게이트웨이 만들기
위 단계의 모든 구성 개체로 응용 프로그램 게이트웨이를 만듭니다.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

## 응용 프로그램 게이트웨이 가져오기
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG

<!---HONumber=AcomDC_0608_2016-->