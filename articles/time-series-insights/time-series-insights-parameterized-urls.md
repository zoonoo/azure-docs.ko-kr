---
title: 매개 변수가 있는 Url을 사용 하 여 사용자 지정 보기 공유-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights에서 사용자 지정 된 탐색기 보기를 쉽게 공유 하기 위해 매개 변수가 있는 Url을 만드는 방법을 알아봅니다.
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
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665330"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>매개 변수가 있는 URL을 사용하여 사용자 지정 보기 공유

Azure Time Series Insights 탐색기에서 사용자 지정 보기를 공유 하려면 프로그래밍 방식으로 사용자 지정 보기의 매개 변수가 있는 URL을 만들 수 있습니다.

Azure Time Series Insights 탐색기는 url 쿼리 매개 변수를 지원 하 여 URL에서 직접 환경에 보기를 지정 합니다. 예를 들어 URL만 사용하여 대상 환경, 검색 조건자 및 원하는 시간 범위를 지정할 수 있습니다. 사용자가 사용자 지정 URL을 선택 하는 경우 인터페이스는 Azure Time Series Insights 포털에서 해당 자산에 직접 링크를 제공 합니다. 데이터 액세스 정책이 적용됩니다.

> [!TIP]
>
> * 무료 [Azure Time Series Insights 데모](https://insights.timeseries.azure.com/samples)를 확인 하세요.
> * 함께 제공 되는 [Azure Time Series Insights 탐색기](./time-series-insights-explorer.md) 설명서를 참조 하세요.

## <a name="environment-id"></a>환경 ID

`environmentId=<guid>` 매개 변수는 대상 환경 ID를 지정합니다. 데이터 액세스 FQDN의 구성 요소 이며, Azure Portal 환경 개요의 오른쪽 위 모서리에서 찾을 수 있습니다. 모든 것이 앞에 나옵니다 `env.timeseries.azure.com` .

예를 들어 `?environmentId=10000000-0000-0000-0000-100000000108`은 환경 ID 매개 변수입니다.

## <a name="time"></a>시간

매개 변수가 있는 URL을 사용하여 절대 또는 상대 시간 값을 지정할 수 있습니다.

### <a name="absolute-time-values"></a>절대 시간 값

절대 시간 값의 경우 `from=<integer>` 및 `to=<integer>` 매개 변수를 사용합니다.

* `from=<integer>`는 검색 범위에 대한 시작 시간의 JavaScript 밀리초 값입니다.
* `to=<integer>`는 검색 범위에 대한 종료 시간의 JavaScript 밀리초 값입니다.

> [!TIP]
> 날짜를 JavaScript 밀리초로 쉽게 변환 하려면 [Epoch & Unix 타임 스탬프 변환기](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)를 사용해 보세요.

### <a name="relative-time-values"></a>상대 시간 값

상대 시간 값의 경우를 사용 `relativeMillis=<value>` 합니다. 여기서 *값* 은 API에서 받은 최신 타임 스탬프에서 JavaScript 밀리초에 있습니다.

예를 들어 `&relativeMillis=3600000`은 최근 60분의 데이터를 표시합니다.

허용 되는 값은 Azure Time Series Insights 탐색기 **빠른 시간** 메뉴에 해당 하며 다음을 포함 합니다.

* `1800000` (최근 30 분)
* `3600000` (지난 60 분)
* `10800000` (지난 3 시간)
* `21600000` (지난 6 시간)
* `43200000` (지난 12 시간)
* `86400000` (지난 24 시간)
* `604800000` (최근 7 일)
* `2592000000` (지난 30 시간)

### <a name="optional-parameters"></a>선택적 매개 변수

`timeSeriesDefinitions=<collection of term objects>`매개 변수는 Azure Time Series Insights 뷰에 표시 되는 조건자 용어를 지정 합니다.

| 매개 변수 | URL 항목 | Description |
| --- | --- | --- |
| **name** | `\<string>` | *조건*의 이름입니다. |
| **splitBy** | `\<string>` | *분할의 기준*이 되는 열 이름입니다. |
| **measureName** | `\<string>` | *측정값*의 열 이름입니다. |
| **가** | `\<string>` | 서버 쪽 필터링에 대한 *where* 절입니다. |
| **.** | `true` | 측정값에 sum을 사용 하 여 지정 하는 선택적 매개 변수입니다. |

> [!NOTE]
> `Events`가 선택한가 나 **um** 측정값이 면 기본적으로 개수가 선택 됩니다.  
> `Events`을 선택 하지 않으면 기본적으로 평균이 선택 됩니다. |

* `multiChartStack=<true/false>`키-값 쌍은 차트에서 누적을 사용 합니다.
* `multiChartSameScale=<true/false>`키-값 쌍은 선택적 매개 변수 내의 여러 용어에서 동일한 Y 축 배율을 사용할 수 있도록 합니다.  
* 를 `timeBucketUnit=<Unit>&timeBucketSize=<integer>` 사용 하면 간격 슬라이더를 조정 하 여 차트에 대 한 보다 세분화 되거나 더 정교 하 고 세분화 된 보기를 제공할 수 있습니다.  
* `timezoneOffset=<integer>`매개 변수를 사용 하면 UTC에 대 한 오프셋으로 표시할 차트의 표준 시간대를 설정할 수 있습니다.

| 쌍 | Description |
| --- | --- |
| `multiChartStack=false` | `true` 는 기본적으로 사용 하도록 설정 되므로 스택에 전달 됩니다 `false` . |
| `multiChartStack=false&multiChartSameScale=true` | 용어 전체에서 동일한 Y-축 눈금 사용하려면 스택을 사용하도록 설정해야 합니다.  `false`기본적으로를 전달 하면 `true` 이 기능을 사용할 수 있습니다. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 단위 = `days` , `hours` , `minutes` , `seconds` , `milliseconds` .  단위는 항상 대문자로 시작합니다. </br> **TimeBucketSize**에 필요한 정수를 전달 하 여 단위 수를 정의 합니다.  |
| `timezoneOffset=-<integer>` | 정수는 항상 밀리초 단위입니다. |

> [!NOTE]
> **timeBucketUnit** 값은 최대 7 일까 지 사용할 수 있습니다.
> **timezoneOffset** 값은 UTC 및 현지 시간이 아닙니다.

### <a name="examples"></a>예

URL 매개 변수로 Azure Time Series Insights 환경에 시계열 정의를 추가 하려면 다음을 추가 합니다.

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

다음에 대 한 예제 시계열 정의를 사용 합니다.

* 환경 ID
* 데이터의 최근 60 분
* 선택적 매개 변수를 구성 하는 용어 (**F1PressureID**, **F2TempStation**및 **F3VibrationPL**)

뷰에 대해 다음 매개 변수가 있는 URL을 생성할 수 있습니다.

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Azure Time Series Insights 탐색기 매개 변수가 있는 URL](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> 위의 [URL 예제를 사용 하 여](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
) 탐색기 라이브를 확인 합니다.

위의 URL은 매개 변수가 있는 Azure Time Series Insights 탐색기 뷰를 설명 하 고 표시 합니다.

* 매개 변수가 있는 조건자입니다.

  [![Azure Time Series Insights 탐색기 매개 변수가 있는 조건자입니다.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* 공유 된 전체 차트 뷰입니다.

  [![공유 된 전체 차트 뷰입니다.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>다음 단계

* [C #을 사용 하 여 데이터를 쿼리](time-series-insights-query-data-csharp.md)하는 방법을 알아봅니다.

* [Azure Time Series Insights 탐색기](./time-series-insights-explorer.md)에 대해 알아봅니다.
