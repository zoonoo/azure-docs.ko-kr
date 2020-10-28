---
title: Azure Load Balancer에 대 한 정보
description: 부하 분산 장치 정보를 사용 하 여 신속한 오류 로컬리제이션 및 합리적인 설계 결정 사항 실현
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 2168ee05ab93655cc0ad87221bff29c1b6b1035d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897594"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>정보를 사용 하 여 Azure Load Balancer 모니터링 및 구성

[네트워크에 대 한 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/insights-overview#azure-monitor-for-networks-preview)를 통해 부하 분산 장치에 대 한 기능 종속성 시각화 및 미리 구성 된 메트릭 대시보드를 제공 합니다. 이러한 시각적 개체는 적절 한 디자인 결정을 하 고 오류를 신속 하 게 지역화, 진단 및 해결 하는 데 도움이 됩니다.

>[!NOTE] 
>이 기능은 미리 보기 상태 이며, 기능 종속성 보기 및 미리 구성 된 대시보드가이 환경을 개선 하기 위해 변경 될 수 있습니다.

>[!IMPORTANT]
>표준 Load Balancer는 미리 구성 된 메트릭 대시보드의 Load Balancer 네임 스페이스에서 메트릭을 확인 하는 데 필요 합니다. 여전히 VM, 가상 머신 확장 집합 및 연결 모니터 네임 스페이스의 메트릭을 볼 수 있지만, 강력한 Load Balancer 메트릭 집합을 활용 하려면 프로덕션 워크 로드에 대 한 [Standard로 업그레이드](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard) 하는 것이 좋습니다.

## <a name="functional-dependency-view"></a>함수 종속성 보기

함수 종속성 보기를 사용 하면 가장 복잡 한 부하 분산 장치 설정도 파악할 수 있습니다. 최신 Load Balancer 구성에 대 한 시각적 피드백을 통해 구성을 염두에 두면 업데이트를 수행할 수 있습니다.

Azure에서 Load Balancer 리소스의 Insights 블레이드를 방문 하 여이 보기에 액세스할 수 있습니다.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="함수 종속성 뷰를 표시 합니다. 부하 분산 장치의 프런트 엔드는 구성 된 규칙을 통해 백 엔드 풀 멤버에 연결 하는 것을 볼 수 있습니다. 표준 Load Balancer에 대해 부하 분산 규칙에서 백 엔드 풀 인스턴스로의 줄은 상태 프로브 상태에 따라 색으로 구분 됩니다." border="true":::

표준 부하 분산 장치의 경우 백 엔드 풀 리소스는 트래픽 처리를 위한 백 엔드 풀의 현재 가용성을 나타내는 상태 프로브 상태를 사용 하 여 색으로 구분 됩니다. 위의 토폴로지와 함께 상태 그래프가 표시 되 고 응용 프로그램의 상태에 대 한 스냅숏 보기를 제공 합니다.

## <a name="metrics-dashboard"></a>메트릭 대시보드

Load Balancer의 정보 블레이드에서 더 자세한 메트릭을 선택 하 여 Load Balancer의 특정 측면과 관련 된 메트릭 시각적 개체를 포함 하는 미리 구성 된 [Azure Monitor 통합 문서](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) 를 볼 수 있습니다. 이 대시보드는 페이지 맨 위에 있는 관련 설명서에 대 한 링크 및 Load Balancer 상태를 표시 합니다.

처음에는 개요 탭이 표시 됩니다. Load Balancer의 특정 측면과 관련 된 시각적 개체를 포함 하는 사용 가능한 탭을 탐색할 수 있습니다. 각 탭의 아래쪽에 있는 대시보드에서 각각에 대 한 명시적 지침을 사용할 수 있습니다.

현재 사용할 수 있는 대시보드 탭은 다음과 같습니다.
* 개요
* 프런트 엔드 및 백 엔드 가용성
* 데이터 처리량
* 흐름 분포
* 연결 모니터
* 메트릭 정의 

### <a name="overview-tab"></a>개요 탭
개요 탭에는 Load Balancer에 연결 된 각 프런트 엔드 Ip의 전체 데이터 경로 가용성 및 상태 프로브 상태를 포함 하는 검색 가능한 표가 있습니다. 이러한 메트릭은 프런트 엔드 IP가 응답 하 고 백 엔드 풀의 계산 인스턴스가 인바운드 연결에 개별적으로 반응 하는지 여부를 나타냅니다.

또한이 페이지에서 각 프런트 엔드 IP의 전체 데이터 처리량을 확인 하 여 예상 되는 트래픽 수준을 생성 하 고 받을지 여부를 파악할 수 있습니다. 페이지 맨 아래에 있는 지침은 불규칙 한 값이 표시 되는 적절 한 탭으로 안내 합니다.

### <a name="frontend-and-backend-availability-tab"></a>프런트 엔드 및 백 엔드 가용성 탭
프런트 엔드 및 백 엔드 가용성 탭은 몇 가지 유용한 보기에서 제공 되는 데이터 경로 처리량 및 상태 프로브 상태 메트릭을 보여 줍니다. 첫 번째 그래프에는 문제가 있는지 여부를 확인할 수 있도록 집계 값이 표시 됩니다. 그래프의 나머지 부분에서는 인바운드 가용성 문제의 원인을 파악 하 고 해결할 수 있도록 이러한 메트릭을 다양 한 차원으로 분할 하는 방법을 보여 줍니다.

이러한 그래프를 보는 워크플로는 다양 한 증상의 일반적인 원인과 함께 페이지 아래쪽에 제공 됩니다. 

### <a name="data-throughput-tab"></a>데이터 처리량 탭
데이터 처리량 탭에서는 인바운드 및 아웃 바운드 처리량을 검토 하 여 트래픽 패턴이 예상 대로 작동 하는지 확인할 수 있습니다. 실행 중인 서비스가 개별적으로 수행 하 고 있는지 확인할 수 있도록 프런트 엔드 IP 및 프런트 엔드 포트로 분할 된 인바운드 및 아웃 바운드 데이터 처리량을 보여 줍니다.

### <a name="flow-distribution"></a>흐름 분포
흐름 분산 탭은 백 엔드 인스턴스가 받고 생성 하는 흐름 수를 시각화 하 고 관리 하는 데 도움이 됩니다. 인바운드 및 아웃 바운드 트래픽에 대 한 흐름 생성 비율 및 흐름 수와 각 VM 및 가상 머신 확장 집합 인스턴스가 수신 하는 네트워크 트래픽을 보여 줍니다. 

이러한 보기는 Load Balancer 구성 또는 트래픽 패턴이 불균형 트래픽으로 선행 되는지 여부에 대 한 피드백을 제공 합니다. 예를 들어 세션 선호도가 구성 되어 있고 단일 클라이언트가 불균형 요청 수를 만드는 경우입니다. 또한 컴퓨터 크기에 대 한 [VM 당 흐름 제한](https://docs.microsoft.com/azure/virtual-network/virtual-machine-network-throughput#flow-limits-and-recommendations) 에 근접 하 고 있는지 여부도 알 수 있습니다.

### <a name="connection-monitors"></a>연결 모니터
연결 모니터 탭에는 구성한 모든 [연결 모니터](https://docs.microsoft.com/azure/network-watcher/connection-monitor)  의 글로벌 맵에 대 한 왕복 대기 시간이 표시 됩니다. 이러한 시각적 개체는 엄격한 대기 시간 요구 사항이 있는 서비스에 대 한 유용한 정보를 제공 합니다. 요구 사항에 맞게 추가 지역 배포를 추가 하거나 [지역 간 부하 분산](https://docs.microsoft.com/azure/load-balancer/cross-region-overview) 모델로 이동 해야 할 수 있습니다.

### <a name="metric-definitions"></a>메트릭 정의
메트릭 정의 탭은 [다차원 메트릭 문서](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics)에 표시 된 모든 정보를 포함 합니다.

## <a name="next-steps"></a>다음 단계
* 개선할 수 있는 항목이 있는 경우 대시보드를 검토 하 고 아래 링크를 사용 하 여 피드백을 제공 하세요.
* [메트릭 설명서를 검토 하 여 각 메트릭이 계산 되는 방식을 이해 했는지 확인 합니다.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics)
* [Load Balancer에 대 한 연결 모니터 만들기](https://docs.microsoft.com/azure/network-watcher/connection-monitor)
* [사용자 고유의 통합 문서를 만들](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)수 있습니다. 자세한 메트릭 대시보드에서 편집 단추를 클릭 하 여이를 수행할 수 있습니다.
