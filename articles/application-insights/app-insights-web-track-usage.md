<properties 
	pageTitle="웹 응용 프로그램에서 사용 현황 추적" 
	description="사용자 활동을 기록합니다." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>
 
# 웹 응용 프로그램에서 사용 현황 추적

웹 응용 프로그램이 사용되는 방식을 알아보세요. 사용 현황 분석을 설정하면 사용자들이 보는 페이지, 방문하는 사용자 수 및 사이트 방문 빈도를 확인할 수 있습니다. 몇 가지 [사용자 지정 이벤트 및 메트릭][track]을 추가하고 가장 인기 있는 기능, 가장 일반적인 실수를 자세히 분석하고 사용자와 더불어 성공적인 결과를 얻을 수 있게 앱을 조정합니다.

클라이언트와 서버 모두에서 원격 분석이 수집됩니다. 클라이언트 데이터는 모든 최신 웹 브라우저에서 수집되며, 플랫폼이 ASP.NET인 경우 서버 데이터가 수집될 수 있습니다. Azure에서 실행될 필요는 없습니다. 

* [웹 사용 현황 분석 설정](#webclient)
* [사용 현황 분석](#usage)
* [단일 페이지 앱에 대한 사용자 지정 페이지 수](#spa)
* [개별 페이지 이벤트 검사](#inspect)
* [사용자 지정 이벤트 및 메트릭을 통한 자세한 추적](#custom)
* [비디오](#video)

## <a name="webclient"></a> 웹 클라이언트 분석 설정

#### Azure에서 Application Insights 리소스 가져오기

**ASP.NET 앱을 개발하며** 이 작업을 아직 수행하지 않은 경우 [웹 프로젝트에 Application Insights를 추가합니다][start]. 

**웹 사이트 플랫폼이 ASP.NET이 아닌 경우:** [Microsoft Azure](http://azure.com)에 등록하고 [미리 보기 포털](https://portal.azure.com)로 이동하여 Application Insights 리소스를 추가합니다.

![](./media/app-insights-web-track-usage/appinsights-11newApp.png)

(나중에 찾아보기 단추를 클릭하여 다시 돌아올 수 있습니다.)



#### 웹 페이지에 스크립트 추가

빠른 시작에서 웹 페이지용 스크립트를 가져옵니다.

![](./media/app-insights-web-track-usage/appinsights-06webcode.png)

추적하려는 모든 페이지의 &lt;/head&gt; 태그 바로 앞에 스크립트를 삽입합니다. 웹 사이트에 마스터 페이지가 있는 경우 이 페이지에 스크립트를 넣을 수 있습니다. 예를 들어 ASP.NET MVC 프로젝트에서는 View\Shared_Layout.cshtml에 추가합니다.

## <a name="usage"></a>사용 현황 분석

웹 사이트를 실행하고 이를 사용하여 원격 분석을 생성한 후 1-2분 정도 기다립니다. 개발 컴퓨터에서 F5 키를 눌러 실행하거나 서버에 배포할 수 있습니다.

응용 프로그램 개요 블레이드에는 다음과 같은 사용 현황 타일이 표시됩니다.

![](./media/app-insights-web-track-usage/appinsights-47usage.png)

*아직 아무 데이터도 없나요? 페이지 위쪽에서 **새로 고침**을 클릭합니다.*

* **브라우저별 세션**

    *세션*은 사용자가 웹 사이트에서 페이지를 열 때 시작되고 30분의 제한 시간 동안 웹 요청을 보내지 않으면 끝나는 기간입니다. 

    클릭하면 차트를 확대할 수 있습니다.

* **상위 페이지 보기**

    지난 24시간 동안의 총 페이지 보기 횟수가 표시됩니다.

    페이지 보기 타일을 클릭하면 보다 자세한 기록을 볼 수 있습니다. 더 긴 기록을 보려면 보고서의 시간 범위를 변경하면 됩니다.

![](./media/app-insights-web-track-usage/appinsights-49usage.png)


차트를 클릭하여 표시할 수 있는 다른 메트릭을 확인하거나 새 차트를 추가하고 표시되는 메트릭을 선택합니다.

![](./media/app-insights-web-track-usage/appinsights-63usermetrics.png)

> [AZURE.NOTE] 메트릭을 모두 사용하려면 *모든* 메트릭을 선택 취소합니다. 메트릭이 몇 가지 조합으로만 표시될 수 있습니다. 메트릭을 하나 선택하면 호환되지 않는 다른 메트릭을 사용할 수 없게 됩니다.



## <a name="spa"></a> 단일 페이지 앱에 대한 사용자 지정 페이지 수

기본적으로는 새 페이지를 클라이언트 브라우저로 로드할 때마다 페이지 수가 계산됩니다.  그러나 추가 페이지 보기를 계산에 포함할 수도 있습니다. 예를 들어 탭에 콘텐츠가 표시되는 페이지에서 사용자가 탭을 전환할 때 페이지 수를 계산하도록 지정할 수 있습니다. 또는 페이지의 JavaScript 코드가 브라우저 URL은 변경하지 않고 새 콘텐츠를 로드할 수도 있습니다. 

클라이언트 코드의 적절한 지점에 다음과 같은 JavaScript 호출을 삽입합니다.

    appInsights.trackPageView(myPageName);

페이지 이름은 URL과 같은 문자를 포함할 수 있지만 "#" 또는 "?" 뒤의 모든 문자는 무시됩니다.


## <a name="inspect"></a> 개별 페이지 보기 이벤트 검사

일반적으로 페이지 보기 원격 분석은 Application Insights에서 분석하며 모든 사용자에 대해 계산된 평균이 포함된 누적 보고서만 표시됩니다. 그러나 디버깅을 위해 개별 페이지 보기 이벤트를 확인할 수도 있습니다.

진단 검색 블레이드에서 필터를 페이지 보기로 설정합니다.

![](./media/app-insights-web-track-usage/appinsights-51searchpageviews.png)

보다 자세한 정보를 확인하려면 원하는 이벤트를 선택합니다.

> [AZURE.NOTE] [검색][diagnostic]을 사용하는 경우 단어 단위를 사용해야 합니다. 예를 들어 "Abou"와 "bout"는 "About"과 일치하지 않지만 "Abou* "는 일치합니다. 또한 검색 용어는 와일드로 시작할 수 없습니다. 예를 들어 "*bou"로 검색해도 "About"는 검색되지 않습니다. 

> [진단 검색에 대해 자세히 알아보기][diagnostic]

## <a name="custom"></a> 사용자 지정 이벤트 및 메트릭을 통한 자세한 추적

사용자가 앱으로 어떤 작업을 수행하려고 하는지 확인하고 싶나요? 클라이언트 및 서버 코드에 호출을 삽입하여 사용자 고유의 원격 분석을 Application Insights로 보낼 수 있습니다. 예를 들어 주문은 작성했으나 완료하지 않은 사용자의 수, 가장 자주 발생하는 유효성 검사 오류 또는 게임의 평균 점수를 확인할 수 있습니다.

[사용자 지정 이벤트 및 메트릭 API에 대해 자세히 알아보기][track]

## <a name="video"></a> 비디오: 사용 현황 추적

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> 다음 단계

[사용자 지정 이벤트 및 메트릭으로 사용량 추적][track](영문)




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]





<!--HONumber=35.2-->

<!--HONumber=46--> 
 