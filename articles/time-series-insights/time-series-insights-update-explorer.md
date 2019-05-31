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
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399870"
---
# <a name="visualize-data-in-the-explorer-preview"></a>탐색기 미리 보기에서 데이터 시각화

이 문서에서는 사용자 인터페이스 및 사용자 환경 기능은 Azure Time Series Insights 미리 보기의 인터페이스를 설명 [데모 웹 응용 프로그램](https://insights.timeseries.azure.com/preview/demo)합니다. 특히 호스 티 드 샘플과 인터페이스 사용자 지정 옵션을 제공 된 데모를 통해 탐색의 레이아웃에 설명 합니다.

## <a name="prerequisites"></a>필수 조건

Azure Time Series Insights 미리 보기 탐색기를 시작 하려면 다음을 수행 해야 합니다.

* Time Series Insights 환경을 설정합니다. 인스턴스를 프로 비전 하는 방법에 대 한 자세한 내용은 다음을 시도 합니다 [Azure Time Series Insights 미리 보기](./time-series-insights-update-create-environment.md) 자습서입니다.
* [데이터 액세스를 제공](./time-series-insights-data-access.md) 계정에 대해 만든 Time Series Insights 환경에 있습니다. 자신과 다른 사용자에 대한 액세스 권한을 모두 부여할 수 있습니다.
* 환경에 데이터 푸시를 Time Series Insights 환경에 이벤트 원본을 추가 합니다.
  * 에 대해 알아봅니다 [event hub에 연결 하는 방법을](./time-series-insights-how-to-add-an-event-source-eventhub.md)합니다.
  * 에 대해 알아봅니다 [IoT hub에 연결 하는 방법을](./time-series-insights-how-to-add-an-event-source-iothub.md)합니다.

## <a name="learn-about-the-preview-explorer"></a>미리 보기 탐색기에 알아봅니다

Azure Time Series Insights 미리 보기 탐색기는 다음 요소로 구성되어 있습니다.

[![탐색기 보기](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">환경 패널</a>: Azure Time Series Insights 환경에 표시 됩니다.
- <a href="#navigation-menu">탐색 메뉴</a>: 전환 하는 데 사용할 합니다 **분석** 하 고 **모델** 페이지입니다.
- <a href="#hierarchy-tree">계층 구조 트리</a>: 이 사용 하 여를 차트로 표시할 특정 모델 및 데이터 요소를 선택 합니다.
- <a href="#preview-well">시계열</a>: 현재 선택된 된 데이터 요소 색 구분 된 테이블 형식으로 표시 됩니다.
- <a href="#preview-chart">차트 패널</a>: 현재 작업 중인 차트를 표시합니다.
- <a href="#time-editor-panel">타임라인</a>: 사용자 작업 기간을 수정 하려면 사용 합니다.
- <a href="#navigation-panel">앱 바</a>: 현재 테 넌 트와 같은 사용자 관리 옵션을 포함합니다. 테마 및 언어 설정을 변경 하 고 사용할 수 있습니다.

## <a name="environment-drop-down-list"></a>환경 드롭다운 목록

환경 드롭다운 목록에 액세스할 수 있는 모든 Time Series Insights 환경을 표시 합니다. Time Series Insights 미리 보기와 같은 종 량 제 환경 목록에 포함 됩니다. 목록에는 일반적으로 사용할 수 있는 S1/S2 환경도 포함 됩니다.

1. 표시 된 환경의 옆의 드롭다운 화살표를 선택 합니다.

   [![제어판](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. 환경을 선택 합니다.

## <a name="navigation-menu"></a>탐색 메뉴

  [![탐색 메뉴](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

탐색 메뉴를 사용 하 여 두 뷰 간에 선택 하려면:

* **분석**: 차트 및 모델 또는 모델링 되지 않은 시계열 데이터에서 다양 한 분석을 수행 하는 데 사용할.
* **모델**: Time Series Insights 모델과 새로운 Time Series Insights 미리 보기 형식, 계층 및 인스턴스를 푸시 하려면 사용 합니다.

## <a name="hierarchy-tree"></a>계층 구조 트리

계층 트리에서 모델, 특정 장치 및 장치에서 센서를 포함 하는 선택한 데이터 요소를 표시 합니다.

### <a name="model-search-panel"></a>모델 검색 패널

모델 검색 패널을 쉽게 검색 하 고 차트에 표시 하려는 특정 시간 시리즈 인스턴스를 찾으려면 시계열 모델 계층 탐색에 사용할 수 있습니다. 인스턴스를 선택 하면 추가 데이터 및 현재 차트 모두에 합니다.

  [![모델 검색 패널](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>모델 제작

전체 Azure Time Series Insights 미리 보기 지원 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업에 시계열 모델에 있습니다.

* **시계열 모델 형식**: Time Series Insights 형식 변수 또는 계산을 수행 하는 것에 대 한 수식을 정의 하려면 사용할 수 있습니다. 지정 된 Time Series Insights 인스턴스와 연결 됩니다. 형식에는 하나 이상의 변수가 포함될 수 있습니다.
* **시계열 모델 계층 구조**: 계층 구조는 데이터의 체계적인 조직입니다. 계층 구조는 Time Series Insights 데이터에 있는 서로 다른 엔티티 간의 관계를 나타냅니다.
* **시계열 모델 인스턴스**: 인스턴스는 시계열 자체입니다. 있을 때 대부분의 경우에 **DeviceID** 또는 **AssetID**, 환경에서 자산의 고유 식별자 인 합니다.

시계열 모델에 대한 자세한 내용은 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.

## <a name="preview-well"></a>잘 미리 보기

웰은 인스턴스 필드 및 선택한 Time Series Insights 인스턴스와 연결 된 다른 메타 데이터를 표시 합니다. 오른쪽에 있는 확인란을 선택 하면 숨길 수도 있고 현재 차트에서 특정 인스턴스를 표시할 수 있습니다. 빨간색을 선택 하 여 잘 현재 데이터에서 특정 데이터 요소를 제거할 수도 있습니다 **삭제** (휴지통) 요소의 왼쪽에는 컨트롤입니다.

  [![잘 미리 보기](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

레이아웃을 다시 구성 하 여 **분석** 페이지 차트, 오른쪽 위 모서리에서 줄임표 아이콘을 선택 합니다.

  [![원격 분석 레이아웃 옵션](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> 다음과 같은 메시지가 나타나면 인스턴스 선택한 기간 동안 데이터가 없습니다. 문제를 해결 하려면 시간 범위를 늘리거나 인스턴스 데이터를 푸시하는 확인 합니다.
>
> ![데이터 알림 없음](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>미리 보기 차트

차트를 사용 하 여 선으로 Time Series Insights 인스턴스를 표시할 수 있습니다. 더 큰 차트를 설정 하는 웹 컨트롤을 선택 하 여 환경 패널, 데이터 모델 및 시간 범위 컨트롤 패널을 축소할 수 있습니다.

  [![미리 보기 차트 개요](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **날짜 범위 선택**: 시각화에 사용할 수 있는 데이터 요소를 제어합니다.

- **내부 날짜 범위 슬라이더 도구**: 원하는 기간 동안 끌어서 두 끝점 컨트롤을 사용 합니다.

- **시간 범위 축소 컨트롤**: 시간 범위 패널 편집기를 접거나 펼칩니다.

- **Y축 형식 컨트롤**: 사용할 수 있는 y 축 보기 옵션 돌아가면서 선택 합니다.

    * `Default`: 각 줄에는 개별 y 축을 있습니다.
    * `Stacked`: 사용 하 여 해당 줄을 선택에 따라 변경 되는 y 축 데이터를 사용한 동일한 y 축에 여러 줄 스택.
    * `Shared`: 모든 y 축 데이터를 함께 표시 합니다.

- **현재 데이터 요소**: 현재 선택한 데이터 요소 및 관련된 세부 정보입니다.

검색할 추가 특정 데이터 조각으로 현재 그래프에서 데이터 요소를 마우스 왼쪽 단추로 클릭 하 고 끌어서 선택된 영역 원하는 끝점에 있습니다. 선택한 회색 영역을 마우스 오른쪽 단추로 누르고 **확대/축소**이 다음 이미지에 나와 있는 것 처럼:

  [![미리 보기 차트 확대/축소](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

수행 하 고 나면 합니다 **확대/축소** 설정 하 여 선택한 데이터 표시 작업을 합니다. Time Series Insights 데이터의 세 y 축 표현을 순환 하려면 y 축 서식 컨트롤을 선택 합니다.

  [![미리 보기 차트 y 축](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

여기에서 Y 축 공유의 예를 확인할 수 있습니다.

  [![미리 보기에는 Y 축을 공유](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>시간 편집기 패널

Time Series Insights 미리 보기를 사용하는 경우 먼저 시간 범위를 선택합니다. Time Series Insights 미리 보기 위젯 사용 하는 조작에 사용할 수 있는 데이터 집합을 제어 하는 선택한 시간 범위입니다. 다음 웹 컨트롤은 사용자 작업 기간을 선택 하기 위한 Time Series Insights 미리 보기에서 사용할 수 있습니다.

  [![시간 선택 패널](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **내부 날짜 범위 슬라이더 도구**: 원하는 기간 동안 끌어서 두 끝점 컨트롤을 사용 합니다. 이 내부 날짜 범위는 외부 날짜 범위 슬라이더 컨트롤에 의해 제한 됩니다.

1. **날짜 범위 증가 및 감소 단추**: 원하는 간격의 단추를 선택하여 시간 범위를 늘리거나 줄입니다.

1. **시간 범위 축소 컨트롤**: 이 웹 컨트롤을 통해 내부 날짜 범위 슬라이더 도구를 제외 하 고 모든 컨트롤을 숨길 수 있습니다.

1. **외부 날짜 범위 슬라이더 컨트롤**: 끝점 제어를 사용 하 여 내부 날짜 범위 컨트롤에 사용할 수 있는 외부 날짜 범위를 선택 합니다.

1. **빠른 시간 날짜 범위 드롭 다운 목록**: 마지막 등의 미리 설정 된 시간 범위 선택 항목을 신속 하 게 전환 하는 데 사용할 **30 분**의 **지난 12 시간 동안**, 또는 **사용자 지정 범위**합니다. 이 값을 변경하면 내부 크기 슬라이더 도구에서 설명하는 사용 가능한 간격 범위도 변경됩니다.

1. **간격 크기 슬라이더 도구**: 동일한 기간 동안 간격 확대/축소를 사용 합니다. 이 작업을 통해 큰 시간 조각 간의 이동을 보다 정확하게 제어할 수 있습니다. 부드러운 추세 밀리초 작게 표시 됩니다. 세분화 된, 고해상도 긴 데이터를 사용할 수 있습니다. 슬라이더의 기본 시작점은 선택한 데이터를 최적으로 볼 수 있는 보기로 해상도, 쿼리 속도, 세분성이 균형을 이루도록 설정됩니다.

1. **날짜 범위를에서 웹 컨트롤**: 이 웹 컨트롤을 사용 하 여 원하는 날짜 및 시간 범위를 쉽게 선택할 수 있습니다. 컨트롤을 사용하여 표준 시간대 간에 전환할 수도 있습니다. 현재 작업 영역에 적용할 변경 하면, 선택 **저장할**합니다.

   [![선택 패널에서](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>탐색 패널

Time Series Insights 앱의 맨 위에 있는 Time Series Insights 미리 보기 탐색 패널에 표시 됩니다. 다음과 같은 기능을 제공합니다.

### <a name="current-session-share-link-control"></a>현재 세션 공유 링크 컨트롤

  [![공유 아이콘](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

새 선택 **공유** URL 링크를 팀과 공유 하는 아이콘입니다.

  [![공유 인스턴스 URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>테넌트 섹션

  [![테 넌 트 선택](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* 현재 Time Series Insights에 로그인 계정 정보를 표시합니다.
* 사용 가능한 Time Series Insights 테마 간에 전환 하려면 사용 합니다.
* 미리 보기를 볼 [데모 웹 응용 프로그램](https://insights.timeseries.azure.com/preview/demo)합니다.

### <a name="theme-selection"></a>테마 선택

새 테마를 선택 하려면 오른쪽 위 모서리에 있는 프로필 아이콘을 선택 합니다. 그런 다음 선택 **테마 변경**합니다.

  [![테마 선택](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> 언어 선택 프로필 아이콘을 선택 하 여 사용할 수도 있습니다.

Azure Time Series Insights 미리 보기는 다음 두 가지 테마를 지원합니다.

* **밝은 테마**: 이 문서 전체에 표시된 기본 테마입니다.
* **어두운 테마**: 다음과 같이 탐색기를 렌더링합니다.

  [![어두운 테마를 선택합니다.](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 환경 컨트롤

### <a name="preview-terms-panel"></a>미리 보기 기간 패널

이 섹션은 업데이트된 UI에서 탐색기를 사용하려는 기존 S1/S2 환경에만 적용됩니다. 일반적으로 사용할 수 있는 제품 및 미리 보기를 사용 하 여 함께에서 좋습니다. 기존 U의 일부 기능을 업데이트된 탐색기에 추가했지만, 기존 Time Series Insights 탐색기에서 S1/S2 환경에 대한 전체 UI 환경을 이용할 수 있습니다. 

계층 대신 사용자 환경에서 쿼리를 정의 하면 Time Series Insights 용어 패널에 표시 됩니다. 조건자에 따라 데이터를 필터링 하는 데 사용할.

  [![패널 위치 쿼리](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Time Series Insights 미리 보기 용어 편집기 패널은 다음 매개 변수를 사용합니다.

**Where**: Where를 사용 하 여 다음 표에 나열 된 절을 신속 하 게 피연산자의 집합을 사용 하 여 이벤트를 필터링 합니다. 피연산자를 선택하여 검색을 수행하는 경우 조건자는 해당 검색에 따라 자동으로 업데이트됩니다. 지원되는 연산자는 다음과 같습니다.

| 작업(Operation) | 지원되는 형식   | 메모 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | 모든 연산자는 같은 형식이거나 NULL 상수여야 합니다. |
| `HAS` | String | 오른쪽에 있는 상수 문자열 리터럴만 허용 됩니다. 빈 문자열과 NULL은 허용 되지 않습니다. |

지원 되는 쿼리 작업 및 데이터 형식에 대 한 자세한 내용은 참조 하세요 [시간 시리즈 식 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)합니다.

### <a name="examples-of-where-clauses"></a>예가 where 절

  [![여기서 절 예제](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**측정값**: 모든 숫자 열을 표시 하는 드롭다운 목록 (**Double**) 현재 차트의 요소로 사용할 수 있습니다.

**분할 기준**: 이 드롭다운 목록에서 데이터를 그룹화 할 수 있는 모델에는 사용 가능한 모든 범주 열 (문자열)을 표시 합니다. 동일한 x 축에 표시할 최대 5 개의 기간을 추가할 수 있습니다. 선택한 다음 선택 하면 매개 변수를 입력 **추가** 새 용어를 추가 합니다.

  [![하나는 쿼리 및 필터링 된 보기](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

표시 하 고 다음 그림에 나와 있는 것 처럼 표시 되는 아이콘을 선택 하 여 차트 패널의 요소를 숨길 수 있습니다. 쿼리를 완전히 제거 하려면 빨간색 선택 **X**합니다.

  [![쿼리 및 필터링 된 보기 2](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [저장소 및 수신](./time-series-insights-update-storage-ingress.md) Azure Time Series Insights 미리 보기에 있습니다.
- Time Series Insights 미리 보기 문서를 참조 [데이터 모델링](./time-series-insights-update-tsm.md)합니다.
- 에 대해 알아봅니다 [진단 및 문제 해결 방법](./time-series-insights-update-how-to-troubleshoot.md) Time Series Insights 인스턴스.
