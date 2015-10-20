<properties 
   pageTitle="Azure 리소스 관리자를 사용하여 ILB(내부 부하 분산 장치)에서 응용 프로그램 게이트웨이 만들기 및 구성 | Microsoft Azure"
   description="이 페이지에서는 Azure 리소스 관리자용 ILB(내부 부하 분산 장치)를 사용하여 Azure 응용 프로그램 게이트웨이를 만들고, 구성하고, 시작하고, 삭제하기 위한 지침을 제공합니다."
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
   ms.date="09/21/2015"
   ms.author="joaoma"/>


# Azure 리소스 관리자를 사용하여 ILB(내부 부하 분산 장치)에서 응용 프로그램 게이트웨이 만들기

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-ilb.md)
- [Resource Manager Powershell steps](application-gateway-ilb-arm.md)

응용 프로그램 게이트웨이는 인터넷 연결 VIP 또는 ILB 끝점이라고 알려진 인터넷에 노출되지 않은 내부 끝점을 사용하여 구성할 수 있습니다. ILB를 사용하여 게이트웨이를 구성하는 것은 인터넷에 노출되지 않은 비즈니스 응용 프로그램의 내부 라인에 대해 유용합니다. 또한 인터넷에 노출되지 않은 보안 경계에 앉아 있는 다중 계층이 포함된 서비스/계층에 유용하지만 여전히 라운드 로빈 부하 분산, 세션 인력 또는 SSL 종료가 필요합니다. 이 문서는 ILB와 응용 프로그램 게이트웨이 구성 단계를 안내합니다.

## 시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하여 최신 버전의 Azure PowerShell cmdlet을 설치합니다. [다운로드 페이지](http://azure.microsoft.com/downloads/)의 **Windows PowerShell** 섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷을 만듭니다. 서브넷을 사용 중인 가상 컴퓨터 또는 클라우드 배포가 없는지 확인합니다. 응용 프로그램 게이트웨이는 가상 네트워크 서브넷에서 단독이어야 합니다.
3. 응용 프로그램 게이트웨이를 사용하도록 구성된 서버가 존재하거나 가상 네트워크나 공용 IP/VIP가 할당된 해당 끝점이 만들어져야 합니다.

## 응용 프로그램 게이트웨이를 만드는 데 필요한 것은 무엇입니까?
 

- **백엔드 서버 풀:** 백엔드 서버의 IP 주소 목록입니다. 나열된 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP/VIP이어야 합니다. 
- **백엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
- **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에서 열린 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백엔드 서버 중의 하나로 리디렉트됩니다.
- **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 경우 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다. 
- **규칙:** 규칙은 수신기와 백엔드 서버 풀을 바인드하고 특정 수신기에 도달했을 때 디렉트하는 트래픽을 정의합니다. 현재 *기본* 규칙만 지원됩니다. *기본* 규칙은 라운드 로빈 부하 분산입니다.


 
## 새 응용 프로그램 게이트웨이 만들기

Azure Classic 및 Azure 리소스 관리자 간의 차이점은 응용 프로그램 게이트웨이를 만드는 순서와 구성할 항목입니다. 리소스 관리자를 사용하면 응용 프로그램 게이트웨이를 만드는 모든 항목이 개별적으로 구성된 후 응용 프로그램 게이트웨이 리소스를 만드는 데 사용됩니다.


여기서는 응용 프로그램 게이트웨이 만드는 데 필요한 단계를 따르세요.

1. 리소스 관리자에 대한 리소스 그룹 만들기
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기
3. 응용 프로그램 게이트웨이 구성 개체 만들기
4. 응용 프로그램 게이트웨이 리소스 만들기


## 리소스 관리자에 대한 리소스 그룹 만들기

ARM cmdlet을 사용하려면 PowerShell 모드를 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell](powershell-azure-resource-manager.md) 사용을 참조하세요.

### 1단계

    Switch-AzureMode -Name AzureResourceManager

### 2단계

Azure 계정에 로그인


    Add-AzureAccount

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.


### 3단계

사용할 Azure 구독을 선택합니다.

    Select-AzureSubscription -SubscriptionName "MySubscription"

사용 가능한 구독 목록을 보려면 ‘Get-AzureSubscription’ cmdlet을 사용합니다.


### 4단계

새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    New-AzureResourceGroup -Name appgw-rg -location "West US"

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

위 예제에서는 "appgw-RG"라는 리소스 그룹과 "West US"라는 위치를 만들었습니다.

## 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 리소스 관리자를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다.

### 1단계	
	
	$subnetconfig = New-AzureVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.

### 2단계
	
	$vnet = New-AzurevirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역에 대해 리소스 그룹 "appw-rg"에서 "appgwvnet"이라는 가상 네트워크를 만듭니다.
	
### 3단계

	$subnet=$vnet.subnets[0]

다음 단계를 위해 $subnet 변수에 서브넷 개체를 할당합니다.
 

## 응용 프로그램 게이트웨이 구성 개체 만들기

### 1단계

	$gipconfig = New-AzureApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

"gatewayIP01"이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.
 
### 2단계

	$pool = New-AzureApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.10,10.0.0.11,10.0.0.12

이 단계에서는 IP 주소가 "10.0.0.10,10.0.0.11, 10.0.0.12"이라는 "pool01"라는 백엔드 IP 주소 풀을 구성합니다. 이러한 IP 주소는 프런트 엔드 IP 끝점에서 들어오는 네트워크 트래픽을 수신합니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 위의 IP 주소를 바꿉니다.

### 3단계

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 응용 프로그램 게이트웨이 설정 "poolsetting01"을 구성합니다.

### 4단계

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 80

ILB에 대해 "frontendport01"이라는 프런트 엔드 IP 포트를 구성합니다.

### 5단계

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

"fipconfig01"라는 프런트 엔드 IP 구성을 만들고 현재 가상 네트워크 서브넷의 개인 IP와 연결합니다.

### 6단계

	$listener = New-AzureApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

"listener01"라는 수신기를 만들고 프런트 엔드 IP 구성에 프런트 엔드 포트를 연결합니다.

### 7단계 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

"rule01"라는 부하 분산 장치 라우팅 규칙을 만들고 부하 분산 장치 동작을 구성합니다.

### 8단계

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

응용 프로그램 게이트웨이의 인스턴스 크기를 구성합니다.

>[AZURE.NOTE]*InstanceCount*의 기본값은 2이고, 최대값은 10입니다. *GatewaySize*의 기본값은 보통입니다. Standard\_Small, Standard\_Medium 및 Standard\_Large 간에 선택할 수 있습니다.

## New-AzureApplicationGateway를 사용하여 응용 프로그램 게이트웨이 만들기

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

위 단계의 모든 구성 항목으로 응용 프로그램 게이트웨이를 만듭니다. 이 예제에서는 응용 프로그램 게이트웨이를 "appgwtest"라고 합니다.



## 게이트웨이 시작

게이트웨이가 구성되면, `Start-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이를 시작합니다. 응용 프로그램 게이트웨이에 대한 청구는 게이트웨이가 성공적으로 작동된 후 시작합니다.


**참고:** `Start-AzureApplicationGateway` cmdlet을 완료하려면 최대 15-20분까지 걸릴 수 있습니다.

아래 예제에서는 응용 프로그램 게이트웨이를 "appgwtest"라고 하고 리소스 그룹을 "appgw-rg"라고 합니다.


### 1단계

게이트웨이 응용 프로그램 개체를 가져오고 "$getgw" 변수에 연결합니다.
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 2단계
	 
`Start-AzureApplicationGateway`를 사용하여 응용 프로그램 게이트웨이를 시작합니다.

	PS C:\> Start-AzureApplicationGateway -ApplicationGateway $getgw  

	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## 응용 프로그램 게이트웨이 상태 확인

`Get-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이의 상태를 확인합니다. *Start-AzureApplicationGateway*가 이전 단계에서 성공한 경우 상태가 *실행 중*이어야 하고, Vip와 DnsName에 유효한 항목이 있어야 합니다.

이 샘플은 작동되어 실행 중이고 `http://<generated-dns-name>.cloudapp.net`으로 전송된 트래픽을 받아들일 준비가 된 응용 프로그램 게이트웨이를 보여 줍니다.

	PS C:\> Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest 
	Description   : 
	VnetName      : appgwvnet 
	Subnets       : {Subnet01} 
	InstanceCount : 2 
	GatewaySize   : Medium 
	State         : Running 
	Vip           : 138.91.170.26 
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## 응용 프로그램 게이트웨이 삭제

응용 프로그램 게이트웨이를 삭제하려면 다음을 순서대로 수행해야 합니다.

1. `Stop-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이를 중지합니다. 
2. `Remove-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이를 제거합니다.
3. `Get-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이가 제거되었는지 확인합니다.

이 샘플의 첫째 줄에는 `Stop-AzureApplicationGateway` cmdlet이 표시되고 그다음에 출력이 표시됩니다.

### 1단계

게이트웨이 응용 프로그램 개체를 가져오고 "$getgw" 변수에 연결합니다.
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 2단계
	 
`Stop-AzureApplicationGateway`를 사용하여 응용 프로그램 게이트웨이를 중지합니다.

	PS C:\> Stop-AzureApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

응용 프로그램 게이트웨이가 중지됨 상태이면 `Remove-AzureApplicationGateway` cmdlet을 사용하여 서비스를 제거합니다.


	PS C:\> Remove-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE]'-force' 스위치를 사용하여 제거 확인 메시지가 표시되지 않도록 할 수 있습니다.
>

서비스가 제거되었는지 확인하려면 `Get-AzureApplicationGateway` cmdlet을 사용할 수 있습니다. 이 단계는 필요 하지 않습니다.


	PS C:\>Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## 다음 단계

SSL 오프로드를 구성하려는 경우 [SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성](application-gateway-ssl.md)을 참조하세요.

ILB에 사용하기 위해 응용 프로그램 게이트웨이를 구성하려는 경우 [ILB(내부 부하 분산 장치)를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

보다 자세한 내용을 원한다면 일반적 부하 분산 옵션을 참조:

- [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 트래픽 관리자](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Oct15_HO3-->