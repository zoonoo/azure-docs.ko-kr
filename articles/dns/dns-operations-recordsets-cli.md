<properties 
   pageTitle="CLI를 사용하여 Azure DNS의 DNS 레코드 집합 및 레코드 관리 | Microsoft Azure" 
   description="Azure DNS에서 도메인을 호스트하는 경우 Azure DNS에서 DNS 레코드 집합 및 레코드를 관리합니다. 레코드 집합 및 레코드 작업에 대한 모든 CLI 명령입니다." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# CLI를 사용하여 DNS 레코드를 관리하는 방법

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [Azure Powershell](dns-operations-recordsets.md)

이 가이드에서는 DNS 영역에 대한 레코드 집합 및 레코드를 관리하는 방법을 보여 줍니다.

DNS 레코드 집합과 개별 DNS 레코드의 차이점을 이해하는 것이 중요합니다. 레코드 집합은 영역 내에서 동일한 이름과 형식을 가진 레코드 컬렉션입니다. 자세한 내용은 [레코드 집합 및 레코드 이해](dns-getstarted-create-recordset.md#Understanding-record-sets-and-records)를 참조하세요.

## 레코드 집합 만들기

`azure network dns record-set create` 명령을 사용하여 레코드 집합을 만듭니다. 레코드 집합 이름, 영역, TTL(Time-to-Live) 및 레코드 형식을 지정해야 합니다.

>[AZURE.NOTE]레코드 집합 이름은 영역 이름을 제외한 상대 이름이어야 합니다. 예를 들어 'contoso.com' 영역의 레코드 집합 이름 'www'는 정규화된 이름 'www.contoso.com'을 가진 레코드 집합을 만듭니다.

>영역 구로의 레코드 집합의 경우, 따옴표를 포함하여 "@"를 레코드 집합 이름으로 사용합니다. 완전한 레코드 집합의 이름은 영역 이름과 동일하며 이 경우 "contoso.com"입니다.

Azure DNS는 A, AAAA, CNAME, MX, NS, SOA, SRV, TXT 등의 레코드 형식을 지원합니다. SOA 형식의 레코드 집합은 각 영역과 함께 자동으로 생성되며 별도로 만들 수 없습니다. [SPF 레코드 유형은 TXT 레코드 유형을 사용하여 SPF 레코드를 만드는데 유리한 DNS 표준에 의해 사용되지 않습니다](http://tools.ietf.org/html/rfc7208#section-3.1).

	azure network dns record-set create myresourcegroup contoso.com  www  A --ttl 300


>[AZURE.IMPORTANT]CNAME 레코드 집합은 동일한 이름의 다른 레코드 집합과 함께 존재할 수 없습니다. 예를 들어 상대 이름이 'www'인 CNAME과 상대 이름이 'www'인 A 레코드를 동시에 만들 수 없습니다. 영역 루트(이름 = '@')는 항상 영역을 만들 때 생성된 NS 및 SOA 레코드 집합을 포함하므로 영역 루트에 CNAME 레코드 집합을 만들 수 없습니다. 이러한 제약 조건은 DNS 표준에서 발생하며 Azure DNS의 제한 사항이 아닙니다.

### 와일드 카드 레코드

Azure DNS는 [와일드 카드 레코드](https://en.wikipedia.org/wiki/Wildcard_DNS_record)를 지원합니다. 이러한 레코드는 일치하는 이름이 있는 모든 쿼리에 대해 반환됩니다(비 와일드 카드 레코드 집합에 더 일치하는 항목이 없는 한).

>[AZURE.NOTE]와일드 카드 레코드 집합을 만들려면 "*"라는 레코드 집합 이름을 사용하거나 첫 번째 레이블의 이름이 "*", 예: "*.foo"인 이름을 사용합니다.

>와일드 카드 레코드 집합은 NS 및 SOA를 제외한 모든 레코드 유형에 대해 지원됩니다.

## 레코드 집합 가져오기
기존 레코드 집합을 가져오려면 리소스 그룹, 영역 이름, 레코드 집합 상대 이름 및 레코드 형식을 지정하여 `azure network dns record-set show`를 사용합니다.

	azure network dns record-set show myresourcegroup contoso.com www A


## 레코드 집합 나열

`azure network dns record-set list` 명령을 사용하여 DNS 영역에 있는 모든 레코드를 나열할 수 있습니다.

### 옵션 1 
모든 레코드 집합을 나열합니다. 이름 또는 레코드 형식에 관계없이 모든 레코드 집합을 반환합니다.

	azure network dns record-set list myresourcegroup contoso.com

### 옵션 2 

지정된 레코드 형식의 레코드 집합을 나열합니다. 지정된 레코드 형식(이 경우 A 레코드)과 일치하는 모든 레코드 집합을 반환합니다.


	azure network dns record-set list myresourcegroup contoso.com A 

두 경우 모두 리소스 그룹 이름 및 영역 이름을 지정합니다.

## 레코드 집합에 레코드 추가

`azure network dns record-set add-record`를 사용하여 레코드 집합에 레코드를 추가합니다.

레코드 집합에 레코드를 추가하기 위한 매개 변수는 레코드 집합 형식에 따라 달라집니다. 예를 들어 'A' 형식의 레코드 집합을 사용하는 경우 "-a `<IPv4 address>`" 매개 변수를 통해서만 레코드를 지정할 수 있습니다.

다음 예제에서는 단일 레코드를 포함하는 각 레코드 형식의 레코드 집합을 만드는 방법을 보여 줍니다.

### 단일 레코드를 포함하는 A 레코드 집합 만들기

레코드 집합을 만들려면 리소스 그룹, 영역 이름, 레코드 집합 상대 이름, 레코드 형식 및 TTL(Time to Live)을 지정하여 `azure network dns record-set create`을 사용합니다.
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

>[AZURE.NOTE]--ttl 매개 변수가 정의되지 않은 경우 기본값은 4(초)입니다.


A 레코드 집합을 만든 후에 `azure network dns record-set add-record`로 레코드 집합에 IPv4 주소를 추가합니다.

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 

### 단일 레코드를 포함하는 AAAA 레코드 집합 만들기

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

### 단일 레코드를 포함하는 CNAME 레코드 집합 만들기

	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300
	
	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"

>[AZURE.NOTE]CNAME 레코드에는 하나의 단일 문자열 값만 허용됩니다.

### 단일 레코드를 포함하는 MX 레코드 집합 만들기

이 예제에서는 레코드 집합 이름을 "@"로 사용하여 영역 구로에 MX 레코드를 만듭니다(예: "contoso.com"). MX 레코드에 공통됩니다.

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


### 단일 레코드를 포함하는 NS 레코드 집합 만들기

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300
	
	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com" 
	
### 단일 레코드를 포함하는 SRV 레코드 집합 만들기

영역 루트에 SRV 레코드를 만드는 경우 레코드 이름에 \_service 및 \_protocol만 지정하면 됩니다. 레코드 이름에 '.@'도 포함할 필요가 없습니다.

	
	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300 

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com" 

### 단일 레코드를 포함하는 TXT 레코드 집합 만들기

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record" 


## 기존 레코드 집합 수정


이 내용은 다음 예제에 나와 있습니다.

### 기존 레코드 집합의 레코드 업데이트

이 예제에서는 기존 A 레코드 집합에 다른 IP 주소(1.2.3.4)를 추가합니다.

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


`azure network dns record-set delete-record`를 사용하여 레코드 집합에서 기존 값을 제거합니다.
 
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
	data:        IPv4 address                : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## 기존 레코드 집합에서 레코드 제거

`azure network dns record-set delete-record`를 사용하여 레코드 집합에서 레코드를 제거할 수 있습니다. 제거되는 레코드는 모든 매개 변수에서 기존 레코드와 정확히 일치해야 합니다.

레코드 집합에서 마지막 레코드를 제거해도 레코드 집합은 삭제되지 않습니다. 자세한 내용은 아래의 [레코드 집합 삭제](#delete-a-record-set)를 참조하세요.


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

### 레코드 집합에서 SRV 레코드 제거

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### 레코드 집합에서 TXT 레코드 제거

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## 레코드 집합 삭제
Remove-AzureDnsRecordSet cmdlet을 사용하여 레코드 집합을 삭제할 수 있습니다.

>[AZURE.NOTE]영역을 만들 때 자동으로 생성된 영역 루트(이름 = '@')의 SOA 및 NS 레코드 집합은 삭제할 수 없습니다. 영역을 삭제하면 자동으로 삭제됩니다.

다음 예제에서는 "test-a" 레코드 집합이 DNS 영역에서 제거됩니다.

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

선택적 '-q' 스위치를 사용하여 확인 메시지가 표시되지 않도록 할 수 있습니다.


##참고 항목

[레코드 집합 및 레코드 만들기 시작](dns-getstarted-create-recordset-cli.md)<BR> [DNS 영역에 대한 작업 수행](dns-operations-dnszones-cli.md)<BR> [.NET SDK로 작업 자동화](dns-sdk.md)
 

<!---HONumber=Oct15_HO3-->