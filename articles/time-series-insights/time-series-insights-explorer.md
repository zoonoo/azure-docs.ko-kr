---
title: 탐색기 - Azure 타임시리즈 인사이트를 사용하여 데이터 탐색 | 마이크로 소프트 문서
description: Azure Time Series Insights 탐색기를 사용하여 IoT 데이터를 보는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: b564373780da5bba71bc46ddbac3dc69a0c94a4a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382773"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights 탐색기

이 문서에서는 Azure Time Series Insights [탐색기 웹 앱의](https://insights.timeseries.azure.com/)일반 가용성의 기능 및 옵션에 대해 설명합니다. Time Series Insights 탐색기는 서비스에서 제공하는 강력한 데이터 시각화 기능을 보여 주며 사용자 고유의 환경에서 액세스할 수 있습니다.

Azure Time Series Insights는 완전히 관리되는 분석, 스토리지 및 시각화 서비스이며 수십억 개의 IoT 이벤트를 동시에 간편하게 탐색 및 분석할 수 있습니다. 데이터에 대한 글로벌 보기를 제공하므로 사용자는 IoT 솔루션의 유효성을 빠르게 검사할 수 있으며, 숨겨진 동향을 찾고, 문제를 찾아내고, 거의 실시간으로 근본 원인 분석을 수행할 수 있도록 지원하는 기능을 통해, 큰 비용이 수반되는 중요 업무용 장치의 가동 중지 시간을 방지할 수 있습니다. Time Series Insights 탐색기는 현재 공개 미리 보기로 제공됩니다.

> [!TIP]
> 데모 환경을 안내하는 투어에 대한 자세한 내용은 [Azure Time Series Insights 퀵스타트](time-series-quickstart.md)를 참조하십시오.

## <a name="video"></a>비디오

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>타임시리즈 인사이트 탐색기를 사용하여 데이터 쿼리에 대해 알아봅니다. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>앞의 비디오 <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Azure IoT 솔루션 가속기를 사용하여 타임시리즈 인사이트를 시작하기" 재생합니다.</a>

## <a name="prerequisites"></a>사전 요구 사항

Time Series Insights 탐색기를 사용하려면 다음을 준비해야 합니다.

- Time Series Insights 환경을 만듭니다. 자세한 내용은 [[열계 정보]](./time-series-insights-get-started.md)
- 환경에서 계정에 [대한 액세스를 제공합니다.](time-series-insights-data-access.md)
- [IoT 허브](time-series-insights-how-to-add-an-event-source-iothub.md) 또는 [이벤트](time-series-insights-how-to-add-an-event-source-eventhub.md) 이벤트 이벤트 소스를 추가합니다.

## <a name="explore-and-query-data"></a>데이터 탐색 및 쿼리하기

이벤트 소스를 Time Series Insights 환경에 연결하면 몇 분 안에 시계열 데이터를 탐색하고 쿼리할 수 있습니다.

1. 시작하려면 웹 브라우저에서 [타임시리즈 인사이트 탐색기를](https://insights.timeseries.azure.com/) 엽니다. 창의 왼쪽에서 환경을 선택합니다. 액세스 가능한 모든 환경이 알파벳 순으로 표시됩니다.

1. 환경을 선택한 후 맨 위에 있는 **보낸 시간** 및 **보낸** 것 구성을 사용하거나 원하는 시간 동안 선택하고 드래그합니다. 오른쪽 상단 모서리에 있는 돋보기를 선택하거나 선택한 시간 범위를 마우스 오른쪽 단추로 클릭하고 **검색을**선택합니다.

1. 자동 **켜기** 버튼을 선택하여 매 분마다 자동으로 가용성을 새로 고칠 수도 있습니다. **자동 켜기** 단추는 기본 시각화의 내용이 아니라 가용성 차트에만 적용됩니다.

1. Azure 클라우드 아이콘은 Azure 포털의 환경으로 이동합니다.

   [![타임시리즈 인사이트 환경 선택](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. 그런 다음 선택한 기간 동안 모든 이벤트 수를 표시하는 차트가 표시됩니다. 여기서 몇 가지 옵션을 사용할 수 있습니다.

    - **용어 편집기 패널**: 용어 공간은 환경을 쿼리하는 곳입니다. 화면 왼쪽에 있습니다.
      - **측정**: 이 드롭다운 목록에는 모든 숫자**열(Doubles)이 표시됩니다.**
      - **분할 BY**: 이 드롭다운 목록에는 범주형 열(문자열)이**표시됩니다.**
      - 단계 보간을 활성화하고 최소 및 최대값을 표시하고 **MEASURE**옆의 제어판에서 y축을 조정할 수 있습니다. 표시된 데이터가 데이터의 개수, 평균 또는 합계인지 여부를 조정할 수도 있습니다.
      - 동일한 x축에서 볼 수 있도록 최대 5개의 용어를 추가할 수 있습니다. 새 용어를 추가하려면 **추가를** 선택하거나 **이 용어 복제** 단추를 사용하여 기존 용어의 복사본을 추가합니다.

        [![용어 선택, 필터링 및 쿼리 패널](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **술어**: 술어를 사용하여 다음 표에 나열된 진연산자 집합을 사용하여 이벤트를 빠르게 필터링합니다. 선택하거나 클릭하여 검색을 수행하는 경우 조건자는 해당 검색에 따라 자동으로 업데이트됩니다. 지원되는 연산자는 다음과 같습니다.

         |작업(Operation)  |지원되는 형식  |메모  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **더블**, **날짜 시간**, **시간 범위**       |         |
         |**=**, **!=**,**<>**     | **문자열,** **Bool**, **더블**, **날짜 시간,** **시간 범위,** **NULL**        |         |
         |**에서**     | **문자열,** **Bool**, **더블**, **날짜 시간,** **시간 범위,** **NULL**        |  모든 발산자는 동일한 형식이거나 **NULL** 상수여야 합니다.        |
         |**HAS**     | **String**        |  오른쪽에는 상수 문자열 리터럴만 허용됩니다. 빈 문자열과 **NULL은** 허용되지 않습니다.       |

      - **쿼리 예**

         [![GA 쿼리 예제](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. **간격 크기** 슬라이더 도구를 사용하여 동일한 시간 동안 간격을 확대 및 축소할 수 있습니다. 슬라이더는 시간의 큰 조각 사이의 이동을 보다 정밀하게 제어하여 밀리초만큼 작은 슬라이스까지 부드러운 추세를 표시하여 데이터의 세분화된 고해상도 컷을 표시하고 분석할 수 있습니다. 슬라이더의 기본 시작점은 해상도, 쿼리 속도 및 세분성의 균형을 맞추기 위해 선택 항목에서 데이터를 가장 최적의 뷰로 설정합니다.

1. **시간 브러시** 도구를 사용하면 한 시간 대에서 다른 시간으로 쉽게 탐색할 수 있습니다.

1. **저장** 아이콘을 선택하여 현재 쿼리를 저장하고 환경의 다른 사용자와 공유합니다. **열기** 아이콘을 선택하면 저장된 모든 쿼리와 액세스할 수 있는 환경에서 다른 사용자의 공유 쿼리를 검토할 수 있습니다.

   [![쿼리](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>데이터 시각화

1. **투시 뷰** 도구를 사용하여 최대 4개의 고유한 쿼리를 동시에 볼 수 있습니다. **원근 뷰** 버튼은 차트의 오른쪽 위 모서리에 있습니다.

   [![원근 창에 추가할 쿼리 선택](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. 차트를 보고 데이터를 시각적으로 탐색하고 **차트** 도구를 사용합니다.

    - 특정 시간 범위 또는 단일 데이터 계열을 **선택하거나** **클릭합니다.**
    - 시간 범위 선택 내에서 이벤트를 확대/축소하거나 탐색할 수 있습니다.
    - 하나의 데이터 계열에서 다른 열을 기준으로 계열을 분할하거나, 계열을 새 기간으로 추가하거나, 선택한 계열만 표시하거나, 선택한 계열을 제외하거나, 계열에 대해 ping을 보내거나 선택한 계열의 이벤트를 탐색할 수 있습니다.
    - 차트 왼쪽의 필터 영역에서 표시된 모든 데이터 계열을 검토하고 값 또는 이름으로 순서를 다시 정렬할 수 있습니다. 또한 모든 데이터 계열 또는 고정또는 고정되지 않은 계열도 볼 수 있습니다. 단일 데이터 계열을 선택하고 계열을 다른 열로 분할하거나, 계열을 새 용어로 추가하거나, 선택한 계열만 표시하거나, 선택한 계열을 제외하거나, 해당 계열을 고정하거나, 선택한 계열의 이벤트를 탐색할 수 있습니다.
    - 여러 용어를 동시에 볼 때 데이터 계열에 대한 추가 데이터를 스택, 스택 해제, 검토하고 모든 용어에서 동일한 y축을 사용할 수 있습니다. 차트의 오른쪽 위 모서리에 있는 단추를 사용합니다.

    [![차트 도구 오른쪽 상단 모서리 옵션 설정](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. **히트맵을** 사용하여 지정된 쿼리에서 고유하거나 비정상적인 데이터 계열을 빠르게 찾아냅니다. 하나의 검색 기간만 열 지도로 시각화할 수 있습니다.

    [![GA 탐색기 히트맵 차트](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. 선택하거나 마우스 오른쪽 단추로 클릭하여 이벤트를 탐색할 때 **이벤트** 패널을 사용할 수 있습니다. 여기에서 모든 원시 이벤트를 검토하고 이벤트를 JSON 또는 CSV 파일로 내보낼 수 있습니다. 타임 시리즈 인사이트는 모든 원시 데이터를 저장합니다.

    [![이벤트](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. 패턴을 탐색하여 패턴 및 열 통계를 노출한 후 **STATS** 탭을 선택합니다.

    - **패턴**: 이 기능은 선택한 데이터 영역에서 가장 통계적으로 유의한 패턴을 사전에 표시합니다. 수천 개의 이벤트를 보면서 가장 많은 시간과 에너지가 필요한 패턴을 파악할 필요가 없습니다. 타임시리즈 인사이트를 사용하면 통계적으로 유의한 패턴으로 직접 이동하여 분석을 계속할 수 있습니다. 이 기능은 과거 데이터에 대한 사후 평가 분석을 실시할 때도 유용합니다.
    - **열 통계**: 열 통계는 선택한 시간 범위에 걸쳐 선택한 데이터 계열의 각 열에서 데이터를 세분화하는 차트와 테이블을 제공합니다.

      [![통계 컬럼 차트 및 옵션](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

이제 Time Series Insights 탐색기 웹 앱에서 사용할 수 있는 주요 기능, 구성 설정 및 디스플레이 옵션에 대해 배웠습니다.

## <a name="next-steps"></a>다음 단계

- 타임시리즈 인사이트 환경에서 [문제를 진단하고 해결하는](time-series-insights-diagnose-and-solve-problems.md) 방법을 알아보십시오.

- 안내Azure [Time Series 인사이트 퀵스타트](time-series-quickstart.md) 투어를 이용하세요.
