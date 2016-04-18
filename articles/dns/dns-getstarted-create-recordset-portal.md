<properties
   pageTitle="Azure 포털을 사용하여 DNS 영역에 대한 레코드 집합 및 레코드 만들기 | Microsoft Azure"
   description="Azure DNS에 대한 호스트 레코드를 만들고 Azure 포털을 사용하여 레코드 집합 및 레코드를 만드는 방법"
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
   ms.date="03/29/2016"
   ms.author="cherylmc"/>


# Azure 포털을 사용하여 DNS 레코드 및 레코드 집합 만들기


> [AZURE.SELECTOR]
- [Azure 포털](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


DNS 영역을 만든 후 도메인에 대한 DNS 레코드를 추가해야 합니다. 이 작업을 수행하려면 먼저 DNS 레코드 및 레코드 집합을 이해해야 합니다.

## 레코드 집합 및 레코드 이해

### 레코드 정보

각 DNS 레코드에는 이름 및 형식이 있습니다.

FQDN(“정규화된” 도메인 이름)은 영역 이름을 포함하는 반면 “상대” 이름은 영역 이름을 포함하지 않습니다. 예를 들어 “contoso.com” 영역의 상대 레코드 이름 “www”는 정규화된 레코드 이름 “www.contoso.com”을 제공합니다.

>[AZURE.NOTE] Azure DNS에서는 상대 이름을 사용하여 레코드를 지정합니다.

레코드는 포함된 데이터에 따라 다양한 형식으로 제공됩니다. 가장 일반적인 형식은 이름을 IPv4 주소에 매핑하는 “**A**” 레코드입니다. 또 다른 형식은 이름을 메일 서버에 매핑하는 “**MX**”입니다.

Azure DNS는 A, AAAA, CNAME, MX, NS, SOA, SRV, TXT 등 모든 일반적인 DNS 레코드 형식을 지원합니다. SPF 레코드는 TXT 레코드 형식을 사용하여 만들어야 합니다. 자세한 내용은 [페이지](http://tools.ietf.org/html/rfc7208#section-3.1)를 참조하세요.


### 레코드 집합 정보

지정된 이름 및 형식을 가진 DNS 레코드를 두 개 이상 만들어야 하는 경우도 있습니다. 예를 들어 www.contoso.com 웹 사이트가 서로 다른 두 IP 주소에서 호스트된다고 가정합니다. 이 경우 각 IP 주소마다 하나씩, 두 개의 A 레코드가 있어야 합니다. 이것이 레코드 집합의 예입니다.

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Azure DNS는 레코드 집합을 사용하여 DNS 레코드를 관리합니다. 레코드 집합은 영역 내에서 동일한 이름과 형식을 가진 DNS 레코드 컬렉션입니다. 대부분의 레코드 집합은 단일 레코드를 포함하지만, 레코드 집합이 두 개 이상의 레코드를 포함하는 위와 같은 예도 드물지 않습니다.

SOA 및 CNAME 형식의 레코드 집합은 예외입니다. DNS 표준에 따라 이러한 형식에는 동일한 이름을 가진 여러 레코드가 허용되지 않습니다.

Time-to-Live, 즉 TTL은 각 레코드가 다시 쿼리되기 전에 클라이언트에 캐시되는 기간을 지정합니다. 위 예제에서 TTL은 3600초, 즉 1시간입니다. TTL은 각 레코드가 아니라 레코드 집합에 대해 지정되므로 해당 레코드 집합 내의 모든 레코드에 동일한 값이 사용됩니다.

#### 와일드카드 레코드 집합

Azure DNS는 [와일드카드 레코드](https://en.wikipedia.org/wiki/Wildcard_DNS_record)를 지원합니다. 이러한 레코드는 일치하는 이름이 있는 모든 쿼리에 대해 반환됩니다(비 와일드카드 레코드 집합에 더 일치하는 항목이 없는 한). 와일드카드 레코드 집합은 NS 및 SOA를 제외한 모든 레코드 유형에 대해 지원됩니다.

와일드카드 레코드 집합을 만들려면 "*"라는 레코드 집합 이름을 사용하거나 첫 번째 레이블의 이름이 "*", 예: "*.foo"인 이름을 사용합니다.

#### CNAME 레코드 집합

CNAME 레코드 집합은 동일한 이름의 다른 레코드 집합과 함께 존재할 수 없습니다. 예를 들어 상대 이름이 “www”인 CNAME과 상대 이름이 “www”인 A 레코드를 동시에 만들 수 없습니다. 영역 루트(이름 = '@')는 항상 영역을 만들 때 생성된 NS 및 SOA 레코드 집합을 포함하므로 영역 루트에 CNAME 레코드 집합을 만들 수 없습니다. 이러한 제약 조건은 DNS 표준에서 발생하며 Azure DNS의 제한 사항이 아닙니다.


## 새 레코드 집합 및 레코드를 만들려면

다음 예제에서는 Azure 포털을 사용하여 레코드 집합 및 레코드를 만드는 방법을 안내합니다. 여기에서는 DNS 'A' 레코드 유형을 사용합니다.

1. Azure 포털에 로그인합니다.

2. 레코드 집합을 만들려는 **DNS 영역** 블레이드로 이동합니다.

3. DNS 영역 블레이드의 위쪽에서 **레코드 집합**을 클릭하여 **레코드 집합 추가** 블레이드를 엽니다.
 
	![새 레코드 집합](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. **레코드 집합 추가** 블레이드에서 레코드 집합의 이름을 지정합니다. 예를 들어 레코드 집합의 이름을 "**www**"로 지정할 수 있습니다.
  
	![레코드 집합 추가](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 만들려는 레코드 유형을 선택합니다. 예를 들어 **A**를 선택합니다.

6. **TTL**을 설정합니다. 포털의 기본값은 1시간입니다.

7. 한 줄당 하나씩 IP 주소를 추가합니다. 위에서 제안된 레코드 집합 이름 및 레코드 유형을 사용할 경우 www 레코드 집합에 대해 A 레코드에 IPv4 IP 주소가 추가됩니다.

8. IP 주소 추가를 마쳤으면 블레이드의 아래쪽에서 **확인**을 클릭합니다. DNS 레코드 집합이 생성됩니다.

## DNS 도구를 사용하여 DNS 영역을 테스트합니다.


Azure DNS에서 새 영역을 사용하도록 도메인을 아직 위임하지 않은 경우 DNS 쿼리를 영역에 대한 이름 서버 중 하나로 직접 보내야 합니다. 영역에 대한 이름 서버가 DNS 영역 블레이드의 Essentials 창에 표시됩니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md) 문서를 참조하세요.

nslookup, dig 또는 [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)과 같은 DNS 도구를 사용하여 DNS 영역을 테스트할 수 있습니다.


## 다음 단계

Azure DNS에 대한 자세한 내용은 [Azure DNS 개요](dns-overview.md)를 참조하세요. DNS 자동화에 대한 자세한 내용은 [.NET SDK를 사용하여 DNS 영역 및 레코드 집합 만들기](dns-sdk.md)를 참조하세요.

<!---HONumber=AcomDC_0406_2016-->