---
title: "DNS 영역 및 레코드 | Microsoft Docs"
description: "Microsoft Azure DNS에서 DNS 영역 및 레코드 호스팅에 대한 지원 개요."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: jtuliani
translationtype: Human Translation
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: b99bc20b66787c7a87fa49143b4cbf861e0e68a2

---

# <a name="dns-zones-and-records"></a>DNS 영역 및 레코드

이 페이지는 도메인, DNS 영역, DNS 레코드 및 레코드 집합의 핵심 개념과 Azure DNS에서 지원되는 방식에 대해 설명합니다.

## <a name="domain-names"></a>도메인 이름

Domain Name System은 도메인 계층 구조입니다. 계층 구조는 이름이 단순히 '**.**'인 'root' 도메인에서 시작합니다.  그 아래에 'com', 'net', 'org', 'uk' 또는 'jp'와 같은 최상위 도메인이 있습니다.  그 아래에 'org.uk' 또는 'co.jp'와 같은 두 번째 수준의 도메인이 있는 DNS 계층 구조의 도메인은 전체적으로 분산되며 전 세계의 DNS 이름 서버에서 호스팅됩니다.

도메인 이름 등록 기관은 'contoso.com'과 같은 도메인 이름을 구입할 수 있는 조직입니다.  도메인 이름을 구입할 경우 해당 이름 하의 DNS 계층 구조를 관리할 수 있습니다. 예를 들어, 'www.contoso.com'이라는 이름을 회사 웹 사이트에 사용할 수 있습니다. 등록 기관은 조직 대신 자체 이름 서버에 도메인을 호스팅하거나 조직이 다른 이름 서버를 지정할 수 있습니다.

Azure DNS는 전 세계적으로 분산된 고가용성 이름 서버 인프라를 제공하므로 조직은 이러한 인프라를 사용하여 도메인을 호스팅할 수 있습니다. Azure에 도메인을 호스팅하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다.

Azure DNS는 현재 도메인 이름 구입을 지원하지 않습니다. 도메인 이름을 구입하려면 타사 도메인 이름 등록 기관을 이용해야 합니다. 등록 기관은 일반적으로 소액의 연간 요금을 부과합니다. 그런 다음, DNS 레코드의 관리를 위해 Azure DNS에 해당 도메인을 호스트할 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md) 을 참조하세요.

## <a name="dns-zones"></a>DNS 영역 

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다.

예를 들어 'contoso.com' 도메인은 'mail.contoso.com'(메일 서버) 및 'www.contoso.com'(웹 사이트)과 같은 여러 DNS 레코드를 포함할 수 있습니다.

Azure DNS에서 DNS 영역을 만들 경우 영역의 이름은 리소스 그룹 내에서 고유해야 합니다. 서로 다른 리소스 그룹이나 Azure 구독에서는 동일한 영역 이름을 다시 사용할 수 있습니다. 복수 영역이 동일한 이름을 공유할 경우 인스턴스는 각각 다른 이름 서버 주소에 할당됩니다. 도메인 이름 등록 기관에는 한 가지 주소 집합만 구성할 수 있습니다.

> [!NOTE]
> Azure DNS에서 해당 도메인 이름으로 DNS 영역을 만들기 위해 도메인을 소유할 필요는 없습니다. 하지만 도메인 이름 등록 기관에 도메인 이름의 올바른 이름 서버로 Azure DNS 이름 서버를 구성하려면 도메인을 소유해야 합니다.

자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

## <a name="dns-records"></a>DNS 레코드

### <a name="record-types"></a>레코드 유형

각 DNS 레코드에는 이름 및 형식이 있습니다. 레코드는 포함된 데이터에 따라 다양한 형식으로 구성됩니다. 가장 일반적인 형식은 이름을 IPv4 주소에 매핑하는 'A' 레코드입니다. 또 다른 일반적인 형식은 이름을 메일 서버에 매핑하는 'MX' 레코드입니다.

Azure DNS는 A, AAAA, CNAME, MX, NS, PTR, SOA, SRV, TXT 등 일반적인 DNS 레코드 형식을 모두 지원합니다.

### <a name="record-names"></a>레코드 이름

Azure DNS에서는 상대 이름을 사용하여 레코드를 지정합니다. FQDN(*정규화된* 도메인 이름)은 영역 이름을 포함하는 반면 *상대* 이름은 영역 이름을 포함하지 않습니다. 예를 들어 'contoso.com' 영역의 상대 레코드 이름 'www'는 정규화된 레코드 이름 'www.contoso.com'을 제공합니다.

*apex* 레코드는 DNS 영역의 루트(또는 *apex*)에 있는 DNS 레코드입니다. 예를 들어 DNS 영역 'contoso.com'에서 루트 레코드는 정규화된 이름 'contoso.com'도 가집니다(*naked* 도메인이라고도 함).  규칙에 따라 루트 레코드를 만드는 데 상대 이름 '@'을 사용합니다.

### <a name="record-sets"></a>레코드 집합

지정된 이름 및 형식을 가진 DNS 레코드를 두 개 이상 만들어야 하는 경우도 있습니다. 예를 들어 'www.contoso.com' 웹 사이트가 서로 다른 두 IP 주소에서 호스트된다고 가정합니다. 웹 사이트에는 각 IP 주소마다 하나씩, 두 개의 A 레코드가 있어야 합니다. 이것이 레코드 집합의 예입니다.

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Azure DNS는 *레코드 집합*을 사용하여 DNS 레코드를 관리합니다. 레코드 집합(*리소스* 레코드 집합이라고도 함)은 영역 내에서 동일한 이름과 형식을 가진 DNS 레코드의 컬렉션입니다. 대부분의 레코드 집합은 단일 레코드를 포함하지만, 레코드 집합이 두 개 이상의 레코드를 포함하는 것과 같은 예도 드물지 않습니다.

예를 들어 'contoso.com' 영역에 IP 주소 '134.170.185.46'(위 첫 번째 레코드)을 가리키는 A 레코드 'www'를 만든 경우를 가정해 보겠습니다.  두 번째 레코드를 만들려면 새 레코드 집합을 만들지 않고 기존 레코드 집합에 해당 레코드를 추가합니다.

SOA 및 CNAME 레코드 유형은 예외입니다. DNS 표준에서는 이러한 유형에 대해 동일한 이름의 복수 레코드를 허용하지 않으므로 이러한 레코드 집합은 단일 레코드만 포함할 수 있습니다.

### <a name="time-to-live"></a>Time-to-Live

Time-to-Live, 즉 TTL은 각 레코드가 다시 쿼리되기 전에 클라이언트에 캐시되는 기간을 지정합니다. 위 예제에서 TTL은 3600초, 즉 1시간입니다.

Azure DNS에서 TTL은 각 레코드가 아니라 레코드 집합에 대해 지정되므로 해당 레코드 집합 내의 모든 레코드에 동일한 값이 사용됩니다.  1 ~ 2,147,483,647초 사이의 TTL 값을 지정할 수 있습니다.

### <a name="wildcard-records"></a>와일드카드 레코드

Azure DNS는 [와일드카드 레코드](https://en.wikipedia.org/wiki/Wildcard_DNS_record)를 지원합니다. 와일드카드 레코드는 쿼리에 대한 응답으로 일치하는 이름을 반환합니다(와일드카드가 아닌 레코드 집합에서 가까운 일치 항목이 없는 경우). 와일드카드 레코드 집합은 NS 및 SOA를 제외한 모든 레코드 유형에 대해 지원됩니다.

와일드카드 레코드 집합을 만들려면 레코드 집합 이름 '\*'을 사용합니다. 또는 맨 왼쪽의 레이블로 '\*'가 포함된 이름을 사용할 수 있습니다(예: '\*.foo').

### <a name="cname-records"></a>CNAME 레코드

CNAME 레코드 집합은 동일한 이름의 다른 레코드 집합과 함께 존재할 수 없습니다. 예를 들어 상대 이름이 'www'인 CNAME 레코드 집합과 상대 이름이 'www'인 A 레코드를 동시에 만들 수 없습니다.

영역 루트(이름 = '@'))는 항상 영역을 만들 때 생성된 NS 및 SOA 레코드 집합을 포함하므로 영역 루트에 CNAME 레코드 집합을 만들 수 없습니다.

이러한 제약 조건은 DNS 표준에서 발생하며 Azure DNS의 제한 사항이 아닙니다.

### <a name="ns-records"></a>NS 레코드

NS 레코드는 각 영역의 루트에서 자동으로 생성되며(이름 = '@'), 영역이 삭제될 경우 자동으로 삭제됩니다(별도로 삭제할 수 없음).  이 레코드 집합의 TTL을 수정할 수 있지만 영역에 할당된 Azure DNS 이름 서버를 참조하도록 사전 구성된 레코드는 수정할 수 없습니다.

영역 루트를 제외한 영역 내 다른 NS 레코드를 만들고 삭제할 수 있습니다.  이 경우 하위 영역을 구성할 수 있습니다([Azure DNS에 하위 도메인 위임](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns) 참조).

### <a name="soa-records"></a>SOA 레코드

SOA 레코드는 각 영역의 루트에서 자동으로 생성되며(이름 = '@'), 영역이 삭제될 경우 자동으로 삭제됩니다.  SOA 레코드는 별도로 생성 또는 삭제할 수 없습니다.

SOA 레코드에서 'host' 속성(Azure DNS에서 제공한 기본 이름 서버 이름을 참조하도록 사전 구성됨)을 제외한 모든 속성을 수정할 수 있습니다.

### <a name="spf-records"></a>SPF 레코드

SPF(Sender Policy Framework ) 레코드는 지정된 도메인 이름 대신 전자 메일을 전송하도록 허용된 전자 메일 서버를 지정하는 데 사용합니다.  SPF 레코드를 올바르게 구성하는 것은 수신자가 전자 메일을 '정크'로 지정하지 않도록 하는 데 중요합니다.

DNS RFC는 원래 이 시나리오를 지원하기 위한 새로운 'SPF' 레코드 유형으로 소개되었습니다. 기존 이름 서버를 지원하기 위해 TXT 레코드 유형을 사용하여 SPF 레코드를 지정할 수도 있습니다.  이러한 모호성으로 인한 혼란은 [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1)로 해결되었습니다.  즉, SPF 레코드는 TXT 레코드 유형만 사용하여 만들어야 하며 SPF 레코드 유형은 사용하지 않습니다.

**SPF 레코드는 Azure DNS에서 지원되며 TXT 레코드 유형을 사용하여 만들어야 합니다.** 사용하지 않는 SPF 레코드 유형은 지원되지 않습니다. [DNS 영역 파일을 가져올 경우](dns-import-export.md) SPF 레코드 유형을 사용하는 SPF 레코드는 TXT 레코드 유형으로 전환됩니다.

### <a name="srv-records"></a>SRV 레코드

[SRV 레코드](https://en.wikipedia.org/wiki/SRV_record)는 다양한 서비스에서 서버 위치를 지정하는 데 사용됩니다. Azure DNS에서 SRV 레코드를 지정할 경우

* *서비스*와 *프로토콜*은 레코드 집합 이름에 포함하여 지정하고 밑줄로 접두사를 지정해야 합니다.  예를 들어 '\_sip.\_tcp.name'입니다.  영역 루트에 있는 레코드의 경우 레코드 이름에 '@'을 지정하지 않아도 되며 서비스 이름과 프로토콜만 사용합니다(예: '\_sip.\_tcp').
* *우선 순위*, *가중치*, *포트*, *대상*은 레코드 집합에서 각 레코드의 매개 변수로 지정됩니다.

## <a name="tags-and-metadata"></a>태그 및 메타데이터

### <a name="tags"></a>태그

태그는 이름-값 쌍의 목록으로, Azure Resource Manager에서 리소스에 레이블을 지정하는 데 사용됩니다.  Azure Resource Manager는 태그를 사용하여 Azure 청구서를 필터링하여 표시할 수 있으며 태그가 필요한 정책을 설정할 수 있습니다. 태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../resource-group-using-tags.md)을 참조하십시오.

Azure DNS에서는 DNS 영역 리소스에 Azure Resource Manager 태그를 사용할 수 있으며  DNS 레코드 집합의 태그는 지원하지 않습니다.

### <a name="metadata"></a>Metadata

Azure DNS에서는 레코드 집합 태그 대신 '메타데이터'를 사용하여 레코드 집합에 주석을 추가할 수 있습니다.  태그와 유사한 메타데이터를 사용하면 각 레코드 집합에 이름-값 쌍을 연결할 수 있습니다.  이는 각 레코드 집합의 목적을 기록하는 데 유용할 수 있습니다.  태그와 달리, 메타데이터는 Azure 청구서를 필터링하여 표시하는 데 사용할 수 없으며 Azure Resource Manager 정책에서 지정할 수 없습니다.

## <a name="limits"></a>제한

Azure DNS를 사용할 경우 다음과 같은 기본 제한이 적용됩니다.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>다음 단계

* Azure DNS 사용을 시작하려면 [DNS 영역 만들기](dns-getstarted-create-dnszone-portal.md) 및 [DNS 레코드 만들기](dns-getstarted-create-recordset-portal.md) 방법에 대해 알아보세요.
* 기존 DNS 영역을 마이그레이션하려면 [DNS 영역 파일 가져오기](dns-import-export.md) 방법에 대해 알아보세요.




<!--HONumber=Nov16_HO3-->


