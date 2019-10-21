---
title: Azure Time Series Insights 사용자 지정 보기를 매개 변수가 있는 URL과 공유 | Microsoft Docs
description: 이 문서에서는 고객 보기를 쉽게 공유할 수 있도록 Azure Time Series Insights에서 매개 변수가 있는 URL을 개발하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: e862cd604263c5125259344baf2e990ad56a3291
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675852"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>매개 변수가 있는 URL을 사용하여 사용자 지정 보기 공유

Time Series Insights 탐색기에서 사용자 지정 보기를 공유 하려면 프로그래밍 방식으로 사용자 지정 보기의 매개 변수가 있는 URL을 만들 수 있습니다.

Time Series Insights 탐색기는 url 쿼리 매개 변수를 지원 하 여 URL에서 직접 환경에 보기를 지정 합니다. 예를 들어 URL만 사용하여 대상 환경, 검색 조건자 및 원하는 시간 범위를 지정할 수 있습니다. 사용자가 사용자 지정 URL을 선택 하는 경우 인터페이스는 Time Series Insights 포털에서 해당 자산에 직접 링크를 제공 합니다. 데이터 액세스 정책이 적용됩니다.

> [!TIP]
> * 무료 [Time Series Insights 데모](https://insights.timeseries.azure.com/samples)를 확인 하세요.
> * 함께 제공 되는 [Time Series Insights 탐색기](./time-series-insights-explorer.md) 설명서를 참조 하세요.

## <a name="environment-id"></a>환경 ID

`environmentId=<guid>` 매개 변수는 대상 환경 ID를 지정합니다. 데이터 액세스 FQDN의 구성 요소 이며, Azure Portal 환경 개요의 오른쪽 위 모서리에서 찾을 수 있습니다. `env.timeseries.azure.com` 앞에 있는 모든 것이 환경 ID입니다.

예를 들어 `?environmentId=10000000-0000-0000-0000-100000000108`은 환경 ID 매개 변수입니다.

## <a name="time"></a>시간

매개 변수가 있는 URL을 사용하여 절대 또는 상대 시간 값을 지정할 수 있습니다.

### <a name="absolute-time-values"></a>절대 시간 값

절대 시간 값의 경우 `from=<integer>` 및 `to=<integer>` 매개 변수를 사용합니다.

* `from=<integer>`는 검색 범위에 대한 시작 시간의 JavaScript 밀리초 값입니다.
* `to=<integer>`는 검색 범위에 대한 종료 시간의 JavaScript 밀리초 값입니다.

날짜에 대한 JavaScript 밀리초를 확인하는 방법은 [Epoch & Unix 타임스탬프 변환기](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)를 참조하세요.

### <a name="relative-time-values"></a>상대 시간 값

상대 시간 값의 경우 `relativeMillis=<value>`를 사용합니다. 여기서 *value*는 백 엔드에서 가장 최신 데이터의 JavaScript 밀리초입니다.

예를 들어 `&relativeMillis=3600000`은 최근 60분의 데이터를 표시합니다.

허용 되는 값은 Time Series Insights 탐색기 **빠른 시간** 메뉴에 해당 하며 다음을 포함 합니다.

* `1800000` (최근 30 분)
* `3600000` (지난 60 분)
* `10800000` (지난 3 시간)
* `21600000` (지난 6 시간)
* `43200000` (지난 12 시간)
* `86400000` (지난 24 시간)
* `604800000` (최근 7 일)
* `2592000000` (지난 30 시간)

### <a name="optional-parameters"></a>선택적 매개 변수

@No__t_0 매개 변수는 Time Series Insights 보기의 조건을 지정 합니다.

| 매개 변수를 포함해야 합니다. | URL 항목 | 설명 |
| --- | --- | --- |
| **name** | `\<string>` | *조건*의 이름입니다. |
| **splitBy** | `\<string>` | *분할의 기준*이 되는 열 이름입니다. |
| **measureName** | `\<string>` | *측정값*의 열 이름입니다. |
| **가** | `\<string>` | 서버 쪽 필터링에 대한 *where* 절입니다. |
| **.** | `true` | 측정값에 sum을 사용 하 여 지정 하는 선택적 매개 변수입니다. </br>  @No__t_0 선택 된 측정값이 면 기본적으로 개수가 선택 됩니다.  </br>  @No__t_0 선택 되지 않은 경우 기본적으로 평균이 선택 됩니다. |

* @No__t_0 키-값 쌍은 차트에서 누적을 사용 합니다.
* @No__t_0 키-값 쌍은 선택적 매개 변수 내에서 용어에 대해 동일한 Y 축 눈금을 사용할 수 있도록 합니다.  
* @No__t_0를 사용 하면 간격 슬라이더를 조정 하 여 보다 세분화 하거나 더 정교 하 고 더 세분화 된 차트 보기를 제공할 수 있습니다.  
* @No__t_0 매개 변수를 사용 하 여 UTC의 오프셋으로 표시할 차트의 표준 시간대를 설정할 수 있습니다.

| 쌍 | 설명 |
| --- | --- |
| `multiChartStack=false` | `true`은 기본적으로 사용 하도록 설정 되므로 스택에 `false`를 전달 합니다. |
| `multiChartStack=false&multiChartSameScale=true` | 용어 전체에서 동일한 Y-축 눈금 사용하려면 스택을 사용하도록 설정해야 합니다.  기본적으로 `false` 이므로 ' t r u e '를 전달 하면이 기능을 사용할 수 있습니다. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 단위 = 일, 시간, 분, 초, 밀리초.  단위는 항상 대문자로 시작합니다. </br> timeBucketSize에 원하는 정수를 전달하여 단위의 수를 정의합니다.  최대 7일까지 설정할 수 있습니다.  |
| `timezoneOffset=-<integer>` | 정수는 항상 밀리초 단위입니다. </br> 이 기능은 Time Series Insights 탐색기에서 사용 하는 것과 약간 다르며, 여기에서 로컬 (브라우저 시간) 또는 UTC를 선택할 수 있습니다. |

### <a name="examples"></a>예시

URL 매개 변수로 Time Series Insights 환경에 시계열 정의를 추가 하려면 다음을 추가 합니다.

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

다음에 대 한 예제 시계열 정의를 사용 합니다.

* 환경 ID
* 데이터의 최근 60 분
* 선택적 매개 변수를 구성 하는 용어 (F1PressureID, F2TempStation 및 F3VibrationPL)

뷰에 대해 다음 매개 변수가 있는 URL을 생성할 수 있습니다.

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> [URL을 사용 하 여](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}])탐색기 라이브를 확인 합니다.

위의 URL은 Time Series Insights 탐색기 보기를 설명 하 고 빌드합니다.

[![Time Series Insights 탐색기 용어](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

전체 보기 (차트 포함):

[![Chart 뷰](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>다음 단계

* 을 [사용 하 여 C#데이터를 쿼리 ](time-series-insights-query-data-csharp.md)하는 방법을 알아봅니다.

* [Time Series Insights 탐색기](./time-series-insights-explorer.md)에 대해 알아봅니다.
