---
title: Azure DNS 별칭 레코드 개요
description: Microsoft Azure DNS의 별칭 레코드에 대한 지원 개요입니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52b42e964e7abe207064aff49f7f8f27f8476ef4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092845"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 별칭 레코드 개요

Azure DNS 별칭 레코드는 DNS 레코드 집합에서 한정됩니다. DNS 영역 내의 다른 Azure 리소스를 참조할 수 있습니다. 예를 들어 A 레코드 대신 Azure 공용 IP 주소를 참조하는 별칭 레코드 집합을 만들 수 있습니다. 별칭 레코드 집합은 Azure 공용 IP 주소 서비스 인스턴스를 동적으로 가리킵니다. 결과적으로, 별칭 레코드 집합은 DNS 확인 동안 자신을 원활하게 업데이트합니다.

별칭 레코드 집합은 Azure DNS 영역의 다음 레코드 유형에 대해 지원됩니다. 

- A 
- AAAA 
- CNAME 

> [!NOTE]
> Azure Traffic Manager의 A 또는 AAAA 레코드 유형에 대한 별칭 레코드는 외부 엔드포인트 유형에만 지원됩니다. Traffic Manager의 외부 엔드포인트에 IPv4 또는 IPv6 주소를 적절히 제공해야 합니다. 이상적으로 주소에 대해 고정 IP를 사용합니다.

## <a name="capabilities"></a>기능

- **DNS A/AAAA 레코드 집합의 공용 IP 리소스를 가리킵니다**. A/AAAA 레코드 집합을 만들고 공용 IP 리소스를 가리키는 별칭 레코드 집합으로 설정할 수 있습니다.

- **DNS A/AAAA/CNAME 레코드 집합의 Traffic Manager 프로필을 가리킵니다**. DNS CNAME 레코드 집합에서 Traffic Manager 프로필의 CNAME을 가리킬 수 있습니다. 예로 contoso.trafficmanager.net이 있습니다. 이제 DNS 영역의 A 또는 AAAA 레코드 집합에서 외부 엔드포인트가 있는 Traffic Manager 프로필을 가리킬 수도 있습니다.

   > [!NOTE]
   > Traffic Manager의 A 또는 AAAA 레코드 유형에 대한 별칭 레코드는 외부 엔드포인트 유형에만 지원됩니다. Traffic Manager의 외부 엔드포인트에 IPv4 또는 IPv6 주소를 적절히 제공해야 합니다. 이상적으로 주소에 대해 고정 IP를 사용합니다.
   
- **동일한 영역 내의 다른 DNS 레코드 집합을 가리킵니다**. 별칭 레코드는 동일한 유형의 다른 레코드 집합을 참조할 수 있습니다. 예를 들어 DNS CNAME 레코드 집합을 동일한 유형의 다른 CNAME 레코드 집합에 대한 별칭으로 설정할 수 있습니다. 이 정렬은 일부 레코드 집합은 별칭으로, 일부는 비별칭으로 지정하려는 경우에 유용합니다.

## <a name="scenarios"></a>시나리오
별칭 레코드에 대한 몇 가지 일반적인 시나리오가 있습니다.

### <a name="prevent-dangling-dns-records"></a>허상 DNS 레코드 방지
 Azure DNS 영역 내에서 별칭 레코드를 사용하여 Azure 리소스의 수명 주기를 자세히 추적할 수 있습니다. 리소스는 공용 IP 또는 Traffic Manager 프로필을 포함합니다. 기존 DNS 레코드의 일반적인 문제는 레코드 누락입니다. 이 문제는 특히 AAAA 또는 CNAME 레코드 유형에서 발생합니다. 

기존 DNS 영역 레코드의 경우 대상 IP 또는 CNAME이 더 이상 존재하지 않을 경우 DNS 영역 레코드가 이 사실을 알지 못합니다. 따라서 레코드를 수동으로 업데이트해야 합니다. 일부 조직에서는 이 수동 업데이트가 제시간 안에 발생하지 않을 수 있습니다. 또한 역할과 관련 권한 수준이 분리되기 때문에 문제가 될 수도 있습니다.

예를 들어 역할에 응용 프로그램에 속하는 CNAME 또는 IP 주소를 삭제하기 위한 권한이 있지만 해당 대상을 가리키는 DNS 레코드를 업데이트할 수 있는 권한은 없을 수 있습니다. IP 또는 CNAME이 삭제될 때와 가리키는 DNS 레코드가 제거될 때가 일치하지 않고 시간 지연이 발생합니다. 이러한 시간 지연은 사용자에게 가동 중단을 가져올 수 있습니다.

별칭 레코드는 이러한 레코드와 관련된 복잡성을 없애고, 이러한 허상 참조 문제를 방지하는 데 도움이 됩니다. 예를 들어, 별칭 레코드로 한정된 DNS 레코드가 공용 IP 또는 Traffic Manager 프로필을 가리키도록 합니다. 이러한 기본 리소스가 삭제되면 DNS 별칭 레코드도 동시에 제거됩니다. 이 프로세스는 사용자가 절대 가동 중단을 겪지 않게 합니다.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>응용 프로그램 IP 변경 시 자동으로 DNS 영역 업데이트

이 시나리오는 이전 시나리오와 비슷합니다. 응용 프로그램이 이동되거나 기본 가상 머신이 다시 시작될 수 있습니다. 그런 후에 기본 공용 IP 리소스에 대해 IP 주소가 변경되면 별칭 레코드가 자동으로 업데이트됩니다. 잠재적인 보안 위험을 방지하려면 사용자를 오래된 IP 주소가 있는 다른 응용 프로그램으로 보냅니다.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>영역 루트에서 워크로드가 분산된 응용 프로그램 호스트

DNS 프로토콜은 영역 루트에서 A 또는 AAAA 레코드를 제외한 다른 레코드의 할당을 방지합니다. 예를 들어 contoso.com이 있습니다. 이 제한은 Traffic Manager 뒤에 워크로드가 분산된 응용 프로그램 있는 응용 프로그램 소유자의 문제를 나타냅니다. 영역 apex 레코드에서 Traffic Manager 프로필을 가리킬 수는 없습니다. 결과적으로, 응용 프로그램 소유자는 해결 방법을 사용해야 합니다. 응용 프로그램 계층에서 리디렉션을 수행하면 영역 apex에서 다른 도메인으로 리디렉션됩니다. contoso.com에서 www.contoso.com으로의 리디렉션을 예로 들 수 있습니다. 이 정렬은 리디렉션 함수에 대한 단일 실패 지점을 나타냅니다.

별칭 레코드를 사용하면 이 문제가 더 이상 발생하지 않습니다. 이제 응용 프로그램 소유자는 영역 apex 레코드에서 외부 엔드포인트가 있는 Traffic Manager 프로필을 가리키도록 할 수 있습니다. 응용 프로그램 소유자는 DNS 영역 내 다른 모든 도메인에 사용되는 Traffic Manager 프로필을 가리킬 수 있습니다. 예를 들어 contoso.com 및 www.contoso.com은 동일한 Traffic Manager 프로필을 가리킬 수 있습니다. 단, Traffic Manager 프로필에 외부 엔드포인트만 구성되어 있어야 합니다.

## <a name="next-steps"></a>다음 단계

별칭 레코드에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [자습서: Azure 공용 IP 주소를 참조하도록 별칭 레코드 구성](tutorial-alias-pip.md)
- [자습서: Traffic Manager를 사용하여 apex 도메인 이름을 지원하도록 별칭 레코드 구성](tutorial-alias-tm.md)
- [DNS FAQ](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
