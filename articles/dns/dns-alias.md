---
title: Azure DNS 별칭 레코드 개요
description: Microsoft Azure DNS의 별칭 레코드에 대한 지원 개요입니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 05a6a1700de2b8b334b40d9efd9b8d79e9e7241f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957575"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 별칭 레코드 개요

Azure DNS 별칭 레코드는 DNS 영역 내에서 다른 Azure 리소스를 참조할 수 있게 해주는 DNS 레코드 집합에 대한 조건입니다. 예를 들어 A 레코드 대신 Azure 공용 IP 주소를 참조하는 별칭 레코드 집합을 만들 수 있습니다. 별칭 레코드 집합은 Azure 공용 IP 주소 서비스 인스턴스를 동적으로 가리키므로 별칭 레코드 집합은 DNS 확인 중에 원활하게 업데이트됩니다.

별칭 레코드 집합은 Azure DNS 영역의 레코드 유형 A, AAAA 및 CNAME에 지원됩니다. 

> [!NOTE]
> Traffic Manager의 A 또는 AAAA 레코드 유형에 대한 별칭 레코드는 외부 엔드포인트 유형에만 지원됩니다. Traffic Manager의 외부 엔드포인트에 IPv4 또는 IPv6 주소(고정 IP가 이상적임)를 적절히 제공해야 합니다.

## <a name="capabilities"></a>기능

- **DNS A/AAAA 레코드 집합의 공용 IP 리소스를 가리킵니다**. A/AAAA 레코드 집합을 만들고 공용 IP 리소스를 가리키는 별칭 레코드 집합으로 설정할 수 있습니다.
- **DNS A/AAAA/CNAME 레코드 집합의 Traffic Manager 프로필을 가리킵니다**. DNS CNAME 레코드 집합의 Traffic Manager 프로필(예: contoso.trafficmanager.net) 중 CNAME을 가리킬 수 있을 뿐만 아니라, 이제 DNS 영역에 있는 A 또는 AAAA 레코드 집합의 외부 엔드포인트가 포함된 Traffic Manager 프로필을 가리킬 수도 있습니다.
   > [!NOTE]
   > Traffic Manager의 A 또는 AAAA 레코드 유형에 대한 별칭 레코드는 외부 엔드포인트 유형에만 지원됩니다. Traffic Manager의 외부 엔드포인트에 IPv4 또는 IPv6 주소(고정 IP가 이상적임)를 적절히 제공해야 합니다.
- **동일한 영역 내의 다른 DNS 레코드 집합을 가리킵니다**. 별칭 레코드는 동일한 유형의 다른 레코드 집합을 참조할 수 있습니다. 예를 들어 DNS CNAME 레코드 집합을 동일한 유형의 다른 CNAME 레코드 집합에 대한 별칭으로 설정할 수 있습니다. 일부 레코드 집합을 별칭으로 설정하고 일부를 별칭이 아닌 항목으로 설정하려는 경우 이 기능이 유용합니다.

## <a name="scenarios"></a>시나리오
별칭 레코드에 대한 몇 가지 일반적인 시나리오가 있습니다.

### <a name="prevent-dangling-dns-records"></a>허상 DNS 레코드 방지
Azure DNS 영역 내에서 별칭 레코드를 사용하여 공용 IP 및 Traffic Manager 프로필과 같은 Azure 리소스의 수명 주기를 자세히 추적할 수 있습니다. 기존 DNS 레코드와 관련된 일반적인 문제 중 하나는 AAAA 또는 CNAME 레코드 유형과 관련된 “허상 레코드” 문제입니다. 

기존 DNS 영역 레코드의 경우 대상 IP 또는 CNAME이 더 이상 존재하지 않을 경우 DNS 영역 레코드가 이 사실을 알지 못하므로 수동으로 업데이트해야 합니다. 일부 조직에서는 이 수동 업데이트가 제때 이루어지지 않거나, 역할 및 관련 권한 수준의 구분으로 인해 문제를 유발할 수 있습니다.

예를 들어 한 역할에 응용 프로그램에 속하는 CNAME 또는 IP 주소를 삭제하는 데 필요한 권한은 있지만 이러한 대상을 가리키는 DNS 레코드를 업데이트하는 데 필요한 권한은 없는 경우가 있습니다. 따라서 IP 또는 CNAME이 삭제되고 나서 한참 후에 이를 가리키는 DNS 레코드가 제거됨에 따라 최종 사용자가 서비스 중단 문제를 겪을 수 있습니다.

별칭 레코드는 이러한 레코드와 관련된 복잡성을 없애고, 이러한 허상 참조 문제를 방지하는 데 도움이 됩니다. DNS 레코드를 공용 IP 또는 Traffic Manager 프로필을 가리키는 별칭 레코드로 사용할 수 있는 경우 이러한 기본 리소스가 삭제됨과 동시에 DNS 별칭 레코드도 제거됩니다. 따라서 최종 사용자가 서비스 중단 문제를 겪지 않습니다.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>응용 프로그램 IP 변경 시 자동으로 DNS 영역 업데이트

이전 시나리오와 마찬가지로, 응용 프로그램이 이동하거나 기본 가상 머신이 다시 시작된 경우 기본 공용 IP 리소스의 IP 주소가 변경되면 별칭 레코드가 자동으로 업데이트됩니다. 최종 사용자가 오래된 IP 주소가 있는 다른 응용 프로그램으로 이동한 경우 잠재적인 보안 위험을 방지할 수 있습니다.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>영역 루트에서 부하가 분산된 응용 프로그램 호스트

DNS 프로토콜은 영역 루트(예: contoso.com)에서 A 또는 AAAA 레코드를 제외한 다른 레코드의 할당을 방지합니다. 따라서 응용 프로그램 소유자가 Traffic Manager 뒤에 부하가 분산된 응용 프로그램을 가지고 있는 경우에도 문제가 발생하지 않습니다. 영역 루트 레코드에서 Traffic Manager 프로필을 가리킬 수 없기 때문입니다. 결과적으로, 응용 프로그램 소유자는 어쩔 수 없이 해결 방법을 적용해야 했습니다. 예를 들어 영역 루트에서 다른 도메인으로(예: contoso.com에서 www.contoso.com으로) 리디렉션하려면 응용 프로그램 계층에서 리디렉션해야 합니다. 그러면 리디렉션 기능 측면에서 단일 실패 지점이 발생하지 않습니다.

별칭 레코드를 사용하면 이 문제가 더 이상 발생하지 않습니다. 이제 응용 프로그램 소유자는 영역 루트 레코드에서 외부 엔드포인트가 있는 Traffic Manager 프로필을 가리키도록 할 수 있습니다. 응용 프로그램 소유자는 이 기능을 사용하여 DNS 영역 내 다른 모든 도메인에 사용되는 Traffic Manager 프로필을 가리킬 수 있습니다. 예를 들어 Traffic Manager 프로필에 외부 엔드포인트만 구성되어 있다면 contoso.com과 www.contoso.com은 모두 동일한 Traffic Manager 프로필을 가리킬 수 있습니다.

## <a name="next-steps"></a>다음 단계

별칭 레코드에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [자습서: Azure 공용 IP 주소를 가리키도록 별칭 레코드 구성](tutorial-alias-pip.md)
- [자습서: Traffic Manager를 사용하여 apex 도메인 이름을 지원하도록 별칭 레코드 구성](tutorial-alias-tm.md)
- [DNS FAQ](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
