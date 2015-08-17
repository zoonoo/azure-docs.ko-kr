<properties 
	pageTitle="Windows 장치에서 Application Insights 문제 해결" 
	description="문제 해결 가이드 및 질문과 대답입니다." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="awills"/>
 
# Windows 장치용 Application Insights 문제 해결 및 질문과 대답

[Windows의 Visual Studio Application Insights][windows]와 관련된 질문이나 문제가 있나요? 다음은 몇 가지 팁입니다.



## 데이터 없음 

*Application Insights를 추가하고 내 앱을 실행했는데 포털에 데이터가 표시되지 않습니다.*

* 잠시 기다린 후 새로 고침을 클릭합니다. 현재 새로 고침이 자동으로 수행되지 않습니다.
* ApplicationInsights.config 파일에 정의된 계측 키가 있고 Application Insights 포털의 키와 동일한지 확인합니다. 키를 보려면 개요 블레이드에서 필수 패키지를 클릭합니다.
* 앱에서 [나가는 네트워크 액세스를 요청](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx)하는지 확인합니다.
* 에뮬레이터 또는 테스트 장치와 Application Insights 포털 사이에 방화벽이 있나요? dc.services.visualstudio.com 및 f5.services.visualstudio.com으로 나가는 트래픽에 대해 TCP 포트 80 및 443을 열어야 할 수 있습니다.
* Microsoft Azure 시작 보드에서 서비스 상태 맵을 살펴보세요. 어떤 경고 표시가 있는 경우 정상으로 돌아갈 때까지 기다린 후 Application Insights 응용 프로그램 블레이드를 닫고 다시 엽니다.


#### 데이터를 보는 데 중지되었습니다.

* [상태 블로그](http://blogs.msdn.com/b/applicationinsights-status/)를 참조하세요.
* 데이터 요소의 월간 할당량에 도달했습니까? 설정/할당량 및 가격을 열어 알아봅니다. 그렇다면 계획을 업그레이드하거나 추가 용량에 대한 비용을 지불할 수 있습니다. [가격 체계](http://azure.microsoft.com/pricing/details/application-insights/)를 참조하세요.


## Application Insights를 유니버설 앱에 추가하려면 어떻게 해야 하나요?

솔루션의 각 장치 프로젝트에 NuGet 패키지를 수동으로 추가합니다. [시작 - 유니버설 앱][universal]을 참조하세요.

## 원격 분석 사용 안 함

*원격 분석 수집을 사용하지 않도록 설정하려면 어떻게 해야 하나요?*

코드:

    TelemetryConfiguration config = TelemetryConfiguration.Active;
    config.TrackingIsDisabled = true;

## 대상 원본 변경

*내 프로젝트에서 데이터를 보내는 Application Insights 리소스를 변경하려면 어떻게 해야 하나요?*

새 Application Insights 개요 블레이드에서 필수 패키지를 열고 계측 키를 복사합니다.

`<InstrumentationKey>` 노드에서 ApplicationInsights.config 파일에 키를 붙여넣습니다.

또는 런타임에 대상을 변경하려는 경우 다음을 사용합니다.

     var telemetry = new TelemetryClient();
     telemetry.Context.InstrumentationKey = newKey;
    
## 클라이언트-서버 앱을 모니터링하려면 어떻게 해야 하나요?

다음 두 가지 방법이 있습니다.

* 클라이언트용과 서버용의 Application Insights 리소스(서로 다른 계측 키) 두 개를 만듭니다. 그러나 동일한 Azure 리소스 그룹에 만들어야 합니다. 그러면 두 리소스 간에 쉽게 전환할 수 있습니다.
* 하나의 Application Insights 리소스를 사용하고 해당 계측 키를 클라이언트 및 서버 프로젝트 둘 다에 둡니다. 그러면 두 원본의 메트릭 및 이벤트에 대한 상관 관계를 지정할 수 있습니다.

클라이언트와 서버의 이벤트에 대한 상관 관계를 쉽게 지정하려면 각 요청에 대한 작업 ID를 생성합니다. 클라이언트와 서버 간에 전송하고 양쪽 끝의 원격 분석에 추가합니다.

    telemetry.Context.OperationId = opid;


## Azure 시작 화면

*[Azure 포털](http://portal.azure.com)을 보고 있습니다. 맵에 내 앱에 대한 정보가 표시되나요?*

아니요, 전 세계 Azure 서버의 상태가 표시됩니다.

*Azure 시작 보드(홈 화면)에서 내 앱에 대한 데이터를 찾으려면 어떻게 해야 하나요?*

[Application Insights에 대해 앱을 이미 설정][windows]한 경우 찾아보기를 클릭하고 Application Insights를 선택한 다음 앱을 위해 만든 리소스를 선택합니다. 이후에 더 빨리 액세스하기 위해 리소스를 시작 보드에 고정할 수 있습니다.

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

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-custom-events-metrics-api.md
[universal]: app-insights-windows-get-started.md#universal
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=August15_HO6-->