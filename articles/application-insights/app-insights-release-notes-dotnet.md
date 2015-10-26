<properties 
	pageTitle=".NET용 Application Insights에 대한 릴리스 정보" 
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
	ms.date="08/06/2015" 
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

## 버전 2.0.0-beta1
- 일부 필수 필드가 지정되지 않은 경우 TrackDependency에서 유효한 JSON을 생성합니다.
- 이제 중복 속성 ```RequestTelemetry.ID```가 ```RequestTelemetry.Operation.Id```의 프록시입니다.
- 새 인터페이스 ```ISupportSampling```이 도입되었으며 대부분의 데이터 항목 형식에서 이를 명시적으로 구현합니다.
- DependencyTelemetry에서 ```Count``` 속성이 사용되지 않는 것으로 표시됩니다. 대신 ```SamplingPercentage```를 사용합니다.
- 새 ```CloudContext```가 도입되었으며 ```RoleName``` 및 ```RoleInstance``` 속성이 ```DeviceContext```에서 여기로 이동했습니다.
- 인증된 사용자 ID를 지정하기 위해 ```UserContext```에 새 속성 ```AuthenticatedUserId```이 도입되었습니다.
- Javascript SDK에 의해 설정된 대로 인증된 사용자 컨텍스트를 초기화하는 `Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer`, `Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer`가 추가되었습니다.
- 이에 대한 구현으로 `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` 및 고정 속도 샘플링 지원이 추가되었습니다.
- `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` 집합을 사용하여 `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`를 생성할 수 있는 `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.TelemetryChannelBuilder`가 추가되었습니다.

## 버전 1.2

- ASP.NET 라이브러리에 종속성이 없는 원격 분석 이니셜라이저가 `Microsoft.ApplicationInsights.Web`에서 새 종속성 nuget`Microsoft.ApplicationInsights.WindowsServer`으로 옮겨졌습니다.
- `Microsoft.AI.Web.dll`에서 `Microsoft.ApplicationInsights.Web.dll`의 이름이 변경되었습니다.
- `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`에서 `Microsoft.ApplicationInsights.Web.TelemetryChannel` nuget의 이름이 변경되었습니다.`Microsoft.AI.ServerTelemetryChannel.dll`에서 `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` 어셈블리의 이름이 변경되었습니다.`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`에서 `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` 클래스의 이름이 변경되었습니다.
- 웹 SDK의 일부인 모든 네임 스페이스가 `Extensibility` 부분을 제외하도록 변경되었습니다. 이는 ApplicationInsights.config의 모든 원격 분석 이니셜라이저와 web.config의 `ApplicationInsightsWebTracking` 모듈을 포함합니다.
- 런타임 계측 에이전트(상태 모니터 또는 Azure 웹사이트 확장을 통해 사용 가능)를 사용하여 수집된 종속성 은 스레드에 HttpContext.Current가 없는 경우 비동기로 표시되지 않습니다.
- `DependencyTrackingTelemetryModule`의 속성 `SamplingRatio`는 아무 작업도 수행하지 않고 사용되지 않음으로 표시됩니다.
- `Microsoft.AI.PerfCounterCollector`에서 `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` 어셈블리의 이름이 변경되었습니다.
- 웹 및 장치 SDK의 몇 가지 사소한 버그 수정


## 버전 1.1

- 새 원격 분석 유형 `DependencyTelemetry`에 응용 프로그램에의 종속성 호출에 대한 정보를 보내는 데 사용할 수 있는 기능(SQL, HTTP 호출 등)이 추가되었습니다.
- 새 오버로드 메서드 `TelemetryClient.TrackDependency`에 종속성 호출에 대한 정보를 보낼 수 있도록 하는 기능이 추가되었습니다.
- TelemetryConfiguration.CreateDefault를 사용할 때 고정된 NullReferenceException은 진단 모듈에 의해 throw 됩니다.

## 버전 1.0

- 원격 분석 이니셜라이저 및 원격 분석 모듈을 별도의 하위 네임스페이스에서 루트 `Microsoft.ApplicationInsights.Extensibility.Web` 네임스페이스로 이동시킵니다.
- “Web”접두사는 이미 `Microsoft.ApplicationInsights.Extensibility.Web` 네임스페이스 이름에 포함되었기 때문에 원격 분석 이니셜라이저 및 원격 분석 모듈의 이름에서 “Web” 접두사를 제거합니다.
- `DeviceContextInitializer`을 `Microsoft.ApplicationInsights` 어셈블리에서 `Microsoft.ApplicationInsights.Extensibility.Web` 어셈블리로 이동하고 `ITelemetryInitializer`로 변환시켰습니다.
- 네임스페이스 및 어셈블리 이름을 `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry`에서 `Microsoft.ApplicationInsights.Extensibility.DependencyCollector`로 변경하여 NuGet 패키지의 이름에 대한 일관성을 유지시킵니다.
- `RemoteDependencyModule`에서 `DependencyTrackingTelemetryModule`로 이름을 변경합니다.
- `CustomPerformanceCounterCollectionRequest`에서 `PerformanceCounterCollectionRequest`로 이름을 변경합니다.

## 버전 0.17
- Framework 4.5 응용 프로그램에 대한 EventSource NuGet에 대한 종속성을 제거합니다.
- 익명 사용자 및 세션 쿠키는 서버쪽에서 생성되지 않습니다. 웹앱의 사용자 및 세션 추적을 구현하려면, JS SDK를 포함한 계측은 원래 목적을 실현하는 JavaScript SDK의 쿠키가 필요합니다. 원격 분석 모듈 ```WebSessionTrackingTelemetryModule``` 및 ```WebUserTrackingTelemetryModule```은 더이상 지원되지 않으며 ApplicationInsights.config 파일에서 제거되었습니다. 이 변경 사항 은 오직 사용자-시작 세션만 계산되는 것처럼 사용자 및 세션 개수의 중요한 restatement를 발생시킨다는 것을 참고하세요.
- OSVersion은 더 이상 기본적으로 SDK로 채워지지 않습니다. 비어 있는 경우, OS 및 OSVersion은 사용자 에이전트에 따라 Application Insights 파이프라인에서 계산됩니다. 
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

 

<!---HONumber=Oct15_HO3-->