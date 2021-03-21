---
title: Azure Load Balancer 및 가용성 영역
titleSuffix: Azure Load Balancer
description: 이 학습 경로를 사용 하 여 Azure 표준 Load Balancer 및 가용성 영역를 시작 합니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 3c18b6d8dc44762649a9c07b88af348a18888fb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699116"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer 및 가용성 영역

Azure Load Balancer는 가용성 영역 시나리오를 지원 합니다. 표준 Load Balancer를 사용 하 여 리소스를에 맞추고 영역에 분산 하 여 시나리오 전체에서 가용성을 높일 수 있습니다.  이러한 개념과 기본적인 시나리오 디자인 지침을 이해 하려면이 문서를 검토 하세요.

Load Balancer는 **영역 중복, 영역** 또는 **비** 영역 일 수 있습니다. 부하 분산 장치에 대해 위에서 언급 한 영역 관련 속성을 구성 하려면 필요한 적절 한 유형의 프런트 엔드를 선택 합니다.

## <a name="zone-redundant"></a>영역 중복

가용성 영역 지역에서 표준 Load Balancer는 영역 중복 될 수 있습니다. 이 트래픽은 단일 IP 주소에 의해 제공 됩니다.

단일 프런트 엔드 IP 주소는 영역 실패 후에도 유지 됩니다. 프런트 엔드 IP를 사용 하 여 영역에 관계 없이 모든 (영향을 받지 않는) 백 엔드 풀 구성원에 연결할 수 있습니다. 하나 이상의 가용성 영역이 실패할 수 있으며, 지역에서 한 영역이 정상으로 유지 되는 한 데이터 경로가 생존 합니다.

프런트 엔드의 IP 주소는 여러 가용성 영역에서 여러 독립 인프라 배포에 의해 동시에 처리 됩니다. 재시도 또는 재설정는 영역 실패의 영향을 받지 않는 다른 영역에서 성공 합니다.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*그림: 영역 중복 부하 분산 장치*

## <a name="zonal"></a>횡단면

영역 이라고 *하는 단일* 영역에 대 한 프런트 엔드를 보장 하도록 선택할 수 있습니다.  이 시나리오는 모든 인바운드 또는 아웃 바운드 흐름이 한 지역의 단일 영역에서 처리 됨을 의미 합니다.  프런트 엔드는 영역의 상태와 수명을 공유합니다.  데이터 경로는 보장된 영역 이외의 영역에서 오류가 발생해도 영향을 받지 않습니다. 영역 프런트 엔드를 사용하여 가용성 영역별 IP 주소를 공개할 수 있습니다.  

또한 각 영역 내의 부하 분산 된 끝점에 대해 영역 프런트 엔드를 직접 사용 하는 것이 지원 됩니다. 이 구성을 사용 하 여 각 영역을 개별적으로 모니터링 하기 위해 부하 분산 된 영역 당 끝점을 노출할 수 있습니다. 공용 끝점의 경우 [Traffic Manager](../traffic-manager/traffic-manager-overview.md) 와 같은 dns 부하 분산 제품과 통합 하 고 단일 dns 이름을 사용할 수 있습니다.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*그림: 영역 부하 분산 장치*

공용 부하 분산 장치 프런트 엔드의 경우 공용 IP에 **영역** 매개 변수를 추가 합니다. 이 공용 IP는 해당 규칙에서 사용 하는 프런트 엔드 IP 구성에서 참조 됩니다.

내부 부하 분산 장치 프런트 엔드의 경우 내부 부하 분산 장치 프런트 엔드 IP 구성에 **zones** 매개 변수를 추가 합니다. 영역 프런트 엔드는 서브넷의 IP 주소를 특정 영역으로 보장 합니다.

## <a name="design-considerations"></a><a name="design"></a> 디자인 고려 사항

표준 Load Balancer에 대 한 영역 관련 속성을 이해 했으므로, 다음 디자인 고려 사항은 고가용성을 위해 디자인 하는 데 도움이 될 수 있습니다.

### <a name="tolerance-to-zone-failure"></a>영역에 대 한 허용 오차 오류

- **영역 중복** Load Balancer는 하나의 IP 주소를 사용 하는 모든 영역에서 영역 리소스를 제공할 수 있습니다.  하나 이상의 영역이 영역 내에서 정상 상태로 유지 되는 한, IP는 하나 이상의 영역 오류에 남을 수 있습니다.
- 영역 **프런트 엔드는 단일** 영역에 대 한 서비스를 줄이고 해당 영역에 대 한 공유를 공유 하는 것입니다. 배포가 있는 영역이 중단 되 면 배포에이 오류가 발생 하지 않습니다.

프로덕션 워크 로드에 대해 영역 중복 Load Balancer를 사용 하는 것이 좋습니다.

### <a name="control-vs-data-plane-implications"></a>제어 vs 데이터 평면 영향

영역 중복성은 비충돌 데이터 평면이 나 제어 평면을 의미 하지 않습니다. 영역 중복 흐름은 모든 영역을 사용할 수 있으며 흐름은 지역에서 모든 정상 영역을 사용 합니다. 영역 오류에서 정상 영역을 사용 하는 트래픽 흐름은 영향을 받지 않습니다.

영역 장애 시 영역을 사용 하는 트래픽 흐름에 영향을 줄 수 있지만 응용 프로그램을 복구할 수 있습니다. Azure가 영역 장애를 수렴 했을 때 재전송 시 해당 지역 내의 정상 영역에서 트래픽이 계속 됩니다.

[Azure 클라우드 디자인 패턴](/azure/architecture/patterns/) 을 검토 하 여 응용 프로그램의 복원 력을 오류 시나리오로 향상 시킵니다.

## <a name="next-steps"></a>다음 단계
- [가용성 영역](../availability-zones/az-overview.md) 에 대 한 자세한 정보
- [표준 Load Balancer](./load-balancer-overview.md) 에 대 한 자세한 정보
- [영역을 사용 하 여 영역 내에서 vm을 부하 분산](./quickstart-load-balancer-standard-public-cli.md) 하는 방법에 대해 알아봅니다 표준 Load Balancer
- [영역 중복 표준 Load Balancer를 사용 하 여 영역에서 vm을 부하 분산](./quickstart-load-balancer-standard-public-cli.md) 하는 방법을 알아봅니다.
- 응용 프로그램의 복원 력을 오류 시나리오로 개선 하기 위해 [Azure 클라우드 디자인 패턴](/azure/architecture/patterns/) 에 대해 알아봅니다.
