---
title: Azure Functions를 사용하여 사용자 지정 가용성 테스트 생성 및 실행
description: 이 문서에서는 TimerTrigger 함수에 지정된 구성에 따라 주기적으로 실행되는 TrackAvailability()를 사용하여 Azure 함수를 만드는 방법을 다룹니다. 이 테스트의 결과는 응용 프로그램 인사이트 리소스로 전송되며, 여기서 가용성 결과 데이터를 쿼리하고 경고할 수 있습니다. 사용자 지정 테스트를 사용하면 포털 UI를 사용하여 가능한 것보다 더 복잡한 가용성 테스트를 작성하거나, Azure VNET 내부의 앱을 모니터링하거나, 끝점 주소를 변경하거나, 해당 지역에서 사용할 수 없는 경우 가용성 테스트를 만들 수 있습니다.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665802"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Azure Functions를 사용하여 사용자 지정 가용성 테스트 생성 및 실행

이 문서에서는 TimerTrigger 함수에 제공된 구성에 따라 주기적으로 실행되는 TrackAvailability()를 사용하여 Azure 함수를 만드는 방법을 설명합니다. 이 테스트의 결과는 응용 프로그램 인사이트 리소스로 전송되며, 여기서 가용성 결과 데이터를 쿼리하고 경고할 수 있습니다. 이렇게 하면 포털의 [가용성 모니터링을](../../azure-monitor/app/monitor-web-app-availability.md) 통해 수행할 수 있는 것과 유사한 사용자 지정 테스트를 만들 수 있습니다. 사용자 지정 테스트를 사용하면 포털 UI를 사용하여 가능한 것보다 더 복잡한 가용성 테스트를 작성하거나, Azure VNET 내부의 앱을 모니터링하거나, 끝점 주소를 변경하거나, 해당 지역에서 이 기능을 사용할 수 없는 경우에도 가용성 테스트를 만들 수 있습니다.

> [!NOTE]
> 이 예제는 TrackAvailability() API 호출이 Azure Function 내에서 작동하는 방식을 보여주기 위해서만 설계되었습니다. 이를 완벽하게 작동하는 가용성 테스트로 전환하는 데 필요한 기본 HTTP 테스트 코드/비즈니스 논리를 작성하는 방법은 없습니다. 기본적으로 이 예제를 살펴보면 항상 오류를 생성하는 가용성 테스트를 만듭니다.

## <a name="create-timer-triggered-function"></a>타이머 트리거 함수 만들기

- 응용 프로그램 인사이트 리소스가 있는 경우:
    - 기본적으로 Azure Functions는 응용 프로그램 인사이트 리소스를 생성하지만 이미 만든 리소스 중 하나를 사용하려면 생성 하는 동안 지정 해야 합니다.
    - [Azure Functions 리소스 및 타이머 트리거 함수를 만드는](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) 방법에 대한 지침을 따르십시오(정리하기 전에 중지)를 다음과 같은 선택 사항으로 선택합니다.
        -  상단 근처의 **모니터링** 탭을 선택합니다.

            ![ 고유한 앱 인사이트 리소스를 사용하여 Azure Functions 앱 만들기](media/availability-azure-functions/create-function-app.png)

        - 응용 프로그램 인사이트 드롭다운 상자를 선택하고 리소스 이름을 입력하거나 선택합니다.

            ![기존 애플리케이션 인사이트 리소스 선택](media/availability-azure-functions/app-insights-resource.png)

        - **검토 + 만들기** 선택
- 타이머 트리거 함수에 대해 아직 생성된 응용 프로그램 인사이트 리소스가 없는 경우:
    - 기본적으로 Azure Functions 응용 프로그램을 만들 때 응용 프로그램 인사이트 리소스를 만듭니다.
    - [Azure Functions 리소스 및 타이머 트리거 함수를 만드는](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) 방법에 대한 지침을 따릅니다(정리 전에 중지).

## <a name="sample-code"></a>예제 코드

아래 코드를 run.csx 파일로 복사합니다(기존 코드가 대체됨). 이렇게 하려면 Azure Functions 응용 프로그램으로 이동하여 왼쪽에 있는 타이머 트리거 함수를 선택합니다.

>[!div class="mx-imgBorder"]
>![Azure 포털에서 Azure 함수의 run.csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> 사용할 끝점 주소의 경우: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. 리소스가 Azure 정부 또는 Azure China와 같은 지역에 있는 경우가 아니면 [기본 끝점 재정의에](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) 대해 이 문서를 참조하고 해당 지역의 적절한 원격 분석 채널 끝점을 선택하십시오.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
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
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

보기 파일 아래오른쪽에서 **추가를**선택합니다. 다음 구성으로 새 파일 **function.proj를** 호출합니다.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![오른쪽 선택에 추가합니다. 파일 함수 의 이름을 지정합니다.proj](media/availability-azure-functions/addfile.png)

보기 파일 아래오른쪽에서 **추가를**선택합니다. 다음 구성으로 새 파일 **runAvailabilityTest.csx를** 호출합니다.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>중복 확인

모든 것이 작동하는지 확인하려면 응용 프로그램 인사이트 리소스의 가용성 탭에서 그래프를 볼 수 있습니다.

> [!NOTE]
> runAvailabilityTest.csx에서 자신의 비즈니스 논리를 구현 하는 경우 다음 아래 스크린샷에서 와 같은 성공적인 결과 볼 것 이다, 그렇지 않은 경우 실패 한 결과 볼 것 이다.

>[!div class="mx-imgBorder"]
>![성공적인 결과와 가용성 탭](media/availability-azure-functions/availtab.png)

Azure Functions를 사용하여 테스트를 설정하면 가용성 탭에서 **테스트 추가를** 사용하는 것과 달리 테스트 이름이 나타나지 않으며 테스트와 상호 작용할 수 없다는 것을 알 수 있습니다. 결과는 시각화되지만 포털을 통해 가용성 테스트를 만들 때 얻을 수 있는 것과 동일한 세부 보기 대신 요약 보기를 얻을 수 있습니다.

종단 간 트랜잭션 세부 정보를 보려면 드릴에서 **성공** 또는 **실패를** 선택한 다음 샘플을 선택합니다. 그래프에서 데이터 포인트를 선택하여 종단 간 트랜잭션 세부 정보로 이동하여 얻을 수도 있습니다.

>[!div class="mx-imgBorder"]
>![샘플 가용성 테스트 선택](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![종단 간 트랜잭션 세부 정보](media/availability-azure-functions/end-to-end.png)

비즈니스 논리를 추가하지 않고 모든 것을 있는 것처럼 실행한 경우 테스트가 실패한 것을 볼 수 있습니다.

## <a name="query-in-logs-analytics"></a>로그 내 쿼리(분석)

Logs(분석)를 사용하여 가용성 결과, 종속성 등을 볼 수 있습니다. 로그에 대해 자세히 알아보려면 [로그 쿼리 개요를](../../azure-monitor/log-query/log-query-overview.md)방문하십시오.

>[!div class="mx-imgBorder"]
>![가용성 결과](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![종속성](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>다음 단계

- [애플리케이션 맵](../../azure-monitor/app/app-map.md)
- [트랜잭션 진단](../../azure-monitor/app/transaction-diagnostics.md)
