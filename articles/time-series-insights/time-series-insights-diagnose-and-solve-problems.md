---
title: 문제 진단, 문제 해결 및 해결-Azure Time Series Insights
description: 이 문서에서는 Azure Time Series Insights 환경의 일반적인 문제를 진단 하 고 해결 하 고 해결 하는 방법을 설명 합니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 231f2e4df1445c60378ac06aab0d0e56f410c1c8
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530139"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>Azure Time Series Insights Gen1 환경에서 문제 진단 및 해결

> [!CAUTION]
> Gen1 문서입니다.

이 문서에서는 Azure Time Series Insights 환경에서 발생할 수 있는 문제를 설명 합니다. 이 문서에서는 잠재적인 원인 및 해결 방법을 제안합니다.

## <a name="video"></a>동영상

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>일반적인 Azure Time Series Insights 과제 및 완화 방법에 대 한 자세한 정보</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>문제: 데이터가 표시 되지 않습니다.

[Azure Time Series Insights 탐색기](https://insights.timeseries.azure.com)에 데이터가 나타나지 않는 경우 다음과 같은 일반적인 원인을 고려 하십시오.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>원인 A: 이벤트 원본 데이터가 JSON 형식이 아닙니다.

Azure Time Series Insights는 JSON 데이터만 지원합니다. JSON 샘플의 경우 [지원되는 JSON 셰이프](./how-to-shape-query-json.md)를 참조하세요.

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>원인 B: 이벤트 원본 키에 필요한 사용 권한이 없습니다.

* Azure IoT Hub에서 IoT hub의 경우 서비스 연결 권한이 있는 키를 제공 해야 합니다. **Iothubowner** 또는 **서비스** 정책 중 하나를 선택 합니다. 둘 다 서비스 연결 권한이 있습니다.

   [![IoT Hub 서비스 연결 권한](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Azure Event Hubs에서 이벤트 허브의 경우 수신 대기 권한이 있는 키를 제공 해야 합니다. **읽기** 및 **관리** 정책은 모두 수신 사용 권한이 있기 때문에 작동 합니다.

   [![이벤트 허브 수신 권한](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>원인 C: 제공 된 소비자 그룹이 Azure Time Series Insights 전용이 아닙니다.

IoT Hub 또는 이벤트 허브를 등록할 경우 데이터를 읽는 데 사용할 소비자 그룹을 설정해야 합니다. 이 소비자 그룹은 *공유할 수 없습니다*. 소비자 그룹이 공유되면 기본 IoT Hub 또는 이벤트 허브는 자동으로 한 명의 읽기 권한자와의 연결을 임의로 끊습니다. 읽을 Azure Time Series Insights의 고유한 소비자 그룹을 제공 합니다.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>원인 D: 환경이 프로 비전 된 것입니다.

환경 및 해당 데이터를 처음 만든 후 몇 분 내에 데이터를 Azure Time Series Insights 탐색기에 표시 합니다.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>문제: 일부 데이터가 표시 되지만 데이터가 누락 되었습니다.

데이터가 부분적 으로만 표시 되 고 데이터가 지연 된 것 같으면 이러한 가능한 문제를 고려해 야 합니다.

### <a name="cause-a-your-environment-is-being-throttled"></a>원인 A: 사용자 환경을 제한 하 고 있습니다.

데이터를 포함 하는 이벤트 원본을 만든 후 환경이 프로 비전 되는 경우 [제한이](time-series-insights-environment-mitigate-latency.md) 일반적인 문제입니다. Azure IoT Hub 및 Azure Event Hubs는 최대 7일 동안의 데이터를 저장합니다. Azure Time Series Insights 항상 이벤트 원본에서 가장 오래 된 이벤트 (선입 선출 (선입 선출) 또는 *FIFO*)로 시작 합니다.

예를 들어 이벤트 원본에 500만 이벤트가 있는 경우 S1, 단일 단위 Azure Time Series Insights 환경에 연결 하면 하루에 약 100만 이벤트를 Azure Time Series Insights 읽습니다. 5 일의 대기 시간이 발생 Azure Time Series Insights 것 처럼 보일 수 있습니다. 그러나 환경을 제한 하 고 있습니다.

이벤트 원본에 이전 이벤트가 있는 경우 다음 두 가지 방법 중 하나로 제한에 접근할 수 있습니다.

* Azure Time Series Insights에 표시 하지 않을 이전 이벤트를 제거 하는 데 도움이 되도록 이벤트 원본의 보존 제한을 변경 합니다.
* 더 큰 크기의 환경(단위 수)을 프로비전하여 이전 이벤트의 처리량 늘리기. 앞의 예제에서 1 일 동안 동일한 S1 환경을 5 개 단위로 늘리면 환경은 하루 내에 처리 해야 합니다. 안정적인 상태 이벤트 프로덕션이 100만 이하인 이벤트 일 경우 Azure Time Series Insights를 처리 한 후 이벤트 용량을 한 단위로 줄일 수 있습니다.

적용 되는 제한 제한은 환경의 SKU 유형 및 용량을 기반으로 합니다. 환경의 모든 이벤트 원본은 이 용량을 공유합니다. IoT hub 또는 이벤트 허브에 대 한 이벤트 원본이 적용 된 한도를 초과 하 여 데이터를 푸시하는 경우 제한 및 지연 시간이 발생 합니다.

다음 그림은 SKU가 S1이 고 용량이 3 인 Azure Time Series Insights 환경을 보여 줍니다. 하루에 3백만 개의 이벤트를 수신할 수 있습니다.

[![환경 용량](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

이벤트 허브에서 메시지를 수집 하는 환경을 상상해 보세요. 약 67000 메시지의 일별 수신 요금을 갖습니다. 이 속도를 1분마다 약 46개 메시지로 변환합니다.

* 각 이벤트 허브 메시지가 단일 Azure Time Series Insights 이벤트로 평면화 되는 경우 제한이 발생 하지 않습니다.
* 각 이벤트 허브 메시지가 100 Azure Time Series Insights 이벤트로 평면화 되는 경우 4600 이벤트는 1 분 마다 수집 됩니다.

용량이 3인 S1 SKU 환경은 1분마다 2,100개 이벤트만 수신할 수 있습니다(일별 1백만개 이벤트 = 3 단위에서 분당 700개 이벤트 = 분당 2,100개 이벤트).

평면화 논리 작동 방식에 대 한 개략적인 이해를 얻으려면 [지원 되는 JSON 셰이프](./how-to-shape-query-json.md)를 참조 하세요.

#### <a name="recommended-resolutions-for-excessive-throttling"></a>과도한 제한에 대한 권장 해결 방법

지연을 해결하려면 사용자 환경의 SKU 용량을 늘립니다. 자세한 내용은 [Azure Time Series Insights 환경 크기 조정](time-series-insights-how-to-scale-your-environment.md)을 참조 하세요.

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>원인 B: 기록 데이터의 초기 수집이 느리게 수신 됩니다.

기존 이벤트 원본에 연결하는 경우 이미 IoT Hub 또는 이벤트 허브에 데이터가 있을 수 있습니다. 환경은 이벤트 원본 메시지 보존 기간의 시작 부분에서 데이터 풀링을 시작합니다. 이 기본 처리는 재정의할 수 없습니다. 제한을 적용할 수 있습니다. 기록 데이터를 수집하면서 지연을 따라잡기 위해 얼마 동안 제한이 적용될 수 있습니다.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>대용량 초기 수집에 대한 권장 해결 방법

지연을 해결하려면

1. SKU 용량을 최대 허용 값(이 경우 10)으로 늘립니다. 용량을 늘린 후에는 수신 프로세스가 훨씬 더 빠르게 지연을 따라잡기 시작합니다. 증가 된 용량에 대 한 요금이 청구 됩니다. 얼마나 신속 하 게 탐색 하 고 있는지를 시각화 하기 위해 [Azure Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 가용성 차트를 볼 수 있습니다.

2. 지연을 따라잡으면 SKU 용량을 다시 정상 수신 속도로 줄입니다.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>문제: 데이터가 이전에 표시 되었지만 더 이상 표시 되지 않습니다.

Azure Time Series Insights 더 이상 데이터를 수집 않지만 이벤트가 Iot Hub 또는 이벤트 허브로 계속 스트리밍되는 경우이 잠재적인 원인을 고려 하세요.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>원인 A: 허브 액세스 키가 다시 생성 되었으므로 환경을 업데이트 해야 합니다.

이 문제는 이벤트 원본을 만들 때 제공 된 키가 더 이상 유효 하지 않을 때 발생 합니다. 허브에 원격 분석이 표시 되지만 수신 수신 메시지는 Azure Time Series Insights에 표시 되지 않습니다. 키가 다시 생성 되었는지 확실 하지 않으면 이벤트 허브의 활동 로그에서 "네임 스페이스 권한 부여 규칙 만들기 또는 업데이트"를 검색할 수 있습니다. IoT hub의 경우 "IotHub 리소스 만들기 또는 업데이트"를 검색 합니다.

새 키를 사용 하 여 Azure Time Series Insights 환경을 업데이트 하려면 Azure Portal에서 허브 리소스를 열고 새 키를 복사 합니다. Azure Time Series Insights 리소스로 이동 하 여 **이벤트 원본**을 선택 합니다.

   [![이벤트 원본 선택](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

수집을 중지할 이벤트 원본 또는 소스를 선택 하 고 새 키를 붙여넣은 다음 **저장**을 선택 합니다.

   [![새 키에 붙여넣기](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>문제: 이벤트 원본의 타임 스탬프 속성 이름 설정이 작동 하지 않습니다.

이벤트 원본에서 JSON 문자열로 제공 되는 타임 스탬프 속성 값이 _yyyy-mm-yyyy-mm-ddthh: MM: ss 형식 인지 확인 합니다. FFFFFFFK_입니다. 예를 들면 **2008-04-12T12:53Z**가 있습니다.

타임 스탬프 속성 이름은 대/소문자를 구분 한다는 점에 유의 하세요.

타임 스탬프 속성 이름이 캡처되고 제대로 작동 하는지 확인 하는 가장 쉬운 방법은 Azure Time Series Insights 탐색기를 사용 하는 것입니다. Azure Time Series Insights 탐색기에서 차트를 사용 하 여 타임 스탬프 속성 이름을 입력 한 후의 기간을 선택 합니다. 선택 영역을 마우스 오른쪽 단추로 클릭 한 다음 **이벤트 탐색**을 선택 합니다.

첫 번째 열 머리글에는 타임스탬프 속성 이름에 있어야 합니다. **타임 스탬프**옆에 **($ts)** 가 표시 됩니다.

다음 값은 표시 되지 않습니다.

* *(abc)*: Azure Time Series Insights에서 데이터 값을 문자열로 읽도록 지정 합니다.
* *Calendar icon*: Azure Time Series Insights에서 데이터 값을 datetime 값으로 읽도록 지정 합니다.
* *#*: Azure Time Series Insights에서 데이터 값을 정수로 읽도록 지정 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Time Series Insights에서 대기 시간을 완화 하는 방법](time-series-insights-environment-mitigate-latency.md)에 대해 알아봅니다.

* [Azure Time Series Insights 환경의 크기를 조정 하는 방법을](time-series-insights-how-to-scale-your-environment.md)알아봅니다.
