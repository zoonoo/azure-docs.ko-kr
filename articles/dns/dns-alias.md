---
title: 별칭 레코드 개요 - Azure DNS
description: 이 문서에서는 Microsoft Azure DNS의 별칭 레코드 지원을 알아봅니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/23/2021
ms.author: rohink
ms.openlocfilehash: 962f1f98ad610c953368b351c1859e8e738340f9
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107949383"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 별칭 레코드 개요

Azure DNS 별칭 레코드는 DNS 레코드 집합에서 한정됩니다. DNS 영역 내의 다른 Azure 리소스를 참조할 수 있습니다. 예를 들어 A 레코드 대신 Azure 공용 IP 주소를 참조하는 별칭 레코드 집합을 만들 수 있습니다. 별칭 레코드 집합은 Azure 공용 IP 주소 서비스 인스턴스를 동적으로 가리킵니다. 결과적으로, 별칭 레코드 집합은 DNS 확인 동안 자신을 원활하게 업데이트합니다.

별칭 레코드 집합은 Azure DNS 영역의 다음 레코드 유형에 대해 지원됩니다. 

- A
- AAAA
- CNAME

> [!NOTE]
> A 또는 AAAA 레코드 유형에 대해 별칭 레코드를 사용하여 [Azure Traffic Manager 프로필](../traffic-manager/quickstart-create-traffic-manager-profile.md)을 가리키려면 Traffic Manager 프로필에 [외부 엔드포인트](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)만 있는지 확인해야 합니다. Traffic Manager의 외부 엔드포인트에 IPv4 또는 IPv6 주소를 제공해야 합니다. 엔드포인트에서는 FQDN(정규화된 도메인 이름)을 사용할 수 없습니다. 이상적으로는 고정 IP 주소를 사용합니다.

## <a name="capabilities"></a>기능

- **DNS A/AAAA 레코드 집합의 공용 IP 리소스를 가리킵니다**. A/AAAA 레코드 집합을 만들고 별칭 레코드 집합이 공용 IP 리소스로 가리키도록 할 수 있습니다(표준 또는 기본). 공용 IP 주소가 변경되거나 삭제되면 DNS 레코드 집합이 자동으로 변경됩니다. 잘못된 IP 주소를 가리키는 현수 DNS 레코드가 방지됩니다.

   > [!NOTE]
   > 현재 별칭 레코드 집합은 리소스당 20개로 제한됩니다.

- **DNS A/AAAA/CNAME 레코드 집합에서 Traffic Manager 프로필 가리키기** - A/AAAA 또는 CNAME 레코드 집합을 만들고 별칭 레코드를 사용하여 Traffic Manager 프로필을 가리킬 수 있습니다. 영역 루트에서 트래픽을 라우팅해야 하는 경우에 특히 유용합니다. 영역 루트에 기존 CNAME 레코드가 지원되지 않기 때문입니다. 예를 들어 Traffic Manager 프로필이 myprofile.trafficmanager.net이고 비즈니스 DNS 영역이 contoso.com이라고 가정하겠습니다. contoso.com(영역 루트)에 대해 A/AAAA 유형의 별칭 레코드 집합을 만들고 myprofile.trafficmanager.net을 가리킬 수 있습니다.
- **Azure CDN(Content Delivery Network) 엔드포인트 가리키기** - Azure 스토리지와 Azure CDN을 사용하여 정적 웹사이트를 만들 때 유용합니다.
- **동일한 영역에 있는 다른 DNS 레코드 집합 가리키기** - 별칭 레코드는 같은 형식의 다른 레코드 집합을 참조할 수 있습니다. 예를 들어 DNS CNAME 레코드 집합을 다른 CNAME 레코드 집합에 대한 별칭으로 설정할 수 있습니다. 이 정렬은 일부 레코드 집합은 별칭으로, 일부는 비별칭으로 지정하려는 경우에 유용합니다.

## <a name="scenarios"></a>시나리오

다음은 별칭 레코드를 사용하는 일반적인 몇몇 시나리오입니다.

### <a name="prevent-dangling-dns-records"></a>허상 DNS 레코드 방지

기존 DNS 레코드의 일반적인 문제는 레코드 누락입니다. 예를 들어 IP 주소에 대한 변경 내용이 반영되도록 업데이트되지 않은 DNS 레코드가 있습니다. 이런 문제는 특히 A/AAAA 또는 CNAME 레코드 유형에서 발생합니다.

기존 DNS 영역 레코드를 사용할 경우 대상 IP나 CNAME이 더 이상 존재하지 않으면 이와 연결된 DNS 레코드를 수동으로 업데이트해야 합니다. 일부 조직에서는 프로세스 문제 또는 역할 및 관련된 권한 수준 분리로 인해 수동 업데이트가 신속하게 수행되지 않을 수 있습니다. 예를 들어 역할에 애플리케이션에 속하는 CNAME 또는 IP 주소를 삭제하기 위한 권한이 있지만 해당 대상을 가리키는 DNS 레코드를 업데이트할 수 있는 충분한 권한이 없습니다. DNS 레코드 업데이트가 지연되면 잠재적으로 사용자에게 확장 중단이 발생할 수 있습니다.

별칭 레코드는 DNS 레코드의 수명주기를 Azure 리소스와 긴밀하게 연결하여 현수 참조를 방지합니다. 공용 IP 주소 또는 Traffic Manager 프로필을 가리키는 별칭 레코드로 정규화된 DNS 레코드를 예로 들어보겠습니다. 이러한 기본 리소스를 삭제하면 DNS 별칭 레코드가 빈 레코드 집합이 됩니다. 삭제된 리소스를 더 이상 참조하지 않습니다.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>애플리케이션 IP 주소가 변경되면 DNS 레코드 집합이 자동으로 업데이트됩니다.

이 시나리오는 이전 시나리오와 비슷합니다. 애플리케이션이 이동하거나 기본 가상 머신이 다시 시작할 수 있습니다. 그런 다음, 기본 공용 IP 리소스의 IP 주소가 변경되면 별칭 레코드가 자동으로 업데이트됩니다. 이 업데이트는 사용자를 이전 공용 IP 주소에 할당된 다른 애플리케이션으로 연결하는 보안 위험을 방지할 수 있습니다.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>영역 루트에서 워크로드가 분산된 애플리케이션 호스트

DNS 프로토콜은 영역 루트에서 CNAME 레코드가 할당되는 것을 방지합니다. 예를 들어 도메인이 contoso.com이라고 가정합니다. somelabel.contoso.com의 CNAME 레코드를 만들 수 있지만 contoso.com 자체의 CNAME을 만들 수 없습니다.

이 제한은 [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 뒤에 워크로드가 분산된 애플리케이션이 있는 애플리케이션 소유자에게 문제가 됩니다. Traffic Manager 프로필을 사용하려면 CNAME 레코드를 만들어야 하므로 영역 루트에서 Traffic Manager 프로필을 가리킬 수 없습니다.

이 문제를 해결하려면 별칭 레코드를 사용하면 됩니다. CNAME 레코드와 달리 별칭 레코드는 영역 루트에서 생성되며 애플리케이션 소유자는 별칭 레코드를 사용하여 영역 루트 레코드를 외부 엔드포인트가 있는 Traffic Manager 프로필로 가리킬 수 있습니다. 애플리케이션 소유자는 DNS 영역 내 다른 모든 도메인에 사용되는 동일한 Traffic Manager 프로필을 가리킵니다.

예를 들어 contoso.com 및 www\.contoso.com은 동일한 Traffic Manager 프로필을 가리킬 수 있습니다. Azure Traffic Manager 프로필을 사용하여 별칭 레코드를 사용하는 방법에 대한 자세한 내용을 보려면 다음 단계 섹션을 참조하세요.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>영역 루트를 Azure CDN 엔드포인트로 가리키기

Traffic Manager 프로필과 마찬가지로 별칭 레코드를 사용하여 DNS 영역 루트를 Azure CDN 엔드포인트로 가리킬 수도 있습니다. Azure 스토리지와 Azure CDN을 사용하여 정적 웹사이트를 만들 때 유용합니다. 그런 다음, DNS 이름 앞에 “www”를 추가하지 않고도 웹사이트에 액세스할 수 있습니다.

예를 들어 정적 웹사이트 이름이 `www.contoso.com`인 경우 사용자는 DNS 이름 앞에 www를 추가할 필요 없이 `contoso.com`을 사용하여 사이트에 액세스할 수 있습니다.

앞에서 설명한 대로 CNAME 레코드는 영역 루트에서 지원되지 않습니다. CNAME 레코드를 사용하여 contoso.com을 CDN 엔드포인트로 가리킬 수 없습니다. 대신 별칭 레코드를 사용하여 영역 루트를 CDN 엔드포인트로 직접 가리킬 수 있습니다.

> [!NOTE]
> 현재 영역 루트를 Akamai에서 Azure CDN의 CDN 엔드포인트로 가리킬 수 없습니다.

## <a name="next-steps"></a>다음 단계

별칭 레코드에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [자습서: Azure 공용 IP 주소를 참조하도록 별칭 레코드 구성](tutorial-alias-pip.md)
- [자습서: Traffic Manager를 사용하여 apex 도메인 이름을 지원하도록 별칭 레코드 구성](tutorial-alias-tm.md)
- [DNS FAQ](./dns-faq.md#alias-records)