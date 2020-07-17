---
title: 탐색기를 사용 하 여 데이터 탐색-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights 탐색기를 사용 하 여 IoT 데이터를 보는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81382773"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights 탐색기

이 문서에서는 Azure Time Series Insights [explorer 웹 앱](https://insights.timeseries.azure.com/)에 대 한 일반 가용성의 기능 및 옵션을 설명 합니다. Time Series Insights 탐색기는 서비스에서 제공 하는 강력한 데이터 시각화 기능을 보여 주며 사용자 환경 내에서 액세스할 수 있습니다.

Azure Time Series Insights는 완전히 관리되는 분석, 스토리지 및 시각화 서비스이며 수십억 개의 IoT 이벤트를 동시에 간편하게 탐색 및 분석할 수 있습니다. 데이터에 대한 글로벌 보기를 제공하므로 사용자는 IoT 솔루션의 유효성을 빠르게 검사할 수 있으며, 숨겨진 동향을 찾고, 문제를 찾아내고, 거의 실시간으로 근본 원인 분석을 수행할 수 있도록 지원하는 기능을 통해, 큰 비용이 수반되는 중요 업무용 장치의 가동 중지 시간을 방지할 수 있습니다. Time Series Insights 탐색기는 현재 공개 미리 보기로 제공됩니다.

> [!TIP]
> 데모 환경에서 둘러보기를 보려면 [Azure Time Series Insights 빠른](time-series-quickstart.md)시작을 참조 하세요.

## <a name="video"></a>동영상

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Time Series Insights 탐색기를 사용 하 여 데이터를 쿼리 하는 방법에 대해 알아봅니다. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>이전 비디오 <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Azure IoT 솔루션 가속기를 사용 하 여 Time Series Insights 시작"을 재생 합니다.</a>

## <a name="prerequisites"></a>필수 구성 요소

Time Series Insights 탐색기를 사용하려면 다음을 준비해야 합니다.

- Time Series Insights 환경을 만듭니다. 자세한 내용은 [Time Series Insights 시작 하는 방법](./time-series-insights-get-started.md)을 참조 하세요.
- 환경에서 사용자 계정에 대 한 [액세스를 제공](time-series-insights-data-access.md) 합니다.
- [IoT hub](time-series-insights-how-to-add-an-event-source-iothub.md) 또는 [이벤트 허브](time-series-insights-how-to-add-an-event-source-eventhub.md) 이벤트 원본을 추가 합니다.

## <a name="explore-and-query-data"></a>데이터 탐색 및 쿼리하기

이벤트 소스를 Time Series Insights 환경에 연결하면 몇 분 안에 시계열 데이터를 탐색하고 쿼리할 수 있습니다.

1. 시작 하려면 웹 브라우저에서 [Time Series Insights 탐색기](https://insights.timeseries.azure.com/) 를 엽니다. 창 왼쪽에서 환경을 선택 합니다. 액세스 가능한 모든 환경이 알파벳 순으로 표시됩니다.

1. 환경을 선택한 후에는 맨 위에서 **시작** 및 구성 **을** 사용 하거나 원하는 timespan을 선택 하 고 끕니다. 오른쪽 위 모서리에서 돋보기를 선택 하거나 선택한 timespan을 마우스 오른쪽 단추로 클릭 하 고 **검색**을 선택 합니다.

1. **자동 켜기** 단추를 선택 하 여 1 분 마다 자동으로 가용성을 새로 고칠 수도 있습니다. **자동 켜기** 단추는 주 시각화의 내용이 아니라 가용성 차트에만 적용 됩니다.

1. Azure 클라우드 아이콘을 사용 하 여 Azure Portal 환경으로 이동할 수 있습니다.

   [![환경 선택 Time Series Insights](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. 다음으로, 선택한 timespan 동안의 모든 이벤트 수를 표시 하는 차트가 표시 됩니다. 여기서 몇 가지 옵션을 사용할 수 있습니다.

    - **용어 편집기 패널**: 용어 공간은 환경을 쿼리 하는 위치입니다. 화면 왼쪽에 있습니다.
      - **MEASURE**:이 드롭다운 목록에는 모든 숫자 열 (**double**)이 표시 됩니다.
      - **분할 기준**:이 드롭다운 목록은 범주 열 (**문자열**)을 표시 합니다.
      - 단계 보간을 사용 하도록 설정 하 고, 최소값과 최대값을 표시 하 고, **측정**옆의 제어판에서 y 축을 조정할 수 있습니다. 표시 된 데이터가 데이터의 개수, 평균 또는 합계 인지를 조정할 수도 있습니다.
      - 동일한 x 축에 대해 볼 수 있는 최대 5 개의 용어를 추가할 수 있습니다. **추가** 를 선택 하 여 새 용어를 추가 하거나 **이 용어 복제** 단추를 사용 하 여 기존 용어의 복사본을 추가 합니다.

        [![용어 선택, 필터링 및 쿼리 패널](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **조건자**: 다음 표에 나열 된 피연산자 집합을 사용 하 여 이벤트를 신속 하 게 필터링 하려면 조건자를 사용 합니다. 를 선택 하거나 클릭 하 여 검색을 수행 하는 경우 조건자는 해당 검색에 따라 자동으로 업데이트 됩니다. 지원되는 연산자는 다음과 같습니다.

         |작업  |지원되는 형식  |참고  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**, **DateTime**, **TimeSpan**       |         |
         |**=**, **!=**,**<>**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |         |
         |**진행**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |  모든 피연산자는 동일한 형식 이거나 **NULL** 상수 여야 합니다.        |
         |**HAS**     | **String**        |  오른쪽에는 상수 문자열 리터럴만 사용할 수 있습니다. 빈 문자열 및 **NULL** 은 허용 되지 않습니다.       |

      - **예제 쿼리**

         [![예 GA 쿼리](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. **간격 크기** 슬라이더 도구를 사용 하 여 동일한 timespan을 기준으로 간격을 확대 및 축소할 수 있습니다. 슬라이더를 사용 하면 데이터의 세부적인 고해상도 컷을 표시 하 고 분석할 수 있는 밀리초 만큼 작은 조각으로 부드러운 추세를 표시 하는 큰 시간 조각 간의 움직임을 보다 정확 하 게 제어할 수 있습니다. 슬라이더의 기본 시작 지점은 해상도, 쿼리 속도 및 세분성의 균형을 유지 하기 위해 선택에서 가장 최적의 데이터 뷰로 설정 됩니다.

1. **시간 브러시** 도구를 사용 하면 한 timespan에서 다른 timespan으로 쉽게 이동할 수 있습니다.

1. **저장** 아이콘을 선택 하 여 현재 쿼리를 저장 하 고 환경의 다른 사용자와 공유 합니다. **열기** 아이콘을 선택 하면 액세스 권한이 있는 환경에서 다른 사용자의 저장 된 쿼리 및 공유 쿼리를 모두 검토할 수 있습니다.

   [![쿼리](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>데이터 시각화

1. 최대 4 개의 고유한 쿼리를 동시에 보려면 **큐브 뷰 보기** 도구를 사용 합니다. **원근감 뷰** 단추는 차트의 오른쪽 위 모퉁이에 있습니다.

   [![큐브 뷰 창에 추가할 쿼리를 선택 합니다.](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. 차트를 보고 데이터를 시각적으로 탐색 하 고 **차트** 도구를 사용 합니다.

    - 특정 timespan 또는 단일 데이터 계열을 **선택** 하거나 **클릭** 합니다.
    - Timespan 선택 내에서 이벤트를 확대/축소 하거나 탐색할 수 있습니다.
    - 하나의 데이터 계열에서 다른 열을 기준으로 계열을 분할하거나, 계열을 새 기간으로 추가하거나, 선택한 계열만 표시하거나, 선택한 계열을 제외하거나, 계열에 대해 ping을 보내거나 선택한 계열의 이벤트를 탐색할 수 있습니다.
    - 차트 왼쪽의 필터 영역에서 표시 된 모든 데이터 계열을 검토 하 고 값 또는 이름별로 순서를 변경할 수 있습니다. 모든 데이터 계열이 나 고정 또는 고정 해제 된 계열을 볼 수도 있습니다. 단일 데이터 계열을 선택 하 고 다른 열을 기준으로 계열을 분할 하거나, 계열을 새 용어로 추가 하거나, 선택한 계열만 표시 하거나, 선택한 계열을 제외 하거나, 해당 계열을 고정 하거나, 선택한 계열의 이벤트를 탐색할 수 있습니다.
    - 여러 용어를 동시에 표시 하는 경우 데이터 계열에 대 한 추가 데이터를 누적, 누적 및 검토 하 고 모든 용어에서 동일한 y 축을 사용할 수 있습니다. 차트의 오른쪽 위 모퉁이에 있는 단추를 사용 합니다.

    [![차트 도구 오른쪽 위 모퉁이 옵션 설정](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. **열 지도** 를 사용 하 여 지정 된 쿼리에서 고유 하거나 비정상 데이터 계열을 빠르게 찾을 수 있습니다. 하나의 검색 기간만 열 지도로 시각화할 수 있습니다.

    [![GA 탐색기 열 지도 차트](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. 이벤트를 선택 하거나 마우스 오른쪽 단추로 클릭 하 여 이벤트를 탐색할 때 **이벤트** 패널을 사용할 수 있습니다. 여기에서 모든 원시 이벤트를 검토 하 고 이벤트를 JSON 또는 CSV 파일로 내보낼 수 있습니다. Time Series Insights 모든 원시 데이터를 저장 합니다.

    [![이벤트](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. 패턴 및 열 통계를 노출 하는 이벤트를 탐색 한 후 **통계** 탭을 선택 합니다.

    - **패턴**:이 기능은 선택한 데이터 영역에서 가장 통계적으로 중요 한 패턴을 사전에 표시 합니다. 많은 시간과 에너지를 필요로 하는 패턴을 이해 하기 위해 수천 개의 이벤트를 살펴볼 필요가 없습니다. Time Series Insights를 사용 하면 통계적으로 중요 한 패턴으로 직접 이동 하 여 분석을 계속 수행할 수 있습니다. 이 기능은 과거 데이터에 대한 사후 평가 분석을 실시할 때도 유용합니다.
    - **열 통계**: 열 통계는 선택한 시간 범위 동안 선택한 데이터 계열의 각 열에서 데이터를 분할 하는 차트와 테이블을 제공 합니다.

      [![통계 열 차트 및 옵션](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

이제 Time Series Insights explorer 웹 앱에서 사용할 수 있는 주요 기능, 구성 설정 및 표시 옵션에 대해 알아보았습니다.

## <a name="next-steps"></a>다음 단계

- Time Series Insights 환경에서 [문제를 진단 하 고 해결](time-series-insights-diagnose-and-solve-problems.md) 하는 방법을 알아봅니다.

- 단계별 [Azure Time Series Insights 빠른](time-series-quickstart.md) 시작 둘러보기를 수행 합니다.
