---
title: 문제 진단, 문제 해결 및 해결-Azure Time Series Insights | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights 환경의 일반적인 문제를 진단 하 고 해결 하 고 해결 하는 방법을 설명 합니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 12/06/2019
ms.custom: seodec18
ms.openlocfilehash: 3e73afa89ee61243784c5952eeda26a79d508dee
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863413"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Time Series Insights 환경에서 문제 진단 및 해결

이 문서에서는 Time Series Insights 환경에서 발생할 수 있는 몇 가지 문제를 설명 합니다. 이 문서에서는 잠재적인 원인 및 해결 방법을 제안합니다.

## <a name="video"></a>비디오

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>일반적인 Time Series Insights 고객 과제 및 완화에 대해 알아봅니다.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>문제: 데이터가 표시 되지 않습니다.

[Azure Time Series Insights 탐색기](https://insights.timeseries.azure.com)에 데이터가 표시되지 않는 문제는 다음과 같은 몇 가지 일반적인 이유로 인해 발생할 수 있습니다.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>원인 A: 이벤트 원본 데이터가 JSON 형식이 아닙니다.

Azure Time Series Insights는 JSON 데이터만 지원합니다. JSON 샘플의 경우 [지원 되는 json 셰이프](./how-to-shape-query-json.md)를 참조 하세요.

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>원인 B: 이벤트 원본 키에 필요한 사용 권한이 없습니다.

* Azure IoT Hub에 있는 IoT Hub의 경우 **서비스 연결** 사용 권한이 있는 키를 제공해야 합니다. 둘 다 **서비스 연결** 권한이 있으므로 **iothubowner** 또는 **서비스** 정책 중 하나를 선택 합니다.

   [![IoT Hub 서비스 연결 사용 권한](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Azure Event Hubs의 이벤트 허브에서는 **수신** 권한이 있는 키를 제공해야 합니다. **read** 또는 **manage** 정책에는 **수신** 권한이 있으므로 둘 다 사용할 수 있습니다.

   [이벤트 허브 수신 사용 권한 ![](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>원인 C: 제공 된 소비자 그룹이 Time Series Insights 전용이 아닙니다.

IoT Hub 또는 이벤트 허브를 등록할 경우 데이터를 읽는 데 사용할 소비자 그룹을 설정해야 합니다. 이 소비자 그룹은 *공유할 수 없습니다*. 소비자 그룹이 공유되면 기본 IoT Hub 또는 이벤트 허브는 자동으로 한 명의 읽기 권한자와의 연결을 임의로 끊습니다. Time Series Insights에서 읽을 고유한 소비자 그룹을 제공합니다.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>원인 D: 환경이 프로 비전 된 것입니다.

환경 및 해당 데이터를 처음 만든 후 몇 분 내에 데이터를 Time Series Insights 탐색기에 표시 합니다.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>문제: 일부 데이터가 표시 되지만 데이터가 누락 되었습니다.

데이터가 부분적으로만 표시되고 지연되는 것처럼 보이면 여러 가지 가능성을 고려해야 합니다.

### <a name="cause-a-your-environment-is-being-throttled"></a>원인 A: 사용자 환경을 제한 하 고 있습니다.

데이터를 포함 하는 이벤트 원본을 만든 후 환경이 프로 비전 되는 경우 [제한이](time-series-insights-environment-mitigate-latency.md) 일반적인 문제입니다. Azure IoT Hub 및 Azure Event Hubs는 최대 7일 동안의 데이터를 저장합니다. Time Series Insights는 항상 이벤트 원본에서 가장 오래된 이벤트부터 시작합니다(선입선출 또는 *FIFO*).

예를 들어, S1, 단일 단위 Time Series Insights 환경에 연결할 때 이벤트 원본에 5백만 개의 이벤트가 있는 경우 Time Series Insights는 하루에 약 1백만 개의 이벤트를 읽습니다. Time Series Insights가 5일의 대기 시간을 겪게 되는 것처럼 보일 수 있습니다. 그렇지만 실제로는 작업 환경이 제한되고 있는 것입니다.

이벤트 원본에 이전 이벤트가 있는 경우 다음 두 가지 방법 중 하나로 제한에 접근할 수 있습니다.

- 이벤트 원본의 보존 제한을 변경하여 Time Series Insights에 표시하지 않으려는 이전 이벤트 제거
- 더 큰 크기의 환경(단위 수)을 프로비전하여 이전 이벤트의 처리량 늘리기. 위 예제를 사용하는 경우 동일한 S1 환경을 하루에 5개 단위로 늘리면 해당 환경은 이제 하루 만에 따라잡을 수 있습니다. 안정 상태의 이벤트 생성이 하루에 백만 개 미만의 이벤트인 경우 처리량을 따라잡은 후에는 이벤트 용량을 다시 1개 단위로 줄일 수 있습니다.

제한적 한도는 환경의 SKU 유형 및 용량을 기준으로 적용됩니다. 환경의 모든 이벤트 원본은 이 용량을 공유합니다. IoT hub 또는 이벤트 허브에 대 한 이벤트 원본이 적용 된 한도를 초과 하 여 데이터를 푸시하는 경우 제한 및 지연 시간이 발생 합니다.

다음 그림에는 SKU S1 및 용량 3을 사용하는 Time Series Insights 환경이 나와 있습니다. 하루에 3백만 개의 이벤트를 수신할 수 있습니다.

[![환경 SKU 현재 용량](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

예를 들어, 환경이 이벤트 허브에서 메시지를 수집 하는 경우를 가정 합니다. 일일 수신 속도는 67,000개 이하 메시지입니다. 이 속도를 1분마다 약 46개 메시지로 변환합니다. 

* 각 이벤트 허브 메시지가 단일 Time Series Insights 이벤트로 결합되는 경우 제한이 나타나지 않습니다. 
* 각 이벤트 허브 메시지가 100 Time Series Insights 이벤트로 결합되는 경우에는 1분마다 4,600개의 이벤트가 수집되어야 합니다. 

용량이 3인 S1 SKU 환경은 1분마다 2,100개 이벤트만 수신할 수 있습니다(일별 1백만개 이벤트 = 3 단위에서 분당 700개 이벤트 = 분당 2,100개 이벤트). 

평면화 논리 작동 방식에 대 한 개략적인 이해를 위해 [지원 되는 JSON 셰이프](./how-to-shape-query-json.md)를 참조 하세요.

#### <a name="recommended-resolutions-for-excessive-throttling"></a>과도한 제한에 대한 권장 해결 방법

지연을 해결하려면 사용자 환경의 SKU 용량을 늘립니다. 자세한 내용은 [Time Series Insights 환경 크기 조정](time-series-insights-how-to-scale-your-environment.md)을 참조 하세요.

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>원인 B: 기록 데이터의 초기 수집이 느리게 수신 됩니다.

기존 이벤트 원본에 연결하는 경우 이미 IoT Hub 또는 이벤트 허브에 데이터가 있을 수 있습니다. 환경은 이벤트 원본 메시지 보존 기간의 시작 부분에서 데이터 풀링을 시작합니다. 이 기본 처리는 재정의할 수 없습니다. 제한을 적용할 수 있습니다. 기록 데이터를 수집하면서 지연을 따라잡기 위해 얼마 동안 제한이 적용될 수 있습니다.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>대용량 초기 수집에 대한 권장 해결 방법

지연을 해결하려면

1. SKU 용량을 최대 허용 값(이 경우 10)으로 늘립니다. 용량을 늘린 후에는 수신 프로세스가 훨씬 더 빠르게 지연을 따라잡기 시작합니다. 증가한 용량에 대해 요금이 부과됩니다. 지연을 얼마나 빠르게 따라잡는지를 시각화하려면 [Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 가용성 차트를 확인할 수 있습니다.

2. 지연을 따라잡으면 SKU 용량을 다시 정상 수신 속도로 줄입니다.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>문제: 내 이벤트 원본의 타임 스탬프 속성 이름 설정이 작동 하지 않습니다.

타임스탬프 속성 이름 및 값이 다음 규칙을 준수하는지 확인합니다.

* 타임스탬프 속성 이름은 대/소문자를 구분합니다.
* JSON 문자열처럼 이벤트 원본에서 가져오는 타임스탬프 속성 값은 _yyyy-MM-ddTHH:mm:ss.FFFFFFFK_ 형식이어야 합니다. 예를 들면 **2008-04-12T12:53Z**와 같습니다.

타임스탬프 속성 이름이 캡처되고 제대로 작동하는지 확인하는 가장 쉬운 방법은 Time Series Insights 탐색기를 사용하는 것입니다. Time Series Insights 탐색기 내에서 차트를 사용하고, 타임스탬프 속성 이름을 제공하기 전에 경과될 기간을 선택합니다. 선택 영역을 마우스 오른쪽 단추로 클릭하고 **이벤트 탐색** 옵션을 선택합니다.

첫 번째 열 머리글에는 타임스탬프 속성 이름에 있어야 합니다. **타임 스탬프**옆에 **($ts)** 가 표시 됩니다.

다음 값은 표시 되지 않습니다.

- *(abc)* : Time Series Insights에서 데이터 값을 문자열로 읽도록 지정 합니다.
- *Calendar icon*: Time Series Insights에서 데이터 값을 *datetime*으로 읽도록 지정 합니다.
- *#* : Time Series Insights 데이터 값을 정수로 읽도록 지정 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Time Series Insights에서 대기 시간을 완화 하는 방법](time-series-insights-environment-mitigate-latency.md)에 대해 알아봅니다.

- [Time Series Insights 환경의 크기를 조정 하는 방법을](time-series-insights-how-to-scale-your-environment.md)알아봅니다.