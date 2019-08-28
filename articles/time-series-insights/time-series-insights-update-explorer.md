---
title: Azure Time Series Insights 미리 보기 탐색기에서 데이터 시각화 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights 미리 보기 탐색기 웹앱에서 사용할 수 있는 기능 및 옵션에 대해 설명합니다.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 33e485e4fcee665e810c42bca6b38aac065ff668
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841439"
---
# <a name="visualize-data-in-the-explorer-preview"></a>탐색기 미리 보기에서 데이터 시각화

이 문서에서는 Azure Time Series Insights Preview [demo 웹 앱](https://insights.timeseries.azure.com/preview/demo)의 사용자 인터페이스 및 사용자 환경 기능 및 인터페이스에 대해 설명 합니다. 특히 호스팅된 샘플의 레이아웃, 인터페이스 사용자 지정 옵션 및 제공 된 데모를 통한 탐색에 대해 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Time Series Insights Preview 탐색기를 시작 하려면 다음을 수행 해야 합니다.

* Time Series Insights 환경을 설정합니다. 인스턴스를 프로 비전 하는 방법에 대해 자세히 알아보려면 [Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md) 자습서를 사용해 보세요.
* 계정에 대해 만든 Time Series Insights 환경에 대 한 [데이터 액세스를 제공](./time-series-insights-data-access.md) 합니다. 자신과 다른 사용자에 대한 액세스 권한을 모두 부여할 수 있습니다.
* Time Series Insights 환경에 이벤트 소스를 추가 하 여 환경에 데이터를 푸시합니다.
  * [이벤트 허브에 연결 하는 방법을](./time-series-insights-how-to-add-an-event-source-eventhub.md)알아봅니다.
  * [IoT hub에 연결 하는 방법을](./time-series-insights-how-to-add-an-event-source-iothub.md)알아봅니다.

## <a name="learn-about-the-preview-explorer"></a>Preview 탐색기에 대 한 자세한 정보

Azure Time Series Insights 미리 보기 탐색기는 다음 요소로 구성되어 있습니다.

[![탐색기 보기](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">환경 패널</a>: Azure Time Series Insights 환경을 표시 합니다.
- <a href="#navigation-menu">탐색 메뉴</a>: **분석** 및 **모델** 페이지 간을 전환 하는 데 사용 합니다.
- <a href="#hierarchy-tree">계층 구조 트리</a>: 이를 사용 하 여 차트가 될 특정 모델 및 데이터 요소를 선택할 수 있습니다.
- <a href="#preview-well">시계열</a>: 현재 선택한 데이터 요소를 색 구분과 함께 테이블 형식으로 표시 합니다.
- <a href="#preview-chart">차트 패널</a>: 현재 작업 중인 차트를 표시합니다.
- <a href="#time-editor-panel">타임라인</a>: 작업 시간 범위를 수정 하는 데 사용 합니다.
- <a href="#navigation-panel">앱 바</a>: 현재 테 넌 트와 같은 사용자 관리 옵션을 포함 합니다. 테마 및 언어 설정을 변경 하는 데 사용할 수 있습니다.

## <a name="environment-drop-down-list"></a>환경 드롭다운 목록

환경 드롭다운 목록에는 사용자가 액세스할 수 있는 모든 Time Series Insights 환경이 표시 됩니다. 이 목록에는 Time Series Insights 미리 보기와 같은 종 량 제 환경이 포함 됩니다. 이 목록에는 일반적으로 사용할 수 있는 S1/S2 환경도 포함 되어 있습니다.

1. 표시 된 환경 옆에 있는 드롭다운 화살표를 선택 합니다.

   [![제어판](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. 그런 다음 원하는 환경을 선택 합니다.

## <a name="navigation-menu"></a>탐색 메뉴

  [![탐색 메뉴](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

탐색 메뉴를 사용 하 여 두 뷰 중에서 선택 합니다.

* **분석**: 이를 사용 하 여 모델링 되거나 모델링 되지 않은 시계열 데이터에 대해 다양 한 분석을 수행 합니다.
* **모델**: 이를 사용 하 여 새 Time Series Insights 미리 보기 유형, 계층 및 인스턴스를 Time Series Insights 모델에 푸시합니다.

## <a name="hierarchy-tree"></a>계층 구조 트리

계층 구조 트리에는 장치에 모델, 특정 장치 및 센서가 포함 된 선택한 데이터 요소가 표시 됩니다.

### <a name="model-search-panel"></a>모델 검색 패널

모델 검색 패널을 사용 하 여 시계열 모델 계층 구조를 쉽게 검색 하 고 탐색 하 여 차트에 표시할 특정 시계열 인스턴스를 찾을 수 있습니다. 인스턴스를 선택 하면 현재 차트와 데이터 웰에 모두 추가 됩니다.

  [![모델 검색 패널](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>모델 작성

Azure Time Series Insights 미리 보기는 시계열 모델에 대 한 모든 CRUD (만들기, 읽기, 업데이트 및 삭제) 작업을 지원 합니다.

* **시계열 모델 형식**: Time Series Insights 형식을 사용 하 여 계산을 수행 하는 변수나 수식을 정의할 수 있습니다. 지정 된 Time Series Insights 인스턴스와 연결 됩니다. 형식에는 하나 이상의 변수가 포함될 수 있습니다.
* **시계열 모델 계층 구조**: 계층 구조는 데이터의 체계적인 조직입니다. 계층 구조는 Time Series Insights 데이터에 있는 서로 다른 엔티티 간의 관계를 나타냅니다.
* **시계열 모델 인스턴스**: 인스턴스는 시계열 자체입니다. 대부분의 경우에는 해당 환경에서 자산의 고유 식별자 인 **DeviceID** 또는 **AssetID**입니다.

시계열 모델에 대한 자세한 내용은 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.

## <a name="preview-well"></a>미리 보기 웰

는 선택한 Time Series Insights 인스턴스와 연결 된 인스턴스 필드 및 기타 메타 데이터를 표시 합니다. 오른쪽에 있는 확인란을 선택 하 여 현재 차트의 특정 인스턴스를 숨기 거 나 표시할 수 있습니다. 요소의 왼쪽에 있는 빨간색 **삭제** (휴지통) 컨트롤을 선택 하 여 현재 데이터에서 특정 데이터 요소를 제거할 수도 있습니다.

  [![미리 보기 웰](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

**분석** 차트 페이지의 레이아웃을 다시 구성 하려면 오른쪽 위 모퉁이에 있는 줄임표 아이콘을 선택 합니다.

  [![원격 분석 레이아웃 옵션](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> 다음 메시지가 표시 되 면 선택 된 시간 범위 동안 인스턴스에 데이터가 없는 것입니다. 이 문제를 해결 하려면 시간 범위를 늘리거나 인스턴스가 데이터를 푸시하는 지 확인 합니다.
>
> ![데이터 알림 없음](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>차트 미리 보기

차트를 사용 하 여 Time Series Insights 인스턴스를 선으로 표시할 수 있습니다. 차트의 크기를 더 크게 설정 하려면 웹 컨트롤을 선택 하 여 환경 패널, 데이터 모델 및 시간 범위 제어판을 축소할 수 있습니다.

  [![차트 미리 보기 개요](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **선택한 날짜 범위**: 시각화에 사용할 수 있는 데이터 요소를 제어합니다.

- **내부 날짜 범위 슬라이더 도구**: 두 끝점 컨트롤을 원하는 시간 범위 위로 끌어서 두 컨트롤을 사용 합니다.

- **시간 범위 축소 컨트롤**: 시간 범위 패널 편집기를 접거나 펼칩니다.

- **Y축 형식 컨트롤**: 사용 가능한 y 축 뷰 옵션을 순환 합니다.

    * `Default`: 각 줄에는 개별 y 축이 있습니다.
    * `Stacked`: 이를 사용 하 여 동일한 y 축에서 여러 줄을 스택에 정렬 하 고 y 축 데이터는 선택한 선에 따라 변경 합니다.
    * `Shared`: 모든 y 축 데이터를 함께 표시 합니다.

- **현재 데이터 요소**: 현재 선택한 데이터 요소 및 관련된 세부 정보입니다.

특정 데이터 조각으로 더 자세히 살펴보려면 현재 그래프의 데이터 요소를 마우스 왼쪽 단추로 클릭 한 다음 선택한 영역을 원하는 끝점으로 끕니다. 다음 그림에 표시 된 것 처럼 선택한 회색 영역을 마우스 오른쪽 단추로 클릭 하 고 **확대/축소**를 선택 합니다.

  [![차트 확대/축소 미리 보기](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

**확대/축소** 작업을 수행 하면 선택한 데이터 집합이 표시 됩니다. Y 축 형식 컨트롤을 선택 하 여 Time Series Insights 데이터의 y 축 표현 3 개를 순환 합니다.

  [![차트 y 축 미리 보기](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

여기에서 공유 Y 축의 예를 볼 수 있습니다.

  [![공유 Y 축 미리 보기](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>시간 편집기 패널

Time Series Insights 미리 보기를 사용하는 경우 먼저 시간 범위를 선택합니다. 선택한 시간 범위는 Time Series Insights 미리 보기 위젯을 조작 하는 데 사용할 수 있는 데이터 집합을 제어 합니다. 다음 웹 컨트롤은 작업 시간 범위를 선택 하기 위해 Time Series Insights 미리 보기에서 사용할 수 있습니다.

  [![시간 선택 패널](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **내부 날짜 범위 슬라이더 도구**: 두 끝점 컨트롤을 원하는 시간 범위 위로 끌어서 두 컨트롤을 사용 합니다. 이 내부 날짜 범위는 외부 날짜 범위 슬라이더 컨트롤에 의해 제한 됩니다.

1. **날짜 범위 증가 및 감소 단추**: 원하는 간격의 단추를 선택하여 시간 범위를 늘리거나 줄입니다.

1. **시간 범위 축소 컨트롤**: 이 웹 컨트롤을 사용 하면 내부 날짜 범위 슬라이더 도구를 제외한 모든 컨트롤을 숨길 수 있습니다.

1. **외부 날짜 범위 슬라이더 컨트롤**: 끝점 컨트롤을 사용 하 여 내부 날짜 범위 컨트롤에 사용할 수 있는 외부 날짜 범위를 선택 합니다.

1. **빠른 시간 날짜-범위 드롭다운 목록**: 이 도구를 사용 하 여 최근 **30 분**, **지난 12 시간**또는 **사용자 지정 범위**와 같은 미리 설정 된 시간 범위 선택을 빠르게 전환할 수 있습니다. 이 값을 변경하면 내부 크기 슬라이더 도구에서 설명하는 사용 가능한 간격 범위도 변경됩니다.

1. **간격 크기 슬라이더 도구**: 이를 사용 하 여 동일한 시간 범위에서 간격을 확대 및 축소할 수 있습니다. 이 작업을 통해 큰 시간 조각 간의 이동을 보다 정확하게 제어할 수 있습니다. 여기에는 1 밀리초 만큼 작은 조각으로 부드러운 추세가 표시 됩니다. 이를 사용 하 여 데이터의 세부적인 고해상도 컷을 확인할 수 있습니다. 슬라이더의 기본 시작점은 선택한 데이터를 최적으로 볼 수 있는 보기로 해상도, 쿼리 속도, 세분성이 균형을 이루도록 설정됩니다.

1. **웹 컨트롤에 대 한 날짜 범위**: 이 웹 컨트롤을 사용 하면 원하는 날짜 및 시간 범위를 쉽게 선택할 수 있습니다. 컨트롤을 사용하여 표준 시간대 간에 전환할 수도 있습니다. 현재 작업 영역에 변경 내용을 적용 한 후 **저장**을 선택 합니다.

   [![선택 패널로/에서](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>탐색 패널

Time Series Insights Preview 탐색 패널이 Time Series Insights 앱의 맨 위에 나타납니다. 다음 기능을 제공 합니다.

### <a name="current-session-share-link-control"></a>현재 세션 공유 링크 컨트롤

  [![공유 아이콘](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

새 **공유** 아이콘을 선택 하 여 팀과 URL 링크를 공유 합니다.

  [![인스턴스 URL 공유](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>테넌트 섹션

  [![테 넌 트 선택](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* 현재 Time Series Insights 로그인 계정 정보를 표시 합니다.
* 사용 가능한 Time Series Insights 테마 사이를 전환 하는 데 사용 합니다.
* 이를 통해 Preview [demo 웹 앱](https://insights.timeseries.azure.com/preview/demo)을 볼 수 있습니다.

### <a name="theme-selection"></a>테마 선택

새 테마를 선택 하려면 오른쪽 위 모퉁이에 있는 프로필 아이콘을 선택 합니다. 그런 다음 **테마 변경**을 선택 합니다.

  [![테마 선택](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> 프로필 아이콘을 선택 하 여 언어를 선택할 수도 있습니다.

Azure Time Series Insights 미리 보기는 다음 두 가지 테마를 지원합니다.

* **밝은 테마**: 이 문서 전체에 표시된 기본 테마입니다.
* **어두운 테마**: 다음과 같이 탐색기를 렌더링합니다.

  [![선택한 짙은 테마](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 환경 컨트롤

### <a name="preview-terms-panel"></a>미리 보기 약관 패널

이 섹션은 업데이트된 UI에서 탐색기를 사용하려는 기존 S1/S2 환경에만 적용됩니다. 일반적으로 사용 가능한 제품 및 미리 보기를 함께 사용 하는 것이 좋습니다. 기존 U의 일부 기능을 업데이트된 탐색기에 추가했지만, 기존 Time Series Insights 탐색기에서 S1/S2 환경에 대한 전체 UI 환경을 이용할 수 있습니다. 

계층 구조 대신 환경에서 쿼리를 정의 하는 Time Series Insights 용어 창이 표시 됩니다. 조건자를 기준으로 데이터를 필터링 하는 데 사용 합니다.

  [![Where 쿼리 패널](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Time Series Insights 미리 보기 용어 편집기 패널은 다음 매개 변수를 사용합니다.

**Where**: Where 절을 사용 하 여 다음 표에 나열 된 피연산자 집합을 사용 하 여 이벤트를 신속 하 게 필터링 합니다. 피연산자를 선택하여 검색을 수행하는 경우 조건자는 해당 검색에 따라 자동으로 업데이트됩니다. 지원되는 연산자는 다음과 같습니다.

| 연산 | 지원되는 형식   | 참고 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | 모든 연산자는 같은 형식이거나 NULL 상수여야 합니다. |
| `HAS` | String | 오른쪽에는 상수 문자열 리터럴만 사용할 수 있습니다. 빈 문자열 및 NULL은 허용 되지 않습니다. |

지원 되는 쿼리 작업 및 데이터 형식에 대 한 자세한 내용은 [TSX (Time Series Expression)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)를 참조 하세요.

### <a name="examples-of-where-clauses"></a>Where 절의 예

  [![Where 절 예](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**측정값**: 현재 차트의 요소로 사용할 수 있는 모든 숫자 열 (**double**)을 표시 하는 드롭다운 목록입니다.

**분할 기준**: 이 드롭다운 목록에는 데이터를 그룹화 할 수 있는 사용 가능한 모든 범주 열 (문자열)이 모델에 표시 됩니다. 동일한 x 축에 대해 볼 수 있는 최대 5 개의 용어를 추가할 수 있습니다. 원하는 매개 변수를 입력 한 다음 **추가** 를 선택 하 여 새 용어를 추가 합니다.

  [![쿼리 및 필터링 된 뷰 1](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

다음 그림에 표시 된 것 처럼 표시 되는 아이콘을 선택 하 여 차트 패널에서 요소를 표시 하거나 숨길 수 있습니다. 쿼리를 완전히 제거 하려면 빨간색 **X**를 선택 합니다.

  [![쿼리 및 필터링 된 뷰 2](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>다음 단계

- Azure Time Series Insights 미리 보기에서 [저장소 및 수신](./time-series-insights-update-storage-ingress.md) 에 대해 알아봅니다.
- [데이터 모델링](./time-series-insights-update-tsm.md)에 대 한 Time Series Insights 미리 보기 문서를 읽습니다.
- Time Series Insights 인스턴스를 [진단 하 고 문제를 해결 하는 방법을](./time-series-insights-update-how-to-troubleshoot.md) 알아봅니다.
