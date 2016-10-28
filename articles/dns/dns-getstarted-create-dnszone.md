<properties
   pageTitle="Azure DNS 시작 | Microsoft Azure"
   description="Azure DNS에 대한 DNS 영역을 만드는 방법을 알아봅니다. PowerShell을 사용하여 DNS 도메인 호스팅을 시작하기 위해 첫 번째 DNS 영역을 만드는 단계별 가이드입니다."
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

# PowerShell을 사용하여 DNS 영역 만들기

> [AZURE.SELECTOR]
- [Azure 포털](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)

이 문서에서는 PowerShell을 사용하여 DNS 영역을 만드는 단계를 안내합니다. CLI 또는 Azure 포털을 사용하여 DNS 영역을 만들 수도 있습니다.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>Etag 및 태그 정보

### <a name="etags"></a>Etag

두 사용자 또는 두 프로세스가 동시에 DNS 레코드 수정을 시도한다고 가정합니다. 어느 쪽이 성공할까요? 그리고 성공한 사용자는 다른 사용자의 변경 내용을 자신이 방금 덮어썼다는 것을 알고 있을까요?

Azure DNS는 Etag를 사용하여 동일한 리소스에 대한 동시 변경을 안전하게 처리합니다. 각 DNS 리소스(영역 또는 레코드 집합)에는 Etag가 연결되어 있습니다. 리소스를 검색할 때마다 해당 Etag도 검색됩니다. 리소스를 업데이트할 때 Azure DNS에서 서버의 Etag가 일치하는지 확인할 수 있도록 Etag를 다시 전달할 수 있습니다. 리소스를 업데이트할 때마다 Etag가 다시 생성되므로 Etag 불일치는 동시 변경이 발생했음을 나타냅니다. 새 리소스를 만들 때도 Etag를 사용하여 리소스가 존재하지 않는지 확인합니다.

기본적으로 Azure DNS PowerShell은 Etag를 사용하여 영역 및 레코드 집합에 대한 동시 변경을 차단합니다. 선택적 *-Overwrite* 스위치를 사용하여 Etag 검사를 무시할 수 있으며, 이 경우 발생한 동시 변경 내용을 덮어쓰게 됩니다.

Azure DNS REST API 수준에서 Etag는 HTTP 헤더를 사용하여 지정됩니다. 해당 동작은 다음 표에 나와 있습니다.

|헤더|동작|
|------|--------|
|없음|PUT 항상 성공(Etag 검사 안 함)|
|If-match <etag>|리소스가 있고 Etag가 일치하는 경우에만 PUT 성공|
|If-match * | 리소스가 있는 경우에만 PUT 성공|
|If-none-match * |	리소스가 없는 경우에만 PUT 성공|

### <a name="tags"></a>태그

태그는 Etag와 다릅니다. 태그는 이름-값 쌍의 목록으로, Azure Resource Manager에서 대금 청구 또는 그룹화를 위해 리소스에 레이블을 지정하는 데 사용됩니다. 태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../resource-group-using-tags.md)을 참조하십시오.

Azure DNS PowerShell은 옵션 `-Tag` 매개 변수를 사용하여 지정된 영역 및 레코드 집합 둘 다에서 태그를 지원합니다.


## 시작하기 전에

구성을 시작하기 전에 다음 항목이 있는지 확인합니다.
	
- Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
	
- 최신 버전(1.0 이상)의 Azure Resource Manager PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## 1단계 - 로그인

PowerShell 콘솔을 열고 계정에 연결합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

연결에 도움이 되도록 다음 샘플을 사용합니다.

	Login-AzureRmAccount

계정에 대한 구독을 확인합니다.

	Get-AzureRmSubscription 

사용할 구독을 지정합니다.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2단계 - 리소스 그룹 만들기

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 그러나 모든 DNS 리소스는 국가별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure DNS에 영향을 주지 않습니다.

기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛸 수 있습니다.

	New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## 3단계 - 등록

Azure DNS 서비스는 Microsoft.Network 리소스 공급자에 의해 관리됩니다. Azure DNS를 사용하려면 먼저 이 리소스 공급자를 사용하도록 Azure 구독을 등록해야 합니다. 이 작업은 각 구독에 대해 한 번만 수행하면 됩니다.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## 4단계 - DNS 영역 만들기

DNS 영역은 `New-AzureRmDnsZone` cmdlet을 사용하여 생성됩니다. 태그를 사용하거나 사용하지 않고 DNS 영역을 만드는 예제가 아래에 있습니다. 태그에 대한 자세한 내용은 이 문서의 [태그](#tags) 섹션을 참조하세요.

>[AZURE.NOTE] Azure DNS에서는 종료하는 **'.'** 없이 영역 이름을 지정해야 합니다. 예를 들어 '**contoso.com.**' 대신 '**contoso.com**'으로 지정합니다.

### DNS 영역을 만들려면

아래 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### 태그를 사용하여 DNS 영역을 만들려면

다음 예제에서는 두 태그 *project = demo* 및 *env = test*를 사용하여 DNS 영역을 만드는 방법을 보여 줍니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## 레코드 보기

DNS 영역을 만들면 다음과 같은 DNS 레코드도 생성됩니다.

- *권한 시작*(SOA) 레코드입니다. 각 DNS 영역의 루트에 있습니다.

- 권한이 있는 NS(이름 서버) 레코드. 영역을 호스트하는 이름 서버를 보여 줍니다. Azure DNS는 이름 서버 풀을 사용하므로 Azure DNS의 각 영역에 다른 이름 서버가 할당될 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

이러한 레코드를 보려면 다음을 사용 `Get-AzureRmDnsRecordSet`:

	Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
	RecordType        : SOA
	Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
	Tags              : {}

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
	Tags              : {}


DNS 영역의 루트(또는 *apex*)에 있는 레코드 집합은 레코드 집합 이름으로 **@**를 사용합니다.


## 테스트

nslookup, dig 또는 [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)과 같은 DNS 도구를 사용하여 DNS 영역을 테스트할 수 있습니다.

Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내야 합니다. 사용자의 영역에 대한 이름 서버는 `Get-AzureRmDnsRecordSet`을 통해 나열된 것처럼 NS 레코드에 제공됩니다. 아래 명령을 사용자 영역의 올바른 값으로 대체해야 합니다.

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.microsoft.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)


## 다음 단계

DNS 영역을 만든 후에는 [레코드 집합 및 레코드](dns-getstarted-create-recordset.md)를 만들어 인터넷 도메인에 대한 이름 확인을 시작해야 합니다.

<!---HONumber=AcomDC_0824_2016-->