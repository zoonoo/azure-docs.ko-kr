---
title: 매개 변수화된 URL로 사용자 지정 보기 공유 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: Azure Time Series Insights에서 사용자 지정된 탐색기 보기를 쉽게 공유할 수 있도록 매개 변수화된 URL을 만드는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 10616c8003d9bbbe42cb70bd1bac4193044907c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416987"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>매개 변수가 있는 URL을 사용하여 사용자 지정 보기 공유

타임 시리즈 인사이트 탐색기에서 사용자 지정 보기를 공유하려면 사용자 지정 보기의 매개 변수화된 URL을 프로그래밍 방식으로 만들 수 있습니다.

타임시리즈 인사이트 탐색기는 URL 쿼리 매개 변수를 지원하여 URL에서 직접 환경의 보기를 지정합니다. 예를 들어 URL만 사용하여 대상 환경, 검색 조건자 및 원하는 시간 범위를 지정할 수 있습니다. 사용자가 사용자 지정된 URL을 선택하면 인터페이스는 타임시리즈 인사이트 포털에서 해당 자산에 직접 대한 링크를 제공합니다. 데이터 액세스 정책이 적용됩니다.

> [!TIP]
> * 무료 [타임시리즈 인사이트 데모보기.](https://insights.timeseries.azure.com/samples)
> * 함께 제공되는 [타임시리즈 인사이트 탐색기](./time-series-insights-explorer.md) 설명서를 읽어보십시오.

## <a name="environment-id"></a>환경 ID

`environmentId=<guid>` 매개 변수는 대상 환경 ID를 지정합니다. 데이터 액세스 FQDN의 구성 요소이며 Azure 포털에서 환경 개요의 오른쪽 상단 모서리에서 찾을 수 있습니다. 그것은 앞에 모든 `env.timeseries.azure.com`것입니다.

예를 들어 `?environmentId=10000000-0000-0000-0000-100000000108`은 환경 ID 매개 변수입니다.

## <a name="time"></a>Time

매개 변수가 있는 URL을 사용하여 절대 또는 상대 시간 값을 지정할 수 있습니다.

### <a name="absolute-time-values"></a>절대 시간 값

절대 시간 값의 경우 `from=<integer>` 및 `to=<integer>` 매개 변수를 사용합니다.

* `from=<integer>`는 검색 범위에 대한 시작 시간의 JavaScript 밀리초 값입니다.
* `to=<integer>`는 검색 범위에 대한 종료 시간의 JavaScript 밀리초 값입니다.

> [!TIP]
> 날짜를 자바스크립트 밀리초로 쉽게 번역하려면 [유닉스 타임스탬프 변환기 &.](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)

### <a name="relative-time-values"></a>상대 시간 값

상대 시간 값의 `relativeMillis=<value>`경우 API에서 받은 가장 최근 타임스탬프에서 *JavaScript* 밀리초의 값이 있는 경우 를 사용합니다.

예를 들어 `&relativeMillis=3600000`은 최근 60분의 데이터를 표시합니다.

허용된 값은 타임시리즈 인사이트 탐색기 **퀵 타임** 메뉴에 해당하며 다음을 포함합니다.

* `1800000`(마지막 30분)
* `3600000`(마지막 60분)
* `10800000`(지난 3시간)
* `21600000`(지난 6시간)
* `43200000`(지난 12시간)
* `86400000`(지난 24시간)
* `604800000`(지난 7일)
* `2592000000`(지난 30시간)

### <a name="optional-parameters"></a>선택적 매개 변수

매개 `timeSeriesDefinitions=<collection of term objects>` 변수는 타임시리즈 인사이트 보기에 표시되는 조건자 용어를 지정합니다.

| 매개 변수 | URL 항목 | Description |
| --- | --- | --- |
| **name** | `\<string>` | *조건*의 이름입니다. |
| **분할 바이** | `\<string>` | *분할의 기준*이 되는 열 이름입니다. |
| **측정값 이름** | `\<string>` | *측정값*의 열 이름입니다. |
| **조건자** | `\<string>` | 서버 쪽 필터링에 대한 *where* 절입니다. |
| **useSum** | `true` | 측정값에 합계를 사용하여 지정하는 선택적 매개 변수입니다. |

> [!NOTE]
> 선택한 `Events` **useSum** 측정값인 경우 기본적으로 개수가 선택됩니다.  
> 이 `Events` 옵션을 선택하지 않으면 기본적으로 평균이 선택됩니다. |

* `multiChartStack=<true/false>` 키-값 쌍을 사용하면 차트에 스태킹할 수 있습니다.
* 키-값 쌍은 `multiChartSameScale=<true/false>` 선택적 매개변수 내에서 용어에 걸쳐 동일한 Y축 배율을 활성화합니다.  
* 을 `timeBucketUnit=<Unit>&timeBucketSize=<integer>` 사용하면 간격 슬라이더를 조정하여 차트의 보다 세분화적이거나 매끄럽고 집계된 보기를 제공할 수 있습니다.  
* 매개 `timezoneOffset=<integer>` 변수를 사용하면 차트를 UTC에 대한 오프셋으로 볼 시간대를 설정할 수 있습니다.

| 쌍 (들) | Description |
| --- | --- |
| `multiChartStack=false` | `true`스택에 전달되도록 `false` 기본적으로 활성화됩니다. |
| `multiChartStack=false&multiChartSameScale=true` | 용어 전체에서 동일한 Y-축 눈금 사용하려면 스택을 사용하도록 설정해야 합니다.  기본적으로 전달하면 `false` 이 기능을 `true` 사용할 수 있습니다. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 단위 `days`= `hours` `minutes`, `seconds` `milliseconds`, ,  단위는 항상 대문자로 시작합니다. </br> **timeBucketSize에**대해 원하는 정수를 전달하여 단위 수를 정의합니다.  |
| `timezoneOffset=-<integer>` | 정수는 항상 밀리초 단위입니다. |

> [!NOTE]
> **timeBucketUnit** 값은 최대 7일까지 스무딩할 수 있습니다.
> **timezoneOffset** 값은 UTC도 현지 시간도 아닙니다.

### <a name="examples"></a>예

열렬 인사이트 환경에 시간열 정의를 URL 매개 변수로 추가하려면 다음을 추가합니다.

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

다음에 대한 예제 연등선 정의를 사용합니다.

* 환경 ID
* 최근 60분 간의 데이터
* 선택적 매개 변수를 포함하는 용어 **(F1PressureID,** **F2TempStation**및 **F3VibrationPL)**

뷰에 대해 다음과 같은 매개 변수화된 URL을 생성할 수 있습니다.

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![타임 시리즈 인사이트 탐색기 매개 변수화 URL](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> 위의 URL [예제를 사용하여](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) 탐색기 라이브를 참조하십시오.

위의 URL은 매개변수화된 타임시리즈 인사이트 탐색기 보기를 설명하고 표시합니다. 

* 매개 변수화된 조건자입니다.

  [![타임 시리즈 인사이트 탐색기 매개 변수화된 조건자입니다.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* 공유된 전체 차트 보기입니다.

  [![공유된 전체 차트 보기입니다.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>다음 단계

* C# 을 [사용하여 데이터를 쿼리하는](time-series-insights-query-data-csharp.md)방법에 대해 알아봅니다.

* [타임시리즈 인사이트 탐색기.](./time-series-insights-explorer.md)
