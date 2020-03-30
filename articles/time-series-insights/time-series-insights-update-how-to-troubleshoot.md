---
title: 미리 보기 환경 진단 및 문제 해결 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: Azure Time Series 인사이트 미리 보기 환경을 진단하고 해결하는 방법을 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 667dee6365f38ae058e91c61c24838d8912df26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152661"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>미리 보기 환경 진단 및 문제 해결

이 문서에서는 Azure Time Series Insights 미리 보기 환경을 사용할 때 발생할 수 있는 몇 가지 일반적인 문제를 요약해서 보여 줍니다. 또한 이 문서에서는 각 문제의 잠재적 원인 및 해결 방법에 대해 설명합니다.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>문제: 미리 보기 탐색기에서 내 환경을 찾을 수 없습니다.

이 문제는 Time Series Insights 환경에 액세스할 수 있는 권한이 없는 경우에 발생할 수 있습니다. 사용자가 Time Series Insights 환경을 보려면 읽기 권한자 수준의 액세스 역할이 필요합니다. 현재 액세스 수준을 확인하고 추가 액세스 권한을 부여하려면 [Azure 포털의](https://portal.azure.com/)시계열 인사이트 리소스의 **데이터 액세스 정책** 섹션으로 이동하십시오.

  [![데이터 액세스 정책을 확인합니다.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>문제: 미리 보기 탐색기에서 데이터가 보이지 않습니다.

[Azure Time Series Insights 미리 보기 탐색기에서](https://insights.timeseries.azure.com/preview)데이터가 나타나지 않는 몇 가지 일반적인 이유가 있습니다.

- 이벤트 원본이 데이터를 수신하지 않을 수 있습니다.

    이벤트 허브 또는 IoT 허브인 이벤트 원본이 태그 또는 인스턴스에서 데이터를 받고 있는지 확인합니다. 확인하려면 Azure Portal에서 리소스 개요 페이지로 이동하세요.

    [![대시보드 메트릭 개요를 검토합니다.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- 이벤트 원본 데이터가 JSON 형식이 아닙니다.

    Time Series Insights는 JSON 데이터만 지원합니다. JSON 샘플의 경우 [지원되는 JSON 셰이프를](./how-to-shape-query-json.md)참조하십시오.

- 이벤트 원본 키에 필요한 사용 권한이 없습니다.

  * IoT 허브의 경우 **서비스 연결** 권한이 있는 키를 제공해야 합니다.

    [![IoT 허브 사용 권한을 확인합니다.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * 그들은 **서비스 연결** 권한이 있기 때문에 정책 **iothub소유자와** **서비스** 작업 모두.

  * 이벤트 허브의 경우 **수신** 사용 권한이 있는 키를 제공해야 합니다.
  
    [![이벤트 허브 권한을 검토합니다.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * **읽기** 및 **관리** 정책은 모두 **Listen** 권한이 있으므로 작동합니다.

- 제공된 소비자 그룹이 Time Series Insights에 배타적으로 적용되지 않습니다.

    IoT 허브 또는 이벤트 허브 등록 중에 데이터를 읽는 데 사용되는 소비자 그룹을 지정합니다. 이 소비자 그룹은 환경별 고유해야 합니다. 소비자 그룹이 공유되면 기본 이벤트 허브는 자동으로 읽기 권한자 중 하나의 연결을 임의로 끊습니다. Time Series Insights에서 읽을 고유한 소비자 그룹을 제공합니다.

- 프로비전 시 지정된 시계열 ID 속성이 잘못되었거나 누락되었거나 Null입니다.

    이 문제는 환경 프로비전 시 시계열 ID 속성이 잘못 구성된 경우에 발생할 수 있습니다. 자세한 내용은 [타임시리즈 ID 를 선택하는 모범 사례를](./time-series-insights-update-how-to-id.md)참조하십시오. 지금은 다른 시계열 ID를 사용하도록 기존 Time Series Insights 환경을 업데이트할 수 없습니다.

## <a name="problem-some-data-shows-but-some-is-missing"></a>문제: 일부 데이터가 표시되지만 일부 데이터가 누락되었습니다.

시계열 ID 없이 데이터를 전송했을 수 있습니다.

- 이 문제는 페이로드에 시계열 ID 필드가 없는 이벤트를 전송할 때 발생할 수 있습니다. 자세한 내용은 [지원되는 JSON 셰이프를](./how-to-shape-query-json.md)참조하십시오.
- 이 문제는 사용자 환경이 제한적이기 때문에 발생할 수 있습니다.

    > [!NOTE]
    > 지금은 Time Series Insights에서 최대 6Mbps의 수집 속도를 지원합니다.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>문제: 데이터가 표시되었지만 이제 수집이 중지되었습니다.

- 이벤트 소스 키가 다시 생성되었을 수 있으며 미리 보기 환경에 새 이벤트 원본 키가 필요합니다.

이 문제는 이벤트 소스를 만들 때 제공된 키가 더 이상 유효하지 않은 경우에 발생합니다. 허브에 원격 분석이 표시되지만 수신 된 메시지는 타임 시리즈 인사이트에서 표시되지 않습니다. 키가 재생성되었는지 확실하지 않은 경우 이벤트 허브의 활동 로그를 검색하여 "네임스페이스 권한 부여 규칙 만들기 또는 업데이트"를 검색하거나 IoT 허브에 대해 "IotHub 리소스 만들기 또는 업데이트"를 검색할 수 있습니다. 

새 키로 타임시리즈 인사이트 미리 보기 환경을 업데이트하려면 Azure Portal에서 허브 리소스를 열고 새 키를 복사합니다. TSI 리소스로 이동하여 이벤트 소스를 클릭합니다. 

   [![키를 업데이트합니다.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

섭취가 중지된 이벤트 소스를 선택하고 새 키에 붙여넣은 다음 저장을 클릭합니다.

   [![키를 업데이트합니다.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>문제: 내 이벤트 소스의 타임스탬프 속성 이름이 작동하지 않습니다.

이름 및 값이 다음 규칙을 준수하는지 확인합니다.

* 타임스탬프 속성 이름은 대/소문자를 구분합니다.
* JSON 문자열로 이벤트 소스에서 오는 Timestamp 속성 값에는 형식이 `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`있습니다. 이러한 문자열의 예는 `“2008-04-12T12:53Z”`입니다.

타임스탬프 속성 이름이 캡처되고 제대로 작동하는지 확인하는 가장 쉬운 방법은 Time Series Insights 미리 보기 탐색기를 사용하는 것입니다. Time Series Insights 미리 보기 탐색기 내에서 타임스탬프 속성 이름을 제공한 후 차트를 사용하여 기간을 선택합니다. 선택 영역을 마우스 오른쪽 단추로 클릭하고 **이벤트 탐색** 옵션을 선택합니다. 첫 번째 열 머리글은 타임스탬프 속성 이름입니다. `Timestamp` 단어 옆에 다음 대신 `($ts)`가 있어야 합니다.

* `(abc)`는 Time Series Insights가 데이터 값을 문자열로 읽고 있음을 나타냅니다.
* **[타임시리즈** 인사이트]가 데이터 값을 날짜 시간으로 읽는다는 것을 나타내는 캘린더 아이콘입니다.
* `#`은 Time Series Insights가 데이터 값을 정수로 읽고 있음을 나타냅니다.

타임스탬프 속성이 명시적으로 지정되지 않은 경우 이벤트의 IoT 허브 또는 이벤트 허브 큐에 넣은 시간이 기본 타임스탬프로 사용됩니다.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>문제: 탐색기에서 웜 스토어의 데이터를 볼 수 없습니다.

- 최근에 웜 스토어를 프로비전했을 수 있으며 데이터가 계속 유입되고 있습니다.
- 웜 스토어를 삭제했을 수 있으며, 이 경우 데이터가 손실되었을 수 있습니다.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>문제: 내 타임시리즈 모델을 보거나 편집할 수 없습니다.

- Time Series Insights S1 또는 S2 환경에 액세스 중일 수 있습니다.

   타임시리즈 모델은 종량제 환경에서만 지원됩니다. 타임시리즈 인사이트 미리 보기 탐색기에서 S1 또는 S2 환경에 액세스하는 방법에 대한 자세한 내용은 [탐색기의 데이터 시각화를](./time-series-insights-update-explorer.md)참조하십시오.

   [![환경에 이벤트가 없습니다.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- 모델을 보고 편집할 권한이 없을 수 있습니다.

   사용자가 시계열 모델을 편집하고 보려면 기여자 수준 액세스 권한이 필요합니다. 현재 액세스 수준을 확인하고 추가 액세스 권한을 부여하려면 Azure 포털의 시계열 인사이트 리소스의 **데이터 액세스 정책** 섹션으로 이동하십시오.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>문제: 미리 보기 탐색기의 모든 인스턴스에 부모가 없습니다.

이 문제는 사용자 환경에 시계열 모델 계층 구조가 정의되지 않은 경우에 발생할 수 있습니다. 자세한 내용은 [시계열 모델 작업을](./time-series-insights-update-how-to-tsm.md)참조하십시오.

  [![부모가 없는 인스턴스에는 경고가 표시됩니다.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [시계열 모델 작업](./time-series-insights-update-how-to-tsm.md)을 읽습니다.

- [지원되는 JSON 셰이프에](./how-to-shape-query-json.md)대해 자세히 알아봅니다.

- Azure Time Series 인사이트 미리 보기에서 [계획 및 제한을](./time-series-insights-update-plan.md) 검토합니다.
