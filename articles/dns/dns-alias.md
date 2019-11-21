---
title: Alias records overview - Azure DNS
description: In this article, learn about support for alias records in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: allensu
ms.openlocfilehash: da396d3e3db4acd1a9843977b8b3e3d51c33f021
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212333"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 별칭 레코드 개요

Azure DNS 별칭 레코드는 DNS 레코드 집합에서 한정됩니다. DNS 영역 내의 다른 Azure 리소스를 참조할 수 있습니다. 예를 들어 A 레코드 대신 Azure 공용 IP 주소를 참조하는 별칭 레코드 집합을 만들 수 있습니다. 별칭 레코드 집합은 Azure 공용 IP 주소 서비스 인스턴스를 동적으로 가리킵니다. 결과적으로, 별칭 레코드 집합은 DNS 확인 동안 자신을 원활하게 업데이트합니다.

별칭 레코드 집합은 Azure DNS 영역의 다음 레코드 유형에 대해 지원됩니다. 

- 문자열(UTF-8 형식) 또는
- AAAA
- CNAME

> [!NOTE]
> A 또는 AAAA 레코드 유형에 대해 별칭 레코드를 사용하여 [Azure Traffic Manager 프로필](../traffic-manager/quickstart-create-traffic-manager-profile.md)을 가리키려면 Traffic Manager 프로필에 [외부 엔드포인트](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)만 있는지 확인해야 합니다. Traffic Manager의 외부 엔드포인트에 IPv4 또는 IPv6 주소를 제공해야 합니다. You can't use fully-qualified domain names (FQDNs) in endpoints. 이상적으로는 고정 IP 주소를 사용합니다.

## <a name="capabilities"></a>기능

- **DNS A/AAAA 레코드 집합의 공용 IP 리소스를 가리킵니다**. You can create an A/AAAA record set and make it an alias record set to point to a public IP resource (standard or basic). The DNS record set changes automatically if the public IP address changes or is deleted. 잘못된 IP 주소를 가리키는 현수 DNS 레코드가 방지됩니다.

   There is a current limit of 20 alias records sets per resource.

- **DNS A/AAAA/CNAME 레코드 집합의 Traffic Manager 프로필을 가리킵니다**. A/AAAA 또는 CNAME 레코드 집합을 만들고 별칭 레코드를 사용하여 Traffic Manager 프로필을 가리킬 수 있습니다. It's especially useful when you need to route traffic at a zone apex, as traditional CNAME records aren't supported for a zone apex. 예를 들어 Traffic Manager 프로필이 myprofile.trafficmanager.net이고 비즈니스 DNS 영역이 contoso.com이라고 가정하겠습니다. contoso.com(영역 루트)에 대해 A/AAAA 유형의 별칭 레코드 집합을 만들고 myprofile.trafficmanager.net을 가리킬 수 있습니다.
- **Point to an Azure Content Delivery Network (CDN) endpoint**. This is useful when you create static websites using Azure storage and Azure CDN.
- **동일한 영역 내의 다른 DNS 레코드 집합을 가리킵니다**. 별칭 레코드는 동일한 유형의 다른 레코드 집합을 참조할 수 있습니다. 예를 들어 DNS CNAME 레코드 집합을 다른 CNAME 레코드 집합에 대한 별칭으로 설정할 수 있습니다. 이 정렬은 일부 레코드 집합은 별칭으로, 일부는 비별칭으로 지정하려는 경우에 유용합니다.

## <a name="scenarios"></a>시나리오

별칭 레코드에 대한 몇 가지 일반적인 시나리오가 있습니다.

### <a name="prevent-dangling-dns-records"></a>허상 DNS 레코드 방지

기존 DNS 레코드의 일반적인 문제는 레코드 누락입니다. For example, DNS records that haven't been updated to reflect changes to IP addresses. 이런 문제는 특히 A/AAAA 또는 CNAME 레코드 유형에서 발생합니다.

기존 DNS 영역 레코드의 경우 대상 IP 또는 CNAME이 더 이상 존재하지 않을 경우, 이와 연결된 DNS 영역 레코드를 수동으로 업데이트해야 합니다. In some organizations, a manual update might not happen in time because of process issues or the separation of roles and associated permission levels. 예를 들어 역할에 애플리케이션에 속하는 CNAME 또는 IP 주소를 삭제하기 위한 권한이 있지만 해당 대상을 가리키는 DNS 레코드를 업데이트할 수 있는 권한은 없을 수 있습니다. DNS 레코드 업데이트가 지연되면 잠재적으로 사용자에 대한 중단이 발생할 수 있습니다.

별칭 레코드는 DNS 레코드의 수명주기를 Azure 리소스와 긴밀하게 연결하여 현수 참조를 방지합니다. 공용 IP 주소 또는 Traffic Manager 프로필을 가리키는 별칭 레코드로 정규화된 DNS 레코드를 예로 들어보겠습니다. If you delete those underlying resources, the DNS alias record becomes an empty record set. It no longer references the deleted resource.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>애플리케이션 IP 주소가 변경되면 DNS 레코드 집합이 자동으로 업데이트됩니다.

이 시나리오는 이전 시나리오와 비슷합니다. 애플리케이션이 이동되거나 기본 가상 머신이 다시 시작될 수 있습니다. 그런 후에 기본 공용 IP 리소스에 대해 IP 주소가 변경되면 별칭 레코드가 자동으로 업데이트됩니다. 오래된 공용 IP 주소가 할당된 다른 애플리케이션으로 사용자를 연결하는 잠재적인 보안 위험을 피할 수 있습니다.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>영역 루트에서 워크로드가 분산된 애플리케이션 호스트

DNS 프로토콜은 영역 루트에서 CNAME 레코드가 할당되는 것을 방지합니다. For example if your domain is contoso.com; you can create CNAME records for somelabel.contoso.com; but you can't create CNAME for contoso.com itself.
이 제한은 [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 뒤에 워크로드가 분산된 애플리케이션이 있는 애플리케이션 소유자에게 문제가 됩니다. Since using a Traffic Manager profile requires creation of a CNAME record, it isn't possible to point at the Traffic Manager profile from the zone apex.

This problem is solved using alias records. Unlike CNAME records, alias records are created at the zone apex and application owners can use it to point their zone apex record to a Traffic Manager profile that has external endpoints. Application owners point to the same Traffic Manager profile that's used for any other domain within their DNS zone.

For example, contoso.com and www\.contoso.com can point to the same Traffic Manager profile. Azure Traffic Manager 프로필을 사용하여 별칭 레코드를 사용하는 방법에 대한 자세한 내용을 보려면 다음 단계 섹션을 참조하세요.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Point zone apex to Azure CDN endpoints

Just like a Traffic Manager profile, you can also use alias records to point your DNS zone apex to Azure CDN endpoints. This is useful when you create static websites using Azure storage and Azure CDN. You can then access the website without prepending "www" to your DNS name.

For example, if your static website is named www.contoso.com, your users can access your site using contoso.com without the need to prepend www to the DNS name.

As described previously, CNAME records aren't supported at the zone apex. So, you can’t use a CNAME record to point contoso.com to your CDN endpoint. Instead, you can use an alias record to point the zone apex to a CDN endpoint directly.

> [!NOTE]
> Pointing a zone apex to CDN endpoints for Azure CDN from Akamai is currently not supported.

## <a name="next-steps"></a>다음 단계

별칭 레코드에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [자습서: Azure 공용 IP 주소를 참조하도록 별칭 레코드 구성](tutorial-alias-pip.md)
- [자습서: Traffic Manager를 사용하여 apex 도메인 이름을 지원하도록 별칭 레코드 구성](tutorial-alias-tm.md)
- [DNS FAQ](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
