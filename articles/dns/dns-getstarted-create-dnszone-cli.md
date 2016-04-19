<properties
   pageTitle="CLI를 사용한 Azure DNS 시작 | Microsoft Azure"
   description="Azure DNS에 대한 DNS 영역을 만들고 CLI를 사용하여 DNS 도메인 호스팅을 시작하는 방법을 단계별로 알아봅니다."
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma"/>

# CLI를 사용한 Azure DNS 시작


> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [PowerShell](dns-getstarted-create-dnszone.md)


'contoso.com' 도메인은 'mail.contoso.com'(메일 서버) 및 'www.contoso.com'(웹 사이트)과 같은 많은 DNS 레코드를 포함할 수 있습니다. DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다.

도메인 호스팅을 시작하려면 먼저 DNS 영역을 만들어야 합니다. 특정 도메인에 대해 만든 DNS 레코드는 모두 해당 도메인에 대한 DNS 영역 내에 있습니다.

이 지침은 Microsoft Azure CLI를 사용합니다. Azure DNS 명령을 사용하려면 최신 Azure CLI로 업데이트해야 합니다.

## Azure CLI 설치

### 1단계

Azure CLI를 설치합니다. Windows, Linux 또는 MAC용 Azure CLI를 설치할 수 있습니다. Azure CLI를 사용하여 Azure DNS를 관리하려면 먼저 다음 단계를 완료해야 합니다. 자세한 내용은 [Azure CLI 설치](../xplat-cli-install.md)를 참조하세요. CLI에 대한 모든 네트워크 공급자 명령은 다음 명령을 사용하여 찾을 수 있습니다.

	Azure network


>[AZURE.IMPORTANT] DNS 명령에는 Azure CLI 버전 0.9.8 이상이 필요합니다. `azure -v`를 입력하여 현재 컴퓨터에 설치되어 있는 Azure CLI 버전을 확인합니다.

### 2단계

Azure DNS는 Azure 리소스 관리자를 사용합니다. arm 명령 및 DNS를 사용하려면 CLI를 전환해야 합니다.

	Azure config mode arm

### 3단계

Azure 계정에 로그인합니다.

    Azure login -u "username"

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다. ORGID 계정만을 사용할 수 있습니다.

### 4단계
사용할 Azure 구독을 선택합니다.

    Azure account set "subscription name"

사용 가능한 구독 목록을 보려면 "azure account list" 명령을 사용합니다.

### 5단계

새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    Azure group create -n myresourcegroup --location "West US"

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 그러나 모든 DNS 리소스는 국가별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure DNS에 영향을 주지 않습니다.

### 6단계

Azure DNS 서비스는 Microsoft.Network 리소스 공급자에 의해 관리됩니다. Azure DNS를 사용하려면 먼저 이 리소스 공급자를 사용하도록 Azure 구독을 등록해야 합니다. 이 작업은 각 구독에 대해 한 번만 수행하면 됩니다.

	Azure provider register --namespace Microsoft.Network

## 태그

태그는 Etag와 다릅니다. 태그는 이름-값 쌍의 목록으로, Azure 리소스 관리자에서 대금 청구 또는 그룹화를 위해 리소스에 레이블을 지정하는 데 사용됩니다. 태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../resource-group-using-tags.md)을 참조하십시오. Azure DNS CLI는 옵션 '-Tag ' 매개 변수를 사용하여 지정된 영역 및 레코드 집합 양쪽에서 태그를 지원합니다. 다음 예제에서는 두 태그 ‘project = demo’ and ‘env = test’를 사용하여 DNS 영역을 만드는 방법을 보여 줍니다.

	Azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

## DNS 영역 만들기

`azure network dns zone create` 명령을 사용하여 DNS 영역을 만듭니다. 아래 예제에서는 'MyResourceGroup'이라는 리소스 그룹에 'contoso.com'이라는 DNS 영역을 만듭니다.

    Azure network dns zone create -n contoso.com -g myresourcegroup


>[AZURE.NOTE] Azure DNS에서는 종료하는 '.' 없이 영역 이름을 지정해야 합니다. 예를 들어 'contoso.com.' 대신 'contoso.com'으로 지정합니다.


이제 Azure DNS에 DNS 영역이 생성되었습니다. DNS 영역을 만들면 다음과 같은 DNS 레코드도 생성됩니다.

- 'SOA(권한 시작)' 레코드. 각 DNS 영역의 루트에 있습니다.
- 권한이 있는 NS(이름 서버) 레코드. 영역을 호스트하는 이름 서버를 보여 줍니다. Azure DNS는 이름 서버 풀을 사용하므로 Azure DNS의 각 영역에 다른 이름 서버가 할당될 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

이러한 레코드를 보려면 "azure network dns-record-set show" 명령을 사용합니다.

	Usage: network dns record-set show <resource-group> <dns-zone-name> <name> <type>


아래 예제에서 "myresourcegroup" 리소스 그룹, "@" 레코드 집합 이름(루트 레코드인 경우) 및 "SOA" 형식으로 명령을 실행하면 다음과 같은 결과가 생성됩니다.


	azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR> 생성된 NS 레코드를 보려면 다음 명령을 사용합니다.

	azure network dns record-set show myresourcegroup "contoso.com" "@" NS
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE] DNS 영역의 루트에 있는 레코드 집합은 레코드 집합 이름으로 "@"를 사용합니다.

첫 번째 DNS 영역을 만들었으므로 nslookup, DIG 또는 **Resolve-DnsName** PowerShell cmdlet과 같은 DNS 도구를 사용하여 테스트할 수 있습니다. Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내야 합니다. 사용자의 영역에 대한 이름 서버는 "azure network dns-record-set show" 명령에 의해 나열된 것처럼 NS 레코드에 제공됩니다. 아래 명령을 사용자 영역의 올바른 값으로 대체해야 합니다.

다음 예에서는 DIG를 사용하여 DNS 영역에 할당된 이름 서버를 사용하는 도메인 contoso.com을 쿼리합니다. 쿼리는 DIG를 사용하여 영역 이름 및 `@<name server for the zone>`을 사용한 이름 서버를 가리켜야 합니다.

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120

## 다음 단계

DNS 영역을 만든 후에는 [레코드 집합 및 레코드](dns-getstarted-create-recordset-cli.md)를 만들어 인터넷 도메인에 대한 이름 확인을 시작해야 합니다.<BR> [DNS 영역을 관리하는 방법](dns-operations-dnszones-cli.md) 및 해당 DNS 영역 작업도 학습할 수 있습니다.<BR> [DNS 레코드를 관리하는 방법](dns-operations-recordsets-cli.md) 및 [.NET SDK로 Azure 작업 자동화](dns-sdk.md)<BR> [Azure DNS REST API 참조](https://msdn.microsoft.com/library/azure/mt163862.aspx)에 대해 알아봅니다.

<!---HONumber=AcomDC_0413_2016-->