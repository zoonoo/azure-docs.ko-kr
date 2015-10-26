<properties
   pageTitle="Azure DNS 시작 | Microsoft Azure"
   description="Azure DNS에 대한 DNS 영역을 만드는 방법을 알아봅니다. PowerShell을 사용하여 DNS 도메인 호스팅을 시작하기 위해 첫 번째 DNS 영역을 만드는 단계별 가이드입니다."
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# Powershell을 사용한 Azure DNS 시작


> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [Azure Powershell steps](dns-getstarted-create-dnszone.md)

'contoso.com' 도메인은 'mail.contoso.com'(메일 서버) 및 'www.contoso.com'(웹 사이트)과 같은 많은 DNS 레코드를 포함할 수 있습니다. DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다.<BR><BR> 도메인 호스팅을 시작하려면 먼저 DNS 영역을 만들어야 합니다. 특정 도메인에 대해 만든 DNS 레코드는 모두 해당 도메인에 대한 DNS 영역 내에 있습니다.<BR><BR> 이러한 지침은 Microsoft Azure PowerShell을 사용합니다. Azure DNS cmdlet을 사용하려면 최신 Azure PowerShell로 업데이트해야 합니다. Microsoft Azure 명령줄 인터페이스, REST API 또는 SDK를 사용하여 동일한 단계를 실행할 수도 있습니다.<BR><BR>

## Azure DNS PowerShell 설정

Azure PowerShell을 사용하여 Azure DNS를 관리하려면 먼저 다음 단계를 완료해야 합니다.

### 1단계
Azure DNS는 ARM(Azure 리소스 관리자)을 사용합니다. ARM cmdlet을 사용하려면 PowerShell 모드를 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.<BR><BR>

		PS C:\> Switch-AzureMode -Name AzureResourceManager

"The Switch-AzureMode cmdlet은 더 이상 사용되지 않으며 향후 릴리스에서 제거될 예정입니다."라는 경고 메시지가 표시되는 경우 무시합니다.

### 2단계
 Azure 계정에 로그인합니다.<BR><BR>

		PS C:\> Add-AzureAccount

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.<BR>

### 3단계
사용할 Azure 구독을 선택합니다. <BR>


		PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

사용 가능한 구독 목록을 보려면 'Get-azuresubscription' cmdlet을 사용합니다.<BR>

### 4단계
새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.<BR>

		PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"


Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 그러나 모든 DNS 리소스는 국가별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure DNS에 영향을 주지 않습니다.<BR>

### 5단계

Azure DNS 서비스는 Microsoft.Network 리소스 공급자에 의해 관리됩니다. Azure DNS를 사용하려면 먼저 이 리소스 공급자를 사용하도록 Azure 구독을 등록해야 합니다. 이 작업은 각 구독에 대해 한 번만 수행하면 됩니다.

	PS c:> Register-AzureProvider -ProviderNamespace Microsoft.Network



## Etag 및 태그
### Etag
두 사용자 또는 두 프로세스가 동시에 DNS 레코드 수정을 시도한다고 가정합니다. 어느 쪽이 성공할까요? 그리고 성공한 사용자는 다른 사용자의 변경 내용을 자신이 방금 덮어썼다는 것을 알고 있을까요?<BR><BR> Azure DNS는 Etag를 사용하여 동일한 리소스에 대한 동시 변경을 안전하게 처리합니다. 각 DNS 리소스(영역 또는 레코드 집합)에는 Etag가 연결되어 있습니다. 리소스를 검색할 때마다 해당 Etag도 검색됩니다. 리소스를 업데이트할 때 Azure DNS에서 서버의 Etag가 일치하는지 확인할 수 있도록 Etag를 다시 전달할 수 있습니다. 리소스를 업데이트할 때마다 Etag가 다시 생성되므로 Etag 불일치는 동시 변경이 발생했음을 나타냅니다. 새 리소스를 만들 때도 Etag를 사용하여 리소스가 존재하지 않는지 확인합니다.

기본적으로 Azure DNS PowerShell은 Etag를 사용하여 영역 및 레코드 집합에 대한 동시 변경을 차단합니다. 선택적 '-Overwrite' 스위치를 사용하여 Etag 검사를 무시할 수 있으며, 이 경우 발생한 동시 변경 내용을 덮어쓰게 됩니다.<BR><BR> Azure DNS REST API 수준에서 Etag는 HTTP 헤더를 사용하여 지정됩니다. 해당 동작은 다음 표에 나와 있습니다.

|헤더|동작|
|------|--------|
|없음|PUT 항상 성공(Etag 검사 안 함)|
|If-match <etag>|리소스가 있고 Etag가 일치하는 경우에만 PUT 성공|
|If-match * |리소스가 있는 경우에만 PUT 성공|
|If-none-match |* 리소스가 없는 경우에만 PUT 성공|

### 태그
태그는 Etag와 다릅니다. 태그는 이름-값 쌍의 목록으로, Azure 리소스 관리자에서 대금 청구 또는 그룹화를 위해 리소스에 레이블을 지정하는 데 사용됩니다. 태그에 대한 자세한 내용은 태그를 사용하여 Azure 리소스 구성을 참조하세요. Azure DNS PowerShell은 옵션 '-Tag ' 매개 변수를 사용하여 지정된 영역 및 레코드 집합 둘 다에서 태그를 지원합니다. 다음 예제에서는 두 태그 ‘project = demo’ and ‘env = test’를 사용하여 DNS 영역을 만드는 방법을 보여 줍니다.

	PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )


## DNS 영역 만들기
New-AzureDnsZone cmdlet을 사용하여 DNS 영역을 만듭니다. 아래 예제에서는 'MyResourceGroup'이라는 리소스 그룹에 'contoso.com'이라는 DNS 영역을 만듭니다.<BR>

		PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

>[AZURE.NOTE]Azure DNS에서는 종료하는 '.' 없이 영역 이름을 지정해야 합니다. 예를 들어 'contoso.com.' 대신 'contoso.com'으로 지정합니다.<BR>


이제 Azure DNS에 DNS 영역이 생성되었습니다. DNS 영역을 만들면 다음과 같은 DNS 레코드도 생성됩니다.<BR>



- 'SOA(권한 시작)' 레코드. 각 DNS 영역의 루트에 있습니다.
- 권한이 있는 NS(이름 서버) 레코드. 영역을 호스트하는 이름 서버를 보여 줍니다. Azure DNS는 이름 서버 풀을 사용하므로 Azure DNS의 각 영역에 다른 이름 서버가 할당될 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.<BR>

이러한 레코드를 보려면 Get-AzureDnsRecordSet를 사용합니다.

		PS C:\> Get-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

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

>[AZURE.NOTE]DNS 영역의 루트에 있는 레코드 집합은 레코드 집합 이름으로 "@"를 사용합니다.


첫 번째 DNS 영역을 만들었으므로 nslookup, dig 또는 [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/en-us/library/jj590781.aspx)과 같은 DNS 도구를 사용하여 테스트할 수 있습니다.<BR>

Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내야 합니다. 위의 Get-AzureDnsRecordSet에 의해 나열된 것처럼 영역에 대한 이름 서버는 NS 레코드에 제공되며, 아래 명령에 영역에 대한 올바른 값을 대체해야 합니다.<BR>

		C:\> nslookup
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


[레코드 집합 및 레코드 만들기 시작](dns-getstarted-create-recordset.md)<BR> [DNS 영역을 관리하는 방법](dns-operations-dnszones.md)<BR> [DNS 레코드를 관리하는 방법](dns-operations-recordsets.md)<BR> [.NET SDK로 Azure 작업 자동화](dns-sdk.md)<BR> [Azure DNS REST API 참조](https://msdn.microsoft.com/library/azure/mt163862.aspx)
 

<!---HONumber=Oct15_HO3-->