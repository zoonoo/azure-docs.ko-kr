---
title: 시각화
description: Azure Synapse 노트북을 사용 하 여 데이터 시각화
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 523356947d6d5f93fa8ef2202ad6e7d235c6afdd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919787"
---
# <a name="visualize-data"></a>데이터 시각화
Azure Synapse는 데이터 웨어하우스 및 빅 데이터 분석 시스템에서 정보에 대 한 시간을 가속화 하는 통합 분석 서비스입니다. 데이터 시각화는 데이터에 대 한 통찰력을 얻을 수 있는 핵심 구성 요소입니다. 이를 통해 큰 데이터를 더 쉽게 이해할 수 있습니다. 또한 데이터 그룹의 패턴, 추세 및 이상 값을 더 쉽게 검색할 수 있습니다. 

Azure Synapse Analytics에서 Apache Spark를 사용 하는 경우 Synapse 노트북 차트 옵션, 인기 있는 오픈 소스 라이브러리에 대 한 액세스 및 Synapse SQL 및 Power BI와의 통합을 포함 하 여 데이터를 시각화 하는 데 도움이 되는 다양 한 기본 제공 옵션이 있습니다.

## <a name="notebook-chart-options"></a>노트북 차트 옵션
Azure Synapse 노트북을 사용 하는 경우 차트 옵션을 사용 하 여 테이블 형식 결과 뷰를 사용자 지정 된 차트로 전환할 수 있습니다. 여기에서 코드를 작성 하지 않고도 데이터를 시각화할 수 있습니다. 

### <a name="displaydf-function"></a>display (df) 함수
함수를 사용 하 여 ```display``` SQL 쿼리를 설정 하 고 데이터 프레임 및 Rds를 다양 한 데이터 시각화로 Apache Spark 수 있습니다. ```display``` PySpark, Scala, Java 및 .net에서 만든 데이터 프레임 또는 RDDs에서 함수를 사용할 수 있습니다.

차트 옵션에 액세스 하려면:
1. ```%%sql```기본적으로 매직 명령의 출력이 렌더링 된 테이블 뷰에 표시 됩니다. 또한 ```display(df)``` Spark 데이터 프레임 또는 RDD (복원 력 있는 분산 데이터 집합) 함수를 호출 하 여 렌더링 된 테이블 뷰를 생성할 수 있습니다. 
   
2. 렌더링 된 테이블 뷰가 있으면 차트 뷰로 전환 합니다.
   ![기본 제공 차트](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. 이제 다음 값을 지정 하 여 시각화를 사용자 지정할 수 있습니다.
   | 구성 | 설명 |
   |--|--| 
   | 차트 종류 | ```display```함수는 가로 막대형 차트, 산 점도, 선 그래프 등을 비롯 한 다양 한 차트 종류를 지원 합니다. |
   | Key | X 축의 값 범위를 지정 합니다.|
   | 값 | Y 축 값의 범위를 지정 합니다. |
   | 계열 그룹 | 집계에 대 한 그룹을 결정 하는 데 사용 됩니다. | 
   | 집계 | 시각화에서 데이터를 집계 하는 방법| 
   
   
    > [!NOTE]
    > 기본적으로 ```display(df)``` 함수는 차트를 렌더링 하기 위해 데이터의 처음 1000 행만 사용 합니다. **모든 결과에 대 한 집계** 를 확인 하 고 **적용** 단추를 클릭 하 여 전체 데이터 집합에서 차트 생성을 적용 합니다. Spark 작업은 차트 설정이 변경 될 때 트리거됩니다. 계산을 완료 하 고 차트를 렌더링 하는 데 몇 분 정도 걸릴 수 있습니다.
   
4. 완료 되 면 최종 시각화를 보고 조작할 수 있습니다.

### <a name="displaydf-statistic-details"></a>표시 (df) 통계 세부 정보
를 사용 하 여 열 <code>display(df, summary = true)</code> 이름, 열 유형, 고유 값 및 각 열에 대 한 누락 값을 포함 하는 지정 된 Apache Spark 데이터 프레임의 통계 요약을 확인할 수 있습니다. 특정 열을 선택 하 여 최소값, 최대값, 평균 값 및 표준 편차를 볼 수도 있습니다.
    ![기본 제공 차트-요약](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>displayHTML (df) 옵션
Azure Synapse Analytics 노트북은 함수를 사용 하 여 HTML 그래픽을 지원 ```displayHTML``` 합니다.

다음 그림은 [D3.js](https://d3js.org/)를 사용 하 여 시각화를 만드는 예입니다.

   ![d3-js-예제](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

위의 시각화를 만들려면 다음 코드를 실행 합니다.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>인기 있는 라이브러리
데이터 시각화와 함께 제공 되는 Python은 다양 한 기능을 제공 하는 여러 그래프 라이브러리를 제공 합니다. 기본적으로 Azure Synapse Analytics의 모든 Apache Spark 풀에는 큐 레이트 및 인기 있는 오픈 소스 라이브러리 집합이 포함 되어 있습니다. Azure Synapse Analytics 라이브러리 관리 기능을 사용 하 여 추가 라이브러리 & 버전을 추가 하거나 관리할 수도 있습니다. 

### <a name="bokeh"></a>Bokeh
를 사용 하 여 **빛 망 울** 와 같은 HTML 또는 대화형 라이브러리를 렌더링할 수 있습니다 ```displayHTML(df)``` . 

다음 이미지는 **빛망울** 을 사용하여 지도 위에 문자 모양을 그리는 예입니다.

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

위의 이미지를 그리려면 다음 샘플 코드를 실행합니다.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```

### <a name="matplotlib"></a>Matplotlib
각 라이브러리에 대해 기본 제공 렌더링 함수를 사용 하 여 Matplotlib와 같은 표준 그리기 라이브러리를 렌더링할 수 있습니다.

다음 그림은 **Matplotlib** 를 사용 하 여 가로 막대형 차트를 만드는 예입니다.
   ![선 그래프 예제입니다.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

위의 이미지를 그리려면 다음 샘플 코드를 실행합니다.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```

### <a name="additional-libraries"></a>추가 라이브러리 
이러한 라이브러리 외에도 Azure Synapse Analytics 런타임에는 데이터 시각화에 자주 사용 되는 다음과 같은 라이브러리 집합이 포함 되어 있습니다.
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 

사용할 수 있는 라이브러리 및 버전에 대 한 최신 정보는 Azure Synapse Analytics 런타임 [설명서](./spark/../apache-spark-version-support.md) 를 참조 하세요.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>주문형 SQL & Apache Spark를 사용 하 여 Power BI에 연결
Azure Synapse Analytics는 데이터 엔지니어가 분석 솔루션을 빌드할 수 있도록 하 Power BI와 긴밀 하 게 통합 됩니다.

Azure Synapse Analytics를 사용하면 여러 작업 영역 컴퓨팅 엔진이 Spark 풀(미리 보기)과 SQL 주문형 엔진(미리 보기) 간에 데이터베이스와 테이블을 공유할 수 있습니다. [공유 메타 데이터 모델](https://docs.microsoft.com/azure/synapse-analytics/metadata/overview)을 사용 하 여 SQL 주문형으로 Apache Spark 테이블을 쿼리할 수 있습니다. 완료 되 면 SQL 주문형 끝점을 Power BI에 연결 하 여 동기화 된 Spark 테이블을 쉽게 쿼리할 수 있습니다.


## <a name="next-steps"></a>다음 단계
- Spark SQL DW 커넥터를 설정 하는 방법에 대 한 자세한 내용은: [SYNAPSE sql Connector](./spark/../synapse-spark-sql-pool-import-export.md)
- 기본 라이브러리 보기: [Azure Synapse Analytics 런타임](../spark/apache-spark-version-support.md)