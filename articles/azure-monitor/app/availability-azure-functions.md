---
title: Azure Functions를 사용하여 사용자 지정 가용성 테스트 만들기 및 실행
description: 이 문서에서는 TimerTrigger 함수에 지정된 구성에 따라 주기적으로 실행되는 TrackAvailability()를 사용하여 Azure 함수를 만드는 방법을 설명합니다. 이 테스트의 결과는 Application Insights 리소스로 전송되며, 해당 리소스에서 가용성 결과 데이터를 쿼리하고 경고할 수 있습니다. 사용자 지정된 테스트를 통해 포털 UI를 사용하여 가능한 것보다 복잡한 가용성 테스트를 작성하거나, Azure VNET 내부에서 앱을 모니터링하거나, 엔드포인트 주소를 변경하거나, 지역에서 사용할 수 없는 경우 가용성 테스트를 만들 수 있습니다.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 98d9eaadb31ffdeabe85752f7c76bdd4f7c0d4f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589942"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Azure Functions를 사용하여 사용자 지정 가용성 테스트 만들기 및 실행

이 문서에서는 고유한 비즈니스 논리를 포함하는 TimerTrigger 함수에 지정된 구성에 따라 주기적으로 실행되는 TrackAvailability()를 사용하여 Azure 함수를 만드는 방법을 설명합니다. 이 테스트의 결과는 Application Insights 리소스로 전송되며, 해당 리소스에서 가용성 결과 데이터를 쿼리하고 경고할 수 있습니다. 그러면 포털에서 [가용성 모니터링](./monitor-web-app-availability.md)을 통해 수행할 수 있는 것과 유사한 사용자 지정된 테스트를 만들 수 있습니다. 사용자 지정된 테스트를 통해 포털 UI를 사용하여 가능한 것보다 복잡한 가용성 테스트를 작성하거나, Azure VNET 내부에서 앱을 모니터링하거나, 엔드포인트 주소를 변경하거나, 지역에서 사용할 수 없는 기능이라 하더라도 가용성 테스트를 만들 수 있습니다.

> [!NOTE]
> 이 예제는 Azure Function 내에서 TrackAvailability() API 호출이 작동하는 방식에 대한 메커니즘을 보여 주기 위해서만 설계되었습니다. 이를 전체 기능을 갖춘 가용성 테스트로 전환하는 데 필요한 기본 HTTP 테스트 코드/비즈니스 논리를 작성하는 방법에 대해서는 설명하지 않습니다. 기본적으로 이 예제를 진행하는 경우 항상 오류를 생성하는 가용성 테스트를 만듭니다.

## <a name="create-timer-triggered-function"></a>타이머 트리거 함수 만들기

- Application Insights 리소스가 있는 경우
    - 기본적으로 Azure Functions는 Application Insights 리소스를 만들지만 이미 만든 리소스 중 하나를 사용하려는 경우 만드는 도중에 지정해야 합니다.
    - [Azure Functions 리소스 및 타이머 트리거 함수 만들기](../../azure-functions/functions-create-scheduled-function.md)(정리 전 중지) 지침을 따르고 다음 선택 사항을 사용하세요.
        -  위쪽에서 **모니터링** 탭을 선택합니다.

            ![ 자체 App Insights 리소스를 사용하여 Azure Functions 앱 만들기](media/availability-azure-functions/create-function-app.png)

        - Application Insights 드롭다운 상자를 선택하고 리소스의 이름을 입력하거나 선택합니다.

            ![기존 Application Insights 리소스 선택](media/availability-azure-functions/app-insights-resource.png)

        - **검토 + 만들기** 를 선택합니다.
- 타이머 트리거 함수에서 사용할 Application Insights 리소스가 아직 생성되지 않은 경우
    - 기본적으로 Azure Functions 애플리케이션을 만들 때 Application Insights 리소스를 만들게 됩니다.
    - [Azure Functions 리소스 및 타이머 트리거 함수 만들기](../../azure-functions/functions-create-scheduled-function.md)(정리 전 중지) 지침을 따르세요.

## <a name="sample-code"></a>샘플 코드

아래 코드를 run.csx 파일에 복사합니다. 그러면 기존 코드가 바뀝니다. 이렇게 하려면 Azure Functions 애플리케이션으로 이동하여 왼쪽에서 타이머 트리거 함수를 선택합니다.

>[!div class="mx-imgBorder"]
>![Azure Portal에서 Azure 함수의 run.csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> 엔드포인트 주소에 `EndpointAddress= https://dc.services.visualstudio.com/v2/track`을 사용합니다. 리소스가 Azure Government 또는 Azure 중국 같은 지역에 있는 경우를 제외하고 [기본 엔드포인트 재정의](./custom-endpoints.md#regions-that-require-endpoint-modification)에 관한 이 문서를 참조하고 해당 지역에 적절한 원격 분석 채널 엔드포인트를 선택합니다.

```C#
#load "runAvailabilityTest.csx&quot;
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable(&quot;APPINSIGHTS_INSTRUMENTATIONKEY");
 
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

오른쪽의 파일 보기 아래에서 **추가** 를 선택합니다. 다음 구성을 사용하여 새 파일 **function.proj** 를 호출합니다.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![오른쪽에서 추가를 선택합니다. function.proj 파일에 이름을 지정합니다.](media/availability-azure-functions/addfile.png)

오른쪽의 파일 보기 아래에서 **추가** 를 선택합니다. 다음 구성을 사용하여 새 파일 **runAvailabilityTest.csx** 를 호출합니다.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>중복 확인

모든 것이 작동하는지 확인하기 위해 Application Insights 리소스의 가용성 탭에서 그래프를 살펴볼 수 있습니다.

> [!NOTE]
> runAvailabilityTest.csx에 고유한 비즈니스 논리를 구현한 경우 아래 스크린샷처럼 성공 결과가 표시됩니다. 그렇지 않은 경우에는 실패 결과가 표시됩니다. `TrackAvailability()`로 만든 테스트는 테스트 이름 옆에 **CUSTOM** 이 표시됩니다.

>[!div class="mx-imgBorder"]
>![성공 결과가 표시된 가용성 탭](media/availability-azure-functions/availability-custom.png)

엔드투엔드 트랜잭션 세부 정보를 보려면 상세 검색 아래에서 **성공** 또는 **실패** 를 선택하고 샘플을 선택합니다. 그래프에서 데이터 요소를 선택하여 엔드투엔드 트랜잭션 세부 정보를 가져올 수도 있습니다.

>[!div class="mx-imgBorder"]
>![샘플 가용성 테스트 선택](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![엔드투엔드 트랜잭션 세부 정보](media/availability-azure-functions/end-to-end.png)

비즈니스 논리를 추가하지 않고 함수를 있는 그대로 실행하는 경우 테스트가 실패하는 것을 볼 수 있습니다.

## <a name="query-in-logs-analytics"></a>로그(분석)에서 쿼리

로그(분석)를 사용하여 가용성 결과, 종속성 등을 볼 수 있습니다. 로그에 대한 자세한 내용을 보려면 [로그 쿼리 개요](../logs/log-query-overview.md)를 참조하세요.

>[!div class="mx-imgBorder"]
>![가용성 결과](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![종속성이 50개로 제한된 새 쿼리 탭을 보여 주는 스크린샷](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>다음 단계

- [애플리케이션 맵](./app-map.md)
- [트랜잭션 진단](./transaction-diagnostics.md)

