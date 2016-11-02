<properties
   pageTitle="Azure 포털을 사용하여 DNS 레코드 집합 및 레코드 관리 | Microsoft Azure"
   description="Azure DNS에서 도메인을 호스트하는 경우 Azure DNS에서 DNS 레코드 집합 및 레코드를 관리합니다. 레코드 집합 및 레코드 작업에 대한 모든 PowerShell 명령입니다."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# PowerShell을 사용하여 DNS 레코드 및 레코드 집합 관리



> [AZURE.SELECTOR]
- [Azure 포털](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



이 문서는 Windows PowerShell을 사용하여 DNS 영역에 대한 레코드 집합 및 레코드를 관리하는 방법을 보여 줍니다.

DNS 레코드 집합과 개별 DNS 레코드 사이의 차이를 이해하는 것이 중요합니다. 레코드 집합은 영역 내에서 동일한 이름과 형식을 가진 레코드 컬렉션입니다. 자세한 내용은 [Azure 포털을 사용하여 DNS 레코드 집합 및 레코드 만들기](dns-getstarted-create-recordset-portal.md)를 참조하세요.

레코드 집합과 레코드를 관리하려면 최신 버전의 Azure Resource Manager PowerShell cmdlet이 필요합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요. PowerShell 작업에 대한 자세한 내용은 [Azure Resource Manager에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.



## 새 레코드 집합 및 레코드 만들기

PowerShell을 사용하여 레코드 집합을 만들려면 [PowerShell을 사용하여 DNS 레코드 집합 및 레코드 만들기](dns-getstarted-create-recordset.md)를 참조하세요.

## 레코드 집합 가져오기

기존 레코드 집합을 가져오려면, `Get-AzureRmDnsRecordSet`를 사용합니다. 레코드 집합 상대 이름, 레코드 유형 및 영역을 지정합니다.

	$rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

`New-AzureRmDnsRecordSet`와 마찬가지로, 레코드 이름은 상대 이름이어야 합니다. 즉, 영역 이름을 제외해야 합니다.

영역 이름 및 리소스 그룹 이름을 사용하거나 영역 개체를 사용하여 영역을 지정할 수 있습니다.

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet`는 Azure DNS에 생성된 레코드 집합을 나타내는 로컬 개체를 반환합니다.

## 레코드 집합 나열

*–Name* 및/또는 *–RecordType* 매개 변수를 생략하면, `Get-AzureRmDnsRecordSet`를 사용하여 레코드 집합을 나열할 수도 있습니다.

### 모든 레코드 집합을 나열하려면

이 예제는 이름이나 레코드 유형에 관계없이 모든 레코드 집합을 반환합니다.

	$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### 지정된 레코드 형식의 레코드 집합을 나열하려면

이 예제에서는 지정한 레코드 유형과 일치하는 모든 레코드 집합을 반환합니다. 이 경우에 반환되는 레코드 집합은 "A" 레코드입니다.

	$list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

영역은 *–ZoneName* 및 *–ResourceGroupName* 매개 변수를 사용하거나 영역 개체를 지정하여 지정될 수 있습니다.

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$list = Get-AzureRmDnsRecordSet -Zone $zone

## 레코드 집합에 레코드 추가

`Add-AzureRmDnsRecordConfig` cmdlet을 사용하여 레코드 집합에 레코드를 추가합니다. 이 작업은 오프라인 작업입니다. 레코드 집합을 나타내는 로컬 개체만 변경됩니다.

레코드 집합에 레코드를 추가하기 위한 매개 변수는 레코드 집합 형식에 따라 달라집니다. 예를 들어 “A” 형식의 레코드 집합을 사용하는 경우 *-IPv4Address* 매개 변수를 통해서만 레코드를 지정할 수 있습니다.

`Add-AzureRmDnsRecordConfig`를 추가로 호출하여 각 레코드 집합에 레코드를 추가할 수 있습니다. 레코드 집합에 최대 20개의 레코드를 추가할 수 있습니다. 그러나 “CNAME” 형식의 레코드 집합은 최대 1개의 레코드를 포함할 수 있으며 레코드 집합에 두 개의 동일한 레코드가 포함될 수 없습니다. 빈 레코드 집합(0개 레코드 포함)을 만들 수 있지만 Azure DNS 이름 서버에는 표시되지 않습니다.

레코드 집합에 원하는 레코드 컬렉션이 포함되면, `Set-AzureRmDnsRecordSet` cmdlet을 사용하여 커밋해야 합니다. 레코드 집합이 커밋되면 Azure DNS의 기존 레코드 집합 대신 바뀝니다.

### 단일 레코드로 A 레코드 집합을 만들려면

	$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

레코드를 만드는 작업 시퀀스를 *파이프*할 수도 있습니다. 이것은 레코드 집합 개체를 매개 변수로 전달하는 대신 파이프를 통해 전달하는 것을 의미합니다. 예:

	New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### 추가 레코드 형식 예제

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## 기존 레코드 집합 수정

기존 레코드 집합을 수정하기 위한 단계는 레코드를 만들 때 수행하는 단계와 비슷합니다. 작업 시퀀스는 다음과 같습니다.

1.	`Get-AzureRmDnsRecordSet`을 사용하여 기존 레코드를 가져옵니다.

2.	레코드를 추가하거나, 레코드를 제거하거나, 레코드 매개 변수를 변경하거나, 레코드 집합 TTL(Time to Live)을 변경하여 레코드 집합을 수정합니다. 이 작업은 오프라인 작업입니다. 레코드 집합을 나타내는 로컬 개체만 변경됩니다.

3.	`Set-AzureRmDnsRecordSet` cmdlet을 사용하여 변경 내용을 커밋합니다. 그러면 Azure DNS의 기존 레코드 집합이 바뀝니다.


### 기존 레코드 집합의 레코드를 업데이트하려면

이 예제에서는 기존 “A” 레코드의 IP 주소를 변경합니다.

	$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Ipv4Address = "134.170.185.46"
	Set-AzureRmDnsRecordSet -RecordSet $rs

`Set-AzureRmDnsRecordSet` cmdlet은 etag 검사를 사용하여 동시 변경 내용을 덮어쓰지 않도록 합니다. *-Overwrite* 플래그를 사용하면 이러한 검사가 무시됩니다. 자세한 내용은 [Etag 및 태그 정보](dns-getstarted-create-dnszone.md#tagetag)를 참조하세요.

### SOA 레코드를 수정하려면

영역 루트(이름 = “@”)에 설정된 자동으로 생성된 SOA 레코드 집합에서 레코드를 추가 또는 제거할 수는 없습니다. 그러나 SOA 레코드 내의 매개 변수("Host" 제외) 및 레코드 집합 TTL을 수정할 수 있습니다.

다음 예제에서는 SOA 레코드의 *Email* 속성을 변경하는 방법을 보여 줍니다.

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Email = "admin.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 영역 루트의 NS 레코드를 수정하려면

영역 루트(이름 = “@”)에 자동으로 생성된 NS 레코드 집합에서 레코드를 추가, 제거 또는 수정할 수는 없습니다. 레코드 집합 TTL을 수정하는 변경 작업만 허용됩니다.

다음 예제에서는 NS 레코드 집합의 TTL 속성을 변경하는 방법을 보여 줍니다.

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Ttl = 300
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 기존 레코드 집합에 레코드를 추가하려면

이 예제에서는 기존 레코드 집합에 두 개의 MX 레코드를 더 추가합니다.

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	Set-AzureRmDnsRecordSet -RecordSet $rs

## 기존 레코드 집합에서 레코드 제거

`Remove-AzureRmDnsRecordConfig`를 사용하여 레코드 집합에서 레코드를 제거할 수 있습니다. 제거되는 레코드는 모든 매개 변수가 기존 레코드와 정확히 일치해야 합니다. `Set-AzureRmDnsRecordSet`을 사용하여 변경 내용을 커밋해야 합니다.

레코드 집합에서 마지막 레코드를 제거해도 레코드 집합은 삭제되지 않습니다. 자세한 내용은 아래의 [레코드 집합 삭제](#delete-a-record-set)를 참조하세요.


	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

레코드 집합에서 레코드를 제거하는 작업 시퀀스를 파이프할 수도 있습니다. 이것은 레코드 집합 개체를 매개 변수로 전달하는 대신 파이프를 통해 전달하는 것을 의미합니다. 예:

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### 레코드 집합에서 AAAA 레코드 제거

	$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 레코드 집합에서 CNAME 레코드 제거

CNAME 레코드 집합은 최대 1개의 레코드를 포함할 수 있으므로 해당 레코드를 제거하면 빈 레코드 집합이 남습니다.

	$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 레코드 집합에서 MX 레코드 제거

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 레코드 집합에서 NS 레코드 제거

	$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 레코드 집합에서 SRV 레코드 제거

	$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 레코드 집합에서 TXT 레코드 제거

	$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	Set-AzureRmDnsRecordSet -RecordSet $rs

## 레코드 집합 삭제

`Remove-AzureRmDnsRecordSet` cmdlet을 사용하여 레코드 집합을 삭제할 수 있습니다. 영역을 만들 때 자동으로 생성된 영역 루트(이름 = “@”)의 SOA 및 NS 레코드 집합은 삭제할 수 없습니다. 영역이 삭제되면 자동으로 삭제됩니다.

다음 세 가지 방법 중 하나를 사용하여 레코드 집합을 제거합니다.

### 모든 매개 변수를 이름으로 지정

선택적 *-Force* 스위치를 사용하여 확인 메시지가 표시되지 않도록 할 수 있습니다.

	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### 이름 및 형식으로 레코드 집합을 지정하고 개체를 통해 영역을 지정합니다.

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### 개체를 통해 레코드 집합을 지정합니다.

	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

개체를 사용하여 레코드 집합을 지정하면 동시 변경 내용이 삭제되지 않도록 etag 검사가 활성화됩니다. 선택적 *-Overwrite* 플래그를 사용하면 이러한 검사가 무시됩니다. 자세한 내용은 [Etag 및 태그](dns-getstarted-create-dnszone.md#tagetag)를 참조하세요.

레코드 집합 개체를 매개 변수로 전달하는 대신 파이프할 수도 있습니다.

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## 다음 단계

Azure DNS에 대한 자세한 내용은 [Azure DNS 개요](dns-overview.md)를 참조하세요. DNS 자동화에 대한 자세한 내용은 [.NET SDK를 사용하여 DNS 영역 및 레코드 집합 만들기](dns-sdk.md)를 참조하세요.

역방향 DNS 레코드에 대한 자세한 내용은 [PowerShell을 사용하여 서비스에 대한 역방향 DNS 레코드를 관리하는 방법](dns-reverse-dns-record-operations-ps.md)을 참조하세요.

<!---HONumber=AcomDC_0824_2016-->