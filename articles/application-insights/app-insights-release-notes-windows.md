<properties 
	pageTitle="Windows용 Application Insights에 대한 릴리스 정보"
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
 
# Windows Phone 및 Store용 Application Insights SDK에 대한 릴리스 정보

[Application Insights SDK](app-insights-windows-get-started.md)는 라이브 앱에 대한 원격 분석을 [Application Insights](http://azure.microsoft.com/services/application-insights/)에 보내며 사용법과 성능을 분석할 수 있습니다.


#### 응용 프로그램에 SDK를 설치하려면

[Windows Phone 및 Store 앱용 Application Insights 시작](app-insights-windows-get-started.md)을 참조하세요.

#### 최신 SDK를 업그레이드하려면 

* ApplicationInsights.config의 복사본을 수행하려면, 수행한 모든 사용자 지정을 유지합니다.
* 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
* 설치된 패키지를 표시하도록 필터를 설정합니다. 
* 설치된 Application Insights 패키지를 선택하고 업그레이드를 선택합니다.
* 이전 및 새 버전의 ApplicationInsights.config를 비교합니다. 이전 파일에 대한 모든 사용자 지정을 복구합니다.
* 솔루션을 다시 빌드합니다.

## Version 1.0.0

### Windows 앱 SDK

- Windows 앱을 새로 초기화 `InitializeAsync()` 메서드를 사용한 새로운 `WindowsAppInitializer` 클래스는 SDK 컬렉션의 초기화를 부트스트래핑을 허용합니다. 이 변경은 보다 정확한 제어 및 이전 ApplicationInsights.config 기법을 통해 중요한 앱 초기화 성능 향상을 가져옵니다.
- DeveloperMode 더이상 자동으로 설정되지 않습니다. DeveloperMode 동작을 변경하려면 코드에서 지정 해야 합니다.
- NuGet 패키지는 더이상 ApplicationInsights.config를 삽입하지 않습니다. NuGet 패키지를 수동으로 추가하는 경우 새 WindowsAppInitializer를 사용하는 것을 권장합니다.
- ApplicationInsights.config은 `<InstrumentationKey>`에 대해 읽기 전용이며, WindowsAppInitializer 설정에 대한 선호도에서 다른 모든 설정은 무시 됩니다.
- SDK는 Store Market을 자동 수집합니다.
- 많은 버그를 수정, 안정성 향상, 성능을 강화 시켰습니다.

### 핵심 SDK

- ApplicationInsights.config 파일은 더이상 필요하지 않습니다. 그리고 NuGet 패키지에 의해 추가되지 않습니다. 코드에서 구성을 전체 지정할 수 있습니다.
- NuGet 패키지를 더이상 솔루션에 대상 파일을 추가할 수 없습니다. 디버그 빌드 동안 Developer Mode의 자동 설정을 제거합니다. DeveloperMode는 코드에서 수동으로 설정해야 합니다.

## 버전 0.17

### Windows 앱 SDK

- Windows 앱 SDK는 이제 VS 2015 RC와 Windows 10 기술 미리 보기에 대해 만든 범용 Windows 앱을 지원 합니다.

### 핵심 SDK

- Telementry클라이언트는 InMemory Channel의 기본값입니다.
- 새로운 API를 `TelemetryClient.Flush()`에 추가합니다. 이 Flush 메서드는 해당 클라이언트에 기록하는 모든 원격 분석의 업로드 즉시 차단을 트리거합니다. 프로세스 종료 전에 업로드 수동 트리거를 가능하게 합니다.
- NuGet 패키지에는.Net 4.5 대상이 추가되었습니다. 이 대상은 (제거 BCL 및 EventSource 종속성) 외부 종속성이 없습니다.

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

<!---HONumber=September15_HO1-->