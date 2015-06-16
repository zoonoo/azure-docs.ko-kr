<properties 
	pageTitle="Application Insights에 대한 릴리스 정보" 
	description="최신 업데이트입니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="sergkanz"/>
 
# Application Insights SDK for .NET에 대한 릴리스 정보

[SDK for .NET 사용](app-insights-start-monitoring-app-health-usage.md)

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

<!---HONumber=58--> 