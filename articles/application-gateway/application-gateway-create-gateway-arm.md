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
   ms.date="09/21/2015"
   ms.author="joaoma"/>


# Azure 리소스 관리자를 사용하여 응용 프로그램 게이트웨이 만들기, 시작 또는 삭제

응용 프로그램 게이트웨이는 부하 분산 장치 계층 7입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다. 응용 프로그램 게이트웨이의 응용 프로그램 전달 기능에는 HTTP 부하 분산, 쿠키 기반 세션 선호도, SSL 오프로드 등이 있습니다.


> [AZURE.SELECTOR]
- [Azure Classic Powershell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager Powershell steps](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template steps](application-gateway-create-gateway-arm-template.md)


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
- **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에서 열린 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백엔드 서버 중의 하나로 리디렉트됩니다.
- **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 경우 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다. 
- **규칙:** 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽이 이동되는 백 엔드 서버 풀을 정의합니다. 현재 *기본* 규칙만 지원됩니다. *기본* 규칙은 라운드 로빈 부하 분산입니다.


 
## 새 응용 프로그램 게이트웨이 만들기

Azure Classic 및 Azure 리소스 관리자 간의 차이점은 응용 프로그램 게이트웨이를 만드는 순서와 구성할 항목입니다.

리소스 관리자를 사용하면 응용 프로그램 게이트웨이를 만드는 모든 항목이 개별적으로 구성된 후 응용 프로그램 게이트웨이 리소스를 만드는 데 사용됩니다.


여기서는 응용 프로그램 게이트웨이 만드는 데 필요한 단계를 따르세요.

1. 리소스 관리자에 대한 리소스 그룹 만들기
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크, 서브넷 및 공용 IP 만들기
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
	
	$subnet = New-AzureVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.

### 2단계	
	$vnet = New-AzurevirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역에 대해 리소스 그룹 "appw-rg"에서 "appgwvnet"이라는 가상 네트워크를 만듭니다.

### 3단계
	
	$subnet=$vnet.Subnets[0]

## 프런트 엔드 구성에 대한 공용 IP 주소 만들기

	$publicip = New-AzurePublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

미국 서부 지역에 대해 리소스 그룹 "appw-rg"에서 공용 IP 리소스 "PublicIP01"을 만듭니다.


## 응용 프로그램 게이트웨이 구성 개체 만들기

### 1단계

	$gipconfig = New-AzureApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

"gatewayIP01"이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.
 
### 2단계

	$pool = New-AzureApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

이 단계에서는 IP 주소가 "134.170.185.46, 134.170.188.221,134.170.185.50"인 "pool01"이라는 백 엔드 IP 주소 풀을 구성합니다. 이러한 IP 주소는 프런트 엔드 IP 끝점에서 들어오는 네트워크 트래픽을 수신합니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 위의 IP 주소를 바꿉니다.

### 3단계

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 응용 프로그램 게이트웨이 설정 "poolsetting01"을 구성합니다.

### 4단계

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 80

공용 IP 끝점의 경우 여기서 "frontendport01"이라는 프런트 엔드 IP 포트를 구성합니다.

### 5단계

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

"fipconfig01"이라는 프런트 엔드 IP 구성을 만들고 프런트 엔드 IP 구성에 공용 IP 주소를 연결합니다.

### 6단계

	$listener = New-AzureApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

"listener01"이라는 수신기를 만들고 프런트 엔드 IP 구성에 프런트 엔드 포트를 연결합니다.

### 7단계 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

부하 분산 장치 동작을 구성하는 "rule01"이라는 부하 분산 장치 라우팅 규칙을 만듭니다.

### 8단계

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

응용 프로그램 게이트웨이의 인스턴스 크기를 구성합니다.

>[AZURE.NOTE]*InstanceCount*에 대한 기본값은 2이고, 최대값은 10입니다. *GatewaySize*에 대한 기본값은 보통입니다. Standard\_Small, Standard\_Medium 및 Standard\_Large 간에 선택할 수 있습니다.

## New-AzureApplicationGateway를 사용하여 응용 프로그램 게이트웨이 만들기

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

위 단계의 모든 구성 항목으로 응용 프로그램 게이트웨이를 만듭니다. 이 예제에서는 응용 프로그램 게이트웨이를 "appgwtest"라고 합니다.


## 응용 프로그램 게이트웨이 시작

게이트웨이가 구성되면, `Start-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이를 시작합니다. 응용 프로그램 게이트웨이에 대한 청구는 게이트웨이가 성공적으로 작동된 후 시작합니다.


**참고:** `Start-AzureApplicationGateway` cmdlet을 완료하려면 최대 15-20분까지 걸릴 수 있습니다.

아래 예제에서는 응용 프로그램 게이트웨이를 "appgwtest"라고 하고 리소스 그룹을 "app-rg"라고 합니다.


### 1단계

게이트웨이 응용 프로그램 개체를 가져오고 "$getgw" 변수에 연결합니다.
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### 2단계
	 
`Start-AzureApplicationGateway`를 사용하여 응용 프로그램 게이트웨이를 시작합니다.

	 Start-AzureApplicationGateway -ApplicationGateway $getgw  

	

## 응용 프로그램 게이트웨이 상태 확인

`Get-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이의 상태를 확인합니다. *시작 AzureApplicationGateway*가 이전 단계에서 성공한 경우, 상태가 *실행*이 되어야 하고, Vip와 DNS 이름은 유효한 항목을 가져야 합니다.

이 샘플은 작동되어 실행 중이고 `http://<generated-dns-name>.cloudapp.net`으로 전송된 트래픽을 받아들일 준비가 된 응용 프로그램 게이트웨이를 보여 줍니다.

	Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	Sku                               : Microsoft.Azure.Commands.Network.Models.PSApplicationGatewaySku
	GatewayIPConfigurations           : {gatewayip01}
	SslCertificates                   : {}
	FrontendIPConfigurations          : {frontendip01}
	FrontendPorts                     : {frontendport01}
	BackendAddressPools               : {pool01}
	BackendHttpSettingsCollection     : {setting01}
	HttpListeners                     : {listener01}
	RequestRoutingRules               : {rule01}
	OperationalState                  : 
	ProvisioningState                 : Succeeded
	GatewayIpConfigurationsText       : [
                                      {
                                        "Subnet": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/virtualNetworks/vnet01/subnets/subnet01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "gatewayip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/gatewayIPConfigurations/gatewayip
                                    01"
                                      }
                                    ]
	SslCertificatesText               : []
	FrontendIpConfigurationsText      : [
                                      {
                                        "PrivateIPAddress": null,
                                        "PrivateIPAllocationMethod": "Dynamic",
                                        "Subnet": null,
                                        "PublicIPAddress": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/publicIPAddresses/publicip01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/frontend
                                    ip01"
                                      }
                                    ]
	FrontendPortsText                 : [
                                      {
                                        "Port": 80,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendport01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                      }
                                    ]
	BackendAddressPoolsText           : [
                                      {
                                        "BackendAddresses": [
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.46"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.188.221"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.50"
                                          }
                                        ],
                                        "BackendIpConfigurations": [],
                                        "ProvisioningState": "Succeeded",
                                        "Name": "pool01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                      }
                                    ]
	BackendHttpSettingsCollectionText : [
                                      {
                                        "Port": 80,
                                        "Protocol": "Http",
                                        "CookieBasedAffinity": "Disabled",
                                        "ProvisioningState": "Succeeded",
                                        "Name": "setting01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/set
                                    ting01"
                                      }
                                    ]
	HttpListenersText                 : [
                                      {
                                        "FrontendIpConfiguration": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fronte
                                    ndip01"
                                        },
                                        "FrontendPort": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                        },
                                        "Protocol": "Http",
                                        "SslCertificate": null,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "listener01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                      }
                                    ]
	RequestRoutingRulesText           : [
                                      {
                                        "RuleType": "Basic",
                                        "BackendAddressPool": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                        },
                                        "BackendHttpSettings": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/s
                                    etting01"
                                        },
                                        "HttpListener": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "rule01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/requestRoutingRules/rule01"
                                      }
                                    ]
	ResourceGroupName                 : appgw-rg
	Location                          : westus
		Tag                               : {}
	TagsTable                         : 
	Name                              : appgwtest
	Etag                              : W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"
	Id                                : /subscriptions/###############################/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest




## 응용 프로그램 게이트웨이 삭제

응용 프로그램 게이트웨이를 삭제하려면 다음을 순서대로 수행해야 합니다.

1. `Stop-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이를 중지합니다. 
2. `Remove-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이를 제거합니다.
3. `Get-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이가 제거되었는지 확인합니다.


### 1단계

게이트웨이 응용 프로그램 개체를 가져오고 "$getgw" 변수에 연결합니다.
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 2단계
	 
`Stop-AzureApplicationGateway`를 사용하여 응용 프로그램 게이트웨이를 중지합니다.

	Stop-AzureApplicationGateway -ApplicationGateway $getgw  


응용 프로그램 게이트웨이가 중지됨 상태이면 `Remove-AzureApplicationGateway` cmdlet을 사용하여 서비스를 제거합니다.


	Remove-AzureApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

	

>[AZURE.NOTE]'-force' 스위치를 사용하여 제거 확인 메시지가 표시되지 않도록 할 수 있습니다.
>

서비스가 제거되었는지 확인하려면 `Get-AzureApplicationGateway` cmdlet을 사용합니다. 이 단계는 필요 하지 않습니다.


	Get-AzureApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg

	


## 다음 단계

SSL 오프로드를 구성하려는 경우 [SSL 오프로드에 대해 응용 프로그램 게이트웨이 구성](application-gateway-ssl.md)을 참조하세요.

ILB에 사용하기 위해 응용 프로그램 게이트웨이를 구성하려는 경우 [ILB(내부 부하 분산 장치)를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

보다 자세한 내용을 원한다면 일반적 부하 분산 옵션을 참조:

- [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 트래픽 관리자](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Nov15_HO2-->