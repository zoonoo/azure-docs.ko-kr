<properties 
	pageTitle="PowerShell을 사용하여 Application Insights에서 경고 설정" 
	description="Application Insights의 구성을 자동화하여 메트릭 변경 사항에 대한 전자 메일을 받습니다." 
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
	ms.date="01/22/2016" 
	ms.author="awills"/>
 
# PowerShell을 사용하여 Application Insights에서 경고 설정

[Visual Studio Application Insights](app-insights-overview.md)에서 [경고](app-insights-alerts.md)의 구성을 자동화할 수 있습니다.

## 일 회 설정

아직 Azure 구독에서 PowerShell을 사용한 적이 없을 경우:

스크립트를 실행하려는 컴퓨터에 Azure Powershell 모듈을 설치합니다.

 * [Microsoft 웹 플랫폼 설치 관리자(v5 이상)](http://www.microsoft.com/web/downloads/platform.aspx)를 설치합니다.
 * 이를 사용하여 Microsoft Azure Powershell을 설치합니다.


## Azure에 연결

Azure PowerShell을 시작하고 [구독에 연결](powershell-install-configure.md)합니다.

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## 경고 받기

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## 경고 추가


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



## 예 1

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

## 예 2

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

## 메트릭 이름

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


## 참고 항목


* [Application Insights를 구성하는 스크립트](app-insights-powershell-script-create-resource.md)
* [서식 파일에서 Application Insights 및 웹 테스트 리소스 만들기](app-insights-powershell.md)
* [Application Insights에 Microsoft Azure 진단 결합 자동화](app-insights-powershell-azure-diagnostics.md)


 

<!---HONumber=AcomDC_0128_2016-->