---
title: 시뮬레이션 테스트 Azure DDoS Protection
description: 시뮬레이션을 통해 테스트 하는 방법 알아보기
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: eff738e24b3abce52e80291c55a3ae64c3c8c853
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905496"
---
# <a name="test-through-simulations"></a>시뮬레이션을 통해 테스트

주기적으로 시뮬레이션을 수행하여 서비스가 공격에 대응하는 방법에 대한 가정을 테스트하는 것이 좋습니다. 테스트하는 동안 서비스 또는 애플리케이션이 계속해서 예상대로 작동하는지, 사용자 환경이 중단되지 않는지 검사해야 합니다. 기술과 프로세스 관점 간의 격차를 식별하여 DDoS 대응 전략에 통합해야 합니다. 프로덕션 환경에 미치는 영향을 최소화하기 위해 스테이징 환경에서 또는 피크 시간대 이외의 시간에 테스트를 수행하는 것이 좋습니다.

Microsoft는 Azure 고객이 시뮬레이션을 위해 DDoS Protection 사용 가능한 공용 끝점에 대 한 트래픽을 생성할 수 있는 인터페이스를 빌드하기 위해, 셀프 서비스 트래픽 [생성기와 파트너](https://www.ixiacom.com/products/breakingpoint-cloud)관계를 맺고 있습니다. 시뮬레이션을 사용 하 여 다음을 수행할 수 있습니다.

- Azure DDoS Protection을 통해 DDoS 공격으로부터 Azure 리소스를 보호하는 방법을 확인합니다.
- DDoS 공격을 받는 동안 인시던트 응답 프로세스를 최적화합니다.
- DDoS 규정 준수 문서화
- 네트워크 보안 팀을 교육합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 이 자습서의 단계를 완료 하려면 먼저 보호 된 공용 IP 주소를 사용 하 여 [Azure DDoS Standard 보호 계획](manage-ddos-protection.md) 을 만들어야 합니다.
- 먼저이 [클라우드](http://breakingpoint.cloud/)를 사용 하 여 계정을 만들어야 합니다. 

## <a name="configure-a-ddos-test-attack"></a>DDoS 테스트 공격 구성

1. 다음 값을 입력 하거나 선택 하 고 **테스트 시작** 을 선택 합니다.

    |설정        |값                                              |
    |---------      |---------                                          |
    |대상 IP 주소           | 테스트 하려는 공용 IP 주소 중 하나를 입력 합니다.                     |
    |포트 번호   | _443_ 을 입력합니다.                       |
    |DDoS 프로필 | **TCP SYN 홍수** 를 선택 합니다.|
    |테스트 크기       | **계의 pps, 100 Mbps 및 8 원본 ip를 선택 합니다.**                                  |
    |테스트 지속 시간 | **10 분** 을 선택 합니다.|

이제 다음과 같이 표시 됩니다.

![DDoS 공격 시뮬레이션 예: 간에 Ingpoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>모니터링 및 유효성 검사

1. 에 로그인 하 여 https://portal.azure.com 구독으로 이동 합니다.
1. 공격을 테스트 한 공용 IP 주소를 선택 합니다.
1. **모니터링** 아래에서 **메트릭** 을 선택합니다.
1. **메트릭에** 대해 _DDoS 공격_ 을 선택 합니다.

리소스가 공격을 받고 있으면 다음 그림과 같이 값이 **0** 에서 **1** 로 변경 되는 것을 볼 수 있습니다.

![DDoS 공격 시뮬레이션 예: 포털](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

## <a name="next-steps"></a>다음 단계

- [DDoS protection 원격 분석을 보고 구성](telemetry-monitoring-alerting.md)하는 방법에 대해 알아봅니다.
- [DDoS 공격 완화 보고서 및 흐름 로그를 구성](reports-and-flow-logs.md)하는 방법에 대해 알아봅니다.
- [DDoS 빠른 응답에 참여](ddos-rapid-response.md)하는 방법을 알아봅니다.