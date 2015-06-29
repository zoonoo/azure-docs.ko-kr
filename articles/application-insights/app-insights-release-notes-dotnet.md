<properties 
	pageTitle="Application Insights에 대한 릴리스 정보" 
	description="최신 업데이트입니다." 
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
	ms.date="06/18/2015" 
	ms.author="sergkanz"/>
 
# Application Insights SDK for .NET에 대한 릴리스 정보

[Application Insights SDK for.NET](app-insights-start-monitoring-app-health-usage.md)은 라이브 앱에 대한 원격 분석을 [Application Insights](http://azure.microsoft.com/services/application-insights/)에 보내며 사용법과 성능을 분석할 수 있습니다.


#### 응용 프로그램에 SDK를 설치하려면

[.NET용 Application Insights 시작](app-insights-start-monitoring-app-health-usage.md)을 참조하세요.

#### 최신 SDK를 업그레이드하려면 

* 업그레이드 후에 ApplicationInsights.config에 대해 작성된 모든 사용자을 다시 병합해야 합니다. 사용자 지정 여부가 확실하지 않은 경우, 새 프로젝트를 만들고 Application Insights를 추가하고 .config 파일을 새 프로젝트의 파일과 비교합니다. 모든 차이점을 기록해둡니다.
* 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
* 설치된 패키지를 표시하도록 필터를 설정합니다. 
* **Microsoft.ApplicationInsights.Web**을 선택하고 **업그레이드**를 선택합니다. (또한 모든 종속 패키지를 업그레이드합니다 .)
* 이전 복사본과 ApplicationInsights.config를 비교합니다. 대부분의 변경 내용은 일부 모듈을 제거하고 다른 일부를 매개변수화하기 때문입니다. 이전 파일에 대한 모든 사용자 지정을 복구합니다.
* 솔루션을 다시 빌드합니다.

## 버전 0.17
- Framework 4.5 응용 프로그램에 대한 EventSource NuGet에 대한 종속성을 제거합니다.
- 익명 사용자 및 세션 쿠키는 서버쪽에서 생성되지 않습니다. 원격 분석 모듈 ```WebSessionTrackingTelemetryModule``` 및 ```WebUserTrackingTelemetryModule```은 더이상 지원되지 않으며 ApplicationInsights.config 파일에서 제거되었습니다. JavaScript SDK의 쿠키가 적용됩니다.
- 로드가 많은 시나리오에 맞게 최적화된 지속성 채널이 웹 SDK에 사용됩니다. "죽음의 나선" 문제가 수정되었습니다. 끝점에 대한 조정 제한을 훨씬 초과하는 원격 분석 항목의 나선 수가 특정 시간 후 다시 시도되며 다시 시도되는 동안 침체되는 경우 죽음의 나선이 조건입니다.
- 개발자 모드는 프로덕션에 최적화되어있습니다. 실수로 남아있는 경우, 추가 정보를 출력하기 전에 큰 오버 헤드가 발생하지 않습니다.
- 응용 프로그램이 디버거 상태이면 기본적으로 개발자 모드만이 활성화됩니다. ```ITelemetryChannel``` 인터페이스의 ```DeveloperMode``` 속성을 사용하여 재정의할 수 있습니다.

## 버전 0.16 

2015-04-28 미리 보기

- SDK는 이제 DNX 대상 플랫폼을 지원하므로 [.NET Core 프레임워크](http://www.dotnetfoundation.org/NETCore5) 응용 프로그램을 모니터링할 수 있습니다.
- ```TelemetryClient```의 인스턴스가 더 이상 계측 키를 캐시하지 않습니다. 이제 ```TelemetryClient```에서 계측 키를 명시적으로 설정하지 않으면 ```InstrumentationKey```에서 null을 반환합니다. 일부 원격 분석이 이미 수집된 후 ```TelemetryConfiguration.Active.InstrumentationKey```를 설정하면 종속성 수집기, 웹 요청 데이터 모음, 성능 카운터 수집기 같은 원격 분석 모듈에서 새로운 계측 키를 사용하므로 문제가 해결됩니다.

## 버전 0.15

- 이제 새로운 ```Operation.SyntheticSource``` 속성이 ```TelemetryContext```에 제공됩니다. 이제 원격 분석 항목을 "실제 사용자 트래픽이 아님"으로 표시하고 이 트래픽이 생성되는 방식을 지정할 수 있습니다. 예를 들어 이 속성을 설정하여 테스트 자동화의 트래픽과 부하 테스트 트래픽을 구분할 수 있습니다.
- 채널 논리는 Microsoft.ApplicationInsights.PersistenceChannel이라고 하는 별도의 NuGet로 이동되었습니다. 이제 기본 채널은 InMemoryChannel입니다.
- 새로운 ```TelemetryClient.Flush``` 메서드를 사용하여 버퍼의 원격 분석 항목을 동기적으로 플러시할 수 있습니다.

## 버전 0.13

이전 버전에 대한 릴리스 정보가 없습니다.

 

<!---HONumber=58_postMigration-->