---
title: Gen2 환경 진단 및 문제 해결 - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen2 환경을 진단하고 문제를 해결하는 방법을 알아봅니다.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 610c579929462f641371896085355987c4d67dae
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796258"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 환경 진단 및 문제 해결

이 문서에서는 Azure Time Series Insights Gen2 환경을 사용할 때 발생할 수 있는 몇 가지 일반적인 문제를 요약해서 보여 줍니다. 또한 이 문서에서는 각 문제의 잠재적 원인 및 해결 방법에 대해 설명합니다.

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>문제: Gen2 탐색기에서 내 환경을 찾을 수 없음

이 문제는 Time Series Insights 환경에 액세스할 수 있는 권한이 없는 경우에 발생할 수 있습니다. 사용자가 Time Series Insights 환경을 보려면 읽기 권한자 수준의 액세스 역할이 필요합니다. 현재 액세스 수준을 확인하고 추가 액세스 권한을 부여하려면 **Azure Portal** 의 Time Series Insights 리소스에 대한 [데이터 액세스 정책](https://portal.azure.com/) 섹션을 방문하세요.

  [![데이터 액세스 정책을 확인합니다.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>문제: Gen2 탐색기에 데이터가 표시되지 않음

[Azure Time Series Insights Gen2 탐색기](https://insights.timeseries.azure.com/preview)에서 데이터가 표시되지 않을 때는 다음과 같은 몇 가지 일반적인 원인이 있습니다.

- 이벤트 원본이 데이터를 수신하지 않을 수 있습니다.

    이벤트 허브 또는 IoT 허브인 이벤트 원본이 태그 또는 인스턴스에서 데이터를 받고 있는지 확인합니다. 확인하려면 Azure Portal에서 리소스 개요 페이지로 이동하세요.

    [![대시보드 메트릭 개요를 검토합니다.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- 이벤트 원본 데이터가 JSON 형식이 아닙니다.

    Time Series Insights는 JSON 데이터만 지원합니다. JSON 샘플은 [지원되는 JSON 모양](./concepts-json-flattening-escaping-rules.md)을 참조하세요.

- 이벤트 원본 키에 필요한 사용 권한이 없습니다.

  - IoT Hub의 경우 **서비스 연결** 사용 권한이 있는 키를 제공해야 합니다.

    [![IoT Hub 권한을 확인합니다.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    - **iothubowner** 및 **서비스** 정책은 모두 **서비스 연결** 권한이 있으므로 작동합니다.

  - 이벤트 허브의 경우 **수신** 사용 권한이 있는 키를 제공해야 합니다.

    [![이벤트 허브 권한을 검토합니다.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    - **읽기** 및 **관리** 정책은 모두 **수신** 권한이 있기 때문에 작동합니다.

- 제공된 소비자 그룹이 Time Series Insights에 배타적으로 적용되지 않습니다.

    IoT 허브 또는 이벤트 허브 등록 중에 데이터를 읽는 데 사용되는 소비자 그룹을 지정합니다. 이 소비자 그룹은 환경별로 고유해야 합니다. 소비자 그룹이 공유되면 기본 이벤트 허브는 자동으로 읽기 권한자 중 하나의 연결을 임의로 끊습니다. Time Series Insights에서 읽을 고유한 소비자 그룹을 제공합니다.

- 프로비전 시 지정된 시계열 ID 속성이 잘못되었거나 누락되었거나 Null입니다.

    이 문제는 환경 프로비전 시 시계열 ID 속성이 잘못 구성된 경우에 발생할 수 있습니다. 자세한 내용은 [시계열 ID 선택 모범 사례](./how-to-select-tsid.md)를 참조하세요. 지금은 다른 시계열 ID를 사용하도록 기존 Time Series Insights 환경을 업데이트할 수 없습니다.

## <a name="problem-some-data-shows-but-some-is-missing"></a>문제: 일부 데이터가 표시되지만 일부는 누락됨

시계열 ID 없이 데이터를 전송했을 수 있습니다.

- 이 문제는 페이로드에 시계열 ID 필드가 없는 이벤트를 전송할 때 발생할 수 있습니다. 자세한 내용은 [지원되는 JSON 모양](./concepts-json-flattening-escaping-rules.md)을 참조하세요.
- 이 문제는 사용자 환경이 제한적이기 때문에 발생할 수 있습니다.

    > [!NOTE]
    > 지금은 Time Series Insights에서 최대 1Mbps의 수집 속도를 지원합니다.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>문제: 데이터가 표시되었지만 이제 수집이 중지됨

- 이벤트 원본 키가 다시 생성되었을 수 있으며 Gen2 환경에 새 이벤트 원본 키가 필요합니다.

이 문제는 이벤트 원본을 만들 때 제공한 키가 더 이상 유효하지 않을 때 발생합니다. 허브에는 원격 분석이 표시되지만 Time Series Insights에는 수신 메시지가 표시되지 않습니다. 키가 다시 생성되었는지 여부를 확신할 수 없는 경우 Event Hubs의 활동 로그에서 "네임스페이스 권한 부여 규칙 만들기 또는 업데이트"를 검색하거나 IoT hub의 경우 "IotHub 리소스 만들기 또는 업데이트"를 검색할 수 있습니다.

새 키로 Time Series Insights Gen2 환경을 업데이트하려면 Azure Portal에서 허브 리소스를 열고 새 키를 복사합니다. TSI 리소스로 이동하여 이벤트 원본을 클릭합니다.

   [![이벤트 원본 메뉴 항목이 호출된 T S I 리소스를 보여 주는 스크린샷.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

수집이 중지된 이벤트 원본을 선택하고 새 키를 붙여넣고 저장을 클릭합니다.

   [![I o T 허브 정책 키가 입력된 T S I 리소스를 보여 주는 스크린샷.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>문제: 내 이벤트 원본의 타임스탬프 속성 이름이 작동하지 않음

이름 및 값이 다음 규칙을 준수하는지 확인합니다.

- 타임스탬프 속성 이름은 대/소문자를 구분합니다.
- JSON 문자열처럼 이벤트 원본에서 가져오는 타임스탬프 속성 값은 `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` 형식입니다. 이러한 문자열의 예는 `"2008-04-12T12:53Z"`입니다.

타임스탬프 속성 이름이 캡처되고 제대로 작동하는지 확인하는 가장 쉬운 방법은 Time Series Insights Gen2 탐색기를 사용하는 것입니다. Time Series Insights Gen2 탐색기 내에서 타임스탬프 속성 이름을 제공한 후 차트를 사용하여 기간을 선택합니다. 선택 영역을 마우스 오른쪽 단추로 클릭하고 **이벤트 탐색** 옵션을 선택합니다. 첫 번째 열 머리글은 타임스탬프 속성 이름입니다. `Timestamp` 단어 옆에 다음 대신 `($ts)`가 있어야 합니다.

- `(abc)`는 Time Series Insights가 데이터 값을 문자열로 읽고 있음을 나타냅니다.
- **달력** 아이콘은 Time Series Insights가 데이터 값을 날짜/시간으로 읽고 있음을 나타냅니다.
- `#`은 Time Series Insights가 데이터 값을 정수로 읽고 있음을 나타냅니다.

타임스탬프 속성이 명시적으로 지정되지 않은 경우 이벤트의 IoT 허브 또는 이벤트 허브 큐에 넣은 시간이 기본 타임스탬프로 사용됩니다.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>문제: 탐색기에서 내 웜 저장소의 데이터를 볼 수 없음

- 최근에 웜 저장소를 프로비저닝했을 수 있으며 데이터가 계속 유입되고 있습니다.
- 웜 저장소를 삭제했을 수 있으며 이 경우 데이터가 손실될 수 있습니다.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>문제: 시계열 모델을 보거나 편집할 수 없음

- Time Series Insights S1 또는 S2 환경에 액세스 중일 수 있습니다.

   시계열 모델은 종량제 환경에서만 지원됩니다. Time Series Insights Gen2 탐색기에서 S1 또는 S2 환경에 액세스하는 방법에 대한 자세한 내용은 [탐색기에서 데이터 시각화](./concepts-ux-panels.md)를 참조하세요.

   [![환경에 이벤트가 없습니다.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- 모델을 보고 편집할 권한이 없을 수 있습니다.

   사용자가 시계열 모델을 편집하고 보려면 기여자 수준 액세스 권한이 필요합니다. 현재 액세스 수준을 확인하고 추가 액세스 권한을 부여하려면 Azure Portal에서 Time Series Insights 리소스에 대한 **데이터 액세스 정책** 섹션을 방문하세요.

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>문제: Gen2 탐색기의 내 모든 인스턴스에 부모가 없음

이 문제는 사용자 환경에 시계열 모델 계층 구조가 정의되지 않은 경우에 발생할 수 있습니다. 자세한 내용은 [시계열 모델 작업](./time-series-insights-overview.md) 방법을 참조하세요.

  [![부모가 없는 인스턴스는 경고를 표시합니다.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [시계열 모델 작업](./time-series-insights-overview.md) 방법에 대해 읽어봅니다.

- [지원되는 JSON 모양](./concepts-json-flattening-escaping-rules.md)에 대해 알아봅니다.

- Azure Time Series Insights Gen2에서 [계획 및 제한](./how-to-plan-your-environment.md)을 검토합니다.