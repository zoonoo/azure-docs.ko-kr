---
title: Azure Time Series Insights 미리 보기 탐색기에서 데이터 시각화 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights 미리 보기 탐색기 웹앱에서 사용할 수 있는 기능 및 옵션에 대해 설명합니다.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: 862465a7611f1a2bc65dbb0c49c4de512bd239de
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442091"
---
# <a name="visualize-data-in-the-explorer-preview"></a>탐색기 미리 보기에서 데이터 시각화

UI/UX 기능과 Azure Time Series Insights 미리 보기의 인터페이스에 설명 하는이 문서 [데모 웹 응용 프로그램](https://insights.timeseries.azure.com/preview/demo)합니다. 특히 호스 티 드 샘플과 인터페이스 사용자 지정 옵션을 제공 된 데모를 통해 탐색의 레이아웃에 설명 합니다.

## <a name="prerequisites"></a>필수 조건

Azure Time Series Insights 미리 보기 탐색기를 시작 하려면 다음을 수행 해야 합니다.

* Time Series Insights 환경을 설정합니다. 시도 인스턴스를 프로 비전에 대 한 자세한 내용을 보려면 우리의 [Azure Time Series Insights 미리 보기](./time-series-insights-update-create-environment.md) 자습서입니다.
* [데이터 액세스를 제공](./time-series-insights-data-access.md) 계정에 대해 만든 Time Series Insights 환경에 있습니다. 자신과 다른 사용자에 대한 액세스 권한을 모두 부여할 수 있습니다.
* 환경에 데이터 푸시를 Time Series Insights 환경에 이벤트 원본을 추가 합니다.
  * 에 대해 알아봅니다 [이벤트 허브에 연결 하는 방법](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * 에 대해 알아봅니다 [IoT hub에 연결 하는 방법](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="learn-about-the-preview-explorer"></a>미리 보기 탐색기에 알아봅니다

Azure Time Series Insights 미리 보기 탐색기는 다음 요소로 구성되어 있습니다.

[![탐색기 보기](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

1. <a href="#environment-dropdown">**환경 패널**</a>: Azure TSI 환경에 표시 됩니다.
1. <a href="#navigation-menu">**탐색 메뉴**</a>: 사이 전환할 수 있습니다 합니다 **분석** 하 고 **모델** 페이지입니다.
1. <a href="#hierarchy-tree">**계층 트리의**</a>: 특정 모델 및 데이터 요소 차트 화할 수를 선택할 수 있습니다.
1. <a href="#preview-well">**잘 시계열**</a>: 현재 선택된 된 데이터 요소 색 사용 하 여 테이블 형식으로 표시 됩니다.
1. <a href="#preview-chart">**차트 패널**</a>:  현재 작업 중인 차트를 표시합니다.
1. <a href="#time-editor-panel">**타임 라인**</a>:  작업 시간 범위를 수정할 수 있습니다.
1. <a href="#navigation-panel">**앱 바**</a>:  현재 테넌트와 같은 사용자 관리 옵션을 포함하며 테마 및 언어 설정을 변경할 수 있습니다.

## <a name="environment-dropdown"></a>환경 드롭다운

환경 드롭다운에 액세스할 수 있는 모든 Time Series Insights 환경을 표시 합니다. 종 량 제 환경 (미리 보기) 및 S1/S2 환경 (일반 공급 또는 GA) 목록에 포함 됩니다. 

1. 표시 된 환경의 옆에 있는 드롭다운 화살표를 클릭 하기만 하면 됩니다.

   [![제어판](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. 그런 다음 원하는 환경을 선택 합니다.

## <a name="navigation-menu"></a>탐색 메뉴

  [![탐색 메뉴](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

탐색 메뉴를 사용 하면 두 보기 중에서 선택할 수 있습니다.

* **분석**: 모델링되거나 모델링되지 않은 시계열 데이터의 차트를 작성하고 다양한 분석을 수행할 수 있습니다.
* **모델**: 새로운 Time Series Insights 유형, 계층 구조 및 인스턴스를 Time Series Insights 모델로 푸시할 수 있습니다.

## <a name="hierarchy-tree"></a>계층 구조 트리

계층 트리에서 모델, 특정 장치 및 장치에서 센서를 포함 하 여 선택한 데이터 요소를 표시 합니다.

### <a name="model-search-panel"></a>모델 검색 패널

모델 검색 패널을 사용하면 시계열 모델 계층 구조를 쉽게 검색하고 탐색하여 차트에 표시할 특정 시계열 인스턴스를 찾을 수 있습니다. 인스턴스를 선택하면 현재 차트와 데이터에 모두 추가됩니다.

  [![모델 검색 패널](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>모델 제작

Azure Time Series Insights 미리 보기는 시계열 모델에 대 한 모든 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업을 지원합니다.  

* **시계열 모델 형식**: Time Series Insights 형식을 사용하면 계산을 수행하기 위한 변수 또는 수식을 정의할 수 있습니다. 지정된 Time Series Insights 인스턴스와 연결됩니다. 형식에는 하나 이상의 변수가 포함될 수 있습니다.
* **시계열 모델 계층 구조**: 계층 구조는 데이터의 체계적인 조직입니다. 계층 구조는 Time Series Insights 데이터에 있는 서로 다른 엔티티 간의 관계를 나타냅니다.
* **시계열 모델 인스턴스**: 인스턴스는 시계열 자체입니다. 대부분의 경우에 **DeviceID** 또는 **AssetID**, 환경에서 자산의 고유 식별자 인 합니다.

시계열 모델에 대한 자세한 내용은 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.

## <a name="preview-well"></a>잘 미리 보기

웰은 인스턴스 필드 및 선택한 TSI 인스턴스와 연결 된 다른 메타 데이터를 표시 합니다. 오른쪽에 있는 확인란을 사용 하 여 숨기 거 나 특정 인스턴스에서 현재 차트를 표시할 수 있습니다. 또한 특정 데이터 요소에 현재 데이터에서 빨간색을 클릭 하 여 제거할 수 있습니다 **삭제** (휴지통) 컨트롤 요소의 왼쪽에 있습니다.

  [![잘 미리 보기](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

레이아웃도 다시 구성할 수 있습니다 하 **분석** 차트 페이지 오른쪽 상단에에서 있는 줄임표 아이콘을 선택 하 여:

  [![원격 분석 레이아웃 옵션](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> 다음 메시지가 표시되면 선택한 시간 범위 동안 인스턴스에 데이터가 없는 것입니다. 이 문제를 해결하려면 시간 범위를 늘리거나 인스턴스가 데이터를 푸시하는지 확인합니다.
>
> ![데이터 알림 없음](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>미리 보기 차트

차트를 사용 하 여 선으로 TSI 인스턴스를 표시할 수 있습니다. 웹 컨트롤을 클릭해서 환경 패널, 데이터 모델 및 시간 범위 제어 패널을 접으면 차트를 확장할 수 있습니다.

  [![미리 보기 차트 개요](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

1. **선택한 날짜 범위**: 시각화에 사용할 수 있는 데이터 요소를 제어합니다.

1. **내부 날짜 범위 슬라이더 도구**: 원하는 시간 범위 위로 끌어 두 개의 엔드포인트 컨트롤을 사용합니다.

1. **시간 범위 축소 컨트롤**: 시간 범위 패널 편집기를 접거나 펼칩니다.

1. **Y축 형식 컨트롤**: 사용 가능한 Y축 보기 옵션을 순환합니다.

    * `Default`: 각 선마다 개별 Y축이 있습니다.
    * `Stacked`: 동일한 Y축에 여러 개의 선을 누적할 수 있으며, 선택한 선을 기준으로 Y축 데이터가 변경됩니다.
    * `Shared`: 모든 Y축 데이터가 함께 표시됩니다.

1. **현재 데이터 요소**: 현재 선택한 데이터 요소 및 관련된 세부 정보입니다.

현재 그래프에서 데이터 요소를 마우스 왼쪽 단추로 클릭한 다음, 선택한 영역을 선택한 엔드포인트로 끌어 특정 데이터 조각을 드릴할 수 있습니다. 되지, 선택한 영역을 마우스 오른쪽 단추로 클릭 하 고 클릭 **확대/축소** 이 다음 그림과 같이:

  [![미리 보기 차트 확대/축소](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

수행 하 고 나면 합니다 **확대/축소** 작업을 선택한 데이터 집합이 표시 됩니다. Y축 형식 컨트롤을 클릭하여 Time Series Insights 데이터의 Y축 표시 세 개를 순환합니다.

  [![미리 보기 차트 y 축](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

여기서는 공유 Y축의 예를 확인할 수 있습니다.

  [![공유 축 미리 보기](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>시간 편집기 패널

Time Series Insights 미리 보기를 사용하는 경우 먼저 시간 범위를 선택합니다. 선택한 시간 범위는 Time Series Insights 미리 보기 위젯을 사용하여 조작할 수 있는 데이터 세트를 제어합니다. 다음 웹 컨트롤은 Time Series Insights 미리 보기에서 작업 시간 범위를 선택하는 데 사용할 수 있습니다.

  [![시간 선택 패널](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **내부 날짜 범위 슬라이더 도구**: 원하는 시간 범위 위로 끌어 두 개의 엔드포인트 컨트롤을 사용합니다. 이 내부 날짜 범위는 외부 날짜 범위 슬라이더 컨트롤에 의해 제한됩니다.

1. **날짜 범위 증가 및 감소 단추**: 원하는 간격의 단추를 선택하여 시간 범위를 늘리거나 줄입니다.

1. **시간 범위 축소 컨트롤**: 이 웹 컨트롤을 사용하면 내부 날짜 범위 슬라이더 도구를 제외한 모든 컨트롤을 숨길 수 있습니다.

1. **외부 날짜 범위 슬라이더 컨트롤**: 엔드포인트 컨트롤을 사용하여 내부 날짜 범위 제어에 사용할 수 있는 외부 날짜 범위를 선택할 수 있습니다.

1. **빠른 시간 날짜 범위 드롭다운**: 마지막 같은 미리 설정 된 시간 범위 선택 간을 빠르게 전환할 수 있습니다 **30 분**서 **지난 12 시간 동안**, 또는 **사용자 지정 범위**합니다. 이 값을 변경하면 내부 크기 슬라이더 도구에서 설명하는 사용 가능한 간격 범위도 변경됩니다.

1. **간격 크기 슬라이더 도구**: 동일한 시간 범위에 대해 간격을 확대하고 축소할 수 있습니다. 이 작업을 통해 큰 시간 조각 간의 이동을 보다 정확하게 제어할 수 있습니다. 또한 밀리초만큼 작은 조각까지 부드러운 추세를 표시하므로 세부적인 데이터를 자세히 살펴볼 수 있습니다. 슬라이더의 기본 시작점은 선택한 데이터를 최적으로 볼 수 있는 보기로 해상도, 쿼리 속도, 세분성이 균형을 이루도록 설정됩니다.

1. **날짜 범위 종료 및 시작 웹 컨트롤**: 이 웹 컨트롤을 사용 하 여 클릭 하 고 원하는 날짜 및 시간 범위 선택 쉽게 수 있습니다. 컨트롤을 사용하여 표준 시간대 간에 전환할 수도 있습니다. 변경한 후 현재 작업 영역에 적용하려면 **저장**을 선택합니다.

   [![선택 패널에서](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>탐색 패널

Time Series Insights 미리 보기 탐색 패널 TSI 앱의 맨 위에 나타납니다. 다음과 같은 기능을 제공합니다.

### <a name="current-session-share-link-control"></a>현재 세션 공유 링크 컨트롤

  [![공유 아이콘](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

새 선택 **공유** URL 링크를 팀과 공유 하는 아이콘입니다.

  [![공유 인스턴스 URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>테넌트 섹션

  [![테 넌 트 선택](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* 현재 Time Series Insights 로그인 계정 정보를 표시합니다.
* 사용 가능한 Time Series Insights 테마 간에 전환할 수 있습니다.
* 미리 보기를 볼 수 있습니다 [데모 웹 응용 프로그램](https://insights.timeseries.azure.com/preview/demo)합니다.

### <a name="theme-selection"></a>테마 선택

새 테마를 선택 하려면 오른쪽 위 모서리에 있는 프로필 아이콘을 클릭 합니다. 그런 다음 선택 **테마 변경**합니다.

  [![테마 선택](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> 언어 선택 프로필 아이콘을 클릭 하 여 사용할 수도 있습니다.

Azure Time Series Insights 미리 보기는 다음 두 가지 테마를 지원합니다.

* **밝은 테마**: 이 문서 전체에 표시된 기본 테마입니다.
* **어두운 테마**:  다음과 같이 탐색기를 렌더링합니다.

  [![어두운 테마를 선택합니다.](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 환경 컨트롤

### <a name="preview-terms-panel"></a>미리 보기 기간 패널

이 섹션은 업데이트된 UI에서 탐색기를 사용하려는 기존 S1/S2 환경에만 적용됩니다. GA 제품과 미리 보기를 조합해서 사용하는 것이 좋습니다. 기존 U의 일부 기능을 업데이트된 탐색기에 추가했지만, 기존 Time Series Insights 탐색기에서 S1/S2 환경에 대한 전체 UI 환경을 이용할 수 있습니다.  

계층 구조 대신, 사용자 환경에서 쿼리를 정의하는 데 사용되는 Time Series Insights 용어 패널이 표시됩니다. 조건자를 기준으로 데이터를 필터링할 수 있습니다.

  [![패널 위치 쿼리](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Time Series Insights 미리 보기 용어 편집기 패널은 다음 매개 변수를 사용합니다.

**Where**: where 절을 사용하면 다음 표에 나열된 피연산자 세트를 사용하여 이벤트를 빠르게 필터링할 수 있습니다. 피연산자를 선택하여 검색을 수행하는 경우 조건자는 해당 검색에 따라 자동으로 업데이트됩니다. 지원되는 연산자는 다음과 같습니다.

| 작업(Operation) | 지원되는 형식   | 메모 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | 모든 연산자는 같은 형식이거나 NULL 상수여야 합니다. |
| `HAS` | String | 우측 항에는 상수 문자열 리터럴만 사용할 수 있습니다. 빈 문자열과 NULL은 허용되지 않습니다. |

[TSX(시계열 식)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)를 읽어 지원되는 쿼리 작업 및 데이터 형식에 대해 자세히 알아봅니다.

### <a name="examples-of-where-clauses"></a>Where 절의 예

  [![여기서 절 예제](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**측정값**: 모든 숫자 열을 표시 하는 드롭다운 (**Double**) 현재 차트의 요소로 사용할 수 있습니다.

**분할 기준**: 이 드롭다운에는 데이터를 그룹화하는 데 사용할 수 있는, 모델에서 사용 가능한 모든 범주 열(문자열)이 표시됩니다. 하나의 X축에서 최대 5개의 기간을 더할 수 있습니다. 원하는 매개 변수를 입력하고 **추가**를 선택하여 새 용어를 추가합니다.

  [![하나는 쿼리 및 필터링 된 보기](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

다음 이미지에 나와 있는 표시 아이콘을 선택하여 차트 패널에서 요소를 표시하고 숨길 수 있습니다. 빨간색을 클릭 하 여 쿼리를 완전히 제거할 수 있습니다 **X**합니다.

  [![쿼리 및 필터링 된 보기 2](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [저장소 및 수신](./time-series-insights-update-storage-ingress.md) Azure Time Series Insights 미리 보기에 있습니다.

- Time Series Insights 미리 보기 문서를 참조 [데이터 모델링](./time-series-insights-update-tsm.md)합니다.

- 에 대해 알아봅니다 [진단 및 문제 해결 방법](./time-series-insights-update-how-to-troubleshoot.md) Time Series Insights 인스턴스.
