<properties 
	pageTitle="Analytics 사용 - Application Insights의 강력한 검색 도구 | Microsoft Azure" 
	description="Application Insights의 강력한 진단 검색 도구인 Analytics를 사용하는 방법에 대해 설명합니다. " 
	services="application-insights" 
    documentationCenter=""
	authors="danhadari" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2016" 
	ms.author="danha"/>


# Application Insights에서 Analytics 사용


[분석](app-insights-analytics.md)은 [Application Insights](app-insights-overview.md)의 강력한 검색 기능입니다. 다음 페이지에서는 분석 쿼리 언어에 대해 설명합니다.

* **[소개 비디오 보기](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**
* 앱이 아직 데이터를 Application Insights로 전송하지 않은 경우 **[시뮬레이션된 데이터에 대한 드라이브 분석을 테스트](https://analytics.applicationinsights.io/demo)**합니다.

## Analytics 열기

Application Insights의 앱 홈 리소스에서 Analytics를 클릭합니다.

![portal.azure.com을 열고 Application Insights 리소스를 열고 Analytics를 클릭합니다.](./media/app-insights-analytics-using/001.png)

인라인 자습서에서 수행할 수 있는 몇 가지 작업을 확인할 수 있습니다.

[보다 광범위한 둘러보기는 여기](app-insights-analytics-tour.md)서 제공됩니다.

## 원격 분석 쿼리

### 쿼리 작성

![스키마 표시](./media/app-insights-analytics-using/150.png)

왼쪽에 나열된 테이블의 이름(또는 [range](app-insights-analytics-reference.md#range-operator) 또는 [union](app-insights-analytics-reference.md#union-operator) 연산자)으로 시작합니다. [연산자](app-insights-analytics-reference.md#queries-and-operators) 파이프라인을 만들려면 `|`을(를) 사용합니다. IntelliSense에 사용할 수 있는 연산자 및 식 요소 일부를 지정하라는 메시지가 표시됩니다.

[분석 언어 개요](app-insights-analytics-tour.md) 및 [언어 참조](app-insights-analytics-reference.md)를 참조하세요.

### 쿼리 실행

![쿼리 실행](./media/app-insights-analytics-using/130.png)

1. 쿼리에서는 단일 줄 바꿈을 사용할 수 있습니다.
2. 실행하려는 쿼리의 내부 또는 끝에 커서를 놓습니다.
3. 이동을 클릭하여 쿼리를 실행합니다.
4. 쿼리에 빈 줄을 넣으면 안 됩니다. 빈 줄로 구분하여 하나의 쿼리 탭에서 분리된 여러 개의 쿼리를 유지할 수 있습니다. 커서가 있는 쿼리만 실행됩니다.

### 쿼리 저장

![쿼리 저장](./media/app-insights-analytics-using/140.png)

1. 현재 쿼리 파일을 저장합니다.
2. 저장된 쿼리 파일을 엽니다.
3. 새 쿼리 파일을 만듭니다.


## 세부 정보 참조

속성의 전체 목록을 보려면 결과의 행을 확장합니다. 구조화된 값이 있는 속성(예: 사용자 지정 차원 또는 예외에 나열된 스택)을 추가로 확장할 수 있습니다.

![행 확장](./media/app-insights-analytics-using/070.png)

 

## 결과 정렬

쿼리에서 반환된 결과를 정렬하고, 필터링하고, 페이지를 매기고, 그룹화할 수 있습니다.

> [AZURE.NOTE] 브라우저에서 정렬, 그룹화 및 필터링을 수행해도 쿼리가 다시 실행되지는 않습니다. 마지막 쿼리에서 반환된 결과만 다시 정렬됩니다.
> 
> 결과가 반환하기 전에 서버에서 이러한 작업을 수행하려면 [sort](app-insights-analytics-reference.md#sort-operator), [summarize](app-insights-analytics-reference.md#summarize-operator) 및 [where](app-insights-analytics-reference.md#where-operator) 연산자를 사용하여 쿼리를 작성합니다.

보려는 열을 선택하고, 열 머리글을 끌어 다시 정렬하고, 테두리를 끌어 열 크기를 조정합니다.

![열 정렬](./media/app-insights-analytics-using/030.png)

### 항목 정렬 및 필터

열의 제목을 클릭하여 결과를 정렬합니다. 다른 방법으로 정렬하려면 다시 클릭하고, 쿼리에서 반환된 원래 순서로 되돌리려면 세 번째로 클릭합니다.

필터 아이콘을 사용하여 검색 범위를 좁힙니다.

![열 정렬 및 필터링](./media/app-insights-analytics-using/040.png)



### 항목 그룹화

둘 이상의 열로 정렬하려면 그룹화를 사용합니다. 먼저 사용하도록 설정한 후 열 머리글을 테이블 위의 공간으로 끕니다.

![그룹](./media/app-insights-analytics-using/060.png)



### 일부 결과가 누락되었나요?

포털에서 반환된 결과에 대해 약 10,000개의 행 제한이 있습니다. 이 제한을 초과하면 경고가 표시됩니다. 이런 경우 테이블의 결과를 정렬한다고 해서 항상 첫 번째 또는 마지막 실제 결과가 모두 표시되는 것은 아닙니다.

이 제한에 도달하지 않도록 하는 것이 좋습니다. 다음과 같은 연산자를 사용합니다.

* [where timestamp > ago(3d)](app-insights-analytics-reference.md#where-operator)
* [top 100 by timestamp](app-insights-analytics-reference.md#top-operator)
* [take 100](app-insights-analytics-reference.md#take-operator)
* [summarize](app-insights-analytics-reference.md#summarize-operator)



## 다이어그램

원하는 다이어그램의 유형을 선택합니다.

![다이어그램 유형 선택](./media/app-insights-analytics-using/230.png)

적합한 유형의 열이 여러 개인 경우 x축과 y축 및 결과 정렬 기준으로 사용할 크기의 열을 선택할 수 있습니다.

기본적으로 결과는 처음에는 테이블로 표시되며 다이어그램은 수동으로 선택합니다. 하지만 쿼리 끝에 [render 지시문](app-insights-analytics-reference.md#render-directive)을 사용하여 다이어그램을 선택할 수 있습니다.

## 대시보드에 고정

다이어그램을 [대시보드 공유](app-insights-dashboards.md) 중 하나에 고정할 수 있습니다. 핀을 클릭합니다. (이 기능을 설정하려면 [앱의 가격 패키지를 업그레이드](app-insights-pricing.md)해야 할 수 있습니다.)

![핀 클릭](./media/app-insights-analytics-using/pin-01.png)

즉, 웹 서비스의 성능 또는 사용 현황을 모니터링하기 위해 대시보드를 함께 사용하는 경우 매우 복잡한 분석을 기타 메트릭과 함께 포함할 수 있습니다.

#### 대시보드 새로 고침

약 30분마다 쿼리를 다시 실행하면 대시보드에 고정된 차트가 자동으로 새로 고침됩니다.

#### 자동 단순화

일부 경우 차트를 대시보드에 고정할 때 특정 단순화가 적용됩니다.

수많은 불연속 막대(일반적으로 가로 막대형 차트)를 표시하는 차트를 고정할 경우 덜 채워진 막대가 하나의 "기타" 막대로 자동으로 그룹화됩니다. 예를 들어 다음 쿼리는

    requests | summarize count_search = count() by client_CountryOrRegion

분석에서 다음과 같이 표시됩니다.


![길게 늘어지는 차트](./media/app-insights-analytics-using/pin-07.png)

하지만 대시보드에 이를 고정하면 다음과 같이 표시됩니다.


![제한된 막대가 있는 차트](./media/app-insights-analytics-using/pin-08.png)




## Excel로 내보내기

쿼리를 실행한 후 .csv 파일을 다운로드할 수 있습니다. 이렇게 하려면 **Excel로 내보내기**를 클릭합니다.

## Power BI에 내보내기

1. 쿼리에 커서를 놓고 **Power BI로 내보내기**를 선택합니다.

    ![](./media/app-insights-analytics-using/240.png)

    그러면 M 스크립트 파일이 다운로드됩니다.

3. Power BI Desktop 고급 쿼리 편집기에 M 언어 스크립트를 복사합니다.
 * 내보낸 파일을 엽니다.
 * Power BI Desktop에서 **데이터 가져오기 > 빈 쿼리 > 고급 편집기**를 선택하고 M 언어 스크립트를 붙여넣습니다.

    ![](./media/app-insights-analytics-using/250.png)

4. 필요에 따라 자격 증명을 편집한 후 보고서를 작성할 수 있습니다.

    ![](./media/app-insights-analytics-using/260.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0817_2016-->