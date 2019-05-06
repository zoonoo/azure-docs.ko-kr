---
title: 시계열 변칙 검색 및 Azure 데이터 탐색기에서 예측
description: 변칙 검색 및 Azure 데이터 탐색기를 사용 하 여 예측을 위해 시계열 데이터를 분석 하는 방법에 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 934dfd3334b6f433c7acdf9816a3fb5e24f0430f
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872002"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>변칙 검색 및 Azure 데이터 탐색기에서 예측

Azure 데이터 탐색기는 클라우드 서비스 또는 IoT 장치에서 원격 분석 데이터의 지속적으로 수집을 수행합니다. 이 데이터는 모니터링 서비스 상태, 실제 프로덕션 프로세스, 사용 추세 및 부하 예측 같은 다양 한 정보에 대 한 분석 됩니다. 선택한 메트릭이 메트릭 정상적인 기준 일반적인 패턴을 기준으로 편차 패턴을 찾을 시계열에서 분석을 수행 합니다. Azure 데이터 탐색기에 생성, 조작 및 여러 시계열 분석에 대 한 기본 지원을 포함합니다. 만들 수 있으며 거의 실시간으로 모니터링 솔루션 및 워크플로 사용 하도록 설정 시간 (초)에서 수천 개의 시계열 분석.

이 문서에서는 Azure Data Explorer 시간 시계열 변칙 검색 및 예측 기능을 자세히 설명합니다. 적용 가능 기간 시리즈 함수는 모델을 기반으로 강력한 잘 알려진 분해, 여기서 각 원래 시계열을 서비스로 분해할 계절성, 추세, 및 나머지 구성 요소입니다. 비정상 상태는 계절성을 추정 하면 됩니다 예측 하는 동안 나머지 구성 요소에서 이상 값으로 감지 하 고 추세 구성 요소. Azure Data Explorer 구현에는 자동 계절성 검색, 강력한 이상 분석 및 시간 (초)에 시계열의 프로세스 수천 대에 벡터화 된 구현에서 기본 분해 모델을 상당히 개선 되었습니다.

## <a name="prerequisites"></a>필수 조건

읽기 [Azure 데이터 탐색기에서 시계열 분석 시간](/azure/data-explorer/time-series-analysis) 시간 시리즈 기능에 대 한 개요입니다.

## <a name="time-series-decomposition-model"></a>시간 시계열 분해 모델

시계열 예측 및 변칙 검색에 대 한 azure 데이터 탐색기 네이티브 구현에는 잘 알려진 분해 모델을 사용합니다. 이 모델은 정기적이 고 서비스 트래픽, 하트 비트 구성 요소 및 IoT 정기적인 측정이 향후 메트릭 값을 예측 하 고 비정상적인 것을 감지 하기 등의 추세 동작 매니페스트를 예상 하는 메트릭의 시계열에 적용 됩니다. 이 회귀 프로세스의 것으로 가정 하는 이전에 알려진 이외의 계절별 추세 동작을 시리즈 무작위로 분산 시간. 다음에서 계절성을 향후 메트릭 값 및 추세 구성 요소를 통칭 하 여 기준, 명명 된 예측 하 고 나머지 파트를 무시할 수 있습니다. 또한 나머지 부분만 사용 하 여 이상 값 분석을 기반으로 하는 비정상적인 값을 검색할 수 있습니다.
함수를 사용 하 여 분해 모델을 만들려면 [ `series_decompose()` ](/azure/kusto/query/series-decomposefunction)합니다. `series_decompose()` 함수는 시계열 집합 및 각 시계열의 계절성, 추세, 나머지, 및 기본 구성 요소를 자동으로 분해 합니다. 

예를 들어 다음 쿼리를 사용 하 여 내부 웹 서비스의 트래픽을 분해할 수 있습니다.

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![시간 시계열 분해](media/anomaly-detection/series-decompose-timechart.png)

* 원래 시계열 이라고 **num** 빨간색으로 합니다. 
* 함수를 사용 하 여 계절성의 자동 검색으로 프로세스를 시작 [ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) 추출 합니다 **계절** (자주색)의 패턴.
* 함수를 사용 하 여 선형 회귀를 실행 하 고 원래 시계열에서 계절성 패턴을 뺍니다 [ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction) 찾으려고 합니다 **추세** 구성 요소 (옅은 파란색).
* 함수 뺍니다 추세 이며 나머지는 **잔여** 구성 요소 (녹색).
* 함수는 계절성을 추가 하 고 추세 구성 요소를 생성 하는 마지막으로 **기준** (파란색)에 있습니다.

## <a name="time-series-anomaly-detection"></a>시계열 변칙 검색

함수 [ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction) 시계열 집합에서 비정상적인 요소를 찾습니다. 이 함수를 호출 `series_decompose()` 분해 모델 및 다음 실행을 만들려고 [ `series_outliers()` ](/azure/kusto/query/series-outliersfunction) 잔여 구성 요소입니다. `series_outliers()` fence은 Tukey의 테스트를 사용 하 여 잔여 구성 요소의 각 요소에 대 한 이상 점수를 계산 합니다. 1.5 보다 높거나 낮은-1.5 변칙 점수에는 각각 거부 또는 가벼운 이상 증가 나타냅니다. 변칙 점수 3.0 위 또는 아래-3.0입니다. 강력한 변칙을 나타냅니다. 

다음 쿼리를 사용 하면 변칙을 검색할 내부 웹 서비스 트래픽이 있습니다.

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![시계열 변칙 검색](media/anomaly-detection/series-anomaly-detection.png)

* 빨간색으로 원래 시계열입니다. 
* 기준선 (계절별 추세 +) 구성 요소 (파란색).
* 비정상적인 요소 (자주색) 원래 시계열을 기반으로 합니다. 비정상적인 지점 예상된 기준 값에서 크게 벗어난 합니다.

## <a name="time-series-forecasting"></a>시계열 예측

함수 [ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction) 집합이 시계열의 미래 값을 예측 합니다. 이 함수 호출 `series_decompose()` 모델 및 그런 다음 각 시계열에 대 한 분해를 빌드하려면 미래 기준 구성 요소를 추정 합니다.

다음 쿼리를 사용 하면 다음 주 웹 서비스 트래픽의 예측할 수 있습니다.

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![시계열 예측](media/anomaly-detection/series-forecasting.png)

* 원래 메트릭 (빨강)입니다. 미래 값이 없는 및 기본적으로 0으로 설정 합니다.
* 다음 주 값을 예측 하려면 (파란색)의 초기 구성 요소를 추정 합니다.

## <a name="scalability"></a>확장성

Azure 데이터 탐색기 쿼리 언어 구문을 통해 여러 시계열 처리에 대 한 단일 호출 수 있습니다. 효과적인 변칙 검색 및 수천 개의 거의 실시간 시나리오에서 카운터를 모니터링 하는 경우 예측에 중요 한 빠른 성능을 위해 최적화 된 고유 구현 수 있습니다.

다음 쿼리는 동시에 세 시계열의 처리를 보여 줍니다.

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![시간 시계열 확장성](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>요약

이 문서는 기반이 되는 기본 분해 모델을 크게 향상 시켜 주는 시계열 변칙 검색 및 예측에 대 한 네이티브 Azure 데이터 탐색기 함수 설명 합니다. 변칙 검색 각 원래 시계열 분해 하 고 예측 수행 됩니다. 시간 시계열 변칙 검색 및 예측 기능 거의 요청, 오류 감지 및 예측 유지 관리 등의 실시간 모니터링 시나리오에 사용 됩니다 하 고 예측을 로드 합니다.

## <a name="next-steps"></a>다음 단계

에 대 한 자세한 [기계 학습 기능](/azure/data-explorer/machine-learning-clustering) Azure 데이터 탐색기에서.