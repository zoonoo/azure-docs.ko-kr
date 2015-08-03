<properties 
   pageTitle="SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성 | Microsoft Azure"
   description="이 문서는 Azure 응용 프로그램 게이트웨이에서 SSL 오프로드 구성 지침을 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/30/2015"
   ms.author="joaoma"/>

# SSL 오프로드에 대한 응용 프로그램 게이트웨이를 구성합니다.

응용 프로그램 게이트웨이 구성을 사용하여 웹 팜에서 비용이 많이 드는 SSL 암호 해독을 방지할 수 있는 게이트웨이에서 SSL 세션을 종료합니다. SSL 오프로드는 또한 프런트엔드 서버 설치 및 응용 프로그램의 관리를 간소화합니다.

## 시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하여 최신 버전의 Azure PowerShell cmdlet을 설치합니다. [다운로드 페이지](http://azure.microsoft.com/downloads/)의 **Windows PowerShell** 섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. 유효한 서브넷과 작업 가상 네트워크가 있는지 확인합니다.
3. 가상 네트워크 또는 할당된 공용 IP/VIP와 백엔드 서버가 있는지 확인합니다.

SSL 오프로드에 응용 프로그램 게이트웨이를 구성하려면 다음 단계를 나열된 순서대로 수행합니다.

1. [새 응용 프로그램 게이트웨이 만들기](#create-a-new-application-gateway)
2. [SSL 인증서를 업로드 합니다.](#upload-ssl-certificates) 
3. [게이트웨이 구성](#configure-the-gateway)
4. [게이트웨이 구성 설정](#set-the-gateway-configuration)
5. [게이트웨이 시작](#start-the-gateway)
6. [게이트웨이 상태를 확인합니다.](#verify-the-gateway-status)


## 새 응용 프로그램 게이트웨이 만들기

**게이트웨이를 생성**하려면 `New-AzureApplicationGateway` cmdlet을 사용하여 자체의 값을 대체합니다. 게이트웨이에 대한 청구는 이 지점에서 시작되지 않습니다. 게이트웨이가 성공적으로 작동되면, 요금청구가 시작됩니다.

이 출력 다음 샘플에서는 첫 줄에 cmdlet을 보여줍니다.

	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

생성된 게이트웨이의 **유효성을 검사**하려면 `Get-AzureApplicationGateway` cmdlet을 사용합니다.


이 샘플에서 *설명*, *InstanceCount* 및 *GatewaySize*는 선택적 매개 변수입니다. *InstanceCount*에 대한 기본값은 2이고, 최대값은 10입니다. *GatewaySize*에 대한 기본값은 보통입니다. 크고 작은 다른 사용 가능한 값이 됩니다. 게이트웨이가 아직 작동되지 않기 때문에 *Vip* 및 *DnsName* 출력이 빈 값으로 표시됩니다. 이 값들은 게이트웨이가 실행 상태가 되면 생성됩니다.

이 출력 다음 샘플에서는 첫 줄에 cmdlet을 보여줍니다.

	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 4:39:39 PM - Begin Operation:
	Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
	Operation: Get-AzureApplicationGateway
	Name: AppGwTest	
	Description: 
	VnetName: testvnet1 
	Subnets: {Subnet-1} 
	InstanceCount: 2 
	GatewaySize: Medium 
	State: Stopped 
	VirtualIPs: 
	DnsName:


## SSL 인증서를 업로드 합니다. 

`Add-AzureApplicationGatewaySslCertificate`을 사용하여 응용 프로그램 게이트웨이의*pfx* 형식의 서버 인증서를 업로드합니다. 인증서 이름은 사용자가 선택해야 하고 응용 프로그램 게이트웨이 내에서 고유해야 합니다. 이 인증서는 응용 프로그램 게이트웨이에 대한 모든 인증서 관리작업에 이름이 참조됩니다.

이 출력 다음 샘플에서는 첫 줄에 cmdlet을 보여줍니다. 사용자 고유의 샘플 값으로 대체 합니다.

	PS C:\> Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file> 
	
	VERBOSE: 5:05:23 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate 
	VERBOSE: 5:06:29 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   21fdc5a0-3bf7-2c12-ad98-192e0dd078ef

그런 다음 인증서 업로드 유효성을 검사 합니다. `Get-AzureApplicationGatewayCertificate`을 사용합니다.

이 출력 다음 샘플에서는 첫 줄에 cmdlet을 보여줍니다.

	PS C:\> Get-AzureApplicationGatewaySslCertificate AppGwTest 

	VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate 
	VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name           : SslCert 
	SubjectName    : CN=gwcert.app.test.contoso.com 
	Thumbprint     : AF5ADD77E160A01A6......EE48D1A 
	ThumbprintAlgo : sha1RSA
	State..........: Provisioned


## 게이트웨이 구성

응용 프로그램 게이트웨이는 여러 값으로 구성됩니다. 값은 연결되어 구성을 만듭니다.

값은 다음과 같습니다.
 
- **백엔드 서버 풀:** 백엔드 서버 IP 주소의 목록입니다. 나열 된 IP 주소는 VNet 서브넷에 속해야 하거나 또는 공용 IP/VIP 이어야 합니다. 
- **백엔드 서버 풀 설정:**프로토콜, 포트와 같은 모든 풀 설정 및 쿠키는 선호도 기반으로 합니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
- **프런트엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에서 열린 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백엔드 서버 중의 하나가 리디렉트됩니다.
- **수신기:** 수신기는 프런트엔드 포트, 프로토콜(Http 또는 Https, 이 경우 대/소문자 구분) 및 SSL 인증서 이름(SSL을 구성하는 작업에 오프로드하는 경우)을 가집니다. 
- **규칙:** 규칙은 수신기와 백엔드 서버 풀을 바인드하고 특정 수신기에 도달했을 때 디렉트하는 트래픽을 정의합니다. 현재는 *기본*규칙만 지원 됩니다. *기본*규칙은 라운드 로빈 부하 분산입니다.

**추가 구성 정보:**

SSL 인증서 구성에서 **HttpListener**의 프로토콜은 *Https*로(대/소문자 구분)바꿔야 합니다. **SslCert** 요소는 위의 SSL 인증서 섹션의 업로드에서 사용된 동일한 이름으로 설정된 해당 값으로 **HttpListener**에 추가해야 합니다. 프런트엔드 포트는 443으로 업데이트 되어야 합니다.

**선호도 쿠키 기반으로 사용하도록 설정**: 응용 프로그램 게이트웨이는 웹팜에 있는 동일한 VM으로부터 항상 연결되는 클라이언트 세션의 해당 요청을 확인할 수 있게 구성될 수 있습니다. 트래픽에 적절하게 연결해주는 게이트웨이를 허용하는 세션 쿠키를 삽입하면 완료됩니다. 쿠키를 기반 선호도를 사용하려면,**CookieBasedAffinity**를*사용할수 있게***BackendHttpSettings**요소에 설정합니다.



구성 개체를 만들거나, 구성 XML 파일을 사용하여 구성을 생성할 수 있습니다. 구성 XML 파일을 사용하여 구성을 생성하려면 아래 샘플을 사용합니다.

**구성 XML 샘플**


	    <?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendIPConfigurations />
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>443</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Https</Protocol>
	            <SslCert>GWCert</SslCert>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


## 게이트웨이 구성 설정

다음으로, 응용 프로그램 게이트웨이를 설정합니다. `Set-AzureApplicationGatewayConfig` cmdlet을 구성 XML 파일 또는 구성 개체와 함께 사용할 수 있습니다.


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## 게이트웨이 시작

게이트웨이가 구성되면, `Start-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이를 시작합니다. 응용 프로그램 게이트웨이에 대한 청구는 게이트웨이가 성공적으로 작동된 후 시작합니다.


**참고:** `Start-AzureApplicationGateway` cmdlet을 완료하려면 최대 15-20분까지 걸릴 수 있습니다.

   
	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b


## 게이트웨이 상태를 확인합니다.

`Get-AzureApplicationGateway` cmdlet을 사용하여 게이트웨이의 상태를 확인합니다. *시작 AzureApplicationGateway*가 이전 단계에서 성공한 경우, 상태가 *실행*이 되어야 하고, Vip와 DNS 이름은 유효한 항목을 가져야 합니다.

이 샘플에서는 응용 프로그램 시작, 실행 그리고 트래픽 받을 준비를 보여줍니다.

	PS C:\> Get-AzureApplicationGateway AppGwTest 

	Name          : AppGwTest2
	Description   : 
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	VirtualIPs    : {23.96.22.241}
	DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## 다음 단계


보다 자세한 내용을 원한다면 일반적 부하 분산 옵션을 참조:

- [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 트래픽 관리자](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=July15_HO4-->