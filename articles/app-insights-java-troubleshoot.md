<properties 
	pageTitle="Java 웹 프로젝트에서 Application Insights 문제 해결" 
	description="문제 해결 가이드 및 질문과 대답입니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="awills"/>
 
# Java용 Application Insights 문제 해결 및 Q&A

[Java의 Visual Studio Application Insights][java]와 관련된 질문이나 문제가 있나요? 다음은 몇 가지 팁입니다.


## 빌드 오류

*Eclipse에서 Maven 또는 Gradle을 통해 Application Insights SDK를 추가할 때 빌드 또는 체크섬 유효성 검사 오류가 표시됩니다.*

* 종속성 <version> 요소가 와일드카드 문자와 함께 패턴을 사용하는 경우(예: <version>[0.9,)</version>) <version>0.9.1</version>과 같이 특정 버전을 대신 지정합니다.

## 데이터 없음 

*Application Insights를 추가하고 내 앱을 실행했는데 포털에 데이터가 표시되지 않습니다.*

* 잠시 기다린 후 새로 고침을 클릭합니다. 현재 새로 고침이 자동으로 수행되지 않습니다.
* ApplicationInsights.xml 파일(프로젝트의 리소스 폴더에 있음)에 계측 키가 정의되어 있는지 확인합니다.
* xml 파일에 `<DisableTelemetry>true</DisableTelemetry>` 노드가 없는지 확인합니다.
* 방화벽에서 dc.services.visualstudio.com 및 f5.services.visualstudio.com으로 나가는 트래픽에 대해 TCP 포트 80 및 443을 열어야 할 수 있습니다.
* Microsoft Azure 시작 보드에서 서비스 상태 맵을 살펴보세요. 어떤 경고 표시가 있는 경우 정상으로 돌아갈 때까지 기다린 후 Application Insights 응용 프로그램 블레이드를 닫고 다시 엽니다.
* ApplicationInsights.xml 파일(프로젝트의 리소스 폴더에 있음)의 루트 노드 아래에 <SDKLogger /> 요소를 추가하여 IDE 콘솔 창에 기록을 켜고 앞에 [Error]가 붙은 항목을 확인합니다.


## 사용 현황 데이터 없음

*요청 및 응답 시간에 대한 데이터는 표시되는데 페이지 보기, 브라우저 또는 사용자 데이터는 표시되지 않습니다.*

서버에서 원격 분석을 보내도록 앱을 설정했습니다. 이제 다음 단계로 [웹 브라우저에서 원격 분석을 보내도록 웹 페이지를 설정][usage]해야 합니다.

또는 클라이언트가 [휴대폰이나 기타 장치][platforms]의 앱인 경우 해당 장치에서 원격 분석을 보낼 수 있습니다. 

동일한 계측 키를 사용하여 클라이언트 및 서버 원격 분석을 둘 다 설정합니다. 데이터가 동일한 Application Insights 리소스에 표시되며, 클라이언트와 서버의 이벤트에 상관 관계를 지정할 수 있습니다.



## 원격 분석 사용 안 함

*원격 분석 수집을 사용하지 않도록 설정하려면 어떻게 해야 하나요?*

코드:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**또는** 

ApplicationInsights.xml(프로젝트의 리소스 폴더에 있음)을 업데이트합니다. 루트 노드 아래에 다음을 추가합니다.

    <DisableTelemetry>true</DisableTelemetry>

XML 메서드를 사용하여 값 변경 시 응용 프로그램을 다시 시작해야 합니다.

## 대상 변경

*내 프로젝트에서 데이터를 보내는 Azure 리소스를 변경하려면 어떻게 해야 하나요?*

* [새 리소스의 계측 키를 가져옵니다.][java]
* Azure Toolkit for Eclipse를 사용하여 프로젝트에 Application Insights를 추가한 경우 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure**, **Application Insights 구성**을 차례로 선택한 다음 키를 변경합니다.
* 그렇지 않으면 프로젝트의 리소스 폴더에 있는 ApplicationInsights.xml에서 키를 업데이트합니다.


## Azure 시작 화면

*[Azure 포털](http://portal.azure.com)을 보고 있습니다. 맵에 내 앱에 대한 정보가 표시되나요?*

아니요, 전 세계 Azure 서버의 상태가 표시됩니다.

*Azure 시작 보드(홈 화면)에서 내 앱에 대한 데이터를 찾으려면 어떻게 해야 하나요?*

[Application Insights에 대해 앱을 설정][java]한 경우 찾아보기를 클릭하고 Application Insights를 선택한 다음 앱을 위해 만든 앱 리소스를 선택합니다. 이후에 더 빨리 액세스하기 위해 앱을 시작 보드에 고정할 수 있습니다.

## 인트라넷 서버

*내 인트라넷의 서버를 모니터링할 수 있나요?*

예, 서버가 공용 인터넷을 통해 Application Insights 포털에 원격 분석을 보낼 수 있는 경우 가능합니다. 

방화벽에서 dc.services.visualstudio.com 및 f5.services.visualstudio.com으로 나가는 트래픽에 대해 TCP 포트 80 및 443을 열어야 할 수 있습니다.

## 데이터 보존 

*데이터가 포털에 얼마나 오래 보존되나요? 안전한가요?*

[데이터 보존 및 개인 정보][data]를 참조하세요.

## 다음 단계

*내 Java 서버 앱에 대해 Application Insights를 설정했습니다. 다른 어떤 작업을 할 수 있나요?*

* [웹 페이지의 가용성 모니터링][availability]
* [웹 페이지 사용 모니터링][usage]
* [장치 앱의 사용 추적 및 문제 진단][platforms]
* [코드를 작성하여 앱의 사용 추적][track]
* [진단 로그 캡처][javalogs]


## 도움말 보기

* [스택 오버플로](http://stackoverflow.com/questions/tagged/ms-application-insights)

[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=49-->