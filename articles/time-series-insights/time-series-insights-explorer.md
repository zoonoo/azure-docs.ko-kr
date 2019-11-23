---
title: Explore data using the explorer - Azure Time Series Insights  | Microsoft Docs
description: Learn how to use the Azure Time Series Insights explorer to view your IoT data.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/20/2019
ms.custom: seodec18
ms.openlocfilehash: f6fafb2d4d363ee0d01d5bd9f18a1294ae8110b7
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327821"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights 탐색기

This article describes the features and options in general availability for the Azure Time Series Insights [explorer web app](https://insights.timeseries.azure.com/). The Time Series Insights explorer demonstrates the powerful data visualization capabilities provided by the service and can be accessed within your own environment.

Azure Time Series Insights는 완전히 관리되는 분석, 스토리지 및 시각화 서비스이며 수십억 개의 IoT 이벤트를 동시에 간편하게 탐색 및 분석할 수 있습니다. 데이터에 대한 글로벌 보기를 제공하므로 사용자는 IoT 솔루션의 유효성을 빠르게 검사할 수 있으며, 숨겨진 동향을 찾고, 문제를 찾아내고, 거의 실시간으로 근본 원인 분석을 수행할 수 있도록 지원하는 기능을 통해, 큰 비용이 수반되는 중요 업무용 장치의 가동 중지 시간을 방지할 수 있습니다. Time Series Insights 탐색기는 현재 공개 미리 보기로 제공됩니다.

> [!TIP]
> For a guided tour through the demonstration environment, read the [Azure Time Series Insights quickstart](time-series-quickstart.md).

## <a name="video"></a>비디오

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Learn about querying data by using the Time Series Insights explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>See the preceding video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Getting started with Time Series Insights by using an Azure IoT Solution Accelerator."</a>

## <a name="prerequisites"></a>전제 조건

Time Series Insights 탐색기를 사용하려면 다음을 준비해야 합니다.

- Time Series Insights 환경을 만듭니다. For more information, see [How to get started with Time Series Insights](./time-series-insights-get-started.md).
- [Provide access](time-series-insights-data-access.md) to your account in the environment.
- Add an [IoT hub](time-series-insights-how-to-add-an-event-source-iothub.md) or [event hub](time-series-insights-how-to-add-an-event-source-eventhub.md) event source to it.

## <a name="explore-and-query-data"></a>데이터 탐색 및 쿼리하기

이벤트 소스를 Time Series Insights 환경에 연결하면 몇 분 안에 시계열 데이터를 탐색하고 쿼리할 수 있습니다.

1. To start, open the [Time Series Insights explorer](https://insights.timeseries.azure.com/) in your web browser. On the left side of the window, select an environment. 액세스 가능한 모든 환경이 알파벳 순으로 표시됩니다.

1. After you select an environment, either use the **From** and **To** configurations at the top, or click and drag over the timespan you want. Select the magnifying glass in the upper-right corner, or right-click on the selected timespan and select **Search**.

1. You also can refresh availability automatically every minute by selecting the **Auto On** button. The **Auto On** button only applies to the availability chart, not the content of the main visualization.

1. The Azure cloud icon takes you to your environment in the Azure portal.

   [![Time Series Insights environment selection](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. 선택한 시간 범위에 포함된 모든 이벤트의 개수가 표시된 차트를 보게 됩니다. 여기서 몇 가지 옵션을 사용할 수 있습니다.

    - **Terms Editor panel**: The term space is where you query your environment. It’s found on the left side of the screen:
      - **MEASURE**: This drop-down list shows all numeric columns (**Doubles**).
      - **SPLIT BY**: This drop-down list shows categorical columns (**Strings**).
      - You can enable step interpolation, show minimum and maximum, and adjust the y-axis from the control panel next to **MEASURE**. You also can adjust whether data shown is a count, average, or sum of the data.
      - You can add up to five terms to view on the same x-axis. Select **Add** to add a fresh term or use the **Clone this term** button to add a copy of an existing term.

        [![Terms selection, filtering, and query panel](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predicate**: Use the predicate to quickly filter your events by using the set of operands listed in the following table. If you conduct a search by selecting or clicking, the predicate automatically updates based on that search. 지원되는 연산자는 다음과 같습니다.

         |작업(Operation)  |지원되는 형식  |참고  |
         |---------|---------|---------|
         |**<** , **>** , **<=** , **>=**    |  **Double**, **DateTime**, **TimeSpan**       |         |
         |**=** , **!=** , **<>**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |         |
         |**IN**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |  All operands should be of the same type or be **NULL** constant.        |
         |**HAS**     | **String**        |  Only constant string literals are allowed at the right side. Empty string and **NULL** aren't allowed.       |

      - **Example queries**

         [![Example GA queries](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. You can use the **Interval Size** slider tool to zoom in and out of intervals over the same timespan. The slider provides more precise control of movement between large slices of time that show smooth trends down to slices as small as the millisecond, which allow you to see granular, high-resolution cuts of your data. The slider’s default starting point is set as the most optimal view of the data from your selection to balance resolution, query speed, and granularity.

1. The **Time brush** tool makes it easy to navigate from one timespan to another.

1. Select the **Save** icon to save your current query and share it with other users of the environment. When you select the **Open** icon, you can see all of your saved queries and any shared queries of other users in environments you have access to.

   [![Queries](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>데이터 가상화

1. Use the **Perspective View** tool for a simultaneous view of up to four unique queries. The **Perspective View** button is in the upper-right corner of the chart.

   [![Select queries to add to the perspective pane](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. View a chart to visually explore your data, and use the **Chart** tools:

    - **Select** or **click** a specific timespan or a single data series.
    - Within a timespan selection, you can zoom or explore events.
    - 하나의 데이터 계열에서 다른 열을 기준으로 계열을 분할하거나, 계열을 새 기간으로 추가하거나, 선택한 계열만 표시하거나, 선택한 계열을 제외하거나, 계열에 대해 ping을 보내거나 선택한 계열의 이벤트를 탐색할 수 있습니다.
    - In the filter area to the left of the chart, you can see all displayed data series and reorder by value or name. You also can view all data series or any pinned or unpinned series. You can select a single data series and split the series by another column, add the series as a new term, show only the selected series, exclude the selected series, pin that series, or explore events from the selected series.
    - When you view multiple terms simultaneously, you can stack, unstack, see additional data about a data series, and use the same y-axis across all terms. Use the buttons in the upper-right corner of the chart.

    [![Chart tool upper right corner option settings](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Use the **heatmap** to quickly spot unique or anomalous data series in a given query. 하나의 검색 기간만 열 지도로 시각화할 수 있습니다.

    [![GA explorer heatmap charting ](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. When you explore events by selecting or right-clicking, the **EVENTS** panel is made available. 여기에서 원시 이벤트를 보고 이벤트를 JSON 또는 CSV 파일로 내보낼 수 있습니다. Time Series Insights stores all raw data.

    [![Events](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Select the **STATS** tab after you explore events to expose patterns and column stats.

    - **Patterns**: This feature proactively surfaces the most statistically significant patterns in a selected data region. You don't have to look at thousands of events to understand what patterns require the most time and energy. With Time Series Insights, you can jump directly into these statistically significant patterns to continue conducting an analysis. 이 기능은 과거 데이터에 대한 사후 평가 분석을 실시할 때도 유용합니다.
    - **Column Stats**: Column stats provide charts and tables that break down data from each column of the selected data series over the selected timespan.

      [![STATS column charting and options](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Now you've seen the various features and options that are available within the Time Series Insights explorer web app.

## <a name="next-steps"></a>다음 단계

- Learn how to [diagnose and solve problems](time-series-insights-diagnose-and-solve-problems.md) in your Time Series Insights environment.

- Take the guided [Azure Time Series Insights quickstart](time-series-quickstart.md) tour.
