<properties 
	pageTitle="Application Insights에서 다음을 수행하는 방법" 
	description="Application Insights의 FAQ" 
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
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Application Insights에서 다음을 수행하는 방법

## 전자 메일을 받는 경우

### 내 사이트가 다운되면 전자 메일로 알림

[가용성 웹 테스트](app-insights-monitor-web-app-availability.md)를 설정합니다.

### 내 사이트가 과부하되면 전자 메일로 알림

[서버 응답 시간](app-insights-alerts.md)에서 **경고**를 설정합니다. 1-2초 임계값이 적용됩니다.

![](./media/app-insights-how-do-i/030-server.png)

응용 프로그램이 오류 코드를 반환하여 부하의 흔적을 표시할 수도 있습니다. **실패한 요청**에 대한 경고를 설정합니다.

**서버 예외**에 대한 경고를 설정하기 위해 데이터를 확인하는 데 [일부 추가 설치](app-insights-asp-net-exceptions.md)를 수행해야 할 수 있습니다.


### 내 응용 프로그램에서 이벤트 발생 시 전자 메일로 알림

특정 이벤트가 발생할 때 전자 메일을 받으려 한다고 가정해 보겠습니다. Application Insights는 이 기능을 직접 제공하지 않지만 [메트릭이 임계값에 도달했을 때](app-insights-alerts.md) 경고를 보낼 수 있습니다.

경고는 사용자 지정 이벤트는 아니지만 [사용자 지정 메트릭](app-insights-api-custom-events-metrics.md#track-metric)에서 설정할 수 있습니다. 이벤트 발생 시 메트릭 향상을 위한 몇 가지 코드를 작성합니다.

    telemetry.TrackMetric("Alarm", 10);

또는

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

경고에는 두 상태가 있기 때문에 경고 종료를 고려할 때 낮은 값을 보내야 합니다.

    telemetry.TrackMetric("Alarm", 0.5);

[메트릭 탐색기](app-insights-metric-explorer.md)에서 차트를 만들어 경고를 확인합니다.

![](./media/app-insights-how-do-i/010-alarm.png)

이제 메트릭이 짧은 기간 동안 중간 값 위로 상승하면 발생하는 경고를 설정합니다.


![](./media/app-insights-how-do-i/020-threshold.png)

평균 기간을 최소로 설정합니다.

메트릭이 임계값 위와 아래로 가면 전자 메일을 받게 됩니다.

몇 가지 고려할 점은 다음과 같습니다.

* 경고는 "경고" 및 "정상"의 두 상태가 있습니다. 상태는 메트릭 수신 시에만 평가됩니다.
* 상태가 변경될 때만 전자 메일을 보냅니다. 이 때문에 높은 값과 낮은 값의 메트릭을 모두 보내야 합니다. 
* 경고를 평가하기 위해 이전 기간 동안 수신한 값에서 평균을 취합니다. 이 작업은 메트릭이 수신될 때마다 발생하므로 설정한 기간보다 더 자주 전자 메일이 전달될 수 있습니다.
* "경고" 및 "정상"에서 모두 전자 메일을 보내므로 원샷 이벤트를 2상태 조건으로 간주할 수 있습니다. 예를 들어, "작업 완료" 이벤트 대신, 작업 시작과 종료 시 전자 메일을 받는 "작업 진행 중" 조건을 적용합니다.
 
## 응용 프로그램 버전 필터

응용 프로그램의 새 버전을 게시하면서 다른 버전의 원격 분석을 구분하고자 할 수 있습니다.

응용 프로그램 버전 속성을 설정하여 [검색](app-insights-diagnostic-search.md) 및 [메트릭 탐색기](app-insights-metrics-explorer.md) 결과를 필터링할 수 있습니다.


![](./media/app-insights-how-do-i/050-filter.png)

응용 프로그램 버전 속성은 몇 가지 다른 방법으로 설정할 수 있습니다.

* 직접 설정:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`

* [원격 분석 이니셜라이저](app-insights-api-custom-events-metrics.md#telemetry-initializers) 에서 해당 줄을 래핑하여 모든 TelemetryClient 인스턴스가 일관되게 설정되었는지 확인합니다.

* [ASP.NET] `BuildInfo.config`에서 버전을 설정합니다 웹 모듈은 BuildLabel 노드에서 버전을 선택합니다. 프로젝트에 이 파일을 포함하고 솔루션 탐색기에서 항상 복사 속성을 설정합니다.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] MSBuild에서 BuildInfo.config를 자동으로 생성합니다. 이 작업을 수행하려면.csproj 파일에 몇 줄을 추가합니다.

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
    ```

    그러면 *yourProjectName*. BuildInfo.config 파일이 생성됩니다. 게시 프로세스의 이름이 BuildInfo.config로 바뀝니다.

    Visual Studio를 사용하여 빌드할때 빌드 레이블에는 자리 표시자(AutoGen\_...)가 포함됩니다. MSBuild로 빌드할 때는 정확한 버전 번호가 입력됩니다.

    MSBuild가 버전 번호를 생성하게 하려면 AssemblyReference.cs에서 `1.0.*`같이 버전을 설정합니다.

<!---HONumber=Oct15_HO1-->