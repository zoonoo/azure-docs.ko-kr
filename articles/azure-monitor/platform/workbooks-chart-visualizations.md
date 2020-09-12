---
title: Azure Monitor 통합 문서 차트 시각화
description: 모든 Azure Monitor 통합 문서 차트 시각화에 대해 알아봅니다.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006383"
---
# <a name="chart-visualizations"></a>차트 시각화

통합 문서를 사용 하면 모니터링 데이터를 차트로 표시할 수 있습니다. 지원 되는 차트 종류에는 꺾은선형, 가로 막대형, 가로 막대형 범주, 영역형, 산 점도, 원형 및 시간이 포함 됩니다. 작성자는 차트의 높이, 너비, 색상표, 범례, 제목, 데이터 없음 메시지 등을 사용자 지정 하 고 차트 설정을 사용 하 여 축 유형과 계열 색을 사용자 지정 하도록 선택할 수 있습니다.

통합 문서는 로그 및 메트릭 데이터 원본 모두에 대 한 차트를 지원 합니다.

## <a name="log-charts"></a>로그 차트

Azure Monitor 로그는 리소스 소유자에 게 앱 및 인프라의 작동에 대 한 자세한 정보를 제공 합니다. 메트릭과 달리 로그 정보는 기본적으로 수집 되지 않으며 특정 유형의 수집을 탑재 해야 합니다. 그러나 현재 로그는 리소스의 상태에 대 한 많은 정보와 진단에 유용한 데이터를 제공 합니다. 통합 문서에서는 로그 데이터를 사용자 분석을 위한 시각적 차트로 표시할 수 있습니다.

### <a name="adding-a-log-chart"></a>로그 차트 추가

아래 예제에서는 이전 날짜에 앱에 대 한 요청의 추세를 보여 줍니다.

1. **편집** 도구 모음 항목을 선택 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **쿼리 추가** 링크를 사용 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다.
3. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석에 대 한 [KQL](/azure/kusto/query/) (예: 요청 추세)를 입력할 수 있습니다.
5. 시각화를 **영역**, **가로**막대형, **가로 막대형 (범주)**, **꺾은선형**, **원형**, **분산형**또는 **시간**중 하나로 설정 합니다.
6. 필요한 경우 시간 범위, 시각화, 크기, 색상표 및 범례와 같은 다른 매개 변수를 설정 합니다.

[![편집 모드의 로그 차트 스크린샷](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>로그 차트 매개 변수

| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `Query Type` | 사용할 쿼리 유형입니다. | 로그, Azure 리소스 그래프 등 |
| `Resource Type` | 대상으로 할 리소스 형식입니다. | Application Insights, Log Analytics 또는 Azure-first |
| `Resources` | 메트릭 값을 가져올 리소스 집합입니다. | MyApp1 |
| `Time Range` | 로그 차트를 볼 시간 창입니다. | 지난 시간, 지난 24 시간 등 |
| `Visualization` | 사용할 시각화입니다. | 영역형, 가로 막대형, 꺾은선형, 원형, 분산형, 시간, 막대 범주 |
| `Size` | 컨트롤의 세로 크기입니다. | Small, medium, large 또는 full |
| `Color palette` | 차트에 사용할 색상표입니다. 다중 메트릭 또는 분할 모드에서 무시 됩니다. | 파랑, 녹색, 빨강 등 |
| `Legend` | 범례에 사용할 집계 함수입니다. | 값의 합계 또는 평균, Min, First, Last value |
| `Query` | 차트 시각화에 필요한 형식으로 데이터를 반환 하는 모든 KQL 쿼리입니다. | _요청 \| -시리즈 요청 = count () 기본값 = 0 타임 스탬프 (이전 (1d)에서 now () 단계 1 시간_ |

### <a name="time-series-charts"></a>시계열 차트

영역형, 가로 막대형, 꺾은선형, 분산형 및 시간과 같은 시계열 차트는 통합 문서의 쿼리 컨트롤을 사용 하 여 쉽게 만들 수 있습니다. 키에는 결과 집합에 시간 및 메트릭 정보가 있습니다.

#### <a name="simple-time-series"></a>단순 시계열

다음 쿼리는 *타임 스탬프* 및 *요청*이라는 두 개의 열이 있는 테이블을 반환 합니다. 쿼리 컨트롤은 X 축에 *타임 스탬프* 를 사용 하 고 Y 축에 대 한 *요청* 을 사용 합니다.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![간단한 시계열 로그 꺾은선형 차트의 스크린샷](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>여러 메트릭이 있는 시계열

다음 쿼리는 *타임 스탬프*, *요청*및 *사용자*라는 세 개의 열이 있는 테이블을 반환 합니다. 쿼리 컨트롤은 X 축에 *타임 스탬프* 를 사용 하 *Requests*고  &  *사용자* 를 Y 축에 별도의 계열로 요청 합니다.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![여러 메트릭 로그 꺾은선형 차트를 사용 하는 시계열의 스크린샷](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>분할 시계열

아래 쿼리는 세 개의 *열이 있는*테이블을 반환 *합니다. 여기서* *requestname* 은 *요청 이름이 포함* 된 범주 열입니다. 여기에 있는 쿼리 컨트롤은 X 축에 대 한 *타임 스탬프* 를 사용 하 고 *requestname*값 마다 계열을 추가 합니다.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![분할 된 시계열 로그 꺾은선형 차트의 스크린샷](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>요약 및 시리즈 만들기

이전 섹션의 예제에서는 `summarize` 더 쉽게 이해할 수 있으므로 연산자를 사용 합니다. 그러나 버킷에 항목이 없는 경우에는 결과 행이 생략 되므로 요약에는 중요 한 제한이 있습니다. 빈 버킷이 시간 범위의 앞에 있는지 또는 뒷면에 따라 차트 시간 창을 이동 하는 효과를 가질 수 있습니다.

일반적으로 연산자를 사용 하 여 `make-series` 시계열 데이터를 만드는 것이 좋습니다 .이 데이터에는 빈 버킷의 기본값을 제공 하는 옵션이 있습니다.

다음 쿼리에서는 연산자를 사용 합니다 `make-series` .

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

다음 쿼리는 연산자를 사용 하 여 비슷한 차트를 보여 줍니다. `summarize`

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

기본 결과 집합이 다른 경우에도 마찬가지입니다. 모든 사용자는 시각화를 영역, 선, 가로 막대형 또는 시간으로 설정 하 고 통합 문서는 나머지 작업을 처리 해야 합니다.

[![시리즈 쿼리를 통해 만든 로그 꺾은선형 차트의 스크린샷](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>범주 가로 막대형 차트 또는 히스토그램

사용자는 범주 형 차트를 사용 하 여 차트의 X 축에 차원 또는 열을 나타낼 수 있습니다 .이는 히스토그램에서 특히 유용 합니다. 다음 예제에서는 결과 코드에의 한 요청 분포를 보여 줍니다.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

이 쿼리는 두 개의 열, 즉 *요청* 메트릭 및 *결과* 범주를 반환 합니다. *결과* 열의 각 값은 차트에서 *요청 메트릭에*비례하여 높이가 지정 된 자체 막대를 가져옵니다.

[![결과 코드 별 요청에 대 한 범주 가로 막대형 차트 스크린샷](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>원형 차트

원형 차트에서는 숫자 비율의 시각화를 사용할 수 있습니다. 다음 예제에서는 결과 코드에의 한 요청 비율을 보여 줍니다.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

이 쿼리는 두 개의 열, 즉 *요청* 메트릭 및 *결과* 범주를 반환 합니다. *결과* 열의 각 값은 *요청* 메트릭에 비례하여 크기가 지정 된 원형에서 자체 조각을 가져옵니다.

[![결과 코드를 나타내는 조각이 포함 된 원형 차트의 스크린샷](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>메트릭 차트

대부분의 Azure 리소스는 상태 및 상태에 대 한 메트릭 데이터를 내보냅니다 (예: CPU 사용률, 저장소 가용성, 데이터베이스 트랜잭션 수, 실패 한 앱 요청 등). 통합 문서를 사용 하면이 데이터를 시계열 차트로 시각화할 수 있습니다.

### <a name="adding-a-metric-chart"></a>메트릭 차트 추가

다음 예에서는 이전 시간 동안 저장소 계정에 있는 트랜잭션 수를 보여 줍니다. 이를 통해 저장소 소유자는 트랜잭션 추세를 보고 동작의 비정상 상태를 찾을 수 있습니다.

1. **편집** 도구 모음 항목을 선택 하 여 통합 문서를 편집 모드로 전환 합니다.
2. 메트릭 **추가** 링크를 사용 하 여 통합 문서에 메트릭 컨트롤을 추가 합니다.
3. 리소스 유형 (예: 저장소 계정), 대상 리소스, 메트릭 네임 스페이스 및 이름, 사용할 집계를 선택 합니다.
4. 필요한 경우 시간 범위, 분할 기준, 시각화, 크기 및 색상표와 같은 다른 매개 변수를 설정 합니다.

[![편집 모드의 메트릭 차트 스크린샷](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>메트릭 차트 매개 변수

| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 대상으로 할 리소스 형식입니다. | 저장소 또는 가상 머신. |
| `Resources` | 메트릭 값을 가져올 리소스 집합입니다. | MyStorage1 |
| `Namespace` | 메트릭이 포함 된 네임 스페이스입니다. | Storage > Blob |
| `Metric` | 시각화할 메트릭입니다. | 저장소 > Blob > 트랜잭션 |
| `Aggregation` | 메트릭에 적용할 집계 함수입니다. | 합계, 개수, 평균 등 |
| `Time Range` | 메트릭을 볼 시간 창입니다. | 지난 시간, 지난 24 시간 등 |
| `Visualization` | 사용할 시각화입니다. | 영역형, 가로 막대형, 꺾은선형, 분산형, 그리드 |
| `Split By` | 필요에 따라 차원의 메트릭을 분할 합니다. | 지역 유형별 트랜잭션 |
| `Size` | 컨트롤의 세로 크기입니다. | 소형, 중형 또는 큼 |
| `Color palette` | 차트에 사용할 색상표입니다. `Split by`매개 변수가 사용 되는 경우 무시 됩니다. | 파랑, 녹색, 빨강 등 |

### <a name="examples"></a>예제

API 이름으로 분할 된 트랜잭션 (꺾은선형 차트):

[![API 이름별로 분할 된 저장소 트랜잭션에 대 한 메트릭 꺾은선형 차트의 스크린샷](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

응답 유형에 따라 분할 된 트랜잭션은 크게 막대형 차트로 표시 됩니다.

[![응답 유형별로 분할 된 저장소 트랜잭션에 대 한 커다란 메트릭 가로 막대형 차트 스크린샷](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

분산형 차트의 평균 대기 시간:

[![저장소 대기 시간에 대 한 메트릭 분산형 차트의 스크린샷](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>차트 설정

작성자는 차트 설정을 사용 하 여 차트 축에 사용 되는 필드, 축 단위, 사용자 지정 서식, 범위, 그룹화 동작, 범례 및 계열 색을 사용자 지정할 수 있습니다.

### <a name="the-settings-tab"></a>설정 탭

설정 탭은 다음을 제어 합니다.

- 사용자가 숫자 서식을 축 값 및 사용자 지정 범위로 설정할 수 있도록 하는 사용자 지정 서식 지정 필드를 포함 하는 축 설정입니다.
- 그룹화 설정 (필드 포함)은 "기타" 그룹이 만들어지기 전의 제한을 포함 합니다.
- 아래쪽에 메트릭 (계열 이름, 색 및 숫자)을 표시 하 고 범례 (계열 이름 및 색)를 표시 하는 범례 설정입니다.

![차트 설정의 스크린샷](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>사용자 지정 서식 지정

숫자 형식 지정 옵션은 다음과 같습니다.

| 서식 지정 옵션             | 설명                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | 열에 대 한 단위-백분율, 개수, 시간, 바이트, 개수/시간, 바이트/시간 등에 대 한 다양 한 옵션입니다. 예를 들어 값 1234에 대 한 단위를 밀리초로 설정할 수 있으며,이 값은 1.234로 렌더링 됩니다.                                  |
| `Style`                      | 10 진수, 통화, 백분율로 렌더링할 형식입니다.                                               |
| `Show group separator`       | 그룹 구분 기호를 표시 하려면 확인란을 선택 합니다. 미국에서 1234를 1234으로 렌더링 합니다.                                    |
| `Minimum integer digits`     | 사용할 최소 정수 자릿수 (기본값 1)입니다.                                                   |
| `Minimum fractional digits`  | 사용할 최소 소수 자릿수 (기본값은 0)입니다.                                                |
| `Maximum fractional digits`  | 사용할 최대 소수 자릿수입니다.                                                            |
| `Minimum significant digits` | 사용할 최소 유효 자릿수 (기본값 1)입니다.                                               |
| `Maximum significant digits` | 사용할 최대 유효 자릿수입니다.                                                           |

![X 축 설정의 스크린샷](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>계열 탭

계열 설정 탭에서는 차트의 계열에 대해 표시 되는 레이블과 색을 조정할 수 있습니다.

- `Series name`필드를 사용 하 여 데이터의 계열을 일치 시키고 일치 하는 경우 표시 레이블 및 색이 표시 됩니다.
- `Comment`이 주석은 변환기에서 표시 레이블을 지역화 하는 데 사용할 수 있으므로 템플릿 작성자에 게 유용 합니다.

![계열 설정의 스크린샷](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>다음 단계

- [통합 문서에서 타일](workbooks-tile-visualizations.md)을 만드는 방법에 대해 알아봅니다.
- [대화형 통합 문서](workbooks-interactive.md)를 만드는 방법에 대해 알아봅니다.