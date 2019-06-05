---
title: Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기 | Microsoft docs
description: 미리 빌드되고 사용자 지정 매개 변수가 있는 통합 문서를 사용하여 복잡한 보고 간소화
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 6d84ad69b067f730bbfbcad9e46bdc9ae2036ead
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64569609"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기

통합 문서는 텍스트,  [분석 쿼리](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), Azure 메트릭 및 매개 변수를 풍부한 대화형 보고서로 결합합니다. 통합 문서는 동일한 Azure 리소스에 대한 액세스 권한이 있는 다른 팀 멤버에 의해 편집될 수 있습니다.

통합 문서는 다음과 같은 시나리오에 유용합니다.

* 관심 있는 메트릭을 사전에 모르는 경우 앱의 사용 현황 탐색: 사용자 수, 재방문 주기율, 변환율 등 다른 사용 현황 분석 도구와는 달리 통합 문서를 통해 이러한 종류의 자유 형식 탐색에 매우 유용하도록 여러 종류의 시각화 및 분석을 결합할 수 있습니다.
* 팀에 주요 상호 작용 및 다른 메트릭에 대한 사용자 수를 보여 주어 새로 릴리스된 기능이 수행되는 방법을 설명합니다.
* 앱에서 A/B 실험의 결과를 팀의 다른 멤버와 공유합니다. 텍스트로 실험에 대한 목표를 설명한 다음 각 메트릭이 위 또는 아래-대상이었는지 여부에 대한 분명한 설명선과 함께 실험을 평가하는 데 사용되는 각 사용 현황 메트릭 및 분석 쿼리를 보여줄 수 있습니다.
* 데이터, 텍스트 설명 및 차후의 중단을 방지하기 위한 다음 단계의 논의를 결합하여 앱의 사용 현황에 대한 중단의 영향을 보고합니다.

## <a name="starting-with-a-template-or-saved-workbook"></a>템플릿 또는 저장된 통합 문서 시작

통합 문서는 독립적으로 편집할 수 있는 차트, 테이블, 텍스트 및 입력 컨트롤로 구성된 섹션으로 구성됩니다. 통합 문서를 더 잘 이해하려면 열어보는 것이 좋습니다. 

앱의 Application Insights 환경 내에 있는 왼쪽 메뉴에서 **통합 문서**를 선택합니다.

![통합 문서에 대한 탐색 스크린샷](./media/usage-workbooks/001-workbooks.png)

그러면 시작할 수 있도록 미리 빌드된 여러 통합 문서에서 통합 문서 갤러리를 시작합니다.

![통합 문서 갤러리 스크린샷](./media/usage-workbooks/002-workbook-gallery.png)

**빠른 시작** 제목 아래에 있는 **기본 템플릿**부터 시작하겠습니다.

![통합 문서 갤러리 스크린샷](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>통합 문서 섹션 편집, 다시 정렬, 복제 및 삭제

통합 문서에는 **편집 모드** 및 **읽기 모드**라는 두 가지 모드가 있습니다. 기본 통합 문서가 먼저 시작되면 **편집 모드**가 열립니다. 여기에서는 그렇지 않은 경우 숨겨진 단계 및 매개 변수를 비롯하여 통합 문서의 모든 콘텐츠를 보여줍니다. **읽기 모드**는 간소화된 보고서 스타일 보기를 제공합니다. 이 기능을 사용하면 기본 메커니즘이 유지되는 동안 보고서를 만드는 복잡성을 제거할 수 있습니다. 수정이 필요한 경우 몇 번만 클릭하세요.

![Application Insights 통합 문서 섹션 편집 컨트롤](./media/usage-workbooks/editing-controls-new.png)

1. 섹션 편집을 완료하면 섹션의 왼쪽 아래 모서리에 있는 **편집 완료**를 클릭합니다.

2. 중복 섹션을 만들려면 **이 섹션 복제** 아이콘을 클릭합니다. 중복 섹션을 만드는 것은 이전 반복을 손실하지 않고 쿼리를 반복하는 좋은 방법입니다.

3. 통합 문서에서 섹션을 이동하려면 **위로 이동** 또는 **아래로 이동** 아이콘을 클릭합니다.

4. 섹션을 영구적으로 제거하려면 **제거** 아이콘을 클릭합니다.

## <a name="adding-text-and-markdown-sections"></a>텍스트 및 Markdown 섹션 추가

통합 문서에 제목, 설명 및 해설을 추가하면 테이블 및 차트의 집합을 설명으로 전환할 수 있습니다. 통합 문서의 텍스트 섹션은 머리글, 굵게, 기울임꼴 및 글머리 기호 목록과 같은 텍스트 서식 지정에 대해 [Markdown 구문](https://daringfireball.net/projects/markdown/)을 지원합니다.

통합 문서에 텍스트 섹션을 추가하려면 통합 문서의 맨 아래 또는 섹션의 맨 아래에 있는 **텍스트 추가** 단추를 사용합니다.

## <a name="adding-query-sections"></a>쿼리 추가 섹션

![통합 문서의 쿼리 섹션](./media/usage-workbooks/analytics-section-new.png)

통합 문서에 쿼리 섹션을 추가하려면 통합 문서의 맨 아래 또는 섹션의 맨 아래에 있는 **쿼리 추가** 단추를 사용합니다.

쿼리 섹션은 매우 유연하고 다음과 같은 질문에 응답하는 데 사용할 수 있습니다.

* 사용 현황의 감소와 동일한 기간 동안 사이트에서 throw한 예외의 수는?
* 일부 페이지를 보는 사용자에 대한 페이지 로드 시간의 분포는 무엇이었습니까?
* 사이트의 일부 페이지 집합을 봤지만 다른 일부 페이지 집합을 보지 않은 사용자의 수는? 이 질문은 사이트 기능의 각기 다른 하위 집합을 사용하는 사용자 클러스터가 있는지 여부를 파악하는 데 유용할 수 있습니다([Kusto 쿼리 언어](/azure/kusto/query/)에서는 `kind=leftanti` 한정자와 함께 `join` 연산자 사용).

또한 통합 문서를 시작한 애플리케이션의 컨텍스트에서만 쿼리하도록 제한되지 않습니다. 해당 리소스에 대한 액세스 권한이 있다면 Log Analytics 작업 영역뿐만 아니라 여러 Application Insights 모니터링 앱 간에 쿼리를 수행할 수 있습니다.

추가 외부 Application Insights 리소스에서 쿼리하려면 **앱** 식별자를 사용합니다.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

이 쿼리를 세 가지 애플리케이션의 요청을 통합합니다. 이름이 app01인 앱, 이름이 app02인 앱 및 로컬 Application Insights 리소스의 요청입니다.

외부 Log Analytics 작업 영역의 데이터를 가져오려면 **작업 영역** 식별자를 사용합니다.

리소스 간 쿼리에 대한 자세한 내용을 알아보려면 [공식 지침](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)을 참조합니다.

### <a name="advanced-analytic-query-settings"></a>고급 분석 쿼리 설정

각 섹션에는 **매개 변수 추가** 단추의 오른쪽에 있는 ![Application Insights 통합 문서 섹션 편집 컨트롤](./media/usage-workbooks/005-settings.png) 설정 아이콘을 통해 액세스할 수 있는 고유한 고급 설정이 있습니다.

![Application Insights 통합 문서 섹션 편집 컨트롤](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **사용자 지정 너비**    | 항목을 임의의 크기로 만들도록 설정합니다. 그러면 한 줄의 여러 항목에 맞추어 사용자 테이블과 차트를 더 풍부한 대화형 보고서로 구성할 수 있습니다.  |
   | **조건부로 표시** | 이 기능을 사용하여 읽기 모드에서 매개 변수에 따라 단계를 숨깁니다. |
   | **매개 변수 내보내기**| 이렇게 하면 그리드 또는 차트가 이후 단계에서 선택한 행이 값을 변경하거나 표시하게 됩니다.  |
   | **편집하고 있지 않을 때 쿼리 표시** | 읽기 모드에서 차트 또는 테이블 위에 쿼리를 표시합니다.
   | **편집하고 있지 않을 때 분석에서 열기 단추 표시** | 한 번 클릭하여 액세스할 수 있도록 차트의 오른쪽 모퉁이에 파란색 분석 아이콘을 추가합니다.|

이러한 설정 중 대부분은 매우 간단하지만 **매개 변수 내보내기**를 이해하려면 이 기능을 사용할 수 있는 통합 문서를 검사하는 것이 좋습니다.

미리 빌드된 통합 문서 중 하나에서는 활성 사용자에 대한 정보를 제공합니다.

통합 문서의 첫 번째 섹션은 분석 쿼리 데이터를 기반으로 합니다.

![Application Insights 통합 문서 섹션 편집 컨트롤](./media/usage-workbooks/003-active-users.png)

또한 두 번째 섹션은 분석 쿼리 데이터를 기반으로 하지만 첫 번째 테이블의 행을 선택하면 차트의 콘텐츠를 대화형으로 업데이트합니다.

![Application Insights 통합 문서 섹션 편집 컨트롤](./media/usage-workbooks/004-active-users-trend.png)

 테이블의 분석 쿼리에서 사용할 수 있는 **항목을 선택하면 매개 변수 내보내기** 고급 설정을 통해 가능해집니다.

![Application Insights 통합 문서 섹션 편집 컨트롤](./media/usage-workbooks/007-settings-export.png)

그런 다음, 행을 선택하면 두 번째 분석 쿼리는 내보낸 값을 활용합니다. 행을 선택하지 않으면 전체 값을 나타내는 행을 기본값으로 지정합니다. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>메트릭 추가 섹션

메트릭 섹션에서는 Azure Monitor 메트릭 데이터를 대화형 보고서로 통합할 수 있는 모든 권한을 제공합니다. 미리 빌드된 통합 문서에는 한거번에 모든 기능을 최고로 활용할 수 있도록 하는 분석 쿼리 데이터 및 메트릭 데이터가 모두 포함됩니다. 액세스 권한이 있는 구독에 있는 리소스에서 메트릭 데이터를 가져올 수 있는 기능도 있습니다.

CPU 성능의 그리드 시각화를 제공하기 위해 통합 문서로 가져온 가상 머신 데이터의 예제는 다음과 같습니다.

![Application Insights 통합 문서 섹션 편집 컨트롤](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>매개 변수 추가 섹션

통합 문서 매개 변수를 사용하면 쿼리 또는 텍스트 섹션을 수동으로 편집하지 않고 통합 문서에서 값을 변경할 수 있습니다.  그러면 기본 분석 쿼리 언어를 이해해야 하는 요구 사항을 제거하고 통합 문서 기반 보고의 잠재적인 대상을 크게 확장하게 됩니다.

``{parameterName}``과 같은 중괄호에서 매개 변수의 이름을 입력하여 쿼리, 텍스트 또는 다른 매개 변수 섹션에서 매개 변수 값을 대체합니다.  매개 변수 이름은 기본적으로 영문자 또는 밑줄과 같은 JavaScript 식별자 뒤에 영숫자 또는 밑줄이 오는 것과 비슷한 규칙으로 제한됩니다. 예를 들어 **a1**은 허용되지만, **1a**는 허용되지 않습니다.

매개 변수는 선형이며 통합 문서의 맨 위에서 시작하고 이후 단계까지 이어집니다.  나중에 통합 문서에서 선언된 매개 변수는 선언되었던 매개 변수를 재정의할 수 있습니다.  그러면 쿼리를 사용하는 매개 변수가 정의된 매개 변수 값에 액세스할 수 있습니다.  또한 매개 변수의 단계 자체 내에서 매개 변수는 왼쪽에서 오른쪽으로 이어지는 선형이며 여기서 오른쪽 매개 변수는 동일한 단계에서 이전에 선언된 매개 변수에 종속될 수 있습니다.
 
네 가지 형식의 매개 변수가 현재 지원됩니다.

  |         |          |
   | ---------------- |:-----|
   | **Text**    | 사용자가 텍스트 상자를 편집하고 필요에 따라 쿼리를 제공하여 기본 값을 입력할 수 있습니다. |
   | **드롭다운** | 사용자는 값 집합 중에서 선택합니다. |
   | **시간 범위 선택기**| 사용자는 미리 정의된 시간 범위 값 집합 중에서 선택하거나 사용자 지정 시간 범위 중에서 선택합니다.|
   | **리소스 선택기** | 사용자는 통합 문서에 대해 선택한 리소스 중에서 선택합니다.|

### <a name="using-a-text-parameter"></a>텍스트 매개 변수 사용

이스케이프 또는 인용 부호 없이 사용자가 텍스트 상자에 입력한 값을 쿼리에서 직접 대체합니다. 필요한 값이 문자열인 경우 쿼리는 매개 변수에 인용 부호를 표시해야 합니다(예: **'{parameter}'** ).

그러면 텍스트 상자의 값을 어디서나 사용할 수 있습니다. 테이블 이름, 열 이름, 함수 이름, 연산자 등일 수 있습니다.

텍스트 매개 변수 형식에는 **분석 쿼리에서 기본값 가져오기** 설정이 있습니다. 이를 통해 통합 문서 작성자가 쿼리를 사용하여 해당 텍스트 상자에 대해 기본 값을 채울 수 있습니다.

분석 쿼리에서 기본값을 사용하는 경우 첫 번째 행의 첫 번째 값(행 0, 열 0)만 기본값으로 사용됩니다. 따라서 하나의 행과 하나의 열만 반환하도록 쿼리를 제한하는 것이 좋습니다. 쿼리에서 반환된 다른 데이터는 무시됩니다. 

쿼리가 반환하는 값이 어떤 값이든지 이스케이프 또는 인용 부호 없이 직접 대체됩니다. 쿼리에서 행을 반환하지 않는 경우 매개 변수의 결과는 빈 문자열(매개 변수가 필요 없는 경우)이거나 정의되지 않습니다(매개 변수가 필수인 경우).

### <a name="using-a-dropdown"></a>드롭다운 사용

드롭다운 매개 변수 형식을 통해 드롭다운 컨트롤을 만들어 하나 이상의 값을 선택할 수 있습니다.

드롭다운은 분석 쿼리에 의해 채워집니다. 쿼리에서 하나의 열을 반환하는 경우 해당 열의 값은 드롭다운 컨트롤의 **값** 및 **레이블** 모두입니다. 쿼리에서 두 개의 열을 반환하는 경우 첫 번째 열은 **값**이고, 두 번째 열은 드롭다운에 표시된 **레이블**입니다.  쿼리에서 세 개의 열을 반환하는 경우 세 번째 열은 해당 드롭다운에서 기본 선택 항목을 나타내기 위해 사용됩니다.  이 열은 어떤 형식이든 될 수 있지만 가장 간단한 형식은 부울 또는 숫자 형식입니다. 여기서 0은 false이고, 1은 true입니다.

 열이 문자열 형식인 경우 null/비어 있는 문자열은 false로 간주되고, 다른 모든 값은 true로 간주됩니다. 단일 선택 영역 드롭다운의 경우 true 값인 첫 번째 값은 기본 선택 영역으로 사용됩니다.  다중 선택 영역 드롭다운의 경우 true 값인 모든 값은 기본 선택된 집합으로 사용됩니다. 드롭다운의 항목은 쿼리에서 행을 반환한 순서로 표시됩니다. 

활성 사용자의 보고서에서 표시된 매개 변수를 살펴보겠습니다. **TimeRange** 옆에 있는 편집 기호를 클릭합니다.

![Application Insights 통합 문서 섹션 편집 컨트롤](./media/usage-workbooks/009-time-range.png)

그러면 매개 변수 편집 메뉴를 시작합니다.

![Application Insights 통합 문서 섹션 편집 컨트롤](./media/usage-workbooks/010-time-range-edit.png)

쿼리에서는 갑작스럽게 임의의 테이블, 전체 콘텐츠를 생성할 수 있는 **datatable**이라는 분석 쿼리 언어 기능을 사용합니다. 다음 분석 쿼리를 예로 듭니다.

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

결과를 생성합니다.

![Application Insights 통합 문서 섹션 편집 컨트롤](./media/usage-workbooks/011-data-table.png)

더 적용 하는 예제는 이름으로 국가/지역 집합에서 선택 드롭다운을 사용 합니다.

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

쿼리는 다음과 같이 결과를 표시합니다.

![국가 드롭다운](./media/usage-workbooks/012-country-dropdown.png)

드롭다운은 대화형 보고서를 사용자 지정하고 만들기 위한 매우 강력한 도구입니다.

### <a name="time-range-parameters"></a>시간 범위 매개 변수

드롭다운 매개 변수 형식을 통해 고유한 사용자 지정 시간 범위 매개 변수를 만들 수 있는 동시에 동일한 수준의 유연성이 필요하지 않는 경우 기본 시간 범위 매개 변수 형식을 사용할 수도 있습니다. 

시간 범위 매개 변수 형식에는 5분부터 최근 90일까지 이동하는 15개의 기본 범위가 포함됩니다. 사용자 지정 시간 범위 선택 영역을 허용하는 옵션이 있습니다. 이를 통해 보고서의 연산자가 시간 범위에 대해 명시적 시작 및 중지 값을 선택할 수 있습니다.

### <a name="resource-picker"></a>리소스 선택기

리소스 선택기 매개 변수 형식에서는 특정 형식의 리소스로 보고서 범위를 지정하는 기능을 제공합니다. 리소스 선택기 형식을 활용하는 미리 빌드된 통합 문서의 예로는 **오류 인사이트** 통합 문서가 있습니다.

![국가 드롭다운](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>팀과 통합 문서 저장 및 공유

통합 문서는 사용자 프라이빗인 **내 보고서** 섹션 또는 Application Insights 리소스에 대한 액세스가 있는 모든 사용자에게 액세스가 허용되는 **공유 보고서** 섹션에서 Application Insights 리소스 내에 저장됩니다. 리소스에서 모든 통합 문서를 보려면 작업 모음에서 **열기** 단추를 클릭합니다.

현재 **내 보고서**에 있는 통합 문서를 공유하려면:

1. 작업 모음에서 **열기**를 클릭합니다.
2. 공유하려는 통합 문서 옆에 있는 "..." 단추를 클릭합니다.
3. **공유 보고서로 이동**을 클릭합니다.

링크 또는 전자 메일을 통해 통합 문서를 공유하려면 작업 모음에서 **공유**를 클릭합니다. 통합 문서를 보려면 링크의 수신자는 Azure Portal에서 이 리소스에 대한 액세스가 필요함을 명심하세요. 편집하려면 수신자는 최소한 리소스에 대한 참가자 권한이 필요합니다.

Azure 대시보드에 통합 문서에 대한 링크를 고정하려면:

1. 작업 모음에서 **열기**를 클릭합니다.
2. 고정하려는 통합 문서 옆에 있는 "..." 단추를 클릭합니다.
3. **대시보드에 고정**을 클릭합니다.

## <a name="contributing-workbook-templates"></a>통합 문서 템플릿 기여

훌륭한 통합 문서 템플릿을 만들어서 커뮤니티와 공유하려고 하십니까? 자세히 알아보려면 [GitHub 리포지토리](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)를 방문하세요.

## <a name="next-steps"></a>다음 단계
- 사용 현황 환경을 활성화하려면 [사용자 지정 이벤트](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) 또는 [페이지 보기](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views) 보내기를 시작합니다.
- 사용자 지정 이벤트 또는 페이지 보기를 이미 보낸 경우 사용자가 서비스를 사용하는 방법에 대해 알아보려면 사용 현황 도구를 살펴봅니다.
    - [사용자, 세션, 이벤트](../../azure-monitor/app/usage-segmentation.md)
    - [깔때기](../../azure-monitor/app/usage-funnels.md)
    - [보존](../../azure-monitor/app/usage-retention.md)
    - [사용자 흐름](../../azure-monitor/app/usage-flows.md)
    - [사용자 컨텍스트 추가](../../azure-monitor/app/usage-send-user-context.md)