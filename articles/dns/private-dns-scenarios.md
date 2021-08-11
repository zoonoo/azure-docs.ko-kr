---
title: Private Zones 시나리오 - Azure DNS
description: 이 문서에서는 Azure DNS Private Zones 사용에 대한 일반적인 시나리오에 대해 알아봅니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/27/2021
ms.author: rohink
ms.openlocfilehash: 5a2572af0fd312efeacb8544b653db1b35809244
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108127824"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS Private Zones 시나리오

Azure DNS Private Zones는 가상 네트워크 간뿐 아니라 가상 네트워크 내에서의 이름 확인을 제공합니다. 이 문서에서는 이 기능을 사용하여 혜택을 받을 수 있는 몇 가지 일반적인 시나리오를 살펴봅니다.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>시나리오: 단일 가상 네트워크로 범위가 지정된 이름 확인

이 시나리오에서는 Azure에 가상 머신을 포함한 많은 리소스가 있는 가상 네트워크가 있습니다. 사용자의 요구 사항은 특정 도메인 이름(DNS 영역)을 사용하여 가상 네트워크의 모든 리소스를 확인하는 것입니다. 또한 이름 확인은 비공개여야 하며 인터넷에서 액세스할 수 없어야 합니다. 마지막으로, VM을 DNS 영역에 자동으로 등록하려면 Azure가 필요합니다.

이 시나리오는 다음과 같습니다. 이름이 “A”인 가상 네트워크에는 2개의 VM(VNETA-VM1 및 VNETA-VM2)이 포함되어 있습니다. 각 VM에는 개인 IP가 연결되어 있습니다. 개인 영역을 만들고(예: `contoso.com`) 가상 네트워크 "A"를 등록 가상 네트워크로 연결합니다. Azure DNS는 두 VM을 참조하는 영역에 2개의 A 레코드를 자동으로 만듭니다. `VNETA-VM2.contoso.com`을 확인하기 위한 VNETA-VM1의 DNS 쿼리가 VNETA-VM2의 개인 IP를 포함하는 DNS 응답을 받게 됩니다.
VNETA-VM2에서 VNETA-VM1(10.0.0.1)의 개인 IP에 대한 역방향 DNS 쿼리(PTR)를 수행할 수도 있습니다. DNS 응답은 예상대로 이름 VNETA-VM1을 포함합니다. 

![단일 가상 네트워크 확인](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>시나리오: 가상 네트워크에서 이름 확인

이 시나리오에서는 개인 영역을 여러 가상 네트워크와 연결해야 합니다. 허브 및 스포크 모델 등의 다양한 네트워크 아키텍처에서 이 솔루션을 구현할 수 있습니다. 이 구성은 중앙 허브 가상 네트워크를 사용하여 여러 스포크 가상 네트워크를 함께 연결하는 경우입니다. 중앙 허브 가상 네트워크는 등록 가상 네트워크로 연결될 수 있으며, 스포크 가상 네트워크는 확인 가상 네트워크로 연결될 수 있습니다. 

다음 다이어그램에는 A와 B라는 2개의 가상 네트워크만 있는 이 시나리오의 간단한 버전이 나와 있습니다. A는 등록 가상 네트워크로 지정되고 B는 확인 가상 네트워크로 지정됩니다. 의도는 두 가상 네트워크가 모두 공용 영역 `contoso.com`을 공유하는 것입니다. 영역을 만들 때 등록으로 정의된 가상 네트워크는 가상 네트워크(VNETA-VM1 및 VNETA-VM2)의 VM에 대한 DNS 레코드를 자동으로 등록합니다. 확인 가상 네트워크 B에서 VM에 대한 영역에 DNS 레코드를 수동으로 추가할 수도 있습니다. 이 설정을 사용하면 전달 및 역방향 DNS 쿼리에 대한 다음 동작을 확인할 수 있습니다.
* 확인 가상 네트워크에서 VNETB-VM1의 DNS 쿼리는(VNETA-VM1.contoso.com의 경우) VNETA-VM1의 프라이빗 IP를 포함하는 DNS 응답을 받게 됩니다.
* 확인 가상 네트워크 B에서 VNETB-VM2의 역방향 DNS(PTR) 쿼리는(10.1.0.1의 경우) FQDN VNETB-VM1.contoso.com을 포함하는 DNS 응답을 받게 됩니다.  
* 확인 네트워크 B에서 VNETB-VM3의 역방향 DNS(PTR) 쿼리는(10.0.0.1의 경우) NXDOMAIN을 받게 됩니다. 이유는 역방향 DNS 쿼리의 범위가 동일한 가상 네트워크로만 지정되어 있기 때문입니다. 

![여러 가상 네트워크 확인](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>시나리오: 분할-수평 기능

이 시나리오에서는 동일한 DNS 영역에 대한 클라이언트가 있는 위치에 따라 다른 이름 확인이 필요합니다. 기능이나 동작이 다른 프라이빗 및 퍼블릭 버전의 애플리케이션이 있을 수 있습니다. 두 버전 모두에 동일한 도메인 이름을 사용해야 합니다. 이 시나리오는 Azure DNS에서 동일한 이름의 공용 영역 및 개인 영역을 만들어 수행할 수 있습니다. 

다음 다이어그램에는 이 시나리오가 나와 있습니다. 가상 네트워크에는 2개의 VM(VNETA-VM1 및 VNETA-VM2)이 포함되어 있습니다. 둘 다 개인 IP와 공용 IP가 구성되어 있습니다. `contoso.com`이라는 공용 DNS 영역을 만들고 영역 내에서 DNS 레코드로 이러한 VM에 대한 공용 IP를 등록합니다. `contoso.com`이라는 개인 DNS 영역이 생성됩니다. 가상 네트워크 A를 등록 가상 네트워크로 정의했습니다. Azure는 자동으로 프라이빗 IP를 가리키는 개인 영역에 VM을 A 레코드로 등록합니다.

이제 인터넷 클라이언트가 `VNETA-VM1.contoso.com`에 대한 DNS 쿼리를 수행하면 Azure는 자동으로 공용 영역에서 공용 IP 레코드를 반환합니다. 동일한 DNS 쿼리가 동일한 가상 네트워크 A의 다른 VM(예: VNETA-VM2)에서 발급되는 경우 Azure는 개인 영역에서 프라이빗 IP 레코드를 반환합니다. 

![분할 Brian 확인](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>다음 단계
프라이빗 DNS 영역에 대해 자세히 알아보려면 [프라이빗 도메인에 Azure DNS 사용](private-dns-overview.md)을 참조하세요.

Azure DNS에서 [프라이빗 DNS 영역을 만드는](./private-dns-getstarted-powershell.md) 방법을 알아봅니다.

[DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하여 DNS 영역 및 레코드에 대해 알아봅니다.

Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/fundamentals/networking-overview.md)을 알아봅니다.
