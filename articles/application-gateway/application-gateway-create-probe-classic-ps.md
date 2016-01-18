<properties 
   pageTitle="클래식 배포 모델에서 PowerShell을 사용하여 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브 만들기 | Microsoft Azure"
   description="클래식 배포 모델에서 PowerShell을 사용하여 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브를 만드는 방법에 대해 알아봅니다."
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# PowerShell을 사용하여 응용 프로그램 게이트웨이(클래식)에 대한 사용자 지정 프로브 만들기


[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)].

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](application-gateway-create-probe-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## 새 응용 프로그램 게이트웨이 만들기 

응용 프로그램 게이트웨이를 만들기 위해 수행해야 할 단계의 순서가 있습니다.

1. 응용 프로그램 게이트웨이 리소스를 만듭니다.
2. 구성 XML 파일 또는 구성 개체를 만듭니다.
3. 구성을 새로 만든 응용 프로그램 게이트웨이 리소스에 커밋합니다.

### 응용 프로그램 게이트웨이 리소스 만들기

게이트웨이를 생성하려면 `New-AzureApplicationGateway` cmdlet을 사용하여 해당 값을 원하는 값으로 바꿉니다. 게이트웨이에 대한 청구는 이 지점에서 시작되지 않습니다. 게이트웨이가 성공적으로 작동되면, 요금청구가 시작됩니다.

다음 예제에서는 "testvnet1"이라는 가상 네트워크 및 "서브넷-1"이라는 서브넷을 사용하여 새 응용 프로그램 게이트웨이를 만듭니다.


	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* 및 *GatewaySize*는 선택적 매개 변수입니다.


생성된 게이트웨이의 **유효성을 검사**하려면 `Get-AzureApplicationGateway` cmdlet을 사용합니다.


	PS C:\> Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]*InstanceCount*의 기본값은 2이고, 최대값은 10입니다. *GatewaySize*의 기본값은 보통입니다. 작게, 보통 및 크게를 선택할 수 있습니다.


 게이트웨이가 아직 시작되지 않았으므로 *Vip* 및 *DnsName*이 빈 값으로 표시됩니다. 이 값들은 게이트웨이가 실행 상태가 되면 생성됩니다.

## 응용 프로그램 게이트웨이 구성

XML 또는 구성 개체를 사용하여 응용 프로그램 게이트웨이를 구성할 수 있습니다.

## XML을 사용하여 응용 프로그램 게이트웨이 구성

다음 예제에서는 XML 파일을 사용하여 모든 응용 프로그램 게이트웨이 설정을 구성하고 응용 프로그램 게이트웨이 리소스에 커밋합니다.

### 1단계  

다음 텍스트를 메모장에 복사합니다.


	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
	<FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
				<IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
	    <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


구성 항목에 대한 괄호 사이의 값을 편집합니다. 확장명이.xml 인 파일 저장

다음 예제에서는 구성 파일을 사용하여 공용 포트 80에서 Http 트래픽의 부하를 분산하는 응용 프로그램 게이트웨이를 설정하고 사용자 지정 프로브를 사용하여 두 IP 주소 사이의 백 엔드 포트 80으로 네트워크 트래픽을 전송하는 방법을 보여 줍니다.

>[AZURE.IMPORTANT]Http 또는 Https 프로토콜 항목은 대 소문자를 구분합니다.


새 구성 항목 <Probe>을 추가하여 사용자 지정 프로브를 구성합니다.

구성 매개 변수:

- **Name** - 사용자 지정 프로브에 대한 참조 이름
- **Protocol** - 사용되는 프로토콜(가능한 값은 Http 또는 Https)
- **Host** 및 **Path** - 응용 프로그램 게이트웨이가 인스턴스의 상태를 확인하기 위해 호출하는 완전한 URL 경로. 예: 웹 사이트가 http://contoso.com/이면 프로브 확인을 위해 사용자 지정 프로브를 "http://contoso.com/path/custompath.htm"에 대해 구성하여 성공적으로 HTTP에 응답할 수 있습니다. 
- **Interval** - 프로브 간격 확인(초) 구성 
- **Timeout** - HTTP 응답 확인에 대한 프로브 시간 제한 정의
- **UnhealthyThreshold** - 백 엔드 인스턴스를 *unhealthy*로 표시하는 데 필요한 실패한 HTTP 응답 수

프로브 이름은 사용자 지정 프로브 설정에 사용할 백 엔드 풀을 할당하는 <BackendHttpSettings> 구성에서 참조합니다.

## 기존 응용 프로그램 게이트웨이에 사용자 지정 프로브 구성 추가

현재 응용 프로그램 게이트웨이 구성 변경에 필요한 세 단계는 현재 XML 구성 파일 가져오기, 사용자 지정 프로브 수정 및 새 XML 설정으로 응용 프로그램 게이트웨이 구성입니다.

### 1단계

get-AzureApplicationGatewayConfig를 사용하여 xml 파일을 가져옵니다. 이렇게 하면 프로브 설정을 추가하기 위해 수정할 XML 구성을 내보냅니다.
	
	get-AzureApplicationGatewayConfig -Name <application gateway name> -Exporttofile "<path to file>"


### 2단계 

텍스트 편집기에서 XML 파일을 엽니다. `<frontendport>` 뒤에 `<probe>` 섹션 추가
	
	<Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>

XML의 backendHttpSettings 섹션에서 다음 예제에 표시된 대로 프로브 이름을 추가합니다.
    
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

XML 파일 저장


### 3단계 

`Set-AzureApplicationGatewayConfig`을 사용하여 새 XML 파일로 응용 프로그램 게이트웨이 구성을 업데이트합니다. 이렇게 하면 응용 프로그램 게이트웨이가 새 구성으로 업데이트됩니다.

	set-AzureApplicationGatewayConfig -Name <application gateway name> -Configfile "<path to file>"


## 다음 단계

SSL 오프로드를 구성하려는 경우 [SSL 오프로드에 대해 응용 프로그램 게이트웨이 구성](application-gateway-ssl.md)을 참조하세요.

ILB에서 사용되도록 응용 프로그램 게이트웨이를 구성하려면 [ILB(내부 부하 분산 장치)를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

<!---HONumber=AcomDC_0107_2016-->