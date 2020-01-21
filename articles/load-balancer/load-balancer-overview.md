---
title: Azure Load Balancer란?
titleSuffix: Azure Load Balancer
description: Azure Load Balancer 기능, 아키텍처 및 구현에 대한 개요입니다. Load Balancer의 작동 방법과 클라우드에서 이를 활용하는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045379"
---
# <a name="what-is-azure-load-balancer"></a>Azure Load Balancer란?

*부하 분산*은 백 엔드 리소스 또는 서버의 그룹에서 로드(들어오는 네트워크 트래픽)를 효율적으로 분산하는 것을 의미합니다. Azure는 필요에 따라 선택할 수 있는 [다양한 부하 분산 옵션](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)을 제공합니다. 이 문서에서는 Azure Load Balancer에 대해 설명합니다.

Azure Load Balancer는 OSI(Open Systems Interconnect) 모델의 계층 4에서 작동합니다. 클라이언트의 단일 연락 지점입니다. 부하 분산 장치는 부하 분산 장치의 프런트 엔드에 도착하는 새 인바운드 흐름을 백 엔드 풀 인스턴스에 분산합니다. 이러한 흐름은 구성된 부하 분산 규칙 및 상태 프로브에 따라 진행됩니다. 백 엔드 풀 인스턴스는 Azure Virtual Machines 또는 가상 머신 확장 집합의 인스턴스일 수 있습니다.


**[공용 부하 분산 장치](./concepts-limitations.md#publicloadbalancer)** 를 사용하여 가상 네트워크 내의 VM(가상 머신)에 대해 아웃바운드 연결을 제공합니다. 이러한 연결은 개인 IP 주소를 공용 IP 주소로 변환하여 수행됩니다. 공용 Load Balancer는 인터넷 트래픽 부하를 VM에 분산하는 데 사용됩니다.

**[내부(또는 개인) 부하 분산 장치](./concepts-limitations.md#internalloadbalancer)** 는 사설 IP가 프런트 엔드에서만 필요한 경우에 사용됩니다. 내부 부하 분산 장치는 트래픽 부하를 가상 네트워크 내에 분산하는 데 사용됩니다. 하이브리드 시나리오의 온-프레미스 네트워크에서 부하 분산 장치 프런트 엔드에 액세스할 수 있습니다.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

그림: *퍼블릭 및 내부 Load Balancer를 둘 다 사용하여 다중 계층 애플리케이션 부하 분산*

개별 부하 분산 장치 구성 요소에 대한 자세한 내용은 [Azure Load Balancer 구성 요소 및 제한 사항](./concepts-limitations.md)을 참조하세요.

>[!NOTE]
> Microsoft는 [표준 Load Balancer](./load-balancer-standard-overview.md)를 권장합니다.
독립 실행형 VM, 가용성 집합 및 가상 머신 확장 집합은 하나의 SKU에만 연결될 수 있습니다. 퍼블릭 IP 주소와 함께 사용할 때는 Load Balancer 및 퍼블릭 IP 주소 SKU가 일치해야 합니다. Load Balancer 및 공용 IP SKU는 변경할 수 없습니다.

## <a name="why-use-azure-load-balancer"></a>Azure Load Balancer를 사용하는 이유
Azure Load Balancer를 사용하면 애플리케이션 크기를 조정하고 고가용성 서비스를 만들 수 있습니다. 부하 분산 장치는 인바운드 및 아웃바운드 시나리오를 모두 지원합니다. 부하 분산 장치는 짧은 대기 시간과 높은 처리량을 제공하고, 모든 TCP 및 UDP 애플리케이션에 대해 최대 수백만 개의 흐름으로 확장합니다.

Azure Load Balancer를 사용하여 수행할 수 있는 주요 시나리오는 다음과 같습니다.

- **[내부](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** 및 **[외부](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** 트래픽을 Azure 가상 머신으로 부하를 분산합니다.

- 영역 **[내](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** 및 **[전반에서](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** 리소스를 배포하여 가용성을 높입니다.

- Azure 가상 머신에 대한 **[아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** 을 구성합니다.

- **[상태 프로브](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** 를 사용하여 부하가 분산된 리소스를 모니터링합니다.

- **[포트 전달](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** 을 사용하여 공용 IP 주소 및 포트로 가상 네트워크의 가상 머신에 액세스합니다.

- **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** 의 **[부하 분산](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** 을 위한 지원을 사용하도록 설정합니다.

- **[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)** 와 함께 Azure Load Balancer의 **[메트릭 및 진단](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** 기능을 활용합니다.

- 부하 분산 장치를 **[여러 포트, 여러 IP 주소 또는 둘 다](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** 에서 사용합니다.

- **[내부](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** 및 **[외부](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** 부하 분산 장치 리소스를 Azure 지역에 걸쳐 이동합니다.

- **[HA 포트](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** 를 사용하여 모든 포트에서 TCP 및 UDP 흐름의 부하를 동시에 분산합니다.

## <a name="pricing"></a>가격 책정

표준 Load Balancer 사용량에 해당하는 요금이 청구됩니다.

* 구성된 부하 분산 및 아웃바운드 규칙의 수. 인바운드 NAT 규칙은 총 규칙 수를 계산하지 않습니다.
* 규칙에 관계없이 인바운드 및 아웃바운드에서 처리된 데이터의 양.

표준 Load Balancer 가격 정보에 대해서는 [Load Balancer 가격](https://azure.microsoft.com/pricing/details/load-balancer/)을 참조하세요.

기본 Load Balancer는 무료로 제공됩니다.

## <a name="sla"></a>SLA

표준 Load Balancer SLA에 대한 내용은 [Load Balancer에 대한 SLA](https://aka.ms/lbsla)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Load Balancer를 사용하여 시작하려면 [공용 표준 Load Balancer 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요.

Azure Load Balancer 제한 사항 및 구성 요소에 대한 자세한 내용은 [Azure Load Balancer 개념 및 제한 사항](./concepts-limitations.md)을 참조하세요.
