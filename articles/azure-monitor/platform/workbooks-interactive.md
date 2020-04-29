---
title: 사용자 지정 매개 변수를 사용 하 여 통합 문서 Azure Monitor
description: 미리 빌드되고 사용자 지정 매개 변수가 있는 통합 문서를 사용하여 복잡한 보고 간소화
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658271"
---
# <a name="interactive-workbooks"></a>대화형 Workbooks

통합 문서를 사용 하면 작성자가 소비자를 위한 대화형 보고서 및 환경을 만들 수 있습니다. 대화형 작업은 여러 가지 방법으로 지원 됩니다.

## <a name="parameter-changes"></a>매개 변수 변경
통합 문서 사용자가 매개 변수를 업데이트 하면 새 상태를 반영 하도록 매개 변수를 사용 하는 모든 컨트롤이 자동으로 새로 고쳐지고 다시 그려집니다. Azure Portal 보고서의 대부분은 대화형 작업을 지원 합니다. 통합 문서는 최소한의 사용자 노력으로 매우 간단한 방식으로이를 제공 합니다.

[통합 문서의 매개 변수에](workbooks-parameters.md) 대 한 자세한 정보

## <a name="grid-row-clicks"></a>표 형태의 행 클릭
작성자는 통합 문서를 사용 하 여 표의 행을 클릭 하면 행의 내용에 따라 후속 차트가 업데이트 되는 시나리오를 만들 수 있습니다. 

예를 들어, 사용자는 요청 목록 및 실패 횟수와 같은 일부 통계를 표시 하는 그리드를 사용할 수 있습니다. 요청에 해당 하는 행을 클릭 하면 해당 요청에 대해서만 필터링 하도록 업데이트 아래의 상세 차트가 생성 되도록 설정할 수 있습니다.

### <a name="setting-up-interactivity-on-grid-row-click"></a>표 형태 창의 행 클릭에 대 한 대화형 작업 설정
1. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. _쿼리 추가_ 링크를 사용 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다. 
3. _로그_, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석을 위한 KQL 입력
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`결과를 보려면
6. 쿼리 바닥글에서 _고급 설정_ 아이콘을 클릭 합니다 (아이콘은 기어 처럼 보임). 그러면 고급 설정 창이 열립니다. 
7. 설정을 확인 합니다.`When an item is selected, export a parameter`
    1. 내보낼 필드:`Request`
    2. 매개 변수 이름:`SelectedRequest`
    3. 기본값: `All requests`
    
    ![필드를 매개 변수로 내보내는 설정이 포함 된 고급 편집기를 보여 주는 이미지](./media/workbooks-interactive/advanced-settings.png)

8. `Done Editing`을 클릭합니다.
9. 2 단계와 3 단계를 사용 하 여 다른 쿼리 컨트롤을 추가 합니다.
10. 쿼리 편집기를 사용 하 여 분석을 위한 KQL 입력
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`결과를 확인 합니다.
12. _시각화_ 변경 대상`Area chart`
12. 첫 번째 표의 행을 클릭 합니다. 아래 영역 차트에서 선택한 요청을 필터링 하는 방법을 확인 합니다.

결과 보고서는 편집 모드에서 다음과 같이 표시 됩니다.

![표 형태 창의 행 클릭을 사용 하 여 대화형 환경 만들기를 보여 주는 이미지](./media/workbooks-interactive//grid-click-create.png)

아래 이미지는 동일한 원칙에 따라 읽기 모드에서 보다 정교한 대화형 보고서를 보여 줍니다. 보고서는 그리드 클릭을 사용 하 여 매개 변수를 내보냅니다 .이는 차례로 두 차트와 텍스트 블록에 사용 됩니다.

![표 형태 창의 행 클릭을 사용 하 여 대화형 환경 만들기를 보여 주는 이미지](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>전체 행의 내용 내보내기
경우에 따라 특정 열이 아니라 선택한 행의 전체 내용을 내보내는 것이 좋습니다. 이러한 경우 위의 7.1 단계에서 `Field to export` 속성을 설정 하지 않은 상태로 둡니다. 통합 문서는 전체 행 내용을 json으로 매개 변수에 내보냅니다. 

참조 KQL 컨트롤에서 `todynamic` 함수를 사용 하 여 json을 구문 분석 하 고 개별 열에 액세스 합니다.

 ## <a name="grid-cell-clicks"></a>표 형태의 셀 클릭
통합 문서를 사용 하면 작성자가 이라는 특정 형식의 그리드 열 렌더러를 통해 대화형 `link renderer`작업을 추가할 수 있습니다. 링크 렌더러는 표 형태 셀을 셀의 내용에 따라 하이퍼링크로 변환 합니다. 통합 문서는 리소스 개요 블레이드, 속성 모음 뷰어, 앱 Insights 검색, 사용, 트랜잭션 추적 등을 포함 하 여 다양 한 종류의 링크 렌더러를 지원 합니다.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>표 형태의 셀 클릭을 사용 하 여 대화형 작업 설정
1. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. _쿼리 추가_ 링크를 사용 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다. 
3. _로그_, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석을 위한 KQL 입력
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`결과를 보려면
6. _열 설정_ 을 클릭 하 여 설정 창을 엽니다.
7. _열_ 섹션에서 다음을 설정 합니다.
    1. _샘플_ 열 렌더러: `Link`, 보기 열기: `Cell Details`, 링크 레이블:`Sample`
    2. _개수_ 열 렌더러: `Bar`, 색상표: `Blue`, 최소 값:`0`
    3. _요청_ -열 렌더러:`Automatic`
    4. _저장 후 닫기_ 를 클릭 하 여 변경 내용을 적용 합니다.
8. 표에서 `Sample` 링크 중 하나를 클릭 합니다. 그러면 샘플링 된 요청에 대 한 세부 정보가 포함 된 속성 창이 열립니다.

    ![표 형태의 셀 클릭을 사용 하 여 대화형 환경 만들기를 보여 주는 이미지](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>링크 렌더러 작업
| 링크 작업 | 클릭에 대 한 작업 |
|:------------- |:-------------|
| `Generic Details` | 속성 그리드 컨텍스트 블레이드의 행 값을 표시 합니다. |
| `Cell Details` | 속성 그리드 컨텍스트 블레이드의 셀 값을 표시 합니다. 정보를 포함 하는 동적 형식 (예: 위치, 역할 인스턴스 등의 요청 속성이 있는 json)이 셀에 포함 된 경우에 유용 합니다. |
| `Cell Details` | 속성 그리드 컨텍스트 블레이드의 셀 값을 표시 합니다. 정보를 포함 하는 동적 형식 (예: 위치, 역할 인스턴스 등의 요청 속성이 있는 json)이 셀에 포함 된 경우에 유용 합니다. |
| `Custom Event Details` | 셀에서 사용자 지정 이벤트 ID (itemId)를 사용 하 여 Application Insights 검색 세부 정보를 엽니다. |
| `* Details` | 종속성, 예외, 페이지 보기, 요청 및 추적을 제외 하 고 사용자 지정 이벤트 세부 정보와 유사 합니다. |
| `Custom Event User Flows` | 셀의 사용자 지정 이벤트 이름에서 피벗 된 Application Insights 사용자 흐름 환경을 엽니다. |
| `* User Flows` | 예외, 페이지 보기 및 요청을 제외 하 고 사용자 지정 이벤트 사용자 흐름와 비슷합니다. |
| `User Timeline` | 셀에서 사용자 ID (user_Id)를 사용 하 여 사용자 시간 표시 막대를 엽니다. |
| `Session Timeline` | 셀 값에 대 한 Application Insights 검색 환경을 엽니다. 예를 들어 텍스트 ' abc '를 검색 합니다. 여기서 abc는 셀의 값입니다. |
| `Resource overview` | 셀의 리소스 ID 값을 기반으로 포털에서 리소스의 개요를 엽니다. |

## <a name="conditional-visibility"></a>조건부 표시 유형
사용자는 통합 문서를 사용 하 여 매개 변수의 값에 따라 특정 컨트롤을 표시 하거나 사라지게 할 수 있습니다. 이렇게 하면 작성자가 사용자 입력 또는 원격 분석 상태에 따라 보고서를 다르게 볼 수 있습니다. 예를 들어, 문제가 있는 경우에만 소비자에 대 한 요약을 표시 하는 것이 좋습니다.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>조건부 표시 유형을 사용 하 여 대화형 작업 설정
1. `Setting up interactivity on grid row click` 섹션의 단계에 따라 두 개의 대화형 컨트롤을 설정 합니다.
2. 맨 위에 새 매개 변수를 추가 합니다.
    1. 이름: `ShowDetails`
    2. 매개 변수 유형:`Drop down`
    3. 필수:`checked`
    4. 데이터 가져오기:`JSON`
    5. JSON 입력:`["Yes", "No"]`
    6. 저장을 수행 하 여 변경 내용을 커밋합니다.
3. 매개 변수 값을로 설정 합니다.`Yes`
4. 영역형 차트를 사용 하는 쿼리 컨트롤에서 _고급 설정_ 아이콘 (기어 아이콘)을 클릭 합니다.
5. 설정을 확인 합니다.`Make this item conditionally visible`
    1. 이 항목은 `ShowDetails` 매개 변수 값 `equals` 에 표시 됩니다.`Yes`
6. _편집 완료_ 를 클릭 하 여 변경 내용을 커밋합니다.
7. 통합 문서 도구 모음에서 _편집 완료_ 를 클릭 하 여 읽기 모드로 전환 합니다.
8. 매개 변수 `ShowDetails` 값을로 `No`전환 합니다. 아래 차트가 사라집니다.

아래 이미지에서는 `ShowDetails` 가 인 경우 표시 되는 경우를 보여 줍니다.`Yes`

![차트가 표시 되는 조건부 표시 유형을 보여 주는 이미지](./media/workbooks-interactive/conditional-visibility.png)

아래 이미지는 `ShowDetails` 가 인 숨겨진 경우를 보여줍니다.`No`

![차트가 숨겨진 조건부 표시 유형을 보여 주는 이미지](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>다음 단계


* 통합 문서에 대 한 자세한 내용은 다양 한 기능을 갖춘 시각화 옵션을 [시작](workbooks-visualizations.md) 하세요.
* 통합 문서 리소스에 대 한 액세스를 [제어](workbooks-access-control.md) 하 고 공유 합니다.
