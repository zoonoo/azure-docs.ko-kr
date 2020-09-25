---
title: 영역 간 부하 분산 장치 (미리 보기)
titleSuffix: Azure Load Balancer
description: Azure Load Balancer에 대 한 지역 간 부하 분산 장치 계층 개요.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: d55f52b5e99a7a617e2bec8bea4d6e6ef687730a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336533"
---
# <a name="cross-region-load-balancer-preview"></a>영역 간 부하 분산 장치 (미리 보기)

Azure Load Balancer 부하 분산 장치 프런트 엔드에 도착 하는 인바운드 트래픽을 백 엔드 풀 인스턴스에 배포 합니다.

Azure 표준 Load Balancer는 다음과 같은 지역 중복 HA 시나리오를 가능 하 게 하는 지역 간 부하 분산을 지원 합니다.

* 여러 지역에서 발생 하는 들어오는 트래픽
* 다음으로 최적의 지역 배포에 대 한 [즉각적인 글로벌 장애 조치 (failover)](#regional-redundancy)
* 매우 [대기 시간](#ultra-low-latency)으로 가장 가까운 Azure 지역에 지역 간 배포를 로드 합니다.
* 단일 끝점 뒤에 [확장/축소](#ability-to-scale-updown-behind-a-single-endpoint) 하는 기능
* [고정 IP](#static-ip)
* [클라이언트 IP 유지](#client-ip-preservation)
* 학습 곡선이 없는 [기존 부하 분산 장치 솔루션에 빌드](#build-cross-region-solution-on-existing-azure-load-balancer)

> [!IMPORTANT]
> 영역 간 부하 분산 장치는 현재 미리 보기로 제공 되며 일반적으로 사용할 수 없습니다.  지역 간 부하 분산 장치에 대 한 미리 보기에 액세스 하려면에 문의 [crossregionlb@microsoft.com](mailto:crossregionlb@microsoft.com) 하세요. </br> </br>
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

지역 간 부하 분산은 지역 표준 부하 분산 장치 처럼 고성능 및 짧은 대기 시간으로 동일한 이점을 제공 합니다. 

지역 간 부하 분산 장치의 프런트 엔드 IP 구성은 정적 이며 [대부분의 Azure 지역](#participating-regions)에서 보급 됩니다.

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="영역 간 부하 분산 장치 다이어그램." border="true":::

> [!NOTE]
> 영역 간 부하 분산 장치에 대 한 부하 분산 규칙 부하의 백 엔드 포트는 지역 표준 부하 분산 장치에 대 한 부하 분산 규칙/인바운드 nat 규칙의 프런트 엔드 포트와 일치 해야 합니다. 

### <a name="regional-redundancy"></a>지역 중복

전역 프런트 엔드 공용 IP 주소를 기존 부하 분산 장치에 추가 하 여 지역별 중복성을 구성 합니다. 

한 지역이 실패 하는 경우 트래픽이 가장 가까운 다음으로 정상적인 지역 부하 분산 장치로 라우팅됩니다.  

지역 간 부하 분산 장치의 상태 프로브는 20 초 마다 가용성에 대 한 정보를 수집 합니다. 한 지역 부하 분산 장치에서 가용성을 0으로 설정 하면 지역 간 부하 분산 장치에서 오류를 검색 합니다. 그러면 지역 부하 분산 장치는 순환에서 제외 됩니다. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="글로벌 지역 트래픽 보기의 다이어그램입니다." border="true":::

### <a name="ultra-low-latency"></a>매우 낮은 대기 시간

지리적 근접 부하 분산 알고리즘은 사용자 및 지역 배포의 지리적 위치를 기반으로 합니다. 

클라이언트에서 시작 되는 트래픽은 가장 근접 하 게 참여 하는 지역에 도달 하 여 가장 가까운 지역 배포에 도달할 수 있도록 Microsoft 글로벌 네트워크 백본으로 이동 합니다. 

예를 들어 Azure 지역에서 표준 부하 분산 장치를 사용 하는 지역 간 부하 분산 장치를 사용할 수 있습니다.

* 미국 서부
* 북유럽

시애틀에서 흐름이 시작 되 면 트래픽이 미국 서 부로 전환 됩니다. 이 지역은 시애틀에서 가장 가까운 참여 지역입니다. 트래픽이 가장 가까운 지역 부하 분산 장치로 라우팅됩니다 .이는 미국 서 부입니다.

Azure 지역 간 부하 분산 장치는 라우팅 의사 결정을 위해 지리적으로 근접 한 부하 분산 알고리즘을 사용 합니다. 

지역 부하 분산 장치의 구성 된 부하 분산 모드는 지리적 근접성에 여러 지역 부하 분산 장치를 사용 하는 경우 최종 라우팅 결정을 내리는 데 사용 됩니다.

자세한 내용은 [Azure Load Balancer의 배포 모드 구성](https://docs.microsoft.com/azure/load-balancer/load-balancer-distribution-mode)을 참조하세요.


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>단일 끝점을 확장/축소 하는 기능

지역 간 부하 분산 장치의 글로벌 끝점을 고객에 게 노출 하는 경우 고객에 게 영향을 주지 않고 글로벌 끝점 뒤에 지역 배포를 추가 하거나 제거할 수 있습니다. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>고정 IP
영역 간 부하 분산 장치에는 IP 주소가 동일 하 게 유지 되도록 하는 고정 공용 IP가 제공 됩니다. 고정 IP에 대해 자세히 알아보려면 여기를 참조 [하세요](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#allocation-method) .

### <a name="client-ip-preservation"></a>클라이언트 IP 유지
영역 간 부하 분산 장치는 계층 4 통과 네트워크 부하 분산 장치입니다. 이 통과는 패킷의 원래 IP를 유지 합니다.  원본 IP는 가상 머신에서 실행 되는 코드에서 사용할 수 있습니다. 이러한 유지를 통해 IP 주소와 관련 된 논리를 적용할 수 있습니다.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>기존 Azure Load Balancer에서 지역 간 솔루션 빌드
지역 간 부하 분산 장치의 백 엔드 풀에는 지역 부하 분산 장치가 하나 이상 포함 되어 있습니다. 

항상 사용 가능한 지역 간 배포를 위해 지역 간 부하 분산 장치에 기존 부하 분산 장치 배포를 추가 합니다.

**홈 지역은** 지역 간 부하 분산 장치를 배포 하는 위치입니다. 이 지역은 트래픽이 라우팅되는 방법에 영향을 주지 않습니다. 홈 지역이 중단 되 면 트래픽 흐름에 영향을 주지 않습니다.

### <a name="home-regions"></a>홈 지역
* 미국 동부 2
* 미국 서부
* 서유럽
* 동남 아시아
* 미국 중부
* 북유럽
* 동아시아

> [!NOTE]
> 위의 8 개 지역 중 하나에만 지역 간 부하 분산 장치를 배포할 수 있습니다.

참여 하는 **지역** 에서는 부하 분산 장치의 전역 공용 IP를 사용할 수 있습니다. 

사용자가 시작한 트래픽은 Microsoft core 네트워크를 통해 가장 가까운 참여 지역으로 이동 됩니다. 

영역 간 부하 분산 장치는 해당 지역 부하 분산 장치로 트래픽을 라우팅합니다.

### <a name="participating-regions"></a>참여 지역
* 미국 동부 
* 서유럽 
* 미국 중부 
* 미국 동부 2 
* 미국 서부 
* 북유럽 
* 미국 중남부 
* 미국 서부 2 
* 영국 남부 
* 동남 아시아 
* 미국 중북부 
* 일본 동부 
* 동아시아 
* 미국 중서부 
* 오스트레일리아 남동부 
* 오스트레일리아 동부 
* 인도 중부 

## <a name="limitations"></a>제한 사항

* 지역 간 프런트 엔드 IP 구성은 공용 으로만 사용할 수 있습니다. 내부 프런트 엔드는 현재 지원 되지 않습니다.

* 지역 간 부하 분산 장치의 백 엔드 풀에 개인 또는 내부 부하 분산 장치를 추가할 수 없습니다. 

* 영역 간 IPv6 프런트 엔드 IP 구성은 지원 되지 않습니다. 

* 상태 프로브는 현재 구성할 수 없습니다. 기본 상태 프로브는 20 초 마다 지역 부하 분산 장치에 대 한 가용성 정보를 자동으로 수집 합니다. 

## <a name="pricing-and-sla"></a>가격 및 SLA
지역 간 부하 분산 장치는 표준 부하 분산 장치의 [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) 를 공유 합니다.

 
## <a name="next-steps"></a>다음 단계

- 부하 분산 장치를 사용 하 여 시작 하려면 [공용 표준 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-portal.md) 를 참조 하세요.
- [Azure Load Balancer에 대해 자세히 알아보세요](load-balancer-overview.md).
- 부하 분산 장치 [faq](load-balancer-faqs.md)
