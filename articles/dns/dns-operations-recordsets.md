<properties 
   pageTitle="Azure DNS에서 DNS 레코드 집합 및 레코드 관리 | Microsoft Azure" 
   description="Azure DNS에서 도메인을 호스트하는 경우 Azure DNS에서 DNS 레코드 집합 및 레코드를 관리합니다. 레코드 집합 및 레코드 작업에 대한 모든 PowerShell 명령입니다." 
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
   ms.date="05/01/2015"
   ms.author="joaoma"/>

# DNS 레코드를 관리하는 방법

이 가이드에서는 DNS 영역에 대한 레코드 집합 및 레코드를 관리하는 방법을 보여 줍니다.

DNS 레코드 집합과 개별 DNS 레코드의 차이점을 이해하는 것이 중요합니다. 레코드 집합은 영역 내에서 동일한 이름과 형식을 가진 레코드 컬렉션입니다. 자세한 내용은 [레코드 집합 및 레코드 이해](../dns-getstarted-create-recordset#Understanding-record-sets-and-records)를 참조하세요.

## 레코드 집합 만들기

New-AzureDnsRecordSet cmdlet을 사용하여 레코드 집합을 만듭니다. 레코드 집합 이름, 영역, TTL(Time-to-Live) 및 레코드 형식을 지정해야 합니다.

>[AZURE.NOTE]레코드 집합 이름은 영역 이름을 제외한 상대 이름이어야 합니다. 예를 들어 'contoso.com' 영역의 레코드 집합 이름 'www'는 정규화된 이름 'www.contoso.com'을 가진 레코드 집합을 만듭니다.

>영역 구로의 레코드 집합의 경우, 따옴표를 포함하여 "@"를 레코드 집합 이름으로 사용합니다. 완전한 레코드 집합의 이름은 영역 이름과 동일하며 이 경우 "contoso.com"입니다.

Azure DNS는 A, AAAA, CNAME, MX, NS, SOA, SRV, TXT 등의 레코드 형식을 지원합니다. SOA 형식의 레코드 집합은 각 영역과 함께 자동으로 생성되며 별도로 만들 수 없습니다.

	PS C:\> $rs = New-AzureDnsRecordSet -Name www -Zone $zone -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

레코드 집합이 존재하는 경우 -Overwrite 스위치를 사용하지 않으면 명령이 실패합니다. '-Overwrite' 옵션은 확인 프롬프트를 트리거하며, -Force 스위치를 사용하여 표시되지 않도록 할 수 있습니다.

위 예제에서는 AzureDnsZone 또는 New-AzureDnsZone에서 반환된 영역 개체를 사용하여 영역을 지정합니다. 또는 영역 이름 및 리소스 그룹 이름으로 영역을 지정할 수도 있습니다.

	PS C:\> $rs = New-AzureDnsRecordSet -Name www –ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

New-AzureDnsRecordSet는 Azure DNS에 생성된 레코드 집합을 나타내는 로컬 개체를 반환합니다.

>[AZURE.NOTE]CNAME 레코드 집합은 동일한 이름의 다른 레코드 집합과 함께 존재할 수 없습니다. 예를 들어 상대 이름이 'www'인 CNAME과 상대 이름이 'www'인 A 레코드를 동시에 만들 수 없습니다. 영역 루트(이름 = '@')는 항상 영역을 만들 때 생성된 NS 및 SOA 레코드 집합을 포함하므로 영역 루트에 CNAME 레코드 집합을 만들 수 없습니다. 이러한 제약 조건은 DNS 표준에서 발생하며 Azure DNS의 제한 사항이 아닙니다.

### 와일드 카드 레코드
Azure DNS는 [와일드 카드 레코드](https://en.wikipedia.org/wiki/Wildcard_DNS_record)를 지원합니다. 이러한 레코드는 일치하는 이름이 있는 모든 쿼리에 대해 반환됩니다(비 와일드 카드 레코드 집합에 더 일치하는 항목이 없는 한).

>[AZURE.NOTE]와일드 카드 레코드 집합을 만들려면 "*"라는 레코드 집합 이름을 사용하거나 첫 번째 레이블의 이름이 "*", 예: "*.foo"인 이름을 사용합니다.

>와일드 카드 레코드 집합은 NS 및 SOA를 제외한 모든 레코드 유형에 대해 지원됩니다.

## 레코드 집합 가져오기
기존 레코드 집합을 가져오려면 레코드 집합 상대 이름, 레코드 형식 및 영역을 지정하여 'Get-AzureDnsRecordSet'를 사용합니다.

	PS C:\> $rs = Get-AzureDnsRecordSet -Name www –RecordType A -Zone $zone

New-AzureDnsRecordSet와 마찬가지로, 레코드 이름은 영역 이름을 제외한 상대 이름이어야 합니다. 위와 같이 영역 개체를 사용하거나 영역 이름 및 리소스 그룹 이름으로 영역을 지정할 수 있습니다.

	PS C:\> $rs = Get-AzureDnsRecordSet –Name www –RecordType A -Zonename contoso.com -ResourceGroupName MyAzureResourceGroup

Get-AzureDnsRecordSet는 Azure DNS에 생성된 레코드 집합을 나타내는 로컬 개체를 반환합니다.

## 레코드 집합 나열
\-Name 및/또는 -RecordType 매개 변수를 생략하면 Get-AzureDnsRecordSet를 사용하여 레코드 집합을 나열할 수도 있습니다.

### 옵션 1 
모든 레코드 집합을 나열합니다. 이름 또는 레코드 형식에 관계없이 모든 레코드 집합을 반환합니다.

	PS C:\> $list = Get-AzureDnsRecordSet -Zone $zone
### 옵션 2 

지정된 레코드 형식의 레코드 집합을 나열합니다. 지정된 레코드 형식(이 경우 A 레코드)과 일치하는 모든 레코드 집합을 반환합니다.

	PS C:\> $list = Get-AzureDnsRecordSet –RecordType A -Zone $zone 

위의 두 경우 모두, 표시된 것처럼 영역 개체를 사용하거나 -ZoneName 및 -ResourceGroupName 매개 변수를 지정하여 영역을 지정할 수 있습니다.

## 레코드 집합에 레코드 추가
Add-AzureDnsRecordConfig cmdlet을 사용하여 레코드 집합에 레코드를 추가합니다. 이 작업은 오프라인 작업이므로 레코드 집합을 나타내는 로컬 개체만 변경됩니다.

레코드 집합에 레코드를 추가하기 위한 매개 변수는 레코드 집합 형식에 따라 달라집니다. 예를 들어 'A' 형식의 레코드 집합을 사용하는 경우 'IPv4Address' 매개 변수를 통해서만 레코드를 지정할 수 있습니다.

Add-AzureDnsRecordConfig를 추가로 호출하여 각 레코드 집합에 레코드를 추가할 수 있습니다. 레코드 집합에 최대 100개의 레코드를 추가할 수 있습니다. 그러나 CNAME 형식의 레코드 집합은 최대 1개의 레코드를 포함할 수 있으며 레코드 집합에 두 개의 동일한 레코드가 포함될 수 없습니다. 빈 레코드 집합(0개 레코드 포함)을 만들 수 있지만 Azure DNS 이름 서버에는 표시되지 않습니다.

레코드 집합에 원하는 레코드 컬렉션을 포함한 후에는 Set-AzureDnsRecordSet cmdlet을 사용하여 커밋해야 합니다. 이 cmdlet은 Azure DNS의 기존 레코드 집합을 제공된 레코드 집합으로 바꿉니다. 다음 예제에서는 단일 레코드를 포함하는 각 레코드 형식의 레코드 집합을 만드는 방법을 보여 줍니다.

### 단일 레코드를 포함하는 A 레코드 집합 만들기

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

레코드 집합 개체를 매개 변수로 전달하는 대신 파이프를 통해 전달하여 레코드를 만드는 작업 시퀀스를 '파이프'할 수도 있습니다. 예:

	PS C:\> New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60 | Add-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet

### 단일 레코드를 포함하는 AAAA 레코드 집합 만들기

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 단일 레코드를 포함하는 CNAME 레코드 집합 만들기

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-cname" -RecordType CNAME -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 단일 레코드를 포함하는 MX 레코드 집합 만들기
이 예제에서는 레코드 집합 이름을 "@"로 사용하여 영역 구로에 MX 레코드를 만듭니다(예: "contoso.com"). MX 레코드에 공통됩니다.

	PS C:\> $rs = New-AzureDnsRecordSet -Name "@" -RecordType MX -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 단일 레코드를 포함하는 NS 레코드 집합 만들기

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs
### 단일 레코드를 포함하는 SRV 레코드 집합 만들기

영역 루트에 SRV 레코드를 만드는 경우 레코드 이름에 \_service 및 \_protocol만 지정하면 됩니다. 레코드 이름에 '.@'도 포함할 필요가 없습니다.

	PS C:\> $rs = New-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 단일 레코드를 포함하는 TXT 레코드 집합 만들기

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

## 기존 레코드 집합 수정
기존 레코드 집합을 수정하는 경우 레코드를 만드는 것과 유사한 패턴을 따릅니다. 작업 시퀀스는 다음과 같습니다.

1.	Get-AzureDnsRecordSet를 사용하여 기존 레코드 집합을 가져옵니다.
2.	레코드를 추가하거나, 레코드를 제거하거나, 레코드 매개 변수를 변경하거나, 레코드 집합 TTL을 변경하여 레코드 집합을 수정합니다. 이러한 변경은 오프라인 작업이므로 레코드 집합을 나타내는 로컬 개체만 변경됩니다.
3.	Set-AzureDnsRecordSet cmdlet을 사용하여 변경 내용을 커밋합니다. 그러면 Azure DNS의 기존 레코드 집합이 제공된 레코드 집합으로 바뀝니다.

이 내용은 다음 예제에 나와 있습니다.

### 기존 레코드 집합의 레코드 업데이트
이 예제에서는 기존 A 레코드의 IP 주소를 변경합니다.

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-a" -RecordType A -Zone $zone 
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

Set-AzureDnsRecordSet cmdlet은 'Etag' 검사를 사용하여 동시 변경 내용을 덮어쓰지 않도록 합니다. '-Overwrite' 플래그를 사용하면 이러한 검사가 무시됩니다. 자세한 내용은 Etag 및 태그를 참조하세요.

### SOA 레코드 수정

>[AZURE.NOTE]영역 루트(이름 = '@')에 자동으로 생성된 SOA 레코드 집합에서 레코드를 추가하거나 제거할 수는 없지만 SOA 레코드 내의 매개 변수 및 레코드 집합 TTL을 수정할 수 있습니다.

다음 예제에서는 SOA 레코드의 'Email' 속성을 변경하는 방법을 보여 줍니다.

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType SOA -Zone $zone
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

### 영역 루트의 NS 레코드 수정

>[AZURE.NOTE]영역 루트(이름 = '@')에 자동으로 생성된 NS 레코드 집합에서 레코드를 추가, 제거 또는 수정할 수는 없습니다. 레코드 집합 TTL을 수정하는 변경 작업만 허용됩니다.

다음 예제에서는 NS 레코드 집합의 TTL 속성을 변경하는 방법을 보여 줍니다.

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType NS -Zone $zone
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

### 기존 레코드 집합에 레코드 추가
이 예제에서는 기존 레코드 집합에 두 개의 MX 레코드를 더 추가합니다.

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType MX -Zone $zone
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

## 기존 레코드 집합에서 레코드 제거

Remove-AzureDnsRecordConfig를 사용하여 레코드 집합에서 레코드를 제거할 수 있습니다. 제거되는 레코드는 모든 매개 변수에서 기존 레코드와 정확히 일치해야 합니다. Set-AzureDnsRecordSet를 사용하여 변경 내용을 커밋해야 합니다.

레코드 집합에서 마지막 레코드를 제거해도 레코드 집합은 삭제되지 않습니다. 자세한 내용은 아래의 [레코드 집합 삭제](#delete-a-record-set)를 참조하세요.


	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-a" -RecordType A –Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

레코드 집합 개체를 매개 변수로 전달하는 대신 파이프를 통해 전달하여 레코드 집합에서 레코드를 제거하는 작업 시퀀스를 '파이프'할 수도 있습니다. 예:

	PS C:\> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet
### 레코드 집합에서 AAAA 레코드 제거

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA –Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 레코드 집합에서 CNAME 레코드 제거

CNAME 레코드 집합은 최대 1개의 레코드를 포함할 수 있으므로 해당 레코드를 제거하면 빈 레코드 집합이 남습니다.

	PS C:\> $rs =  Get-AzureDnsRecordSet -name "test-cname" -RecordType CNAME –Zone $zone	
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 레코드 집합에서 MX 레코드 제거

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType 'MX' –Zone $zone	
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 레코드 집합에서 NS 레코드 제거
	
	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 레코드 집합에서 SRV 레코드 제거

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 레코드 집합에서 TXT 레코드 제거

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

## 레코드 집합 삭제
Remove-AzureDnsRecordSet cmdlet을 사용하여 레코드 집합을 삭제할 수 있습니다.

>[AZURE.NOTE]영역을 만들 때 자동으로 생성된 영역 루트(이름 = '@')의 SOA 및 NS 레코드 집합은 삭제할 수 없습니다. 영역을 삭제하면 자동으로 삭제됩니다.

다음 세 가지 방법 중 하나를 사용하여 레코드 집합을 제거합니다.
### 옵션 1
모든 매개 변수를 이름으로 지정합니다.

	PS C:\> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zonename "contoso.com" -ResourceGroupName MyAzureResourceGroup [-Force]
선택적 '-Force' 스위치를 사용하여 확인 메시지가 표시되지 않도록 할 수 있습니다.

### 옵션 2
이름 및 형식으로 레코드 집합을 지정하고 개체를 통해 영역을 지정합니다.

	PS C:\> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### 옵션 3
개체를 통해 레코드 집합을 지정합니다.

	PS C:\> Remove-AzureDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

개체를 사용하여 레코드 집합을 지정하면 동시 변경 내용을 삭제하지 않도록 'Etag' 검사가 사용됩니다. 선택적 '-Overwrite' 플래그를 사용하면 이러한 검사가 무시됩니다. 자세한 내용은 [Etag 및 태그](../dns-getstarted-create-dnszone#Etags-and-tags)를 참조하세요.

레코드 집합 개체를 매개 변수로 전달하는 대신 파이프할 수도 있습니다.

	PS C:\> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordSet [-Overwrite] [-Force]

##참고 항목

[레코드 집합 및 레코드 만들기 시작](../dns-getstarted-create-recordset)<BR> [DNS 영역에 대한 작업 수행](../dns-operations-dnszones)<BR> [.NET SDK로 작업 자동화](../dns-sdk)
 

<!---HONumber=August15_HO6-->