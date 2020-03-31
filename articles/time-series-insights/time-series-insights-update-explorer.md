---
title: 미리 보기 탐색기에서 데이터 시각화 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: Azure Time Series 인사이트 미리 보기 탐색기에서 사용할 수 있는 기능 및 옵션에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75861764"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Azure Time Series 인사이트 미리 보기 탐색기

이 문서에서는 Azure Time Series Insights 미리 보기 [데모 웹 응용 프로그램에서](https://insights.timeseries.azure.com/preview/demo)사용할 수 있는 다양한 기능 및 옵션에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Time Series 인사이트 미리 보기 탐색기를 시작하려면 다음을 수행해야 합니다.

* 타임시리즈 인사이트 환경을 프로비전합니다. [Azure Time Series 인사이트 미리 보기](./time-series-insights-update-create-environment.md) 자습서를 읽고 인스턴스 프로비저닝에 대해 자세히 알아봅니다.
* 계정에 대해 만든 타임시리즈 인사이트 환경에 대한 [데이터 액세스를 제공합니다.](./time-series-insights-data-access.md) 자신과 다른 사용자에 대한 액세스 권한을 모두 부여할 수 있습니다.
* 이벤트 소스를 타임시리즈 인사이트 환경에 추가하여 데이터를 환경에 푸시합니다.
  * [이벤트 허브에 연결하는 방법](./time-series-insights-how-to-add-an-event-source-eventhub.md) 알아보기 
  * [IoT 허브에 연결하는 방법](./time-series-insights-how-to-add-an-event-source-iothub.md) 알아보기

## <a name="explore-the-time-series-insights-preview-explorer"></a>타임시리즈 인사이트 미리보기 탐색기 살펴보기

Azure Time Series 인사이트 미리 보기 탐색기는 다음 7가지 요소로 구성됩니다.

[![타임시리즈 인사이트 미리보기 탐색기 개요](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [환경 패널](#1-environment-panel): 모든 Azure 타임시리즈 인사이트 환경을 표시합니다.
1. [탐색 모음](#2-navigation-bar): **분석** 페이지와 **모델** 페이지 간에 전환할 수 있습니다.
1. [계층 구조 트리 및 검색 패널](#3-hierarchy-tree-and-search-panel): 차트를 구성할 특정 데이터 요소를 선택하고 검색할 수 있습니다.
1. [[타임시리즈]](#4-time-series-well): 현재 선택한 모든 데이터 요소를 표시합니다.
1. [차트 패널](#5-chart-panel): 현재 작업 차트를 표시합니다.
1. [타임라인](#6-time-editor-panel): 작업 시간 범위를 수정할 수 있습니다.
1. [앱 표시줄](#7-app-bar): 사용자 관리 옵션(예: 현재 테넌트)을 포함하고 사용자 및 언어 설정을 변경할 수 있습니다.


## <a name="1-environment-panel"></a>1. 환경 패널

환경 패널에는 액세스 권한이 있는 모든 Time Series Insights 환경이 표시됩니다. 이 목록에는 종량제(미리 보기) 환경과 S1/S2 환경(일반 공급)이 포함됩니다. 사용할 타임시리즈 인사이트 환경을 선택하기만 하면 바로 사용할 수 있습니다.

1. 표시된 환경 옆에 있는 드롭다운 화살표를 선택합니다.

   [![환경 패널](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 그런 다음 원하는 환경을 선택합니다.

## <a name="2-navigation-bar"></a>2. 내비게이션 바

  [![탐색 모음](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

탐색 모음을 사용하여 두 뷰 중에서 선택할 수 있습니다.

* **분석**: 모델링또는 모델링되지 않은 타임시리즈 데이터를 차트화하고 풍부한 분석을 수행하는 데 사용합니다.
* **모델**: 새로운 타임시리즈 인사이트 미리 보기 유형, 계층 구조 및 인스턴스를 타임시리즈 인사이트 모델로 푸시하는 데 사용합니다.

### <a name="model-authoring"></a>모델 작성

Azure Time Series 인사이트 미리 보기는 열열 모델에서 전체 만들기, 읽기, 업데이트 및 삭제(CRUD) 작업을 지원합니다.

[![모델 검색 패널](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **열계 모델 유형**: 타임시리즈 인사이트 유형을 사용하여 계산을 수행하기 위한 변수 또는 수식을 정의할 수 있습니다. 지정된 타임시리즈 인사이트 인스턴스와 연결됩니다. 형식에는 하나 이상의 변수가 포함될 수 있습니다.
* **타임시리즈 모델 계층 구조**: 계층 구조는 데이터의 체계적인 조직입니다. 계층 구조는 Time Series Insights 데이터에 있는 서로 다른 엔티티 간의 관계를 나타냅니다.
* **타임시리즈 모델 인스턴스**: 인스턴스는 타임계 자체입니다. 대부분의 경우 환경에서 자산의 고유 식별자인 **DeviceID** 또는 **AssetID입니다.**

타임시리즈 모델에 대해 자세히 알아보려면 [타임즈 시리즈 모델을](./time-series-insights-update-tsm.md)참조하십시오.

## <a name="3-hierarchy-tree-and-search-panel"></a>3. 계층 구조 트리 및 검색 패널

계층 구조 트리 및 검색 패널을 사용하면 [타임시리즈 모델](./time-series-insights-update-tsm.md) 계층구조를 쉽게 검색하고 탐색하여 차트에 표시할 특정 열계 인스턴스를 찾을 수 있습니다. 인스턴스를 선택하면 현재 차트에 추가될 뿐만 아니라 데이터에 잘 추가됩니다. 

[![계층 구조 트리 및 검색 패널](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

또한 검색 결과 창을 사용하면 계층 구조 보기 또는 목록 보기에서 결과를 볼 수 있으므로 표시할 인스턴스를 쉽게 찾을 수 있습니다.
 
## <a name="4-time-series-well"></a>4. 잘 타임 시리즈

웰에는 선택한 Time Series Insights 인스턴스와 연결된 인스턴스 필드 및 기타 메타데이터가 표시됩니다. 오른쪽에 있는 확인란을 선택하면 현재 차트에서 특정 인스턴스를 숨기거나 표시할 수 있습니다. 

  [![미리보기 잘](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

요소의 왼쪽에 있는 빨간색 삭제(휴지통 **Delete** 수) 컨트롤을 선택하여 현재 데이터에서 특정 데이터 요소를 제거할 수 있습니다. 또한 잘은 각 요소가 차트에 표시되는 방법을 제어 할 수 있습니다. 최소/최대 그림자, 데이터 요소를 추가하고, 요소를 시간에 따라 이동하고, 인스턴스를 단계별 방식으로 시각화하도록 선택할 수 있습니다. 

또한 탐색 컨트롤을 사용하면 시간 이동및 분산도를 쉽게 만들 수 있습니다.  

  [![웰 레이아웃 옵션](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 다음 메시지가 나타나면 선택한 시간 범위 동안 인스턴스에 데이터가 없습니다. 문제를 해결하려면 기간을 늘리거나 인스턴스가 데이터를 푸시하고 있는지 확인합니다.
>
> ![데이터 알림 없음](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. 차트 패널

차트를 사용하면 타임계열 인스턴스를 선으로 표시할 수 있습니다. 웹 컨트롤을 클릭해서 환경 패널, 데이터 모델 및 시간 범위 제어 패널을 접으면 차트를 확장할 수 있습니다. 

  [![차트 개요 미리 보기](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **차트 유형**: 시각화에 사용할 수 있는 데이터 요소를 제어합니다.

1. **간격 크기**: 간격 크기 슬라이더 도구를 사용하면 동일한 시간 범위에 걸쳐 간격을 확대 및 축소할 수 있습니다. 이렇게 하면 밀리초만큼 작은 조각으로 부드러운 추세를 표시하는 큰 시간 조각 간의 움직임을 보다 정밀하게 제어할 수 있으므로 데이터의 세분화된 고해상도 컷을 검토할 수 있습니다. 슬라이더의 기본 시작점은 선택한 데이터를 최적으로 볼 수 있는 보기로 해상도, 쿼리 속도, 세분성이 균형을 이루도록 설정됩니다.

1. **확대/축소 및 이동**: 이 컨트롤을 선택하여 차트를 확대/축소하고 이동합니다.

1. **Y축 제어**: 사용 가능한 y축 뷰 옵션을 순환합니다.

    * `Stacked`: 각 라인에는 개별 Y축이 있습니다.
    * `Overlap`: 선택한 선에 따라 Y축 데이터가 변경되는 동일한 Y축에 여러 선을 스택하는 데 사용합니다.
    * `Shared`: 모든 Y축 데이터가 함께 표시됩니다.

1. **마커 요소**: 현재 선택된 데이터 요소 및 관련 세부 정보입니다.

마우스를 누른 다음 선택한 영역을 원하는 끝점으로 드래그하여 현재 그래프의 데이터 포인트를 **왼쪽으로 클릭하여** 특정 데이터 조각을 더 드릴다운할 수 있습니다. 선택한 파란색 **영역을 마우스 오른쪽 단추로 클릭한** 다음 아래그림과 같이 **확대/축소를** 선택합니다. 선택한 시간 범위에서 원격 분석 이벤트를 보고 다운로드할 수도 있습니다.

  [![차트 확대/축소 미리 보기](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

**확대/축소** 작업을 수행하면 선택한 데이터 집합이 표시됩니다. 타임시리즈 인사이트 데이터의 세 가지 y축 표현을 순환할 형식 컨트롤을 선택합니다.

  [![차트 y축 미리 보기](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

여기서 **겹치는 차트의** 예가 제공됩니다.

  [![차트 겹치는 옵션](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

**더 많은 작업** 단추는 **다운로드를 CSV로**표시하고, Power **BI에 연결하고,** 차트 **데이터를 테이블로 표시하고,** 원시 이벤트 옵션을 **탐색하도록** 확장됩니다.

  [![추가 작업 옵션](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

[[열계 인사이트] 네이티브 Power BI 커넥터에서](concepts-power-bi.md) **Power BI연결** 옵션에 대해 자세히 알아보십시오.

## <a name="6-time-editor-panel"></a>6. 시간 편집기 패널

타임시리즈 인사이트로 작업할 때 먼저 시간 범위를 선택합니다. 선택한 시간 범위는 타임시리즈 인사이트 업데이트 위젯을 사용하여 조작할 수 있는 데이터 집합을 제어합니다.

  [![시간 선택 패널](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> 타임라인의 일부가 주황색 또는 주황색으로 강조 표시되어 웜 스토어에서 사용할 수 있는 데이터의 범위를 나타냅니다.

작업 시간 범위를 선택하기 위한 다음 웹 컨트롤은 Time Series Insights 업데이트에서 사용할 수 있습니다. 

  [![탐사 우물 제어](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **내부 날짜 범위 슬라이더 컨트롤**: 원하는 시간 범위에 걸쳐 드래그하여 두 끝점 컨트롤을 사용합니다. 이 내부 날짜 범위는 외부 날짜 범위 슬라이더 컨트롤에 의해 제한됩니다.

1. **날짜 범위 버튼 증가 및 감소**: 원하는 간격에 대해 어느 버튼을 선택하여 시간 범위를 늘리거나 줄입니다.

1. **시간 범위 축소 컨트롤**:이 웹 컨트롤을 사용하면 내부 날짜 범위 슬라이더 도구를 제외한 모든 컨트롤을 숨길 수 있습니다.

1. **외부 날짜 범위 슬라이더 컨트롤**: 끝점 컨트롤을 사용하여 내부 날짜 범위 컨트롤에 사용할 수 있는 외부 날짜 범위를 선택합니다.

1. **시간 범위 슬라이더 컨트롤**: 마지막 **30 분,** **마지막 12 시간,** 또는 **사용자 정의 범위와**같은 미리 설정된 시간 범위 선택 간에 빠르게 전환하는 데 사용합니다. 이 값을 변경하면 내부 크기 슬라이더 도구에서 설명하는 사용 가능한 간격 범위도 변경됩니다.

   [![선택 패널에서](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. 앱 바

타임시리즈 인사이트 미리보기 탐색 패널이 타임시리즈 인사이트 앱 상단에 표시됩니다. 다음과 같은 기능을 제공합니다.

### <a name="current-session-share-link-control"></a>현재 세션 공유 링크 컨트롤

  [![공유 아이콘](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

새 **공유** 아이콘을 선택하여 팀과 URL 링크를 공유합니다.

  [![인스턴스 URL 공유](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>테넌트 섹션

  [![테넌트 선택](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* 현재 타임시리즈 인사이트 로그인 계정 정보를 표시합니다.
* 사용 가능한 타임시리즈 인사이트 테마 를 전환하는 데 사용합니다.
* 이를 사용하여 미리 보기 데모 웹 앱을 볼 수 [있습니다.](https://insights.timeseries.azure.com/preview/demo)

### <a name="theme-selection"></a>테마 선택

새 테마를 선택하려면 오른쪽 상단에 있는 프로필 아이콘을 선택합니다. 그런 다음 **테마 변경을**선택합니다.

  [![테마 선택](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> 프로필 아이콘을 선택하여 언어 선택도 가능합니다.

Azure Time Series Insights 미리 보기는 다음 두 가지 테마를 지원합니다.

* **라이트 테마**: 이 문서 전체에 표시되는 기본 테마입니다.
* **어두운 테마**: 다음과 같이 탐색기를 렌더링합니다.

  [![선택한 어두운 테마](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 환경 컨트롤

### <a name="preview-terms-panel"></a>용어 미리보기 패널

이 섹션은 업데이트된 UI에서 탐색기를 사용하려는 기존 S1/S2 환경에만 적용됩니다. 일반적으로 사용 가능한 제품과 미리 보기를 함께 사용할 수 있습니다. 기존 U의 일부 기능을 업데이트된 탐색기에 추가했지만, 기존 Time Series Insights 탐색기에서 S1/S2 환경에 대한 전체 UI 환경을 이용할 수 있습니다. 

계층 구조 대신 타임시리즈 인사이트 용어 패널이 표시됩니다. 용어 패널을 사용하면 사용자 환경에서 쿼리를 정의할 수 있습니다. 또한 술어를 기반으로 데이터를 필터링하는 데 사용합니다.

  [![여기서 쿼리 패널](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Time Series Insights 미리 보기 용어 편집기 패널은 다음 매개 변수를 사용합니다.

**위치**: where 절을 사용하여 다음 표에 나열된 진연산자 집합을 사용하여 이벤트를 빠르게 필터링합니다. 피연산자를 선택하여 검색을 수행하는 경우 조건자는 해당 검색에 따라 자동으로 업데이트됩니다. 지원되는 피연산자 유형은 다음과 같습니다.

| 작업(Operation) | 지원되는 형식   | 메모 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | 모든 연산자는 같은 형식이거나 NULL 상수여야 합니다. |
| `HAS` | String | 오른쪽에는 상수 문자열 리터럴만 허용됩니다. 빈 문자열과 NULL은 허용되지 않습니다. |

지원되는 쿼리 작업 및 데이터 형식에 대해 자세히 알아보려면 [TSX(열렬 표현식)를](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)참조하십시오.

### <a name="examples-of-where-clauses"></a>위치 절의 예

  [![여기서 절 예제](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**측정값**: 현재 차트의 요소로 사용할 수 있는 모든 숫자**열(Doubles)을**표시하는 드롭다운 목록입니다.

**분할 :** 이 드롭다운 목록에는 데이터를 그룹화할 수 있는 모델에서 사용 가능한 모든 범주형 열(문자열)이 표시됩니다. 동일한 x축에서 볼 수 있도록 최대 5개의 용어를 추가할 수 있습니다. 원하는 매개변수를 입력한 다음 **추가를** 선택하여 새 용어를 추가합니다.

  [![쿼리 및 필터링된 보기 1개](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

다음 이미지와 같이 표시되는 아이콘을 선택하여 차트 패널에서 요소를 표시하고 숨길 수 있습니다. 쿼리를 완전히 제거하려면 빨간색 **X를**선택합니다.

  [![쿼리 및 필터링된 옵션 취소](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>다음 단계

- Azure Time Series 인사이트 미리 보기에서 [저장소 및 시작에](./time-series-insights-update-storage-ingress.md) 대해 알아봅니다.

- [데이터 모델링에](./time-series-insights-update-tsm.md)대한 타임시리즈 인사이트 미리 보기 문서를 읽어보십시오.

- 타임시리즈 인사이트 [인스턴스를 진단하고 문제를 해결하는 방법을](./time-series-insights-update-how-to-troubleshoot.md) 알아봅니다.
