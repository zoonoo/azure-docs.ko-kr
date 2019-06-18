---
title: Azure 사설 DNS 란?
description: Microsoft Azure의 프라이빗 DNS 호스팅 서비스에 대한 개요입니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: a8548b4972d5853f09630ae3e9ded05ed6fee32b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076411"
---
# <a name="what-is-azure-private-dns"></a>Azure 사설 DNS 란?

> [!IMPORTANT]
> Azure 사설 DNS는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

DNS(Domain Name System)는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다.  Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. 인터넷 연결 DNS 도메인을 지원할 뿐 아니라 Azure DNS는 사설 DNS 영역도 지원 합니다.

Azure 사설 DNS를 관리 하 고 사용자 지정 DNS 솔루션을 추가할 필요 없이 가상 네트워크에서 도메인 이름을 확인 하는 안정적이 고 안전한 DNS 서비스를 제공 합니다. 프라이빗 DNS 영역을 사용하면 현재 Azure에서 제공하는 이름 대신 사용자 고유의 사용자 지정 도메인 이름을 사용할 수 있습니다. 사용자 지정 도메인 이름을 사용하면 조직의 요구 사항에 가장 적합하도록 가상 네트워크 아키텍처를 조정할 수 있습니다. 가상 네트워크 내에서 그리고 가상 네트워크 간에 VM(가상 머신)에 대한 이름을 확인할 수 있게 해줍니다. 또한 분할-수평 보기를 사용해 영역을 구성할 수도 있습니다. 그러면 프라이빗 DNS 영역과 공용 DNS 영역이 이름을 공유할 수 있습니다.

가상 네트워크에서 개인 DNS 영역의 레코드를 해결 하려면 영역을 사용 하 여 가상 네트워크를 연결 해야 합니다. 연결 된 가상 네트워크 전체 액세스할 수 있으며 개인 영역에 게시 된 모든 DNS 레코드를 확인할 수 있습니다. 또한 가상 네트워크 링크에 대 한 자동 등록을 사용할 수 있습니다. 가상 네트워크 링크에 대 한 자동 등록을 사용 하도록 설정 하는 경우 해당 가상 네트워크의 가상 컴퓨터에 대 한 DNS 레코드는 개인 영역에 등록 됩니다. Azure DNS 가상 컴퓨터를 만들 때마다 변경도 영역의 레코드를 업데이트할 경우 자동 등록을 사용 하는 해당 ' IP 주소 또는 삭제 됩니다.

![DNS 개요](./media/private-dns-overview/scenario.png)

> [!NOTE]
> 모범 사례에 사용 하지 않는 한 *.local* 개인 DNS 영역에 대 한 도메인입니다. 일부 운영 체제는이 지원 합니다.

## <a name="benefits"></a>이점

Azure 사설 DNS를 다음과 같은 이점이 있습니다.

* **사용자 지정 DNS 솔루션이 필요 없음**. 이전에는 많은 고객이 가상 네트워크에서 DNS 영역을 관리하기 위해 사용자 지정 DNS 솔루션을 만들었습니다. 이제 사용자 지정 DNS 솔루션의 만들기 및 관리 부담을 제거 하는 기본 Azure 인프라를 사용 하 여 DNS 영역을 관리할 수 있습니다.

* **모든 공용 DNS 레코드 형식 사용**. Azure DNS는 A, AAAA, CNAME, MX, PTR, SOA, SRV 및 TXT 레코드를 지원합니다.

* **자동 호스트 레코드 관리**. Azure는 사용자 지정 DNS 레코드를 호스팅할 뿐 아니라 VM의 호스트 이름 레코드를 지정된 가상 네트워크에 자동으로 보존합니다. 이 시나리오에서는 사용자 지정 DNS 솔루션을 만들거나 애플리케이션을 수정할 필요 없이 사용하는 도메인 이름을 최적화할 수 있습니다.

* **가상 네트워크 간의 호스트 이름 확인**. Azure에서 제공하는 호스트 이름과는 달리, 프라이빗 DNS 영역은 가상 네트워크 간에 공유할 수 있습니다. 이 기능은 가상 네트워크 피어링 같은 교차 네트워크 및 서비스 검색 시나리오를 단순화합니다.

* **친숙한 도구 및 사용자 환경**. 학습 곡선을 줄이기 위해이 서비스는 잘 구성 된 Azure DNS 도구 (Azure portal, Azure PowerShell, Azure CLI, Azure Resource Manager 템플릿 및 REST API)를 사용 합니다.

* **수평 분할 DNS 지원**. Azure DNS를 사용하면 이름이 같고 가상 네트워크 내 그리고 공용 인터넷의 다른 답변을 확인하는 영역을 만들 수 있습니다. 분할 수평 DNS에 대한 일반적인 시나리오는 가상 네트워크 내에서 사용할 수 있는 전용 서비스 버전을 제공하는 것입니다.

* **모든 Azure 지역에서 사용 가능**. Azure DNS 프라이빗 영역 기능은 Azure 공용 클라우드의 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="capabilities"></a>기능

Azure DNS는 다음과 같은 기능을 제공합니다.

* **사용 하도록 설정 하는 자동 등록을 사용 하 여 개인 영역에 연결 된 가상 네트워크에서 가상 컴퓨터의 자동 등록**합니다. 가상 머신은 등록 (추가) 개인 영역에 해당 개인 IP 주소를 가리키는 A 레코드로 합니다. 사용 하도록 설정 하는 자동 등록을 사용 하 여 가상 네트워크 링크의 가상 컴퓨터를 삭제 하면 Azure DNS는 자동으로 해당 DNS 레코드를 연결된 된 개인 영역에서 제거 합니다.

* **정방향 DNS 확인 개인 영역에 연결 된 가상 네트워크에서 지원 됩니다**합니다. 가상 네트워크 간 DNS 확인에 대 한 종속성이 없으므로 명시적는 서로 피어 링 된 가상 네트워크. 그러나 다음 다른 시나리오 (예를 들어, HTTP 트래픽)에 대 한 가상 네트워크를 피어 링 하는 것이 좋습니다.

* **DNS 역방향 조회는 가상 네트워크 범위 내에서 지원됩니다**. 프라이빗 영역에 할당된 가상 네트워크 내의 프라이빗 IP에 대한 역방향 DNS 조회에서는 호스트/레코드 이름과 영역 이름이 접미사로 포함된 FQDN이 반환됩니다.

## <a name="other-considerations"></a>기타 고려 사항

Azure DNS의 제한 사항은 다음과 같습니다.

* 프라이빗 영역당 하나의 등록 가상 네트워크만 허용됩니다.
* 프라이빗 영역당 최대 10개의 확인 가상 네트워크가 허용됩니다. 이 한도는 이 기능이 일반 공급될 때 제거됩니다.
* 특정 가상 네트워크가 1개의 프라이빗 영역에만 등록 가상 네트워크로 연결될 수 있습니다.
* 특정 가상 네트워크가 최대 10개의 프라이빗 영역에 확인 가상 네트워크로 연결될 수 있습니다. 이 한도는 이 기능이 일반 공급될 때 제거됩니다.
* 등록 가상 네트워크를 지정하는 경우, 프라이빗 영역에 등록된 해당 가상 네트워크의 VM에 대한 DNS 레코드는 Azure Powershell 및 Azure CLI API에서 보거나 검색할 수 없습니다. VM 레코드는 실제로 등록되어 성공적으로 확인됩니다.
* 역방향 DNS는 등록 가상 네트워크의 프라이빗 IP 공간에 대해서만 작동합니다.
* 프라이빗 영역에 등록되지 않은 프라이빗 IP(예: 확인 가상 네트워크로 프라이빗 영역에 연결된 가상 네트워크에 있는 가상 머신의 프라이빗 IP)에 대한 역방향 DNS는 DNS 접미사로 *internal.cloudapp.net*을 반환합니다. 그러나 이 접미사를 확인할 수 없습니다.
* 가상 네트워크는 등록 또는 확인 가상 네트워크로 프라이빗 영역에 처음 연결할 때 완전히 비어 있어야 합니다. 그러나 가상 네트워크가 나중에 등록 또는 확인 가상 네트워크로 다른 프라이빗 영역에 연결할 때는 비어 있지 않을 수 있습니다.
* 현재는 Azure와 온-프레미스 네트워크 간 확인이 가능하도록 하려는 등의 경우 조건부 전달이 지원되지 않습니다. 고객이 다른 메커니즘을 통해 이 시나리오를 실현할 수 있는 방법에 대한 자세한 내용은 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.
* 연결 된 가상 네트워크의 개인 IP 공간에 대해서만 역방향 DNS 작동
* 연결된 된 가상 네트워크에 대 한 개인 IP에 대 한 역방향 DNS 가상 컴퓨터에 대 한 기본 접미사로 "internal.cloudapp.net"를 반환합니다. 개인 IP를 반환 하나 이며 기본값은 2 Fqdn 접미사에 대 한 역방향 DNS 사설 영역을 사용 하도록 설정 하는 자동 등록을 사용 하 여 연결 된 가상 네트워크용 *internal.cloudapp.net* 및 다른 개인 영역 접미사를 사용 합니다.
* 조건부 전달은 지원 되지 않습니다. 예를 들어 Azure 및 온-프레미스 네트워크 간에 확인을 사용 합니다. 다른 메커니즘을 사용 하 여이 시나리오를 설정 하는 방법에 대해 알아봅니다. [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.
 


## <a name="pricing"></a>가격

가격 정보를 참조 하세요 [Azure DNS 가격 책정](https://azure.microsoft.com/pricing/details/dns/)합니다.

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 사설 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 일반적인 질문 및 Azure DNS에서 사설 영역에 대 한 답변에 대 한 특정 동작을 포함 하 여 있습니다 특정 작업의 종류에 필요한을 참조 하세요 [사설 DNS FAQ](./dns-faq-private.md)합니다.

* [DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하여 DNS 영역 및 레코드에 대해 알아봅니다.

* Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.
