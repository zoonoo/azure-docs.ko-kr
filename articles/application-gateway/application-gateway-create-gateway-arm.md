<properties 
   pageTitle="Azure 리소스 관리자를 사용하여 응용 프로그램 게이트웨이 만들기, 시작 또는 삭제 | Microsoft Azure"
   description="이 페이지에서는 Azure 리소스 관리자를 사용하여 Azure 응용 프로그램 게이트웨이를 만들고, 구성하고, 시작하고, 삭제하기 위한 지침을 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>


# Azure 리소스 관리자를 사용하여 응용 프로그램 게이트웨이 만들기, 시작 또는 삭제

응용 프로그램 게이트웨이는 계층 7 부하 분산 장치입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다. 응용 프로그램 게이트웨이의 응용 프로그램 전달 기능에는 HTTP 부하 분산, 쿠키 기반 세션 선호도, SSL 오프로드 등이 있습니다.


> [AZURE.SELECTOR]
- [Azure Classic PowerShell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template ](application-gateway-create-gateway-arm-template.md)


<BR>


이 문서는 응용 프로그램 게이트웨이를 생성 및 구성, 시작, 삭제하는 단계를 안내합니다.


>[AZURE.IMPORTANT]Azure 리소스로 작업하기 전에 Azure에는 현재 리소스 관리자와 클래식 모드의 두 가지 배포 모델이 있다는 것을 이해해야 합니다. Azure 리소스로 작업하기 전에 [배포 모델 및 도구](azure-classic-rm.md)를 이해해야 합니다. 이 문서의 윗부분에 있는 탭을 클릭하여 다양한 도구에 대한 설명서를 볼 수 있습니다. 이 문서에서는 Azure 리소스 관리자를 사용하여 응용 프로그램 게이트웨이를 만드는 방법을 설명합니다. 클래식 버전을 사용하려면 [PowerShell을 사용하여 응용 프로그램 게이트웨이 클래식 배포 만들기](application-gateway-create-gateway.md)로 이동합니다.



## 시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하여 최신 버전의 Azure PowerShell cmdlet을 설치합니다. [다운로드 페이지](http://azure.microsoft.com/downloads/)의 **Windows PowerShell** 섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷을 만듭니다. 서브넷을 사용 중인 가상 컴퓨터 또는 클라우드 배포가 없는지 확인합니다. 응용 프로그램 게이트웨이는 가상 네트워크 서브넷에서 단독이어야 합니다.
3. 응용 프로그램 게이트웨이를 사용하도록 구성된 서버가 존재하거나 가상 네트워크나 공용 IP/VIP가 할당된 해당 끝점이 만들어져야 합니다.

## 응용 프로그램 게이트웨이를 만드는 데 필요한 것은 무엇입니까?
 

- **백 엔드 서버 풀:** 백 엔드 서버의 IP 주소 목록. 나열된 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP/VIP이어야 합니다. 
- **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
- **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백엔드 서버 중의 하나로 리디렉트됩니다.
- **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 경우 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다. 
- **규칙:** 규칙은 수신기와 백엔드 서버 풀을 바인드하고 특정 수신기에 도달했을 때 디렉트하는 트래픽을 정의합니다. 현재는 *기본* 규칙만 지원 됩니다. *기본* 규칙은 라운드 로빈 부하 분산입니다.


 
## 새 응용 프로그램 게이트웨이 만들기

Azure Classic 및 Azure 리소스 관리자 간의 차이점은 응용 프로그램 게이트웨이를 만드는 순서와 구성할 항목입니다.

리소스 관리자를 사용하면 응용 프로그램 게이트웨이를 만드는 모든 항목이 개별적으로 구성된 후 응용 프로그램 게이트웨이 리소스를 만드는 데 사용됩니다.


여기서는 응용 프로그램 게이트웨이를 만드는 데 필요한 단계를 따르세요.

1. 리소스 관리자에 대한 리소스 그룹 만들기
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크, 서브넷 및 공용 IP 만들기
3. 응용 프로그램 게이트웨이 구성 개체 만들기
4. 응용 프로그램 게이트웨이 리소스 만들기


## 리소스 관리자에 대한 리소스 그룹 만들기

Azure PowerShell의 최신 버전을 사용하고 있는지 확인합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell](powershell-azure-resource-manager.md) 사용을 참조하세요.

### 1단계

		Login-AzureRmAccount



### 2단계

계정에 대한 구독을 확인합니다.

		Get-AzureRmSubscription 

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.<BR>

### 3단계 

사용할 Azure 구독을 선택합니다. <BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 4단계

새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

위 예제에서는 "appgw-RG"라는 리소스 그룹과 "West US"라는 위치를 만들었습니다.


>[AZURE.NOTE]응용 프로그램 게이트웨이에 사용자 지정 프로브를 구성해야 하는 경우 [PowerShell을 사용하여 사용자 지정 프로브로 응용 프로그램 게이트웨이를 만드는 방법](application-gateway-create-probe-ps.md) 문서로 이동합니다. 자세한 내용은 [사용자 지정 프로브 및 상태 모니터링](application-gateway-probe-overview.md)을 확인합니다.



## 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 리소스 관리자를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다.

### 1단계	
	
주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### 2단계	

접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역에 리소스 그룹 "appgw-rg"에서 "appgwvnet"이라는 가상 네트워크를 만듭니다.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 3단계
	
응용 프로그램 게이트웨이를 만드는 다음 단계에 서브넷 변수 할당

	$subnet=$vnet.Subnets[0]

## 프런트 엔드 구성에 대한 공용 IP 주소 만들기

미국 서부 지역에 리소스 그룹 "appgw-rg"에서 공용 IP 리소스 "publicIP01"을 만듭니다.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## 응용 프로그램 게이트웨이 구성 개체 만들기

응용 프로그램 응용 프로그램 게이트웨이를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### 1단계

"gatewayIP01"이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

 
### 2단계

이 단계에서는 IP 주소가 "134.170.185.46, 134.170.188.221,134.170.185.50"인 "pool01"이라는 백 엔드 IP 주소 풀을 구성합니다. 이러한 IP 주소는 프런트 엔드 IP 끝점에서 들어오는 네트워크 트래픽을 수신합니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 위의 IP 주소를 바꿉니다.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### 3단계

백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 응용 프로그램 게이트웨이 설정 "poolsetting01"을 구성합니다.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### 4단계

공용 IP 끝점의 경우 여기서 "frontendport01"이라는 프런트 엔드 IP 포트를 구성합니다.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80


### 5단계

"fipconfig01"이라는 프런트 엔드 IP 구성을 만들고 프런트 엔드 IP 구성에 공용 IP 주소를 연결합니다.

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### 6단계

"listener01"이라는 수신기를 만들고 프런트 엔드 IP 구성에 프런트 엔드 포트를 연결합니다.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### 7단계 

부하 분산 장치 동작을 구성하는 "rule01"이라는 부하 분산 장치 라우팅 규칙을 만듭니다.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### 8단계

응용 프로그램 게이트웨이의 인스턴스 크기를 구성합니다.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]*InstanceCount*의 기본값은 2이고, 최대값은 10입니다. *GatewaySize*의 기본값은 보통입니다. Standard\_Small, Standard\_Medium 및 Standard\_Large 간에 선택할 수 있습니다.

## New-AzureRmApplicationGateway를 사용하여 응용 프로그램 게이트웨이 만들기

위 단계의 모든 구성 항목으로 응용 프로그램 게이트웨이를 만듭니다. 이 예제에서는 응용 프로그램 게이트웨이를 "appgwtest"라고 합니다.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku


## 응용 프로그램 게이트웨이 삭제

응용 프로그램 게이트웨이를 삭제하려면 다음을 순서대로 수행해야 합니다.

1. `Stop-AzureRmApplicationGateway` cmdlet을 사용하여 게이트웨이를 중지합니다. 
2. `Remove-AzureRmApplicationGateway` cmdlet을 사용하여 게이트웨이를 제거합니다.
3. `Get-AzureRmApplicationGateway` cmdlet을 사용하여 게이트웨이가 제거되었는지 확인합니다.

### 1단계

게이트웨이 응용 프로그램 개체를 가져오고 "$getgw" 변수에 연결합니다.
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 2단계
	 
`Stop-AzureRmApplicationGateway`를 사용하여 응용 프로그램 게이트웨이를 중지합니다.

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


응용 프로그램 게이트웨이의 State가 Stopped가 되면 `Remove-AzureRmApplicationGateway` cmdlet을 사용하여 서비스를 제거합니다.


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

	

>[AZURE.NOTE]'-force' 스위치를 사용하여 제거 확인 메시지가 표시되지 않도록 할 수 있습니다.


서비스가 제거되었는지 확인하려면 `Get-AzureRmApplicationGateway` cmdlet을 사용합니다. 이 단계는 필요 하지 않습니다.


	Get-AzureRmApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg

	
## 다음 단계

SSL 오프로드를 구성하려는 경우, [SSL 오프로드를 위한 응용 프로그램 게이트웨이 구성](application-gateway-ssl.md)을 참조하세요.

ILB를 사용하도록 응용 프로그램 게이트웨이를 구성하려는 경우, [ILB(Internal Load Balancer)를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

보다 자세한 내용을 원한다면 일반적 부하 분산 옵션을 참조:

- [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 트래픽 관리자](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0107_2016-->