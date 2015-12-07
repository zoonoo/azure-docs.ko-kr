<properties 
   pageTitle="웹앱에 대한 사용자 지정 DNS 레코드 만들기 | Microsoft Azure" 
   description="Azure DNS를 사용하여 웹앱에 대한 사용자 지정 도메인 DNS 레코드를 만드는 방법입니다. CNAME 또는 A 레코드를 사용하여 도메인 소유권을 검증하는 단계별 가이드입니다." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# 사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기

Azure DNS를 사용하여 웹앱에 대한 사용자 지정 도메인을 호스트할 수 있습니다. 예를 들어 Azure 웹앱을 만드는 중이며 사용자가 contoso.com 또는 www.contoso.com을 FQDN으로 사용하여 액세스하도록 설정한다고 가정합니다. 이 시나리오에서는 2개의 레코드, 즉 contoso.com을 가리키는 루트 A 레코드와 A 레코드를 가리키는 www 이름에 대한 CNAME 레코드를 만들어야 합니다.

> [AZURE.NOTE]Azure에서 웹앱에 대한 A 레코드를 만드는 경우 웹앱의 기본 IP 주소가 변경되면 A 레코드를 수동으로 업데이트해야 합니다.

사용자 지정 도메인에 대한 레코드를 만들려면 먼저 Azure DNS에서 DNS 영역을 만들고 등록 기관의 영역을 Azure DNS로 위임해야 합니다. DNS 영역을 만들려면 [Azure DNS 시작](../dns-getstarted-create-dnszone/#Create-a-DNS-zone)의 단계를 따르세요. DNS를 Azure DNS로 위임하려면 [Azure DNS로 도메인 위임](../dns-domain-delegation)의 단계를 따르세요.
 
## 사용자 지정 도메인에 대한 A 레코드 만들기

A 레코드는 이름을 해당 IP 주소에 매핑하는 데 사용됩니다. 다음 예제에서는 IPv4 주소에 @를 A 레코드로 할당합니다.

### 1단계
 
A 레코드를 만들고 $rs 변수에 할당합니다.
	
	PS C:\>$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 

### 2단계

할당된 $rs 변수를 사용하여 이전에 만든 레코드 집합 "@"에 IPv4 값을 추가합니다. 할당된 IPv4 값은 웹앱의 IP 주소가 됩니다.

> [AZURE.NOTE]웹앱의 IP 주소를 찾으려면 [Azure 앱 서비스에서 사용자 지정 도메인 이름 구성](../web-sites-custom-domain-name/#Find-the-virtual-IP-address)의 단계를 따르세요.

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### 3단계

레코드 집합의 변경 내용을 커밋합니다. Set-AzureRMDnsRecordSet를 사용하여 레코드 집합의 변경 내용을 Azure DNS로 업로드합니다.

	Set-AzureRMDnsRecordSet -RecordSet $rs

## 사용자 지정 도메인에 대한 CNAME 레코드 만들기

Azure DNS에서 이미 도메인을 관리하고 있는 경우([DNS 도메인 위임](../dns-domain-delegation) 참조), 다음 예제를 사용하여 contoso.azurewebsites.net에 대한 CNAME 레코드를 만들 수 있습니다.

### 1단계

PowerShell을 열고 새 CNAME 레코드 집합을 만든 다음 $rs 변수에 할당합니다.

	PS C:\> $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}

그러면 "contoso.com"이라는 DNS 영역에 "time to live"가 600초인 레코드 집합 형식 CNAME이 생성됩니다.

### 2단계

CNAME 레코드 집합을 만든 후에는 웹앱을 가리키는 별칭 값을 만들어야 합니다.

이전에 할당된 변수 "$rs"를 통해 아래 PowerShell 명령을 사용하여 웹앱 contoso.azurewebsites.net에 대한 별칭을 만들 수 있습니다.

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {contoso.azurewebsites.net}
	Tags              : {}

### 3단계

Set-AzureRMDnsRecordSet cmdlet을 사용하여 변경 내용을 커밋합니다.

	PS C:\>Set-AzureRMDnsRecordSet -RecordSet $rs

아래와 같이 nslookup으로 "www.contoso.com"을 쿼리하여 레코드가 올바르게 생성되었는지 확인할 수 있습니다.

	PS C:\> nslookup
	Default Server:  Default
	Address:  192.168.0.1
 
	> www.contoso.com
	Server:  default server
	Address:  192.168.0.1
	 
	Non-authoritative answer:
	Name:    <instance of web app service>.cloudapp.net
	Address:  <ip of web app service>
	Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## 웹앱에 대한 awverify 레코드 만들기(A 레코드만 해당)

웹앱에 대해 A 레코드를 사용하려는 경우 사용자 지정 도메인의 소유자가 맞는지 확인하도록 검증 프로세스를 수행해야 합니다. 이 검증 단계는 "awverify"라는 특수 CNAME 레코드를 만들어 수행됩니다.

아래 예제에서는 contoso.com에 대한 "awverify" 레코드를 만들어 사용자 지정 도메인에 대한 소유권을 검증합니다.

### 1단계

	PS C:\> $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### 2단계

레코드 집합 awverify를 만든 후에는 아래 명령과 같이 awverify.contoso.azurewebsites.net에 CNAME 레코드 집합 별칭을 할당해야 합니다.

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {awverify.contoso.azurewebsites.net}
	Tags              : {}

### 3단계

아래 명령과 같이 Set-AzureRMDnsRecordSet cmdlet을 사용하여 변경 내용을 커밋합니다.

	PS C:\>Set-AzureRMDnsRecordSet -RecordSet $rs

이제 계속해서 [앱 서비스에 대한 사용자 지정 도메인 이름 구성](../web-sites-custom-domain-name)의 단계에 따라 사용자 지정 도메인을 사용하도록 웹앱을 구성할 수 있습니다.

## 참고 항목

[DNS 영역 관리](../dns-operations-dnszones)

[DNS 레코드 관리](../dns-operations-recordsets)

[트래픽 관리자 개요](../traffic-manager-overview)

[.NET SDK로 Azure 작업 자동화](../dns-sdk)


 

<!---HONumber=AcomDC_1125_2015-->