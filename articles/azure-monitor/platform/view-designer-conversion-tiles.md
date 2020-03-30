---
title: 통합 문서 타일 변환에 Azure 모니터 보기 디자이너
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658629"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure 모니터 보기 디자이너 타일 변환
[뷰 디자이너는](view-designer.md) 차트, 목록 및 타임라인을 사용하여 Log Analytics 작업 영역에서 데이터를 시각화하는 데 도움이 되는 사용자 지정 보기를 만들 수 있는 Azure Monitor의 기능입니다. 단계적으로 폐지되고 추가 기능을 제공하는 통합 문서로 대체됩니다. 이 문서에서는 다른 타일을 통합 문서로 변환하기 위한 세부 정보를 제공합니다.

## <a name="donut--list-tile"></a>도넛 & 목록 타일

![도넛 목록](media/view-designer-conversion-tiles/donut-list.png)

통합 문서에서 도넛 & 목록 타일을 다시 만들려면 두 개의 별도 시각화가 포함됩니다. 도넛 부분에는 두 가지 옵션이 있습니다.
**쿼리 추가를** 선택하여 시작하고 뷰 디자이너에서 원래 쿼리를 셀에 붙여넣습니다.

**옵션 1:** **시각화** 드롭다운에서 **원형 차트** ![선택: 원형 차트 시각화 메뉴](media/view-designer-conversion-tiles/pie-chart.png)

**옵션 2:** **시각화** 드롭다운에서 **쿼리별로 설정을** 선택하고 `| render piechart` 쿼리에 추가합니다.

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

목록을 만들고 스파크라인을 사용하도록 설정하면 일반적인 [작업에](view-designer-conversion-tasks.md)대한 문서를 참조하십시오.

다음은 통합 & 목록 타일을 다시 해석하는 방법의 예입니다.

![도넛 목록 통합 문서](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>표선 표표 & 목록 타일
![선도 목록](media/view-designer-conversion-tiles/line-list.png) 

표선 차트 부분을 다시 만들려면 다음과 같이 쿼리를 업데이트합니다.

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

선부차트를 시각화하기 위한 두 가지 옵션이 있습니다.

**옵션 1:** **시각화** 드롭다운에서 **선도차트를** 선택합니다.
 
 ![선부표 메뉴](media/view-designer-conversion-tiles/line-visualization.png)

**옵션 2:** **시각화** 드롭다운에서 **쿼리별로 설정을** 선택하고 `| render linechart` 쿼리에 추가합니다.

 ![시각화 메뉴](media/view-designer-conversion-tiles/set-by-query.png)

**예제**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

목록을 만들고 스파크라인을 사용하도록 설정하면 일반적인 [작업에](view-designer-conversion-tasks.md)대한 문서를 참조하십시오.

다음은 목록 & 표식표에서 표부 차트를 다시 해석하는 방법의 예입니다.

![표표 목록 통합 문서](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>번호 & 목록 타일

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

시각화 드롭다운을 **타일로** 변경한 다음 **타일 설정을**선택합니다.
 ![타일 시각화](media/view-designer-conversion-tiles/tile-visualization.png)

**제목** 섹션을 비워 두고 **왼쪽을**선택합니다. **사용 열의** 값 변경: **개수**및 **열 렌더러를** **큰 숫자로**변경합니다.

![타일 설정](media/view-designer-conversion-tiles/tile-settings.png)

 
목록을 만들고 스파크라인을 사용하도록 설정하면 일반적인 [작업에](view-designer-conversion-tasks.md)대한 문서를 참조하십시오.

다음은 통합 & 목록 타일 수를 다시 해석하는 방법의 예입니다.

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

쿼리를 막대 차트로 시각화하는 데는 두 가지 옵션이 있습니다.

**옵션 1:** **시각화** 드롭다운에서 **막대 차트** ![선택: 막대 표시자 시각화](media/view-designer-conversion-tiles/bar-visualization.png)
 
**옵션 2:** **시각화** 드롭다운에서 **쿼리별로 설정을** 선택하고 `| render barchart` 쿼리에 추가합니다.

 ![시각화 메뉴](media/view-designer-conversion-tiles/set-by-query.png)

 
목록을 만들고 스파크라인을 사용하도록 설정하면 일반적인 [작업에](view-designer-conversion-tasks.md)대한 문서를 참조하십시오.

다음은 타임라인 & 목록 타일을 통합 문서에서 다시 해석하는 방법의 예입니다.

![타임라인 목록 통합 문서](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>다음 단계

- [통합 문서 전환으로 보기 디자이너의 개요](view-designer-conversion-overview.md)
