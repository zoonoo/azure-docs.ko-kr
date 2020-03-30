---
title: 사용자 지정 매개 변수를 사용 하 고 Azure 모니터 통합 문서
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658271"
---
# <a name="interactive-workbooks"></a>대화형 Workbooks

통합 문서를 사용하면 작성자가 소비자를 위한 대화형 보고서 및 환경을 만들 수 있습니다. 상호 작용은 여러 가지 방법으로 지원됩니다.

## <a name="parameter-changes"></a>매개 변수 변경
통합 문서 사용자가 매개 변수를 업데이트하면 매개 변수를 사용하는 모든 컨트롤이 자동으로 새로 고쳐지고 다시 그려서 새 상태를 반영합니다. 대부분의 Azure 포털 보고서가 상호 작용을 지원하는 방법입니다. 통합 문서는 최소한의 사용자 노력으로 매우 간단 하 게 앞으로 방식으로 이것을 제공 합니다.

[통합 문서의 매개변수에](workbooks-parameters.md) 대해 자세히 알아보기

## <a name="grid-row-clicks"></a>그리드 행 클릭
통합 문서를 사용하면 작성자가 그리드에서 행을 클릭하면 행의 내용에 따라 후속 차트가 업데이트되는 시나리오를 생성할 수 있습니다. 

예를 들어 사용자는 요청 목록과 실패 횟수와 같은 일부 통계를 표시하는 그리드를 가질 수 있습니다. 요청에 해당하는 행을 클릭하면 아래의 상세 차트가 업데이트되어 해당 요청으로 필터링되도록 설정할 수 있습니다.

### <a name="setting-up-interactivity-on-grid-row-click"></a>그리드 행 클릭에 상호 작용 설정
1. 도구 모음 항목 _편집을_ 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 쿼리 _추가_ 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다. 
3. 쿼리 유형을 _로그,_ 리소스 유형(예: 응용 프로그램 인사이트)으로 선택하고 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석에 KQL 입력
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`결과를 보려면
6. 쿼리 바닥글에서 _고급 설정_ 아이콘을 클릭합니다(아이콘이 기어처럼 보입니다). 이렇게 하면 고급 설정 창이 열립니다. 
7. 설정을 확인합니다.`When an item is selected, export a parameter`
    1. 내보낼 필드:`Request`
    2. 매개 변수 이름:`SelectedRequest`
    3. 기본값: `All requests`
    
    ![필드를 매개 변수로 내보내기 위한 설정이 있는 고급 편집기 표시 이미지](./media/workbooks-interactive/advanced-settings.png)

8. `Done Editing`을 클릭합니다.
9. 2단계와 3단계를 사용하여 다른 쿼리 컨트롤을 추가합니다.
10. 쿼리 편집기를 사용하여 분석에 KQL 입력
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`을 사용하여 결과를 확인합니다.
12. _시각화를_ 로 변경`Area chart`
12. 첫 번째 그리드에서 행을 클릭합니다. 아래 지역도표가 선택한 요청으로 필터링하는 방법을 참고합니다.

결과 보고서는 편집 모드에서 다음과 같습니다.

![그리드 행 클릭을 사용하여 대화형 환경을 생성하는 이미지](./media/workbooks-interactive//grid-click-create.png)

아래 이미지는 동일한 원칙에 따라 읽기 모드에서 보다 정교한 대화형 보고서를 보여 주며 있습니다. 이 보고서는 그리드 클릭을 사용하여 매개변수를 내보내며, 이 매개변수는 두 개의 차트와 텍스트 블록에 사용됩니다.

![그리드 행 클릭을 사용하여 대화형 환경을 생성하는 이미지](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>전체 행의 내용 내보내기
때로는 특정 열 대신 선택한 행의 전체 내용을 내보내는 것이 바람직합니다. 이러한 경우 위의 `Field to export` 7.1단계에서 속성을 설정 해제상태로 둡니다. 통합 문서는 전체 행 내용을 매개 변수에 json으로 내보냅니다. 

참조 KQL 컨트롤에서 이 `todynamic` 함수를 사용하여 json을 구문 분석하고 개별 열에 액세스합니다.

 ## <a name="grid-cell-clicks"></a>그리드 셀 클릭
통합 문서를 사용하면 작성자가 을 라는 특수 유형의 그리드 `link renderer`열 렌더러를 통해 상호 작용을 추가할 수 있습니다. 링크 렌더러는 그리드 셀을 셀의 내용에 따라 하이퍼링크로 변환합니다. 통합 문서는 오픈 리소스 개요 블레이드, 속성 가방 뷰어, App Insights 검색, 사용, 트랜잭션 추적 등을 허용하는 링크 렌더러의 많은 종류를 지원합니다.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>그리드 셀 클릭을 사용하여 상호 작용 설정
1. 도구 모음 항목 _편집을_ 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 쿼리 _추가_ 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다. 
3. 쿼리 유형을 _로그,_ 리소스 유형(예: 응용 프로그램 인사이트)으로 선택하고 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석에 KQL 입력
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`결과를 보려면
6. _열 설정을_ 클릭하여 설정 창을 엽니다.
7. 열 섹션에서 다음을 _설정합니다._
    1. _샘플_ - 열 `Link`렌더러: `Cell Details`, 열 보기: [ 링크 레이블]`Sample`
    2. _개수_ - 열 `Bar`렌더러: `Blue`, 색상 팔레트: , 최소값:`0`
    3. _요청_ - 열 렌더러:`Automatic`
    4. 변경 내용 적용하려면 _저장 및 닫기를 클릭합니다._
8. 표의 `Sample` 링크 중 하나를 클릭합니다. 이렇게 하면 샘플링된 요청의 세부 정보가 있는 속성 창이 열립니다.

    ![그리드 셀 클릭을 사용하여 대화형 환경을 생성하는 이미지](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>링크 렌더러 작업
| 링크 작업 | 클릭 시 작업 |
|:------------- |:-------------|
| `Generic Details` | 속성 그리드 컨텍스트 블레이드에서 행 값을 표시합니다. |
| `Cell Details` | 속성 그리드 컨텍스트 블레이드에서 셀 값을 표시합니다. 셀에 정보가 포함된 동적 형식(예: 위치, 역할 인스턴스 등과 같은 요청 속성이 있는 json)이 포함된 경우에 유용합니다. |
| `Cell Details` | 속성 그리드 컨텍스트 블레이드에서 셀 값을 표시합니다. 셀에 정보가 포함된 동적 형식(예: 위치, 역할 인스턴스 등과 같은 요청 속성이 있는 json)이 포함된 경우에 유용합니다. |
| `Custom Event Details` | 셀의 사용자 지정 이벤트 ID(itemId)를 사용하여 응용 프로그램 인사이트 검색 세부 정보를 엽니다. |
| `* Details` | 종속성, 예외, 페이지 뷰, 요청 및 추적을 제외한 사용자 지정 이벤트 세부 정보와 유사합니다. |
| `Custom Event User Flows` | 셀의 사용자 지정 이벤트 이름에 피벗된 응용 프로그램 인사이트 사용자 흐름 환경 열기 |
| `* User Flows` | 예외, 페이지 뷰 및 요청을 제외한 사용자 지정 이벤트 사용자 흐름과 유사 |
| `User Timeline` | 셀의 사용자 ID(user_Id)가 있는 사용자 타임라인을 엽니다. |
| `Session Timeline` | 셀의 값에 대한 응용 프로그램 인사이트 검색 환경을 엽니다(예: abc가 셀의 값인 텍스트 'abc'를 검색). |
| `Resource overview` | 셀의 리소스 ID 값을 기반으로 포털에서 리소스 개요를 엽니다. |

## <a name="conditional-visibility"></a>조건부 가시성
통합 문서를 사용하면 매개 변수 값에 따라 특정 컨트롤이 표시되거나 사라지게 할 수 있습니다. 이렇게 하면 작성자가 사용자 입력 또는 원격 분석 상태에 따라 보고서를 다르게 표시할 수 있습니다. 예를 들어 소비자에게 상황이 좋을 때 요약만 표시하지만 문제가 있을 때 전체 세부 정보를 표시하는 것입니다.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>조건부 가시성을 사용하여 상호 작용 설정
1. `Setting up interactivity on grid row click` 섹션의 단계를 수행하여 두 개의 대화형 컨트롤을 설정합니다.
2. 맨 위에 새 매개변수 추가:
    1. 이름: `ShowDetails`
    2. 매개 변수 유형:`Drop down`
    3. 필수:`checked`
    4. 다음에서 데이터 가져옵니다.`JSON`
    5. JSON 입력:`["Yes", "No"]`
    6. 변경 내용을 커밋하기 위해 저장합니다.
3. 매개 변수 값을 다음값으로 설정`Yes`
4. 지역차트의 쿼리 컨트롤에서 고급 _설정_ 아이콘(기어 아이콘)을 클릭합니다.
5. 설정 확인`Make this item conditionally visible`
    1. 이 항목은 매개 `ShowDetails` 변수 `equals` 값이 표시 됩니다.`Yes`
6. 편집 _완료를_ 클릭하여 변경 내용을 커밋합니다.
7. 통합 문서 도구 모음에서 _편집 완료를_ 클릭하여 읽기 모드로 들어갑니다.
8. 매개 변수 `ShowDetails` 값을 `No`로 전환합니다. 아래 차트가 사라집니다.

아래 이미지는 가시적인 `ShowDetails` 경우`Yes`

![차트가 표시되는 조건부 가시성을 보여주는 이미지](./media/workbooks-interactive/conditional-visibility.png)

아래 이미지는 숨겨진 경우 `ShowDetails``No`

![차트가 숨겨져 있는 조건부 가시성을 보여주는 이미지](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>다음 단계


* 통합 문서에 대해 자세히 알아보고 다양한 다양한 시각화 옵션을 [알아보세요.](workbooks-visualizations.md)
* 통합 문서 리소스에 대한 액세스를 [제어하고](workbooks-access-control.md) 공유합니다.
