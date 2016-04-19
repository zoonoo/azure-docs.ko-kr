<properties
   pageTitle="DNS 영역에 대한 레코드 집합 및 레코드 만들기 | Microsoft Azure"
   description="Azure DNS에 대한 호스트 레코드를 만드는 방법입니다. PowerShell을 사용하여 레코드 집합 및 레코드를 설정합니다."
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="joaoma"/>


# DNS 레코드 만들기


> [AZURE.SELECTOR]
- [Azure 포털](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)

DNS 영역을 만든 후 도메인에 대한 DNS 레코드를 추가해야 합니다. 이 작업을 수행하려면 먼저 DNS 레코드 및 레코드 집합을 이해해야 합니다.


## 레코드 집합 및 레코드 이해
각 DNS 레코드에는 이름 및 형식이 있습니다.

_정규화된_ 이름은 영역 이름을 포함하는 반면 _상대_ 이름은 영역 이름을 포함하지 않습니다. 예를 들어 'contoso.com' 영역의 상대 레코드 이름 'www'는 정규화된 레코드 이름 'www.contoso.com'을 제공합니다.

>[AZURE.NOTE] Azure DNS에서는 상대 이름을 사용하여 레코드를 지정합니다.

레코드는 포함된 데이터에 따라 다양한 형식으로 제공됩니다. 가장 일반적인 형식은 'A' 레코드로, 이름을 IPv4 주소에 매핑합니다. 또 다른 형식은 'MX' 레코드로, 이름을 메일 서버에 매핑합니다.

Azure DNS는 A, AAAA, CNAME, MX, NS, SOA, SRV, TXT 등 모든 일반적인 DNS 레코드 형식을 지원합니다. ([SPF 레코드는 TXT 레코드 형식을 사용하여 만들어야 함](http://tools.ietf.org/html/rfc7208#section-3.1)에 유의합니다.)

지정된 이름 및 형식을 가진 DNS 레코드를 두 개 이상 만들어야 하는 경우도 있습니다. 예를 들어 www.contoso.com 웹 사이트가 서로 다른 두 IP 주소에서 호스트된다고 가정합니다. 이 경우 각 IP 주소마다 하나씩, 두 개의 A 레코드가 있어야 합니다.

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

이것이 레코드 집합의 예입니다. 레코드 집합은 영역 내에서 동일한 이름과 형식을 가진 DNS 레코드 컬렉션입니다. 대부분의 레코드 집합은 단일 레코드를 포함하지만, 레코드 집합이 두 개 이상의 레코드를 포함하는 위와 같은 예도 드물지 않습니다. SOA 및 CNAME 형식의 레코드 집합은 예외입니다. DNS 표준에 따라 이러한 형식에는 동일한 이름을 가진 여러 레코드가 허용되지 않습니다.

Time-to-Live, 즉 TTL은 각 레코드가 다시 쿼리되기 전에 클라이언트에 캐시되는 기간을 지정합니다. 위 예제에서 TTL은 3600초, 즉 1시간입니다. TTL은 각 레코드가 아니라 레코드 집합에 대해 지정되므로 해당 레코드 집합 내의 모든 레코드에 동일한 값이 사용됩니다.

>[AZURE.NOTE] Azure DNS는 레코드 집합을 사용하여 DNS 레코드를 관리합니다.



## 레코드 집합 및 레코드 만들기

다음 예제에서는 레코드 집합 및 레코드를 만드는 방법을 보여 줍니다. DNS 'A' 레코드 유형을 사용하며, 다른 레코드 유형의 경우 [DNS 레코드를 관리하는 방법](dns-operations-recordsets.md)을 참조하세요.


### 1단계

레코드 집합을 만들고 $rs 변수에 할당합니다.

	PS C:\>$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

레코드 집합은 DNS 영역 'contoso.com'에서 상대 이름 'www'를 가지므로 레코드의 정규화된 이름은 'www.contoso.com'입니다. 레코드 형식은 'A'이고 TTL은 60초입니다.

>[AZURE.NOTE] 영역의 구로에서 레코드 집합을 만들려면(이 경우 'contoso.com'), 따옴표를 포함한 레코드 이름 "@"를 사용합니다. 이것이 일반적인 DNS 규칙입니다.

레코드 집합은 비어 있으며, 새로 만든 "www" 레코드 집합을 사용하려면 레코드를 추가해야 합니다.<BR>

### 2단계

1단계에서 레코드 집합을 만들 때 할당된 $rs 변수를 사용하여 "www" 레코드 집합에 IPv4 A 레코드를 추가합니다.

	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

Add-AzureRmDnsRecordConfig를 사용하여 레코드 집합에 레코드를 추가하는 작업은 오프라인 작업입니다. 지역 변수 $rs만 업데이트됩니다.

### 3단계
레코드 집합의 변경 내용을 커밋합니다. Set-AzureRmDnsRecordSet를 사용하여 레코드 집합의 변경 내용을 Azure DNS로 업로드합니다.


	Set-AzureRmDnsRecordSet -RecordSet $rs

변경이 완료되었습니다. Get-AzureRmDnsRecordSet를 사용하여 Azure DNS에서 레코드 집합을 가져올 수 있습니다.


	PS C:\> Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}



nslookup 또는 다른 DNS 도구를 사용하여 새 레코드 집합을 쿼리할 수도 있습니다.

>[AZURE.NOTE] 영역을 만드는 경우와 마찬가지로, Azure DNS 이름 서버에 도메인을 아직 위임하지 않은 경우 영역에 대한 이름 서버 주소를 명시적으로 지정해야 합니다.


	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221



## 다음 단계

[DNS 영역을 관리하는 방법](dns-operations-dnszones.md)

[DNS 레코드를 관리하는 방법](dns-operations-recordsets.md)<BR>

[.NET SDK로 Azure 작업 자동화](dns-sdk.md)
 

<!---HONumber=AcomDC_0406_2016-->