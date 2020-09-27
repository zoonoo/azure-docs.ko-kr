---
title: Azure 피어 링 서비스 개요
description: Azure 피어 링 서비스 개요에 대 한 자세한 정보
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 03ece391b6f33b47f34705cec1c5907602ac7b23
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400182"
---
# <a name="azure-peering-service-overview"></a>Azure 피어 링 서비스 개요

Azure 피어 링 서비스는 Microsoft 365, Dynamics 365, SaaS (software as a Service) 서비스, Azure 또는 공용 인터넷을 통해 액세스할 수 있는 Microsoft 서비스와 같은 Microsoft 클라우드 서비스에 대 한 고객의 연결을 향상 시키는 네트워킹 서비스입니다. Microsoft는 Isp (인터넷 서비스 공급자), IXPs (인터넷 교환 파트너) 및 전 세계적 SDCI (소프트웨어 정의 클라우드 상호 연결) 공급자와 협력 하 여 고객 으로부터 Microsoft 네트워크로의 최적 라우팅을 통해 안정적이 고 성능이 뛰어난 공용 연결을 제공 합니다.

피어 링 서비스를 사용 하는 경우 고객은 지정 된 지역에서 잘 연결 된 파트너 서비스 공급자를 선택할 수 있습니다. 공용 연결은 클라우드 서비스에서 최종 사용자 위치로의 높은 안정성 및 최소 대기 시간에 최적화 되어 있습니다.

![Microsoft 클라우드에 분산 연결](./media/peering-service-about/peering-service-what.png)

또한 고객은 Azure Portal에서 피어 링 서비스 연결을 등록 하 여 Microsoft 네트워크, BGP 경로 모니터링, 누출 및 하이재킹 등에 대 한 경고와 같은 서비스 원격 분석에 대 한 사용자 대기 시간을 선택할 수 있습니다. 

피어 링 서비스를 사용 하려면 고객이 Microsoft에 등록 하지 않아도 됩니다. 유일한 요구 사항은 서비스를 얻기 위해 [피어 링 서비스 파트너](location-partners.md) 에 게 문의 하는 것입니다. 피어 링 서비스 원격 분석을 옵트인 (opt in) 하려면 고객이 Azure Portal에 등록 해야 합니다.

피어 링 서비스를 등록 하는 방법에 대 한 지침은 [Azure Portal를 사용 하 여 피어 링 서비스 등록](azure-portal.md)을 참조 하세요. 

> [!NOTE]
> 이 문서는 클라우드 및 인터넷에 대 한 엔터프라이즈 연결을 담당 하는 네트워크 설계자를 대상으로 합니다.


## <a name="what-is-peering-service"></a>Peering Service란?

피어 링 서비스:

- 공용 인터넷을 사용 하는 IP 서비스입니다. 
- 서비스 공급자 및 부가 가치 서비스를 제공 하는 공동 작업 플랫폼으로, 공용 네트워크를 통해 서비스 공급자 파트너를 통해 고객에 게 최적의 및 안정적인 라우팅을 제공 하기 위한 서비스입니다.

피어 링 서비스는 Azure Express 경로 또는 VPN 제품과 같은 개인 연결 제품이 아닙니다.

> [!NOTE]
> Express 경로에 대 한 자세한 내용은 [express 경로 설명서](https://docs.microsoft.com/azure/expressroute/)를 참조 하세요.
>

## <a name="background"></a>배경

Microsoft 365, Dynamics 365 및 기타 모든 Microsoft SaaS 서비스는 여러 Microsoft 데이터 센터에서 호스팅되며 모든 지리적 위치에서 액세스할 수 있습니다. Microsoft 글로벌 네트워크에는 서비스 공급자를 통해 최종 사용자에 게 연결할 수 있는 전 세계의 Microsoft Edge PoP (point of point) 위치가 있습니다. 

Microsoft 및 파트너 서비스 공급자는 피어 링 서비스 연결에 등록 된 접두사의 트래픽이 Microsoft 글로벌 네트워크에서 가장 가까운 Microsoft Edge PoP 위치에 들어가거나 종료 되도록 합니다. Microsoft는 피어 링 서비스 연결에 등록 된 접두사에서 네트워크 트래픽이 Microsoft의 글로벌 네트워크에서 가장 가까운 Microsoft Edge PoP 위치를 사용 하도록 보장 합니다.

![Microsoft 네트워크 및 공용 연결](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Microsoft 글로벌 네트워크에 대 한 자세한 내용은 [microsoft 글로벌 네트워크](https://docs.microsoft.com/azure/networking/microsoft-global-network)를 참조 하세요.
>

## <a name="why-use-peering-service"></a>피어 링 서비스를 사용 하는 이유

인터넷에 대 한 인터넷 우선 액세스를 찾고 SD WAN 아키텍처를 고려 하거나 Microsoft SaaS 서비스를 많이 사용 하는 기업은 강력 하 고 고성능의 인터넷 연결이 필요 합니다. 고객은 피어 링 서비스를 사용 하 여 이러한 전환을 수행할 수 있습니다. Microsoft 및 서비스 공급자는 Microsoft 클라우드에 안정적이 고 성능이 뛰어난 공용 연결을 제공 하기 위해 파트너 관계를 맺고 있습니다. 주요 고객 기능 중 일부는 다음과 같습니다.

- 최적의 성능 및 안정성을 위해 Microsoft Azure Cloud Services 하기 위해 인터넷을 통해 가장 적합 한 공용 라우팅입니다.
- 기본 설정 서비스 공급자를 선택 하 여 Microsoft 클라우드에 연결 하는 기능입니다.
- 대기 시간 보고 및 접두사 모니터링과 같은 트래픽 통찰력.
- Microsoft 클라우드에서 최적의 네트워크 홉 (홉).
- 경로 분석 및 통계: ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) 경로 변칙 (누수 또는 가로채기 검색) 및 최적이 아닌 라우팅에 대 한 이벤트입니다.

### <a name="robust-reliable-peering"></a>강력 하 고 신뢰할 수 있는 피어 링

피어 링 서비스는 두 가지 유형의 중복성을 사용 합니다.

- **로컬 중복성**

   Microsoft 및 서비스 공급자는 여러 Microsoft Edge PoP 위치를 상호 연결 하 여 피어 링 서비스를 제공 합니다. 각 위치에서 상호는 두 개의 라우터 간 장애 조치 (failover)를 지원 해야 합니다.

   각 피어 링 위치는 중복 및 다양 한 피어 링 링크를 사용 하 여 프로 비전 됩니다.

- **지리적 중복**

   Microsoft는에 지 노드 중 하나가 성능이 저하 되는 경우 트래픽이 대체 사이트를 통해 Microsoft와 상호 연결 되도록 여러 metro 위치에서 서비스 공급자와 상호 연결 되어 있습니다. Microsoft는 최적의 성능을 위해 SDN 기반 라우팅 정책을 사용 하 여 글로벌 네트워크에서 트래픽을 라우팅합니다.

    이 유형의 중복성은 항상 가장 가까운 Microsoft Edge PoP를 최종 사용자에 게 선택 하 여 가장 짧은 라우팅 경로를 사용 하 고 고객은 Microsoft에서 멀리 떨어진 단일 네트워크 홉 (홉)을 보장 합니다.

   ![지리적 중복](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>최적 라우팅

기본 설정 된 라우팅 기술은 다음과 같습니다.

-  **콜드 감자 라우팅**

   소프트웨어 정의 감자 라우팅 기술은 Microsoft 클라우드에서 발생 하는 네트워크 트래픽을 제어 하는 기능을 제공 합니다. 이를 통해 트래픽이 가능한 한 대상에 근접 하 게 될 때까지 고용량, 낮은 대기 시간 및 매우 안정적인 Microsoft 글로벌 네트워크에 유지 됩니다.
   
   콜드 감자 기술을 사용 하지 않는 라우팅은 핫-감자 라우팅 이라고 합니다. 핫 감자 라우팅은 Microsoft 클라우드에서 발생 하는 트래픽을 인터넷을 통해 이동 합니다.

   ![콜드 감자 라우팅](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>모니터링 플랫폼

   서비스 모니터링은 고객 트래픽과 라우팅을 분석 하기 위해 제공 되며 다음과 같은 기능을 제공 합니다. 

-  **인터넷 BGP 경로 변칙 검색**
          
   이 서비스는 하이재킹 등 경로와 같은 모든 경로 변칙 이벤트를 검색 하 고 고객 접두사에 대 한 경고를 생성 하는 데 사용 됩니다.

-  **고객 대기 시간**

   이 서비스는 고객의 위치와 Microsoft 간의 라우팅 성능을 모니터링 합니다. 
   
   라우팅 성능은 클라이언트가 Microsoft Edge PoP에 도달 하는 데 걸린 왕복 시간을 확인 하 여 측정 합니다. 고객은 여러 지리적 위치에 대 한 대기 시간 보고서를 볼 수 있습니다.

   모니터링은 서비스 저하 시 이벤트를 캡처합니다.

   ![피어 링 서비스에 대 한 모니터링 플랫폼](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>트래픽 보호

라우팅은 고객이 피어 링 서비스를 사용 하 여 등록할 때 정의 되는 기본 설정 경로를 통해서만 발생 합니다.

Microsoft는 악성 작업이 검색 된 경우에도 기본 경로를 통해 트래픽을 라우팅하도록 보장 합니다.

BGP 경로 변칙은 Azure Portal에 보고 됩니다 (있는 경우).

## <a name="next-steps"></a>다음 단계

- 피어 링 서비스 연결에 대 한 자세한 내용은 [서비스 연결 피어 링](connection.md)을 참조 하세요.
- Peering Service 연결 원격 분석에 대해 알아보려면 [Peering Service 연결 원격 분석](connection-telemetry.md)을 참조하세요.
- 서비스 공급자 파트너를 찾으려면 [서비스 파트너 및 위치 피어 링](location-partners.md)을 참조 하세요.
- 피어 링 서비스 연결을 등록 하려면 [온 보 딩 서비스 모델](onboarding-model.md)등록을 참조 하세요.
- Azure Portal를 사용 하 여 연결을 등록 하려면 [Azure Portal를 사용 하 여 피어 링 서비스 연결 등록](azure-portal.md)을 참조 하세요.
- 원격 분석을 측정하려면 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조하세요.
