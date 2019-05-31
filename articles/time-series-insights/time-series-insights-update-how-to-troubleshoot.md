---
title: Azure Time Series Insights 미리 보기에 대한 진단 및 문제 해결 | Microsoft Docs
description: Azure Time Series Insights 미리 보기를 사용하여 문제를 진단하고 해결하는 방법을 이해합니다.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: d18f8f4808d5ca9dd7eca1561b1ee08c2073f5fa
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237551"
---
# <a name="diagnose-and-troubleshoot"></a>진단 및 문제 해결

이 문서에서는 Azure Time Series Insights 미리 보기 환경을 사용할 때 발생할 수 있는 몇 가지 일반적인 문제를 요약해서 보여 줍니다. 또한 이 문서에서는 각 문제의 잠재적 원인 및 해결 방법에 대해 설명합니다.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>문제: 내 환경 미리 보기 탐색기에서 찾을 수 없습니다.

이 문제는 Time Series Insights 환경에 액세스할 수 있는 권한이 없는 경우에 발생할 수 있습니다. 사용자가 Time Series Insights 환경을 보려면 읽기 권한자 수준의 액세스 역할이 필요합니다. 현재 액세스 수준을 확인하고 추가 액세스 권한을 부여하려면 [Azure 포털](https://portal.azure.com/)의 Time Series Insights 리소스에 대한 데이터 액세스 정책 섹션을 방문하세요.

  [![환경](media/v2-update-diagnose-and-troubleshoot/environment.png)](media/v2-update-diagnose-and-troubleshoot/environment.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>문제: 데이터가 없는 미리 보기 탐색기에 표시 됩니다.

[Azure Time Series Insights 미리 보기 탐색기](https://insights.timeseries.azure.com/preview)에 데이터가 표시되지 않을 경우 다음과 같은 몇 가지 일반적인 원인이 있습니다.

- 이벤트 원본이 데이터를 수신하지 않을 수 있습니다.

    이벤트 허브 또는 IoT 허브인 이벤트 원본이 태그 또는 인스턴스에서 데이터를 받고 있는지 확인합니다. 확인하려면 Azure Portal에서 리소스 개요 페이지로 이동하세요.

    [![Dashboard-insights](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png)](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png#lightbox)

- 이벤트 원본 데이터가 JSON 형식이 아닙니다.

    Time Series Insights는 JSON 데이터만 지원합니다. JSON 샘플의 경우 [지원되는 JSON 셰이프](./how-to-shape-query-json.md)를 참조하세요.

- 이벤트 원본 키에 필요한 사용 권한이 없습니다.

  * IoT Hub의 경우 **서비스 연결** 사용 권한이 있는 키를 제공해야 합니다.

    [![구성](media/v2-update-diagnose-and-troubleshoot/configuration.png)](media/v2-update-diagnose-and-troubleshoot/configuration.png#lightbox)

  * 위의 이미지에 표시된 것처럼 **iothubowner** 및 **서비스** 정책에는 **서비스 연결** 권한이 있으므로 둘 다 작동합니다.
  * 이벤트 허브의 경우 **수신** 사용 권한이 있는 키를 제공해야 합니다.
  
    [![사용 권한](media/v2-update-diagnose-and-troubleshoot/permissions.png)](media/v2-update-diagnose-and-troubleshoot/permissions.png#lightbox)

  * 위의 이미지에 표시된 것처럼 **읽기** 및 **관리** 정책에는 **수신 대기** 권한이 있으므로 둘 다 작동합니다.

- 제공된 소비자 그룹이 Time Series Insights에 배타적으로 적용되지 않습니다.

    IoT 허브 또는 이벤트 허브 등록 중에 데이터를 읽는 데 사용되는 소비자 그룹을 지정합니다. 해당 소비자 그룹을 공유하지 마세요. 소비자 그룹이 공유되면 기본 이벤트 허브는 자동으로 읽기 권한자 중 하나의 연결을 임의로 끊습니다. Time Series Insights에서 읽을 고유한 소비자 그룹을 제공합니다.

- 프로비전 시 지정된 시계열 ID 속성이 잘못되었거나 누락되었거나 Null입니다.

    이 문제는 환경 프로비전 시 시계열 ID 속성이 잘못 구성된 경우에 발생할 수 있습니다. 자세한 내용은 [시계열 ID 선택 모범 사례](./time-series-insights-update-how-to-id.md)를 참조하세요. 지금은 다른 시계열 ID를 사용하도록 기존 Time Series Insights 환경을 업데이트할 수 없습니다.

## <a name="problem-some-data-shows-but-some-is-missing"></a>문제: 일부 데이터가 표시 되지만 일부 누락 되었습니다.

시계열 ID 없이 데이터를 전송했을 수 있습니다.

- 이 문제는 페이로드에 시계열 ID 필드가 없는 이벤트를 전송할 때 발생할 수 있습니다. 자세한 내용은 [지원되는 JSON 셰이프](./how-to-shape-query-json.md)를 참조하세요.

- 이 문제는 사용자 환경이 제한적이기 때문에 발생할 수 있습니다.

    > [!NOTE]
    > 지금은 Time Series Insights에서 최대 6Mbps의 수집 속도를 지원합니다.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>문제: 내 이벤트 원본의 타임 스탬프 속성 이름 작동 하지 않습니다.

이름 및 값이 다음 규칙을 준수하는지 확인합니다.

* 타임스탬프 속성 이름은 대/소문자를 구분합니다.
* JSON 문자열처럼 이벤트 원본에서 가져오는 타임스탬프 속성 값은 `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` 형식입니다. 이러한 문자열의 예는 `“2008-04-12T12:53Z”`입니다.

타임스탬프 속성 이름이 캡처되고 제대로 작동하는지 확인하는 가장 쉬운 방법은 Time Series Insights 미리 보기 탐색기를 사용하는 것입니다. Time Series Insights 미리 보기 탐색기 내에서 타임스탬프 속성 이름을 제공한 후 차트를 사용하여 기간을 선택합니다. 선택 영역을 마우스 오른쪽 단추로 클릭하고 **이벤트 탐색** 옵션을 선택합니다. 첫 번째 열 머리글은 타임스탬프 속성 이름입니다. `Timestamp` 단어 옆에 다음 대신 `($ts)`가 있어야 합니다.

* `(abc)`는 Time Series Insights가 데이터 값을 문자열로 읽고 있음을 나타냅니다.
* 일정 아이콘은 Time Series Insights가 데이터 값을 날짜/시간으로 읽고 있음을 나타냅니다.
* `#`은 Time Series Insights가 데이터 값을 정수로 읽고 있음을 나타냅니다.

타임스탬프 속성이 명시적으로 지정되지 않은 경우 이벤트의 IoT 허브 또는 이벤트 허브 큐에 넣은 시간이 기본 타임스탬프로 사용됩니다.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>문제: 내 시계열 모델을 편집 하거나 볼 수 없습니다.

- Time Series Insights S1 또는 S2 환경에 액세스 중일 수 있습니다.

   시계열 모델은 PAYG 환경에서만 지원됩니다. Time Series Insights 미리 보기 탐색기에서 S1/S2 환경에 액세스하는 방법에 대한 자세한 내용은 [탐색기에서 데이터 시각화](./time-series-insights-update-explorer.md)를 참조하세요.

   [![Access](media/v2-update-diagnose-and-troubleshoot/access.png)](media/v2-update-diagnose-and-troubleshoot/access.png#lightbox)

- 모델을 보고 편집할 권한이 없을 수 있습니다.

   사용자가 시계열 모델을 편집하고 보려면 기여자 수준 액세스 권한이 필요합니다. 현재 액세스 수준을 확인하고 추가 액세스 권한을 부여하려면 Azure Portal에서 Time Series Insights 리소스에 대한 데이터 액세스 정책 섹션을 방문하세요.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>문제: 미리 보기 탐색기에서 내 모든 인스턴스는 부모 부족

이 문제는 사용자 환경에 시계열 모델 계층 구조가 정의되지 않은 경우에 발생할 수 있습니다. 자세한 내용은 [시계열 모델 작업](./time-series-insights-update-how-to-tsm.md)을 참조하세요.

  [![시계열 모델](media/v2-update-diagnose-and-troubleshoot/tsm.png)](media/v2-update-diagnose-and-troubleshoot/tsm.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [시계열 모델 작업](./time-series-insights-update-how-to-tsm.md)을 읽습니다.

- 에 대 한 자세한 [JSON 셰이프 지원](./how-to-shape-query-json.md)합니다.
