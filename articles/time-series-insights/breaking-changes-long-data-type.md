---
title: Azure Time Series Insights Gen2에서 Long 데이터 형식 지원 | Microsoft Docs
description: Azure Time Series Insights Gen2에서 Long 데이터 형식 지원.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/07/2020
ms.custom: dpalled
ms.openlocfilehash: 0f7ac9844bfe7eac7094d3b7fdf653e07f236599
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780827"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2에서 Long 데이터 형식에 대한 지원 추가

Long 데이터 형식에 대한 지원이 추가되면 Azure Time Series Insights Gen2 환경에서만 숫자 데이터를 저장하고 인덱싱하는 방법에 영향을 미칩니다. Gen1 환경이 있는 경우 이러한 변경 내용을 무시할 수 있습니다.

2020년 6월 29일 또는 6월 30일부터 사용자의 지역에 따라 데이터가 **Long** 및 **Double** 로 인덱싱됩니다.  이 변경에 대한 질문이나 질문이 있는 경우 Azure Portal 통해 지원 티켓을 제출하고 이 통신에 대해 언급하세요.

다음 사례의 영향을 받는 경우 권장 사항을 변경합니다.

- **사례 1**: 현재 시계열 모델 변수를 사용하고 원격 분석 데이터에 정수 계열 데이터 형식만 보냅니다.
- **사례 2**: 현재 시계열 모델 변수를 사용하고 원격 분석 데이터에 정수 계열 및 비통합 데이터 형식을 모두 보냅니다.
- **사례 3**: 범주 변수를 사용하여 정수 값을 범주에 매핑합니다.
- **사례 4**: JavaScript SDK를 사용하여 사용자 지정 프런트 엔드 애플리케이션을 빌드합니다.
- **사례 5**: 웜 저장소에서 1,000개 속성 이름 제한에 근접하고 정수 및 비통합 데이터를 모두 보냅니다. 속성 수는 [Azure Portal](https://portal.azure.com/)의 메트릭으로 볼 수 있습니다.

사례 중 어느 것이라도 적용되는 경우 모델을 변경합니다. 변수 정의에서 TSX(시계열 식)를 권장 변경 내용으로 업데이트합니다. 다음을 모두 업데이트합니다.

- Azure Time Series Insights 탐색기
- API를 사용하는 모든 사용자 지정 클라이언트

IoT 솔루션 및 제약 조건에 따라 Azure Time Series Insights Gen2 환경으로 전송되는 데이터에 대한 가시성이 없을 수 있습니다. 데이터가 정수만 포함하는지 아니면 정수와 비정수를 모두 포함하는지 불확실하다면 몇 가지 옵션이 있습니다.

- 기능이 해제될 때까지 기다릴 수 있습니다. 그런 다음 탐색기 UI에서 원시 이벤트를 탐색하여 두 개의 개별 열에 저장되는 속성을 파악합니다.
- 모든 숫자 태그에 권장되는 변경 작업을 미리 할 수 있습니다.
- 이벤트의 하위 집합을 스토리지로 임시로 라우트하여 스키마를 보다 잘 이해하고 탐색할 수 있습니다.

이벤트를 저장하려면 Azure Event Hubs에 대한 [이벤트 캡처](../event-hubs/event-hubs-capture-overview.md)를 활성화하거나 IoT Hub에서 Azure Blob Storage로 [라우팅](../iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)합니다.

[이벤트 허브 탐색기](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)를 통하거나 [이벤트 프로세서 호스트](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md#receive-events)를 사용하여 데이터를 관찰할 수도 있습니다.

IoT Hub를 사용하는 경우 기본 제공 엔드포인트에 액세스하는 방법에 대한 [기본 제공 엔드포인트에서 디바이스-클라우드 메시지 읽기](../iot-hub/iot-hub-devguide-messages-read-builtin.md)로 이동합니다.

> [!NOTE]
> 권장 사항을 변경하지 않는 경우 중단이 발생할 수 있습니다. 예를 들어 쿼리 API 또는 Time Series Insights 탐색기를 통해 액세스되는 영향을 받는 Time Series Insights 변수는 **null** 을 반환합니다(즉, 탐색기에서 데이터를 표시하지 않습니다).

## <a name="recommended-changes"></a>권장 변경 내용

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>사례 1: 시계열 모델 변수를 사용하여 원격 분석 데이터에서 정수 계열 데이터 형식만 보내기

사례 1의 권장 변경 사항은 사례 2의 경우와 동일합니다. 사례 2에 대한 섹션의 지침을 따릅니다.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>사례 2: 시계열 모델 변수를 사용하여 원격 분석 데이터에서 정수 및 비정수 계열 두 형식 모두 보내기

현재 정수 원격 분석 데이터를 전송하는 경우 데이터는 두 개의 열로 나뉩니다.

- **propertyValue_double**
- **propertyValue_long**

정수 데이터는 **propertyValue_long** 에 기록됩니다. 이전에 수집된 (그리고 나중에 수집될) 숫자 데이터는 **propertyValue_double** 안에 있고, 복사되지 않습니다.

이 두 열에서 **propertyValue** 속성에 대해 데이터를 쿼리하려면 TSX에서 **coalesce()** 스칼라 함수를 사용해야 합니다. 이 함수는 동일한 **DataType** 의 인수를 허용하고 인수 목록에서 null이 아닌 첫 번째 값을 반환합니다. 자세한 내용은 [Azure Time Series Insights Gen2 데이터 액세스 개념](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions)을 참조하세요.

#### <a name="variable-definition-in-tsx---numeric"></a>TSX의 변수 정의 - 숫자

*이전 변수 정의:*

[![PropertyValue 숫자 변수의 새 변수 추가 대화 상자를 보여주는 스크린샷입니다.](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*새 변수 정의:*

[![사용자 지정 값이 숫자인 PropertyValue 변수에 대한 새 변수 추가 대화 상자를 보여주는 스크린샷입니다.](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

**coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** 을 사용자 지정 [시계열 식](/rest/api/time-series-insights/reference-time-series-expression-syntax)으로 사용할 수도 있습니다.

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>TSX 쿼리 API를 사용하는 인라인 변수 정의 - 숫자

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

**coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** 을 사용자 지정 [시계열 식](/rest/api/time-series-insights/reference-time-series-expression-syntax)으로 사용할 수도 있습니다.

> [!NOTE]
> 이러한 변수를 사용할 수 있는 모든 곳에서 업데이트하는 것을 권장합니다. 이러한 위치에는 시계열 모델, 저장된 쿼리 및 Power BI 커넥터 쿼리가 포함됩니다.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>사례 3: 범주 변수를 사용하여 정수 값을 범주에 매핑

현재 정수 값을 범주에 매핑하는 범주 변수를 사용하는 경우 **toLong** 함수를 사용하여 데이터를 **Double** 형식에서 **Long** 형식으로 변환할 수 있습니다. 사례 1 및 2와 마찬가지로 **Double** 및 **Long** **DataType** 열을 병합해야 합니다.

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>시계열 탐색기의 변수 정의 - 범주

*이전 변수 정의:*

[![PropertyValue 범주 변수에 대한 새 변수 추가 대화 상자를 보여주는 스크린샷입니다.](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*새 변수 정의:*

[![사용자 지정 값을 사용하여 PropertyValue 범주 변수에 대한 새 변수 추가 대화 상자를 보여주는 스크린샷입니다.](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

**coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** 을 사용자 지정 [시계열 식](/rest/api/time-series-insights/preview#time-series-expression-and-syntax)으로 사용할 수도 있습니다.

범주 변수에서 여전히 값은 정수 형식이어야 합니다. **coalesce()** 에 있는 모든 인수의 **DataType** 은 사용자 지정 [시계열 식](/rest/api/time-series-insights/reference-time-series-expression-syntax)에서 **Long** 형식이어야 합니다.

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>TSX 쿼리 API를 사용하는 인라인 변수 정의 - 범주

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

범주 변수에서 여전히 값은 정수 형식이어야 합니다. **coalesce()** 에 있는 모든 인수의 **DataType** 은 사용자 지정 [시계열 식](/rest/api/time-series-insights/reference-time-series-expression-syntax)에서 **Long** 형식이어야 합니다.

> [!NOTE]
> 이러한 변수를 사용할 수 있는 모든 곳에서 업데이트하는 것을 권장합니다. 이러한 위치에는 시계열 모델, 저장된 쿼리 및 Power BI 커넥터 쿼리가 포함됩니다.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>사례 4: JavaScript SDK를 사용하여 사용자 지정 프런트 엔드 애플리케이션 빌드

사례 1~3의 영향을 받고 사용자 지정 애플리케이션을 빌드하는 경우 이전 예제에서 설명한 대로 **coalesce()** 함수를 사용하도록 쿼리를 업데이트해야 합니다.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>사례 5: 웜 저장소 1,000개 속성 제한에 근접

많은 수의 속성이 있는 웜 저장소 사용자이고 이 변경으로 인해 환경이 1,000개의 웜 저장소 속성 이름 제한을 초과한다고 생각하는 경우 Azure Portal을 통해 지원 티켓을 제출하고 이 통신을 언급하세요.

## <a name="next-steps"></a>다음 단계

- [지원되는 데이터 유형](concepts-supported-data-types.md)의 전체 목록을 참조하세요.
