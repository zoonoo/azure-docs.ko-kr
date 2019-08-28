---
title: Azure 프라이빗 DNS란?
description: Microsoft Azure의 프라이빗 DNS 호스팅 서비스에 대한 개요입니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 0921a1ac7aa1192fae78f168c2eb51ee3e74e24a
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774616"
---
# <a name="what-is-azure-private-dns"></a>Azure 프라이빗 DNS란?

> [!IMPORTANT]
> Azure 프라이빗 DNS는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

DNS(Domain Name System)는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다.  Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. Azure DNS는 인터넷 연결 DNS 도메인 지원 외에 프라이빗 DNS 영역도 지원합니다.

Azure 프라이빗 DNS는 사용자 지정 DNS 솔루션을 추가하지 않고도 가상 네트워크의 도메인 이름을 관리하고 확인할 수 있는 안정적이고 신뢰할 수 있는 DNS 서비스를 제공합니다. 프라이빗 DNS 영역을 사용하면 현재 Azure에서 제공하는 이름 대신 사용자 고유의 사용자 지정 도메인 이름을 사용할 수 있습니다. 사용자 지정 도메인 이름을 사용하면 조직의 요구 사항에 가장 적합하도록 가상 네트워크 아키텍처를 조정할 수 있습니다. 가상 네트워크 내에서 그리고 가상 네트워크 간에 VM(가상 머신)에 대한 이름을 확인할 수 있게 해줍니다. 또한 분할-수평 보기를 사용해 영역을 구성할 수도 있습니다. 그러면 프라이빗 DNS 영역과 공용 DNS 영역이 이름을 공유할 수 있습니다.

가상 네트워크에서 프라이빗 DNS 영역의 레코드를 확인하려면 가상 네트워크를 해당 영역과 연결해야 합니다. 연결된 가상 네트워크는 전체 액세스 권한을 가지며 프라이빗 영역에 게시된 모든 DNS 레코드를 확인할 수 있습니다. 또한 가상 네트워크 연결에 대한 자동 등록을 사용하도록 설정할 수 있습니다. 가상 네트워크 연결에 대한 자동 등록을 사용하도록 설정하면 해당 가상 네트워크의 가상 머신에 대한 DNS 레코드가 프라이빗 영역에 등록됩니다. 자동 등록이 활성화된 경우 Azure DNS는 가상 머신이 생성되고, 해당 IP 주소를 변경하거나 삭제될 때마다 영역 레코드를 업데이트합니다.

![DNS 개요](./media/private-dns-overview/scenario.png)

> [!NOTE]
> 프라이빗 DNS 영역에 *.local* 도메인을 사용하지 않는 것이 가장 좋습니다. 일부 운영 체제는 이를 지원하지 않습니다.

## <a name="benefits"></a>이점

Azure 프라이빗 DNS는 다음과 같은 이점을 누릴 수 있습니다.

* **사용자 지정 DNS 솔루션이 필요 없음**. 이전에는 많은 고객이 가상 네트워크에서 DNS 영역을 관리하기 위해 사용자 지정 DNS 솔루션을 만들었습니다. 이제 기본 Azure 인프라를 사용하여 DNS 영역을 관리할 수 있으므로 사용자 지정 DNS 솔루션을 만들고 관리해야 하는 부담이 사라졌습니다.

* **모든 공용 DNS 레코드 형식 사용**. Azure DNS는 A, AAAA, CNAME, MX, PTR, SOA, SRV 및 TXT 레코드를 지원합니다.

* **자동 호스트 레코드 관리**. Azure는 사용자 지정 DNS 레코드를 호스팅할 뿐 아니라 VM의 호스트 이름 레코드를 지정된 가상 네트워크에 자동으로 보존합니다. 이 시나리오에서는 사용자 지정 DNS 솔루션을 만들거나 애플리케이션을 수정할 필요 없이 사용하는 도메인 이름을 최적화할 수 있습니다.

* **가상 네트워크 간의 호스트 이름 확인**. Azure에서 제공하는 호스트 이름과는 달리, 프라이빗 DNS 영역은 가상 네트워크 간에 공유할 수 있습니다. 이 기능은 가상 네트워크 피어링 같은 교차 네트워크 및 서비스 검색 시나리오를 단순화합니다.

* **친숙한 도구 및 사용자 환경**. 이 서비스는 학습 곡선을 단축할 수 있도록 잘 설정된 Azure DNS 도구(Azure Portal, Azure PowerShell, Azure CLI, Azure Resource Manager 템플릿, REST API)를 사용합니다.

* **수평 분할 DNS 지원**. Azure DNS를 사용하면 이름이 같고 가상 네트워크 내 그리고 공용 인터넷의 다른 답변을 확인하는 영역을 만들 수 있습니다. 분할 수평 DNS에 대한 일반적인 시나리오는 가상 네트워크 내에서 사용할 수 있는 전용 서비스 버전을 제공하는 것입니다.

* **모든 Azure 지역에서 사용 가능**. Azure DNS 프라이빗 영역 기능은 Azure 공용 클라우드의 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="capabilities"></a>기능

Azure DNS는 다음과 같은 기능을 제공합니다.

* **자동 등록이 사용 설정된 프라이빗 영역에 연결된 가상 네트워크에서 가상 머신 자동 등록**. 가상 머신이 개인 IP 주소를 가리키는 A 레코드로 프라이빗 영역에 등록(추가)됩니다. 자동 등록이 사용 설정된 가상 네트워크 연결에서 가상 머신이 삭제되면 Azure DNS는 연결된 프라이빗 영역에서 해당 DNS 레코드도 자동으로 제거합니다.

* **프라이빗 영역에 연결된 가상 네트워크 전체에서 정방향 DNS 확인이 지원됩니다**. 가상 네트워크 간 DNS 확인의 경우 가상 네트워크가 서로 연결되는 명시적 종속성은 없습니다. 그러나 다른 시나리오(예: HTTP 트래픽)를 위해 가상 네트워크를 피어링할 수도 있습니다.

* **DNS 역방향 조회는 가상 네트워크 범위 내에서 지원됩니다**. 프라이빗 영역에 할당된 가상 네트워크 내의 프라이빗 IP에 대한 역방향 DNS 조회에서는 호스트/레코드 이름과 영역 이름이 접미사로 포함된 FQDN이 반환됩니다.

## <a name="known-issues"></a>알려진 문제
다음 항목은 미리 보기 릴리스에서 알려진 버그와 문제입니다.
* 프라이빗 DNS 영역에 연결된 가상 네트워크를 삭제해도 프라이빗 DNS 영역에 대한 연결은 삭제되지 않습니다. 동일한 이름과 리소스 그룹으로 가상 네트워크를 다시 만들고 임의의 프라이빗 DNS 영역에 다시 연결하려고 하면 연결이 실패합니다. 이 문제를 해결하려면 가상 네트워크를 다른 리소스 그룹에 만들거나 동일한 리소스 그룹에 다른 이름으로 가상 네트워크를 만듭니다.
* 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동하면 프라이빗 DNS 영역에 대한 연결이 업데이트되지 않습니다. 이동된 가상 네트워크의 이름 확인이 계속 작동하지만 프라이빗 DNS 영역의 가상 네트워크 연결을 볼 때 가상 네트워크의 기존 ARM ID가 표시됩니다.
* 현재 아랍에미리트 북부, 아랍에미리트 중부, 남아프리카 공화국 서부, 남아프리카 공화국 북부, 캐나다 동부, 프랑스 남부에서 호스팅되는 연결된 가상 네트워크는 실패할 수 있으며, 일시적인 DNS 확인 문제가 발생할 수 있습니다. 


## <a name="other-considerations"></a>기타 고려 사항

Azure DNS의 제한 사항은 다음과 같습니다.

* VM DNS 레코드의 자동 등록이 활성화된 경우 특정 가상 네트워크를 하나의 프라이빗 영역에만 연결할 수 있습니다. 그러나 단일 DNS 영역에 여러 가상 네트워크를 연결할 수 있습니다.
* 역방향 DNS는 연결된 가상 네트워크의 개인 IP 공간에 대해서만 작동합니다.
* 연결된 가상 네트워크의 개인 IP에 대한 역방향 DNS는 가상 머신의 기본 접미사로 "internal.cloudapp.net"을 반환합니다. 자동 등록이 사용 설정된 프라이빗 영역에 연결된 가상 네트워크에서는 개인 IP의 역방향 DNS가 FQDN 2개를 반환하는데, 하나는 기본 접미사(*internal.cloudapp.net*)를, 다른 하나는 프라이빗 영역 접미사를 사용합니다.
* 조건부 전달은 현재 기본적으로 지원되지 않습니다. Azure와 온-프레미스 네트워크 간에 확인을 사용하려면 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

 
## <a name="pricing"></a>가격

가격 책정 정보는 [Azure DNS 가격 책정](https://azure.microsoft.com/pricing/details/dns/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 대답은 [프라이빗 DNS FAQ](./dns-faq-private.md)를 참조하세요.

* [DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하여 DNS 영역 및 레코드에 대해 알아봅니다.

* Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.
