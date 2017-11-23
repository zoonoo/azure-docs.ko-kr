---
title: Azure DNS FAQ | Microsoft Docs
description: "Azure DNS에 대한 질문과 대답"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: a8cff450730d05970398989ceb3e8585aefd6771
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2017
---
# <a name="azure-dns-faq"></a>Azure DNS FAQ

## <a name="about-azure-dns"></a>Azure DNS 정보

### <a name="what-is-azure-dns"></a>Azure DNS란?

Domain Name System, 즉 DNS는 웹 사이트 또는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다. Azure DNS는 DNS 도메인에 대한 호스팅 서비스로, Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다.

Azure DNS의 DNS 도메인은 DNS 이름 서버의 Azure 글로벌 네트워크에 호스팅됩니다. 사용 가능한 가장 가까운 DNS 서버에서 각 DNS 쿼리에 응답하도록 애니캐스트 네트워킹이 사용됩니다. 이렇게 하면 도메인에 대해 빠른 성능과 고가용성이 제공됩니다.

Azure DNS 서비스는 Azure Resource Manager를 기반으로 합니다. 이 경우 역할 기반 액세스 제어, 감사 로그 및 리소스 잠금과 같은 리소스 관리자 기능의 이점을 누릴 수 있습니다. 도메인과 레코드는 Azure 포털, Azure PowerShell cmdlet 및 플랫폼 간 Azure CLI를 통해 관리할 수 있습니다. 자동 DNS 관리가 필요한 응용 프로그램은 REST API 및 SDK를 통해 서비스와 통합할 수 있습니다.

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS 비용은 얼마입니까?

Azure DNS 요금 청구 모델은 Azure DNS에 호스트되는 DNS 영역의 수와 수신되는 DNS 쿼리 수를 기준으로 합니다. 할인은 사용량에 따라 제공됩니다.

자세한 내용은 [Azure DNS 가격 책정 페이지](https://azure.microsoft.com/pricing/details/dns/)를 참조하세요.

### <a name="what-is-the-sla-for-azure-dns"></a>Azure DNS에 대한 SLA는 무엇인가요?

Microsoft는 유효한 DNS 요청에 대해 99.99% 이상의 시간 동안 하나 이상의 Azure DNS 이름 서버에서 응답을 제공할 것을 보장합니다.

자세한 내용은 [Azure DNS SLA 페이지](https://azure.microsoft.com/support/legal/sla/dns)를 참조하세요.

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>'DNS 영역'이란 무엇인가요? DNS 도메인과 같은 것인가요? 

도메인은 Domain Name System의 고유 이름입니다(예: 'contoso.com').

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. 예를 들어 'contoso.com' 도메인은 'mail.contoso.com'(메일 서버) 및 'www.contoso.com'(웹 사이트)과 같은 여러 DNS 레코드를 포함할 수 있습니다. 이러한 레코드는 DNS 영역 'contoso.com'에서 호스트됩니다.

도메인 이름은 *이름만*으로 구성되지만 DNS 영역은 도메인 이름에 대한 DNS 레코드를 포함하는 데이터 리소스입니다. Azure DNS를 사용하면 DNS 영역을 호스트하고 Azure에서 도메인에 대한 DNS 레코드를 관리할 수 있습니다. 또한 인터넷의 DNS 쿼리에 응답하기 위해 DNS 이름 서버를 제공합니다.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Azure DNS를 사용하기 위해 DNS 도메인 이름을 구입해야 하나요? 

그럴 필요는 없습니다.

Azure DNS에서 DNS 영역을 호스트하기 위해 도메인을 구입할 필요는 없습니다. 도메인 이름을 소유하지 않고도 언제든지 DNS 영역을 만들 수 있습니다. 이 영역에 대한 DNS 쿼리는 해당 영역에 할당된 Azure DNS 이름 서버로 전송되는 경우에만 확인됩니다.

DNS 영역을 전역 DNS 계층 구조에 연결하려면 도메인 이름을 구입해야 합니다. 이 경우 전 세계 어디에서든지 DNS 쿼리를 수행하여 DNS 영역을 찾고 DNS 레코드로 응답을 받을 수 있습니다.

## <a name="azure-dns-features"></a>Azure DNS 기능

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS는 DNS 기반 트래픽 라우팅 또는 끝점 장애 조치(failover)를 지원하나요?

DNS 기반 트래픽 라우팅 및 끝점 장애 조치(failover)는 Azure Traffic Manager에 의해 제공됩니다. 이것은 Azure DNS와 함께 사용할 수 있는 별도 Azure 서비스입니다. 자세한 내용은 [Traffic Manager 개요](../traffic-manager/traffic-manager-overview.md)를 참조하세요.

Azure DNS는 지정된 DNS 레코드에 대한 각 DNS 쿼리가 항상 동일한 DNS 응답을 수신하는 '고정' DNS 도메인 호스트만 지원합니다.

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS는 도메인 이름 등록을 지원하나요?

아니요. Azure DNS는 현재 도메인 이름 구입을 지원하지 않습니다. 도메인을 구입하려면 타사 도메인 이름 등록 기관을 사용해야 합니다. 등록 기관은 일반적으로 소액의 연간 요금을 부과합니다. 그런 다음, DNS 레코드의 관리를 위해 Azure DNS에 해당 도메인을 호스트할 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md) 을 참조하세요.

이 기능은 백로그에서 추적하는 기능입니다. 피드백 사이트를 사용하여 [이 기능에 대한 지원을 등록](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)할 수 있습니다.

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS에서는 '개인' 도메인을 지원하나요?
'개인' 도메인에 대한 지원은 사설 DNS 영역을 사용하여 구현됩니다.  이 기능은 현재 미리 보기로 제공됩니다.  사설 DNS 영역은 인터넷 연결 Azure DNS 영역과 동일한 도구를 사용하여 관리되지만 지정된 가상 네트워크 내에서만 확인할 수 있습니다.  자세한 내용은 [개요](private-dns-overview.md)를 참조하세요.

Azure의 다른 내부 DNS 옵션에 대한 자세한 내용은 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS에서는 DNSSEC를 지원하나요?

아니요. Azure DNS는 현재 DNSSEC를 지원하지 않습니다.

이 기능은 백로그에서 추적하는 기능입니다. 피드백 사이트를 사용하여 [이 기능에 대한 지원을 등록](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)할 수 있습니다.

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS에서는 영역 전송(AXFR/IXFR)을 지원하나요?

아니요. Azure DNS는 현재 영역 전송을 지원하지 않습니다. DNS 영역은 [Azure CLI를 사용하여 Azure DNS로 가져올 수 있습니다](dns-import-export.md). 그런 후 [Azure DNS 관리 포털](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell cmdlet](dns-operations-recordsets.md) 또는 [CLI 도구](dns-operations-recordsets-cli.md)를 통해 DNS 레코드를 관리할 수 있습니다.

이 기능은 백로그에서 추적하는 기능입니다. 피드백 사이트를 사용하여 [이 기능에 대한 지원을 등록](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)할 수 있습니다.

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS에서는 URL 리디렉션을 지원하나요?

아니요. URL 리디렉션 서비스는 실제로 DNS 서비스가 아닙니다. 따라서 DNS 수준이 아닌 HTTP 수준에서 작동합니다. URL을 번들로 묶는 일부 DNS 공급자는 서비스를 전체 제품의 일부로 리디렉션합니다. 현재 이 기능은 Azure DNS에서 지원되지 않습니다.

이 기능은 백로그에서 추적됩니다. 피드백 사이트를 사용하여 [이 기능에 대한 지원을 등록](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)할 수 있습니다.

## <a name="using-azure-dns"></a>Azure DNS 사용

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Azure DNS 및 다른 DNS 공급자를 사용하여 도메인을 공동 호스트할 수 있나요?

예. Azure DNS에서는 다른 DNS 서비스와의 도메인 공동 호스팅을 지원합니다.

이렇게 하려면 도메인의 NS 레코드(도메인에 대한 DNS 쿼리를 수신하는 공급자 제어)가 두 공급자의 이름 서버를 가리키도록 수정해야 합니다. 이러한 NS 레코드는 Azure DNS, 다른 공급자 및 부모 영역(일반적으로 도메인 이름 등록자를 통해 구성됨)의 세 위치에서 수정해야 합니다. DNS 위임에 대한 자세한 내용은 [DNS 도메인 위임](dns-domain-delegation.md)을 참조하세요.

또한 도메인에 대한 DNS 레코드가 두 DNS 공급자 간에 동기화되는지도 확인해야 합니다. Azure DNS는 현재 DNS 영역 전송을 지원하지 않습니다. [Azure DNS 관리 포털](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell cmdlet](dns-operations-recordsets.md) 또는 [CLI 도구](dns-operations-recordsets-cli.md)를 사용해서 DNS 레코드를 동기화해야 합니다.

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>4개의 Azure DNS 이름 서버 모두에 도메인을 위임해야 하나요?

예. Azure DNS는 오류 격리 및 복원력 증가를 위해 각 DNS 영역에 4개의 이름 서버를 할당합니다. Azure DNS SLA를 충족하려면 4개의 이름 서버 모두에 도메인을 위임해야 합니다.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Azure DNS에 대한 사용량 제한은 얼마나 되나요?

Azure DNS를 사용할 경우 다음과 같은 기본 제한이 적용됩니다.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>리소스 그룹 또는 구독 간에 Azure DNS 영역을 이동할 수 있나요?

예. 리소스 그룹 또는 구독 간에 DNS 영역을 이동할 수 있습니다.

DNS 영역을 이동할 때 DNS 쿼리는 아무런 영향도 받지 않습니다. 영역에 할당된 이름 서버는 동일하게 유지되고 DNS 쿼리는 정상 처리량으로 처리됩니다.

DNS 영역을 이동하는 방법에 대한 자세한 내용 및 지침을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요.

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>DNS 변경 내용이 적용되는 데 시간이 얼마나 걸리나요?

새 DNS 영역 및 DNS 레코드는 일반적으로 Azure DNS 이름 서버에 몇 초 안에 신속하게 적용됩니다.

기존 DNS 레코드에 대한 변경은 조금 더 오래 걸릴 수 있지만 60초 이내에 Azure DNS 이름 서버에 적용됩니다. 이 경우 Azure DNS 외부의 DNS 캐싱(DNS 클라이언트 및 DNS 재귀 확인자에 의한) 또한 DNS 변경 내용이 적용되는 데 소요되는 시간에 영향을 줄 수 있습니다. 이러한 캐시 기간은 각 레코드 집합의 TTL(Time-To-Live) 속성을 사용하여 제어할 수 있습니다.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>실수로 삭제되지 않도록 내 DNS 영역을 보호하려면 어떻게 해야 하나요?

Azure DNS는 Azure Resource Manager를 사용하여 관리되며 Azure Resource Manager가 제공하는 Access Control 기능을 활용합니다. 역할 기반 Access Control는 DNS 영역 및 레코드 집합에 대한 읽기 또는 쓰기 액세스 권한이 있는 사용자를 제어하는 데 사용할 수 있습니다. 실수로 DNS 영역 및 레코드 집합을 수정하거나 삭제하지 않도록 하기 위해 리소스 잠금을 적용할 수 있습니다.

자세한 내용은 [DNS 영역 및 레코드 보호](dns-protect-zones-recordsets.md)를 참조하세요.

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Azure DNS에서 SPF 레코드를 설정하려면 어떻게 해야 하나요?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6-"></a>IPv6을 통해 Azure DNS 이름 서버가 확인하나요? 

예. Azure DNS 이름 서버는 IPv4 및 IPv6 주소를 모두 포함하는 이중 스택입니다. DNS 영역에 할당된 Azure DNS 이름 서버에 대한 IPv6 주소를 찾으려면 nslookup과 같은 도구를 사용할 수 있습니다(예: `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Azure DNS에서 IDN(국제 도메인 이름)을 설정하려면 어떻게 해야 하나요?

IDN(국제 도메인 이름)은 '[punycode](https://en.wikipedia.org/wiki/Punycode)'로 각 DNS 이름을 인코딩하여 사용할 수 있습니다. DNS 쿼리는 이러한 punycode로 인코딩된 이름을 사용하여 수행됩니다.

먼저 영역 이름 또는 레코드 집합 이름을 punycode로 변환하여 Azure DNS에서 IDN(국제 도메인 이름)을 구성할 수 있습니다. Azure DNS는 현재 punycode로/부터의 기본 제공 변환을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure DNS에 대해 자세히 알아보기](dns-overview.md)
<br>
[개인 도메인에 Azure DNS 사용에 대해 자세히 알아보기](private-dns-overview.md)
<br>
[DNS 영역 및 레코드에 대해 자세히 알아보기](dns-zones-records.md)
<br>
[Azure DNS 시작](dns-getstarted-portal.md)

