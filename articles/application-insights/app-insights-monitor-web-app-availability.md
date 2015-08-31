<properties 
	pageTitle="웹 사이트의 가용성 및 응답성 모니터링" 
	description="Application Insights에서 웹 테스트를 설정합니다. 웹 사이트가 사용할 수 없게 되거나 느리게 응답하는 경우 알림이 제공됩니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="07/08/2015" 
	ms.author="awills"/>
 
# 웹 사이트의 가용성 및 응답성 모니터링

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

웹 응용 프로그램을 배포한 후 가용성 및 응답성을 모니터링하도록 웹 테스트를 설정할 수 있습니다. Application Insights는 전 세계에서 정기적으로 웹 요청을 보내 응용 프로그램이 느리게 응답하거나 전혀 응답하지 않을 경우 알려줄 수 있습니다.

![웹 테스트의 예](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

공용 인터넷에서 액세스 가능한 모든 HTTP 끝점에 대해 웹 테스트를 설정할 수 있습니다.

웹 테스트에는 두 가지 유형이 있습니다:

* [URL ping 테스트](#set-up-a-url-ping-test): Azure 포털에서 만들 수 있는 간단한 테스트입니다.
* [다단계 웹 테스트](#multi-step-web-tests): Visual Studio Ultimate 또는 Visual Studio Enterprise에서 만들고 포털에 업로드합니다.



## URL ping 테스트를 설정

### <a name="create"></a>1. 새 리소스를 만드나요?

이 응용 프로그램에 대한 [Application Insights 리소스를 설정시작했고][start] 동일한 위치에서 가용성 데이터를 확인하려는 경우 이 단계를 건너뜁니다.

[Microsoft Azure](http://azure.com)에 등록하고 [Azure 포털](https://portal.azure.com)로 이동한 후 새 Application Insights 리소스를 만듭니다.

![새로 만들기 > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

새 리소스에 대한 개요 블레이드가 열립니다. 언제든지 이것을 [Azure 포털](https://portal.azure.com)에서 찾으려면, 찾아보기를 클릭합니다.

### <a name="setup"></a>2. 웹 테스트 만들기

Application Insights 리소스에서 가용성 타일을 찾습니다. 이것을 클릭하여 응용 프로그램에 대한 웹 테스트 블레이드를 열고 웹 테스트를 추가합니다.

![웹 사이트의 최소 URL 채우기](./media/app-insights-monitor-web-app-availability/13-availability.png)

- **URL**은 공용 인터넷에서 볼 수 있어야 합니다. 여기에는 쿼리 문자열이 포함될 수 있으므로 데이터베이스 사용을 연습해 볼 수 있습니다. URL이 리디렉션으로 확인되면 최대 10개의 리디렉션을 따릅니다.

- **다시 시도를 사용**하도록 설정한 경우, 테스트에 실패하면 잠시 후에 다시 시도합니다. 연속 된 세 번의 시도가 실패하는 경우에 실패가 보고됩니다. 후속 테스트는 일반적인 간격으로 수행됩니다. 다음 성공까지 다시 시도는 일시적으로 중단됩니다. 이 규칙은 각 테스트 위치에서 독립적으로 적용됩니다.

- **테스트 위치**는 서버가 URL로 웹 요청을 보내는 곳입니다. 웹 사이트의 문제와 네트워크 문제를 구분할 수 있도록 한 가지 이상을 선택합니다. 최대 16 개의 위치를 선택할 수 있습니다.

- **성공 조건**:

    **HTTP 상태 코드**: 200 is usual.

    "Welcome!"과 같은 **콘텐츠 일치** 문자열. 이 문자열이 모든 응답에 나타나는지 테스트할 것입니다. 와일드카드 없는 일반 문자열이어야 합니다. 페이지 내용이 변경되면 업데이트해야 할 수 있습니다.


- 기본적으로 15분 동안 오류가 반복되는 경우 **알림**이 전송됩니다. 그러나 다소 민감하게 작동하도록 변경할 수 있으며, 알림을 받는 메일 주소도 변경할 수 있습니다.

#### 더 많은 URL 테스트

테스트를 더 추가 합니다. 예를 들어 홈페이지를 테스트할 수 있을 뿐 아니라 검색을 위한 URL을 테스트하여 데이터베이스가 실행되고 있는지 확인할 수 있습니다.


### <a name="monitor"></a>3. 가용성 보고서 보기

1-2분 후 가용성/웹 테스트 블레이드에서 새로 고침을 클릭합니다. 이 버전에서는 새로 고침이 자동으로 수행되지 않습니다.

![홈 블레이드에 대한 요약 결과](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

기간의 보다 자세한 정보를 보려면 위의 요약 차트에서 아무 막대나 클릭합니다.

이 차트들은 이 응용 프로그램의 웹테스트에 대해나 결과들의 합입니다.

#### 웹 페이지의 구성 요소

사용자가 테스트하는 웹 페이지의 이미지, 스타일 시트, 스크립트 그리고 다른 정적 구성 요소는 테스트의 일부로 요청됩니다.

기록된 응답 시간은 모든 구성 요소가 로딩을 완료할 때 걸린 시간입니다.

구성 요소를 로드하지 못하는 경우 테스트가 실패로 표시됩니다.

## <a name="failures"></a>오류가 표시되는 경우...

빨간 점을 클릭합니다.

![빨간 점을 클릭 합니다.](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

또는, 아래로 스크롤하여 100% 성공보다 작은 테스트를 클릭합니다.

![특정 웹 테스트 클릭](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

해당 테스트의 결과를 보여줍니다.

![특정 웹 테스트 클릭](./media/app-insights-monitor-web-app-availability/16-1test.png)

여러 위치에서 테스트를 실행합니다 - (결과 중에서 100% 미만을 하나 선택하십시오.)

![특정 웹 테스트 클릭](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)


아래로 스크롤하여 **실패한 테스트**로 이동하고 결과를 선택합니다.

결과를 포털에서 평가하려면 클릭하고 실패한 이유를 참조하십시오.

![웹 테스트 실행 결과](./media/app-insights-monitor-web-app-availability/18-availDetails.png)


또는 결과 파일을 다운로드하여 Visual Studio에서 검사할 수 있습니다.


*정상으로 보이지만 실패로 보고되었습니까?* 모든 이미지, 스크립트, 스타일 시트 및 페이지에 의해 로드된 다른 파일을 확인합니다. 그 중 하나라도 실패하면, 기본 html 페이지가 확인을 로드하는 경우에도 테스트는 실패로 보고됩니다.



## 다중 단계 웹 테스트

URL 시퀀스를 포함하는 시나리오를 모니터링할 수 있습니다. 예를 들어 판매 웹 사이트를 모니터링하는 경우 장바구니에 항목을 제대로 추가할 수 있는지 테스트할 수 있습니다.

다단계 테스트를 만들려면 Visual Studio를 사용하여 시나리오를 기록한 다음 Application Insights에 기록을 업로드합니다. Application Insights는 지정된 간격에 따라 시나리오를 재생하고 응답을 확인합니다.

#### 1\. 시나리오 기록

Visual Studio Ultimate를 사용하여 웹 세션을 기록합니다.

1. 웹 성능 테스트 프로젝트를 만듭니다.

    ![Visual Studio에서 웹 성능 및 부하 테스트 템플릿으로 새 프로젝트를 만듭니다.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

2. .webtest 파일을 열고 기록을 시작합니다.

    ![.webtest 파일을 열고 레코드를 클릭합니다.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)

3. 웹사이트를 열고 장바구니에 제품을 추가하는 등 테스트에서 시뮬레이션 하려는 사용자 작업을 수행합니다. 테스트를 중지합니다.

    ![웹 테스트 레코더는 Internet Explorer에서 실행합니다.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    시나리오를 길게 만들지 마세요. 100개 단계와 2 분으로 제한됩니다.

4. Visual Studio에서 테스트를 실행하여 제대로 작동하는지 확인합니다.

    웹 test runner에서 웹 브라우저가 열리고 기록한 작업을 반복합니다. 예상대로 작동하는지 확인합니다.

    ![Visual Studio에서 .webtest 파일을 열고 실행을 클릭합니다.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)
 

(웹 테스트코드에 루프를 삽입하지 마세요.)

#### 2\. Application Insights에 웹 테스트를 업로드

1. Application Insights 포털에서 새 웹 테스트를 만듭니다.

    ![웹 테스트 블레이드에서 추가를 선택합니다.](./media/app-insights-monitor-web-app-availability/16-another-test.png)

2. 다단계 테스트를 선택하고 .webtest 파일을 업로드합니다.

    ![다단계 웹 테스트를 선택합니다.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

단일 URL 테스트의 경우와 마찬가지로 테스트 결과 및 발생한 오류를 확인합니다.

실패의 일반적인 이유는 테스트를 너무 오래 실행하기 때문입니다. 2분 이내로 실행해야 합니다.

테스트가 성공하려면 스크립트, 스타일 시트, 이미지 등을 포함하는 페이지의 모든 리소스가 제대로 로드되어야 한다는 점을 잊지 마십시오.


### 다단계 테스트에 시간 및 난수 연결

외부 피드에서 주식과 같이 시간에 따라 변하는 데이터를 가져오는 도구를 테스트한다고 가정합니다. 웹 테스트를 기록할 때 특정 시간을 사용해야 하지만 테스트 매개 변수로 StartTime 및 EndTime을 설정할 수 있습니다.

![매개 변수를 가진 웹 테스트입니다.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

테스트를 실행하면 EndTime이 항상 현재 시간이 되고 StartTime은 15 분이 됩니다.

웹 테스트 플러그 인을 사용하여 이 작업을 수행할 수 있습니다.

1. 원하는 각 가변 매개 변수 값에 대한 웹 테스트 플러그 인을 추가합니다. 웹 테스트 도구 모음에서 **웹 테스트 플러그 인 추가**를 선택합니다.

    ![웹 테스트 플러그인 추가를 선택하고 형식을 선택합니다.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    이 예에서는 날짜 시간 플러그인의 두 인스턴스를 사용하겠습니다. 한 인스턴스는 "15 분 전"이고 다른 하나는 "지금은"입니다.

2. 각 플러그인의 속성을 엽니다. 이름을 지정하고 현재 시간을 사용하도록 설정합니다. 둘 중 하나에 대해 Add Minutes = -15를 설정합니다.

    ![이름을 설정하고, 현재 시간을 사용하며 시간을 추가합니다.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. 웹 테스트 매개 변수에 {{플러그 인 이름}을(를) 사용하여 플러그 인 이름을 참조합니다.

    ![테스트 매개 변수에서 {{플러그인 이름}}을(를) 사용합니다.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

이제 테스트를 포털에 업로드합니다. 테스트의 모든 실행에 동적 값을 사용합니다.

## <a name="edit"></a>테스트 편집 또는 사용 안 함

개별 테스트를 열어서 편집하거나 사용하지 않도록 설정합니다.

![웹 테스트 편집 또는 사용 안 함](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

서비스에 대한 유지 관리를 수행하는 동안 웹 테스트를 사용하지 않도록 설정할 수 있습니다.

## 질문이 있으십니까? 문제가 있습니까?

* 다음과 같은 경우 "잘못된 문자"에 대한 오류가 발생합니다. 

* **"웹 테스트" 및 "가용성" 간의 차이가 있습니까?

    두 용어는 같은 의미로 사용됩니다.



## <a name="video"></a>동영상

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>다음 단계

[진단 로그 검색][diagnostic]

[문제 해결][qna]




<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md

 

<!---HONumber=August15_HO8-->