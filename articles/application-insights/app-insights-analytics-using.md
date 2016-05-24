<properties 
	pageTitle="Application Insights의 강력한 검색 도구인 Analytics 사용" 
	description="Application Insights의 강력한 진단 검색 도구인 Analytics를 사용하는 방법에 대해 설명합니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="awills"/>




# Application Insights에서 Analytics 사용


[분석](app-insights-analytics.md)은 [Application Insights](app-insights-overview.md)의 강력한 검색 기능입니다. 다음 페이지에서는 분석 쿼리 언어에 대해 설명합니다.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

## Analytics 열기

Application Insights의 앱 홈 리소스에서 Analytics를 클릭합니다.

![portal.azure.com에서 Application Insights 리소스를 열고 분석을 클릭합니다.](./media/app-insights-analytics/001.png)

인라인 자습서에서 수행할 수 있는 몇 가지 작업을 확인할 수 있습니다.

보다 광범위한 둘러보기는 [여기](app-insights-analytics-tour.md)서 제공됩니다.

## 쿼리 작성

왼쪽에 나열된 테이블 이름으로 시작되는 쿼리를 작성합니다. [연산자](app-insights-analytics-queries.md) 파이프라인을 만들려면 `|`를 사용합니다.


![](./media/app-insights-analytics-using/150.png)

* 쿼리에 빈 줄을 넣으면 안 됩니다.
* 쿼리에서는 단일 줄 바꿈을 사용할 수 있습니다.
* 창에서 여러 쿼리를 빈 줄로 구분하여 작성할 수 있습니다.
* 쿼리를 실행하려면 **쿼리 안이나 끝에 커서를 놓고** 이동을 클릭합니다.


![](./media/app-insights-analytics-using/130.png)

* 쿼리 창의 내용을 저장하고 불러올 수 있습니다.

![](./media/app-insights-analytics-using/140.png)

## 결과 정렬

표시하려는 열을 선택할 수 있습니다. 항목을 확장하면 반환된 모든 열 값이 표시됩니다.

![](./media/app-insights-analytics-using/030.png)

> [AZURE.NOTE] 웹 브라우저에서 사용할 수 있는 결과의 순서를 빠르게 변경하려면 열 머리글을 클릭합니다. 단, 큰 결과 집합의 경우에는 브라우저에 다운로드되는 행의 수가 제한된다는 점을 고려해야 합니다. 그러므로 이 방식으로 정렬한다고 해서 항상 최상위 항목이나 최하위 항목이 표시되지는 않습니다. 최상위 항목이나 최하위 항목을 표시하려면 [top](app-insights-analytics-queries.md#top-operator) 또는 [sort](app-insights-analytics-queries.md#sort-operator) 연산자를 사용해야 합니다.

그러나 서버에서 매우 큰 테이블을 다운로드하지 않도록 [take](app-insights-analytics-queries.md#take-operator), [top](app-insights-analytics-queries.md#top-operator) 또는 [summarize](app-insights-analytics-queries.md#summarize-operator) 연산자를 사용하는 것이 좋습니다. 행의 수는 쿼리당 약 1만 개로 자동 제한됩니다.


## 다이어그램

원하는 다이어그램의 유형을 선택합니다.

![](./media/app-insights-analytics-using/230.png)

적절한 유형의 열이 여러 개인 경우 x축과 y축 및 결과 정렬 기준으로 사용할 크기의 열을 선택할 수 있습니다.

![](./media/app-insights-analytics-using/100.png)

기본적으로 결과는 처음에는 테이블로 표시되며 다이어그램은 수동으로 선택합니다. 하지만 쿼리 끝에 [render 지시문](app-insights-analytics-queries.md#render-directive)을 사용하여 다이어그램을 선택할 수 있습니다.

## Excel로 내보내기

쿼리를 실행한 후 .csv 파일을 다운로드할 수 있습니다. 이렇게 하려면 **Excel로 내보내기**를 클릭합니다.

## Power BI에 내보내기

1. 쿼리에 커서를 놓고 **Power BI로 내보내기**를 선택합니다.

    ![](./media/app-insights-analytics-using/240.png)

    그러면 M 스크립트 파일이 다운로드됩니다.

3. Power BI Desktop 고급 쿼리 편집기에 M 언어 스크립트를 복사합니다.
 * 내보낸 파일을 엽니다.
 * Power BI Desktop에서 **데이터 가져오기 > 빈 쿼리 > 고급 편집기**를 선택하고 M 언어 스크립트를 붙여 넣습니다.

    ![](./media/app-insights-analytics-using/250.png)

4. 필요에 따라 자격 증명을 편집한 후 보고서를 작성할 수 있습니다.

    ![](./media/app-insights-analytics-using/260.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0511_2016-->