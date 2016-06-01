<properties
   pageTitle="Azure 포털에서 DNS 영역을 만들고 관리하는 방법 | Microsoft Azure"
   description="Azure DNS에 대한 DNS 영역을 만드는 방법을 알아봅니다. 이는 첫 번째 DNS를 만들고 관리하며 Azure 포털을 사용하여 DNS 도메인 호스팅을 시작하는 방법에 대한 단계별 가이드입니다."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# Azure 포털에서 DNS 영역 만들기


> [AZURE.SELECTOR]
- [Azure 포털](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)



이 문서에서는 Azure 포털을 사용하여 DNS 영역을 만드는 단계를 안내합니다. PowerShell 또는 CLI를 사용하여 DNS 영역을 만들 수도 있습니다.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


### Azure DNS에 대한 태그 정보


태그는 이름-값 쌍의 목록으로, Azure Resource Manager에서 대금 청구 또는 그룹화를 위해 리소스에 레이블을 지정하는 데 사용됩니다. 태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../resource-group-using-tags.md) 문서를 참조하세요.

Azure 포털에서 DNS 영역에 대한 **설정** 블레이드를 사용하여 태그를 추가할 수 있습니다.


## DNS 영역 만들기

1. Azure 포털에 로그인합니다.

2. 허브 메뉴에서 **새로 만들기 > 네트워킹**을 클릭한 다음 **DNS 영역**을 클릭하여 DNS 영역 블레이드를 엽니다.
 
	![DNS 영역](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. **DNS 영역** 블레이드의 아래쪽에서 **만들기**를 클릭합니다. 이렇게 하면 **DNS 영역 만들기** 블레이드가 열립니다.

	![영역 만들기](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. **DNS 영역 만들기** 블레이드에서 DNS 영역의 이름을 지정합니다. 예를 들어 *contoso.com*입니다. 위 섹션에서 [DNS 영역 이름 정보](#names)를 참조하세요.

5. 다음으로, 사용할 리소스 그룹을 지정합니다. 새 리소스 그룹을 만들거나 이미 있는 리소스 그룹을 선택할 수 있습니다.

6. **위치** 드롭다운에서 리소스 그룹의 위치를 지정합니다. 이 설정은 DNS 영역의 위치가 아니라 리소스 그룹의 위치를 나타냅니다. 실제 DNS 영역 리소스는 자동으로 "전역"으로 설정되므로 포털에서 지정할 필요가 없거나 지정할 수 있는 항목이 아닙니다.

7. 대시보드에서 새 영역을 쉽게 찾으려는 경우 **대시보드에 고정** 확인란을 선택된 상태로 그대로 둘 수 있습니다. 그런 다음 **Create**를 클릭합니다.

	![대시보드에 고정](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. 만들기를 클릭하면 대시보드에 새 영역이 구성되고 있는 것을 볼 수 있습니다.

	![만드는 중](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. 새 영역이 만들어지면 대시보드에서 새 영역에 대한 블레이드가 열립니다.


## 레코드 보기

DNS 영역을 만들면 다음 레코드도 생성됩니다.

- “SOA(권한 시작)” 레코드. SOA는 각 DNS 영역의 루트에 있습니다.
- 권한이 있는 NS(이름 서버) 레코드. 영역을 호스트하는 이름 서버를 보여 줍니다. Azure DNS는 이름 서버 풀을 사용하므로 Azure DNS의 각 영역에 다른 이름 서버가 할당될 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

Azure 포털에서 레코드를 볼 수 있습니다.

1. **DNS 영역** 블레이드에서 **모든 설정**을 클릭하여 DNS 영역에 대한 **설정 블레이드**를 엽니다. 

	![영역](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)


2. Essentials 창의 아래쪽에서 DNS 영역에 대한 레코드 집합을 볼 수 있습니다.


	![영역](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

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



## DNS 영역 삭제

포털에서 직접 DNS 영역을 삭제할 수 있습니다. Azure DNS에서 DNS 영역을 삭제하기 전에 영역을 만들 때 자동으로 생성된, 영역 루트에 있는 NS 및 SOA 레코드를 제외한 모든 레코드 집합을 삭제해야 합니다.

1. 삭제할 영역에 대한 **DNS 영역** 블레이드를 찾은 후 블레이드 위쪽에 있는 **삭제**를 클릭합니다.
 
2. 자동으로 생성된 NS 및 SOA 레코드를 제외하고 모든 레코드 집합을 삭제해야 함을 알려 주는 메시지가 표시됩니다. 레코드 집합을 삭제한 경우 **예**를 클릭합니다. 포털에서 DNS 영역을 삭제하는 경우 해당 DNS 영역과 연관된 리소스 그룹은 삭제되지 않습니다.


## 다음 단계

DNS 영역을 만든 후에는 [레코드 집합 및 레코드](dns-getstarted-create-recordset-portal.md)를 만들어 인터넷 도메인에 대한 이름 확인을 시작해야 합니다.

<!---HONumber=AcomDC_0518_2016-->