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
ms.openlocfilehash: 55692122461ef1b22b43b0def43e826ac7aeae30
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813788"
---
# <a name="test-through-simulations"></a>시뮬레이션을 통해 테스트

주기적으로 시뮬레이션을 수행하여 서비스가 공격에 대응하는 방법에 대한 가정을 테스트하는 것이 좋습니다. 테스트하는 동안 서비스 또는 애플리케이션이 계속해서 예상대로 작동하는지, 사용자 환경이 중단되지 않는지 검사해야 합니다. 기술과 프로세스 관점 간의 격차를 식별하여 DDoS 대응 전략에 통합해야 합니다. 프로덕션 환경에 미치는 영향을 최소화하기 위해 스테이징 환경에서 또는 피크 시간대 이외의 시간에 테스트를 수행하는 것이 좋습니다.

Microsoft는 Azure 고객이 시뮬레이션을 위해 DDoS Protection 사용 가능한 공용 끝점에 대 한 트래픽을 생성할 수 있는 인터페이스를 빌드하기 위해, 셀프 서비스 트래픽 [생성기와 파트너](https://www.ixiacom.com/products/breakingpoint-cloud)관계를 맺고 있습니다. 시뮬레이션을 사용 하 여 다음을 수행할 수 있습니다.

- Azure DDoS Protection을 통해 DDoS 공격으로부터 Azure 리소스를 보호하는 방법을 확인합니다.
- DDoS 공격을 받는 동안 인시던트 응답 프로세스를 최적화합니다.
- DDoS 규정 준수 문서화
- 네트워크 보안 팀을 교육합니다.

## <a name="prerequisites"></a>필수 구성 요소

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
1. **모니터링** 에서 **메트릭** 을 선택합니다.
1. **메트릭에** 대해 _DDoS 공격_ 을 선택 합니다.

리소스가 공격을 받고 있으면 다음 그림과 같이 값이 **0** 에서 **1** 로 변경 되는 것을 볼 수 있습니다.

![DDoS 공격 시뮬레이션 예: 포털](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>클라우드 API 스크립트

이 [API 스크립트](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Breaking%20Point%20SDK) 를 사용 하 여 한 번 실행 하거나 cron을 사용 하 여 정기적인 테스트를 예약 하 여 DDoS 테스트를 자동화할 수 있습니다. 이는 로깅이 올바르게 구성 되었고 검색 및 응답 절차가 효과적인 지를 확인 하는 데 유용 합니다. 스크립트에는 Linux OS (Ubuntu 18.04 LTS로 테스트) 및 Python 3이 필요 합니다. 포함 된 스크립트를 사용 하거나, 및 [클라우드](http://breakingpoint.cloud/) 웹 사이트의 설명서를 사용 하 여 필수 구성 요소 및 API 클라이언트를 설치 합니다.

## <a name="next-steps"></a>다음 단계

- [DDoS protection 원격 분석을 보고 구성](telemetry.md)하는 방법에 대해 알아봅니다.
- [DDoS 진단 로깅을 보고 구성](diagnostic-logging.md)하는 방법에 대해 알아봅니다.
- [DDoS 빠른 응답에 참여](ddos-rapid-response.md)하는 방법을 알아봅니다.
