---
title: Azure Functions를 사용 하 여 사용자 지정 가용성 테스트 만들기 및 실행
description: 이 문서에서는 동작 트리거 함수에 지정 된 구성에 따라 주기적으로 실행 되는 지 수 ()를 사용 하 여 Azure 함수를 만드는 방법을 설명 합니다. 이 테스트의 결과는 Application Insights 리소스로 전송 되며, 여기서 가용성 결과 데이터를 쿼리하고 경고할 수 있습니다. 사용자 지정 된 테스트를 통해 포털 UI를 사용 하 여 보다 복잡 한 가용성 테스트를 작성 하거나, Azure VNET 내부에서 앱을 모니터링 하거나, 끝점 주소를 변경 하거나, 지역에서 사용할 수 없는 경우 가용성 테스트를 만들 수 있습니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 10/11/2019
ms.openlocfilehash: 900228e1f9bdf9d367fa37b9ec90a6148faec656
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880246"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Azure Functions를 사용 하 여 사용자 지정 가용성 테스트 만들기 및 실행

이 문서에서는 동작 트리거 함수에 지정 된 구성에 따라 주기적으로 실행 되는 지 수 ()를 사용 하 여 Azure 함수를 만드는 방법을 설명 합니다. 이 테스트의 결과는 Application Insights 리소스로 전송 되며, 여기서 가용성 결과 데이터를 쿼리하고 경고할 수 있습니다. 이를 통해 포털에서 [가용성 모니터링](../../azure-monitor/app/monitor-web-app-availability.md) 을 통해 수행할 수 있는 것과 유사한 사용자 지정 된 테스트를 만들 수 있습니다. 사용자 지정 된 테스트를 통해 포털 UI를 사용 하 여 보다 복잡 한 가용성 테스트를 작성 하거나, Azure VNET 내부에서 앱을 모니터링 하거나, 끝점 주소를 변경 하거나, 지역에서이 기능을 사용할 수 없는 경우에도 가용성 테스트를 만들 수 있습니다.


## <a name="create-timer-triggered-function"></a>타이머 트리거 함수 만들기

- Application Insights 리소스가 있는 경우:
    - 기본적으로 Azure Functions는 Application Insights 리소스를 만들지만 이미 생성 된 리소스 중 하나를 사용 하려는 경우 생성 중에 지정 해야 합니다.
    - 다음 선택 항목을 사용 하 여 [Azure Functions 리소스 및 타이머 트리거 함수](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (정리 전에 중지)를 만드는 방법에 대 한 지침을 따르세요.
        -  **만들기**를 선택 하기 전에 Application Insights 섹션을 클릭 합니다.

            ![ 사용자 고유의 App Insights 리소스를 사용 하 여 Azure Functions 앱 만들기](media/availability-azure-functions/create-function-app.png)

        - **기존 리소스 선택** 을 클릭 하 고 리소스의 이름을 입력 합니다. **적용** 선택

            ![기존 Application Insights 리소스 선택](media/availability-azure-functions/app-insights-resource.png)

        - **만들기**
- 타이머 트리거 함수에 대해 아직 Application Insights 리소스가 생성 되지 않은 경우:
    - 기본적으로 Azure Functions 응용 프로그램을 만들 때 Application Insights 리소스를 만들게 됩니다.
    - [Azure Functions 리소스 및 타이머 트리거 함수를 만드는](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) 방법에 대 한 지침을 따르세요 (정리 전 중지).

## <a name="sample-code"></a>샘플 코드

아래 코드를 실행. csx 파일에 복사 합니다. 이렇게 하면 기존 코드가 바뀝니다. 이렇게 하려면 Azure Functions 응용 프로그램으로 이동 하 여 왼쪽에서 타이머 트리거 함수를 선택 합니다.

![Azure Portal에서 Azure 함수의 실행. csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> `EndpointAddress= https://dc.services.visualstudio.com/v2/track`사용 하는 끝점 주소: 리소스가 Azure Government 또는 Azure 중국와 같은 지역에 있는 경우를 제외 하 고 [기본 끝점 재정의](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) 에 대 한이 문서를 참조 하 고 해당 지역에 적절 한 원격 분석 채널 끝점을 선택 합니다.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");

    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }

    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";

    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        //throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
    }
}
```

파일 보기 오른쪽에서 **추가**를 선택 합니다. 다음 구성을 사용 하 여 새 파일 **함수 proj** 를 호출 합니다.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![오른쪽 select에서을 추가 합니다. File 함수 이름을 proj로 합니다.](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>가용성 확인

모든 것이 작동 하는지 확인 하기 위해 Application Insights 리소스의 가용성 탭에서 그래프를 살펴볼 수 있습니다.

![성공적인 결과가 포함 된 가용성 탭](media/availability-azure-functions/availtab.png)

Azure Functions를 사용 하 여 테스트를 설정 하는 경우 가용성 탭에서 **테스트 추가** 를 사용 하는 것과 달리 테스트 이름이 나타나지 않으며 상호 작용할 수 없습니다. 결과는 시각화 되지만 포털을 통해 가용성 테스트를 만들 때 표시 되는 것과 동일한 자세한 보기 대신 요약 뷰를 얻을 수 있습니다.

종단 간 트랜잭션 세부 정보를 보려면 드릴스루에서 **성공** 또는 **실패** 를 선택 하 고 샘플을 선택 합니다. 그래프에서 데이터 요소를 선택 하 여 종단 간 트랜잭션 세부 정보를 가져올 수도 있습니다.

![샘플 가용성 테스트 선택](media/availability-azure-functions/sample.png)

![종단 간 트랜잭션 세부 정보](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>로그에서 쿼리 (분석)

로그 (분석)를 사용 하 여 가용성 결과, 종속성 등을 볼 수 있습니다. 로그에 대 한 자세한 내용을 보려면 [로그 쿼리 개요](../../azure-monitor/log-query/log-query-overview.md)를 참조 하세요.

![가용성 결과](media/availability-azure-functions/availabilityresults.png)

![종속성](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>다음 단계

- [애플리케이션 맵](../../azure-monitor/app/app-map.md)
- [트랜잭션 진단](../../azure-monitor/app/transaction-diagnostics.md)
