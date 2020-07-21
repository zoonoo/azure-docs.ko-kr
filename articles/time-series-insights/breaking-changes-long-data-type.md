---
title: Long 데이터 형식에 대 한 지원 추가 | Microsoft Docs
description: Long 데이터 형식에 대 한 지원
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: dpalled
ms.openlocfilehash: c31ca7fd3eca89159d583b8a51b59a7bd6b8ed67
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531427"
---
# <a name="adding-support-for-long-data-type"></a>Long 데이터 형식에 대 한 지원 추가

이러한 변경 내용은 Gen2 환경에만 적용 됩니다. Gen1 환경이 있는 경우 이러한 변경 내용을 무시할 수 있습니다.

사용자에 게 영향을 줄 수 있는 Azure Time Series Insights Gen2에 숫자 데이터를 저장 하 고 인덱싱하는 방법을 변경 하 고 있습니다. 아래 사례의 영향을 받는 경우 가능한 한 빨리 변경 해야 합니다. 사용자의 지역에 따라 데이터는 길고 6 월 30 일에서 30 2020 년 6 월 30 일 사이에 Long으로 인덱싱되는 것으로 시작 됩니다. 이 변경 내용에 대 한 질문이 나 궁금한 사항이 있는 경우 Azure Portal를 통해 지원 티켓을 제출 하 고이 통신을 언급 합니다.

이 변경 내용은 다음과 같은 경우에 영향을 줍니다.

1. 현재 시계열 모델 변수를 사용 하 고 원격 분석 데이터에 정수 계열 데이터 형식만 보내는 경우
1. 현재 시계열 모델 변수를 사용 하 고 원격 분석 데이터에 정수 계열 및 비정 수 데이터 형식을 모두 보내는 경우
1. 범주 변수를 사용 하 여 정수 값을 범주에 매핑하는 경우
1. JavaScript SDK를 사용 하 여 사용자 지정 프런트 엔드 응용 프로그램을 빌드하는 경우
1. WS (웜 저장소)의 1000 속성 이름 제한에 근접 하 고 정수 계열 및 비정 수 데이터를 모두 보내는 경우 [Azure Portal](https://portal.azure.com/)에서 속성 수를 메트릭으로 볼 수 있습니다.

위의 경우에 해당 하는 경우에는 모델을 변경 하 여이 변경 내용을 적용 해야 합니다. Api를 사용 하 여 Azure Time Series Insights Gen2 탐색기와 사용자 지정 클라이언트 모두에서 변수 정의의 시계열 식을 업데이트 하 고 권장 되는 변경 내용을 적용 합니다. 자세한 내용은 다음을 참조하세요.

IoT 솔루션 및 제약 조건에 따라 Azure Time Series Insights Gen2 환경으로 전송 되는 데이터를 볼 수 없습니다. 데이터가 정수 계열 전용 이거나 정수 계열 및 비정 수 모두 인지 확실 하지 않은 경우 몇 가지 옵션을 사용할 수 있습니다. 기능이 해제 될 때까지 기다렸다가 탐색기 UI에서 원시 이벤트를 탐색 하 여 두 개의 개별 열에 저장 된 속성을 이해할 수 있습니다. 모든 숫자 태그에 대해 아래에서 변경을 미리 스키마를 보다 잘 이해 하 고 탐색할 수 있도록 이벤트의 하위 집합을 저장소로 임시로 라우팅할 수 있습니다. 이벤트를 저장 하려면 Event Hubs에 대 한 [이벤트 캡처](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) 를 설정 하거나 IoT Hub에서 Azure Blob Storage로 [라우팅합니다](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) . 이벤트 [허브 탐색기](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)를 통하거나 [이벤트 프로세서 호스트](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events)를 사용 하 여 데이터를 관찰할 수도 있습니다. IoT Hub 사용 하는 경우 기본 제공 끝점 [에 액세스](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) 하는 방법에 대 한 설명서를 참조 하세요.

이러한 변경의 영향을 받으며 위의 날짜로 변경할 수 없는 경우 쿼리 Api 또는 Time Series Insights 탐색기를 통해 액세스 되는 영향을 받는 시계열 변수가 *null* 을 반환 하는 문제가 발생할 수 있습니다 (즉, 탐색기에서 데이터를 표시 하지 않음).

## <a name="recommended-changes"></a>권장 변경 내용

사례 1 & 2: **시계열 모델 변수를 사용 하 고 정수 계열 데이터 형식만 보내거나 원격 분석 데이터에서 정수 계열 및 비정 두 형식을 모두 보냅니다.**

현재 정수 원격 분석 데이터를 전송 하는 경우 데이터는 "propertyValue_double" 및 "propertyValue_long"의 두 열로 나뉩니다.

변경 내용이 적용 되 고 이전에는 "propertyValue_double"의 이전 수집 (및 이후 수집) 숫자 데이터가 복사 되지 않을 때 정수 데이터가 "propertyValue_long"에 기록 됩니다.

"PropertyValue" 속성에 대해 이러한 두 열에서 데이터를 쿼리하려면 TSX에서 *병합 ()* 스칼라 함수를 사용 해야 합니다. 함수는 동일한 데이터 형식의 인수를 수락 하 고 인수 목록에서 null이 아닌 첫 번째 값을 반환 합니다 ( [여기](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)에서 사용법에 대해 자세히 알아보세요).

### <a name="variable-definition-in-time-series-explorer---numeric"></a>시계열 탐색기의 변수 정의-Numeric

*이전 변수 정의:*

[![이전 변수 정의](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*새 변수 정의:*

[![새 변수 정의](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

사용자 지정 [시계열 식](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) 으로 *"병합 ($event. toDouble, $event)"* 을 사용할 수도 있습니다.

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>시계열 쿼리 Api를 사용 하 여 인라인 변수 정의-Numeric

*이전 변수 정의:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*새 변수 정의:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

사용자 지정 [시계열 식](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) 으로 *"병합 ($event. toDouble, $event)"* 을 사용할 수도 있습니다.

> [!NOTE]
> 사용할 수 있는 모든 위치 (시계열 모델, 저장 된 쿼리, Power BI 커넥터 쿼리)에서 이러한 변수를 업데이트 하는 것이 좋습니다.

사례 3: **범주 변수를 사용 하 여 정수 값을 범주에 매핑**

현재 정수 값을 범주에 매핑하는 범주 변수를 사용 하는 경우 toLong 함수를 사용 하 여 Double 형식에서 Long 형식으로 데이터를 변환할 가능성이 높습니다. 위의 경우와 같이 Double 및 Long DataType 열을 병합 해야 합니다.

### <a name="variable-definition-in-time-series-explorer---categorical"></a>시계열 탐색기의 변수 정의-범주

*이전 변수 정의:*

[![이전 변수 정의](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*새 변수 정의:*

[![새 변수 정의](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

사용자 지정 [시계열 식](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) 으로 *"병합 ($event. toDouble, $event)"* 을 사용할 수도 있습니다.

범주 변수에는 여전히 값이 정수 형식 이어야 합니다. 병합 ()의 모든 인수에 대 한 데이터 형식은 사용자 지정 시계열 식의 Long 형식 이어야 합니다 [.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>시계열 쿼리 Api를 사용 하 여 인라인 변수 정의-범주

*이전 변수 정의:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*새 변수 정의:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

범주 변수에는 여전히 값이 정수 형식 이어야 합니다. 병합 ()의 모든 인수에 대 한 데이터 형식은 사용자 지정 시계열 식의 Long 형식 이어야 합니다 [.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> 사용할 수 있는 모든 위치 (시계열 모델, 저장 된 쿼리, Power BI 커넥터 쿼리)에서 이러한 변수를 업데이트 하는 것이 좋습니다.

사례 4: **JAVASCRIPT SDK를 사용 하 여 사용자 지정 프런트 엔드 응용 프로그램 빌드**

위의 예제에서 설명한 것 처럼 위의 1-3 사례에 영향을 주고 사용자 지정 응용 프로그램을 작성 하는 경우에는 *병합 ()* 함수를 사용 하도록 쿼리를 업데이트 해야 합니다.

사례 5: **근접 웜 저장소 1000 속성 제한**

다 수의 속성을 가진 웜 저장소 사용자 인 경우이 변경으로 인해 1000 WS 속성-이름 제한을 초과 하 여 환경을 푸시하는 경우 Azure Portal를 통해 지원 티켓을 제출 하 고이 통신을 언급 합니다.

## <a name="next-steps"></a>다음 단계

* 지원 되는 데이터 형식에 대 한 전체 목록을 보려면 [지원 되는 데이터 형식](concepts-supported-data-types.md) 을 참조 하세요.
