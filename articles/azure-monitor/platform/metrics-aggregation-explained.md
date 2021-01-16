---
title: Azure Monitor 메트릭 메트릭 집계 및 표시 설명
description: Azure Monitor에서 메트릭을 집계 하는 방법에 대 한 자세한 정보
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 01/12/2020
ms.subservice: metrics
ms.openlocfilehash: 1d83ef07714e0ce69f01aa240cc3058195c7b1af
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251984"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Azure Monitor 메트릭 메트릭 집계 및 표시 설명

이 문서에서는 [플랫폼 메트릭과](data-platform.md) [사용자 지정 메트릭을](metrics-custom-overview.md)Azure Monitor 하는 Azure Monitor 시계열 데이터베이스의 메트릭 집계에 대해 설명 합니다. 이 문서는 표준 [Application Insights 메트릭에](../app/app-insights-overview.md)도 적용 됩니다. 

이는 복잡 한 토픽 이며 Azure Monitor 메트릭을 효과적으로 사용 하기 위해이 문서의 모든 정보를 이해 하는 데 필요 하지 않습니다.

## <a name="overview-and-terms"></a>개요 및 용어

차트에 메트릭을 추가 하면 메트릭 탐색기가 자동으로 해당 기본 집계를 미리 선택 합니다. 기본 시나리오에서는 기본값이 적합 하지만 다른 집계를 사용 하 여 메트릭에 대 한 자세한 정보를 얻을 수 있습니다. 차트에서 다른 집계를 보려면 메트릭 탐색기에서 해당 집계를 처리 하는 방법을 이해 해야 합니다.

몇 가지 용어를 명확 하 게 정의 해 보겠습니다.

- **메트릭 값** – 특정 리소스에 대해 수집 된 단일 측정 값입니다.
- **기간-일반** 기간입니다.
- **시간 간격** – 두 메트릭 값을 수집 하는 시간 간격입니다. 
- **시간 범위** – 차트에 표시 되는 기간입니다. 일반적인 기본값은 24 시간입니다. 특정 범위만 사용할 수 있습니다. 
- **시간 세분성** 또는 **시간 세분화** – 차트에 표시할 수 있도록 값을 집계 하는 데 사용 되는 기간입니다. 특정 범위만 사용할 수 있습니다. 현재 최소값은 1 분입니다. 시간 세분성 값은 선택 된 시간 범위 보다 작아야 합니다. 그렇지 않으면 전체 차트에 대해 하나의 값만 표시 됩니다. 
- **집계 유형** – 여러 메트릭 값에서 계산 되는 통계의 유형입니다.  
- **집계** – 여러 입력 값을 가져온 다음이 값을 사용 하 여 집계 유형에 서 정의 된 규칙을 통해 단일 출력 값을 생성 하는 프로세스입니다. 예를 들어 여러 값의 평균을 차지 합니다.  

메트릭은 일정 한 시간 간격으로 캡처된 일련의 메트릭 값입니다. 차트를 그리면 선택 된 메트릭의 값이 시간 세분성 (시간 세분화 라고도 함)에 대해 개별적으로 집계 됩니다. 시간 세분성 크기는 [메트릭 탐색기 시간 선택 패널](metrics-getting-started.md#select-a-time-range)을 사용 하 여 선택 합니다. 명시적으로 선택 하지 않으면 현재 선택한 시간 범위에 따라 시간 세분성이 자동으로 선택 됩니다. 이를 선택 하면 각 시간 세분성 간격 중에 캡처된 메트릭 값이 집계 되 고 차트에 배치 됩니다 .이 값은 간격당 1 포인트입니다.

## <a name="aggregation-types"></a>집계 형식 

메트릭 탐색기에는 5 가지 기본 집계 형식이 있습니다. 메트릭 탐색기는 관계가 없는 집계를 숨기고 지정 된 메트릭에 사용할 수 없습니다. 

- **합계** – 집계 간격에 대해 캡처된 모든 값의 합계입니다. 합계 집계가 라고도 합니다.
- **Count** – 집계 간격에 대해 캡처된 측정 수입니다. 개수는 측정 값을 확인 하지 않으며 레코드 수만 표시 합니다. 
- **Average** – 집계 간격에 대해 캡처된 메트릭 값의 평균입니다. 대부분의 메트릭에 대해이 값은 합계/개수입니다. 
- **Min** – 집계 간격에 대해 캡처된 가장 작은 값입니다.
- **Max** – 집계 간격에 대해 캡처된 가장 큰 값입니다.

예를 들어 지난 24 시간 동안 **합계** 집계를 사용 하 여 VM에 대 한 **네트워크 아웃 총** 메트릭을 보여 주는 차트가 있다고 가정 합니다. 시간 범위와 세분성은 다음 스크린샷에 표시 된 것 처럼 차트의 오른쪽 위에서 변경할 수 있습니다.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="시간 범위 및 시간 세분성 선택을 보여 주는 스크린샷" border="true":::

시간 세분성 = 30 분 및 시간 범위 = 24 시간:

- 차트는 48 datapoints에서 그려집니다. 이는 1 분 datapoints 집계 된 시간당 24 시간 x 2 datapoints (60 분/30 분)입니다. 
- 꺾은선형 차트는 차트 그림 영역에서 48 점을 연결 합니다. 
- 각 포인트는 관련 된 30 분 기간 동안 전송 된 모든 네트워크 출력 바이트의 합계를 나타냅니다. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="24 시간 범위 및 30 분 시간 세분성으로 설정 된 선 그래프의 데이터를 보여 주는 스크린샷" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*더 큰 버전을 보려면이 섹션의 이미지를 클릭 합니다.*

시간 세분성을 15 분으로 전환 하면 차트는 96 집계 데이터 요소에서 그려집니다. 즉, 초당 60min/15min = 4 datapoints (시간당) x 24 시간입니다.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="24 시간 범위 및 15 분 시간 세분성으로 설정 된 선 그래프의 데이터를 보여 주는 스크린샷" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

시간 세분성이 5 분 이면 24 x (60/5) = 288 점이 표시 됩니다. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="24 시간 범위와 5 분의 시간 세분성으로 설정 된 꺾은선형 그래프의 데이터를 보여 주는 스크린샷" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

1 분의 시간 세분성 (차트에서 가장 작은 값)의 경우 24 x 60/1 = 1440 점이 표시 됩니다. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="24 시간 범위 및 1 분 시간 세분성으로 설정 된 꺾은선형 그래프의 데이터를 보여 주는 스크린샷" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

이전 스크린샷에 표시 된 것 처럼 차트는 이러한 summations 다르게 보입니다.  이 VM의 나머지 기간을 기준으로 작은 시간에 많은 출력을 보유 하 고 있는지 확인 합니다.  

시간 세분성을 사용 하면 차트에서 "신호 대 노이즈" 비율을 조정할 수 있습니다. 집계를 높이면 노이즈를 제거 하 고 급증을 원활 하 게 합니다.  1 분 분량의 작은 차트에서 변형 된 것을 확인 하 고 더 높은 세분성 값으로 이동 하는 경우의 변화를 확인 합니다. 

이 다듬기 동작은 경고와 같은 다른 시스템에이 데이터를 보낼 때 중요 합니다. 일반적으로 90% 보다 CPU 시간을 매우 짧게 하 여 경고 하지 않으려고 합니다. 그러나 CPU가 5 분 동안 90%에 유지 되는 경우이는 중요할 수 있습니다. CPU (또는 메트릭)에 대 한 경고 규칙을 설정 하는 경우 시간 세분성을 더 크게 설정 하면 표시 되는 거짓 경고 수를 줄일 수 있습니다. 

작업에 가장 적합 한 시간 간격을 파악 하기 위해 "정상"을 설정 하는 것이 중요 합니다. 이는 [동적 경고](alerts-dynamic-thresholds.md)의 이점 중 하나입니다 .이는 여기에서 다루지 않는 다른 항목입니다.  

## <a name="how-the-system-collects-metrics"></a>시스템이 메트릭을 수집 하는 방법

데이터 수집은 메트릭에 따라 다릅니다. 컬렉션 기간에는 두 가지 유형이 있습니다.

### <a name="measurement-collection-frequency"></a>측정 수집 빈도 

- **Regular** -메트릭이 차이가 없는 일관 된 시간 간격으로 수집 됩니다.

- **활동 기반** -특정 유형의 트랜잭션이 발생 하는 시점을 기준으로 메트릭이 수집 됩니다. 각 트랜잭션에는 메트릭 항목과 타임 스탬프가 있습니다. 이러한 값은 일정 한 간격으로 수집 되지 않으므로 지정 된 기간 동안 레코드가 다양 하 게 표시 됩니다. 

### <a name="granularity"></a>세분성

최소 시간 간격은 1 분 이지만 기본 시스템은 메트릭에 따라 데이터를 더 빠르게 캡처할 수 있습니다. 예를 들어 CPU 비율은 정기적 간격으로 15 초 마다 추적 됩니다. HTTP 오류는 트랜잭션으로 추적 되므로 1 분 넘게 쉽게 초과할 수 있습니다. SQL 저장소와 같은 기타 메트릭은 20 분 마다 캡처됩니다. 이 선택 항목은 개별 리소스 공급자 및 유형입니다. 가장 작은 간격을 제공 해 보세요.

### <a name="dimensions-splitting-and-filtering"></a>차원, 분할 및 필터링

각 개별 리소스에 대해 메트릭이 캡처됩니다. 그러나 메트릭이 수집, 저장 및 차트로 작성 될 수 있는 수준은 달라질 수 있습니다. 이 수준은 **메트릭 차원** 에서 사용할 수 있는 추가 메트릭으로 표시 됩니다. 각 개별 리소스 공급자는 수집 하는 데이터의 세부 정보를 정의 합니다. Azure Monitor는 해당 세부 정보를 표시 하 고 저장 하는 방법만 정의 합니다. 

메트릭 탐색기에서 메트릭을 차트로 차트를 만들면 차원으로 차트를 "분할" 하는 옵션이 있습니다.  차트를 분할 하는 것은 기본 데이터를 자세히 살펴보고 메트릭 탐색기에서 해당 데이터의 차트 또는 필터링을 확인 하는 것입니다.

예를 들어 [microsoft.apimanagement/service](metrics-supported.md#microsoftapimanagementservice) 는 여러 메트릭에 대 한 차원으로 *위치* 를 가집니다. 

- **용량** 은 이러한 메트릭 중 하나입니다. *Location* 차원은 기본 시스템이 집계 금액에 대해 하나만이 아니라 각 위치의 용량에 대 한 메트릭 레코드를 저장 하는 것을 의미 합니다. 그런 다음 메트릭 차트에서 해당 정보를 검색 하거나 분할할 수 있습니다.  

- **게이트웨이 요청의 전체 기간** 을 확인 하는 데에는 2 차원 *위치* 와 *호스트 이름이* 있으며, 여기에서 지속 시간 및 해당 위치에 있는 호스트 이름을 확인할 수 있습니다.  

- 보다 유연한 메트릭 중 하나인 **요청** 에는 7 개의 다른 차원이 있습니다. 
 
각 메트릭 및 사용 가능한 차원에 대 한 자세한 내용은 [지원 되](metrics-supported.md) 는 Azure Monitor 메트릭 문서를 확인 하세요. 또한 각 리소스 공급자와 유형에 대 한 설명서는 차원 및 측정값에 대 한 추가 정보를 제공할 수 있습니다.

분할 및 필터링을 사용 하 여 문제를 자세히 살펴볼 수 있습니다. 다음은 리소스 그룹의 Vm 그룹에 대 한 *평균 디스크 쓰기 바이트* 를 보여 주는 그래픽의 예입니다. 이 메트릭을 사용 하는 모든 Vm의 롤업이 있지만 실제로는 오전 6 시 까지의 최대 사용량을 실제로 담당 하는 것을 확인 하는 것이 좋습니다. 동일한 컴퓨터 인가요? 얼마나 많은 컴퓨터가 관련 되어 있나요?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="Contoso 호텔 리소스 그룹의 모든 가상 컴퓨터에 대 한 총 디스크 쓰기 바이트 수를 보여 주는 스크린샷" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*더 큰 버전을 보려면이 섹션의 이미지를 클릭 합니다.*

분할을 적용 하면 기본 데이터를 볼 수 있지만 약간의 데이터를 볼 수 있습니다. 위의 차트에 집계 되는 20 개의 Vm이 있습니다. 이 경우에는 마우스를 사용 하 여 오전 6 시에 DCVM11이 원인 임을 알려 줍니다. 그러나 차트를 복잡 하 게 하는 다른 Vm 때문에 해당 VM과 연결 된 나머지 데이터를 확인 하기는 어렵습니다. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="가상 컴퓨터 이름으로 분할 된 Contoso 호텔 리소스 그룹의 모든 가상 컴퓨터에 대 한 디스크 쓰기 바이트를 보여 주는 스크린샷" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

필터링을 사용 하면 차트를 정리 하 여 정말 무슨 일이 발생 하는지 확인할 수 있습니다. 보려는 Vm을 선택 하거나 선택 취소할 수 있습니다. 점선이 표시 됩니다. 이러한 항목은 이후 섹션에서 설명 합니다.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="가상 컴퓨터 이름으로 분할 및 필터링 된 Contoso 호텔 리소스 그룹의 모든 가상 컴퓨터에 대 한 디스크 쓰기 바이트를 보여 주는 스크린샷" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

메트릭 탐색기 차트에서 분할 차원 데이터를 표시 하는 방법에 대 한 자세한 내용은 [메트릭 탐색기의 고급 기능-필터 및 분할](metrics-charts.md#filters)을 참조 하세요.

### <a name="null-and-zero-values"></a>NULL 및 0 값

시스템에서 리소스의 메트릭 데이터를 예상 하지만 수신 하지 않는 경우 NULL 값을 기록 합니다.  NULL은 0 값과 다르며 집계 및 차트 계산에 중요 합니다. NULL 값은 유효한 측정값으로 계산 되지 않습니다. 

Null은 다른 차트에 다르게 표시 됩니다. 산 점도 차트에서 점을 표시 하지 않습니다. 가로 막대형 차트는 막대가 표시 되는 것을 건너뜁니다. 꺾은선형 차트에서 NULL은 이전 섹션의 스크린샷에 표시 된 것 처럼 [점선이 나 파선](metrics-troubleshoot.md#chart-shows-dashed-line) 으로 표시 될 수 있습니다. Null을 포함 하는 평균을 계산할 때 평균을 계산 하는 데 더 많은 데이터 요소가 있습니다.  이 동작으로 인해 차트의 값이 예기치 않게 삭제 될 수도 있지만 일반적으로 값이 0으로 변환 되 고 유효한 값으로 사용 되는 경우 보다 낮습니다.  

[사용자 지정 메트릭은](metrics-custom-overview.md) 데이터를 받지 못한 경우 항상 null을 사용 합니다. [플랫폼 메트릭을](data-platform.md)사용 하는 경우 각 리소스 공급자는 지정 된 메트릭에 대해 가장 적합 한 것을 기준으로 0 또는 null을 사용할지 여부를 결정 합니다.

Azure Monitor 경고는 리소스 공급자가 메트릭 데이터베이스에 기록 하는 값을 사용 하므로 먼저 데이터를 확인 하 여 리소스 공급자가 Null을 처리 하는 방법을 알고 있어야 합니다.

## <a name="how-aggregation-works"></a>집계 작동 방법

이전 시스템의 메트릭 차트는 서로 다른 유형의 집계 데이터를 표시 합니다. 시스템은 요청 된 차트가 많은 반복 계산 없이 빠르게 표시 될 수 있도록 데이터를 미리 집계 합니다.  

이 예제에서는 다음이 적용됩니다.

- **HTTP 오류** 라고 하는 **가상** 의 트랜잭션 메트릭을 수집 합니다. 
- *서버* 는 **HTTP 오류** 메트릭에 대 한 차원입니다.
- 서버 A, B, C의 3 개 서버가 있습니다.

설명을 단순화 하기 위해 SUM 집계 유형만 시작 합니다. 

### <a name="sub-minute-to-1-minute-aggregation"></a>1 분 집계 까지의 분

첫 번째 원시 메트릭 데이터는 Azure Monitor 메트릭 데이터베이스에 수집 되 고 저장 됩니다. 이 경우 *서버* 는 차원 이므로 각 서버에는 타임 스탬프를 사용 하 여 트랜잭션 레코드가 저장 됩니다. 고객으로 볼 수 있는 가장 작은 기간이 1 분인 경우 이러한 타임 스탬프는 먼저 각 개별 서버에 대 한 1 분 메트릭 값으로 집계 됩니다.  서버 B에 대 한 집계 프로세스는 아래 그림에 나와 있습니다. 서버 A와 C는 동일한 방식으로 수행 되며 다른 데이터를 포함 합니다.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="1 분 집계로 하위 분의 트랜잭션 항목을 보여 주는 스크린샷 " border="false":::

결과 1 분 집계 값은 메트릭 데이터베이스에 새 항목으로 저장 되므로 나중에 계산 하기 위해 수집할 수 있습니다. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="서버 차원에서 여러 개의 1 분 집계 항목을 보여 주는 스크린샷 서버 A, B, C가 개별적으로 표시 됨" border="false":::

### <a name="dimension-aggregation"></a>차원 집계

그런 다음 1 분 계산이 차원에 의해 축소 되 고 다시 개별 레코드로 저장 됩니다.   이 경우 모든 개별 서버의 모든 데이터가 1 분 간격 메트릭에 집계 되 고 이후 집계에서 사용할 수 있도록 메트릭 데이터베이스에 저장 됩니다.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="1 분 모든 서버에 집계 된 서버 A, B 및 C의 여러 1 분 집계 항목을 보여 주는 스크린샷" border="false":::

명확성을 위해 다음 표에서는 집계 방법을 보여 줍니다.

| 기간   | 서버 A | 서버 B | 콘텐츠를 복사합니다. | 합계 (A + B + C)|
| -------- | -------- | -------- | -------- | --------   |  
| 분 1 | 1        | 1        | 1        | 3          |
| 분 2 | 0        | 5        | 1        | 6          |
| 분 3 | 0        | 5        | 1        | 6          |
| 분 4 | 2        | 3        | 4        | 9          |
| 분 5 | 1        | 0        | 3        | 4          |
| 분 6 | 1        | 0        | 4        | 5          |
| 분 7 | 1        | 2        | 4        | 7          |
| 분 8 | 0        | 1        | 0        | 1          |
| 분 9 | 1        | 1        | 4        | 6          |
| 분 10| 2        | 1        | 0        | 3          |

위에서 한 차원만 표시 되지만이 동일한 집계 및 저장소 프로세스는 메트릭이 지 원하는 **모든 차원** 에 대해 발생 합니다.

- 해당 차원에서 1 분 집계 집합으로 값을 수집 합니다. 이러한 값을 저장 합니다. 
- 1 분 집계 합계로 차원을 축소 합니다. 이러한 값을 저장 합니다. 

네트워크 어댑터 라는 HTTP 오류의 또 다른 차원을 소개 하겠습니다. 서버당 어댑터 수가 다양 하다 고 가정해 보겠습니다.

- 서버 A에 어댑터가 1 개 있습니다.
- 서버 B에 두 개의 어댑터가 있음
- 서버 C에 어댑터가 3 개 있습니다.

다음 트랜잭션에 대 한 데이터를 별도로 수집 합니다. 다음과 같이 표시 됩니다.

- 시간
- 값
- 트랜잭션을 가져온 서버
- 트랜잭션을 가져온 어댑터

이러한 각 subminute 스트림은 1 분의 시계열 값으로 집계 되 고 Azure Monitor 메트릭 데이터베이스에 저장 됩니다.

- 서버 A, 어댑터 1
- 서버 B, 어댑터 1
- 서버 B, 어댑터 2
- 서버 C, 어댑터 1
- 서버 C, 어댑터 2
- 서버 C, 어댑터 3

또한 다음과 같은 축소 된 집계가 저장 됩니다.

- 서버 A, 어댑터 1 (축소할 항목이 없으므로 다시 저장 됨)
- 서버 B, 어댑터 1 + 2
- 서버 C, 어댑터 1 + 2 + 3
- 모두 서버, 모든 어댑터

이는 많은 수의 차원이 있는 메트릭에 많은 집계가 있음을 보여 줍니다. 모든 순열을 파악 하는 것은 중요 하지 않으며 이유를 이해 하기만 하면 됩니다. 시스템은 모든 차트에서 액세스를 위해 빠른 검색을 위해 저장 된 개별 데이터와 집계 데이터를 모두 포함 하려고 합니다. 시스템은 표시 하도록 선택한 항목에 따라 가장 관련성이 높은 저장 된 집계 또는 기본 원시 데이터를 선택 합니다. 

### <a name="aggregation-with-no-dimensions"></a>차원이 없는 집계

이 메트릭에는 차원 *서버가* 있으므로이 문서 앞부분에서 설명한 대로 분할 및 필터링을 통해 위의 서버 a, B 및 C에 대 한 기본 데이터를 가져올 수 있습니다. 메트릭에 *서버가* 없는 경우 고객은 다이어그램에 검은색으로 표시 된 집계 된 1 분 합계에만 액세스할 수 있습니다. 즉, 3, 6, 6, 9 등의 값이 있습니다. 또한 시스템은 기본 작업을 수행 하 여 분할 값을 집계 하는 것이 아니라 메트릭 탐색기에서 사용 하지 않거나 메트릭을 REST API를 통해 전송 하지 않습니다. 

## <a name="viewing-time-granularities-above-1-minute"></a>1 분 이상 시간 세분성 보기

더 큰 세분성으로 메트릭을 요청 하는 경우 시스템은 1 분 집계 합계를 사용 하 여 더 큰 시간 세분성의 합계를 계산 합니다.  아래 점선은 2 분 및 5 분 세분성의 합계 방법을 보여 줍니다. 여기서는 간단 하 게 SUM 집계 유형만 보여 줍니다.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="2 분에서 5 분 간격으로 집계 된 서버 차원에서 여러 1 분의 집계 된 항목을 보여 주는 스크린샷" border="false":::

2 분의 시간 세분성입니다.

| 기간       | 합계가       
| -------------|-------------|  
| 1 분 & 2 | (3 + 6) = 9 |
| 분 3 & 4 | (6 + 9) = 15|
| 분 4 & 5 | (4 + 5) = 9 |
| 1 분 6 & 7 | (7 + 1) = 8 |
| 분 8 & 9 | (6 + 3) = 9 |

5 분의 시간 세분성입니다.

| 기간              |            합계가        |
|---------------------|------------------------|  
| 분 1 ~ 5  | 3 + 6 + 6 + 9 + 4 = 28 |
| 분 6 ~ 10 | 5 + 7 + 1 + 6 + 3 = 22 |

시스템에서 최적의 성능을 제공 하는 저장 된 집계 데이터를 사용 합니다. 

다음은 위의 1 분 집계 프로세스에 대 한 더 큰 다이어그램으로, 가독성을 향상 시키기 위해 일부 화살표가 남아 있습니다.

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="이전 3 개 스크린샷의 통합을 보여 주는 스크린샷 1 분, 2 분 및 5 분 간격으로 집계 된 서버 차원에서 1 분의 집계 된 여러 항목입니다. 서버 A, B, C가 개별적으로 표시 됨" border="false":::

## <a name="more-complex-example"></a>복잡한 예

다음은 HTTP 응답 시간 (밀리초) 이라는 가상의 메트릭에 대 한 값을 사용 하는 더 큰 예제입니다.  여기서는 복잡성의 추가 수준을 소개 합니다.

1. Sum, Count, Min, Max 및 Average 계산에 대 한 집계를 표시 합니다.
2. NULL 값과이 값이 계산에 미치는 영향을 보여 줍니다. 

다음 예제를 살펴보십시오. 상자와 화살표는 값을 집계 하 고 계산 하는 방법의 예를 보여 줍니다. 

이전 섹션에서 설명한 것과 동일한 1 분 preaggregation 프로세스는 합계, 개수, 최소값 및 최대값에 대해 발생 합니다.  그러나 평균은 미리 집계 되지 않습니다. 계산 오류를 방지 하기 위해 집계 된 데이터를 사용 하 여 다시 계산 됩니다.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="집계 및 sum, count, min, max 및 average 계산의 복잡 한 예를 보여 주는 스크린샷 (1 분에서 10 분)" border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

위에서 강조 표시 된 1 분 집계에 대해 분 6을 고려 합니다. 이 분은 다시 부팅 하는 것과 같은 방법으로 서버 B가 오프 라인 상태로 전환 되 고 데이터 보고를 중지 한 지점입니다. 

위의 분 6부터 계산 된 1 분 집계 유형은 다음과 같습니다. 

| 집계 유형 | 값        | 참고 |
|------------------|--------------|-------|
| 합계              | 53 + 20 = 73 | |
| 개수            | 2            | Null의 효과를 보여 줍니다.  서버를 온라인 상태로 설정한 경우 값은 3입니다.  |
| 최소          | 20           | |
| 최대          | 53           | |
| 평균          | 73/2       | 항상 합계를 개수로 나눈 값입니다. 해당 세분성에 대해 집계 된 숫자를 사용 하 여 각 시간 세분성에 대해 저장 되 고 항상 다시 계산 되지 않습니다. 위에서 강조 표시 된 대로 5 분 및 10 분 시간 세분성 다시 계산 됩니다. |

빨간색 텍스트 색은 일반적인 범위에서 고려 될 수 있는 값을 나타내며, 시간 세분성이 되 면이를 전파 (또는 실패) 하는 방법을 보여 줍니다. *Min* 및 *Max* 는 기본 이상에 문제가 있음을 나타내고, *평균* 및 *합계* 는 시간 세분성이 되 면 해당 정보를 잃게 됩니다.  

또한 Null은 0이 대신 사용 된 경우 보다 평균을 계산 하는 것 보다 더 나은 평균 계산을 확인할 수 있습니다.  

> [!NOTE] 
> 이 예제의 경우는 아니지만 메트릭은 항상 값 *1을 사용* 하 여 캡처되는 경우 *합계* 와 같습니다. 이는 메트릭이 트랜잭션 이벤트의 발생을 추적 하는 경우 일반적입니다. 예를 들어이 문서의 이전 예제에서 언급 한 HTTP 오류 수입니다.

## <a name="next-steps"></a>다음 단계

- [메트릭 탐색기 시작](metrics-getting-started.md)
- [고급 메트릭 탐색기](metrics-charts.md)
