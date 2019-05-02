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
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: d348592589f448dab9b8b4f3a1a3eb286d464417
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723459"
---
# <a name="visualize-data-in-the-explorer-preview"></a>탐색기 미리 보기에서 데이터 시각화

이 문서에서는 Azure Time Series Insights 미리 보기 [탐색기 웹앱](https://insights.timeseries.azure.com/preview/samples)에서 사용할 수 있는 기능 및 옵션에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

Azure Time Series Insights 미리 보기 탐색기를 사용하려면 먼저 다음을 수행해야 합니다.

* Time Series Insights 환경을 설정합니다. 자세한 내용은 [자습서: Azure Time Series Insights 미리 보기](./time-series-insights-update-create-environment.md)를 참조하세요.
* 계정에 대해 만든 Time Series Insights 환경에 데이터 액세스 권한을 부여합니다. 자신과 다른 사용자에 대한 액세스 권한을 모두 부여할 수 있습니다.
* Time Series Insights 환경에 이벤트 원본을 추가하여 데이터를 환경으로 푸시합니다.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Azure Time Series Insights 미리 보기 탐색기에 대한 자세한 정보

  ![explorer-one][1]

Azure Time Series Insights 미리 보기 탐색기는 다음 요소로 구성되어 있습니다.

* **탐색 모음**: 분석과 모델 페이지 간에 전환할 수 있습니다.
* **계층 구조 트리**: 차트로 작성할 특정 데이터 요소를 선택할 수 있습니다.
* **시계열**: 현재 선택한 데이터 요소를 표시합니다.
* **차트 패널**: 현재 작업 중인 차트를 표시합니다.
* **타임라인**: 작업 시간 범위를 수정할 수 있습니다.
* **앱 바**: 현재 테넌트와 같은 사용자 관리 옵션을 포함하며 테마 및 언어 설정을 변경할 수 있습니다.

## <a name="time-series-insights-preview-environment-panel"></a>Time Series Insights 미리 보기 환경 패널

환경 패널에는 액세스 권한이 있는 모든 Time Series Insights 환경이 표시됩니다. 목록에는 종량제 환경 미리 보기와 S1/S2 환경(GA)이 포함되어 있습니다. 사용하려는 Time Series Insights 환경을 클릭하기만 하면 됩니다.

  ![explorer-two][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Time Series Insights 미리 보기 탐색 메뉴

  ![explorer-three][3]

탐색 메뉴를 사용하여 Time Series Insights 앱 간에 전환할 수 있습니다.

* **분석**: 모델링되거나 모델링되지 않은 시계열 데이터의 차트를 작성하고 다양한 분석을 수행할 수 있습니다.

* **모델**: 새로운 Time Series Insights 유형, 계층 구조 및 인스턴스를 Time Series Insights 모델로 푸시할 수 있습니다.

## <a name="time-series-insights-preview-model-authoring"></a>Time Series Insights 미리 보기 모델 작성

이 앱을 사용하면 시계열 모델에 대해 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행할 수 있습니다.  

* **시계열 모델 형식**: Time Series Insights 형식을 사용하면 계산을 수행하기 위한 변수 또는 수식을 정의할 수 있습니다. 지정된 Time Series Insights 인스턴스와 연결됩니다. 형식에는 하나 이상의 변수가 포함될 수 있습니다.
* **시계열 모델 계층 구조**: 계층 구조는 데이터의 체계적인 조직입니다. 계층 구조는 Time Series Insights 데이터에 있는 서로 다른 엔티티 간의 관계를 나타냅니다.
* **시계열 모델 인스턴스**: 인스턴스는 시계열 자체입니다. 대부분의 경우 환경에 있는 자산의 고유 식별자인 DeviceID 또는 AssetID입니다.

시계열 모델에 대한 자세한 내용은 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.

## <a name="time-series-insights-preview-model-search-panel"></a>Time Series Insights 모델 검색 패널

모델 검색 패널을 사용하면 시계열 모델 계층 구조를 쉽게 검색하고 탐색하여 차트에 표시할 특정 시계열 인스턴스를 찾을 수 있습니다. 인스턴스를 선택하면 현재 차트와 데이터에 모두 추가됩니다.

  ![explorer-four][4]

## <a name="time-series-insights-preview-well"></a>Time Series Insights 미리 보기

선택한 시계열 인스턴스와 연결된 인스턴스 필드 및 기타 메타데이터를 표시합니다. 오른쪽의 확인란을 사용하여 현재 차트에서 특정 인스턴스를 숨기거나 표시할 수 있습니다. 요소의 오른쪽에 있는 빨간색 x 컨트롤을 클릭하여 현재 데이터에서 특정 데이터 요소를 제거할 수도 있습니다.

  ![explorer-five][5]

원격 분석 패널을 팝아웃하여 데이터에 있는 요소의 더 나은 세로 보기를 얻을 수 있습니다.

  ![explorer-six][6]

> [!NOTE]
> 다음 메시지가 표시되면 선택한 시간 범위 동안 인스턴스에 데이터가 없는 것입니다. 이 문제를 해결하려면 시간 범위를 늘리거나 인스턴스가 데이터를 푸시하는지 확인합니다.
>
> ![explorer-seven][7]

## <a name="time-series-insights-preview-chart"></a>Time Series Insights 미리 보기 차트

차트를 사용하여 시계열 인스턴스를 선으로 표시할 수 있습니다. 웹 컨트롤을 클릭해서 환경 패널, 데이터 모델 및 시간 범위 제어 패널을 접으면 차트를 확장할 수 있습니다.

  ![explorer-eight][8]

1. **선택한 날짜 범위**: 시각화에 사용할 수 있는 데이터 요소를 제어합니다.

1. **내부 날짜 범위 슬라이더 도구**: 원하는 시간 범위 위로 끌어 두 개의 엔드포인트 컨트롤을 사용합니다.

1. **시간 범위 축소 컨트롤**: 시간 범위 패널 편집기를 접거나 펼칩니다.

1. **Y축 형식 컨트롤**: 사용 가능한 Y축 보기 옵션을 순환합니다.

    * `Default`: 각 선마다 개별 Y축이 있습니다.
    * `Stacked`: 동일한 Y축에 여러 개의 선을 누적할 수 있으며, 선택한 선을 기준으로 Y축 데이터가 변경됩니다.
    * `Shared`: 모든 Y축 데이터가 함께 표시됩니다.

1. **현재 데이터 요소**: 현재 선택한 데이터 요소 및 관련된 세부 정보입니다.

현재 그래프에서 데이터 요소를 마우스 왼쪽 단추로 클릭한 다음, 선택한 영역을 선택한 엔드포인트로 끌어 특정 데이터 조각을 드릴할 수 있습니다. 회색으로 표시되는 선택한 영역을 마우스 오른쪽 단추로 클릭하고 다음 이미지에 표시된 확대/축소를 클릭합니다.

  ![explorer-nine][9]

확대/축소 작업을 수행하면 선택한 데이터 세트가 표시됩니다. Y축 형식 컨트롤을 클릭하여 Time Series Insights 데이터의 Y축 표시 세 개를 순환합니다.

  ![explorer-ten][10]

여기서는 공유 Y축의 예를 확인할 수 있습니다.

  ![explorer-eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Time Series Insights 미리 보기 시간 편집기 패널

Time Series Insights 미리 보기를 사용하는 경우 먼저 시간 범위를 선택합니다. 선택한 시간 범위는 Time Series Insights 미리 보기 위젯을 사용하여 조작할 수 있는 데이터 세트를 제어합니다. 다음 웹 컨트롤은 Time Series Insights 미리 보기에서 작업 시간 범위를 선택하는 데 사용할 수 있습니다.

  ![explorer-twelve][12]

1. **내부 날짜 범위 슬라이더 도구**: 원하는 시간 범위 위로 끌어 두 개의 엔드포인트 컨트롤을 사용합니다. 이 내부 날짜 범위는 외부 날짜 범위 슬라이더 컨트롤에 의해 제한됩니다.

1. **날짜 범위 증가 및 감소 단추**: 원하는 간격의 단추를 선택하여 시간 범위를 늘리거나 줄입니다.

1. **시간 범위 축소 컨트롤**: 이 웹 컨트롤을 사용하면 내부 날짜 범위 슬라이더 도구를 제외한 모든 컨트롤을 숨길 수 있습니다.

1. **외부 날짜 범위 슬라이더 컨트롤**: 엔드포인트 컨트롤을 사용하여 내부 날짜 범위 제어에 사용할 수 있는 외부 날짜 범위를 선택할 수 있습니다.

1. **빠른 시간 날짜 범위 드롭다운**: 지난 30분, 지난 12시간 또는 사용자 지정 범위와 같은 사전 설정된 시간 범위 선택 간에 빠르게 전환할 수 있습니다. 이 값을 변경하면 내부 크기 슬라이더 도구에서 설명하는 사용 가능한 간격 범위도 변경됩니다.

1. **간격 크기 슬라이더 도구**: 동일한 시간 범위에 대해 간격을 확대하고 축소할 수 있습니다. 이 작업을 통해 큰 시간 조각 간의 이동을 보다 정확하게 제어할 수 있습니다. 또한 밀리초만큼 작은 조각까지 부드러운 추세를 표시하므로 세부적인 데이터를 자세히 살펴볼 수 있습니다. 슬라이더의 기본 시작점은 선택한 데이터를 최적으로 볼 수 있는 보기로 해상도, 쿼리 속도, 세분성이 균형을 이루도록 설정됩니다.

1. **날짜 범위 종료 및 시작 웹 컨트롤**: 이 웹 컨트롤을 사용하여 원하는 날짜 및 시간 범위를 쉽게 클릭하고 선택할 수 있습니다. 컨트롤을 사용하여 표준 시간대 간에 전환할 수도 있습니다. 변경한 후 현재 작업 영역에 적용하려면 **저장**을 선택합니다.

   ![explorer-thirteen][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Time Series Insights 미리 보기 탐색 패널

Time Series Insights 미리 보기 탐색 패널은 다음 기능을 제공합니다.

  ![explorer-fourteen][14]

### <a name="current-session-share-link-control"></a>현재 세션 공유 링크 컨트롤

  ![explorer-fifteen][15]

링크 웹 컨트롤(강조 표시됨)을 선택하여 다음을 포함하는 현재 Azure Time Series Insights 작업 세션을 저장하거나 공유할 URL을 생성합니다.

* 현재 선택한 시간 범위
* 현재 선택한 간격 크기
* 현재 선택한 데이터

### <a name="tenant-section"></a>테넌트 섹션

  ![explorer-sixteen][16]

* 현재 Time Series Insights 로그인 계정 정보를 표시합니다.
* 사용 가능한 Time Series Insights 테마 간에 전환할 수 있습니다.

### <a name="theme-selection"></a>테마 선택

Azure Time Series Insights 미리 보기는 다음 두 가지 테마를 지원합니다.

* **밝은 테마**: 이 문서 전체에 표시된 기본 테마입니다.
* **어두운 테마**:  다음과 같이 탐색기를 렌더링합니다.

  ![explorer-seventeen][17]

여기서 지원되는 언어 간에 변경할 수도 있습니다.

## <a name="s1s2-environment-controls"></a>S1/S2 환경 컨트롤

### <a name="time-series-insights-preview-terms-panel"></a>Time Series Insights 미리 보기 용어 패널

이 섹션은 업데이트된 UI에서 탐색기를 사용하려는 기존 S1/S2 환경에만 적용됩니다. GA 제품과 미리 보기를 조합해서 사용하는 것이 좋습니다. 기존 U의 일부 기능을 업데이트된 탐색기에 추가했지만, 기존 Time Series Insights 탐색기에서 S1/S2 환경에 대한 전체 UI 환경을 이용할 수 있습니다.  

계층 구조 대신, 사용자 환경에서 쿼리를 정의하는 데 사용되는 Time Series Insights 용어 패널이 표시됩니다. 조건자를 기준으로 데이터를 필터링할 수 있습니다.

  ![explorer-eighteen][18]

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

  ![explorer-nineteen][19]

**측정값**: 이 드롭다운에는 현재 차트의 요소로 사용할 수 있는 모든 숫자 열(**Doubles**)이 표시됩니다.

**분할 기준**: 이 드롭다운에는 데이터를 그룹화하는 데 사용할 수 있는, 모델에서 사용 가능한 모든 범주 열(문자열)이 표시됩니다. 하나의 X축에서 최대 5개의 기간을 더할 수 있습니다. 원하는 매개 변수를 입력하고 **추가**를 선택하여 새 용어를 추가합니다.

  ![explorer-twenty][20]

다음 이미지에 나와 있는 표시 아이콘을 선택하여 차트 패널에서 요소를 표시하고 숨길 수 있습니다. 빨간색 **x**를 클릭하여 쿼리를 완전히 제거할 수 있습니다.

  ![explorer-twenty-one][21]

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [저장소 및 수신](./time-series-insights-update-storage-ingress.md) Azure Time Series Insights 미리 보기에 있습니다.

- Time Series Insights 미리 보기 문서를 참조 [데이터 모델링](./time-series-insights-update-tsm.md)합니다.

- 에 대해 알아봅니다 [진단 및 문제 해결 방법](./time-series-insights-update-how-to-troubleshoot.md) Time Series Insights 인스턴스.

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
