---
title: '자습서: Log Analytics 쿼리 시작'
description: 이 자습서에서는 Azure Portal에서 Log Analytics를 사용하여 Azure Monitor 로그 쿼리를 작성하고 관리하는 방법을 알아봅니다.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: b337065f125d12e309dd1f7fcc56c2af72b1c28c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088360"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>자습서: Log Analytics 쿼리 시작

이 자습서에서는 Log Analytics를 사용하여 Azure Portal에서 Azure Monitor 로그 쿼리를 작성, 실행 및 관리하는 방법을 보여 줍니다. Log Analytics 쿼리를 사용하여 용어를 검색하고, 추세를 파악하며, 패턴을 분석하고, 데이터에서 다른 많은 인사이트를 제공할 수 있습니다. 

이 자습서에서는 Log Analytics를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 로그 데이터 스키마 이해
> * 간단한 쿼리 작성 및 실행, 쿼리 시간 범위 수정
> * 쿼리 결과 필터링, 정렬 및 그룹화
> * 쿼리 결과의 시각적 개체 보기, 수정 및 공유
> * 쿼리와 결과의 저장, 로드, 내보내기 및 복사

로그 쿼리에 대한 자세한 내용은 [Azure Monitor의 로그 쿼리 개요](log-query-overview.md)를 참조하세요.<br/>
로그 쿼리를 작성하는 방법에 대한 자세한 자습서는 [Azure Monitor에서 로그 쿼리 시작](get-started-queries.md)을 참조하세요.

## <a name="open-log-analytics"></a>Log Analytics 열기
Log Analytics를 사용하려면 Azure 계정에 로그인해야 합니다. Azure 계정이 없는 경우 [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

이 자습서의 단계 대부분을 완료하려면 많은 샘플 데이터가 있는 [이 데모 환경](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)을 사용할 수 있습니다. 데모 환경에서는 쿼리를 저장하거나 결과를 대시보드에 고정할 수 없습니다.

또한 Azure Monitor를 사용하여 하나 이상의 Azure 리소스에 대한 로그 데이터를 수집하는 경우 사용자 고유의 환경을 사용할 수도 있습니다. Log Analytics 작업 영역을 열려면 Azure Monitor의 왼쪽 탐색 영역에서 **로그**를 선택합니다. 

## <a name="understand-the-schema"></a>스키마 이해
 
*스키마*는 논리 범주에 따라 그룹화된 테이블의 컬렉션입니다. Demo 스키마에는 모니터링 솔루션의 몇 가지 범주가 있습니다. 예를 들어 **LogManagement** 범주에는 Windows 및 Syslog 이벤트, 성능 데이터 및 에이전트 하트비트가 포함됩니다.

스키마 테이블은 Log Analytics 작업 영역의 **테이블** 탭에 표시됩니다. 테이블에는 각각 열 이름 옆에 아이콘으로 표시된 데이터 형식이 있는 열이 포함되어 있습니다. 예를 들어 **Event** 테이블에는 **Computer**와 같은 텍스트 열과 **EventCategory**와 같은 숫자 열이 포함되어 있습니다.

![스크린샷은 새 쿼리가 있는 Azure Portal 로그 페이지를 표시하고, 컴퓨터 및 EventCategory가 강조 표시된 테이블 창을 강조 표시합니다.](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>기본 쿼리 작성 및 실행

Log Analytics는 **쿼리 편집기**에서 비어 있는 새 쿼리로 열립니다.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>쿼리 작성

Azure Monitor 로그 쿼리는 Kusto 쿼리 언어의 버전을 사용합니다. 쿼리는 테이블 이름 또는 [search](/azure/kusto/query/searchoperator) 명령을 사용하여 시작할 수 있습니다. 

다음 쿼리는 **Event** 테이블에서 모든 레코드를 검색합니다.

```Kusto
Event
```

파이프(|) 문자는 명령을 구분하므로 첫 번째 명령의 출력은 다음 명령의 입력입니다. 단일 쿼리에 원하는 수의 명령을 추가할 수 있습니다. 다음 쿼리는 **Event** 테이블에서 레코드를 검색한 다음, 모든 속성에서 **error** 용어를 검색합니다.

```Kusto
Event 
| search "error"
```

한 줄 바꿈을 통해 쿼리를 더 쉽게 읽을 수 있습니다. 둘 이상의 줄 바꿈은 쿼리를 별도의 쿼리로 분할합니다.

동일한 쿼리를 작성하는 또 다른 방법은 다음과 같습니다.

```Kusto
search in (Event) "error"
```

두 번째 예제에서 **search** 명령은 **Event** 테이블의 레코드에서만 **error** 용어를 검색합니다.

Log Analytics는 기본적으로 쿼리를 지난 24시간의 시간 범위로 제한합니다. 다른 시간 범위를 설정하려면 명시적 **TimeGenerated** 필터를 쿼리에 추가하거나 **시간 범위** 컨트롤을 사용할 수 있습니다.

### <a name="use-the-time-range-control"></a>시간 범위 컨트롤 사용
**시간 범위** 컨트롤을 사용하려면 맨 위 표시줄에서 선택한 다음, 드롭다운 목록에서 값을 선택하거나 **사용자 지정**을 선택하여 사용자 지정 시간 범위를 만듭니다.

![시간 선택기](media/get-started-portal/time-picker.png)

- 시간 범위 값은 UTC 형식이며, 현지 표준 시간대와 다를 수 있습니다.
- 쿼리에서 **TimeGenerated** 필터를 명시적으로 설정하는 경우 시간 선택기 컨트롤에서 **쿼리 설정**을 표시하며 충돌을 방지하기 위해 비활성화되어 있습니다.

### <a name="run-a-query"></a>쿼리 실행
쿼리를 실행하려면 쿼리 내의 어느 위치에 커서를 놓고 위쪽 표시줄에서 **실행**을 선택하거나 **Shift**+**Enter**를 누릅니다. 빈 줄을 찾을 때까지 쿼리가 실행됩니다.

## <a name="filter-results"></a>결과 필터링
Log Analytics는 결과를 최대 10,000개의 레코드로 제한합니다. `Event`와 같은 일반 쿼리는 너무 많은 결과를 반환하여 유용하지 않습니다. 쿼리에서 테이블 요소를 제한하거나 필터를 결과에 명시적으로 추가하여 쿼리 결과를 필터링할 수 있습니다. 테이블 요소를 필터링하면 새 결과 세트가 반환되지만 명시적 필터는 기존 결과 세트에 적용됩니다.

### <a name="filter-by-restricting-table-elements"></a>테이블 요소를 제한하여 필터링
쿼리에서 테이블 요소를 제한하여 `Event` 쿼리 결과를 **Error** 이벤트로 필터링하려면 다음을 수행합니다.

1. 쿼리 결과에서 **Error**가 **EventLevelName** 열에 있는 레코드 옆의 드롭다운 화살표를 선택합니다. 
   
1. 확장된 세부 정보에서 마우스로 **EventLevelName** 옆에 있는 **...** 위를 가리키고, 선택한 다음, **"Error" 포함**을 선택합니다. 
   
   ![쿼리에 필터 추가](media/get-started-portal/add-filter.png)
   
1. **쿼리 편집기**의 쿼리가 이제 다음과 같이 변경되었습니다.
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. **실행**을 선택하여 새 쿼리를 실행합니다.

### <a name="filter-by-explicitly-filtering-results"></a>명시적으로 결과를 필터링하여 필터링
쿼리 결과를 필터링하여 `Event` 쿼리 결과를 **Error** 이벤트로 필터링하려면 다음을 수행합니다.

1. 쿼리 결과에서 **EventLevelName** 열 제목 옆의 **필터** 아이콘을 선택합니다. 
   
1. 팝업 창의 첫 번째 필드에서 **같음**을 선택하고, 다음 필드에서 *error*를 입력합니다. 
   
1. **필터**를 선택합니다.
   
   ![스크린샷은 EventLevelName을 기준으로 결과를 필터링하는 컨텍스트 메뉴가 있는 결과 테이블을 표시합니다.](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>열 정렬, 그룹화 및 선택
**TimeGenerated [UTC]** 와 같은 특정 열을 기준으로 쿼리 결과를 정렬하려면 열 제목을 선택합니다. 제목을 다시 선택하여 오름차순과 내림차순 간에 전환합니다.

![열 정렬](media/get-started-portal/sort-column.png)

결과를 구성하는 다른 방법은 그룹화하는 것입니다. 특정 열을 기준으로 결과를 그룹화하려면 열 제목을 **해당 열별로 그룹화하려면 열 헤더를 여기에 끌어다 놓으세요.** 라는 레이블이 지정된 결과 테이블의 위쪽 표시줄로 끕니다. 하위 그룹을 만들려면 다른 열을 위쪽 표시줄로 끕니다. 이 표시줄에서 그룹 및 하위 그룹의 계층 구조와 정렬을 다시 정렬할 수 있습니다.

![스크린샷은 EventLevelName 및 컴퓨터에 대한 하위 그룹을 포함하는 쿼리 결과를 표시합니다.](media/get-started-portal/groups.png)

결과에서 열을 숨기거나 표시하려면 테이블 위에서 **열**을 선택한 다음, 드롭다운 목록에서 원하는 열을 선택하거나 선택 취소합니다.

![열 선택](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>차트 보기 및 수정
쿼리 결과를 시각적 형식으로 볼 수도 있습니다. 예를 들어 다음 쿼리를 입력합니다.

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

결과는 기본적으로 테이블에 표시됩니다. 테이블 위에서 **차트**를 선택하여 결과를 그래픽 보기로 표시합니다.

![가로 막대형 차트](media/get-started-portal/bar-chart.png)

결과가 누적 가로 막대형 차트로 표시됩니다. 다른 결과 보기를 표시하려면 **누적 세로 막대형** 또는 **원형**과 같은 다른 옵션을 선택합니다.

![원형 차트](media/get-started-portal/pie-chart.png)

컨트롤 막대에서 x축 및 y축과 같은 보기의 속성 또는 그룹화 및 분할 기본 설정을 수동으로 변경할 수 있습니다.

[render](/azure/kusto/query/renderoperator) 연산자를 사용하여 쿼리 자체에서 기본 보기를 설정할 수도 있습니다.

## <a name="pin-results-to-a-dashboard"></a>대시보드에 결과 고정

Log Analytics에서 결과 테이블 또는 차트를 공유 Azure 대시보드에 고정하려면 위쪽 표시줄에서 **대시보드에 고정**을 선택합니다. 

![대시보드에 고정](media/get-started-portal/pin-dashboard.png)

**다른 대시보드에 고정** 창에서 고정할 공유 대시보드를 선택하거나 만들고, **적용**을 선택합니다. 테이블 또는 차트가 선택한 Azure 대시보드에 표시됩니다.

![대시보드에 고정된 차트](media/get-started-portal/pin-dashboard2.png)

공유 대시보드에 고정하는 테이블 또는 차트는 다음과 같이 간소화됩니다. 

- 데이터가 지난 14일로 제한됩니다.
- 최대 4개의 열 및 상위 7개의 행만 테이블에 표시됩니다.
- 불연속 범주가 많은 차트는 덜 채워진 범주를 단일 **기타** bin으로 자동으로 그룹화합니다.

## <a name="save-load-or-export-queries"></a>쿼리 저장, 로드 또는 내보내기

쿼리가 만들어지면 쿼리 또는 결과를 저장하거나 다른 사용자와 공유할 수 있습니다. 

### <a name="save-queries"></a>쿼리 저장

쿼리를 저장하려면 다음을 수행합니다.

1. 위쪽 표시줄에서**저장**을 선택합니다.
   
1. **저장** 대화 상자에서 a–z, A–Z, 0-9, 공백, 하이픈, 밑줄, 마침표, 괄호 또는 파이프 문자를 사용하여 쿼리 **이름**을 지정합니다. 
   
1. 쿼리를 **쿼리** 또는 **함수**로 저장할지 여부를 선택합니다. 함수는 다른 쿼리에서 참조할 수 있는 쿼리입니다. 
   
   쿼리를 함수로 저장하려면 다른 쿼리에서 이 쿼리를 호출하는 데 사용할 짧은 이름인 **함수 별칭**을 제공합니다.
   
1. Log Analytics 작업 영역에 있는 경우 쿼리에 사용할 **쿼리 탐색기** **범주**를 제공합니다 (Applications Insights 쿼리에는 범주를 사용할 수 없음).
   
1. **저장**을 선택합니다.
   
   ![함수 저장](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>쿼리 로드
저장된 쿼리를 로드하려면 오른쪽 위에서 **쿼리 탐색기**를 선택합니다. **쿼리 탐색기** 창이 열리고 모든 쿼리가 범주별로 나열됩니다. 범주를 펼치거나 검색 창에서 쿼리 이름을 입력한 다음, 쿼리를 선택하여 **쿼리 편집기**에 로드합니다. 쿼리 이름 옆의 별표를 선택하여 쿼리를 **즐겨찾기**로 표시할 수 있습니다.

![쿼리 탐색기](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>쿼리 내보내기 및 공유
쿼리를 내보내려면 위쪽 표시줄에서 **내보내기**를 선택한 다음, 드롭다운 목록에서 **CSV로 내보내기 - 모든 열**, **CSV로 내보내기 - 표시된 열** 또는 **Power BI(M Query)로 내보내기**를 선택합니다.

다음 비디오에서는 Log Analytics와 Excel을 통합하는 방법을 보여 줍니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Asme]

쿼리에 대한 링크를 공유하려면 위쪽 표시줄에서 **링크 복사**를 선택한 다음, **쿼리에 링크 복사**, **쿼리 텍스트 복사** 또는 **쿼리 결과 복사**를 선택하여 클립보드에 복사합니다. 쿼리 링크는 동일한 작업 영역에 액세스할 수 있는 다른 사용자에게 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Monitor 로그 쿼리를 작성하는 방법에 대해 자세히 알아보려면 다음 자습서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [Azure Monitor 로그 쿼리 작성](get-started-queries.md)
