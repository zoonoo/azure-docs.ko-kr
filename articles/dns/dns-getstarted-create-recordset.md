<properties
   pageTitle="PowerShell을 사용하여 DNS 영역에 대한 레코드 집합 및 레코드 만들기 | Microsoft Azure"
   description="Azure DNS에 대한 호스트 레코드를 만드는 방법입니다. PowerShell을 사용하여 레코드 집합 및 레코드를 설정합니다."
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
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# PowerShell을 사용하여 DNS 레코드 집합 및 레코드 만들기


> [AZURE.SELECTOR]
- [Azure 포털](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)

이 문서는 PowerShell을 사용하여 레코드 및 레코드 집합을 만드는 방법을 안내합니다. DNS 영역을 만든 후 도메인에 대한 DNS 레코드를 추가해야 합니다. 이 작업을 수행하려면 먼저 DNS 레코드 및 레코드 집합을 이해해야 합니다.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## 시작하기 전에

최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치했는지 확인합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## 레코드 집합 및 레코드 만들기

이 섹션에서는 레코드 집합 및 레코드를 만드는 방법을 보여 줍니다.


### 1\. 구독에 연결 

PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

	Login-AzureRmAccount

계정에 대한 구독을 확인합니다.

	Get-AzureRmSubscription 

사용할 구독을 지정합니다.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

PowerShell 작업에 대한 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.


### 2\. 레코드 집합 만들기

`New-AzureRmDnsRecordSet` cmdlet을 사용하여 레코드 집합을 만듭니다. 레코드 집합을 만들 때, 레코드 집합 이름, 영역, TTL(Time-to-Live) 및 레코드 형식을 지정해야 합니다.

영역의 구로에서 레코드 집합을 만들려면(이 경우 'contoso.com'), 따옴표를 포함한 레코드 이름 "@"를 사용합니다. 이것이 일반적인 DNS 규칙입니다.

아래 예제에서는 DNS 영역 *contoso.com*에 상대적 이름 *www*가 포함된 레코드 집합을 만듭니다. 레코드의 정규화된 이름은 *www.contoso.com*이고, 레코드 유형은 *A*이고, TTL은 60초입니다. 이 단계를 완료하면 *$rs* 변수에 할당된 빈 *www* 레코드 집합이 생성됩니다.

	$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### 레코드 집합이 이미 있으면

레코드 집합이 존재하는 경우 *-Overwrite* 스위치를 사용하지 않으면 명령이 실패합니다. *-Overwrite* 옵션은 확인 프롬프트를 트리거하며, *-Force* 스위치를 사용하여 표시되지 않도록 할 수 있습니다.


	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


위의 예제에서는 영역 이름 및 리소스 그룹 이름을 사용하여 영역을 지정합니다. 아니면, `Get-AzureRmDnsZone` 또는 `New-AzureRmDnsZone`에서 반환되는 영역 개체를 지정할 수 있습니다.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet`는 Azure DNS에 생성된 레코드 집합을 나타내는 로컬 개체를 반환합니다.

### 3\. 레코드 추가

새로 생성된 *www* 레코드 집합을 사용하려면 여기에 레코드를 추가해야 합니다. 아래 예제를 사용하여 *www* 레코드 집합에 IPv4 *A* 레코드를 추가할 수 있습니다. 이 예제는 이전 단계에서 설정한 변수 즉, $rs에 의존합니다.

`Add-AzureRmDnsRecordConfig`를 사용하여 레코드 집합에 레코드를 추가하는 작업은 오프라인 작업입니다. 지역 변수 *$rs*만 업데이트됩니다.


	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### 4\. 변경 내용 커밋

레코드 집합의 변경 내용을 커밋합니다. `Set-AzureRmDnsRecordSet`를 사용하여 레코드 집합의 변경 내용을 Azure DNS로 업로드합니다.

	Set-AzureRmDnsRecordSet -RecordSet $rs

### 5\. 레코드 집합 검색

다음 예제와 같이, `Get-AzureRmDnsRecordSet`를 사용하여 Azure DNS에서 레코드 집합을 검색할 수 있습니다.


	Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}


nslookup 또는 다른 DNS 도구를 사용하여 새 레코드 집합을 쿼리할 수도 있습니다.

Azure DNS 이름 서버에 도메인을 아직 위임하지 않은 경우 영역에 대한 이름 서버 주소를 명시적으로 지정해야 합니다.


	nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221

## 추가 레코드 형식 예제


다음 예제는 단일 레코드가 포함된 각 레코드 형식의 레코드 집합을 만드는 방법을 보여 줍니다.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## 다음 단계

[DNS 영역을 관리하는 방법](dns-operations-dnszones.md)

[DNS 레코드를 관리하는 방법](dns-operations-recordsets.md)

[.NET SDK로 Azure 작업 자동화](dns-sdk.md)
 

<!---HONumber=AcomDC_0518_2016-->