---
title: 메트릭 관리자에 게 질문과 대답
titleSuffix: Azure Cognitive Services
description: 메트릭 관리자 서비스에 대 한 질문과 대답입니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: mbullwin
ms.openlocfilehash: da4dc3579630d641fcbc1d4321b56de0cc09d555
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893580"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>메트릭 관리자에 게 질문과 대답

### <a name="what-is-the-cost-of-my-instance"></a>내 인스턴스의 비용은 어떻습니까?

현재 미리 보기 중에는 인스턴스를 사용 하는 데 비용이 들지 않습니다.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>리소스를 만들 수 없는 이유는 무엇입니까? "가격 책정 계층"을 사용할 수 없으며 "이미이 구독에 대해 S0 1 개를 만들었습니다." 라는 메시지가 표시 되나요?

:::image type="content" source="media/pricing.png" alt-text="F0 리소스가 이미 있는 경우의 메시지":::

공개 미리 보기 중에는 구독에서 지역 마다 메트릭 관리자의 인스턴스를 하나만 만들 수 있습니다.

동일한 구독을 사용 하 여 동일한 지역에서 만든 인스턴스가 이미 있는 경우 다른 지역 또는 다른 구독을 시도 하 여 새 인스턴스를 만들 수 있습니다. 기존 인스턴스를 삭제 하 여 새 인스턴스를 만들 수도 있습니다.

기존 인스턴스를 이미 삭제 했지만 오류가 계속 표시 되는 경우 새 인스턴스를 만들기 전에 리소스를 삭제 한 후 20 분 정도 기다려 주세요.

## <a name="basic-concepts"></a>기본 개념

### <a name="what-is-multi-dimensional-time-series-data"></a>다차원 시계열 데이터는 무엇 인가요?

용어집에서 [다차원 메트릭](glossary.md#multi-dimensional-metric)  정의를 참조 하세요.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>메트릭 관리자에서 변칙 검색을 시작 하는 데 필요한 데이터의 양은 얼마 인가요?

적어도 하나의 데이터 요소는 변칙 검색을 트리거할 수 있습니다. 그러나 최상의 정확도는 아닙니다. 이 서비스는 데이터 피드를 만드는 동안 "채우기 간격" 규칙으로 지정한 값을 사용 하 여 이전 데이터 요소의 창을 가정 합니다.

검색 하려는 타임 스탬프 앞에 일부 데이터를 포함 하는 것이 좋습니다.
데이터의 세분성에 따라 권장 되는 데이터 양은 아래와 같습니다.

| 세분성 | 검색에 권장 되는 데이터 양 |
| ----------- | ------------------------------------- |
| 5 분 미만 | 4 일간의 데이터 |
| 5 분 ~ 1 일 | 데이터의 28 일 |
| 1 일 이상, 31 일 이상 | 4 년 분량의 데이터 |
| 31 일 초과 | 48 년 분량의 데이터 |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>메트릭 관리자가 기록 데이터에서 변칙을 검색 하지 않는 이유는 무엇 인가요?

메트릭 관리자는 라이브 스트리밍 데이터를 검색 하기 위한 것입니다. 서비스가 다시 검색 하 고 변칙 검색을 실행 하는 기록 데이터의 최대 길이에 제한이 있습니다. 즉, 특정 한 가장 이른 타임 스탬프 이후의 데이터 요소만 변칙 검색 결과를 갖게 됩니다. 가장 이른 타임 스탬프는 데이터의 세분성에 따라 달라 집니다.

데이터의 세분성에 따라 변칙 검색 결과가 발생 하는 기록 데이터의 길이는 다음과 같습니다.

| 세분성 | 변칙 검색에 대 한 기록 데이터의 최대 길이 |
| ----------- | ------------------------------------- |
| 5 분 미만 | 온보드 시간-13 시간 |
| 1 시간 미만 5 분 | 등록 시간-4 일  |
| 1 시간-1 일 미만 | 등록 시간-14 일  |
| 1일 | 등록 시간-28 일  |
| 1 일 보다 크고 31 일 미만 | 온보드 시간-2 년  |
| 31 일 초과 | 온보드 시간-24 년   |

### <a name="more-concepts-and-technical-terms"></a>추가 개념 및 기술 조건

자세한 내용은 [용어집](glossary.md) 을 참조 하십시오.

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>수집 내 데이터에 대 한 올바른 쿼리를 작성 어떻게 할까요?  

메트릭 관리자가 데이터를 수집 하려면 단일 타임 스탬프에서 데이터의 크기를 반환 하는 쿼리를 만들어야 합니다. 메트릭 관리자는이 쿼리를 여러 번 실행 하 여 각 타임 스탬프에서 데이터를 가져옵니다. 

쿼리는 지정 된 타임 스탬프에서 각 차원 조합에 대해 최대 하나의 레코드를 반환 해야 합니다. 반환 된 모든 레코드의 타임 스탬프는 동일 해야 합니다. 쿼리에서 반환 된 중복 레코드가 없어야 합니다.

예를 들어 일별 메트릭에 대해 아래 쿼리를 만든다고 가정 합니다. 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

시계열에 대해 정확한 세분성을 사용 해야 합니다. 시간별 메트릭에 대해 다음을 사용 합니다. 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

이러한 쿼리는 단일 타임 스탬프로 데이터만 반환 하 고 메트릭 관리자가 수집 하는 모든 차원 조합을 포함 합니다. 

:::image type="content" source="media/query-result.png" alt-text="F0 리소스가 이미 있는 경우의 메시지" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>비정상 상태에서 급증 & dip를 검색 어떻게 할까요??

하드 임계값을 미리 정의한 경우 [변칙 검색 구성](how-tos/configure-metrics.md#anomaly-detection-methods)에서 "하드 임계값"을 수동으로 설정할 수 있습니다.
임계값이 없는 경우 AI로 구동 되는 "스마트 검색"을 사용할 수 있습니다. 자세한 내용은 [검색 구성 조정](how-tos/configure-metrics.md#tune-the-detecting-configuration) 을 참조 하세요.

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>일반 (계절) 패턴을 비정상으로 감지 어떻게 할까요??

"스마트 감지"는 계절 패턴을 포함 하 여 데이터 패턴을 학습할 수 있습니다. 그런 다음 일반 패턴을 따르지 않는 데이터 요소를 변칙으로 검색 합니다. 자세한 내용은 [검색 구성 조정](how-tos/configure-metrics.md#tune-the-detecting-configuration) 을 참조 하세요.

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>평면을 비정상으로 감지 어떻게 할까요??

데이터가 정상적으로 안정적이 고 변동 되지 않으며, 너무 안정적으로 전환 되거나 평평한 선 일 때 경고를 표시 하려는 경우 변경이 너무 작은 경우 이러한 데이터 요소를 검색 하도록 "변경 임계값"을 구성할 수 있습니다.
자세한 내용은 [변칙 검색 구성](how-tos/configure-metrics.md#anomaly-detection-methods) 을 참조 하세요.

## <a name="advanced-concepts"></a>고급 개념

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>메트릭 관리자는 다차원 메트릭에 대 한 인시던트 트리를 어떻게 작성 하나요?

메트릭은 차원을 통해 여러 시간 계열로 분할 될 수 있습니다. 예를 들어 메트릭은 `Response latency` 팀에서 소유 하는 모든 서비스에 대해 모니터링 됩니다. `Service`범주는 메트릭을 보강 하는 차원으로 사용 될 수 있으므로 `Response latency` `Service1` , 등으로 분할 `Service2` 됩니다. 각 서비스는 여러 데이터 센터의 여러 컴퓨터에 배포 될 수 있으므로 메트릭은 및로 추가로 분할 될 수 `Machine` 있습니다 `Data center` .

|서비스| 데이터 센터| 컴퓨터  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

합계부터 시작 하 `Response latency` 여, 및로 메트릭을 드릴 다운할 수 있습니다 `Service` `Data center` `Machine` . 그러나 서비스 소유자가 경로를 사용 하는 것이 더 적합할 수도 `Service`  ->  `Data center`  ->  `Machine` 있고 인프라 엔지니어가 경로를 사용 하는 것이 더 적합할 `Data Center`  ->  `Machine`  ->  `Service` 수도 있습니다. 이는 사용자의 개별 비즈니스 요구 사항에 따라 달라 집니다. 

메트릭 관리자에서 사용자는 계층 토폴로지의 한 노드에서 드릴 다운 하거나 롤업할 경로를 지정할 수 있습니다. 보다 정확 하 게, 계층 토폴로지는 트리 구조가 아니라 방향이 지정 된 비순환 그래프입니다. 다음과 같이 모든 잠재적 차원 조합으로 구성 된 전체 계층 토폴로지가 있습니다. 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="F0 리소스가 이미 있는 경우의 메시지" lightbox="media/dimension-combinations-view.png":::

이론적으로 차원에 고유한 값이 있고 차원에 고유한 값이 있으며 차원에 고유한 값이 있는 경우 `Service` `Ls` `Data center` `Ldc` `Machine` `Lm` `(Ls + 1) * (Ldc + 1) * (Lm + 1)` 계층 구조 토폴로지에서 차원 조합이 있을 수 있습니다. 

그러나 일반적으로 일부 차원 조합은 유효 하지 않으므로 복잡성을 크게 줄일 수 있습니다. 현재 사용자가 메트릭 자체를 집계 하는 경우 차원 수를 제한 하지 않습니다. 메트릭 관리자가 제공 하는 롤업 기능을 사용 해야 하는 경우 차원 수는 6 보다 커야 합니다. 그러나 메트릭에 대 한 차원으로 확장 되는 시계열 수를 1만 미만으로 제한 합니다.

진단 페이지의 **인시던트 트리** 도구는 전체 토폴로지가 아닌 변칙이 검색 된 노드만 표시 합니다. 이는 현재 문제에 초점을 맞출 수 있도록 하기 위한 것입니다. 또한 메트릭 내에 모든 비정상을 표시 하지 않을 수도 있으며, 대신 기여에 따라 상위 변칙을 표시 합니다. 이러한 방식으로 비정상적인 데이터의 영향, 범위 및 확산 경로를 빠르게 확인할 수 있습니다. 이로 인해 초점을 맞춰야 하는 변칙 수가 현저 하 게 감소 하 고 사용자가 주요 문제를 이해 하 고 찾을 수 있습니다. 
 
예를 들어에서 변칙이 발생 하는 경우 변칙 `Service = S2 | Data Center = DC2 | Machine = M5` 의 편차는 부모 노드에 영향 `Service= S2` 을 주므로 비정상을 감지 했지만 이상에서는의 전체 데이터 센터 및 모든 서비스에 영향을 주지 않습니다 `DC2` `M5` . 인시던트 트리는 아래 스크린샷에 나와 있는 것 처럼 작성 되 고, 상위 변칙은에서 캡처되고 `Service = S2` , 근본 원인은 모두로 이어지는 두 경로에서 분석할 수 있습니다 `Service = S2 | Data Center = DC2 | Machine = M5` .

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 개의 서로 다른 경로를 포함 하는 꼭 짓 점 레이블이 S2 인 공통 노드를 사용 하 여 연결 합니다. Top 변칙은 Service = S2에서 캡처되고, 근본 원인은 서비스 = S2로 이어지는 두 경로에서 분석할 수 있습니다. 데이터 센터 = DC2 | Machine = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>다음 단계
- [Metrics Advisor 개요](overview.md)
- [웹 포털 사용](quickstarts/web-portal.md)