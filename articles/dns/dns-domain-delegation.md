<properties
   pageTitle="Azure DNS로 도메인 위임 | Microsoft Azure"
   description="도메인 위임을 변경하고 Azure DNS 이름 서버를 사용하여 도메인 호스팅을 제공하는 방법을 이해합니다."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2016"
   ms.author="cherylmc"/>


# Azure DNS에 도메인 위임

Azure DNS를 사용하면 DNS 영역을 호스트하고 Azure에서 도메인에 대한 DNS 레코드를 관리할 수 있습니다. 도메인에 대한 DNS 쿼리가 Azure DNS에 도달하려면 부모 도메인에서 Azure DNS로 도메인을 위임해야 합니다. Azure DNS는 도메인 등록 기관이 아닙니다. 이 문서에서는 도메인 위임의 작동 방식 및 도메인을 Azure DNS로 위임하는 방법을 설명합니다.




## DNS 위임의 작동 방식

### 도메인 및 영역

Domain Name System은 도메인 계층 구조입니다. 계층 구조는 이름이 단순히 ‘**.**’인 ‘root’ 도메인에서 시작합니다. 그 아래에 'com', 'net', 'org', 'uk' 또는 'jp'와 같은 최상위 도메인이 있습니다. 그 아래에 'org.uk' 또는 'co.jp'와 같은 두 번째 수준의 도메인이 있는 방식으로 계속됩니다. DNS 계층 구조의 도메인은 별도의 DNS 영역을 사용하여 호스트됩니다. 이러한 영역은 전체적으로 분산되며 전 세계의 DNS 이름 서버에서 호스트됩니다.

**DNS 영역**

도메인은 Domain Name System의 고유 이름입니다(예: 'contoso.com'). DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. 예를 들어 'contoso.com' 도메인은 'mail.contoso.com'(메일 서버) 및 'www.contoso.com'(웹 사이트)과 같은 많은 DNS 레코드를 포함할 수 있습니다.

**도메인 등록 기관**

도메인 등록 기관은 인터넷 도메인 이름을 제공할 수 있는 회사입니다. 사용하려는 인터넷 도메인이 사용 가능한지 확인하고 구입할 수 있게 해줍니다. 도메인 이름이 등록되면 도메인 이름에 대한 법적 소유자가 됩니다. 인터넷 도메인이 이미 있는 경우 현재 도메인 등록 기관을 사용하여 Azure DNS에 위임합니다.

>[AZURE.NOTE] 지정된 도메인 이름의 소유자에 대한 자세한 정보나 도메인 구입 방법에 대한 정보를 알아보려면 [Azure AD에서 인터넷 도메인 관리](https://msdn.microsoft.com/library/azure/hh969248.aspx)를 참조하세요.

### 확인 및 위임

DNS 서버에는 다음 두 가지 유형이 있습니다.

- _권한이 있는_ DNS 서버는 DNS 영역을 호스트합니다. 해당 영역의 레코드에 대한 DNS 쿼리에만 대답합니다.
- _재귀적_ DNS 서버는 DNS 영역을 호스트하지 않습니다. 권한이 있는 DNS 서버를 호출하고 필요한 데이터를 수집하여 모든 DNS 쿼리에 응답합니다.

>[AZURE.NOTE] Azure DNS는 권한이 있는 DNS 서비스를 제공합니다. 재귀 DNS 서비스를 제공하지 않습니다.

> Azure의 클라우드 서비스 및 VM은 자동으로 구성하여 Azure의 인프라의 일부로 제공되는 재귀 DNS 서비스를 별도로 사용합니다. 이러한 DNS 설정을 변경하는 방법에 대한 정보는 [Azure에서 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)을 참조하세요.

PC 또는 모바일 장치의 DNS 클라이언트는 일반적으로 재귀적 DNS 서버를 호출하여 클라이언트 응용 프로그램에 필요한 DNS 쿼리를 수행합니다.

재귀적 DNS 서버는 'www.contoso.com'과 같은 DNS 레코드에 대한 쿼리를 받을 경우 먼저 'contoso.com' 도메인에 대한 영역을 호스트하는 이름 서버를 찾아야 합니다. 이 작업을 위해 루트 이름 서버에서 시작하여 'com' 영역을 호스트하는 이름 서버를 찾습니다. 그런 다음 'com' 이름 서버를 쿼리하여 'contoso.com' 영역을 호스트하는 이름 서버를 찾습니다. 끝으로, 이러한 이름 서버에 'www.contoso.com'을 쿼리할 수 있습니다.

이 과정을 DNS 이름 확인이라고 합니다. 엄격히 말해, DNS 확인에는 CNAME 따르기와 같은 추가 단계가 포함되지만 DNS 위임의 작동 방식을 이해하는 데에는 중요하지 않습니다.

부모 영역은 자식 영역에 대한 이름 서버를 어떻게 ‘가리킬까요'? 이 작업을 위해 NS 레코드(NS는 '이름 서버'의 약자)라는 특수 형식의 DNS 레코드를 사용합니다. 예를 들어 루트 영역에는 'com'에 대한 NS 레코드가 포함되며 'com' 영역에 대한 이름 서버가 표시됩니다. 마찬가지로, 'com' 영역에는 'contoso.com' 영역에 대한 이름 서버를 보여주는 'contoso.com'에 대한 NS 레코드가 포함됩니다. 부모 영역에서 자식 영역에 대한 NS 레코드를 설정하는 작업을 도메인 위임이라고 합니다.


![Dns-nameserver](./media/dns-domain-delegation/image1.png)

실제로 각 위임에는 NS 레코드의 두 복사본이 있습니다. 하나는 자식을 가리키는 부모 영역의 NS 레코드이고 다른 하나는 자식 영역 자체의 NS 레코드입니다. 'contoso.com' 영역은 'com'의 NS 레코드뿐 아니라 'contoso.com'의 NS 레코드를 포함합니다. 이를 권한이 있는 NS 레코드라고 하며 자식 영역의 루트에 있습니다.


## Azure DNS에 도메인 위임

Azure DNS에서 DNS 영역을 만든 후 부모 영역에서 NS 레코드를 설정하여 Azure DNS를 영역에 대한 권한이 있는 이름 확인 소스로 만들어야 합니다. 등록 기관에서 구입한 도메인의 경우 등록 기관에서 이러한 NS 레코드를 설정하는 옵션을 제공합니다.

>[AZURE.NOTE] Azure DNS에서 해당 도메인 이름으로 DNS 영역을 만들기 위해 도메인을 소유할 필요는 없습니다. 그러나 등록 기관에서 Azure DNS로 위임을 설정하려면 도메인을 소유해야 합니다.

예를 들어 'contoso.com' 도메인을 구입하고 Azure DNS에서 이름이 'contoso.com'인 영역을 만든다고 가정합니다. 도메인 소유자로, 등록 기관에서 도메인에 대한 이름 서버 주소(즉, NS 레코드)를 구성하는 옵션을 제공합니다. 등록 기관은 이러한 NS 레코드를 부모 도메인, 이 경우 'com'에 저장합니다. 그러면 전 세계 클라이언트가 'contoso.com'의 DNS 레코드를 확인하려고 할 때 Azure DNS 영역의 도메인으로 보내집니다.

### 이름 서버 이름 찾기

DNS 영역을 Azure DNS에 위임하려면 먼저 해당 영역에 대한 이름 서버 이름을 알아야 합니다. Azure DNS는 영역이 만들어질 때마다 풀에서 이름 서버를 할당합니다.

영역에 할당된 이름 서버를 확인하는 가장 쉬운 방법은 Azure 포털을 통해서 확인하는 것입니다. 이 예제에서는 'contoso.com' 영역에 이름 서버 'ns1-01.azure-dns.com', 'ns2-01.azure-dns.net', 'ns3-01.azure-dns.org', 'ns4-01.azure-dns.info'가 할당되었습니다.

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS는 할당된 이름 서버를 포함하는 영역에 권한이 있는 NS 레코드를 자동으로 만듭니다. Azure PowerShell 또는 Azure CLI를 통해 이름 서버 이름을 확인하려면 이러한 레코드만 검색하면 됩니다.

Azure PowerShell을 사용하여 다음과 같이 권한 있는 NS 레코드를 검색할 수 있습니다. 레코드 이름 "@"은 영역 루트에 있는 레코드를 가리키는 데 사용합니다.

	PS> $zone = Get-AzureRmDnsZone –Name contoso.net –ResourceGroupName MyResourceGroup
	PS> Get-AzureRmDnsRecordSet –Name “@” –RecordType NS –Zone $zone

	Name              : @
	ZoneName          : contoso.net
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                        ns4-01.azure-dns.info}
	Tags              : {}

또한 권한이 있는 NS 레코드를 검색하는 데 크로스 플랫폼 Azure CLI를 사용할 수 있으므로 영역에 할당된 이름 서버를 검색할 수 있습니다.

	C:\> azure network dns record-set show MyResourceGroup contoso.net @ NS
	info:    Executing command network dns record-set show
		+ Looking up the DNS Record Set "@" of type "NS"
	data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 172800
	data:    NS records
	data:        Name server domain name     : ns1-01.azure-dns.com.
	data:        Name server domain name     : ns2-01.azure-dns.net.
	data:        Name server domain name     : ns3-01.azure-dns.org.
	data:        Name server domain name     : ns4-01.azure-dns.info.
	data:
	info:    network dns record-set show command OK

### 위임을 설정하려면

각 등록 기관에는 도메인에 대한 이름 서버 레코드를 변경하는 자체 DNS 관리 도구가 있습니다. 등록 기관의 DNS 관리 페이지에서 NS 레코드를 편집하고 NS 레코드를 Azure DNS에서 만든 레코드로 바꿉니다.

Azure DNS에 도메인을 위임하는 경우 Azure DNS에서 제공하는 이름 서버 이름을 사용해야 합니다. 도메인 이름에 상관 없이 4개의 이름 서버 이름을 항상 모두 사용해야 합니다. 도메인 위임에는 같은 최상위 도메인을 도메인으로 사용하는 이름 서버 이름이 필요하지 않습니다.

이러한 IP 주소는 나중에 변경될 수 있으므로 Azure DNS 이름 서버 IP 주소를 가리키는 데 '연결 레코드'를 사용하지 않아야 합니다. 고유한 영역에서 이름 서버 이름을 사용하는 위임('베니티 이름 서버'라고도 함)은 현재 Azure DNS에서 지원되지 않습니다.

### 이름 확인이 작동하는지 확인하려면

위임을 완료한 후 'nslookup'과 같은 도구로 영역에 대한 SOA 레코드(영역을 만들 때 자동으로 생성됨)를 쿼리하여 이름 확인이 작동하는지 확인할 수 있습니다.

일반적인 DNS 확인 프로세스는 위임이 올바르게 설정된 경우 자동으로 이름 서버를 찾기 때문에 Azure DNS 이름 서버를 지정할 필요가 없습니다.

	nslookup –type=SOA contoso.com

	Server: ns1-04.azure-dns.com
	Address: 208.76.47.4

	contoso.com
	primary name server = ns1-04.azure-dns.com
	responsible mail addr = msnhst.microsoft.com
	serial = 1
	refresh = 900 (15 mins)
	retry = 300 (5 mins)
	expire = 604800 (7 days)
	default TTL = 300 (5 mins)

## Azure DNS에 하위 도메인 위임

별도의 자식 영역을 설정하려는 경우 Azure DNS에 하위 도메인을 위임할 수 있습니다. 예를 들어 Azure DNS에서 'contoso.com'을 설정하고 위임하면 별도의 자식 영역인 'partners.contoso.com'을 설정하는 경우를 가정합니다.

하위 도메인 설정은 일반 위임과 유사한 프로세스를 따릅니다. 유일한 차이점은 3단계에서 도메인 등록을 통해 설정하는 것이 아니라 Azure DNS ' contoso.com' 부모 영역에 NS 레코드를 만들어야 한다는 점입니다.


1. Azure DNS에서 자식 영역 'partners.contoso.com'을 만듭니다.
2. Azure DNS에서 자식 영역을 호스팅하는 이름 서버를 가져오려면 자식 영역에서 신뢰할 수 있는 NS 레코드를 조회합니다.
3. 자식 영역을 가리키는 부모 영역에 NS 레코드를 구성하여 자식 영역을 위임합니다.


### 하위 도메인을 위임하려면

다음 PowerShell 예는 작동 방식을 보여줍니다. Azure 포털 또는 크로스 플랫폼 Azure CLI를 통해 동일한 단계를 실행할 수 있습니다.

#### 1단계. 부모 및 하위 영역 만들기

가장 먼저 부모 및 하위 영역을 만듭니다. 이러한 영역은 동일하거나 다른 리소스 그룹에 있을 수 있습니다.

	$parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
	$child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1

#### 2단계. NS 레코드 검색

다음으로 아래 예제와 같이 자식 영역에서 신뢰할 수 있는 NS 레코드 검색합니다. 여기에는 자식 영역에 할당된 이름 서버가 포함됩니다.

	$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

#### 3단계. 자식 영역 위임

부모 영역에 해당하는 NS 레코드 집합을 만들어 위임을 완료합니다. 부모 영역의 레코드 집합 이름이 자식 영역 이름과 일치하며 이 경우는 "partners"입니다.

	$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
	$parent_ns_recordset.Records = $child_ns_recordset.Records
	Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset

### 이름 확인이 작동하는지 확인하려면

자식 영역의 SOA 레코드를 조회하여 모든 항목이 올바르게 설정되었는지 확인할 수 있습니다.

	nslookup –type=SOA partners.contoso.com

	Server: ns1-08.azure-dns.com
	Address: 208.76.47.8

	partners.contoso.com
		primary name server = ns1-08.azure-dns.com
		responsible mail addr = msnhst.microsoft.com
		serial = 1
		refresh = 900 (15 mins)
		retry = 300 (5 mins)
		expire = 604800 (7 days)
		default TTL = 300 (5 mins)

## 다음 단계

[DNS 영역 관리](dns-operations-dnszones.md)

[DNS 레코드 관리](dns-operations-recordsets.md)

<!---HONumber=AcomDC_0824_2016-->