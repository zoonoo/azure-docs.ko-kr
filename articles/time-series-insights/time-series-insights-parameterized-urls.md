---
title: 매개 변수가 있는 URL을 사용하여 사용자 지정 보기 공유 - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights에서 매개 변수가 있는 URL을 사용하여 사용자 지정 탐색기 보기를 쉽게 공유하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 9bf857a66643b1e95ea2559601761a7217babad4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91665330"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>매개 변수가 있는 URL을 사용하여 사용자 지정 보기 공유

Azure Time Series Insights 탐색기에서 사용자 지정 보기를 공유하려면 사용자 지정 보기의 매개 변수가 있는 URL을 프로그래밍 방식으로 만들면 됩니다.

Azure Time Series Insights 탐색기는 URL에서 바로 환경의 보기를 지정할 수 있는 URL 쿼리 매개 변수를 지원합니다. 예를 들어 URL만 사용하여 대상 환경, 검색 조건자 및 원하는 시간 범위를 지정할 수 있습니다. 사용자가 사용자 지정 URL을 선택하면 인터페이스에서 Azure Time Series Insights 포털의 해당 자산과 직접 연결되는 링크를 제공합니다. 데이터 액세스 정책이 적용됩니다.

> [!TIP]
>
> * 무료 [Azure Time Series Insights 데모](https://insights.timeseries.azure.com/samples)를 확인합니다.
> * 함께 제공되는 [Azure Time Series Insights 탐색기](./time-series-insights-explorer.md) 설명서를 참조하세요.

## <a name="environment-id"></a>환경 ID

`environmentId=<guid>` 매개 변수는 대상 환경 ID를 지정합니다. 데이터 액세스 FQDN의 구성 요소로, Azure Portal의 환경 개요 오른쪽 위에서 찾을 수 있습니다. `env.timeseries.azure.com` 앞에 있는 모든 것이 환경 ID입니다.

예를 들어 `?environmentId=10000000-0000-0000-0000-100000000108`은 환경 ID 매개 변수입니다.

## <a name="time"></a>시간

매개 변수가 있는 URL을 사용하여 절대 또는 상대 시간 값을 지정할 수 있습니다.

### <a name="absolute-time-values"></a>절대 시간 값

절대 시간 값의 경우 `from=<integer>` 및 `to=<integer>` 매개 변수를 사용합니다.

* `from=<integer>`는 검색 범위에 대한 시작 시간의 JavaScript 밀리초 값입니다.
* `to=<integer>`는 검색 범위에 대한 종료 시간의 JavaScript 밀리초 값입니다.

> [!TIP]
> 날짜를 JavaScript 밀리초로 쉽게 변환하려면 [Epoch 및 Unix 타임스탬프 변환기](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)를 사용해 보세요.

### <a name="relative-time-values"></a>상대 시간 값

상대 시간 값의 경우 `relativeMillis=<value>`를 사용합니다. 여기서 *value* 는 API에서 받은 가장 최근 타임스탬프의 JavaScript 밀리초입니다.

예를 들어 `&relativeMillis=3600000`은 최근 60분의 데이터를 표시합니다.

허용되는 값은 Azure Time Series Insights 탐색기 **빠른 시간** 메뉴에 해당하며 다음을 포함합니다.

* `1800000`(지난 30분)
* `3600000`(지난 60분)
* `10800000`(지난 3시간)
* `21600000`(지난 6시간)
* `43200000`(지난 12시간)
* `86400000`(지난 24시간)
* `604800000`(지난 7일)
* `2592000000`(지난 30시간)

### <a name="optional-parameters"></a>선택적 매개 변수

`timeSeriesDefinitions=<collection of term objects>` 매개 변수는 Azure Time Series Insights 보기에 표시되는 조건자 조건을 지정합니다.

| 매개 변수 | URL 항목 | Description |
| --- | --- | --- |
| **name** | `\<string>` | *조건* 의 이름입니다. |
| **splitBy** | `\<string>` | *분할의 기준* 이 되는 열 이름입니다. |
| **measureName** | `\<string>` | *측정값* 의 열 이름입니다. |
| **predicate** | `\<string>` | 서버 쪽 필터링에 대한 *where* 절입니다. |
| **useSum** | `true` | 측정값에 합계를 사용하도록 지정하는 선택적 매개 변수입니다. |

> [!NOTE]
> `Events`가 선택한 **useSum** 측정값이면 기본적으로 count가 선택됩니다.  
> `Events`를 선택하지 않은 경우 기본적으로 average가 선택됩니다. |

* `multiChartStack=<true/false>` 키-값 쌍은 차트에서 누적을 사용하도록 설정합니다.
* `multiChartSameScale=<true/false>` 키-값 쌍은 선택적 매개 변수 내의 조건에서 동일한 Y축 눈금을 사용하도록 설정합니다.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>`를 사용하면 간격 슬라이더를 조정하여 보다 세부적이거나 부드럽고 더 집계된 차트 보기를 제공할 수 있습니다.  
* `timezoneOffset=<integer>` 매개 변수를 사용하면 차트가 표시될 표준 시간대를 UTC에 대한 오프셋으로 설정할 수 있습니다.

| 쌍 | Description |
| --- | --- |
| `multiChartStack=false` | 기본적으로 `true`가 사용하도록 설정되어 있으므로 스택에 `false`를 전달합니다. |
| `multiChartStack=false&multiChartSameScale=true` | 용어 전체에서 동일한 Y-축 눈금 사용하려면 스택을 사용하도록 설정해야 합니다.  기본적으로 `false`이므로 `true`를 전달하면 이 기능을 사용할 수 있습니다. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 단위는 `days`, `hours`, `minutes`, `seconds`, `milliseconds`입니다.  단위는 항상 대문자로 시작합니다. </br> **timeBucketSize** 에 대해 원하는 정수를 전달하여 단위 수를 정의합니다.  |
| `timezoneOffset=-<integer>` | 정수는 항상 밀리초 단위입니다. |

> [!NOTE]
> **timeBucketUnit** 값을 최대 7일까지 다듬을 수 있습니다.
> **timezoneOffset** 값은 UTC 및 현지 시간이 아닙니다.

### <a name="examples"></a>예제

Azure Time Series Insights 환경에 시계열 정의를 URL 매개 변수로 추가하려면 다음을 추가합니다.

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

다음에 대한 예제 시계열 정의를 사용합니다.

* 환경 ID
* 지난 60분 동안의 데이터
* 선택적 매개 변수를 구성하는 조건(**F1PressureID**, **F2TempStation**, **F3VibrationPL**)

보기에 대해 매개 변수가 있는 다음 URL을 생성할 수 있습니다.

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Azure Time Series Insights 탐색기 매개 변수가 있는 URL](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> 위의 [URL 예제를 사용](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
)하여 라이브 상태의 탐색기를 확인합니다.

위의 URL은 매개 변수가 있는 Azure Time Series Insights 탐색기 보기를 설명하고 표시합니다.

* 매개 변수가 있는 조건자

  [![Azure Time Series Insights 탐색기 매개 변수가 있는 조건자](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* 공유된 전체 차트 보기

  [![공유된 전체 차트 보기](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>다음 단계

* [C#을 사용하여 데이터를 쿼리](time-series-insights-query-data-csharp.md)하는 방법을 알아봅니다.

* [Azure Time Series Insights 탐색기](./time-series-insights-explorer.md)에 대해 알아봅니다.
