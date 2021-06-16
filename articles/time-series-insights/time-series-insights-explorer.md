---
title: Azure Time Series Insights 탐색기로 데이터 탐색| Microsoft Docs
description: Azure Time Series Insights 탐색기를 사용하여 IoT 데이터를 확인하는 방법에 대해 알아보세요.
ms.service: time-series-insights
services: time-series-insights
author: narmeens
ms.author: narsam
manager: cnovak
ms.reviewer: orspodek
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: d4fae8dd1a737b84345513244949e23286bd7772
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063489"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights Gen1 탐색기

> [!CAUTION]
> 이는 Gen1 문서입니다.

이 문서는 Azure Time Series Insights Gen1 [ 탐색기 웹 앱 ](https://insights.timeseries.azure.com/)의 기능과 옵션에 대해 설명합니다. Azure Time Series Insights 탐색기는 서비스에서 제공하는 강력한 데이터 시각화 기능을 보여주며 사용자 환경에서 액세스할 수 있습니다.

Azure Time Series Insights는 완전히 관리되는 분석, 스토리지 및 시각화 서비스이며 수십억 개의 IoT 이벤트를 동시에 간편하게 탐색 및 분석할 수 있습니다. 데이터에 대한 글로벌 보기를 제공하므로 사용자는 IoT 솔루션의 유효성을 빠르게 검사할 수 있으며, 숨겨진 동향을 찾고, 문제를 찾아내고, 거의 실시간으로 근본 원인 분석을 수행할 수 있도록 지원하는 기능을 통해, 큰 비용이 수반되는 중요 업무용 장치의 가동 중지 시간을 방지할 수 있습니다.

> [!TIP]
> 데모 환경을 둘러보려면 [Azure Time Series Insights 빠른 시작](time-series-quickstart.md)을 참조하세요.

## <a name="video"></a>비디오

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Azure Time Series Insights 탐색기를 통해 데이터 쿼리 방법에 대해 알아봅니다. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>앞선 <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Azure IoT 솔루션 가속기를 통한 Azure Time Series Insights 시작" 비디오를 재생합니다.</a>

## <a name="prerequisites"></a>사전 요구 사항

Time Series Insights 탐색기를 사용하려면 먼저 다음을 수행해야 합니다

- Azure Time Series Insights 환경을 만듭니다. 자세한 내용은 [Azure Time Series Insights 시작 방법](./time-series-insights-get-started.md)을 참조 하시기 바랍니다.
- 환경에서 계정에 대한 액세스 제공
- [IoT hub](./how-to-ingest-data-iot-hub.md) 또는 [이벤트 허브](./how-to-ingest-data-event-hub.md) 이벤트 원본을 추가합니다.

## <a name="explore-and-query-data"></a>데이터 탐색 및 쿼리하기

이벤트 원본을 Azure Time Series Insights 환경에 연결하면 몇 분 안에 Time Series 데이터를 탐색하고 쿼리할 수 있습니다.

1. 시작하려면 웹 브라우저에서 [Azure Time Series Insights 탐색기](https://insights.timeseries.azure.com/)를 실행합니다. 창 왼쪽에서 환경을 선택합니다. 액세스 가능한 모든 환경이 알파벳 순으로 표시됩니다.

1. 환경을 선택한 다음 상단의 **FROM** 및 **TO** 구성을 사용하거나 원하는 TimeSpan을 선택하여 드래그합니다. 오른쪽 상단에 있는 모서리에서 돋보기를 선택하거나 선택한 TimeSpan을 마우스 오른쪽 단추로 클릭하고 **검색** 을 선택합니다.

1. **Auto On** 버튼을 선택하면 1분에 한 번씩 자동으로 가용 시간을 새로 고칠 수 있습니다. **Auto On** 버튼은 기본 시각화의 콘텐츠가 아닌 가용성 차트에만 적용됩니다.

1. Azure 클라우드 아이콘을 통해 Azure Portal 환경으로 이동할 수 있습니다.

   [![Azure Time Series Insights 환경 선택](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. 선택한 TimeSpan의 모든 이벤트 개수를 보여주는 차트가 표시됩니다. 여기서 몇 가지 옵션을 사용할 수 있습니다.

    - **기간 편집기 패널**: 기간 패널에서 환경을 쿼리할 수 있습니다. 화면 왼쪽에 표시되며, 다음과 같은 기능이 제공됩니다.
      - **측정값**: 이 드롭다운 목록에는 모든 숫자 열(**Doubles**)이 표시됩니다.
      - **분할**: 이 드롭다운 목록에는 범주 열(**Strings**)이 표시됩니다.
      - 단계 보간을 활성화하고 최솟값 및 최댓값을 표시하여 **측정값** 옆의 제어판에서 y축을 조정할 수 있습니다. 또한 표시된 데이터가 데이터의 개수, 평균 또는 합계인지를 조정할 수 있습니다.
      - 동일한 x축에 최대 5개의 항을 추가할 수 있습니다. **Add** 를 선택하여 새 항을 추가하거나 **Colon this term** 버튼을 사용하여 기존 항의 복사본을 추가합니다.

        [![항 선택, 필터링 및 쿼리 패널](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **조건자**: 다음 테이블에 나열된 피연산자 세트를 사용하여 조건자를 통해 이벤트를 빠르게 필터링할 수 있습니다 선택이나 클릭을 통해 검색을 수행하는 경우 해당 검색에 따라 조건자가 자동으로 업데이트됩니다. 지원되는 연산자는 다음과 같습니다.

         |작업  |지원되는 형식  |참고  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**, **DateTime**, **TimeSpan**       |         |
         |**=** , **!=** , **<>**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |         |
         |**IN**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |  모든 피연산자는 동일한 형식이거나 NULL 상수여야 합니다.        |
         |**HAS**     | **String**        |  우측 항에는 상수 문자열 리터럴만 사용할 수 있습니다. 빈 문자열과 **NULL** 은 허용되지 않습니다.       |

      - **쿼리 예**

         [![Gen1 쿼리 예시](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. **간격 크기** 슬라이더 도구를 사용하면 동일한 TimeSpan에서 간격을 확대하거나 축소할 수 있습니다. 슬라이더를 사용하면 부드러운 추세를 밀리초 단위로 보여 주는 큰 조각 간의 움직임을 보다 정확하게 제어할 수 있으므로 데이터의 세부적인 고해상도 조각을 표시하고 분석할 수 있습니다. 슬라이더의 기본 시작점은 해상도, 쿼리 속도 및 세분성의 균형을 위해 선택한 데이터를 최적으로 보여주도록 설정됩니다.

1. **Time brush** 도구를 통해 한 TimeSpan에서 다른 TimeSpan으로 쉽게 이동할 수 있습니다.

1. **Save** 아이콘을 선택하여 현재 쿼리를 저장하고 환경의 다른 사용자와 공유합니다. **Open** 을 선택하면 내가 저장한 쿼리와 액세스 권한이 있는 환경의 다른 사용자의 공유 쿼리를 볼 수 있습니다.

   [![쿼리](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>데이터 시각화

1. **원근감 뷰** 도구를 사용하면 최대 4개의 고유한 쿼리를 동시에 볼 수 있습니다. **원근감 뷰** 버튼은 차트의 오른쪽 상단에 있습니다.

   [![큐브 뷰 창에 추가할 쿼리를 선택합니다.](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. 차트에서 데이터를 시각적으로 탐색하고 **Chart** 도구를 사용합니다.

    - 특정 TimeSpan 또는 단일 데이터 계열을 **선택** 하거나 **클릭** 합니다.
    - 선택한 TimeSpan에서 이벤트를 확대하고 탐색할 수 있습니다.
    - 하나의 데이터 계열에서 다른 열을 기준으로 계열을 분할하거나, 계열을 새 기간으로 추가하거나, 선택한 계열만 표시하거나, 선택한 계열을 제외하거나, 계열에 대해 ping을 보내거나 선택한 계열의 이벤트를 탐색할 수 있습니다.
    - 차트 왼쪽의 필터 영역에서 표시된 모든 데이터 계열을 검토하고 값 또는 이름별로 순서를 변경할 수 있습니다. 또한 모든 데이터 계열이나 고정 또는 고정 해제된 계열을 볼 수 있습니다. 단일 데이터 계열을 선택하고 다른 열로 분할하거나, 해당 계열을 새 항으로 추가하거나, 해당 계열만 표시하거나, 해당 계열을 제외하거나, 해당 계열을 고정 하거나, 선택한 계열의 이벤트를 탐색할 수 있습니다.
    - 여러 항을 동시에 볼 때 데이터 계열에 대한 추가 데이터를 스택하고, 해제하고, 검토하며 모든 항의 동일한 y축을 사용할 수 있습니다. 차트의 오른쪽 상단 모서리에 있는 버튼을 사용합니다.

    [![차트 도구 오른쪽 상단 모서리 옵션 설정](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. **열 지도** 를 통해 지정된 쿼리에서 고유한 데이터 계열과 비정상적인 데이터 계열을 신속하게 찾을 수 있습니다. 하나의 검색 기간만 열 지도로 시각화할 수 있습니다.

    [Time Series Insights 탐색기 열 지도 차팅](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)] (미디어/시계열-insights-탐색기/tsi-ga-example-heatmap-charting.png#lightbox)

1. 선택 또는 마우스 오른쪽 버튼을 클릭하여 이벤트를 탐색하면 **EVENTS** 패널이 표시됩니다. 여기에서 원시 이벤트를 검토하고 이벤트를 JSON 또는 CSV 파일로 내보낼 수 있습니다. Time Series Insights에 모든 원시 데이터가 저장됩니다.

    [![이벤트](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. 이벤트를 탐색한 후 **STATS** 탭을 클릭하여 패턴, 열, 통계를 표시합니다.

    - **Patterns**: 선택한 데이터 영역에서 통계적으로 가장 유의미한 패턴을 사전에 보여줍니다. 어떤 패턴에 가장 많은 시간과 에너지가 필요한지 이해하기 위해 수천 개의 이벤트를 살펴볼 필요가 없습니다. Azure Time Series Insights를 통해 이처럼 통계적으로 유의미한 패턴으로 직접 이동하여 계속 분석할 수 있습니다. 이 기능은 과거 데이터에 대한 사후 평가 분석을 실시할 때도 유용합니다.
    - **Column Stats**: 열 통계는 선택한 TimeSpan에서 선택한 데이터 계열의 각 열에서 데이터를 분류하는 차트와 테이블을 제공합니다.

      [![통계 열 차트 및 옵션](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

이제 Azure Time Series Insights 탐색기 웹 앱에서 사용할 수 있는 주요 기능, 구성 설정 디스플레이 옵션에 대해 알아보았습니다.

## <a name="next-steps"></a>다음 단계

- Azure Time Series Insights 환경에서 [문제를 진단하고 해결](time-series-insights-diagnose-and-solve-problems.md)하는 방법에 대해 알아보세요.

- 안내에 따라 [Azure Time Series Insights 빠른 시작](time-series-quickstart.md)을 둘러봅니다.