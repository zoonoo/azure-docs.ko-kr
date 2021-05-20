---
title: Time Series Insights 탐색기에서 데이터 시각화 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights 탐색기에서 제공되는 기능 및 옵션에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 77c6e8790451ef830b37cc5914ce9de8e92174f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464091"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights 탐색기

이 문서에서는 Azure Time Series Insights Gen2 [데모 환경](https://insights.timeseries.azure.com/preview/demo)에서 사용할 수 있는 다양한 기능 및 옵션을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Time Series Insights 탐색기를 시작하려면 다음을 수행해야 합니다.

* Azure Time Series Insights Gen2 환경을 프로비전합니다. [Azure Time Series Insights Gen2](./tutorial-set-up-environment.md) 자습서를 통해 인스턴스를 프로비저닝하는 방법에 대해 자세히 알아보세요.
* 계정에 대해 만든 Azure Time Series Insights Gen2 환경에 [데이터 액세스를 제공](./concepts-access-policies.md)합니다. 자신과 다른 사용자에 대한 액세스 권한을 모두 부여할 수 있습니다.
* Azure Time Series Insights Gen2 환경에 이벤트 원본을 추가하여 데이터를 환경으로 푸시합니다.
  * [이벤트 허브에 연결하는 방법](./how-to-ingest-data-event-hub.md) 알아보기
  * [IoT Hub에 연결하는 방법](./how-to-ingest-data-iot-hub.md) 알아보기

## <a name="explore-the-azure-time-series-insights-explorer"></a>Azure Time Series Insights 탐색기 살펴보기

Azure Time Series Insights 탐색기는 다음과 같은 7가지 요소로 구성되어 있습니다.

[![Azure Time Series Insights 탐색기 개요](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [환경 패널](#1-environment-panel): 모든 Azure Time Series Insights Gen2 환경을 표시합니다.
1. [탐색 표시줄](#2-navigation-bar): **분석** 및 **모델** 페이지 사이를 전환할 수 있습니다.
1. [계층 구조 트리 및 검색 패널](#3-hierarchy-tree-and-search-panel): 차트를 만들 특정 데이터 요소를 선택하고 검색할 수 있습니다.
1. [시계열 웰](#4-time-series-well): 현재 선택한 데이터 요소를 모두 표시합니다.
1. [차트 패널](#5-chart-panel): 현재 작업 중인 차트를 표시합니다.
1. [타임라인](#6-time-editor-panel): 작업 시간 범위를 수정할 수 있습니다.
1. [앱 표시줄](#7-app-bar): 현재 테넌트와 같은 사용자 관리 옵션을 포함하며 그러한 옵션 및 언어 설정을 변경할 수 있습니다.

## <a name="1-environment-panel"></a>1. 환경 패널

환경 패널에는 액세스 권한이 있는 모든 Azure Time Series Insights Gen2 환경이 표시됩니다. 목록에는 Gen2 환경과 Gen1 환경이 포함됩니다. 즉시 사용하려는 환경을 선택하기만 하면 됩니다.

1. 표시된 환경 옆에 있는 드롭다운 화살표를 선택합니다.

   [![환경 패널](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 그런 다음, 원하는 환경을 선택합니다.

## <a name="2-navigation-bar"></a>2. 탐색 표시줄

  [![탐색 표시줄](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

탐색 표시줄을 사용하여 두 보기 중에서 선택합니다.

* **분석**: 모델링되거나 모델링되지 않은 시계열 데이터의 차트를 작성하고 다양한 분석을 수행합니다.
* **모델**: 새 Azure Time Series Insights Gen2 유형, 계층 구조 및 인스턴스를 시계열 모델에 푸시하는 데 사용합니다.

### <a name="model-authoring"></a>모델 작성

Azure Time Series Insights Gen2는 시계열 모델에 대한 모든 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 지원합니다.

[![모델 검색 패널](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **시계열 모델 유형**: 시계열 모델 유형을 사용하여 계산을 수행하는 변수나 수식을 정의할 수 있습니다. 지정된 시계열 모델 인스턴스와 연결됩니다. 형식에는 하나 이상의 변수가 포함될 수 있습니다.
* **시계열 모델 계층 구조**: 계층 구조는 데이터의 체계적 조직입니다. 계층 구조는 시계열 모델에 있는 서로 다른 인스턴스 간의 관계를 나타냅니다.
* **시계열 모델 인스턴스**: 인스턴스는 시계열 자체입니다. 대부분의 경우 환경에 있는 자산의 고유 식별자인 **DeviceID** 또는 **AssetID** 입니다.

시계열 모델에 대한 자세한 내용은 [시계열 모델](./concepts-model-overview.md)을 참조하세요.

## <a name="3-hierarchy-tree-and-search-panel"></a>3. 계층 트리 및 검색 패널

계층 트리 및 검색 패널을 사용하면 [시계열 모델](./concepts-model-overview.md) 계층 구조를 쉽게 검색하고 탐색하여 차트에 표시할 특정 시계열 인스턴스를 찾을 수 있습니다. 인스턴스를 선택하는 경우 해당 인스턴스는 현재 차트에 추가될 뿐만 아니라 데이터 웰에도 추가됩니다.

[![계층 트리 및 검색 패널](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

검색 결과 창을 사용하여 계층 구조 보기나 목록 보기에 결과를 표시하여 표시하려는 인스턴스를 쉽게 찾을 수도 있습니다.

## <a name="4-time-series-well"></a>4. 시계열 웰

선택한 시계열 모델 인스턴스와 연결된 인스턴스 필드 및 기타 메타데이터를 표시합니다. 오른쪽의 확인란을 선택하여 현재 차트에서 특정 인스턴스를 숨기거나 표시할 수 있습니다.

  [![Gen2 웰](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

요소의 왼쪽에 있는 빨간색 **삭제**(휴지통) 컨트롤을 선택하여 현재 데이터에서 데이터 웰에서 특정 데이터 요소를 제거할 수 있습니다. 웰을 사용하면 각 요소가 차트에 표시되는 방식을 제어할 수도 있습니다. 최소/최대 그림자, 데이터 포인트를 추가하고, 요소를 시간으로 이동하고, 인스턴스를 단계별 방식으로 시각화하도록 선택할 수 있습니다.

또한 탐색 컨트롤을 사용하여 시간 이동 및 분산형 차트를 쉽게 만들 수 있습니다.

  [![웰 레이아웃 옵션](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 다음 메시지가 표시되면 선택한 시간 범위 동안 인스턴스에 데이터가 없는 것입니다. 이 문제를 해결하려면 시간 범위를 늘리거나 인스턴스가 데이터를 푸시하는지 확인합니다.
>
> ![데이터 알림 없음](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. 차트 패널

차트를 사용하면 시계열 인스턴스를 선으로 표시할 수 있습니다. 웹 컨트롤을 클릭해서 환경 패널, 데이터 모델 및 시간 범위 제어 패널을 접으면 차트를 확장할 수 있습니다.

  [![Gen2 차트 개요](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **차트 유형**: 시각화에 사용할 수 있는 데이터 요소를 제어합니다.

1. **간격 크기**: 간격 크기 슬라이더 도구를 사용하면 동일한 시간 범위에 대해 간격을 확대하거나 축소할 수 있습니다. 이렇게 하면 매끄러운 추세를 밀리초만큼 작은 조각으로 보여 줌으로써 대규모 시간 조각 간의 이동을 더 정확하게 제어할 수 있으므로 세분화된 고해상도 데이터 조각을 검토할 수 있습니다. 슬라이더의 기본 시작점은 선택한 데이터를 최적으로 볼 수 있는 보기로 설정되어 해상도, 쿼리 속도, 세분성이 균형을 이룹니다.

1. **확대/축소 및 이동**: 차트를 확대/축소하고 이동하려면 이 컨트롤을 선택합니다.

1. **Y축 컨트롤**: 사용 가능한 Y축 보기 옵션을 순환합니다.

    * `Stacked`: 각 선마다 개별 Y축이 있습니다.
    * `Overlap`: 이를 사용하여 동일한 Y축에 여러 개의 선을 누적할 수 있으며, 선택한 선을 기준으로 Y축 데이터가 변경됩니다.
    * `Shared`: 모든 Y축 데이터가 함께 표시됩니다.

1. **마커 요소**: 현재 선택한 데이터 요소 및 관련된 세부 정보입니다.

현재 그래프에서 데이터 요소를 **마우스 왼쪽 단추로 클릭** 한 다음, 마우스를 누르고 있는 상태로 선택한 영역을 선택한 엔드포인트로 끌어 특정 데이터 조각을 드릴할 수 있습니다. 파란색 선택 영역을 **마우스 오른쪽 단추로 클릭** 한 다음, 아래와 같이 **확대/축소** 를 선택합니다. 선택한 시간 간격에서 원격 분석 이벤트를 보고 다운로드할 수도 있습니다.

  [![Gen2 차트 확대/축소](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

**확대/축소** 작업을 수행하면 선택한 데이터 세트가 표시됩니다. 형식 컨트롤을 선택하여 사용자 데이터의 Y축 표시 세 개를 순환합니다.

  [![Gen2 차트 Y축](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

여기에는 **겹치는 차트** 의 예제가 제공됩니다.

  [![겹치는 차트 옵션](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

**추가 작업** 단추는 **CSV로 다운로드**, **Power BI에 연결**, **차트 데이터를 테이블로 표시** 및 **원시 이벤트 탐색** 옵션을 표시하도록 확장됩니다.

  [![추가 작업 옵션](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

[Power BI 커넥터](concepts-power-bi.md)의 **Power BI에 연결** 옵션에 대해 자세히 알아보세요.

## <a name="6-time-editor-panel"></a>6. 기간 편집기 패널

Azure Time Series Insights Gen2로 작업할 때 먼저 시간 범위를 선택합니다. 선택한 시간 범위는 Azure Time Series Insights Gen2 업데이트 위젯을 통한 조작에 사용할 수 있는 데이터 세트를 제어합니다.

  [![시간 선택 패널](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> 타임라인의 일부는 웜 저장소에서 사용할 수 있는 데이터의 범위를 나타내기 위해 황색 또는 주황색으로 강조 표시됩니다.

다음 웹 컨트롤은 Azure Time Series Insights Gen2에서 작업 시간 범위를 선택하는 데 사용할 수 있습니다.

  [![탐색 웰 컨트롤](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **내부 날짜 범위 슬라이더 컨트롤**: 원하는 시간 범위 위로 끌어 두 엔드포인트 컨트롤을 사용합니다. 이 내부 날짜 범위는 외부 날짜 범위 슬라이더 컨트롤에 의해 제한됩니다.

1. **날짜 범위 증가 및 감소 단추**: 원하는 간격의 단추를 선택하여 시간 범위를 늘리거나 줄입니다.

1. **시간 범위 축소 컨트롤**: 이 웹 컨트롤을 사용하면 내부 날짜 범위 슬라이더 도구를 제외한 모든 컨트롤을 숨길 수 있습니다.

1. **외부 날짜 범위 슬라이더 컨트롤**: 엔드포인트 컨트롤을 사용하여 내부 날짜 범위 제어에 사용할 수 있는 외부 날짜 범위를 선택할 수 있습니다.

1. **시간 범위 슬라이더 컨트롤**: 지난 **30분**, **지난 12시간** 또는 **사용자 지정 범위** 와 같은 사전 설정된 시간 범위 선택 사이를 빠르게 전환할 수 있습니다. 이 값을 변경하면 내부 크기 슬라이더 도구에서 설명하는 사용 가능한 간격 범위도 변경됩니다.

   [![선택 패널로/에서](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. 앱 표시줄

Azure Time Series Insights Gen2 탐색 패널이 앱 위쪽에 표시됩니다. 다음과 같은 기능을 제공합니다.

### <a name="current-session-share-link-control"></a>현재 세션 공유 링크 컨트롤

  [![공유 아이콘](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

새 **공유** 아이콘을 선택하여 팀과 URL 링크를 공유합니다.

  [![인스턴스 URL 공유](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>테넌트 섹션

  [![테넌트 선택](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* 현재 Azure Time Series Insights Gen2 로그인 계정 정보를 표시합니다.
* 사용 가능한 테마 사이를 전환하는 데 사용합니다.
* Gen2 [데모 환경을](https://insights.timeseries.azure.com/preview/demo) 보는 데 사용합니다.

### <a name="theme-selection"></a>테마 선택

새 테마를 선택하려면 오른쪽 위 모서리에 있는 프로필 아이콘을 선택합니다. 그런 다음, **테마 변경** 을 선택합니다.

  [![테마 선택](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> 프로필 아이콘을 선택하여 언어를 선택할 수도 있습니다.

Azure Time Series Insights 탐색기는 다음 두 가지 테마를 지원합니다.

* **밝은 테마**: 이 문서 전체에 표시된 기본 테마입니다.
* **어두운 테마**:다음과 같이 탐색기를 렌더링합니다.

  [![선택된 어두운 테마](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Gen1 환경 컨트롤

### <a name="gen2-terms-panel"></a>Gen2 용어 패널

이 섹션은 업데이트된 UI에서 탐색기를 사용하려는 기존 Gen1 환경에만 적용됩니다. Gen1 제품과 Gen2 제품을 조합해서 사용할 수 있습니다. 기존 UI의 일부 기능을 업데이트된 탐색기에 추가했지만, 새로운 Azure Time Series Insights 탐색기에서 Gen1 환경에 대한 전체 UI 환경을 이용할 수 있습니다.

계층 구조 대신 Azure Time Series Insights Gen2 용어 패널이 표시됩니다. 용어 패널을 사용하여 사용자 환경에서 쿼리를 정의할 수 있습니다. 조건자를 기준으로 데이터를 필터링하는 데도 사용합니다.

  [![Where 쿼리 패널](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Azure Time Series Insights Gen2 용어 편집 패널은 다음 매개 변수를 사용합니다.

**Where**: 다음 표에 나열된 피연산자 세트를 사용하여 이벤트를 빠르게 필터링하는 데 where 절을 사용합니다. 피연산자를 선택하여 검색을 수행하는 경우 조건자는 해당 검색에 따라 자동으로 업데이트됩니다. 지원되는 피연산자 유형은 다음과 같습니다.

| 작업    | 지원되는 형식    | 참고 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | 모든 연산자는 같은 형식이거나 NULL 상수여야 합니다. |
| `HAS` | 문자열 | 우측 항에는 상수 문자열 리터럴만 사용할 수 있습니다. 빈 문자열과 NULL은 허용되지 않습니다. |

지원되는 쿼리 작업 및 데이터 형식에 대해 자세히 알아보려면 [TSX(시계열 식)](/rest/api/time-series-insights/reference-time-series-expression-syntax)를 읽어보세요.

### <a name="examples-of-where-clauses"></a>where 절의 예제

  [![where 절 예제](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Measure**: 드롭다운 목록에는 현재 차트의 요소로 사용할 수 있는 모든 숫자 열(**Doubles**)이 표시됩니다.

**Split by**: 이 드롭다운 목록에는 데이터를 그룹화하는 기준으로 사용할 수 있는, 모델에서 사용 가능한 모든 범주 열(문자열)이 표시됩니다. 동일한 X축에서 최대 5개의 용어를 더할 수 있습니다. 원하는 매개 변수를 입력한 다음, **추가** 를 선택하여 새 용어를 추가합니다.

  [![쿼리 및 필터링된 보기 1](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

다음 이미지에 나와 있는 표시 아이콘을 선택하여 차트 패널에서 요소를 표시하고 숨길 수 있습니다. 쿼리를 완전히 제거하려면 빨간색 **X** 를 선택합니다.

  [![쿼리 및 필터링된 옵션 취소](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>다음 단계

* 사용자 환경에 [데이터를 수집](./concepts-ingestion-overview.md)하는 방법에 대해 알아봅니다.

* [스토리지](concepts-storage.md)에 대한 문서를 확인하세요.

* Azure Time Series Insights Gen2의 [데이터 모델링](./concepts-model-overview.md)을 읽어보세요.

* 환경을 [진단하고 문제를 해결하는 방법](./how-to-diagnose-troubleshoot.md)을 알아봅니다.
