<properties 
	pageTitle="웹 사이트의 가용성 및 응답성 모니터링" 
	description="Application Insights에서 웹 테스트를 설정합니다. 웹 사이트가 사용할 수 없게 되거나 느리게 응답하는 경우 알림이 제공됩니다.." 
	services="application-insights" 
documentationCenter=""
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/27/2015" 
	ms.author="awills"/>
 
# 웹 사이트의 가용성 및 응답성 모니터링

웹 응용 프로그램을 배포한 후 가용성 및 응답성을 모니터링하도록 웹 테스트를 설정할 수 있습니다. Application Insights는 전 세계에서 정기적으로 웹 요청을 보내 응용 프로그램이 느리게 응답하거나 전혀 응답하지 않을 경우 알려줄 수 있습니다.

![Web test example](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

공용 인터넷에서 액세스 가능한 모든 HTTP 끝점에 대해 웹 테스트를 설정할 수 있습니다.

*Azure 웹 사이트인가요? [웹 사이트 블레이드][azurewebtest]에서 웹 테스트를 만들기만 하면 됩니다.*


1. [새 리소스를 만드나요?](#create)
1. [웹 테스트 설정](#setup)
1. [결과 보기](#monitor)
2. [오류가 표시되는 경우...](#failures)
2. [다중 단계 웹 테스트](#multistep)
1. [테스트 편집 또는 사용 안 함](#edit)


 [비디오](#video)
 [다음 단계](#next)

## 웹 테스트 설정

### <a name="create"></a>1. 새 리소스를 만드나요?

이 응용 프로그램에 대한 [Application Insights 리소스를 설정][시작]했고 동일한 위치에서 가용성 데이터를 확인하려는 경우 이 단계를 건너뜁니다.

[Microsoft Azure](http://azure.com)에 등록하고 [미리 보기 포털](https://portal.azure.com)로 이동한 후 새 Application Insights 리소스를 만듭니다. 

![New > Application Insights](./media/app-insights-monitor-web-app-availability/appinsights-11newApp.png)

### <a name="setup"></a>2. 웹 테스트 만들기

응용 프로그램의 개요 블레이드에서 웹 테스트 타일까지 클릭해서 이동합니다. 

![Click the empty availability test](./media/app-insights-monitor-web-app-availability/appinsights-12avail.png)

*이미 웹 테스트가 있나요? 웹 테스트 타일까지 클릭해 간 다음 웹 테스트 추가를 선택합니다.*

테스트 세부 정보를 설정합니다.

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/appinsights-13availChoices.png)

- **URL**은 공용 인터넷에서 볼 수 있어야 합니다. 여기에는 쿼리 문자열이 포함될 수 있으므로 데이터베이스 사용을 연습해 볼 수 있습니다. URL이 리디렉션으로 확인되면 최대 10개의 리디렉션을 따릅니다.

- **테스트 위치**는 서버가 URL로 웹 요청을 보내는 곳입니다. 웹 사이트의 문제와 네트워크 문제를 구분할 수 있도록 2 ~ 3가지를 선택합니다. 3개 이하만 선택할 수 있습니다.

- **성공 조건**:
    **HTTP 반환 코드**: 200이 일반적입니다. 

    **"Welcome!"과 같은 콘텐츠** 일치 문자열. 이 문자열이 모든 응답에 나타나는지 테스트할 것입니다. 와일드카드 없는 일반 문자열이어야 합니다. 페이지 내용이 변경되면 업데이트해야 할 수 있습니다.

- **알림**: 기본적으로 15분 동안 오류가 반복되는 경우 알림이 전송됩니다. 그러나 다소 민감하게 작동하도록 변경할 수 있으며, 알림을 받는 메일 주소도 변경할 수 있습니다.

#### 더 많은 URL 테스트

원하는 만큼 많은 URL에 대한 테스트를 더 추가할 수 있습니다. 예를 들어 홈페이지를 테스트할 수 있을 뿐 아니라 검색을 위한 URL을 테스트하여 데이터베이스가 실행되고 있는지 확인할 수 있습니다.

![On the web tests blade, choose Add](./media/app-insights-monitor-web-app-availability/appinsights-16anotherWebtest.png)


### <a name="monitor"></a>3. 가용성 보고서 보기

1-2분 후에 개요 블레이드에서 새로 고침을 클릭합니다. 이 버전에서는 새로 고침이 자동으로 수행되지 않습니다.

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/appinsights-14availSummary.png)

개요 블레이드의 차트는 이 응용 프로그램의 모든 웹 테스트에 결과를 조합해서 표시합니다.

#### 페이지구성 요소

이미지, 스타일 시트와 스크립트 및 기타 정적 구성 요소는 테스트의 일부로 요청됩니다.  

기록된 응답 시간은 모든 구성 요소가 로딩을 완료할 때 걸린 시간입니다.

구성 요소를 로드하지 못하는 경우 테스트가 실패로 표시됩니다.

## <a name="failures"></a>오류가 표시되는 경우...

웹 테스트 블레이드까지 클릭하여 각 테스트의 개별 결과를 확인합니다.

특정 웹 테스트를 엽니다.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/appinsights-15webTestList.png)

아래로 스크롤하여 **실패한 테스트**로 이동한 후 결과를 선택합니다.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/appinsights-17-availViewDetails.png)

결과는 실패 이유를 보여 줍니다.

![Webtest run result](./media/app-insights-monitor-web-app-availability/appinsights-18-availDetails.png)

자세한 내용을 보려면 결과 파일을 다운로드하여 Visual Studio에서 살펴봅니다.



##<a name="multistep"></a>다중 단계 웹 테스트

URL 시퀀스를 포함하는 시나리오를 모니터링할 수 있습니다. 예를 들어 판매 웹 사이트를 모니터링하는 경우 장바구니에 항목을 제대로 추가할 수 있는지 테스트할 수 있습니다. 

다단계 테스트를 만들려면 Visual Studio를 사용하여 시나리오를 기록한 다음 Application Insights에 기록을 업로드합니다. Application Insights는 지정된 간격에 따라 시나리오를 재생하고 응답을 확인합니다.

#### 1. 시나리오 기록

Visual Studio Ultimate를 사용하여 웹 세션을 기록합니다.

1. 웹 성능 테스트 프로젝트를 만듭니다.
    ![In Visual Studio, create a new project from the Web Performance and Load Test template.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)
2. .webtest 파일을 열고 기록을 시작합니다.
    ![Open the .webtest file and click Record.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. 테스트에서 웹 사이트를 열고 장바구니에 제품을 추가하는 등의 시뮬레이션할 사용자 작업을 수행합니다. 테스트를 중지합니다. 
    ![The web test recorder runs in Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)
    시나리오를 길게 만들지 마세요. 100개 단계와 2 분으로 제한됩니다.
4. Visual Studio에서 테스트를 실행하여 제대로 작동하는지 확인합니다.
    웹 test runner에서 웹 브라우저가 열리고 기록한 작업을 반복합니다. 예상대로 작동하는지 확인합니다. 
    ![In Visual Studio, open the .webtest file and click Run.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)
 

(웹 테스트코드에 루프를 삽입하지 마세요.)

#### 2. Application Insights에 웹 테스트를 업로드합니다.

Application Insights 포털에서 새 웹 테스트를 만듭니다.

![On the web tests blade, choose Add.](./media/app-insights-monitor-web-app-availability/appinsights-16anotherWebtest.png)

다단계 테스트를 선택하고 .webtest 파일을 업로드합니다.

![Select multi-step webtest.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

단일 URL 테스트의 경우와 마찬가지로 테스트 결과 및 발생한 오류를 확인합니다. 

실패의 일반적인 이유는 테스트를 너무 오래 실행하기 때문입니다. 2분 이내로 실행해야 합니다.


### 다단계 테스트에 시간 및 난수 연결

외부 피드에서 주식과 같이 시간에 따라 변하는 데이터를 가져오는 도구를 테스트한다고 가정합니다. 웹 테스트를 기록할 때 특정 시간을 사용해야 하지만 테스트 매개 변수로 StartTime 및 EndTime을 설정할 수 있습니다.

![A web test with parameters.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

테스트를 실행하면 EndTime이 항상 현재 시간이 되고 StartTime은 15 분이 됩니다.

웹 테스트 플러그 인을 사용하여 이 작업을 수행할 수 있습니다.

1. 원하는 각 가변 매개 변수 값에 대한 웹 테스트 플러그 인을 추가합니다. 웹 테스트 도구 모음에서 **웹 테스트 플러그 인 추가**를 선택합니다.

    ![Choose Add Web Test Plugin and select a type.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    이 예에서는 날짜 시간 플러그 인의 두 인스턴스를 사용합니다. 인스턴스 하나는 "15 분 전"에 대한 것이고 다른 하나는 "지금"에 대한 것입니다. 

2. 각 플러그 인의 속성을 열고 이름을 지정한 후 현재 시간을 사용하도록 설정합니다. 둘 중 하나에 대해 Add Minutes = -15를 설정합니다.

    ![Set name, Use Current Time, and Add Minutes.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. 웹 테스트 매개 변수에 {{플러그 인 이름}을(를) 사용하여 플러그 인 이름을 참조합니다.

    ![In the test parameter, use {{plug-in name}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

이제 테스트를 포털에 업로드합니다. 테스트의 모든 실행에 동적 값을 사용합니다.

## <a name="edit"></a> 테스트 편집 또는 사용 안 함

개별 테스트를 열어서 편집하거나 사용하지 않도록 설정합니다.

![Edit or disable a web test](./media/app-insights-monitor-web-app-availability/appinsights-19-availEdit.png)

서비스에 대한 유지 관리를 수행하는 동안 웹 테스트를 사용하지 않도록 설정할 수 있습니다.

## <a name="video"></a>비디오

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>다음 단계

[진단 로그 검색][진단]

[문제 해결][qna]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




[azurewebtest]: ../insights-create-web-tests/

<!--HONumber=46--> 
 
