---
title: Azure Peering Service 개요
description: Azure Peering Service에 대해 알아보기 개요
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026732"
---
# <a name="azure-peering-service-overview"></a>Azure Peering Service 개요

Azure Peering Service는 Microsoft 365, Dynamics 365, SaaS(Software as a Service) 서비스, Azure 또는 공용 인터넷을 통해 액세스할 수 있는 Microsoft 서비스와 같은 Microsoft 클라우드 서비스에 대한 고객의 연결을 향상시키는 네트워킹 서비스입니다. Microsoft는 전 세계 ISP(인터넷 서비스 공급자), IXP(인터넷 교환 파트너) 및 SDCI(소프트웨어 정의 클라우드 인터커넥터) 공급자와 제휴하여 고객에게 최적의 라우팅을 통해 안정적이고 성능이 뛰어난 공용 연결을 Microsoft 네트워크로 제공합니다.

Peering Service를 사용하면 고객은 주어진 지역에서 잘 연결된 파트너 서비스 공급자를 선택할 수 있습니다. 공용 연결은 클라우드 서비스에서 최종 사용자 위치까지의 높은 안정성 및 최소 대기 시간에 최적화되어 있습니다.

![Microsoft 클라우드로 분산된 연결](./media/peering-service-about/peering-service-what.png)

또한 고객은 Azure Portal에서 Peering Service 연결을 등록하여 Microsoft 네트워크에 대한 사용자 대기 시간 측정값, BGP 경로 모니터링, 누출 및 하이재킹에 대한 경고 등의 Peering Service 원격 분석을 선택할 수 있습니다. 

고객은 Microsoft에 등록하지 않아도 Peering Service를 사용할 수 있습니다. 서비스를 받기 위해 [Peering Service 파트너](location-partners.md)에게 문의하기만 하면 됩니다. Peering Service 원격 분석에 옵트인하려면 고객이 Azure Portal에 등록해야 합니다.

Peering Service를 등록하는 방법에 대한 지침은 [Azure Portal를 사용하여 Peering Service 등록](azure-portal.md)을 참조하세요. 

> [!NOTE]
> 이 문서는 클라우드 및 인터넷과의 엔터프라이즈 연결을 담당하는 네트워크 설계자를 위한 문서입니다.


## <a name="what-is-peering-service"></a>Peering Service란?

Peering Service:

- 공용 인터넷을 사용하는 IP 서비스입니다. 
- 서비스 공급자 및 부가 가치 서비스가 있는 공동 작업 플랫폼으로, 공용 네트워크를 통해 서비스 공급자 파트너를 통해 고객에게 최적의 안정적인 라우팅을 제공하기 위한 서비스입니다.

Peering Service는 Azure ExpressRoute 또는 VPN 제품과 같은 프라이빗 연결 제품이 아닙니다.

> [!NOTE]
> ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](../expressroute/index.yml)를 참조하세요.
>

## <a name="background"></a>배경

Microsoft 365, Dynamics 365 및 기타 모든 Microsoft SaaS 서비스는 여러 Microsoft 데이터 센터에서 호스팅되며 모든 지리적 위치에서 액세스할 수 있습니다. Microsoft 글로벌 네트워크에는 서비스 공급자를 통해 최종 사용자에게 연결할 수 있는 전 세계의 Microsoft Edge PoP(point-of-presence) 위치가 있습니다. 

Microsoft 및 파트너 서비스 공급자는 Peering Service 연결에 등록된 접두사에 대한 트래픽이 Microsoft 글로벌 네트워크에서 가장 가까운 Microsoft Edge PoP 위치에서 들어오고 나가도록 합니다. Microsoft는 Peering Service 연결에 등록된 접두사에서 송신하는 네트워킹 트래픽이 Microsoft 글로벌 네트워크에서 가장 가까운 Microsoft Edge PoP 위치를 차지하도록 합니다.

![Microsoft 네트워크 및 공용 연결](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Microsoft 글로벌 네트워크에 대한 자세한 내용은 [Microsoft 글로벌 네트워크](../networking/microsoft-global-network.md)를 참조하세요.
>

## <a name="why-use-peering-service"></a>Peering Service를 사용하는 이유

클라우드에 대한 인터넷 우선 액세스를 찾고 있거나, SD-WAN 아키텍처를 고려하거나, Microsoft SaaS 서비스의 사용량이 높은 기업은 강력한 고성능 인터넷 연결이 필요합니다. 고객은 Peering Service를 사용하여 이러한 전환을 해낼 수 있습니다. Microsoft 및 서비스 공급자는 Microsoft 클라우드에 안정적이고 성능 중심의 공용 연결을 제공하기 위해 파트너 관계를 맺었습니다. 주요 고객 기능 중 일부는 다음과 같습니다.

- 최적의 성능 및 안정성을 위해 인터넷을 통해 Microsoft Azure Cloud Services에 가장 적합한 공용 라우팅.
- Microsoft 클라우드에 연결할 기본 서비스 공급자를 선택하는 기능.
- 대기 시간 보고 및 접두사 모니터링과 같은 트래픽 인사이트.
- Microsoft 클라우드에서의 최적의 네트워크 홉(AS 홉).
- 경로 분석 및 통계: 최적이 아닌 라우팅 및 ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) 경로 변칙(누수 또는 하이재킹 탐지)에 대한 이벤트

### <a name="robust-reliable-peering"></a>신뢰할 수 있는 강력한 피어링

Peering Service는 두 가지 유형의 중복도를 사용합니다.

- **로컬 중복도**

   Microsoft 및 서비스 공급자는 여러 Microsoft Edge PoP 위치에서 상호 연결하여 Peering Service를 제공합니다. 각 위치에서 상호 연결은 두 라우터 간의 장애 조치(failover)를 지원해야 합니다.

   각 피어링 위치는 다양한 중복된 피어링 링크로 프로비전됩니다.

- **지리적 중복도**

   Microsoft는 에지 노드 중 하나의 성능이 저하된 경우 트래픽이 대체 사이트를 통해 Microsoft로 그리고 Microsoft에서 라우팅되도록 여러 도시 위치에서 서비스 공급자와 상호 연결되었습니다. Microsoft는 최적의 성능을 위해 SDN 기반 라우팅 정책을 사용하여 글로벌 네트워크에서 트래픽을 라우팅합니다.

    이 유형의 중복도는 최종 사용자에 대해 항상 가장 가까운 Microsoft Edge PoP를 선택하여 가장 짧은 라우팅 경로를 사용하고 고객이 Microsoft에서 하나의 네트워크 홉(AS홉)만큼 떨어져 있도록 보장합니다.

   ![지리적 중복](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>최적 라우팅

기본 설정된 라우팅 기술은 다음과 같습니다.

-  **콜드 포테이토 라우팅**

   소프트웨어 정의 콜드 포테이토 라우팅 기술은 Microsoft 클라우드에서 발생하는 네트워크 트래픽을 제어하는 기능을 제공합니다. 이를 통해 트래픽은 가능한 한 대상에 가까워질 때까지 고용량, 낮은 대기 시간 및 매우 안정적인 Microsoft 글로벌 네트워크에 유지 됩니다.
   
   콜드 포테이토 기술을 사용하지 않는 라우팅은 핫 포테이토 라우팅이라고 합니다. 핫 포테이토 라우팅의 경우 Microsoft 클라우드에서 발생하는 트래픽은 인터넷으로 이동합니다.

   ![콜드 포테이토 라우팅](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>모니터링 플랫폼

   서비스 모니터링은 고객 트래픽 및 라우팅을 분석하기 위해 제공되며 다음과 같은 기능을 제공합니다. 

-  **인터넷 BGP 경로 변칙 탐지**
          
   이 서비스는 고객 접두사에 대한 경로 하이재킹과 같은 경로 변칙 이벤트를 탐지하고 경고하는 데 사용됩니다.

-  **고객 대기 시간**

   이 서비스는 고객의 위치와 Microsoft 간의 라우팅 성능을 모니터링합니다. 
   
   라우팅 성능은 클라이언트가 Microsoft Edge PoP에 도달하는 데 걸린 왕복 시간을 확인하여 측정됩니다. 고객은 여러 지리적 위치에 대한 대기 시간 보고서를 볼 수 있습니다.

   모니터링은 서비스 저하 시 이벤트를 캡처합니다.

   ![Peering Service에 대한 모니터링 플랫폼](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>트래픽 보호

라우팅은 고객이 Peering Service에 등록할 때 정의된 기본 경로를 통해서만 발생합니다.

Microsoft는 악의적인 활동이 탐지되더라도 기본 경로를 통해 트래픽을 라우팅하도록 보장합니다.

BGP 경로 변칙이 있는 경우 Azure Portal에서 보고됩니다.

## <a name="next-steps"></a>다음 단계

- Peering Service 연결에 대해 알아보려면 [Peering Service 연결](connection.md)을 참조하세요.
- Peering Service 연결 원격 분석에 대해 알아보려면 [Peering Service 연결 원격 분석](connection-telemetry.md)을 참조하세요.
- 서비스 공급자 파트너를 찾으려면 [Peering Service 파트너 및 위치](location-partners.md)를 참조하세요.
- Peering Service 연결을 온보딩하려면 [Peering Service 모델 온보딩](onboarding-model.md)을 참조하세요.
- Azure Portal을 사용하여 Peering Service 연결을 등록하려면 [Azure Portal을 사용하여 Peering Service 연결 등록](azure-portal.md)을 참조하세요.
- 원격 분석을 측정하려면 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조하세요.