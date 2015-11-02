<properties 
	pageTitle="Application Insights에서 경고 설정" 
	description="충돌, 예외, 메트릭 변경 사항에 대한 전자 메일을 받습니다." 
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
 
# Application Insights에서 경고 설정

[Visual Studio Application Insights][start]는 앱의 성능 및 사용 메트릭이 변경되면 사용자에게 경고할 수 있습니다.

Application Insights는 [다양한 플랫폼][platforms]에서 라이브 앱을 모니터링하여 성능 문제를 진단하고 사용 패턴을 이해할 수 있습니다.

경고의 종류는 두 가지입니다.
 
* **웹 테스트**는 인터넷에서 사이트를 사용할 수 없거나 응답 속도가 느려지면 그 사실을 알려줍니다. [자세히 알아봅니다][availability].
* **메트릭 경고**는 어떤 메트릭이 실패 수, 메모리, 페이지 보기 등의 임계값을 일정 기간 동안 초과하면 그 사실을 알려줍니다. 

[웹 테스트에 대한 별도의 페이지][availability]를 준비해 두었으니 지금은 메트릭 경고를 집중적으로 살펴보겠습니다.

## 메트릭 경고

앱에 대해 Application Insights를 설정하지 않았다면 일단 [설정 작업][start]부터 수행하세요.

메트릭이 임계값을 초과할 때 전자 메일을 받으려면 메트릭 탐색기 또는 개요 블레이드의 경고 규칙 타일에서 시작합니다.

![경고 규칙 블레이드에서 경고 추가를 선택합니다. 측정할 리소스로 앱을 설정하고, 경고의 이름을 입력하고, 메트릭을 선택합니다.](./media/app-insights-alerts/01-set-metric.png)

다른 속성에 앞서 리소스를 설정합니다. **"(구성 요소)" 리소스 선택** 성능 또는 사용 메트릭에 대한 경고를 설정하려는 경우.

임계값을 입력하라는 단위에 주의하세요.

경고에 입력하는 이름은 리소스 그룹(응용 프로그램 아님) 내에서 고유한 이름이어야 합니다.

*경고 추가 단추가 보이지 않습니다.* - 조직 계정을 사용 중이신가요? 이 응용 프로그램 리소스에 소유자 또는 참가자 액세스가 가능하면 경고를 설정할 수 있습니다. 설정 -> 사용자를 살펴보세요. [액세스 제어에 대해 자세히 알아보세요][roles].

## 경고 보기

알림 상태가 비활성 및 활성 간에 변경될 때 전자 메일이 제공됩니다.

각 알림의 현재 상태는 알림 규칙 블레이드에 표시됩니다.

경고 드롭다운에 최신 활동에 대한 요약이 있습니다.

![](./media/app-insights-alerts/010-alert-drop.png)

상태 변경 내역은 작업 이벤트 로그에 있습니다.

![하단 근처의 개요 블레이드에서 '지난 주의 이벤트'를 클릭합니다.](./media/app-insights-alerts/09-alerts.png)

*이러한 "이벤트"가 원격 분석 이벤트 또는 사용자 지정 이벤트와 관련이 있나요?*

* 아니요. 이러한 작업 이벤트는 이 응용 프로그램 리소스에 발생한 일에 대한 기록일 뿐입니다. 


## 경고 작동 방식

* 경고는 "경고" 및 "정상"의 두 상태가 있습니다. 

* 경고 상태가 변경되면 메일을 보냅니다.

* 메트릭이 도착할 때마다 경고가 평가되고 그렇지 않은 경우는 평가되지 않습니다.

* 평가는 이전 기간에 대한 메트릭을 집계한 다음 임계값과 비교하여 새 상태를 결정합니다.

* 선택하는 기간으로 메트릭이 집계되는 간격이 지정됩니다. 기간은 경고가 평가되는 빈도에는 영향을 주지 않습니다. 이는 메트릭 도착 빈도에 따라 다릅니다.

* 일정 시간 동안 특정 메트릭에 대해 데이터가 도착하지 않는 경우 해당 간격은 메트릭 탐색기의 차트와 경고 평가에 다른 영향을 미칩니다. 메트릭 탐색기에서 데이터가 차트의 샘플링 간격보다 오랫동안 표시되지 않는 경우 차트 값에는 0이 표시됩니다. 하지만 동일한 메트릭에 따른 경고는 재평가되지 않으며 경고의 상태는 변경되지 않고 그대로 유지됩니다.

    마침내 데이터가 도착하면 차트는 0이 아닌 값으로 이동합니다. 경고는 지정된 기간 동안 사용 가능한 데이터에 따라 평가합니다. 새 데이터 요소를 해당 기간에 하나만 사용할 수 있는 경우 집계는 해당 요소만 기반으로 합니다.

* 경고는 긴 기간을 설정한 경우에도 경고와 정상 상태 사이에서 자주 깜박거릴 수 있습니다. 메트릭 값이 임계값 주위를 가리키는 경우 이런 현상이 발생할 수 있습니다. 임계값에는 이력이 없습니다. 경고로 전환되는 것과 정상으로 전환되는 것이 같은 값에서 발생합니다.



## 가용성 경고

전세계 지점에서 모든 웹사이트를 테스트하도록 웹 테스트를 설정할 수 있습니다. [자세히 알아봅니다][availability].

## 어떤 경고를 설정하면 좋은가요?

응용 프로그램에 따라 다릅니다. 처음에는 경고를 너무 많이 설정하지 않는 것이 좋습니다. 일단 앱이 실행되는 동안 메트릭 차트를 살펴보면서 정상적으로 작동할 때의 상태를 숙지합니다. 이렇게 하면 성능을 개선하는 방법을 찾는 데 도움이 됩니다. 그런 다음 메트릭이 정상 영역을 벗어나면 알려주는 경고를 설정합니다.

다음은 많은 사람들이 사용하는 경고입니다.

* [웹 테스트][availability] - 응용 프로그램이 공용 인터넷에 표시되는 웹사이트 또는 웹 서비스인 경우에 중요합니다. 사이트가 중단되거나 응답 속도가 느려지면 사용자에게 알립니다. 앱이 아니라 통신 회사 문제인 경우에도 사용자에게 알립니다. 하지만 가상 테스트이기 때문에 사용자의 실제 경험을 측정하지는 않습니다.
* [브라우저 메트릭][client], 특히 브라우저 **페이지 로드 시간**은 웹 응용 프로그램에 매우 유용합니다. 페이지에 스크립트가 많으면 **브라우저 예외**를 살펴봐야 합니다. 이러한 메트릭 및 경고를 가져오려면 [웹 페이지 모니터링][client]을 설정해야 합니다.
* **서버 응답 시간** 및 서버 쪽 웹 응용 프로그램에 대한 **실패한 요청**. 경고 설정 외에도 이러한 메트릭을 주시하여 메트릭이 불균형적으로 변하고 요청 속도가 빠른지 살펴보는 것이 좋습니다. 앱 리소스가 부족하다는 의미일 수 있습니다.
* **서버 예외**를 보려면 일부 [추가 설치](app-insights-asp-net-exceptions.md) 작업을 수행해야 합니다.

## PowerShell을 사용하여 경고 설정

대부분의 경우 경고는 수동으로 설정하는 것으로 충분합니다. 하지만 메트릭 경고를 자동으로 만들려면 PowerShell을 사용할 수 있습니다.

#### 일 회 설정

아직 Azure 구독에서 PowerShell을 사용한 적이 없을 경우:

스크립트를 실행하려는 컴퓨터에 Azure Powershell 모듈을 설치합니다.

 * [Microsoft 웹 플랫폼 설치 관리자(v5 이상)](http://www.microsoft.com/web/downloads/platform.aspx)를 설치합니다.
 * 이를 사용하여 Microsoft Azure Powershell을 설치합니다.


#### Azure에 연결

Azure PowerShell을 시작하고 [구독에 연결](powershell-install-configure.md)합니다.

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


#### 경고 받기

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

#### 경고 추가


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



#### 예 1

HTTP 요청에 대한 서버의 응답이 5분 이상 평균 1초보다 느린 경우 전자 메일로 알립니다. Application Insights 리소스의 이름이 IceCreamWebApp이며 리소스 그룹 Fabrikam 내에 있습니다. 제가 Azure 구독의 소유자입니다.

GUID는 구독 ID입니다(응용 프로그램의 계측 키 아님).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

#### 예 2

[TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric)을 사용하여 "salesPerHour"라는 메트릭을 보고하는 응용 프로그램이 있습니다. 24시간 이상 평균 "salesPerHour"가 100 미만으로 떨어지는 경우 동료에게 전자 메일을 보냅니다.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

TrackEvent 또는 trackPageView와 같은 다른 추적 호출의 [측정 매개 변수](app-insights-api-custom-events-metrics.md#properties)를 사용하여 보고된 메트릭에도 동일한 규칙을 사용할 수 있습니다.

#### 메트릭 이름

메트릭 이름 | 화면 이름 | 설명
---|---|---
`basicExceptionBrowser.count`|브라우저 예외|브라우저에서 발생한 확인할 수 없는 예외의 개수입니다.
`basicExceptionServer.count`|서버 예외|앱에서 발생한 처리되지 않은 예외의 개수입니다.
`clientPerformance.clientProcess.value`|클라이언트 처리 시간|DOM을 로드할 때부터 문서의 마지막 바이트를 받는 사이의 시간입니다. 비동기 요청은 계속 처리 중일 수 있습니다.
`clientPerformance.networkConnection.value`|페이지 로드 네트워크 연결 시간| 브라우저가 네트워크에 연결하는 데 걸리는 시간입니다. 캐시된 경우 0일 수 있습니다.
`clientPerformance.receiveRequest.value`|응답 수신 시간| 브라우저가 요청을 보내서 응답을 받기 시작하는 사이의 시간입니다.
`clientPerformance.sendRequest.value`|요청 전송 시간| 브라우저가 요청을 보내는 데 소요된 시간입니다.
`clientPerformance.total.value`|브라우저 페이지 로드 시간|사용자가 요청한 때부터 DOM, 스타일시트, 스크립트 및 이미지가 로드될 때까지 소요된 시간입니다.
`performanceCounter.available_bytes.value`|사용 가능한 메모리|처리를 위해서나 시스템에서 바로 사용할 수 있는 실제 메모리입니다.
`performanceCounter.io_data_bytes_per_sec.value`|프로세스 IO 속도|파일, 네트워크 및 장치에서 읽고 쓴 초당 총 바이트 수입니다.
`performanceCounter.number_of_exceps_thrown_per_sec`|예외 속도|초당 발생한 예외입니다.
`performanceCounter.percentage_processor_time.value`|CPU 프로세스|응용 프로그램 프로세스에 대한 지침 실행을 위해 프로세서가 사용한 모든 프로세스 스레드의 경과 시간 비율입니다.
`performanceCounter.percentage_processor_total.value`|프로세서 시간|프로세서가 비 유휴 스레드에 소요한 시간의 비율입니다.
`performanceCounter.process_private_bytes.value`|프로세스 전용 바이트|모니터링되는 응용 프로그램의 프로세스에 독점적으로 할당된 메모리입니다.
`performanceCounter.request_execution_time.value`|ASP.NET 요청 실행 시간|가장 최근 요청의 실행 시간입니다.
`performanceCounter.requests_in_application_queue.value`|실행 큐의 ASP.NET 요청|응용 프로그램 요청 큐의 길이입니다.
`performanceCounter.requests_per_sec`|ASP.NET 요청 속도|ASP.NET에서 응용 프로그램에 전송된 모든 요청의 속도(초)입니다.
`remoteDependencyFailed.durationMetric.count`|종속성 실패|서버 응용 프로그램에서 외부 리소스에 보낸 호출이 실패한 횟수입니다.
`request.duration`|서버 응답 시간|HTTP 요청을 받은 후 응답 전송을 완료한 때까지의 시간입니다.
`request.rate`|요청 속도|응용 프로그램에 전송된 모든 요청의 속도(초)입니다.
`requestFailed.count`|실패한 요청|응답 코드가 400 이상인 HTTP 요청의 개수입니다. 
`view.count`|페이지 보기|웹 페이지에 대한 클라이언트 사용자 요청의 수입니다. 가상 트래픽은 필터링됩니다.
{사용자 지정 메트릭 이름}|{사용자의 메트릭 이름}|메트릭 값은 [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric)에 의해 또는 [추적 호출의 측정 매개 변수](app-insights-api-custom-events-metrics.md#properties)에 보고됩니다.

다음과 같은 다양한 원격 분석 모듈에서 메트릭이 전송됩니다.

메트릭 그룹 | 수집기 모듈
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>view | [브라우저 JavaScript](app-insights-javascript.md)
performanceCounter | [성능](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [종속성](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
request,<br/>requestFailed|[서버 요청](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO4-->