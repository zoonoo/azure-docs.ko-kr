<properties
   pageTitle="Azure CLI를 사용하여 Azure DNS의 DNS 레코드 집합 및 레코드 관리 | Microsoft Azure"
   description="Azure DNS에서 도메인을 호스트하는 경우 Azure DNS에서 DNS 레코드 집합 및 레코드를 관리합니다. 레코드 집합 및 레코드 작업에 대한 모든 CLI 명령입니다."
   services="dns"
   documentationCenter="na"
   authors="jtuliani"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# CLI를 사용하여 DNS 레코드 및 레코드 집합 관리


> [AZURE.SELECTOR]
- [Azure 포털](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


이 문서는 플랫폼 간 Azure CLI(명령줄 인터페이스)를 사용하여 DNS 영역에 대한 레코드 집합 및 레코드를 관리하는 방법을 보여 줍니다.

DNS 레코드 집합과 개별 DNS 레코드 사이의 차이를 이해하는 것이 중요합니다. 레코드 집합은 영역 내에서 동일한 이름과 형식을 가진 DNS 레코드 컬렉션입니다. 자세한 내용은 [레코드 집합 및 레코드 이해](dns-getstarted-create-recordset-cli.md)를 참조하세요.


## 플랫폼 간 Azure CLI 구성

Azure DNS는 Azure 리소스 관리자 전용 서비스입니다. Azure 서비스 관리 API가 없습니다. `azure config mode arm` 명령을 사용하여 Azure CLI가 리소스 관리자 모드를 사용하도록 구성되었는지 확인합니다.

**오류: 'dns'가 azure 명령이 아닙니다.**가 표시되면 Azure CLI를 Resource Manager 모드가 아닌 Azure 서비스 관리 모드에서 사용하고 있기 때문입니다.

## 새 레코드 집합 및 레코드 만들기

Azure 포털에서 새 레코드 집합을 만들려면, [레코드 집합 및 레코드 만들기](dns-getstarted-create-recordset-cli.md)를 참조하세요.


## 레코드 집합 가져오기

기존 레코드 집합을 가져오려면, `azure network dns record-set show`를 사용합니다. 리소스 그룹, 영역 이름, 레코드 집합의 상대적 이름, 레코드 형식을 지정합니다. 아래 예제(사용자 고유의 값으로 대체)를 사용합니다.

	azure network dns record-set show myresourcegroup contoso.com www A


## 레코드 집합 나열

`azure network dns record-set list` 명령을 사용하여 DNS 영역에 있는 모든 레코드를 나열할 수 있습니다. 리소스 그룹 이름 및 영역 이름을 지정해야 합니다.

### 모든 레코드 집합을 나열하려면

이 예제는 이름이나 레코드 유형에 관계없이 모든 레코드 집합을 반환합니다.

	azure network dns record-set list myresourcegroup contoso.com

### 지정된 형식의 레코드 집합을 나열하려면

이 예제는 지정된 레코드 형식(이 경우 “A” 레코드)과 일치하는 모든 레코드 집합을 반환합니다.

	azure network dns record-set list myresourcegroup contoso.com A


## 레코드 집합에 레코드 추가

`azure network dns record-set add-record` 명령을 사용하여 레코드 집합에 레코드를 추가합니다. 레코드 집합에 레코드를 추가하기 위한 매개 변수는 설정하는 레코드 집합 형식에 따라 달라집니다. 예를 들어 “A” 형식의 레코드 집합을 사용하는 경우 `-a <IPv4 address>` 매개 변수를 통해서만 레코드를 지정할 수 있습니다.

레코드 집합을 만들려면 `azure network dns record-set create` 명령을 사용합니다. 리소스 그룹, 영역 이름, 레코드 집합의 상대적 이름, 레코드 형식, TTL(Time to Live)을 지정합니다. `--ttl` 매개 변수가 정의되지 않은 경우 기본값은 4(초)입니다.

	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


“A” 레코드 집합을 만든 후에, `azure network dns record-set add-record` 명령을 사용하여 IPv4 주소를 추가합니다.

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


다음 예제에서는 각 레코드 형식의 레코드 집합을 만드는 방법을 보여 줍니다. 각 레코드 집합은 단일 레코드를 포함합니다.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## 레코드 집합의 레코드 업데이트

### 기존 “A” 레코드 집합(“www”)에 다른 IP 주소(1.2.3.4)를 추가하려면

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK

### 레코드 집합에서 기존 값을 제거하려면
`azure network dns record-set delete-record`를 사용합니다.

	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:    IPv4 address                    : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## 레코드 집합에서 레코드 제거

`azure network dns record-set delete-record`를 사용하여 레코드 집합에서 레코드를 제거할 수 있습니다. 제거되는 레코드는 모든 매개 변수가 기존 레코드와 정확히 일치해야 합니다.

레코드 집합에서 마지막 레코드를 제거해도 레코드 집합은 삭제되지 않습니다. 자세한 내용은 이 문서의 [레코드 집합 삭제](#delete) 섹션을 참조하세요.

	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### 레코드 집합에서 AAAA 레코드 제거

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### 레코드 집합에서 CNAME 레코드 제거

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### 레코드 집합에서 MX 레코드 제거

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### 레코드 집합에서 NS 레코드 제거

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### 레코드 집합에서 PTR 레코드 제거
이 경우 'my-arpa-zone.com'은 IP 범위를 나타내는 ARPA 영역을 나타냅니다. 이 영역의 각 PTR 레코드 집합은 IP 범위 내의 IP 주소에 해당합니다.

	azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### 레코드 집합에서 SRV 레코드 제거

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### 레코드 집합에서 TXT 레코드 제거

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>레코드 집합 삭제

`Remove-AzureRmDnsRecordSet` cmdlet을 사용하여 레코드 집합을 삭제할 수 있습니다. 영역을 만들 때 자동으로 생성된 영역 루트(이름 = “@”)의 SOA 및 NS 레코드 집합은 삭제할 수 없습니다. 영역이 삭제되면 자동으로 삭제됩니다.

다음 예제에서는 "A" 레코드 집합 "test-a"가 "contoso.com" DNS 영역에서 제거됩니다.

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

선택적 *-q* 스위치를 사용하여 확인 메시지가 표시되지 않도록 할 수 있습니다.


## 다음 단계

Azure DNS에 대한 자세한 내용은 [Azure DNS 개요](dns-overview.md)를 참조하세요. DNS 자동화에 대한 자세한 내용은 [.NET SDK를 사용하여 DNS 영역 및 레코드 집합 만들기](dns-sdk.md)를 참조하세요.

역방향 DNS 레코드를 작업하려면 [Azure CLI를 사용하여 서비스에 대한 역방향 DNS 레코드를 관리하는 방법](dns-reverse-dns-record-operations-cli.md)을 참조하세요.

<!---HONumber=AcomDC_0928_2016-->