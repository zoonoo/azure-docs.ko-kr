---
title: Azure Monitor Log Analytics 시작 | Microsoft Docs
description: 이 문서에서는 Azure Portal에서 Log Analytics를 사용하여 쿼리를 작성하는 방법에 대한 자습서를 제공합니다.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: bwren
ms.openlocfilehash: af01ebdc72df096b45c4ca4e755b2ed3880bab65
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255274"
---
# <a name="get-started-with-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics 시작

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

이 자습서에서는 Azure portal에서 Azure Monitor Log Analytics를 사용 하 여 Azure Monitor 로그 쿼리를 작성 하는 방법을 배웁니다. 다음을 수행하는 방법에 대해 알아봅니다.

- 단순 쿼리 작성
- 데이터의 스키마 이해
- 결과의 필터, 정렬 및 그룹화
- 시간 범위 적용
- 차트 만들기
- 쿼리 저장 및 로드
- 쿼리 내보내기 및 공유


## <a name="meet-log-analytics"></a>Log Analytics를 충족 합니다.
Log Analytics는 웹 도구 작성 및 Azure Monitor 로그 쿼리를 실행 하는 데 사용 합니다. Azure Monitor 메뉴에서 **로그**를 클릭하여 Log Analytics를 엽니다. 비어 있는 새 쿼리로 시작합니다.

![홈 페이지](media/get-started-portal/homepage.png)



## <a name="basic-queries"></a>기본 쿼리
쿼리는 용어를 검색하고, 추세를 파악하고, 패턴을 분석하며 데이터에 따라 다른 많은 정보를 제공하는 데 사용할 수 있습니다. 기본 쿼리를 시작합니다.

```Kusto
Event | search "error"
```

이 쿼리는 속성에 “error”라는 용어를 포함하는 레코드에 대한 _이벤트_ 테이블을 검색합니다.

쿼리는 테이블 이름 또는 **search** 명령을 사용하여 시작할 수 있습니다. 위의 예제는 쿼리의 범위를 정의하는 테이블 이름 _Event_로 시작합니다. 파이프 (|) 문자 다음 명령의 입력으로 사용 되는 첫 번째 출력 하므로 명령을 구분 합니다. 단일 쿼리에 원하는 수의 명령을 추가할 수 있습니다.

동일한 쿼리를 작성하는 또 다른 방법은 다음과 같습니다.

```Kusto
search in (Event) "error"
```

이 예제에서 **search**의 범위는 _Event_ 테이블이며, 해당 테이블의 모든 레코드는 “error”라는 용어에 대해 검색됩니다.

## <a name="running-a-query"></a>쿼리 실행
**실행** 단추를 클릭하거나 **Shift + Enter** 키를 눌러 쿼리를 실행합니다. 실행되는 코드와 반환되는 데이터를 결정하는 다음 세부 정보를 고려합니다.

- 줄 바꿈: 단일 줄 바꿈은 쿼리를 더 명확하게 만듭니다. 여러 줄 바꿈은 쿼리를 별도의 쿼리로 분할합니다.
- 커서: 실행하도록 쿼리 내 어딘가에 커서를 놓습니다. 현재 쿼리는 빈 줄을 찾을 때까지 코드 업으로 간주됩니다.
- 시간 범위 - _지난 24시간_의 시간 범위가 기본적으로 설정됩니다. 다른 범위를 사용하려면 시간 선택기를 사용하거나 명시적 시간 범위 필터를 쿼리에 추가합니다.


## <a name="understand-the-schema"></a>스키마 이해
스키마는 논리 범주에서 시각적으로 그룹화된 테이블의 컬렉션입니다. 다양한 범주는 모니터링 솔루션에서 가져온 것입니다. _LogManagement_ 범주는 Windows 및 Syslog 이벤트, 성능 데이터 및 클라이언트 하트비트와 같은 일반적인 데이터를 포함합니다.

![스키마](media/get-started-portal/schema.png)

각 테이블에서 데이터는 열 이름 옆의 아이콘으로 표시된 대로 각각 다른 데이터 형식을 사용하는 열로 구성되어 있습니다. 예를 들어 스크린샷에 표시된 _Event_ 테이블에는 텍스트인 _Computer_, 숫자인 _EventCategory_ 및 날짜/시간인 _TimeGenerated_와 같은 열이 포함되어 있습니다.

## <a name="filter-the-results"></a>결과 필터링
모든 항목을 _Event_ 테이블로 가져와서 시작합니다.

```Kusto
Event
```

Log Analytics를 기준으로 결과 자동으로 범위:

- 시간 범위:  기본적으로 쿼리는 지난 24시간으로 제한됩니다.
- 결과 수: 결과는 최대 10,000개 레코드로 제한됩니다.

이 쿼리는 매우 일반적이며 결과를 너무 많이 반환해서 사용할 수 없습니다. 테이블 요소를 통해 또는 명시적으로 쿼리에 필터를 추가하여 결과를 필터링할 수 있습니다. 테이블 요소를 통한 결과 필터링은 기존 결과 집합에 적용되는 반면, 쿼리 자체에 대한 필터는 새로 필터링된 결과 집합을 반환하므로 더 정확한 결과를 생성할 수 있습니다.

### <a name="add-a-filter-to-the-query"></a>쿼리에 필터 추가
각 레코드의 왼쪽에 화살표가 있습니다. 특정 레코드에 대한 세부 정보를 열려면 이 화살표를 클릭합니다.

열 이름 위를 마우스로 가리켜 “+” 및 “-” 아이콘을 표시합니다. 동일한 값을 가진 레코드만 반환하는 필터를 추가하려면 “+” 기호를 클릭합니다. 이 값을 가진 레코드를 제외하려면 “-”를 클릭한 다음, **실행**을 클릭하여 쿼리를 다시 실행합니다.

![쿼리에 필터 추가](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>테이블 요소를 통한 필터링
이제 _Error_의 심각도가 포함된 이벤트를 살펴보겠습니다. 이는 _EventLevelName_이라는 열에 지정됩니다. 이 열을 보려면 오른쪽으로 스크롤해야 합니다.

열 제목 옆에 있는 필터 아이콘을 클릭하고 팝업 창에서 텍스트 _error_를 _사용하여 시작_하는 값을 선택합니다.

![Filter](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>결과의 정렬 및 그룹화
이제 지난 24시간 동안 생성된 SQL Server의 오류 이벤트만 포함하도록 결과 범위가 축소되었습니다. 단, 결과는 어떤 방식으로든 정렬되지는 않습니다. 예를 들어 _타임 스탬프_와 같은 특정 열에 따라 결과를 정렬하려면 열 제목을 클릭합니다. 한 번 클릭하면 오름차순으로 정렬하고, 한 번 더 클릭하면 내림차순으로 정렬합니다.

![열 정렬](media/get-started-portal/sort-column.png)

결과를 구성하는 다른 방법은 그룹화하는 것입니다. 결과를 특정 열 기준으로 그룹화하려면 다른 열 위에 열 머리글을 끌어오기만 하면 됩니다. 하위 그룹을 만들려면 마찬가지로 다른 열을 끌어오면 됩니다.

![그룹](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>표시할 열 선택
결과 테이블에는 종종 많은 열이 포함됩니다. 반환된 열 중 일부는 기본적으로 표시되지 않거나 일부 표시되는 열을 제거하려는 경우가 있습니다. 표시할 열을 선택하려면 열 단추를 클릭합니다.

![열 선택](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>시간 범위 선택
기본적으로 Log Analytics를 적용 합니다 _지난 24 시간 동안_ 시간 범위입니다. 다른 범위를 사용하려면 시간 선택기를 통해 다른 값을 선택하고 **실행**을 클릭합니다. 미리 설정된 값 외에도 쿼리에 대한 절대 범위를 선택하는 _사용자 지정 시간 범위_ 옵션을 사용할 수 있습니다.

![시간 선택기](media/get-started-portal/time-picker.png)

사용자 지정 시간 범위를 선택할 때 선택한 값은 UTC로 표시되어 현지 표준 시간대와 다를 수 있습니다.

쿼리에 _TimeGenerated_에 대한 필터가 명시적으로 포함되는 경우 시간 선택기 제목에 _쿼리에서 설정_이 표시됩니다. 충돌을 방지하기 위해 수동 선택은 사용하지 않도록 설정됩니다.


## <a name="charts"></a>차트
결과를 테이블에 반환하는 것 외에, 쿼리 결과를 시각적 형식으로 표시할 수 있습니다. 한 예로 다음 쿼리를 사용합니다.

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

기본적으로 결과는 테이블에 표시됩니다. 결과를 그래픽 보기로 확인하려면 _차트_를 클릭합니다.

![가로 막대형 차트](media/get-started-portal/bar-chart.png)

결과가 누적 가로 막대형 차트로 표시됩니다. 결과의 다른 보기를 표시하려면 _누적 세로 막대형_을 클릭하고 _원형_을 선택합니다.

![원형 차트](media/get-started-portal/pie-chart.png)

x 및 y축과 같은 보기의 다른 속성이나 기본 설정의 그룹화 및 분할은 컨트롤 막대에서 수동으로 변경할 수 있습니다.

렌더링 연산자를 사용하여 쿼리 자체에서 선호하는 보기를 설정할 수도 있습니다.

### <a name="smart-diagnostics"></a>스마트 진단
시간 차트에서 데이터에 갑작스러운 스파이크 또는 단계가 나타날 경우 선에서 강조 표시된 점을 볼 수 있습니다. 이것은 _스마트 진단_이 급격한 변화를 필터링하는 속성 조합을 확인했다는 것을 나타냅니다. 해당 점을 클릭하여 필터에 대한 자세한 내용을 확인하고 필터링된 버전을 확인합니다. 이렇게 하면 변화의 원인을 파악할 수 있습니다.

![스마트 진단](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>대시보드에 고정
다이어그램 또는 테이블을 공유 Azure 대시보드 중 하나에 고정하려면 핀 아이콘을 클릭합니다.

![대시보드에 고정](media/get-started-portal/pin-dashboard.png)

대시보드에 고정할 때 특정 단순화가 차트에 적용됩니다.

- 테이블 열 및 행: 테이블을 대시보드에 고정하려면 4개 이하의 열이 있어야 합니다. 상위 7개의 행만 표시됩니다.
- 시간 제한: 쿼리가 지난 14일로 자동으로 제한됩니다.
- Bin 개수 제한: 불연속적인 Bin이 많은 차트를 표시할 경우 덜 채워진 Bin은 하나의 _기타_ Bin으로 자동으로 그룹화됩니다.

## <a name="save-queries"></a>쿼리 저장
유용한 쿼리를 만든 후 저장하거나 다른 사용자와 공유하고자 할 수도 있습니다. **저장** 아이콘은 위쪽 막대에 있습니다.

전체 쿼리 페이지 또는 단일 쿼리를 함수로 저장할 수 있습니다. 함수는 다른 쿼리에서 참조될 수 있는 쿼리입니다. 쿼리를 함수로 저장하려면 다른 쿼리에서 참조하는 경우 이 쿼리를 호출하는 데 사용되는 이름인 함수 별칭을 제공해야 합니다.

![함수 저장](media/get-started-portal/save-function.png)

Log Analytics 쿼리는 항상 선택한 작업 영역에 저장되고 해당 작업 영역의 다른 사용자와 공유됩니다.

## <a name="load-queries"></a>쿼리 로드
오른쪽 위 영역에 쿼리 탐색기 아이콘이 있습니다. 범주별로 모든 저장된 쿼리가 나열되어 있습니다. 또한 특정 쿼리를 나중에 빠르게 찾도록 즐겨찾기로 표시할 수 있습니다. 현재 창에 추가하려면 저장된 쿼리를 두 번 클릭합니다.

![쿼리 탐색기](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>링크로 내보내기 및 공유
Log Analytics에는 여러 내보내기 방법을 지원합니다.

- Excel: 결과를 CSV 파일로 저장합니다.
- Power BI: Power BI로 결과를 내보냅니다. 자세한 내용은 [Azure Monitor 로그 데이터를 Power BI로 가져오기](../../azure-monitor/platform/powerbi.md)를 참조하세요.
- 링크 공유: 쿼리 자체는 링크로 공유할 수 있습니다. 따라서 동일한 작업 영역에 액세스할 수 있는 다른 사용자에 의해 전송 및 실행될 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 로그 쿼리 작성](get-started-queries.md)에 대해 자세히 알아봅니다.
