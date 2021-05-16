---
title: Azure Monitor 뷰 디자이너에서 통합 문서로 변환 일반 작업
description: Azure Monitor의 통합 문서에 대한 뷰에서 전환할 때의 일반적인 작업입니다.
author: shijatsu
ms.author: shijain
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 3b1147b4a16fd453605bb4bab6d48605e110b995
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108318084"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>뷰 디자이너에서 통합 문서로 변환 일반 작업
[뷰 디자이너](view-designer.md)는 차트, 목록 및 타임라인을 사용하여 Log Analytics 작업 영역의 데이터를 시각화하는 데 도움이 되는 사용자 지정 뷰를 만들 수 있게 해주는 Azure Monitor 기능입니다. 이러한 기능은 단계적으로 제거되고 있으며, 추가 기능을 제공하는 통합 문서로 대체되고 있습니다. 이 문서에서는 뷰를 통합 문서로 변환할 때 일반적인 작업에 대해 자세히 설명합니다.


## <a name="quickstart-with-preset-view-designer-templates"></a>미리 설정된 뷰 디자이너 템플릿을 사용하는 빠른 시작

Log Analytics 작업 영역의 통합 문서에는 뷰 디자이너의 일부 보기와 일치하도록 만들어진 템플릿이 이미 있습니다. **뷰 디자이너 가이드** 범주 아래에서 **뷰 디자이너 전환 가이드** 를 선택하여 옵션에 대해 알아보거나 미리 설정된 템플릿 중 하나를 선택합니다.

![예제 템플릿](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>시간 범위 필터 사용
뷰 디자이너에는 기본 시간 범위 필터가 기본 제공되지만 통합 문서에서 이 설정은 기본적으로 사용되지 않습니다. 통합 문서를 통해 사용자는 자신의 데이터 로그에 더 적합할 수 있는 고유한 시간 범위 필터를 만들 수 있습니다. 필터를 생성하는 단계는 다음과 같습니다.

**매개 변수 추가** 옵션을 선택합니다. 기본 **스타일** 은 *알약* 으로 설정됩니다.

![매개 변수 추가](media/view-designer-conversion-tasks/add-param.png)

 **매개 변수 추가** 단추를 선택합니다.

![매개 변수 추가](media/view-designer-conversion-tasks/add-parameter.png)

사이드바 메뉴의 **매개 변수 이름** 텍스트 상자에 *TimeRange* 를 입력합니다. **매개 변수 유형** 을 *시간 범위 선택기* 로 설정합니다. **필수?** 확인란을 선택 취소합니다.

![매개 변수 메뉴](media/view-designer-conversion-tasks/parameter-menu.png)

사이드바 메뉴의 왼쪽 상단 모퉁이에 매개 변수를 저장합니다. 기본적으로 드롭다운을 *설정되지 않은 상태* 로 두거나 기본 **TimeRange** 값(예: *24시간*)을 선택할 수 있습니다. **Done Editing** 을 선택합니다.

매개 변수 이름 주위에 중괄호({})를 추가하여 쿼리에 매개 변수를 사용할 수 있습니다. 매개 변수에 대한 자세한 내용은 [매개 변수에 대한 통합 문서 설명서](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)를 참조하세요.

## <a name="updating-queries-with-the-timerange-parameter"></a>TimeRange 매개 변수를 사용하여 쿼리 업데이트

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>옵션 1: 시간 범위 드롭다운에서 TimeRange 선택

![시간 매개 변수](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>옵션 2: 로그 쿼리 업데이트

쿼리에서 다음 예제와 같이 `| where TimeGenerated {TimeRange}` 줄을 추가합니다.

원래 쿼리
```KQL
search * 
| summarize count() by Type
```

업데이트된 쿼리
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>목록 포함
대부분의 뷰 디자이너 뷰에는 목록이 포함되어 있으며 통합 문서에서 이 표준 목록을 재현할 수 있습니다.

![타일 목록](media/view-designer-conversion-tasks/tile-list.png)

셀 옵션에서 **쿼리 추가** 를 클릭하여 시각화를 추가합니다.

![매개 변수 추가](media/view-designer-conversion-tasks/add-param.png)

뷰 디자이너는 원래 예제의 구문과 일치하는 기본 쿼리를 채택합니다. 다음 예제와 같이 쿼리를 업데이트된 형식으로 변경하여 이를 업데이트할 수 있습니다.

원래 쿼리
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

업데이트된 쿼리
```KQL
search * 
| summarize Count = count() by Type
```

이는 다음과 유사한 목록을 생성합니다.

![목록 예제](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>스파크라인 사용
그리드의 일반적인 기능은 시간별로 다양한 데이터 패턴을 요약하는 스파크라인을 추가하는 것입니다. 뷰 디자이너는 통합 문서와 마찬가지로 모든 목록에 대해 **스파크라인 사용** 기능을 제공합니다. 데이터에 뷰 디자이너와 일치하는 스파크라인을 포함하려면 다음 예제와 같이 데이터를 원래 쿼리와 조인합니다.

원래 쿼리
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

업데이트된 쿼리
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

**열 설정** 을 선택합니다.
![열 설정](media/view-designer-conversion-tasks/column-settings.png)

**열 렌더러** 드롭다운을 *Spark 영역* 으로 업데이트합니다.
![스파크라인](media/view-designer-conversion-tasks/sparkline.png)

설정을 저장하고 쿼리를 다시 실행하여 스파크라인을 포함하도록 테이블을 업데이트합니다.

결과 그리드는 ![스파크라인 예제](media/view-designer-conversion-tasks/sparkline-example.png)와 유사하게 됩니다.

## <a name="advanced-cell-settings"></a>고급 셀 설정
뷰 디자이너를 미러링하려면 통합 문서 셀의 크기를 변경하거나 로그에 핀과 외부 링크를 추가하는 등의 작업을 수행하면 됩니다.

**고급 설정** 에 액세스하려면 각 셀의 아래쪽에 있는 기어 아이콘을 선택합니다.

![고급 설정](media/view-designer-conversion-tasks/advanced-settings.png)

그러면 다양한 옵션이 포함된 메뉴가 표시됩니다.

![고급 설정](media/view-designer-conversion-tasks/advanced-settings-settings.png)

외부 쿼리에 핀과 링크를 추가하려면 해당 확인란을 선택합니다. 셀에 제목을 추가하려면 **차트 제목** 섹션에 원하는 제목을 입력합니다.

기본적으로 모든 통합 문서 셀은 전체 페이지 너비에 가득 차게 설정되지만, **고급 설정** 메뉴의 **스타일** 탭 아래에서 셀을 스케일 다운하여 이를 조정할 수 있습니다.

![고급 설정 스타일](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>추가 매개 변수
**매개 변수 추가** 를 선택하여 통합 문서에서 새 매개 변수를 만듭니다. 

구독을 선택하려면 오른쪽 메뉴의 **매개 변수 이름** 필드에 *구독* 을 입력하고 **매개 변수 유형** 드롭다운에서 *구독 선택기* 를 선택합니다.

![구독 메뉴](media/view-designer-conversion-tasks/subscription-filter.png)

리소스를 선택하려면 측면 메뉴의 **매개 변수 이름** 필드에 *리소스* 를 입력하고 **매개 변수 유형** 드롭다운에서 *리소스 선택기* 를 선택합니다.

![리소스 메뉴](media/view-designer-conversion-tasks/resource-filter.png)

그러면 다양한 구독 및 리소스에 액세스할 수 있는 드롭다운이 삽입됩니다.

![구독 리소스 드롭다운](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>다음 단계
- [타일 변환](view-designer-conversion-tiles.md)
