---
title: Azure Monitor 뷰 디자이너에서 통합 문서로 타일 변환
description: Azure Monitor의 뷰에서 전환할 때 타일을 통합 문서로 변환하는 방법에 대한 세부 정보입니다.
author: shijatsu
ms.author: shijain
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: b73aa0569fb9889de687e7b3eacba5a0fcd54cc1
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108318012"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor 뷰 디자이너 타일 변환
[뷰 디자이너](view-designer.md)는 차트, 목록 및 타임라인을 사용하여 Log Analytics 작업 영역의 데이터를 시각화하는 데 도움이 되는 사용자 지정 뷰를 만들 수 있게 해주는 Azure Monitor 기능입니다. 이러한 기능은 단계적으로 제거되고 있으며, 추가 기능을 제공하는 통합 문서로 대체되고 있습니다. 이 문서에서는 다양한 타일을 통합 문서로 변환하는 방법에 대해 자세히 설명합니다.

## <a name="donut--list-tile"></a>도넛형 및 목록 타일

![도넛형 목록](media/view-designer-conversion-tiles/donut-list.png)

통합 문서에서 도넛형 및 목록 타일을 다시 만들려면 두 가지 별도의 시각화가 필요합니다. 도넛형 부분에는 두 가지 옵션이 있습니다.
둘 다 **쿼리 추가** 를 선택하는 것으로 시작하며, 뷰 디자이너의 원래 쿼리를 셀에 붙여넣습니다.

**옵션 1:** **시각화** 드롭다운: ![원형 차트 시각화 메뉴](media/view-designer-conversion-tiles/pie-chart.png)에서 **원형 차트** 선택

**옵션 2:** **시각화** 드롭다운에서 **쿼리로 설정** 을 선택하고 쿼리에 `| render piechart` 추가:

 ![시각화 메뉴](media/view-designer-conversion-tiles/set-by-query.png)

**예제**

원래 쿼리
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

업데이트된 쿼리
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

목록을 만들고 스파크라인을 사용하도록 설정하려면 [일반 작업](view-designer-conversion-tasks.md)에 대한 문서를 참조하세요.

다음은 도넛형 및 목록 타일이 통합 문서에서 재해석되는 방법에 대한 예입니다.

![도넛형 목록 통합 문서](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>꺾은선형 차트 및 목록 타일
![꺾은선형 차트 목록](media/view-designer-conversion-tiles/line-list.png) 

꺾은선형 차트 부분을 다시 만들려면 다음과 같이 쿼리를 업데이트합니다.

원래 쿼리
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

업데이트된 쿼리
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

꺾은선형 차트를 시각화하는 옵션은 두 가지가 있습니다.

**옵션 1:** **시각화** 드롭다운에서 **꺾은선형 차트** 를 선택합니다.
 
 ![꺾은선형 차트 메뉴](media/view-designer-conversion-tiles/line-visualization.png)

**옵션 2:** **시각화** 드롭다운에서 **쿼리로 설정** 을 선택하고 쿼리에 `| render linechart` 추가:

 ![시각화 메뉴](media/view-designer-conversion-tiles/set-by-query.png)

**예제**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

목록을 만들고 스파크라인을 사용하도록 설정하려면 [일반 작업](view-designer-conversion-tasks.md)에 대한 문서를 참조하세요.

다음은 꺾은선형 차트 및 목록 타일이 통합 문서에서 재해석되는 방법에 대한 예입니다.

![꺾은선형 차트 목록 통합 문서](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>숫자 및 목록 타일

 ![타일 목록](media/view-designer-conversion-tiles/tile-list-example.png)

숫자 타일의 경우 다음과 같이 쿼리를 업데이트합니다.

원래 쿼리
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

업데이트된 쿼리
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

시각화 드롭다운을 **타일** 로 변경한 다음, **타일 설정** 을 선택합니다.
 ![타일 시각화](media/view-designer-conversion-tiles/tile-visualization.png)

**제목** 섹션을 비워 두고 **왼쪽** 을 선택합니다. **다음 열 사용:** 값을 **개수** 로, **열 렌더러** 를 **큰 숫자** 로 변경합니다.

![타일 설정](media/view-designer-conversion-tiles/tile-settings.png)

 
목록을 만들고 스파크라인을 사용하도록 설정하려면 [일반 작업](view-designer-conversion-tasks.md)에 대한 문서를 참조하세요.

다음은 숫자 및 목록 타일이 통합 문서에서 재해석되는 방법에 대한 예입니다.

![숫자 목록 통합 문서](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>타임라인 및 목록

 ![타임라인 목록](media/view-designer-conversion-tiles/time-list.png)

타임라인의 경우 다음과 같이 쿼리를 업데이트합니다.

원래 쿼리
```KQL
search * 
| sort by TimeGenerated desc
```

업데이트된 쿼리
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

쿼리를 막대형 차트로 시각화하는 옵션은 두 가지가 있습니다.

**옵션 1:** **시각화** 드롭다운: ![막대형 차트 시각화](media/view-designer-conversion-tiles/bar-visualization.png)에서 **막대형 차트** 선택
 
**옵션 2:** **시각화** 드롭다운에서 **쿼리로 설정** 을 선택하고 쿼리에 `| render barchart` 추가:

 ![시각화 메뉴](media/view-designer-conversion-tiles/set-by-query.png)

 
목록을 만들고 스파크라인을 사용하도록 설정하려면 [일반 작업](view-designer-conversion-tasks.md)에 대한 문서를 참조하세요.

다음은 타임라인 및 목록 타일이 통합 문서에서 재해석되는 방법에 대한 예입니다.

![타임라인 목록 통합 문서](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>다음 단계

- [뷰 디자이너에서 통합 문서로 전환 개요](view-designer-conversion-overview.md)
